# Spot: Plan Técnico

## 1. Visión

**Spot** elimina fraudes en ticketing usando blockchain invisible. Los usuarios compran con CLP, el sistema mintea NFTs automáticamente.

---

## 2. Arquitectura

```
spot-web (Next.js) ──┐
spot-mobile (Kotlin) ├── spot-backend (NestJS) ── PostgreSQL/Redis
spot-validator ──────┘                        └── Solana/Alchemy
```

### Módulos Backend
| Módulo | Responsabilidad |
|--------|-----------------|
| AuthModule | JWT, wallets custodiales |
| UsersModule | CRUD usuarios |
| ArtistsModule | Artistas, fan clubs |
| EventsModule | Eventos, tiers, venues |
| TicketsModule | Compra, reserva, mint |
| MarketModule | Reventa, royalties |
| PaymentsModule | WebPay, MercadoPago, Flow |
| BlockchainModule | Solana minting |
| MultiChainModule | Verificación ETH/Polygon |
| ValidationModule | NFC, check-in |

---

## 3. Stack

| Capa | Tecnología |
|------|------------|
| Backend | NestJS 10, TypeORM |
| Database | PostgreSQL 15, Redis 7 |
| Frontend | Next.js 14, TailwindCSS |
| Mobile | Android Kotlin API 26+ |
| Blockchain | Solana (Metaplex) |
| Multi-chain | Alchemy/Moralis |

---

## 4. Seguridad

### Wallets Custodiales
- Generación: `Keypair.generate()` (Solana)
- Encriptación: AES-256-GCM
- Key rotation: AWS KMS o Vault
- Almacenamiento: `wallet_secret_encrypted` + `key_version`

### NFC Anti-Replay
```
payload = { ticketId, timestamp, nonce, signature }
1. Verificar timestamp < 60s
2. Verificar nonce único (Redis SET)
3. Verificar firma Ed25519
```

### Rate Limiting
- `/tickets/purchase`: 3 req/seg
- `/marketplace`: 100 req/min
- General: 1000 req/min

---

## 5. Cumplimiento Chile

| Regulación | Acción |
|------------|--------|
| CMF Ley Fintech | Registro si >100M CLP/año |
| SII IVA | Campo `iva_amount` en transactions |
| Ley 19.628 | Consentimientos datos |

---

## 6. Fases

| Fase | Semanas | Alcance |
|------|---------|---------|
| 0 | 1 | Docker, CI/CD, boilerplate |
| 1 | 2 | Auth, wallets, CRUD base |
| 2 | 3 | Blockchain, pagos, IVA |
| 3 | 2 | Tickets, validación NFC |
| 4 | 1.5 | Marketplace, royalties |
| 5 | 1.5 | Fan clubs multi-chain |
| 6 | 2 | Frontend web |
| 7 | 2 | Mobile Android |
| 8 | 1 | Auditorías, testing |

**Total: ~16 semanas**

---

## 7. Costos Mensuales 2025

| Item | USD |
|------|-----|
| Servidores (Railway) | 75 |
| Solana RPC (Helius) | 100 |
| Fallback RPC | 50 |
| Gas Solana | 50 |
| **Total** | **~275** |

---

## 8. Documentos Relacionados

- [database_schema.md](file:///C:/Users/Benja/.gemini/antigravity/brain/85929a9c-a527-46a0-a18c-38c5bdc2dee7/database_schema.md) - Esquema SQL completo
- [api_specification.md](file:///C:/Users/Benja/.gemini/antigravity/brain/85929a9c-a527-46a0-a18c-38c5bdc2dee7/api_specification.md) - APIs detalladas
- [business_model.md](file:///C:/Users/Benja/.gemini/antigravity/brain/85929a9c-a527-46a0-a18c-38c5bdc2dee7/business_model.md) - Modelo de negocios
