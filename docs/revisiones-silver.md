# Revisiones para silver

Tabla origen: `fabriperezchauca.bronze.ventas`

## Filas que se excluyen

- Filas completamente vacías (todas las columnas del negocio en null): 4 filas
- Filas sin `fecha`: todos los KPIs se calculan por mes
- Egresos: `categoria` = otros y `producto` = arreglos local (18 filas). Son gastos registrados con valor positivo e inflan los ingresos

## Limpieza y conversión por columna

| Columna | Transformación | Notas |
|---|---|---|
| `fecha` | `try_to_date(replace(fecha, '-', '/'), 'dd/MM/yyyy')`. Si el año no es 2026, se reemplaza por 2026 con `make_date` | 20 fechas con guion en lugar de barra. 25 fechas con el año mal digitado (`0202`): el mes es correcto, el día puede no serlo |
| `cantidad` | Convertir a número | Sin valores inválidos |
| `precio_original` | `regexp_replace(col, '[^0-9.-]', '')` y convertir a número | Formato ` $10.00 ` con espacios invisibles |
| `precio_dado` | Igual que `precio_original` | |
| `precio_total` | Igual que `precio_original` | `$-` (2) y `#¡VALOR!` (1) quedan null: no son montos reales |
| `ajuste_precio` | No se toma del archivo. Se recalcula: `cantidad × (precio_original − precio_dado)` | Si alguna de las tres es null, el ajuste es null. El valor del archivo tiene ceros falsos (`$-`) y errores |
| `regateo` | `trim` + mayúsculas | Valores finales: `SI`, `NO`, null |
| `forma_pago` | `trim` + minúsculas + mapeo: `tarjeta credito` → `tarjeta de credito` | Valores finales: `de una`, `efectivo`, `transferencia`, `tarjeta de credito`, null |
| `categoria` | `trim` + minúsculas + mapeo: `accesorio` → `accesorios`, `uniforme` → `uniformes`, `equipo táctico` y `equipamiento tactico` → `equipo tactico` | Valores finales: `accesorios`, `uniformes`, `equipo tactico`, `equipamiento campo`, `calzado`, `otros`, null |
| `producto` | `trim` + minúsculas | Sin mapeo manual: el top 40 no tiene variantes |
| `talla`, `observaciones` | Sin cambios | Opcionales: ~98 % null |

## Validaciones

- `regateo` debe coincidir con el ajuste: `SI` solo si ajuste > 0, `NO` si ajuste ≤ 0. **Acción ante contradicciones: pendiente**
- Un ajuste negativo es un recargo válido (confirmado con el negocio), no un error

## Para gold (no se aplica en silver)

- KPI 1 y demás KPIs mensuales: octubre = sin datos (no $0). Septiembre y noviembre marcados como parciales, con sus días con ventas
- KPI 2: excluir `valor de ventas` (resumen de varias ventas, no es un producto)
- KPI 3: excluir filas con ajuste null. Separar descuentos (ajuste > 0) de recargos (ajuste < 0)

## Pendientes del EDA

- Revisión 7: duplicados
- Revisión 8: rangos y outliers (incluye fechas fuera del período y cantidades ≤ 0)
- Revisión 9: análisis temporal
- Definir la acción ante contradicciones entre `regateo` y el ajuste