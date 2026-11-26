
# CHANGELOG — Arquitectura MLOps

Este documento registra los cambios realizados entre la propuesta inicial de la Unidad 1 y la arquitectura reestructurada de la Unidad 3, orientada a la predicción de enfermedades comunes y huérfanas.

La versión 2.0 conserva las seis etapas principales del diseño original, pero amplía la definición de tecnologías, flujos de procesamiento, controles operativos y mecanismos de automatización, con el propósito de facilitar su evaluación e implementación por un equipo de Machine Learning.

## Versión 2.0 — Reestructuración de arquitectura MLOps

### 1. Data Ingestion

**Versión original:** se contemplaba la integración de historias clínicas, diagnósticos confirmados, información demográfica y registros de enfermedades huérfanas mediante APIs y bases de datos, sin definir una frecuencia de ingestión ni un mecanismo específico de orquestación.

**Cambios realizados:**
- Se definió una estrategia de ingestión batch incremental diaria.
- Se incorporó Apache Airflow para programar, supervisar y gestionar las cargas.
- Se especificó el uso de APIs institucionales, conectores Python y estándares como HL7 FHIR cuando estén disponibles.
- Se añadieron mecanismos de trazabilidad, reintentos y control de duplicados.

**Justificación:** establecer un proceso reproducible y supervisado, evitando incorporar infraestructura de streaming sin una necesidad identificada.

### 2. Data Processing & Feature Engineering

**Versión original:** incluía una arquitectura Medallion con capas Bronze, Silver y Gold, procesamiento con Pandas y actividades de limpieza, transformación y Feature Engineering.

**Cambios realizados:**
- Se seleccionó MinIO como almacenamiento de objetos para el Data Lake institucional.
- Se definió Parquet como formato principal para datasets procesados.
- Se incorporó Pandera para validar esquemas y restricciones de datos.
- Se precisaron las responsabilidades de las capas Bronze, Silver y Gold.
- Se trasladó el tratamiento del desbalance de clases a la etapa de entrenamiento.
- Se separaron las transformaciones de datos generales de aquellas que deben ajustarse exclusivamente sobre el conjunto de entrenamiento.

**Justificación:** mejorar la calidad, reproducibilidad y trazabilidad de los datos, evitando fugas de información durante la evaluación de modelos.

### 3. Model Engineering & Registry

**Versión original:** proponía modelos generales basados en Random Forest y XGBoost, estrategias para enfermedades huérfanas, evaluación predictiva y registro de modelos mediante MLflow.

**Cambios realizados:**
- Se definió un proceso de experimentación y selección de modelos candidatos, sin establecer anticipadamente un algoritmo definitivo.
- Se incorporó regresión logística como modelo de referencia.
- Se especificaron estrategias de manejo del desbalance y aprendizaje con pocos datos.
- Se amplió la evaluación con calibración y métricas por enfermedad.
- Se reforzó la separación de entrenamiento, validación y prueba por paciente.
- Se precisó el versionamiento conjunto de modelos, preprocesamiento, parámetros y métricas en MLflow.
- Se estableció la necesidad de validación técnica y clínica antes de promover una nueva versión.

**Justificación:** permitir la comparación reproducible de alternativas y mejorar la confiabilidad de la solución, especialmente ante la escasez de casos de enfermedades huérfanas.

### 4. Model Deployment (CI/CD)

**Versión original:** contemplaba recuperar modelos versionados de MLflow, actualizar el servicio de predicción y reiniciar contenedores Docker.

**Cambios realizados:**
- Se incorporó GitHub Actions para ejecutar pruebas automatizadas.
- Se definió Docker como mecanismo de empaquetado del servicio y sus artefactos.
- Se incorporó GHCR como registro de imágenes Docker versionadas.
- Se separaron los procesos de construcción, publicación y despliegue.
- Se estableció un mecanismo de despliegue autorizado en la infraestructura institucional.
- Se añadió la posibilidad de rollback a versiones previamente aprobadas.

**Justificación:** automatizar las verificaciones técnicas y garantizar que las actualizaciones sean reproducibles, trazables y controladas.

### 5. Serving & Prediction

**Versión original:** proponía una aplicación web dentro de una red clínica privada, con Nginx, dos contenedores FastAPI administrados mediante Docker Compose y una base de datos para registrar predicciones.

**Cambios realizados:**
- Se precisaron los mecanismos de HTTPS/TLS, autenticación y control de acceso.
- Se estableció el despliegue on-premises como modalidad principal.
- Se definieron dos réplicas del servicio FastAPI con balanceo de carga mediante Nginx.
- Se reemplazó la referencia a un único archivo modelo.pkl por artefactos de inferencia versionados que incluyen modelos, preprocesamiento y configuración.
- Se seleccionó PostgreSQL para la auditoría de predicciones.
- Se incorporó una modalidad alternativa de ejecución local mediante Docker para modelos livianos.
- Se reconoció que dos réplicas en un mismo servidor no garantizan alta disponibilidad ante fallas físicas.

**Justificación:** mejorar la seguridad, trazabilidad y consistencia de la inferencia, atendiendo las modalidades de ejecución requeridas.

### 6. Model Monitoring & Retraining

**Versión original:** contemplaba el monitoreo de calidad de datos, drift, desempeño predictivo y operación, utilizando Prometheus, Grafana, Evidently y Airflow para el reentrenamiento.

**Cambios realizados:**
- Se diferenciaron el monitoreo operativo continuo y la evaluación predictiva periódica.
- Se establecieron frecuencias propuestas de seguimiento y criterios de alerta.
- Se precisó que la evaluación predictiva requiere diagnósticos confirmados.
- Se incorporaron nuevos datos etiquetados, degradación del desempeño y nueva evidencia médica como posibles disparadores de revisión.
- Se definió un pipeline de reentrenamiento controlado mediante Airflow.
- Se estableció que las alertas no generan despliegues automáticos.
- Se incorporaron mecanismos de aprobación y recuperación de versiones.

**Justificación:** mantener la confiabilidad de los modelos en producción, evitando actualizaciones automáticas sin evaluación técnica y clínica.

## Cambios transversales de arquitectura

Además de los cambios por componente, la reestructuración incorpora:

- **Dimensionamiento:** definición de un escenario hipotético con 100.000 pacientes, 500.000 registros clínicos, 100 médicos y hasta 2.000 predicciones diarias.
- **Requisitos:** incorporación de requisitos funcionales y no funcionales con criterios de aceptación.
- **Procesamiento:** separación explícita entre flujos batch, inferencia online y procesos activados por eventos.
- **Infraestructura:** definición de servidores institucionales on-premises como arquitectura principal.
- **Seguridad:** mayor precisión en los controles de acceso, protección de datos clínicos y auditoría.
- **Escalabilidad:** identificación de alternativas tecnológicas ante el crecimiento de los datos y solicitudes.
- **Gobernanza:** incorporación de aprobaciones técnicas y clínicas para promover modelos a producción.

## Referencias de las versiones

- [Arquitectura original — Unidad 1](docs/Arquitectura%20MLOPS.pdf)
- [Arquitectura reestructurada — Versión 2.0](docs/arquitectura%20ajustada_v2.drawio.png)

La propuesta V2 conserva la estructura conceptual de la versión inicial, pero incorpora decisiones técnicas y operativas que permiten evaluar su viabilidad y orientar una implementación empresarial.
