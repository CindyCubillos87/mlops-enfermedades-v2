# Arquitectura MLOps para la Predicción de Enfermedades Comunes y Huérfanas

**Versión:** 2.0 — Propuesta de reestructuración de arquitectura

## 1. Contexto y objetivo

### 1.1. Descripción del problema

Las instituciones de salud disponen de grandes volúmenes de información clínica que pueden aprovecharse mediante técnicas de Machine Learning para apoyar la identificación temprana de enfermedades.

Sin embargo, existe una diferencia importante entre las enfermedades comunes, para las cuales suele haber mayor disponibilidad de registros, y las enfermedades huérfanas, caracterizadas por su baja prevalencia y la escasez de datos clínicos etiquetados.

Esta situación plantea desafíos relacionados con la integración de fuentes heterogéneas, la calidad de los datos, el desbalance de clases, el entrenamiento de modelos, la confiabilidad de las predicciones y la operación de sistemas de ML en entornos clínicos.

### 1.2. Objetivo de la solución

Diseñar una arquitectura MLOps end-to-end que permita desarrollar, desplegar, monitorear y mantener modelos predictivos orientados a estimar la posibilidad de que un paciente presente enfermedades comunes o huérfanas, utilizando síntomas, antecedentes y características clínicas.

La arquitectura deberá garantizar la reproducibilidad de los procesos de datos y modelos, el versionamiento de los artefactos, el despliegue controlado y el monitoreo continuo de la solución.

El sistema será una herramienta de apoyo a la decisión médica y no sustituirá el diagnóstico realizado por profesionales de la salud.

### 1.3. Alcance de la arquitectura

La propuesta contempla el ciclo de vida completo de Machine Learning:

1. Ingestión e integración de datos clínicos.
2. Almacenamiento, validación, procesamiento y transformación de datos.
3. Entrenamiento, evaluación y selección de modelos predictivos.
4. Registro y versionamiento de modelos y artefactos.
5. Automatización de pruebas, integración y despliegue.
6. Exposición de modelos mediante servicios de inferencia.
7. Monitoreo, auditoría y reentrenamiento controlado.

#### 1.3.1. Modalidades de ejecución de la solución

La arquitectura contempla dos modalidades de ejecución para facilitar su adopción en diferentes entornos clínicos:

* Despliegue centralizado (modalidad principal): los modelos estarán alojados en un servidor institucional o servicio de nube privada, y los profesionales médicos podrán realizar solicitudes de predicción mediante una aplicación web que se comunica con una API. Esta alternativa permite centralizar la administración, seguridad, monitoreo y actualización de los modelos.
  
* Despliegue local (modalidad alternativa): cuando los modelos tengan bajos requerimientos computacionales, la solución podrá ejecutarse directamente en el computador del médico mediante un contenedor Docker. En este escenario, la aplicación y los artefactos del modelo se distribuirán de manera controlada, considerando los requisitos de hardware, seguridad y actualización de versiones.

Se selecciona el despliegue centralizado como arquitectura principal debido a sus ventajas de administración, trazabilidad y control de acceso en un entorno clínico. La modalidad local se contempla para situaciones con conectividad limitada o restricciones de infraestructura, siempre que se cumplan los requisitos técnicos y de protección de datos.

## 2. Supuestos de arquitectura


## 2. Supuestos de arquitectura

Debido a que el problema no proporciona información específica sobre infraestructura, volumen de datos o necesidades operativas, se establecen los siguientes supuestos para dimensionar la solución.

Para el diseño de la arquitectura se plantea un escenario hipotético de operación en una institución de salud de escala media, con 100.000 pacientes históricos, aproximadamente 500.000 registros clínicos y 100 profesionales médicos que realizan hasta 2.000 solicitudes de predicción diarias.

Estos valores no corresponden a estadísticas reales, sino a supuestos de ingeniería utilizados para estimar las necesidades de almacenamiento, procesamiento, concurrencia y disponibilidad de la solución.

El dimensionamiento inicial permitirá seleccionar tecnologías acordes con la carga esperada, evitando incorporar componentes de procesamiento distribuido o infraestructura compleja sin una necesidad demostrada.

Las decisiones tecnológicas serán evaluadas mediante pruebas de rendimiento, consumo de recursos y capacidad. Si el volumen de datos, la concurrencia o los requisitos de disponibilidad aumentan, se revisará la necesidad de escalar los componentes de forma vertical u horizontal.

Para las enfermedades huérfanas se considera una disponibilidad significativamente menor de casos confirmados, lo que condicionará la selección, entrenamiento y validación de los modelos especializados.

### 2.1. Escenario de dimensionamiento

| Dimensión | Supuesto de diseño |
|---|---|
| Escenario organizacional | Una institución de salud de escala media en fase piloto |
| Fuentes de información | Historias clínicas electrónicas, síntomas, antecedentes y diagnósticos confirmados |
| Tipo de información | Datos clínicos principalmente estructurados |
| Pacientes históricos | Aproximadamente 100.000 pacientes |
| Volumen histórico inicial | Aproximadamente 500.000 registros clínicos, considerando un promedio de 5 registros por paciente |
| Enfermedades comunes | Mayor disponibilidad de registros clínicos etiquetados |
| Enfermedades huérfanas | Entre 10 y 100 casos confirmados por enfermedad incluida en el piloto, como escenario hipotético de escasez |
| Usuarios del sistema | 100 profesionales médicos |
| Volumen de predicciones | Hasta 2.000 solicitudes diarias, considerando 20 solicitudes por médico |
| Concurrencia | Hasta 20 solicitudes simultáneas durante períodos de mayor demanda |
| Actualización de datos | Ingestión batch diaria |
| Uso del modelo | Inferencia online bajo demanda del médico |
| Tiempo de respuesta | Objetivo de latencia p95 inferior a 2 segundos, sujeto a pruebas de rendimiento |
| Infraestructura | Servidor institucional con acceso restringido |
| Actualización de modelos | Evaluación mensual y reentrenamiento condicionado a nuevos datos, cambios relevantes o degradación |
| Seguridad | Protección de datos sensibles, autenticación, autorización y auditoría |

Los valores anteriores constituyen hipótesis de diseño que deberán validarse durante el levantamiento de requisitos con la institución de salud. Su propósito es establecer una referencia para justificar las decisiones arquitectónicas y definir criterios iniciales de capacidad, rendimiento y escalabilidad.


## 3. Principios arquitectónicos

La solución se diseñará bajo los siguientes principios:

- **Modularidad:** separar los componentes de datos, entrenamiento, inferencia y monitoreo para facilitar su mantenimiento.
- **Reproducibilidad:** permitir reconstruir los experimentos mediante el versionamiento de código, datos, parámetros y modelos.
- **Escalabilidad:** permitir que la infraestructura evolucione conforme aumenten los datos y las solicitudes.
- **Seguridad y privacidad:** aplicar controles de protección sobre la información clínica durante todo su ciclo de vida.
- **Automatización:** reducir intervenciones manuales mediante pipelines de datos, pruebas y despliegue.
- **Trazabilidad:** mantener evidencia de las versiones utilizadas para generar cada predicción.
- **Confiabilidad clínica:** priorizar la evaluación rigurosa de los modelos, especialmente para enfermedades huérfanas, y exigir validación clínica antes de su uso asistencial.


## 4. Requisitos funcionales y no funcionales

La arquitectura propuesta debe satisfacer un conjunto de requisitos funcionales y no funcionales que permitan garantizar la operación, seguridad, trazabilidad y mantenimiento de la solución MLOps.

Estos requisitos se establecen a partir del escenario de dimensionamiento definido en la sección 2 y servirán como referencia para seleccionar las tecnologías, diseñar los componentes y establecer los criterios de validación de la arquitectura.

### 4.1. Requisitos funcionales

Los requisitos funcionales describen las capacidades que debe proporcionar la solución durante las etapas de gestión de datos, desarrollo de modelos, despliegue y operación.

| ID | Requisito funcional | Criterio de aceptación | Componente propuesto |
|---|---|---|---|
| RF-01 | Integrar datos clínicos provenientes de historias médicas, registros de síntomas y diagnósticos confirmados. | Ejecutar cargas batch diarias, registrando el estado de cada ejecución y los errores encontrados. | Apache Airflow + Python |
| RF-02 | Almacenar y organizar los datos clínicos conservando su trazabilidad. | Mantener datasets diferenciados en las capas Bronze, Silver y Gold, identificando fuentes y fechas de procesamiento. | MinIO/S3 + Parquet |
| RF-03 | Validar y transformar los datos antes de utilizarlos en los modelos. | Detectar registros duplicados, valores faltantes, inconsistencias y errores de esquema; documentar los resultados de validación. | Python + Pandera + Pandas |
| RF-04 | Entrenar modelos predictivos para enfermedades comunes y evaluar estrategias especializadas para enfermedades huérfanas. | Ejecutar experimentos reproducibles y comparar modelos mediante métricas definidas para cada grupo de enfermedades. | Scikit-learn + XGBoost + Optuna |
| RF-05 | Registrar los experimentos, modelos y artefactos desarrollados. | Cada versión debe conservar parámetros, métricas, referencia del dataset, transformaciones y artefactos asociados. | MLflow |
| RF-06 | Permitir que el médico realice consultas predictivas desde una aplicación web. | Recibir datos clínicos validados y devolver una respuesta estructurada, con resultados y advertencias correspondientes. | FastAPI + Pydantic + aplicación web |
| RF-07 | Integrar los resultados de los modelos generales y especializados. | Aplicar una estrategia de combinación validada y entregar una respuesta consistente, incluyendo casos de incertidumbre o evidencia insuficiente. | Servicio de inferencia en Python |
| RF-08 | Registrar las predicciones para auditoría y evaluación posterior. | Conservar identificador de auditoría, fecha, versión del modelo y resultado, evitando almacenar información sensible innecesaria. | PostgreSQL |
| RF-09 | Automatizar las pruebas y el proceso de publicación de nuevas versiones del servicio. | Ejecutar pruebas ante cambios de código y publicar imágenes Docker aprobadas y versionadas. | GitHub Actions + Docker + GHCR |
| RF-10 | Monitorear el comportamiento operativo y predictivo de los modelos. | Generar reportes y alertas sobre errores, latencia, calidad de datos, drift y desempeño cuando existan diagnósticos confirmados. | Prometheus + Grafana + Evidently |
| RF-11 | Permitir la actualización controlada de los modelos. | Ejecutar un pipeline de reentrenamiento que valide nuevas versiones antes de su promoción y despliegue. | Airflow + MLflow |
| RF-12 | Permitir la ejecución centralizada y, cuando sea viable, local. | Documentar el despliegue en servidor y disponer de un procedimiento de instalación local para modelos compatibles con los recursos disponibles. | Docker + FastAPI |

### 4.2. Requisitos no funcionales

Los requisitos no funcionales establecen las condiciones de calidad y operación que debe cumplir la arquitectura. Los valores cuantitativos corresponden al escenario hipotético definido previamente y deberán validarse mediante pruebas técnicas y revisión institucional.

| ID | Requisito no funcional | Criterio de aceptación propuesto | Componente o mecanismo |
|---|---|---|---|
| RNF-01 | Rendimiento | Mantener una latencia p95 inferior a 2 segundos para solicitudes de inferencia válidas, bajo la carga de prueba definida. | FastAPI + servicio de inferencia |
| RNF-02 | Capacidad | Soportar hasta 2.000 solicitudes diarias y una prueba de carga con 20 solicitudes concurrentes, sin incumplir el objetivo de latencia. | Servidor de aplicación + pruebas de carga |
| RNF-03 | Disponibilidad | Alcanzar una disponibilidad mensual objetivo del 99,5 % para el servicio centralizado. | Monitoreo, health checks y recuperación del servicio |
| RNF-04 | Seguridad | Exigir autenticación, autorización por roles, cifrado de comunicaciones y protección de credenciales. | HTTPS/TLS + control de acceso + gestión de secretos |
| RNF-05 | Privacidad | Minimizar y seudonimizar los datos destinados al entrenamiento, restringiendo el acceso a información identificable. | Pipelines de datos + políticas de acceso |
| RNF-06 | Trazabilidad | Permitir identificar el código, dataset, preprocesamiento y versión del modelo asociados a cada despliegue y predicción. | GitHub + MLflow + registros de auditoría |
| RNF-07 | Reproducibilidad | Permitir reconstruir experimentos utilizando versiones registradas de datos, código, dependencias y parámetros. | Git + MLflow + versionamiento de datasets |
| RNF-08 | Escalabilidad | Permitir ampliar recursos de procesamiento y capacidad de inferencia sin rediseñar completamente el sistema. | Componentes desacoplados + contenedores Docker |
| RNF-09 | Mantenibilidad | Mantener separados los componentes de ingestión, entrenamiento, inferencia y monitoreo, con pruebas y documentación técnica. | Arquitectura modular + GitHub Actions |
| RNF-10 | Confiabilidad predictiva | Evaluar sensibilidad, especificidad, precisión, PR-AUC y calibración según el tipo de enfermedad; establecer criterios clínicos antes de aprobar modelos. | Pipeline de evaluación + revisión clínica |
| RNF-11 | Recuperación | Permitir restaurar una versión previamente aprobada del servicio y sus artefactos ante fallas de despliegue. | Registro de versiones + imágenes Docker + rollback |
| RNF-12 | Observabilidad | Recopilar métricas de latencia, errores, disponibilidad y calidad de datos, con alertas y responsables definidos. | Prometheus + Grafana + Evidently |

La aprobación técnica de una nueva versión no implica automáticamente su autorización para uso clínico. Los modelos deberán cumplir criterios de desempeño y seguridad definidos junto con los responsables clínicos antes de su puesta en producción.

Los requisitos establecidos en esta sección servirán como base para las decisiones de arquitectura y la selección justificada de tecnologías en las siguientes etapas del pipeline.


## 5. Arquitectura general y flujos de procesamiento

### 5.1. Enfoque arquitectónico

Se propone una arquitectura MLOps modular que combina procesamiento batch para la preparación de datos y el entrenamiento de modelos, con inferencia online para atender las consultas médicas.

La arquitectura separa los procesos de datos, entrenamiento, despliegue e inferencia, permitiendo que los modelos en producción continúen operando de manera independiente de los procesos de actualización.

Se establecen tres flujos principales:

- **Flujo A - Datos (batch):** ingestión diaria de información clínica, validación, limpieza y preparación de datasets mediante una arquitectura Medallion (Bronze, Silver y Gold).
- **Flujo B - Inferencia (online):** consultas de los médicos a través de una aplicación web que consume una API de predicción.
- **Flujo C - Monitoreo y reentrenamiento:** seguimiento continuo de la operación, evaluación periódica del desempeño y actualización controlada de los modelos.


### 5.2. Diagrama general de arquitectura

La siguiente arquitectura representa el ciclo de vida end-to-end de la solución MLOps, desde la ingestión y preparación de los datos clínicos hasta el despliegue, inferencia, monitoreo y reentrenamiento de los modelos.

La propuesta conserva seis componentes principales: Data Ingestion, Data Processing & Feature Engineering, Model Engineering & Registry, Model Deployment (CI/CD), Serving & Prediction y Model Monitoring & Retraining.

![Arquitectura MLOps V2](docs/arquitectura ajustada_v2.drawio.png)

[Consultar arquitectura original — Unidad 1](docs/Arquitectura U1.png)


### 5.3. Flujos de procesamiento

| Flujo | Procesamiento | Frecuencia o disparador | Tecnologías principales |
|---|---|---|---|
| Ingestión de datos clínicos | Batch incremental | Diario | Airflow, Python, MinIO/S3 |
| Limpieza y transformación | Batch | Después de cada ingestión | Pandas, Pandera, Parquet |
| Entrenamiento y evaluación | Batch | Bajo demanda o reentrenamiento autorizado | Scikit-learn, XGBoost, Optuna |
| Registro de modelos | Por evento | Al aprobar un modelo candidato | MLflow |
| Integración y despliegue | Por evento | Cambios aprobados en código o modelos | GitHub Actions, Docker, GHCR |
| Predicción clínica | Online síncrono | Solicitud del médico | FastAPI, Pydantic |
| Monitoreo operativo | Continuo | Durante la operación | Prometheus, Grafana |
| Evaluación de drift | Batch periódico | Programado | Evidently |
| Reentrenamiento | Batch condicionado | Nuevos datos o degradación confirmada | Airflow, MLflow |

### 5.4. Decisiones principales

**Batch frente a streaming:** se selecciona ingestión batch diaria porque los datos históricos no requieren procesamiento continuo. No se incorpora Kafka inicialmente, evitando complejidad operativa innecesaria.

**Inferencia online:** se utiliza una API REST con FastAPI para atender solicitudes síncronas de los médicos. La necesidad de obtener respuestas inmediatas no implica utilizar una plataforma de streaming.

**Procesamiento de datos:** se propone Pandas para el volumen histórico estimado de 500.000 registros. Spark se considera una alternativa de evolución si el volumen o los tiempos de procesamiento superan la capacidad disponible.

**Despliegue:** se prioriza un servidor institucional mediante Docker, permitiendo centralizar seguridad, actualizaciones y monitoreo. Se contempla ejecución local para modelos livianos.

**Independencia operativa:** los modelos aprobados y sus transformaciones permanecerán disponibles en el servicio de inferencia, evitando depender de Airflow, del Data Lake o de MLflow para responder cada consulta.

Las decisiones anteriores se desarrollarán y justificarán en las siguientes secciones de diseño técnico.



