# Decisiones de silver

## Filas excluidas

- Filas vacías: no tienen datos.
- Filas sin fecha: todos los KPIs son mensuales.
- "arreglos local" en la categoría "otros": son gastos del local registrados como ventas.
- Filas sin cantidad o sin precio: no tienen monto y no aportan a ningún KPI.

## Columnas

- Precios: se quitan el símbolo $ y los espacios, y se guardan como DECIMAL(10,2) para no perder centavos.
- `precio_dado` pasa a llamarse `precio_venta`, para no confundirlo con el precio de lista.
- `fecha`: se corrige el guion por barra. Si el año está mal escrito (0202), se cambia a 2026. El mes es correcto según el negocio.
- `categoria` y `forma_pago`: se quitan espacios, se pasan a minúsculas y se unifican variantes y errores de tipeo.
- `producto`: se quitan espacios y se pasa a minúsculas. No se unifica a mano, porque el top de productos no tiene variantes.
- `talla` y `observaciones`: sin cambios. Casi siempre están vacías.

## Columnas recalculadas

- `total` = cantidad × precio de venta. El total del Excel tenía errores de digitación.
- `descuento` y `recargo`: diferencia entre el precio de lista y el de venta. Van en columnas separadas, siempre positivas, para que sean fáciles de leer y sumar.
- `ajuste_precio` y `precio_total` del Excel no se usan: con celdas vacías daban ceros falsos o errores.
- `regateo` no se incluye: es lo mismo que descuento > 0 y en 26 filas contradecía los precios.

## Lo que no se cambia

- Duplicados: se mantienen. Sin número de venta, no se puede saber si son errores o ventas iguales.
- Ventas grandes (hasta 80 unidades o $800): se mantienen. Son ventas al por mayor.