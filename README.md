# 🌱 easyMoney — Proyecto Final de Data Science
### End-to-End Machine Learning | Nuclio Digital School

---

## 📋 Descripción del proyecto

Proyecto final del máster en Data Science & AI de Nuclio Digital School. El caso práctico simula el trabajo de un Data Scientist recién incorporado a **easyMoney**, una plataforma fintech de comercialización de productos financieros (ahorro, inversión, financiación) con una base de ~441.000 clientes activos.

El objetivo principal es **aumentar la rentabilidad de la cartera actual de clientes** mediante analítica avanzada, siguiendo la estrategia de penetración de mercado definida por la dirección (cuadrante de la Matriz de Ansoff: mercados actuales × productos actuales).

---

## 🗂️ Estructura del repositorio

```
easymoney-ds/
│
├── data/
│   ├── raw/                        # Datos originales (17 particiones mensuales)
│   └── processed/
│       └── master_df_flags.parquet # Dataset maestro procesado
│
├── notebooks/
│   ├── 01-eda.ipynb                # Tarea 1a — EDA general
│   ├── 02-eda-deep-dive.ipynb      # Tarea 1b — EDA profundo
│   ├── 03-power-bi.ipynb           # Tarea 0  — BI Dashboard
│   ├── 04-segmentación.ipynb       # Tarea 2  — Segmentación de clientes
│   ├── 05-recomendación.ipynb      # Tarea 3  — Sistema de recomendación
│   ├── 06-personalización.ipynb    # Tarea 4  — Personalización de campaña
│   ├── 07-seguimiento.ipynb        # Tarea 5  — Framework de KPIs
│   └── 08-coordinación.ipynb       # Tarea 6  — Plan de proyecto
│
├── outputs/
│   ├── muestra_1000_clientes.csv            # Muestra para pruebas ESP
│   ├── muestra_1000_personalizado.csv       # Muestra con creatividades
│   ├── recomendacion_10000_clientes.csv     # Selección campaña (formato Frank)
│   └── recomendacion_10000_personalizado.csv # Campaña con creatividades asignadas
│
└── README.md
```

---

## 📊 Dataset

| Campo | Tabla | Descripción |
|-------|-------|-------------|
| `pk_cid` | pk | Identificador único de cliente |
| `pk_partition` | pk | Fecha de ingesta (snapshot mensual) |
| `age` | sociodemographic | Edad del cliente |
| `gender` | sociodemographic | Sexo del cliente |
| `salary` | sociodemographic | Ingresos brutos de la unidad familiar |
| `country_id` | sociodemographic | País de residencia |
| `region_code` | sociodemographic | Provincia (para ES) |
| `segment` | commercial_activity | Segmento comercial |
| `active_customer` | commercial_activity | Indicador de actividad en la app |
| `entry_channel` | commercial_activity | Canal de captación |
| `entry_date` | commercial_activity | Fecha de primera contratación |
| `em_acount` | products | Cuenta easyMoney (hucha) |
| `credit_card` | products | Tarjeta de crédito |
| `debit_card` | products | Tarjeta de débito |
| `payroll_account` | products | Cuenta bonificada por domiciliaciones |
| `funds` | products | Fondos de inversión |
| `pension_plan` | products | Plan de pensiones |
| `long_term_deposit` | products | Depósito a largo plazo |
| `short_term_deposit` | products | Depósito a corto plazo |
| `securities` | products | Valores |
| `loans` | products | Préstamos |
| `mortgage` | products | Hipoteca |

**Volumen:** ~5,94M registros | 17 particiones mensuales (enero 2018 – mayo 2019) | ~441.752 clientes únicos

---

## 🚀 Tareas desarrolladas

### Tarea 0 — BI Dashboard (`03-power-bi.ipynb`)
Construcción de un autoservicio de Business Intelligence interactivo para el Comité de Dirección de easyMoney.
- Visualización de evolución de clientes y productos por partición mensual
- Análisis de cross-sell por segmento comercial
- Matriz de correlación entre productos

---

### Tarea 1 — Análisis Exploratorio (`01-eda.ipynb` + `02-eda-deep-dive.ipynb`)
EDA completo sobre los datos de clientes para responder preguntas de negocio clave:
- Detección y tratamiento de anomalías (`age`, `salary`, `entry_date`, `deceased`)
- Análisis de penetración de productos (el 67% de clientes solo tiene `em_acount`)
- Identificación del punto de inflexión en julio 2018 (entrada masiva de nuevos clientes)
- Cross-sell rate actual: **14,3%** (objetivo: >25%)

**Hallazgos clave:**
| Pregunta | Resultado |
|----------|-----------|
| Segmento más rentable | TOP: ~40 €/cliente vs UNIVERSITARIO: ~9 €/cliente |
| Riesgo de abandono | 26,5% de clientes inactivos |
| Mayor correlación cross-sell | Nómina ↔ Pensión (r = 0,97) |

---

### Tarea 2 — Segmentación de Clientes (`04-segmentación.ipynb`)
Segmentación no supervisada de la base de ~441.000 clientes para orientar la estrategia comercial.

**Metodología:**
- Feature engineering en 3 pasos iterativos documentados
- **KMeans++** con selección de k óptimo mediante 4 métricas: Elbow, Silhouette, Davies-Bouldin, Calinski-Harabasz
- PCA para visualización 2D
- Profiling de clusters para interpretación de negocio

**Resultado:** `K = 7` clusters identificados con perfiles diferenciados por edad, salario, género y cartera de productos.

**Output:** `customer_segments.csv`, `cluster_profiles.csv`

---

### Tarea 3 — Sistema de Recomendación (`05-recomendación.ipynb`)
Selección de los 10.000 clientes con mayor potencial de rentabilidad para una campaña de email marketing.

**Metodología:**
- Modelos de clasificación: Decision Tree, Random Forest, **XGBoost** (mejor AUC-ROC)
- Evaluación con SHAP values para interpretabilidad
- División temporal: train / validación / test / scoring
- Valor esperado = P(contratación) × margen económico del producto

**Márgenes por producto:**
| Tipo de producto | Margen |
|-----------------|--------|
| Tarjetas | 60 € |
| Préstamos / Hipoteca | 60 € |
| Ahorro e inversión | 40 € |
| Cuentas | 10 € |

**Resultado:** 9.836 clientes seleccionados con recomendación de `credit_card` (producto de mayor valor esperado).

**ROI estimado de la campaña: 10.331%** (10.260 € revenue / 98 € coste)

**Output:** `recomendacion_10000_clientes.csv`

---

### Tarea 4 — Personalización (`06-personalización.ipynb`)
Segmentación de los 9.836 clientes seleccionados en 5 perfiles para asignar creatividades diferenciadas.

**Metodología:**
- KMeans (K=5) sobre variables sociodemográficas (edad, salario, género)
- Selección de K justificada: Silhouette K=5 (0,471) vs K=6 (0,477) — diferencia mínima frente a la ganancia en interpretabilidad

**Perfiles y creatividades:**

| Creatividad | Perfil | N clientes |
|------------|--------|-----------|
| Experiencia y solidez | Maduros con estabilidad financiera | 1.363 |
| Éxito sin límites | Jóvenes profesionales en ascenso | 3.174 |
| Inteligente y práctico | Perfil analítico, control del gasto | 1.119 |
| Activo y conectado | Jóvenes digitales y móviles | 2.385 |
| Premium y exclusivo | Alto poder adquisitivo | 1.795 |

**Output:** `recomendacion_10000_personalizado.csv`, `muestra_1000_personalizado.csv`

---

### Tarea 5 — Seguimiento (`07-seguimiento.ipynb`)
Framework de KPIs en dos niveles para medir la campaña y la nueva estrategia.

**KPIs de campaña (resultados ficticios ilustrativos):**

| KPI | Campaña | Benchmark sector | Δ |
|-----|---------|-----------------|---|
| Open Rate | 24,2% | 22,0% | ✅ +2,2% |
| CTR | 3,2% | 2,8% | ✅ +0,4% |
| Conv Rate | 1,7% | 1,5% | ✅ +0,2% |
| ROI | 10.331% | >500% | ✅ |

**KPIs estratégicos (datos reales, mayo 2019):**

| KPI | Valor actual | Objetivo |
|-----|-------------|---------|
| PPP (Productos por cliente) | 0,99 | > 1,5 |
| Cross-sell Rate | 14,3% | > 25% |
| Tasa de Activación en app | 38,7% | > 50% |
| Revenue estimado / cliente | 13,7 € | > 20 € |

---

### Tarea 6 — Coordinación (`08-coordinación.ipynb`)
Plan de proyecto y circuit de validación para Dan (Director de Marketing).
- Protocolo de revisión y aprobación de entregables
- Gantt chart con timeline del proyecto (junio – julio 2019)
- Resumen ejecutivo de resultados para el Comité de Dirección

---

## 🛠️ Stack tecnológico

| Categoría | Librerías |
|-----------|-----------|
| **Manipulación de datos** | `pandas`, `numpy` |
| **Machine Learning** | `scikit-learn`, `xgboost` |
| **Visualización** | `plotly`, `matplotlib` |
| **Interpretabilidad** | `shap` |
| **Entorno** | Python 3.13, Jupyter Notebook |

---

## ⚙️ Instalación y uso

```bash
# Clonar el repositorio
git clone https://github.com/tu-usuario/easymoney-ds.git
cd easymoney-ds

# Instalar dependencias
pip install -r requirements.txt

# Ejecutar notebooks en orden
jupyter notebook notebooks/01-eda.ipynb
```

**Orden de ejecución recomendado:**
```
01-eda → 02-eda-deep-dive → 03-power-bi → 04-segmentación
→ 05-recomendación → 06-personalización → 07-seguimiento → 08-coordinación
```

> ⚠️ Los notebooks `05` en adelante requieren `master_df_flags.parquet` generado en los pasos anteriores.

---

## 📁 Outputs principales

| Fichero | Descripción | Generado en |
|---------|-------------|------------|
| `master_df_flags.parquet` | Dataset maestro con anomalías marcadas | `02-eda-deep-dive.ipynb` |
| `customer_segments.csv` | Segmentación de ~441k clientes | `04-segmentación.ipynb` |
| `recomendacion_10000_clientes.csv` | 9.836 clientes + producto recomendado (formato ESP) | `05-recomendación.ipynb` |
| `recomendacion_10000_personalizado.csv` | 9.836 clientes + creatividad asignada | `06-personalización.ipynb` |
| `muestra_1000_clientes.csv` | Muestra de 1.000 clientes para prueba ESP | `05-recomendación.ipynb` |
| `muestra_1000_personalizado.csv` | Muestra con creatividades para prueba ESP | `06-personalización.ipynb` |

---

## 👥 Contexto del proyecto

**Empresa:** easyMoney — plataforma fintech fundada por Carol Denver  
**Rol simulado:** Bob — Data Scientist del equipo de Marketing  
**Stakeholders:**
- **Carol** (CEO) — estrategia data-driven, penetración de cartera
- **Dan** (Director de Marketing) — validación de entregables
- **Erin** (Responsable Marketing Directo) — campaña de email
- **Frank** (Director de IT) — infraestructura y ESP

**Institución:** Nuclio Digital School — Máster en Data Science & AI  

---

## 📄 Licencia

Este proyecto es de uso académico. Los datos son ficticios y han sido generados para el caso práctico easyMoney de Nuclio Digital School.