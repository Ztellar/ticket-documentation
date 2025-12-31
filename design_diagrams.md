# Spot: Diagramas de Diseño

## 1. Actores del Sistema

```mermaid
graph LR
    subgraph Usuarios
        Fan((Fan/Cliente))
        Org((Organizador))
        Artist((Artista))
        Staff((Validador))
    end

    subgraph Plataforma [Spot]
        direction TB
        UC1(Registrarse/Login)
        UC2(Comprar Entrada)
        UC3(Ver Mis Entradas)
        UC4(Revender Entrada)
        UC5(Unirse a Fan Club)
        UC6(Crear Evento)
        UC7(Ver Ventas)
        UC8(Crear Fan Club)
        UC9(Enviar Drops)
        UC10(Validar Entrada NFC)
    end

    Fan --> UC1 & UC2 & UC3 & UC4 & UC5
    Org --> UC6 & UC7
    Artist --> UC8 & UC9
    Staff --> UC10
```

---

## 2. Flujo de Compra (Venta Primaria)

```mermaid
sequenceDiagram
    participant U as Usuario
    participant W as Web/App
    participant API as Backend
    participant Pay as WebPay/MercadoPago
    participant Chain as Solana

    U->>W: Selecciona evento y sector
    W->>API: Reservar ticket (5 min)
    API-->>W: Ticket reservado
    W->>Pay: Iniciar pago (CLP)
    Pay->>U: Formulario de pago
    U->>Pay: Completa pago
    Pay->>API: Webhook: Pago exitoso
    API->>Chain: Mint NFT a wallet usuario
    Chain-->>API: TxHash
    API-->>W: Compra confirmada
    W-->>U: ¡Ya tienes tu entrada!
```

---

## 3. Flujo de Reventa (Mercado Secundario)

```mermaid
sequenceDiagram
    participant V as Vendedor
    participant C as Comprador
    participant API as Backend
    participant Pay as MercadoPago
    participant Chain as Solana

    V->>API: Listar entrada ($X, máx original+20%)
    API-->>V: Entrada en marketplace
    C->>API: Ver marketplace
    API-->>C: Lista de entradas
    C->>Pay: Pagar entrada
    Pay->>API: Webhook: Pago exitoso
    
    Note over API: Distribución automática
    API->>API: Vendedor: 85%
    API->>API: Organizador: 5%
    API->>API: Artista: 5%
    API->>API: Spot: 5%
    
    API->>Chain: Transferir NFT a comprador
    Chain-->>API: TxHash
    API-->>C: Entrada tuya
    API-->>V: Pago recibido
```

---

## 4. Flujo de Validación (Ingreso NFC)

```mermaid
graph TD
    A[Staff escanea NFC] --> B{Firma válida?}
    B -- No --> X1[⛔ DENEGADO: Firma inválida]
    B -- Sí --> C{Es dueño del NFT?}
    C -- No --> X2[⛔ DENEGADO: No es tuya]
    C -- Sí --> D{Ya usada?}
    D -- Sí --> X3[⛔ DENEGADO: Ya ingresaste]
    D -- No --> E[Marcar como usada]
    E --> F[✅ BIENVENIDO]
```

---

## 5. Fan Club: Preventa y Drops

```mermaid
sequenceDiagram
    participant A as Artista
    participant API as Backend
    participant FC as Fan Club Member
    participant Chain as Solana

    Note over A: Crear Fan Club
    A->>API: Crear club "Deathbat Club"
    API->>Chain: Deploy colección NFT

    Note over FC: Unirse al club
    FC->>API: Comprar membresía
    API->>Chain: Mint membresía NFT
    API-->>FC: Eres miembro

    Note over A: Crear evento con preventa
    A->>API: Evento con preventa 48h para club
    
    Note over FC: Preventa exclusiva
    FC->>API: Comprar en preventa
    API->>API: Verificar tiene NFT club
    API->>Chain: Mint ticket con diseño exclusivo

    Note over A: Enviar drop post-evento
    A->>API: Crear drop (Meet & Greet)
    API->>API: Buscar holders club que asistieron
    API->>Chain: Batch airdrop beneficio
    API-->>FC: ¡Tienes un Meet & Greet!
```

---

## 6. Tipos de Diseño de Entrada

| Tipo | Descripción | Quién lo obtiene |
|---|---|---|
| **Estándar** | Diseño base del evento | Compra pública |
| **Fan Club** | Diseño premium exclusivo | Compra en preventa club |
| **VIP** | Diseño dorado + beneficios | Tier especial |
| **Coleccionable** | Post-evento, recuerdo digital | Todos los asistentes |

---

## 7. Modelo de Datos Simplificado

```mermaid
erDiagram
    USER ||--o{ TICKET : owns
    USER ||--o{ FAN_CLUB_MEMBER : has
    ARTIST ||--|| FAN_CLUB : creates
    EVENT ||--o{ TICKET_TIER : has
    EVENT }|--|| VENUE : at
    TICKET_TIER ||--o{ TICKET : generates
    FAN_CLUB ||--o{ BENEFIT : offers

    USER {
        uuid id
        string email
        string wallet_public_key
    }
    ARTIST {
        uuid id
        string name
        boolean verified
    }
    FAN_CLUB {
        uuid id
        string name
        decimal price
    }
    EVENT {
        uuid id
        string name
        int resale_max_pct
    }
    TICKET {
        uuid id
        string mint_address
        string status
        string design_type
    }
```
