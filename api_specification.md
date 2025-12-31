# Spot: Especificación de APIs

## Formato de Respuestas

```json
{
    "success": true,
    "data": { ... },
    "error": null,
    "meta": { "page": 1, "total": 100 }
}
```

---

## Auth

### POST /auth/register
```
Body: {
    email: string,
    password: string (min 8),
    firstName: string,
    lastName: string,
    phone?: string
}
Response 201: {
    user: { id, email, firstName },
    accessToken: string,
    refreshToken: string
}
Acciones: Hash password, generar wallet custodial, encriptar secret
```

### POST /auth/login
```
Body: { email, password }
Response 200: { accessToken, refreshToken, user }
```

### POST /auth/refresh
```
Body: { refreshToken }
Response 200: { accessToken, refreshToken }
```

### GET /auth/me
```
Headers: Authorization: Bearer <token>
Response 200: { user }
```

---

## Events

### GET /events
```
Query: page, limit, city?, dateFrom?, artistId?, search?
Response 200: { data: Event[], meta }
```

### GET /events/:slug
```
Response 200: {
    ...event,
    artist: { id, name },
    venue: { id, name, address },
    tiers: [{
        id, name, price, quantity, sold, available,
        saleStart, saleEnd,
        fanClubPresale: { active, start, end, price }
    }]
}
```

### POST /events (organizer)
```
Body: {
    artistId, venueId, name, description,
    eventDate, resaleLimitPercent,
    organizerRoyaltyPercent, artistRoyaltyPercent,
    tiers: [{
        name, sectorId, price, quantity,
        saleStart, maxPerUser?,
        fanClubId?, fanClubPresaleStart?
    }]
}
Response 201: { event }
```

### PUT /events/:id (organizer)
### DELETE /events/:id (organizer)

---

## Tickets

### POST /tickets/reserve
```
Body: { tierId, quantity }
Response 200: {
    reservationId,
    expiresAt,
    subtotal, spotFee, processorFee, total
}
```

### POST /tickets/purchase
```
Body: { reservationId, paymentMethod }
Response 200: {
    transactionId,
    paymentUrl  // Redirect
}
```

### POST /tickets/webhook/:provider
```
Webhook de WebPay/MercadoPago/Flow
Acciones: Verificar firma, mint NFT, crear ticket, email
```

### GET /tickets/my
```
Response 200: [{
    id, mintAddress, status, designType,
    isUsed, resalePrice,
    tier: { name, price },
    event: { name, date, venue }
}]
```

### PUT /tickets/:id/list-for-sale
```
Body: { price }  // Máx: original + resaleLimitPercent%
Response 200: { ticket }
```

### DELETE /tickets/:id/list-for-sale

---

## Marketplace

### GET /marketplace
```
Query: eventId?, minPrice?, maxPrice?, page?, limit?
Response 200: [{
    ticket: { id, resalePrice },
    tier: { name, sectorId },
    event: { name, date }
}]
```

### POST /marketplace/buy/:ticketId
```
Body: { paymentMethod }
Response 200: {
    transactionId,
    paymentUrl,
    breakdown: {
        ticketPrice, spotFee,
        organizerRoyalty, artistRoyalty,
        processorFee, total
    }
}
```

---

## Fan Clubs

### POST /wallets/link
```
Body: {
    blockchain: 'ethereum' | 'polygon' | 'solana',
    walletAddress,
    signature,
    message
}
Response 200: { linkedWallet }
```

### GET /wallets/linked
```
Response 200: [{ id, blockchain, walletAddress }]
```

### POST /fan-clubs/:id/verify
```
Body: { linkedWalletId }
Response 200: {
    isMember: boolean,
    tier?: string,
    benefits: object,
    expiresAt
}
Acciones: Query Alchemy/Moralis, verificar NFT, cache Redis
```

### GET /fan-clubs/my-memberships
```
Response 200: [{
    fanClub: { name, artistName },
    tier, benefits, expiresAt
}]
```

---

## Validation (Staff)

### POST /validation/login
```
Body: { eventId, staffCode }
Response 200: { validationToken, event, staffName }
```

### POST /validation/verify
```
Headers: X-Validation-Token
Body: {
    ticketId,
    timestamp,
    nonce,
    signature
}
Response 200: {
    valid: boolean,
    reason?: 'already_used' | 'wrong_event' | 'invalid_signature',
    ticket?: { ownerName, tierName }
}
```

### POST /validation/mark-used
```
Body: { ticketId }
Response 200: { success }
```

### GET /validation/stats/:eventId
```
Response 200: {
    totalCapacity, ticketsSold, ticketsValidated,
    percentValidated,
    byTier: [{ name, sold, validated }]
}
```

---

## Rate Limiting

| Endpoint | Límite |
|----------|--------|
| /tickets/purchase | 3/seg |
| /tickets/reserve | 5/seg |
| /marketplace | 100/min |
| General | 1000/min |
