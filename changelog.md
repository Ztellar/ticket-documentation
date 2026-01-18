# Spot: Registro de Cambios

Este documento registra todos los cambios realizados en el proyecto.

---

## 2026-01-02 - Fase 0: Setup Inicial

### Docker y Base de Datos
- Creado `docker-compose.yml` con:
  - PostgreSQL 15 (puerto **5433** - cambiado para evitar conflicto con instalación local)
  - Redis 7 (puerto 6379)
  - Healthchecks configurados
  - Volúmenes persistentes

### Configuración
- Creado `.env.example` con todas las variables de entorno
- Creado `.env` con valores de desarrollo
- Variables incluidas:
  - Database: `DATABASE_HOST`, `DATABASE_PORT`, `DATABASE_USER`, `DATABASE_PASSWORD`, `DATABASE_NAME`
  - Redis: `REDIS_HOST`, `REDIS_PORT`
  - JWT: `JWT_SECRET`, `JWT_EXPIRES_IN`, `JWT_REFRESH_SECRET`, `JWT_REFRESH_EXPIRES_IN`
  - Wallet: `WALLET_ENCRYPTION_KEY`
  - Solana: `SOLANA_RPC_URL`, `SOLANA_NETWORK`
  - Pagos: WebPay, MercadoPago, Flow
  - Multi-chain: `ALCHEMY_API_KEY`

### Archivos de Configuración NestJS
- `src/config/database.config.ts` - Configuración de PostgreSQL
- `src/config/jwt.config.ts` - Configuración de JWT
- `src/config/app.config.ts` - Configuración general
- `src/config/index.ts` - Exportaciones

### Entidades TypeORM Creadas

#### Usuarios (`src/users/entities/`)
| Archivo | Descripción |
|---------|-------------|
| `user.entity.ts` | Usuario con wallet custodial, roles, soft delete |
| `linked-wallet.entity.ts` | Wallets externas vinculadas (multi-chain) |

#### Artistas (`src/artists/entities/`)
| Archivo | Descripción |
|---------|-------------|
| `artist.entity.ts` | Artista con royalty wallet |
| `fan-club.entity.ts` | Fan club con NFT contract y beneficios |

#### Venues (`src/venues/entities/`)
| Archivo | Descripción |
|---------|-------------|
| `venue.entity.ts` | Recinto con sectores JSONB |

#### Eventos (`src/events/entities/`)
| Archivo | Descripción |
|---------|-------------|
| `event.entity.ts` | Evento con control de reventa y royalties |
| `ticket-tier.entity.ts` | Tier con preventa fan club |

#### Tickets (`src/tickets/entities/`)
| Archivo | Descripción |
|---------|-------------|
| `ticket.entity.ts` | Ticket NFT con estados y reventa |
| `transaction.entity.ts` | Transacción con breakdown de fees e IVA |

### Módulos Actualizados
- `app.module.ts` - Integración TypeORM, ConfigModule, ThrottlerModule
- `events/events.module.ts` - TypeORM entities
- `events/events.service.ts` - Repository pattern
- `events/events.controller.ts` - Async methods
- `tickets/tickets.module.ts` - TypeORM entities
- `tickets/tickets.service.ts` - Repository pattern
- `tickets/tickets.controller.ts` - String IDs
- `tickets/dto/create-ticket.dto.ts` - class-validator decorators

### Dependencias Instaladas
```bash
npm install @nestjs/typeorm typeorm pg @nestjs/config @nestjs/jwt 
npm install @nestjs/passport passport passport-jwt bcrypt 
npm install class-validator class-transformer @nestjs/throttler ioredis
npm install -D @types/passport-jwt @types/bcrypt
```

### Estado Final
- ✅ Backend corriendo en `http://localhost:3000`
- ✅ PostgreSQL en `localhost:5433`
- ✅ Redis en `localhost:6379`
- ✅ TypeORM sincroniza tablas automáticamente (dev mode)

---

## Próximos Cambios (Fase 1)
- AuthModule: register, login, refresh, logout
- JWT strategy + guards
- Generación wallet custodial con encriptación AES-256-GCM
- UsersModule: CRUD completo
- ArtistsModule, VenuesModule

---

## 2026-01-06 - DTOs para Todas las Entidades

### Users (`src/users/dto/`)
| Archivo | Descripción |
|---------|-------------|
| `create-user.dto.ts` | Email, password, firstName, lastName, phone, rut, role |
| `update-user.dto.ts` | Partial, excluye password y email |

### Artists (`src/artists/dto/`)
| Archivo | Descripción |
|---------|-------------|
| `create-artist.dto.ts` | name, slug, description, imageUrl, royaltyWalletAddress |
| `update-artist.dto.ts` | Partial |
| `create-fan-club.dto.ts` | artistId, name, blockchain, contractAddress, benefits |
| `update-fan-club.dto.ts` | Partial, excluye artistId |

### Venues (`src/venues/dto/`)
| Archivo | Descripción |
|---------|-------------|
| `create-venue.dto.ts` | name, slug, address, city, sectors[] (nested validation) |
| `update-venue.dto.ts` | Partial |

### Events (`src/events/dto/`)
| Archivo | Descripción |
|---------|-------------|
| `create-event.dto.ts` | artistId, venueId, name, eventDate, resale config, tiers[] |
| `update-event.dto.ts` | Partial, excluye artistId y venueId |

### Tickets (`src/tickets/dto/`)
| Archivo | Descripción |
|---------|-------------|
| `create-ticket.dto.ts` | name, symbol, metadataUri, owner, tierId |
| `update-ticket.dto.ts` | Partial |

### Validaciones Usadas
- `@IsEmail()`, `@IsString()`, `@MinLength()`
- `@IsUUID()`, `@IsUrl()`, `@IsDateString()`
- `@IsNumber()`, `@Min()`, `@Max()`
- `@IsArray()`, `@ValidateNested()`, `@Type()`
- `@IsOptional()`, `@IsBoolean()`, `@IsEnum()`

---

## 2026-01-17 - Fase 1: AuthModule

### Archivos Creados
| Archivo | Descripción |
|---------|-------------|
| `src/auth/auth.module.ts` | Módulo con JWT, Passport, TypeORM |
| `src/auth/auth.service.ts` | Register, login, refresh tokens |
| `src/auth/auth.controller.ts` | Endpoints /auth/* |
| `src/auth/wallet.service.ts` | Generación wallet + AES-256-GCM |
| `src/auth/dto/*.ts` | RegisterDto, LoginDto, RefreshTokenDto |
| `src/auth/strategies/jwt.strategy.ts` | Validación JWT |
| `src/auth/guards/jwt-auth.guard.ts` | Protección rutas |
| `src/auth/guards/roles.guard.ts` | RBAC por rol |
| `src/auth/decorators/*.ts` | @Public, @Roles, @CurrentUser |

### Endpoints Implementados
```
POST /auth/register → {accessToken, refreshToken}
POST /auth/login → {accessToken, refreshToken}
POST /auth/refresh → {accessToken, refreshToken}
```

### Seguridad Implementada
- Passwords hasheados con bcrypt (10 rounds)
- Wallets encriptadas con AES-256-GCM
- JWT access token expira en 15 minutos
- JWT refresh token expira en 7 días

---

## 2026-01-18 - Fase 2: Blockchain + Pagos

### BlockchainModule Mejorado
| Funcionalidad | Descripción |
|---------------|-------------|
| RPC dinámico | Configurable via env (Helius/QuickNode) |
| Fee Payer | Wallet auto-generada con airdrop en Devnet |
| Transfer NFT | `transferTicket()` para reventas |
| Metadata | `getTicketMetadata()` consulta on-chain |

### PaymentsModule
| Provider | Estado |
|----------|--------|
| WebPay (Transbank) | Mock sandbox |
| MercadoPago | Mock sandbox |
| Flow.cl | Mock sandbox |

### Características
- Webhook processing con verificación HMAC-SHA256
- Idempotencia via cache in-memory
- Cálculo automático IVA (19%)
- Variables de entorno para todos los providers

---

## 2026-01-18 - Fase 3: Tickets + Validación

### ReservationService
- Reservas temporales en Redis (TTL 5 minutos)
- Hold counts por tier para prevenir overselling
- Extensión de reserva si está pagando

### PurchaseService
Flujo completo: **reserve → pay → mint NFT → confirm**
- Validación disponibilidad tier
- Integración con PaymentsService
- Minting automático post-pago
- Creación de tickets en DB

### ValidationModule (NFC-only)
| Endpoint | Descripción |
|----------|-------------|
| GET /validation/nonce | Obtener nonce anti-replay |
| POST /validation/validate | Validar ticket NFC |
| POST /validation/manual | Validación manual (fallback) |
| POST /validation/undo/:id | Deshacer validación (5 min) |
| GET /validation/stats/:eventId | Estadísticas de validación |

### Seguridad Anti-Replay
1. Staff solicita nonce (válido 1 hora)
2. App genera payload: `ticketId|timestamp|signature`
3. Nonce consumido tras uso (one-time)
4. Timestamp validado < 5 minutos

---

## 2026-01-18 - Fase 4: Marketplace

### MarketplaceService
| Funcionalidad | Descripción |
|---------------|-------------|
| Listar ticket | Con validación `resaleLimitPercent` |
| Quitar de venta | Retorna a estado ACTIVE |
| Buscar listings | Filtros por evento, precio, paginación |
| Comprar reventa | Inicia pago + transferencia NFT |

### Modelo de Royalties (Corregido)
```
Precio vendedor: $100,000 CLP → Vendedor recibe 100%

Buyer paga:
├── Ticket:         $100,000
├── Spot Fee (5%):   $5,000 + IVA $950
├── Organizer:       $3,000 (pass-through)
├── Artist:          $2,000 (pass-through)
├── Procesador:      $3,300
└── TOTAL:        $114,250
```

### Payout Entity
- Tipos: `seller`, `organizer`, `artist`
- Estados: `pending` → `processing` → `completed`
- Creados automáticamente al confirmar reventa

---

## 2026-01-18 - Fase 5: Fan Clubs (En Progreso)

### Objetivos
- LinkedWalletsModule para conexión multi-chain
- Verificación NFT en Ethereum/Polygon/Solana
- Membresías automáticas via NFT holdings

