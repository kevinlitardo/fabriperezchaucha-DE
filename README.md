# Análisis de ventas - Fabriperez Chauca

## Descripción y objetivos

Negocio familiar de uniformes y accesorios policiales. Registraba sus ventas en un Excel antes de empezar a usar una app de inventario.

Objetivos:
- Limpiar y ordenar el registro de ventas.
- Calcular los KPIs más útiles para el negocio.
- Mostrarlos en un dashboard.
- Identificar qué le falta registrar al negocio para medir más cosas.

## Arquitectura

Arquitectura medallion en Databricks:

- Landing: el Excel original y su exportación a CSV, sin cambios.
- Bronze: el CSV como tabla, todo como texto, con metadatos de carga.
- Silver: datos limpios, tipados y validados.
- Gold: una tabla ancha (OBT) con columnas de negocio y una vista por KPI.

## Tecnologías

- Databricks (Free Edition), Unity Catalog, Delta Lake
- Spark SQL
- AI/BI Dashboards de Databricks
- Git y GitHub

## Modelado de datos

- Grano: una fila = un producto vendido en una fecha. El registro no tiene número de venta.
- Gold usa una OBT (One Big Table): hay un solo hecho (ventas) y no hay dimensiones separadas.
- Los KPIs son vistas sobre la OBT, para que siempre estén actualizados.

## Dashboard analítico

KPIs:
1. Venta neta mensual y variación contra el mes anterior.
2. Top productos por unidades vendidas y por ingresos.
3. Descuentos por regateo (% sobre el precio de lista).
4. Ingresos por forma de pago.
5. Ingresos por categoría.

Visualizaciones:
- Venta neta por mes, marcando los meses incompletos.
- Top 10 productos.
- Total de descuentos por mes.
- Participación de cada forma de pago y de cada categoría por mes.
- Filtro por mes.

## Origen de datos

- Hoja "Ventas" del Excel del negocio, de junio a noviembre de 2026.
- Los registros de octubre se perdieron, y septiembre y noviembre están incompletos.
- Carga manual: Excel → CSV → volumen de landing. La ingesta automática queda como mejora futura.

## Estructura del repo

    00_setup/
        01_crear_catalogo_esquemas_volumen
    02_bronze/
        01_cargar_ventas
    03_eda/
        01_eda_general
        02_eda_kpis
    04_silver/
        01_ddl_silver_ventas
        02_cargar_silver_ventas
    05_gold/
        01_ddl_gold_ventas_obt
        02_crear_vistas_kpis
    docs/
        bitacora.md
        kpis.md
        decisiones-silver.md
        decisiones-gold.md