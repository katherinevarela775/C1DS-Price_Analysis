# C1DS — Análisis Exploratorio de Datos: Mercado Inmobiliario y Consumo Eléctrico

Proyecto de Análisis Exploratorio de Datos (EDA) que aborda dos dominios distintos: el mercado inmobiliario de California y el consumo eléctrico en Brasil.

La pregunta central que guía este trabajo es: **¿qué factores determinan el precio de las viviendas en California?** Complementariamente, se explora el patrón de consumo energético en Brasil.

Este repositorio contiene dos notebooks independientes que aplican la misma metodología de análisis a cada dataset: inspección inicial, limpieza y preprocesamiento, análisis univariado, bivariado y visualizaciones avanzadas.

## Datasets utilizados

| Dataset | Fuente | Descripción |
|---------|--------|-------------|
| California Housing Prices | Kaggle | Precios de vivienda en California (1990), a nivel de bloque censal |
| Brazilian Electricity Market | ANEEL | Consumo eléctrico mensual por estado, tipo de consumidor y sector económico (2004-2026) |

## Estructura del repositorio

```
├── data/
│   └── processed/
│       └── housing_clean.csv          # California Housing (post-limpieza)
├── notebooks/
│   ├── house_prices.ipynb             # EDA: Mercado inmobiliario de California
│   └── energy_analysis.ipynb          # EDA: Consumo eléctrico Brasil
├── C1DS-Price_Analysis.pdf            # Reporte del análisis
├── requirements.txt
└── README.md
```

## Notebook 1 — California Housing

Análisis exploratorio del dataset de precios de vivienda en California, a nivel de bloque censal (20.640 registros).

**Proceso:** inspección inicial → detección de techos artificiales → imputación de nulos → feature engineering (ratios estructurales) → análisis univariado y bivariado → correlaciones y pairplot → visualización geográfica y mapa RGB → segmentación por proximidad al océano.

**Hallazgos principales:**

- El ingreso mediano del vecindario (`median_income`) es el predictor individual más fuerte del precio, con una correlación de Pearson de 0.69 en datos crudos.
- Se detectaron techos artificiales en `median_house_value` ($500,001), `housing_median_age` (52 años) y `median_income` (15.0001). Solo se filtró el de la variable objetivo, ya que las otras dos son predictoras con correlación baja — el costo de eliminar registros no justificaba el beneficio.
- La distribución geográfica del precio no es lineal: existen dos clusters de alto valor claramente definidos (Área de la Bahía de San Francisco y costa de Los Ángeles), invisibles al análisis de correlación simple pero evidentes en el mapa geográfico.
- Tras la limpieza, los ratios derivados (`rooms_per_household`, `bedrooms_per_room`) mostraron correlaciones significativamente más altas con el ingreso, evidenciando que los outliers ocultaban relaciones reales.
- El mapa RGB (rojo=ingreso, verde=valor, azul=edad) revela zonas de alta concentración de riqueza en la costa norte de California.

## Notebook 2 — Consumo Eléctrico: Brasil

Análisis exploratorio del consumo eléctrico brasileño (18.568 registros mensuales por estado, tipo de consumidor y sector económico, cubriendo 270 meses de 2004-01 a 2026-06).

**Proceso (Brasil):** carga y exploración de metadatos → limpieza y preprocesamiento (incluye conteo de registros negativos y en cero) → distribuciones univariadas → segmentación temporal (mes/año, con mediana, media y banda P25-P75) → serie temporal y tendencia de largo plazo (media móvil de 12 meses) → análisis por estado y tipo de consumidor → correlación y regresión.

**Hallazgos principales (Brasil):**

- Existe una marcada estacionalidad en el consumo: los picos se concentran en los meses de diciembre a marzo (verano austral), mientras que los mínimos occur en junio-julio.
- Se detectaron registros con consumo cero o negativo (49 filas con `Consumo <= 0`, solo un 0.032 % del dataset): corresponden a consumidores del mercado libre que inyectan excedentes a la red (net metering), no a errores de datos.
- `Consumo` y `Consumidores` tienen distribuciones muy asimétricas a la derecha: la media supera ampliamente a la mediana por unos pocos registros de gran volumen.
- La distinción ruido/tendencia se logró con una media móvil de 12 meses sobre la serie mensual: suaviza la estacionalidad y revela la trayectoria de largo plazo.
- Los estados de São Paulo, Minas Gerais y Rio de Janeiro concentran el mayor consumo absoluto, pero per cápita la distribución cambia significativamente.
- Los consumidores del tipo "Livre" (mercado libre) presentan un patrón de consumo diferente al de los "Cativo" (regulados), con mayor variabilidad mensual.

## Herramientas

Python · Pandas · NumPy · Matplotlib · Seaborn · Jupyter Notebook

## Instalación

```bash
git clone https://github.com/Cesahz/C1DS-Price_Analysis.git
cd C1DS-Price_Analysis
pip install -r requirements.txt
```

### Configuración de Kaggle

Los notebooks descargan los datasets directamente desde Kaggle. Para que esto funcione, necesitas configurar tu API token de Kaggle:

1. Crea una cuenta en [kaggle.com](https://www.kaggle.com) (si no tienes una)
2. Ve a tu perfil → **Settings** → **API** → **Create New Token**
3. Se descargará un archivo `kaggle.json`. Cópialo a la carpeta `~/.kaggle/`:
   - **Windows:** `C:\Users\<tu-usuario>\.kaggle\kaggle.json`
   - **Mac/Linux:** `~/.kaggle/kaggle.json`
4. Asegúrate de que el archivo tenga permisos de lectura exclusiva:
   ```bash
   chmod 600 ~/.kaggle/kaggle.json
   ```

Una vez configurado, ejecuta los notebooks:

```bash
jupyter notebook
```
