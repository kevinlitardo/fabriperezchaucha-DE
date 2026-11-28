# Decisiones de gold

## Modelo

- OBT (One Big Table): hay un solo hecho (ventas) y no hay dimensiones separadas. Un modelo estrella no aportaría nada.
- Todo son vistas: se actualizan solas cuando cambia silver y no necesitan un paso de carga.

## Columnas de la OBT

- `mes`: primer día del mes. Todos los KPIs agrupan por mes.
- `es_resumen_ventas`: marca las filas "valor de ventas", que no son un producto.
- `estado_mes`: "completo" si el mes tiene 20 o más días con ventas, si no "parcial".
- Se calculan una sola vez en la OBT para no repetir la misma lógica en cada KPI.

## Reglas de los KPIs

- Octubre no aparece en los KPIs: no hay datos, y no se muestra como $0.
- La variación mensual solo compara meses seguidos y completos. Si no, queda vacía.
- Top productos: ranking con row_number, desempatando por nombre para que el orden sea siempre el mismo.
- Las ventas sin forma de pago o sin categoría se muestran como "sin registrar", para no perder ingresos.
- Verificación: la venta neta de cada mes coincide en los KPIs 1, 4 y 5.