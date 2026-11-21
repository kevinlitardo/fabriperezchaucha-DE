# Catálogo de KPIs

## KPI 1 - Venta neta mensual

| Campo | Detalle |
|---|---|
| Definición | Total cobrado por ventas en el mes, después de descuentos |
| Fórmula | Suma de `Precio Total` por mes, y variación % vs. el mes anterior |
| Columnas | `Fecha`, `Precio Total` |
| Periodicidad | Mensual |
| Decisión que apoya | Saber si el negocio crece o cae, y si un cambio (precios, personal, promociones) se reflejó en las ventas |
| Limitaciones | No es ganancia: no hay datos de costo (ver ADR 003) |

## KPI 2 - Top productos

| Campo | Detalle |
|---|---|
| Definición | Top productos por unidades vendidas (2a) y por ingresos (2b) |
| Fórmula | 2a: suma de `Cantidad` agrupada por `Producto`, de mayor a menor. 2b: suma de `Precio Total` agrupada por `Producto`, de mayor a menor |
| Columnas | `Producto`, `Cantidad`, `Precio Total` |
| Periodicidad | Mensual |
| Decisión que apoya | 2a: qué reponer más rápido y con más frecuencia. 2b: qué productos sostienen los ingresos y no pueden faltar aunque se vendan poco |
| Limitaciones | `Producto` es texto libre sin catálogo: el ranking es confiable solo después de unificar nombres en silver. No hay ganancia: faltan datos de costo (ver ADR 003) |

## KPI 3 - Descuento por regateo

| Campo | Detalle |
|---|---|
| Definición | Porcentaje y monto del valor de lista que se dejó de cobrar por regateo |
| Fórmula | Descuentos ($) = suma de `Ajuste de precio ($)` donde Ajuste > 0. % descuento = Descuentos ÷ suma de (`Cantidad` × `Precio Original`) × 100. Agrupado por mes. Primero se suma y después se divide (no se promedian porcentajes por fila) |
| Dato de contexto | Recargos ($) = suma del valor absoluto de `Ajuste de precio ($)` donde Ajuste < 0. No es un KPI: no apoya ninguna decisión, solo da contexto. Se reporta separado para que no compense los descuentos |
| Columnas | `Fecha`, `Cantidad`, `Precio Original`, `Ajuste de precio ($)`, `Regateo` |
| Periodicidad | Mensual |
| Decisión que apoya | Cuánto dinero se deja de cobrar por regateo y si conviene ajustar los precios de lista o poner límites al regateo |
| Limitaciones | `Precio Original` se ingresa a mano: si está mal, el descuento también. Un ajuste negativo es un recargo válido, no un descuento (ver P-02). `Regateo` y `Ajuste` deben coincidir (ver V-01): las filas que se contradicen se tratan según lo que defina el EDA. No hay ganancia: faltan datos de costo (ver ADR 003) |

## KPI 4 - Ingresos por forma de pago

| Campo | Detalle |
|---|---|
| Definición | Ingresos y participación % de cada forma de pago sobre el total del mes |
| Fórmula | Ingresos = suma de `Precio Total` agrupada por `Forma de Pago`, de mayor a menor. Participación % = ingresos de la forma de pago ÷ suma total de `Precio Total` del mes × 100. Agrupado por mes |
| Columnas | `Fecha`, `Forma de Pago`, `Precio Total` |
| Periodicidad | Mensual |
| Decisión que apoya | Qué forma de pago promocionar y qué acuerdos negociar con bancos o apps de pago, ya que las comisiones se cobran sobre el monto |
| Limitaciones | No mide frecuencia de uso: una fila no es un pago (ver ADR 004). `Forma de Pago` se escribe a mano: variantes como "De una" y "deuna" se cuentan por separado hasta unificarlas en silver |

## KPI 5 - Ingresos por categoría

| Campo | Detalle |
|---|---|
| Definición | Ingresos y participación % de cada categoría sobre el total del mes |
| Fórmula | Ingresos = suma de `Precio Total` agrupada por `Categoría`, de mayor a menor. Participación % = ingresos de la categoría ÷ suma total de `Precio Total` del mes × 100. Agrupado por mes |
| Columnas | `Fecha`, `Categoría`, `Precio Total` |
| Periodicidad | Mensual |
| Decisión que apoya | En qué línea de productos invertir más (capital de compra, espacio de exhibición, variedad de surtido) y cuál reducir. Muestra el peso de líneas formadas por muchos productos baratos que no aparecen en el top de productos (KPI 2) |
| Limitaciones | Solo por ingresos: sumar unidades de productos distintos no es comparable. `Categoría` se escribe a mano: se debe verificar en el EDA que tenga pocos valores y sin variantes. No hay ganancia: faltan datos de costo (ver ADR 003) |