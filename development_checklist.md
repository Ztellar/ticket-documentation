# Spot: Checklist de Desarrollo

## Fase 0: Setup (1 semana)
- [x] Docker Compose (Postgres 15, Redis 7)
- [x] NestJS boilerplate con módulos
- [x] TypeORM + primera migración
- [x] ESLint + Prettier + Husky
- [ ] GitHub Actions CI (lint, test)
- [x] .env.example + documentación

## Fase 1: Core Backend (2 semanas)
- [ ] AuthModule: register, login, refresh
- [ ] JWT strategy + guards + roles
- [ ] Generación wallet custodial
- [ ] Encriptación AES-256-GCM
- [ ] UsersModule: CRUD, profile
- [ ] ArtistsModule: CRUD
- [ ] VenuesModule: CRUD, sectores
- [ ] EventsModule: CRUD, tiers
- [ ] Rate limiting (ThrottlerModule)
- [ ] Unit tests >80%

## Fase 2: Blockchain + Pagos (3 semanas)
- [ ] BlockchainModule: Solana connection
- [ ] Helius RPC + QuickNode fallback
- [ ] Fee payer wallet
- [ ] Mint NFT con Metaplex
- [ ] PaymentsModule: WebPay
- [ ] WebPay sandbox tests
- [ ] PaymentsModule: MercadoPago
- [ ] PaymentsModule: Flow
- [ ] Webhooks con verificación firma
- [ ] Idempotencia webhooks
- [ ] IVA automático
- [ ] Integration tests

## Fase 3: Tickets + Validación (2 semanas)
- [ ] TicketsModule: reserva temporal (Redis 5min)
- [ ] Flujo compra completo
- [ ] Email confirmación
- [ ] ValidationModule: login staff
- [ ] Verificación NFC payload
- [ ] Anti-replay (nonce en Redis)
- [ ] Mark used + stats
- [ ] Soft delete

## Fase 4: Marketplace (1.5 semanas)
- [ ] Listar ticket reventa
- [ ] Validación precio máximo
- [ ] Búsqueda/filtrado
- [ ] Compra reventa
- [ ] Transfer NFT on-chain
- [ ] Distribución royalties

## Fase 5: Fan Clubs (1.5 semanas)
- [ ] LinkedWalletsModule
- [ ] Verificación firma multi-chain
- [ ] MultiChainModule: Alchemy ETH
- [ ] Cache verificaciones Redis
- [ ] FanClubsModule: CRUD
- [ ] Verificar membresía NFT
- [ ] Leer metadata/tier
- [ ] Preventa exclusiva
- [ ] Cron re-verificación 24h

## Fase 6: Frontend Web (2 semanas)
- [ ] Next.js 14 + Tailwind + ShadcnUI
- [ ] Auth pages
- [ ] Landing page
- [ ] Listado eventos + filtros
- [ ] Detalle evento + tiers
- [ ] Checkout + redirect pago
- [ ] Mis entradas
- [ ] Reventa: listar/quitar
- [ ] Marketplace
- [ ] Dashboard organizador
- [ ] Responsive mobile-first

## Fase 7: Mobile Android (2 semanas)
- [ ] Proyecto Kotlin + Gradle
- [ ] MVVM + Hilt
- [ ] Auth flow (Retrofit)
- [ ] Listado eventos
- [ ] Detalle + compra (WebView)
- [ ] Mis entradas
- [ ] NFC emisor: generar firma
- [ ] QR fallback
- [ ] App Validador: login staff
- [ ] App Validador: NFC lector
- [ ] App Validador: dashboard
- [ ] Firebase push
- [ ] Beta 10 usuarios

## Fase 8: Pre-producción (1 semana)
- [ ] Auditoría Slither
- [ ] Security scan Snyk
- [ ] Load testing k6
- [ ] Chaos testing
- [ ] Monitoring Sentry
- [ ] Deploy staging
- [ ] Migración Mainnet
