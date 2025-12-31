# Plan de MVP "Proof of Concept" para Pitch

Este documento define la estrategia para un MVP Demo (Producto Mínimo Viable para Demostración) enfocado exclusivamente en **"Show, Don't Tell"** (Mostrar, no decir). El objetivo es deslumbrar en un pitch de 5-10 minutos demostrando funcionalmente la tesis del negocio.

## 1. El Objetivo de la Demo
Demostrar en vivo que TokenTicket soluciona los tres dolores principales sin fricción técnica:
1.  **Compra sin Fricción:** Una billetera se crea sola en segundo plano.
2.  **Seguridad Total:** Validación criptográfica instantánea (adiós QR falsos).
3.  **Monetización Secundaria:** El organizador gana dinero cuando el usuario revende, sin intermediarios.

## 2. Alcance Estricto del MVP (4 Semanas de Desarrollo Demo)

### A. Lo que SÍ vamos a construir (The Happy Path)
Solo construiremos el "camino feliz" para la demo. Sin manejo de errores complejos ni casos borde.

1.  **Web App (Versión Simplificada):**
    *   **Login:** "Entrar con Google" (usando Web3Auth/Firebase).
    *   **Dashboard Evento:** Un solo evento hardcodeado o cargado: "Gran Concierto Demo".
    *   **Flujo de Compra:** Botón "Comprar Ticket" -> Pasarela Falsa (Click para aprobar) -> **¡Mágicamente tienes un NFT!**

2.  **App Móvil (Usuario - "La Billetera Invisible"):**
    *   **Home:** Muestra el Ticket del concierto con un diseño increíble (Modelo 3D o Arte animado).
    *   **Botón "Revender":** Permite poner el ticket a la venta por un precio fijo.
    *   **Botón "Entrar":** Genera el código para validar (NFC/QR dinámico).

3.  **App Móvil (Validador - "El Portero"):**
    *   **Modo Escáner:** Lee el NFC/QR del usuario.
    *   **Resultado:** Pantalla VERDE gigante ("VÁLIDO - Juan Perez") o ROJA.
    *   **KPI en tiempo real:** Contador "Tickets Ingresados: 1/150".

4.  **Backend & Blockchain:**
    *   Smart Contract básico en Solana Devnet.
    *   Regla de Royalty del 10% hardcodeada.

### B. Lo que NO vamos a construir (Out of Scope)
*   Panel de administración complejo para crear cualquier evento.
*   Integración real con bancos (Stripe de verdad).
*   Soporte al cliente, devoluciones, cancelación de eventos.
*   KYC (Verificación de identidad real).

---

## 3. Guion de la Demo (El "Wow" Factor)

**Minuto 0-1: La Promesa (Web)**
*   Abres la web.
*   "Voy a comprar una entrada para el concierto de Bad Bunny".
*   Login con Google (Clásico).
*   Click en comprar con tarjeta (Clásico).
*   **BOOM:** "Ya tengo mi entrada, y miren esto... no es un PDF".

**Minuto 1-2: La Billetera (App Móvil)**
*   Abres la App en tu celular.
*   Ahí está el ticket. Es un coleccionable giratorio.
*   "Esto no se puede fotocopiar. Mi identidad está atada criptográficamente a este activo".

**Minuto 2-3: La Validación (NFC/QR)**
*   "Llego al estadio".
*   Tomas *otro* celular (el del guardia).
*   Acercas los dos teléfonos.
*   **BEEP instantáneo ( < 1 seg).** Pantalla Verde.
*   "Acabamos de validar una transacción en la Blockchain de Solana en tiempo real".

**Minuto 3-4: El Mercado Secundario (The Money Shot)**
*   "Pero espera, no puedo ir al final".
*   En la app de usuario: Click "Vender Entrada".
*   "La vendo a $100".
*   Entras con *otro* usuario (el comprador). La ves en el marketplace, la compras.
*   **Clímax:** Muestras el Dashboard del Organizador.
*   "Miren aquí: El organizador acaba de recibir automáticamente $10 (10%) de esa reventa. Sin perseguir a nadie. Código puro".

---

## 4. Stack Tecnológico "Demo-Ready"
Para validar que **funciona**:

*   **Blockchain:** Solana Devnet (Gratis, Rápida).
*   **Frontend:** Next.js + Tailwind (Vercel).
*   **Mobile:** Expo (React Native) -> Se puede instalar fácil en los demos con QR.
*   **NFC:** `react-native-nfc-manager` (Clave para el efecto "Wow" físico).

## 5. Próximos Pasos Técnicos para el MVP
1.  Desplegar contrato de NFT estándar (Metaplex) en Devnet.
2.  Crear API NestJS que actúe como "billetera custodia" para firmar por el usuario.
3.  Diseñar los pantallazos clave en Figma para asegurar el impacto visual.

---
**Conclusión:**
Este MVP no es el producto final, es la **evidencia** de que la tecnología ya no es una barrera, sino un habilitador de negocios.
