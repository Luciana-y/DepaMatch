# DepaMatch – Recomendador inteligente de propiedades

## Team Members

* Adrian Urbina Mendoza
* Breysi Salazar Medina
* Armando Martinez Palomino
* Luciana Yangali Cáceres

## Tentative Product Name

**DepaMatch – Recomendador inteligente de propiedades**

DepaMatch es una plataforma web inteligente orientada a facilitar la búsqueda de propiedades para **alquiler o compra**. El sistema permitirá que el usuario describa en lenguaje natural el tipo de vivienda que busca y, mediante técnicas de NLP y Machine Learning, transformará dicha descripción en un perfil de búsqueda para recomendar las propiedades con mayor compatibilidad.

## Initial Problem / Opportunity

La búsqueda tradicional de una propiedad requiere que el usuario establezca múltiples filtros de manera independiente, como presupuesto, ubicación, número de dormitorios, baños, área, cochera y otras características. Esto puede obligarlo a revisar una gran cantidad de anuncios para encontrar alternativas que realmente se ajusten a sus necesidades.

**DepaMatch** busca solucionar este problema permitiendo que el usuario describa su vivienda ideal mediante **lenguaje natural**. El sistema identificará automáticamente los atributos relevantes de la solicitud, construirá un perfil matemático y lo comparará con las propiedades disponibles.

Como resultado, se generará un **Top 5 de propiedades**, acompañado de un **Match Score** que represente el grado de compatibilidad entre cada inmueble y las preferencias del usuario.

La oportunidad consiste en transformar los datos inmobiliarios disponibles en recomendaciones personalizadas, pasando de una lógica basada únicamente en filtros a una experiencia de búsqueda basada en **similitud y compatibilidad**.

## Target Domain

**Dominio: Sector inmobiliario / Real Estate**

El producto está dirigido principalmente a personas interesadas en **alquilar o comprar una propiedad**, especialmente usuarios que necesitan comparar múltiples alternativas y que prefieren expresar sus necesidades de manera natural en lugar de configurar numerosos filtros.

Entre los usuarios potenciales se encuentran jóvenes que buscan independizarse, profesionales que desean trasladarse, parejas que buscan su primera vivienda, familias y compradores que desean explorar propiedades de acuerdo con sus preferencias y presupuesto.

El principal contexto de uso será la etapa inicial de **exploración y selección inmobiliaria**, donde existe una gran cantidad de propiedades y el usuario necesita reducirlas a un conjunto manejable de alternativas.

## Dataset Source

El producto requiere un **dataset inmobiliario** donde cada registro represente una propiedad y contenga características que permitan comparar los diferentes inmuebles.

El dataset principal seleccionado para el proyecto es:

**Dataset:** depatamentos_lima.csv
**Fuente:** `https://www.adondevivir.com` y `https://urbania.pe`
**Formato:** CSV
**Número de registros:** 1806
**Cobertura geográfica:** Lima Metropolitana

Las variables de interés incluyen, según su disponibilidad en la fuente, **precio, ubicación/distrito, tipo de propiedad, dormitorios, baños, superficie, cochera, amenidades y descripción textual del anuncio**. Estas variables permitirán construir la representación matemática de cada propiedad y posteriormente compararla con el perfil generado a partir de la solicitud del usuario.

