# Spot: Esquema de Base de Datos

## Diagrama ER

```
USERS ─1:N─ TICKETS ─N:1─ TICKET_TIERS ─N:1─ EVENTS
  │            │                              │
  │ 1:N        │ 1:N                          │ N:1
  ▼            ▼                              ▼
LINKED_WALLETS TRANSACTIONS                 VENUES
  │
  │ N:1
  ▼
FAN_CLUBS ─1:N─ FC_MEMBERSHIPS
  │
  │ N:1
  ▼
ARTISTS
```

---

## Tablas

### users
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role VARCHAR(50) DEFAULT 'client', -- client, organizer, artist, staff, admin
    
    -- Wallet custodial
    wallet_public_key VARCHAR(100) NOT NULL,
    wallet_secret_encrypted TEXT NOT NULL,
    wallet_key_version INT DEFAULT 1,
    
    -- Perfil
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    phone VARCHAR(20),
    rut VARCHAR(12),
    
    -- Estado
    email_verified BOOLEAN DEFAULT FALSE,
    
    -- Auditoría
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    deleted_at TIMESTAMP
);
```

### linked_wallets
```sql
CREATE TABLE linked_wallets (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    blockchain VARCHAR(50) NOT NULL, -- ethereum, polygon, solana
    wallet_address VARCHAR(100) NOT NULL,
    verified_at TIMESTAMP DEFAULT NOW(),
    UNIQUE(user_id, blockchain, wallet_address)
);
```

### artists
```sql
CREATE TABLE artists (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    admin_user_id UUID REFERENCES users(id),
    name VARCHAR(200) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    image_url VARCHAR(500),
    royalty_wallet_address VARCHAR(100) NOT NULL,
    verified BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW(),
    deleted_at TIMESTAMP
);
```

### fan_clubs
```sql
CREATE TABLE fan_clubs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    artist_id UUID REFERENCES artists(id) ON DELETE CASCADE,
    name VARCHAR(200) NOT NULL,
    blockchain VARCHAR(50) NOT NULL,
    contract_address VARCHAR(100) NOT NULL,
    benefits JSONB DEFAULT '{}',
    /*
    {
        "presale_hours": 48,
        "exclusive_design": true,
        "discount_percent": 10
    }
    */
    tier_attribute_name VARCHAR(100),
    active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT NOW()
);
```

### fan_club_memberships
```sql
CREATE TABLE fan_club_memberships (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    fan_club_id UUID REFERENCES fan_clubs(id) ON DELETE CASCADE,
    linked_wallet_id UUID REFERENCES linked_wallets(id),
    nft_token_id VARCHAR(100),
    nft_tier VARCHAR(50),
    verified_at TIMESTAMP DEFAULT NOW(),
    expires_at TIMESTAMP,
    UNIQUE(user_id, fan_club_id)
);
```

### venues
```sql
CREATE TABLE venues (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(200) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    address VARCHAR(500),
    city VARCHAR(100),
    country VARCHAR(100) DEFAULT 'Chile',
    sectors JSONB NOT NULL,
    /*
    [
        {"id": "platea", "name": "Platea", "capacity": 500},
        {"id": "cancha", "name": "Cancha", "capacity": 5000}
    ]
    */
    active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT NOW()
);
```

### events
```sql
CREATE TABLE events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organizer_id UUID REFERENCES users(id),
    artist_id UUID REFERENCES artists(id),
    venue_id UUID REFERENCES venues(id),
    
    name VARCHAR(300) NOT NULL,
    slug VARCHAR(150) UNIQUE NOT NULL,
    description TEXT,
    image_url VARCHAR(500),
    
    event_date TIMESTAMP NOT NULL,
    doors_open_at TIMESTAMP,
    
    -- Reventa
    resale_enabled BOOLEAN DEFAULT TRUE,
    resale_limit_percent INT DEFAULT 20,
    organizer_royalty_percent DECIMAL(5,2) DEFAULT 5.00,
    artist_royalty_percent DECIMAL(5,2) DEFAULT 5.00,
    
    status VARCHAR(50) DEFAULT 'draft', -- draft, published, on_sale, completed
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    deleted_at TIMESTAMP
);
```

### ticket_tiers
```sql
CREATE TABLE ticket_tiers (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    event_id UUID REFERENCES events(id) ON DELETE CASCADE,
    
    name VARCHAR(100) NOT NULL,
    sector_id VARCHAR(50) NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    quantity INT NOT NULL,
    sold INT DEFAULT 0,
    
    -- Preventa fan club
    fan_club_id UUID REFERENCES fan_clubs(id),
    fan_club_presale_start TIMESTAMP,
    fan_club_presale_end TIMESTAMP,
    fan_club_price DECIMAL(10,2),
    
    -- Venta general
    sale_start TIMESTAMP,
    sale_end TIMESTAMP,
    max_per_user INT DEFAULT 4,
    
    -- Diseños
    design_url VARCHAR(500),
    fan_club_design_url VARCHAR(500),
    
    created_at TIMESTAMP DEFAULT NOW()
);
```

### tickets
```sql
CREATE TABLE tickets (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tier_id UUID REFERENCES ticket_tiers(id),
    owner_id UUID REFERENCES users(id),
    original_buyer_id UUID REFERENCES users(id),
    
    -- Blockchain
    mint_address VARCHAR(100) UNIQUE NOT NULL,
    mint_tx_hash VARCHAR(100),
    
    status VARCHAR(50) DEFAULT 'active', -- active, for_sale, used, cancelled
    
    -- Reventa
    resale_price DECIMAL(10,2),
    listed_at TIMESTAMP,
    
    -- Uso
    is_used BOOLEAN DEFAULT FALSE,
    used_at TIMESTAMP,
    validated_by UUID REFERENCES users(id),
    
    -- Metadata
    is_fan_club_purchase BOOLEAN DEFAULT FALSE,
    design_type VARCHAR(50) DEFAULT 'standard',
    
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    deleted_at TIMESTAMP
);
```

### transactions
```sql
CREATE TABLE transactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    ticket_id UUID REFERENCES tickets(id),
    type VARCHAR(50) NOT NULL, -- purchase, resale, refund
    
    buyer_id UUID REFERENCES users(id),
    seller_id UUID REFERENCES users(id),
    
    -- Montos CLP
    base_price DECIMAL(10,2) NOT NULL,
    spot_fee DECIMAL(10,2) NOT NULL,
    processor_fee DECIMAL(10,2) NOT NULL,
    organizer_royalty DECIMAL(10,2) DEFAULT 0,
    artist_royalty DECIMAL(10,2) DEFAULT 0,
    iva_amount DECIMAL(10,2) DEFAULT 0,
    total_paid DECIMAL(10,2) NOT NULL,
    
    -- Pago
    payment_method VARCHAR(50),
    payment_reference VARCHAR(200),
    payment_status VARCHAR(50) DEFAULT 'pending',
    
    -- Blockchain
    blockchain_tx_hash VARCHAR(100),
    
    created_at TIMESTAMP DEFAULT NOW()
);
```

---

## Índices

```sql
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_deleted ON users(deleted_at) WHERE deleted_at IS NULL;
CREATE INDEX idx_events_date ON events(event_date);
CREATE INDEX idx_events_status ON events(status);
CREATE INDEX idx_tickets_owner ON tickets(owner_id);
CREATE INDEX idx_tickets_status ON tickets(status);
CREATE INDEX idx_tickets_for_sale ON tickets(status) WHERE status = 'for_sale';
CREATE INDEX idx_transactions_buyer ON transactions(buyer_id);
CREATE INDEX idx_fc_memberships_user ON fan_club_memberships(user_id);
```

---

## Triggers

```sql
-- Auto-update sold count
CREATE OR REPLACE FUNCTION update_tier_sold()
RETURNS TRIGGER AS $$
BEGIN
    IF TG_OP = 'INSERT' THEN
        UPDATE ticket_tiers SET sold = sold + 1 WHERE id = NEW.tier_id;
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trigger_update_sold
AFTER INSERT ON tickets
FOR EACH ROW EXECUTE FUNCTION update_tier_sold();

-- Auto-update updated_at
CREATE OR REPLACE FUNCTION update_timestamp()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trigger_users_timestamp BEFORE UPDATE ON users
FOR EACH ROW EXECUTE FUNCTION update_timestamp();

CREATE TRIGGER trigger_events_timestamp BEFORE UPDATE ON events
FOR EACH ROW EXECUTE FUNCTION update_timestamp();

CREATE TRIGGER trigger_tickets_timestamp BEFORE UPDATE ON tickets
FOR EACH ROW EXECUTE FUNCTION update_timestamp();
```
