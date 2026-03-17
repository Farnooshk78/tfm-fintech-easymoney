# easyMoney — Capstone (End-to-End Machine Learning)
## Contexto
Proyecto práctico del Máster en Data Science (Nuclio). Trabajamos en equipo “en la piel de Bob”, Data Scientist en easyMoney, una plataforma multicanal de productos financieros (ahorro, inversión, financiación). El reto: reorientar la estrategia para aumentar la rentabilidad de la base de clientes actual (más penetración de productos) y reducir la captación intensiva. 

## Stakeholders (personajes del caso)
- Carol (CEO / Directora General)
- Dan (Director de Marketing) — supervisor y validador final de informes
- Erin (Marketing Directo)
- Frank (Director de IT)
- Alice (RRHH)

## Entregables (alto nivel)
1) **Tarea 1 — Análisis:** nuevo BI self-service para el equipo + presentación para Comité.
2) **Tarea 2 — Segmentación:** 7–8 segmentos de clientes + perfilado para marketing (explorable desde dashboard).
3) **Tarea 3 — Recomendación:** propuesta para campaña de **10.000 e-mails**, recomendando producto por cliente, estimando tasa de respuesta y ROI.
4) **Tarea 4 — Personalización:** 4–5 creatividades/mensajes por perfiles (edad, sexo, ingresos…) para los 10.000 e-mails.
5) **Tarea 5 — Seguimiento:** KPIs de campaña + KPIs de estrategia + metodología de mejora continua.
6) **Tarea 6 — Coordinación:** validación de resultados con Dan + plan de proyecto (tareas y duración).

## Fechas y evaluación (Capstone)
- Entregables finales: **memoria + código/dashboards** (deadline indicado en el material del capstone).
- Defensa/presentación final ante comité (fecha indicada en el material del capstone).
- Seguimiento con tutor: **3 puntos de control** durante el proyecto.
- Evaluación: memoria/proceso + defensa (según criterios del capstone).

## Datos y limitaciones
- **Sin acceso directo a ERP/DB de producción** por seguridad.
- Se trabaja con un **volcado (dump) de la DB del autoservicio BI** (ReportServer).
- Hay **17 particiones de histórico** (por integración del nuevo ERP). 
- Existe un **diccionario de campos** en un `.txt` asociado al dump.

## Estructura del repositorio
- `data/`
  - `raw data/` (NO se sube a Git)
  - `processed/` (NO se sube a Git)
- `notebooks/` — EDA y prototipos
- `src/` — código reutilizable (preparación, features, modelos)
- `docs/` — documentación (KPIs, diccionario de datos, decisiones)
- `reports/` — resultados, figuras y resúmenes
- `outputs/` — ficheros finales para negocio/plataformas

## Forma de trabajo (resumen)
- Trabajo por ramas: `feature/...` → PR → merge.
- Notebooks: `01_...`, `02_...`, etc.

### Accesos rápidos (repo)
- Checklist Kickoff: [docs/KICKOFF_CHECKLIST.md](docs/KICKOFF_CHECKLIST.md)
- KPIs: [docs/KPI_DEFINITIONS.md](docs/KPI_DEFINITIONS.md)
- Diccionario de datos: [docs/DATA_DICTIONARY.md](docs/DATA_DICTIONARY.md)
- Decision log: [docs/DECISIONS_LOG.md](docs/DECISIONS_LOG.md)

## Links internos del proyecto
- Trello / Backlog: https://trello.com/b/5LGWYuNL/tfm-fintech
- Documentación interna: https://drive.google.com/drive/folders/1fsC8LnZhbG54vMmbYB5gug8ROGc1ihjS
