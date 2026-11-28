# Bitácora

Hallazgos y decisiones del proyecto, en orden cronológico.

## 2026-11-17

- Fuente: hoja "Ventas" del Excel del negocio. En landing se guarda el Excel original y su exportación a CSV UTF-8.
- Grano: una fila = un producto vendido en una fecha. No hay número de venta, así que no se sabe qué filas forman una misma venta.
- No se pueden calcular: ticket promedio y frecuencia por forma de pago (falta número de venta), ganancia (falta costo) y recompra (falta cliente).
- KPIs definidos: venta neta mensual, top productos (unidades e ingresos), descuentos, ingresos por forma de pago e ingresos por categoría.
- Según el negocio, regateo = SI significa que hubo descuento. Un precio cobrado mayor al de lista es un recargo válido (bordado, ajustes), no un error.
- Bronze: todo como string, con metadatos de ingesta. Columnas renombradas a snake_case porque Delta no acepta espacios. El Excel original en landing conserva la versión fiel.
- EDA: 2256 filas, igual que el Excel. 0 filas con datos rescatados. 4 filas vacías, se excluyen.

## 2026-11-18

- talla y observaciones tienen ~98 % de null: son opcionales en la práctica.
- Las columnas calculadas del Excel no son confiables: con celdas vacías dan `$-` (un cero falso) y con texto dan `#¡VALOR!`. Se recalculan en silver.
- Las filas sin fecha se excluyen, porque todos los KPIs son mensuales.
- regateo, forma_pago, categoria y producto tienen variantes por espacios, mayúsculas y errores de tipeo. Se estandarizan con trim, minúsculas y mapeo.
- "Equipamiento campo" es una categoría propia (confirmado por el negocio).
- En "Otros", `arreglos local` son gastos registrados como ventas con valor positivo (18 filas). Se excluyen. `arreglo` es un servicio del negocio y se mantiene.
- `valor de ventas` es un resumen de varias ventas: cuenta como ingreso, pero se excluye del top de productos.

## 2026-11-20

- producto: con trim y minúsculas baja de 708 a 595 valores. El top 40 no tiene variantes, así que no se mapea a mano.
- Los precios vienen como texto (` $10.00 `) con espacios invisibles. Se limpian con regexp_replace y se guardan como DECIMAL(10,2).
- 20 fechas con guion en lugar de barra y 25 con año `0202`. Se corrige el año a 2026: el negocio confirma que el mes es correcto. El día puede no serlo, pero los KPIs son mensuales.
- Cantidades de hasta 80 y precios de hasta $800 son ventas al por mayor: se mantienen.
- Los duplicados no se eliminan: sin número de venta no se distingue una doble digitación de dos ventas reales.
- Los registros de octubre se perdieron (confirmado por el negocio). Septiembre y noviembre están incompletos. Octubre se muestra como "sin datos", nunca como $0.
- precio_total no coincide con cantidad × precio en 10 filas (~$50). Se recalcula.
- regateo contradice los precios en 26 filas. Se quita de silver: se deduce de descuento > 0.
- Descuento y recargo van en columnas separadas, siempre positivas.
- Un filtro con NOT excluía 8 filas válidas por los null. Se corrige con `<=>`.

## 2026-11-26

- Gold con modelo OBT: hay un solo hecho (ventas) y no hay dimensiones separadas.
- Vista ventas_obt con columnas derivadas: mes, es_resumen_ventas y estado_mes (un mes con 20 o más días con ventas es completo).
- La variación mensual solo se calcula entre meses consecutivos y completos.

## 2026-11-27

- Top productos con row_number, desempatando por nombre para que el orden sea siempre el mismo.
- Junio tiene 4.32 % de descuento contra ~1.2 % en julio y agosto, por ventas al por mayor. No es un error.
- Las ventas sin forma de pago o sin categoría se muestran como "sin registrar".
- 13 filas sin cantidad o sin precio se excluyen de silver: no tienen monto y no aportan a ningún KPI. Silver queda con 2219 filas.
- Verificación final: la venta neta mensual coincide en los KPIs 1, 4 y 5.
- El negocio sigue registrando en el Excel hasta terminar de cargar su inventario en la app. La carga única deja de aplicar: queda pendiente automatizar la ingesta.
- Dashboard en Databricks con los 5 KPIs e ingesta automática y filtro por mes en kpis consideraados como pendiente.