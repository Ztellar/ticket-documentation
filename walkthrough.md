# TokenTicket - MVP Walkthrough & Runbook

Este documento describe cómo ejecutar y presentar la demo completa de TokenTicket, cubriendo el ciclo completo: Landing Page, Compra, Backend (Blockchain) y Validación Móvil.

## 1. Prerrequisitos de Ejecución

Necesitas **3 terminales** corriendo simultáneamente:

### Terminal 1: Backend (Smart Contracts & API)
Maneja la lógica de negocio, la wallet persistente y la conexión con Solana Devnet.
```bash
cd ticket-backend
npm start dev
```
> **Nota:** Si es la primera vez, verificará saldo. Si falla el Airdrop automático (error 429), carga 1 SOL manualmente en [faucet.solana.com](https://faucet.solana.com) a la dirección que muestre la consola.

### Terminal 2: Frontend (Web de Usuario)
La experiencia de compra del fan.
```bash
cd ticket-frontend
npm run dev -- -p 3001
```
> URL: [http://localhost:3001](http://localhost:3001)

### Terminal 3: Mobile (Wallet & Validator)
La app híbrida que actúa como billetera del usuario y scanner del validador.
```bash
cd ticket-mobile
npx expo start --clear
```
> Escanea el código QR con Expo Go (Android) o la Cámara (iOS).
> **Tip:** Si hay errores de red, usa `npx expo start --tunnel --clear`.

---

## 2. Guía de Presentación (Demo Script)

Sigue estos pasos para contar la historia de TokenTicket.

### Paso 1: La "Venta" (Frontend)
1.  Abre **[http://localhost:3001](http://localhost:3001)** en tu navegador.
2.  Muestra la **Landing Page**:
    *   Destaca la propuesta de valor: "Tus entradas. Sin reventa".
    *   Haz scroll hasta "Cómo funciona" (Compra -> Guarda -> Entra).
3.  Ve al **Evento Destacado** (Bad Bunny).
4.  Haz click en **"Comprar Ahora"** (Botón Indigo).
    *   *Narrativa:* "El usuario paga con tarjeta (simulado). Inmediatamente, el backend acuña un NFT real en Solana".
5.  Espera el popup de éxito:
    *   "Ticket Acuñado con éxito".
    *   Muestra el `Mint Address` (puedes copiarlo para demostrar que es real en [Solana Explorer](https://explorer.solana.com/?cluster=devnet)).

### Paso 2: La "Propiedad" (Mobile Wallet)
1.  Abre la App en tu celular.
2.  La App cargará automáticamente buscando el ticket en la Blockchain.
3.  Verás la tarjeta del evento ("Bad Bunny") con la imagen y detalles.
    *   *Narrativa:* "El ticket ya está en el bolsillo del usuario. Es incopiable y trazable".

### Paso 3: El "Acceso" (Mobile Validator)
1.  En la misma App, toca el botón **"Modo Validador"** (arriba a la derecha).
    *   La pantalla se vuelve verde oscuro (interfaz de staff).
2.  Toca el botón gigante **"Tocar para Escanear NFC"**.
3.  Simulación:
    *   Aparece "Verificando en Blockchain...".
    *   Resultado: **ACCESO CONCEDIDO** (Verde).
    *   *Narrativa:* "En menos de 2 segundos, verificamos criptográficamente que la entrada es válida y genuina".

---

## 3. Troubleshooting

*   **Error "Name too long":** Ya está corregido en el código.
*   **Backend "Rate Limit":** Carga manual en faucet.solana.com.
*   **App Móvil "Network Error":** Asegúrate de que el celular y la PC estén en la misma red WiFi, o usa `--tunnel`.

## 4. Próximos Pasos (Post-MVP)

1.  Integración real de WebPay / Stripe.
2.  Implementación de reventa controlada (Marketplace secundario).
3.  Soporte para múltiples eventos dinámicos desde Base de Datos.
