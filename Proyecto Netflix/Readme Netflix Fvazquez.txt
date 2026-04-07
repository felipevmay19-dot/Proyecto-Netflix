# Análisis de Plataforma Netflix con Power BI

## Descripción del Proyecto
Análisis interactivo completo del catálogo de **Netflix** (películas y series) utilizando **Power BI Desktop**.  
El proyecto transforma archivos CSV/XLSX crudos en un dashboard profesional que permite explorar tendencias de contenido, ratings, países, géneros, evolución temporal y recomendaciones.

**Objetivo principal**: Responder preguntas de negocio clave para entender el comportamiento del catálogo de Netflix y apoyar decisiones estratégicas de contenido.

## 🎯 Objetivos del Proyecto
- Limpiar y modelar datos de **más de 10.000 títulos** de Netflix.
- Crear un dashboard interactivo con filtros cruzados y tooltips.
- Identificar tendencias por género, país, año y rating.
- Generar insights accionables sobre el contenido más popular y menos visto.

## 🛠️ Tecnologías y Herramientas Utilizadas

| Herramienta          | Uso                                      |
|----------------------|------------------------------------------|
| Power BI Desktop     | Creación del dashboard                   |
| Power Query          | Limpieza y transformación ETL            |
| DAX                  | Medidas y cálculos personalizados        |
| Excel + CSV/XLSX     | Fuente de datos original                 |
| GitHub               | Versionamiento y documentación           |

## 📂 Estructura del Repositorio
netflix-powerbi/
├── Netflix_Dataset.csv (original)
├── Netflix_Analysis.pbix ← Dashboard final
├── data/ ← Archivos limpios (opcional)
├── images/ ← Capturas del dashboard
├── README.md
└── .gitignore
text## 📊 Descripción del Dataset
Fuente oficial: **Netflix Top 10** (datos públicos semanales)  
- `all-weeks-global.xlsx` y `all-weeks-countries.xlsx` → Descargados de [top10.netflix.com](https://top10.netflix.com/)  
- `Subscribers.xlsx` → Datos complementarios de suscriptores por región y trimestre (fuente: curso Senpai Academy).

**Columnas principales**:
- week, category, weekly_rank, show_title, season_title, weekly_hours_viewed, etc.
- Datos de suscriptores por región (UCAN, EMEA, LATAM, APAC).

## 🧹 Limpieza de Datos (Power Query)
**Tabla all_weeks_global**:
1. Eliminar columnas innecesarias (`runtime_override_flag`, `is_staggered_launch`, `Episode_launch_details`).
2. Extraer texto antes del delimitador en columna duplicada.
3. Renombrar `category` → `Category_group`.

**Tabla all_weeks_countries**: Renombrar `category` → `Category_group`.

**Tabla Subscribers**:
- Cambio de tipos de datos y fechas (locale).
- Unpivot de columnas de regiones.
- Multiplicar suscriptores × 1.000.000.
- Renombrar columnas finales.

## 🗄️ Modelo de Datos
- **Tablas de hechos** (Fact): `all_weeks_global, `all_weeks_countries`, `Subscribers`.
- **Tablas de dimensiones** (Dim): `dim_category`, `dim_location`, `00_calendar`.
- **Clave única** (`Show_key`): `Category_group & "-" & Season_title & "-" & Show_title`.
- Relaciones: Estrella (star schema) optimizada.

**Columnas calculadas importantes**:
```dax
// Show_key (ambas tablas)
Show_key = 
'all_weeks_countries'[Category_group] & "-" & 
'all_weeks_countries'[Season_title] & "-" & 
'all_weeks_countries'[Show_title]
Columna Market (agrupación regional):
market = 
SWITCH(
TRUE(),
'all_weeks_countries'[country_name] IN {"United States", "Canada"}, "UCAN",
'all_weeks_countries'[country_name] IN {"Austria", "Belgium", "Bulgaria", "Croatia", "Czech Republic", "Denmark", "Estonia", "Finland", "France", "Germany", "Greece", "Hungary", "Iceland", "Ireland", "Italy", "Latvia", "Lithuania", "Luxembourg", "Malta", "Netherlands", "Norway", "Poland", "Portugal", "Romania", "Serbia", "Slovakia", "Slovenia", "Spain", "Sweden", "Switzerland", "Ukraine", "United Kingdom"}, "EMEA",
'all_weeks_countries'[country_name] IN {"Argentina", "Bahamas", "Bolivia", "Brazil", "Chile", "Colombia", "Costa Rica", "Dominican Republic", "Ecuador", "El Salvador", "Guadeloupe", "Guatemala", "Honduras", "Jamaica", "Martinique", "Mexico", "Nicaragua", "Panama", "Paraguay", "Peru", "Trinidad and Tobago", "Uruguay", "Venezuela"}, "LATAM",
'all_weeks_countries'[country_name] IN {"Australia", "New Caledonia", "New Zealand", "Bahrain", "Bangladesh", "Cyprus", "Hong Kong", "India", "Indonesia", "Israel", "Japan", "Jordan", "Kuwait", "Lebanon", "Malaysia", "Maldives", "Oman", "Pakistan", "Philippines", "Qatar", "Saudi Arabia", "Singapore", "South Korea", "Sri Lanka", "Taiwan", "Thailand", "Turkey", "United Arab Emirates", "Vietnam"}, "APAC",
'all_weeks_countries'[country_name] IN {"South Africa", "Réunion", "Nigeria", "Morocco", "Mauritius", "Kenya", "Egypt"}, "EMEA", -- Asignar a EMEA
'all_weeks_countries'[country_name] IN {"Russia"}, "APAC", -- Asignar a APAC
"Unknown"
)
Tablas calculadas:

dim_category (con SUMMARIZE + UNION + DISTINCT).
dim_location.
00_calendar (con columnas de tiempo y Week Index).

📈 Medidas DAX creadas (principales)

Count Top 10, Count Top 10 for #1.
Global Weekly Hours.
Info as of, Quarter Cerrado.
SelectedCountries y SelectedMarkets (dinámicas para slicers).
Subscribers Global, Total países Netflix.

Tabla Slicer para formato dinámico (Thousands / Millions / Billions).



Desafíos y aprendizajes

Manejo de grandes volúmenes de datos y optimización de DAX.
Creación de medidas dinámicas según selección de slicers.
Aprendizaje: Importancia del modelo estrella para rendimiento.




Proyecto realizado como parte del curso Senpai Academy.
Disponible en GitHub: [https://github.com/felipevmay19-dot]

Autor: Felipe Vázquez Analista de Datos.