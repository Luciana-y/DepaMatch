# Reporte de Calidad de Datos: Dataset de Departamentos en Lima (`departamentos_lima.csv`)

## 1. Resumen Ejecutivo

Este informe documenta la evaluación integral de calidad del dataset conformado por **1,806 departamentos individuales** en Lima Metropolitana y el Callao, recolectados a partir de los portales *Adondevivir* y *Urbania*. Se analizan los valores faltantes (*missing values*), el tratamiento de duplicados, la normalización de tipos de datos, la detección de valores sospechosos/outliers y las limitaciones conocidas del dominio inmobiliario.

---

## 2. Análisis de Valores Faltantes (*Missing Values*)

| Categoría de Atributos | Columnas | % Faltante | % Completitud | Evaluación de Calidad y Contexto |
| :--- | :--- | :---: | :---: | :--- |
| **Identificadores y Metadatos** | `id_anuncio`, `fuente`, `tipo_operacion`, `url`, `fecha_scraping` | **0.00%** | **100.00%** | Integridad absoluta en todos los registros. |
| **Ubicación y Coordenadas** | `distrito`, `direccion`<br>`latitud`, `longitud` | **0.00% - 0.17%**<br>5.09% | **99.83% - 100.00%**<br>94.91% | Coordenadas GPS geolocalizadas presentes en 1,714 inmuebles. |
| **Valorización Monetaria** | `precio`, `moneda`, `precio_soles`, `precio_m2` | **0.44%** | **99.56%** | Altísima completitud; el 0.44% restante corresponde a avisos de "precio a consultar". |
| **Distribución y Espacio Físico** | `dormitorios`, `area_total`<br>`banos`<br>`area_construida`<br>`antiguedad`<br>`estacionamientos`<br>`mantenimiento`<br>`piso` | **0.00%**<br>2.10%<br>12.51%<br>25.69%<br>33.67%<br>29.13%<br>66.11% | **100.00%**<br>97.90%<br>87.49%<br>74.31%<br>66.33%<br>70.87%<br>33.89% | Habitaciones y área total al 100%. Estacionamientos y pisos son omitidos por los propietarios si el departamento no tiene cochera o está en edificio bajo. |
| **14 Amenidades Binarias** | `ascensor`, `balcon`, `terraza`, `piscina`, `gimnasio`, `cochera`, `deposito`, `vista_al_mar`, `parrilla`, `areas_verdes`, `seguridad_24_7`, `coworking`, `juegos_infantiles`, `pet_friendly` | **0.00%** | **100.00%** | Extraídas mediante etiquetas del portal y expresiones regulares (NLP) sobre el texto. Si no se menciona la amenidad, se establece en `0`. |
| **Metadatos de Proyectos** | `estado_inmueble`, `nombre_proyecto`, `caracteristicas` | **98.28% - 98.45%** | **1.55% - 1.72%** | Dispersos por diseño: aplican a proyectos en planos/estreno multifamiliares, los cuales se filtraron para priorizar departamentos individuales. |

---

## 3. Estrategia y Resultados de Deduplicación

Los portales inmobiliarios presentan una alta tasa de anuncios redundantes debido a la sindicación entre múltiples corredores inmobiliarios y la republicación de inmuebles no vendidos. Se implementó una canalización de deduplicación en 3 niveles:

1. **Deduplicación por Identificador Exacto**: `(fuente, tipo_operacion, id_anuncio)` para el descarte de publicaciones idénticas repetidas por paginación.
2. **Deduplicación por URL Canónica**: `(tipo_operacion, url)`para eliminar endpoints web duplicados.
3. **Deduplicación por Huella Física del Inmueble**: `(tipo_operacion, titulo, precio_soles, area_total, distrito)` para filtrar inmuebles sindicados simultáneamente por diferentes agentes o agencias.

* **Total de Anuncios Crudos Analizados**: 3,600+
* **Total de Duplicados Descartados**: ~1,800 registros
* **Departamentos Únicos y Limpios Consolidados**: **1,806 registros**

---

## 4. Inconsistencias de Tipos de Datos y Normalización

* **Estandarización de Monedas**: Los precios publicados en USD (aprox. 65% de la venta en Lima) y PEN se convirtieron a una columna unificada `precio_soles` aplicando la tasa de cambio de mercado ($1\text{ USD} = 3.75\text{ PEN}$).
* **Saneamiento de Textos y Entidades HTML**: Se decodificaron entidades HTML y se eliminaron saltos de línea  y caracteres corruptos (*mojibake*) para asegurar un formato CSV uniforme.
* **Normalización de Distritos**: Se mapearon variaciones ortográficas y nombres coloquiales (ej. `"surco"`, `"santiago de surco"`, `"lima cercado"`, `"magdalena"`) a los nombres oficiales en formato *Proper Case*.

---

## 5. Detección y Tratamiento de Valores Sospechosos y Outliers

* **Errores Tipográficos en el Punto Decimal de Áreas**: Algunos agentes registran metrajes sin punto decimal (ej. `135200` en lugar de `135.2` $\text{m}^2$). El pipeline detecta valores en el rango $[10,000 - 999,999]$ y los reescala automáticamente mediante comprobación cruzada con la descripción.
* **Límites de Cordura Física**:
  * **Venta**: $20 \le \text{area\_total} \le 1,000\,\text{m}^2$; $\text{precio\_soles} \ge 100,000$.
  * **Alquiler**: $20 \le \text{area\_total} \le 1,000\,\text{m}^2$; $1,000 \le \text{precio\_soles} \le 50,000$.
  * Aquellos registros con inconsistencias físicas insalvables tienen su `precio_m2` marcado como `NaN` para no distorsionar análisis estadísticos.

---

## 6. Limitaciones Conocidas y Recomendaciones de Modelado

1. **Diferencia de Escalas por Tipo de Operación**:
   * En `tipo_operacion == 'venta'`, los precios representan el valor de compra del activo (S/ 250,000 a S/ 25,000,000).
   * En `tipo_operacion == 'alquiler'`, los precios representan la cuota mensual de arrendamiento (S/ 1,300 a S/ 15,000/mes).
2. **Reporte de Mantenimiento**:
   * La cuota de `mantenimiento` es declarada voluntariamente por el anunciante (presente en ~83% de alquileres y ~55% de ventas). En edificios antiguos o de pocos pisos sin ascensor suele ser S/ 0.
3. **Declaraciones en Descripciones**:
   * Las amenidades binarias extraídas reflejan lo anunciado por el vendedor (por ejemplo, `"vista al mar"` puede referirse tanto a primera línea de malecón como a vista lateral).
4. **Alcence del Scrapping**
   * El dataset representa únicamente los anuncios que pudieron ser recopilados con el scraping. Por ello, no necesariamente representa la totalidad del mercado inmobiliario.
