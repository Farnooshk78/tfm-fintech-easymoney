# easyMoney — Proyecto Final de Data Science
### End-to-End Machine Learning | Nuclio Digital School

---

## 📋 Descripción del proyecto

Proyecto final del máster en Data Science & AI de Nuclio Digital School. El caso práctico simula el trabajo de un Data Scientist recién incorporado a **easyMoney**, una plataforma fintech de comercialización de productos financieros (ahorro, inversión, financiación) con una base de ~441.752 clientes activos.

El objetivo principal es **aumentar la rentabilidad de la cartera actual de clientes** mediante analítica avanzada, siguiendo la estrategia de penetración de mercado definida por la dirección (cuadrante de la Matriz de Ansoff: mercados actuales × productos actuales).

**Resultado final:** 10.000 emails con 5 creatividades diferenciadas recomendando `credit_card`, con un **ROI estimado del 10.160%** (~171 contratos, €10.260 de revenue sobre €100 de coste).

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
- 22 CSVs exportados como fuente de datos del dashboard

---

### Tarea 1 — Análisis Exploratorio (`01-eda.ipynb` + `02-eda-deep-dive.ipynb`)
EDA completo sobre los datos de clientes para responder preguntas de negocio clave:
- Detección y tratamiento de anomalías (`age`, `salary`, `entry_date`, `deceased`)
- Análisis de penetración de productos (el 67% de clientes solo tiene `em_acount`)
- Identificación del punto de inflexión en julio 2018: entrada masiva de ~84.000 clientes universitarios (de 251.533 a 335.669 en un mes)
- Cross-sell rate actual: **14,3%** (objetivo: >25%)

**Hallazgos clave:**
| Pregunta | Resultado |
|----------|-----------|
| Segmento más rentable | TOP: ~40 €/cliente vs UNIVERSITARIO: ~9 €/cliente |
| Riesgo de abandono | 26,5% de clientes inactivos |
| Mayor correlación cross-sell | Nómina ↔ Pensión (r = 0,97) |

---

### Tarea 2 — Segmentación de Clientes (`04-segmentación.ipynb`)
Segmentación no supervisada de la base de ~441.752 clientes para orientar la estrategia comercial.

**Metodología:**
- Feature engineering en 3 pasos iterativos documentados
- **KMeans++** con selección de k óptimo mediante 4 métricas: Elbow, Silhouette, Davies-Bouldin, Calinski-Harabasz
- PCA para visualización 2D
- Profiling de clusters para interpretación de negocio

**Métricas de validación (k=7 seleccionado):**
| Métrica | Valor |
|---------|-------|
| Silhouette Score | 0,2402 (máximo) |
| Davies-Bouldin | 1,3999 (mínimo) |

**Resultado:** `K = 7` clusters identificados con perfiles diferenciados por edad, salario, género y cartera de productos.

**Output:** `customer_segments.csv` (441.752 clientes)

---

### Tarea 3 — Sistema de Recomendación (`05-recomendación.ipynb`)
Selección de los 10.000 clientes con mayor potencial de rentabilidad para una campaña de email marketing.

**Metodología:**
- Modelos de clasificación: Decision Tree (baseline), Random Forest, **XGBoost** (mejor AUC-ROC en los 6 productos)
- Split temporal estricto para evitar data leakage: train (hasta nov 2018) / validación (dic 2018–ene 2019) / test (feb–abr 2019) / scoring (may 2019)
- Valor esperado = P̂(contratación) × Margen(producto)
- SHAP values para interpretabilidad del modelo

**Márgenes por producto:**
| Tipo de producto | Margen |
|-----------------|--------|
| Tarjetas | 60 € |
| Préstamos / Hipoteca | 60 € |
| Ahorro e inversión | 40 € |
| Cuentas | 10 € |

**Métricas del top 10.000 seleccionados:**
| Métrica | Valor |
|---------|-------|
| Valor esperado medio (top 10.000) | ~€55,25 por cliente |
| Valor esperado acumulado | €552.455 |
| Producto recomendado (100%) | `credit_card` |
| Distribución natural sin restricción | payroll_account 44%, em_acount 27%, funds 12%, credit_card 5% |

**Output:** `recomendacion_10000_clientes.csv`

---

### Tarea 4 — Personalización (`06-personalización.ipynb`)
Segmentación de los 10.000 clientes seleccionados en 5 perfiles para asignar creatividades diferenciadas.

**Metodología:**
- KMeans (K=5) sobre variables sociodemográficas (age, salary, gender) — sin reutilizar features comportamentales de T2
- Selección de K: Silhouette K=5 (0,47) vs K=6 (0,472) — diferencia mínima (+0,01) frente a la ganancia en interpretabilidad operativa

**Perfiles y creatividades:**

| Creatividad | Perfil | N clientes | Open Rate est. | Conv Rate est. | Revenue est. |
|------------|--------|-----------|---------------|---------------|-------------|
| Experiencia y solidez | Varón, 35–44a, salary 40–60k | 1.412 | 19,8% | 1,3% | ~€1.080 |
| Éxito sin límites | Varón, 25–34a, salary 80–120k | 3.264 | 24,1% | 1,7% | ~€3.300 |
| Inteligente y práctico | Mujer, 35–44a, salary 80–120k | 2.452 | 23,5% | 1,6% | ~€2.340 |
| Activo y conectado | Mujer, 45–54a, salary 80–120k | 1.116 | 25,4% | 1,9% | ~€1.260 |
| Premium y exclusivo | Varón, 45–54a, salary 80–120k | 1.756 | 26,8% | 2,2% | ~€2.280 |

**Output:** `recomendacion_10000_personalizado.csv`, `muestra_1000_personalizado.csv`

---

### Tarea 5 — Seguimiento (`07-seguimiento.ipynb`)
Framework de KPIs en dos niveles para medir la campaña y la nueva estrategia.

**KPIs de campaña — Funnel completo (escenario base):**

| Etapa | Métrica | Valor Estimado | Benchmark Sector |
|-------|---------|---------------|-----------------|
| Enviados | N emails | 10.000 | — |
| Entregados | Tasa entrega | ~98% | >95% ✅ |
| Abiertos | Open Rate | 23,9% (+1,9pp) | 22% ✅ |
| Clicks | CTR | 3,1% (+0,3pp) | 2,8% ✅ |
| Contratos | Conv Rate | 1,7% (+0,2pp) | 1,5% ✅ |
| Revenue | €60 × contratos | ~€10.260 | — |
| Coste | €0,01 × enviados | ~€100 | — |
| ROI | (Revenue−Coste)/Coste | **10.160%** | — |

**Análisis de sensibilidad del ROI:**

| Escenario | Conv. Rate | Contratos | Revenue | ROI |
|-----------|-----------|-----------|---------|-----|
| Conservador | 1,00% | ~100 | ~€6.000 | 5.900% |
| Esperado (base) | 1,71% | ~171 | ~€10.260 | **10.160%** |
| Optimista | 3,00% | ~300 | ~€18.000 | 17.900% |

**KPIs estratégicos (datos reales, mayo 2019):**

| KPI | Valor actual | Objetivo | Estado |
|-----|-------------|---------|--------|
| PPP (Productos por cliente) | 0,99 | ≥ 1,5 | ⚠️ Por debajo |
| Cross-sell Rate | 14,3% | > 25% | ⚠️ Por debajo |
| Tasa de Activación en app | 38,7% | > 50% | ⚠️ Por debajo |
| Revenue estimado / cliente | €11,1 | > €20 | ⚠️ Por debajo |
| Retención mensual | N/D | > 98% | ✓ OK |

---

### Tarea 6 — Coordinación (`08-coordinación.ipynb`)
Plan de proyecto y circuit de validación para Dan (Director de Marketing).
- Protocolo de revisión y aprobación de entregables (48h de plazo, tag [REVISAR]/[OK])
- Gantt chart interactivo con timeline del proyecto (junio – julio 2019)
- Resumen ejecutivo de resultados para el Comité de Dirección

**Impacto de negocio — Matriz Ansoff:**

| Estrategia Ansoff | Segmento objetivo | Acción | Revenue adicional potencial |
|------------------|-------------------|--------|---------------------------|
| Penetración (actuales × actuales) | Básicos (255k) | Activar tarjeta débito (20% conv) | ~€510.000/año |
| Desarrollo de producto | Digitales (29k) | Bundle nómina + pensión (15% conv) | ~€264.000/año |
| Desarrollo de mercado | Inactivos (115k) | Reactivación selectiva (10% conv) | ~€280.800/año |
| Diversificación | Premium + Inversores (3k) | Ampliar cartera inversiones | ~€240.000/año |

**Arquitectura MLOps para producción:**

```
CSV fuente mensual → Airflow (orquestación) → Great Expectations (validación)
→ master_df_flags.parquet (S3/Azure) → Feature engineering
→ XGBoost endpoint (FastAPI/ONNX) → Selección top 10.000
→ Plataforma email → KPIs → Loop de mejora continua
```

| Componente | Herramienta | Frecuencia | Acción / Alerta |
|-----------|-------------|-----------|----------------|
| Ingesta de datos | Apache Airflow | Mensual | Alerta si volumen difiere >5% vs mes anterior |
| Almacenamiento | S3 / Azure Data Lake | Continuo | Versionado de master_df_flags.parquet |
| Validación de esquemas | Great Expectations | Pre-proceso | Alerta si % anomalías >1% |
| Serving del modelo | FastAPI + XGBoost (ONNX) | On-demand | Endpoint REST, latencia <100ms |
| Monitorización AUC | MLflow / custom | Mensual | Trigger re-entrenamiento si AUC cae >3pp |
| A/B Testing | 5% clientes aleatorios | Por campaña | Cuantificar lift personalización vs genérico |
| Re-entrenamiento | Pipeline automatizado | Mensual | Con nuevos datos de contratación del mes anterior |

---

## 🛠️ Stack tecnológico

| Categoría | Librerías / Herramientas |
|-----------|--------------------------|
| **Manipulación de datos** | `pandas`, `numpy` |
| **Machine Learning** | `scikit-learn`, `xgboost` |
| **Visualización** | `plotly`, `matplotlib` |
| **Interpretabilidad** | `shap` |
| **MLOps** | Apache Airflow, Great Expectations, FastAPI, MLflow |
| **Serving** | XGBoost (ONNX), FastAPI REST endpoint |
| **Almacenamiento** | S3 / Azure Data Lake |
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
| `customer_segments.csv` | Segmentación de ~441.752 clientes (k=7) | `04-segmentación.ipynb` |
| `recomendacion_10000_clientes.csv` | 10.000 clientes + producto recomendado (formato ESP) | `05-recomendación.ipynb` |
| `recomendacion_10000_personalizado.csv` | 10.000 clientes + creatividad asignada | `06-personalización.ipynb` |
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

**Autoras:** Farnoosh Koohi · Gemma Calderon  
**Institución:** Nuclio Digital School — Máster en Data Science & AI

---

## 📄 Licencia

Este proyecto es de uso académico. Los datos son ficticios y han sido generados para el caso práctico easyMoney de Nuclio Digital School.
