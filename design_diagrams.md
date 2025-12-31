# Especificación de Diseño y Procesos: TokenTicket

## 1. Estrategia de Abstracción Web3 (Invisible Wallet)
Para cumplir con el requisito de "abstraer la complejidad", el sistema implementará un modelo de **Billeteras Custodias (Custodial Wallets)** o **Inicio de Sesión Social (Web3Auth)**.

*   **Usuario Final:** Se registra con Google/Email. No ve "frases semilla" ni firma transacciones manualmente. El backend gestiona su llave privada de forma segura (o se usa MPC - Multi-Party Computation).
*   **Transacciones:** El usuario paga con tarjeta (simulado) y el sistema "atrás" acuña/transfiere el NFT a su billetera.
*   **Gas Fees:** El organizador o la plataforma subvencionan los costos de gas (Gasless Transactions) para que la experiencia sea 100% fluida.

---

## 2. Diagramas de Casos de Uso

### Actores
*   **Asistente (Fan):** Usuario final. Quiere comprar entradas y entrar al evento sin saber de crypto.
*   **Organizador:** Crea eventos, define reglas y envía beneficios (drops).
*   **Validador (Seguridad):** Verifica las entradas en la puerta.
*   **Sistema (Backend/Blockchain):** Gestiona la lógica automática.

```mermaid
graph LR
    %% Actores
    Fan((Asistente/Fan))
    Org((Organizador))
    Sec((Validador))

    %% Casos de Uso
    subgraph Platform [TokenTicket Platform]
        direction TB
        UC1("Registrarse con Email/Google")
        UC2("Comprar Entrada Fiat")
        UC3("Ver Entradas QR/NFC")
        UC4("Revender Entrada")
        UC5("Crear Evento & Aforo")
        UC6("Definir Reglas Royalty")
        UC7("Enviar Drop/Beneficio")
        UC8("Validar Entrada")
    end

    %% Relaciones
    Fan --> UC1
    Fan --> UC2
    Fan --> UC3
    Fan --> UC4
    
    Org --> UC5
    Org --> UC6
    Org --> UC7
    
    Sec --> UC8
    
    %% Notas (usando nodos de texto simulados is es necesario, o comentarios)
    %% Mermaid graph no soporta 'note right of' nativo igual que sequence, 
    %% pero es mas seguro que usecaseDiagram que falla.
```

---

## 3. Diagramas de Procesos (BPMN)

### 3.1. Proceso de Compra de Entrada (Abstracción Web3)
Este flujo muestra cómo transformamos un pago tradicional en una transacción blockchain transparente para el usuario.

```mermaid
sequenceDiagram
    participant User as Usuario (Web/Mobile)
    participant API as Backend (NestJS)
    participant DB as Base de Datos
    participant Chain as Solana Blockchain
    
    User->>API: 1. Solicita compra (EventoID, Cantidad)
    API->>DB: 2. Verifica aforo disponible
    DB-->>API: Aforo OK
    User->>API: 3. Realiza Pago (Simulado en USD/CLP)
    API->>API: 4. Valida pago exitoso
    
    par Minting Asíncrono (Invisible)
        API->>Chain: 5. Mint/Transfer NFT a Wallet del Usuario
        Chain-->>API: 6. Confirmación (TxHash)
        API->>DB: 7. Guarda referencia del NFT (Mint Address)
    end
    
    API-->>User: 8. "¡Compra Exitosa! Tu entrada está lista"
    Note over User, API: El usuario nunca vio una wallet ni pagó gas.
```

### 3.2. Proceso de Validación de Acceso (NFC/QR)
Validación segura offline-first u online rápida garantizando que el ticket es válido y no ha sido usado.

```mermaid
graph TD
    Start((Inicio)) --> Scan[Validador escanea NFC/QR del Usuario]
    Scan --> GetToken[Obtener Firma/Token del Dispositivo]
    GetToken --> CheckSig{¿Firma Válida?}
    
    CheckSig -- No --> Deny[⛔ Acceso Denegado: Firma Inválida]
    CheckSig -- Sí --> QueryChain{¿Es Dueño del NFT?}
    
    QueryChain -- No --> Deny2[⛔ Acceso Denegado: No posee entrada]
    QueryChain -- Sí --> CheckUsed{¿Entrada ya usada?}
    
    CheckUsed -- Sí --> Deny3[⛔ Acceso Denegado: Ya ingresó]
    CheckUsed -- No --> MarkUsed[Marcar Entrada como 'Ingresada' en DB/Chain]
    MarkUsed --> Grant[✅ ACCESO PERMITIDO]
    Grant --> End((Fin))
```

### 3.3. Ciclo de Vida del Fan Club (Drops & Fidelización)
Cómo el organizador interactúa con sus fans post-evento (Flujo BPMN en Carriles).

```mermaid
graph TB
    subgraph Organizador
        direction TB
        A["Inicio: Decide premiar fans"] --> B["Seleccionar Evento Pasado"]
        B --> C["Definir Beneficio (Promo/NFT)"]
        C --> D["Ejecutar Drop"]
    end
    
    subgraph Sistema ["Sistema (Backend & Blockchain)"]
        direction TB
        D --> E["Query: Buscar Holders"]
        E --> F["Filtrar: ¿Asistieron?"]
        F --> G{"¿Hay beneficiarios?"}
        G -- No --> H["Fin: Reportar vacío"]
        G -- Sí --> I["Batch Minting / Airdrop"]
        I --> J["Notificar Usuarios"]
    end
    
    subgraph Asistente
        direction TB
        J --> K["Recibe Notificación Push"]
        K --> L["Ver regalo en Billetera"]
    end
```

## 4. Actualización del Stack para Abstracción
*   **Auth:** Usaremos **Web3Auth** o una implementación propia de custodia cifrada en backend.
*   **Pagos:** Integración mock de Stripe/MercadoPago para simular el flujo "Web2".
*   **Gas:** Usaremos un "Fee Payer" (una wallet del sistema que paga todas las comisiones de los usuarios).
