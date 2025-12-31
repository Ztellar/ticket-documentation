# Spot: Modelo de Negocios

## 1. Propuesta de Valor

| Stakeholder | Beneficio |
|-------------|-----------|
| **Fan** | Entradas auténticas, precio justo |
| **Organizador** | Royalties reventa, datos reales |
| **Artista** | Ingresos secundarios, conexión fans |
| **Spot** | Comisiones transacciones |

---

## 2. Comisiones Procesadores Chile

| Procesador | Comisión | IVA | Total |
|------------|----------|-----|-------|
| WebPay | 2.95% | +19% | ~3.5% |
| MercadoPago | 3.49% | incluido | 3.49% |
| Flow | 2.9% + $150 | +19% | ~3.6% |

**Promedio: ~3.5%**

---

## 3. Estructura de Fees

### Venta Primaria
```
Precio entrada:           $50.000 CLP
+ Service Fee (8%):       $4.000 CLP
─────────────────────────────────────
Subtotal:                 $54.000 CLP
+ Fee Procesador (3.5%):  $1.890 CLP
─────────────────────────────────────
COMPRADOR PAGA:           $55.890 CLP

DISTRIBUCIÓN:
├── Organizador:          $50.000 (100% base)
├── Spot (neto):          $2.110 (fee - procesador)
└── Procesador:           $1.890
```

### Reventa (límite +20%)
```
Precio vendedor:                $60.000 CLP
+ Service Fee Spot (5%):        $3.000 CLP
+ Royalty Organizador (5%):     $3.000 CLP
+ Royalty Artista (5%):         $3.000 CLP
─────────────────────────────────────────────
Subtotal:                       $69.000 CLP
+ Fee Procesador (3.5%):        $2.415 CLP
─────────────────────────────────────────────
COMPRADOR PAGA:                 $71.415 CLP

DISTRIBUCIÓN:
├── Vendedor:             $60.000 (100% de su precio)
├── Organizador:          $3.000
├── Artista:              $3.000
├── Spot (neto):          $585
└── Procesador:           $2.415
```

---

## 4. IVA (19%)

```
Sobre comisión Spot: $4.000
IVA a pagar:         $760
Neto real Spot:      $3.240

Sobre comisión reventa: $3.000
IVA a pagar:            $570
Neto real Spot:         $2.430
```

---

## 5. Proyecciones Año 1

| Escenario | Tickets | Reventas (20%) | Ingreso Anual |
|-----------|---------|----------------|---------------|
| Pesimista | 150,000 | 30,000 | $315M CLP |
| Base | 300,000 | 60,000 | $650M CLP |
| Optimista | 500,000 | 100,000 | $1.1B CLP |

*Ajustado 3-4% inflación (Banco Central)*

### Detalle Mensual Base (25k tickets)
```
Primaria: 25,000 × $2,110 =     $52.7M CLP
Reventa:  5,000 × $585 =        $2.9M CLP
─────────────────────────────────────────────
INGRESO MENSUAL:                $55.6M CLP (~$61k USD)
```

---

## 6. Costos Operativos

| Item | Mensual USD |
|------|-------------|
| Servidores (Railway) | 75 |
| Database + Redis | 30 |
| Solana RPC | 100 |
| Fallback RPC | 50 |
| Gas Solana | 50 |
| Auditorías (amort.) | 100 |
| **Total** | **~405** |

### Margen Neto
```
Ingreso: ~$61,000 USD
Costos:  ~$405 USD
Margen:  99.3%
```

---

## 7. Cumplimiento Chile

| Regulación | Umbral | Acción |
|------------|--------|--------|
| CMF Ley Fintech | >100M CLP/año | Registro proveedor crypto |
| SII Facturación | >50M CLP ventas | DTE electrónico |
| Ley 19.628 | Todos | Consentimientos datos |

**Estrategia:** Mantener custodia bajo umbral CMF inicialmente.

---

## 8. Monetización Adicional

| Producto | Precio | Target |
|----------|--------|--------|
| Analytics Premium | $100k CLP/mes | Organizadores grandes |
| API Integración | $50k CLP/mes | Productoras |
| White-label | Negociable | Venues |

---

## 9. Comparativa Competitiva

| Plataforma | Fee Primaria | Fee Reventa | Control |
|------------|--------------|-------------|---------|
| Ticketmaster | 15-25% | 15-20% | ❌ |
| PuntoTicket | 10-15% | N/A | ❌ |
| **Spot** | **~12%** | **~19%** | **✅** |

---

## 10. Riesgos

| Riesgo | Prob. | Impacto | Mitigación |
|--------|-------|---------|------------|
| Regulación crypto | Media | Alto | Bajo umbral CMF |
| Caída Solana | Baja | Alto | RPC fallback |
| Adopción NFC (~70%) | Media | Medio | QR fallback |
| Competencia copia | Media | Medio | First-mover |

---

## 11. Funding

| Fuente | Monto | Timing |
|--------|-------|--------|
| CORFO Growth | Hasta 100M CLP | 2026 |
| Chile Ventures | Seed | 2025-2026 |
| Start-Up Chile | 50M CLP | 2025 |

**Preparar:** One-pager con estos documentos.

---

## 12. Decisiones Finales

| Aspecto | Decisión |
|---------|----------|
| Fee primaria | 8% |
| Fee reventa | 5% |
| Límite reventa | Define artista |
| Quién paga fees | Comprador |
| Transferencias | No permitidas |
| Gas blockchain | Spot absorbe |
| Fan clubs | Verificación multi-chain |
