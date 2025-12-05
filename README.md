# Análisis de Producción e Inyección de Pozos (2023--2025)

## 1. Objetivo

El objetivo de este proyecto fue transformar el informe del Laboratorio
2 en un producto de datos final y profesional, manteniendo la
restricción de una sola página. Para lograrlo, integré una narrativa de
datos (Data Storytelling) que guía al usuario desde la visión general
hasta la conclusión de negocio.

## 2. ETL aplicado

En la etapa de preparación de datos realicé las siguientes
transformaciones: - Convertí los campos *rectificado* y *habilitado* de
f/t a valores booleanos (TRUE/FALSE). - Normalicé las columnas **anio**
y **mes** como enteros. - Creé una columna **Fecha** con la fórmula
`#date([anio],[mes],1)` para relacionar el dataset con la tabla
calendario. - Eliminé comillas dobles duplicadas en campos de texto como
*areapermisoconcesion* y *areayacimiento*. - Convertí las columnas de
producción e inyección (`prod_pet`, `prod_gas`, `prod_agua`, `iny_*`) a
tipo decimal number. - Reemplacé valores nulos o negativos por 0 o los
marqué como "sin movimientos". - Validé que **idpozo** e **idempresa**
no tuvieran duplicados ni nulos, conservando siempre la fila más
reciente según *fecha_data*.

## 3. Modelo de datos (estrella)

Diseñé un modelo en esquema estrella compuesto por: - **FactProducción**
(tabla de hechos): producción de petróleo, gas y agua; inyección de
agua, gas, CO₂ y otros. - **DimPozo**: atributos técnicos del pozo
(tipo, estado, formación, profundidad). - **DimEmpresa**: datos de la
empresa (id, nombre, sigla). - **DimUbicación**: provincia, cuenca, área
de concesión. - **DimTiempo**: fechas (día, mes, año, trimestre).

Las relaciones son 1:M desde cada dimensión hacia la tabla de hechos,
con la columna **Fecha** como vínculo principal con la tabla calendario.

## 4. Medidas DAX creadas

Para este proyecto trabajé únicamente con las medidas principales que
resumen la producción e inyección:

``` dax
Petroleo Total = SUM(FactProduccion[prod_pet])
Gas Total = SUM(FactProduccion[prod_gas])
Agua Total = SUM(FactProduccion[prod_agua])
Inyeccion Total =
    SUM(FactProduccion[iny_agua]) +
    SUM(FactProduccion[iny_gas]) +
    SUM(FactProduccion[iny_co2]) +
    SUM(FactProduccion[iny_otro])
```

Estas medidas se utilizan en las tarjetas KPI y en los gráficos del
dashboard para mostrar indicadores clave de producción e inyección.

## 5. Dashboard con Storytelling

El dashboard se diseñó en un único lienzo siguiendo la narrativa **QUÉ →
POR QUÉ → ENTONCES QUÉ**:

### Zona Superior (QUÉ)

Tarjetas KPI con los indicadores principales (Petróleo, Gas, Agua,
Inyección).

### Zona Central (POR QUÉ)

-   Gráfico de tendencia temporal
-   Mapa geográfico
-   Distribución por empresa

### Zona de Conclusión (ENTONCES QUÉ)

Cuadro de texto con mensaje final y recomendación de negocio.

### Explicación de cada visual

-   **KPIs:** resumen del estado general de la producción e inyección.
-   **Gráfico temporal:** muestra la evolución mensual/anual y
    estabilidad de los valores.
-   **Mapa:** revela la distribución geográfica de la producción por
    provincia/cuenca.
-   **Ranking de empresas:** facilita comparar la participación relativa
    de cada empresa.
-   **Segmentadores:** permiten filtrar por empresa, tipo de pozo,
    estado y año/mes.

### Conclusión narrativa

> *"La producción se mantiene estable en el periodo 2023--2025, con
> mayor aporte de petróleo.\
> La inyección total refleja la importancia de mantener la eficiencia en
> las cuencas principales.\
> Se recomienda focalizar recursos en las provincias con mayor volumen
> para sostener la tendencia."*
