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
