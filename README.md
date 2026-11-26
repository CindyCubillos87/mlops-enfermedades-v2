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

* Despliegue centralizado (modalidad principal): los modelos estarán alojados en servidores de la institución de salud (infraestructura on-premises), y los profesionales médicos podrán realizar solicitudes de predicción mediante una aplicación web que se comunica con una API. Esta alternativa permite centralizar la administración, seguridad, monitoreo y actualización de los modelos.
  
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

Se propone una arquitectura MLOps modular, con infraestructura principal on-premises en servidores de la institución de salud. Combina procesamiento batch para la gestión de datos y el entrenamiento de modelos, con inferencia online para atender las consultas médicas.

La arquitectura separa los procesos de ingestión, procesamiento, entrenamiento, despliegue e inferencia, permitiendo que el servicio predictivo continúe funcionando independientemente de las actividades de actualización de los modelos.

### 5.2. Diagrama general de arquitectura

El siguiente diagrama representa el pipeline MLOps end-to-end, desde la ingestión y preparación de información clínica hasta el despliegue, inferencia, monitoreo y reentrenamiento de modelos.

![Arquitectura MLOps V2](docs/arquitectura%20ajustada_v2.drawio.png)

[Consultar arquitectura original - Unidad 1](docs/Arquitectura%20MLOPS.pdf)

### 5.3. Flujos de procesamiento

| Proceso | Modalidad | Frecuencia o disparador | Tecnologías |
|---|---|---|---|
| Ingestión de datos clínicos | Batch incremental | Diario | Airflow, APIs, Python |
| Procesamiento y validación | Batch | Después de la ingestión | Pandas, Pandera, MinIO, Parquet |
| Entrenamiento y evaluación | Batch | Bajo demanda o reentrenamiento autorizado | Scikit-learn, XGBoost, Optuna |
| Registro de modelos | Por evento | Modelo validado | MLflow |
| CI/CD y despliegue | Por evento | Cambios aprobados en código o modelos | GitHub Actions, Docker, GHCR |
| Predicción clínica | Online síncrono | Solicitud del médico | Nginx, FastAPI, Pydantic |
| Monitoreo operativo | Continuo | Durante la operación | Prometheus, Grafana |
| Evaluación de drift | Batch periódico | Programado | Evidently |
| Reentrenamiento | Batch condicionado | Nuevos datos, degradación o evidencia médica | Airflow, MLflow |


Los componentes y mecanismos de integración se detallan en las secciones 6 a 9. Las decisiones tecnológicas, sus justificaciones y las alternativas consideradas se consolidan en la sección 10.



## 6. Arquitectura de datos

La arquitectura de datos comprende la ingestión, almacenamiento, validación y transformación de la información clínica utilizada para entrenar y evaluar los modelos predictivos.

Se propone un procesamiento batch incremental diario, orquestado mediante Apache Airflow, y un Data Lake basado en la arquitectura Medallion (Bronze, Silver y Gold).

### 6.1. Ingestión de datos clínicos

**Fuentes de información**

Se consideran las siguientes fuentes:

- Historias clínicas electrónicas: síntomas, antecedentes, resultados clínicos y evolución del paciente.
- Diagnósticos confirmados: etiquetas clínicas utilizadas como referencia para el entrenamiento y evaluación.
- Información demográfica: edad y otras características relevantes y autorizadas.
- Registros especializados: información de enfermedades huérfanas procedente de fuentes institucionales o registros autorizados.
- Retroalimentación clínica: nuevos diagnósticos confirmados asociados a predicciones anteriores.

Las predicciones del sistema no serán consideradas diagnósticos confirmados sin validación clínica.

**Estrategia de ingestión**

Se utilizará un proceso batch incremental ejecutado diariamente mediante Apache Airflow.

La extracción se realizará a través de APIs institucionales, preferiblemente con estándares de interoperabilidad como HL7 FHIR cuando estén disponibles, o mediante conectores Python a bases de datos y archivos autorizados.

El proceso contempla:

1. Identificación de registros nuevos o modificados desde la última ejecución exitosa.
2. Extracción y almacenamiento de datos originales en Bronze.
3. Registro de metadatos de origen, fecha y ejecución.
4. Verificación de integridad y detección de duplicados.
5. Reintentos automáticos y alertas ante errores de extracción.

Se utilizarán identificadores únicos y marcas de actualización para permitir cargas idempotentes y evitar reprocesamientos innecesarios. Las fuentes que no permitan extracción incremental utilizarán cargas completas controladas.

**Justificación:** Airflow permite programar, supervisar y gestionar dependencias entre tareas. Se selecciona procesamiento batch porque los datos históricos no requieren ingestión continua. No se incorpora Kafka inicialmente, dado que el escenario no demanda streaming de eventos clínicos.

### 6.2. Almacenamiento y procesamiento Medallion

Se propone MinIO como almacenamiento de objetos compatible con S3, desplegado en infraestructura institucional. Los datos se organizarán mediante una arquitectura Medallion:

| Capa | Función | Procesamiento |
|---|---|---|
| Bronze | Conservar los registros originales y metadatos de ingestión | Almacenamiento de datos sin transformaciones analíticas |
| Silver | Disponer de información limpia, validada y estandarizada | Eliminación de duplicados, normalización, validación de tipos y consistencia |
| Gold | Proporcionar datasets preparados para modelado y evaluación | Selección de variables, codificación y generación de características derivadas |

Se utilizará principalmente Parquet para los datos procesados de Silver y Gold, debido a su formato columnar y eficiencia para el análisis de información tabular.

El almacenamiento estará organizado por capas, fuentes y versiones de datasets, con controles de acceso y políticas de retención definidos por la institución.

### 6.3. Calidad de datos y Feature Engineering

Las transformaciones se implementarán mediante scripts Python utilizando:

- **Pandas:** limpieza, integración, agregaciones y construcción de variables.
- **Pandera:** validación de esquemas, tipos de datos, rangos y restricciones.
- **Scikit-learn:** transformaciones que requieran parámetros aprendidos, como imputación o codificación ajustada sobre el conjunto de entrenamiento.

Se contemplan variables clínicas como presencia y duración de síntomas, antecedentes, características demográficas y otras características derivadas relevantes.

Las transformaciones que aprenden parámetros de los datos se ajustarán exclusivamente sobre los conjuntos de entrenamiento, evitando fugas de información hacia validación y prueba. El tratamiento del desbalance de clases se realizará posteriormente, dentro del pipeline de entrenamiento.

**Controles de calidad:** los registros inválidos se aislarán para revisión, se documentarán las reglas incumplidas y se evitará que datos que no superen las validaciones requeridas avancen hacia Gold.

### 6.4. Versionamiento, seguridad y escalabilidad

**Versionamiento y trazabilidad:** cada dataset utilizado para entrenamiento tendrá un identificador de versión, referencia a las fuentes, período de extracción, fecha de procesamiento y reglas de transformación aplicadas. Estas referencias se registrarán junto con los experimentos en MLflow.

**Seguridad:** se aplicarán mecanismos de seudonimización, cifrado, permisos por rol y restricción de acceso a información clínica. Los identificadores necesarios para relacionar registros de un mismo paciente se gestionarán de manera protegida.

**Escalabilidad:** para el escenario inicial de 500.000 registros clínicos estructurados se propone Pandas, sujeto a validación de memoria y tiempos de ejecución. Si el volumen o la complejidad del procesamiento superan la capacidad disponible, se evaluará migrar las transformaciones a Spark.

**Consideraciones operativas:** MinIO requerirá gestión institucional de almacenamiento persistente, respaldos, recuperación, actualizaciones y soporte. La plataforma y su modalidad de soporte deberán validarse antes de su implementación productiva.


## 7. Arquitectura de Machine Learning

Esta etapa comprende la preparación de los conjuntos de entrenamiento, experimentación, evaluación, selección y registro de modelos predictivos para enfermedades comunes y huérfanas.

Debido a la diferencia en disponibilidad de datos, se propone evaluar enfoques de modelado complementarios. La selección definitiva de algoritmos y su integración dependerán de los resultados experimentales y de los criterios de validación clínica establecidos.

### 7.1. Preparación de datos para entrenamiento

Los datasets preparados en la capa Gold serán utilizados para construir los conjuntos de entrenamiento, validación y prueba.

Se establecen las siguientes consideraciones:

- **División por paciente:** los registros de un mismo paciente no podrán distribuirse entre entrenamiento, validación y prueba, evitando fugas de información.
- **Validación temporal:** cuando existan registros longitudinales, se evaluarán particiones temporales que respeten la disponibilidad de información al momento de la predicción.
- **Preprocesamiento reproducible:** las transformaciones que requieren ajuste, como imputación y codificación, se implementarán mediante pipelines de Scikit-learn, ajustándose únicamente sobre los datos de entrenamiento.
- **Tratamiento del desbalance:** se evaluarán ponderación de clases y técnicas de remuestreo mediante Imbalanced-learn, aplicadas exclusivamente a los conjuntos de entrenamiento dentro de cada partición de validación.

Los procedimientos de preparación serán versionados junto con los modelos para garantizar consistencia entre entrenamiento e inferencia.

### 7.2. Estrategia de experimentación y modelos candidatos

Se evaluarán algoritmos supervisados para enfermedades comunes y estrategias especializadas para enfermedades huérfanas, considerando la disponibilidad y calidad de los datos.

| Enfoque | Modelos o estrategias candidatos | Justificación |
|---|---|---|
| Modelo de referencia (baseline) | Regresión logística | Establecer un desempeño inicial interpretable para comparar alternativas. |
| Enfermedades comunes | Random Forest y XGBoost | Evaluar patrones no lineales e interacciones entre variables clínicas tabulares. |
| Enfermedades huérfanas | Ponderación de clases, aprendizaje por transferencia y métodos basados en similitud | Explorar alternativas ante la escasez de casos confirmados. |
| Optimización | Optuna | Automatizar la búsqueda de hiperparámetros bajo criterios de evaluación definidos. |

La viabilidad de los enfoques especializados dependerá del número de casos disponibles por enfermedad. Cuando los datos sean insuficientes para construir y validar un modelo confiable, se priorizarán mecanismos de alerta de sospecha y revisión clínica, evitando emitir predicciones con una confianza injustificada.

No se establece un algoritmo definitivo en esta fase, dado que la selección requiere experimentación con datos reales.

### 7.3. Evaluación y selección de modelos

Los modelos candidatos se evaluarán mediante validación cruzada compatible con la separación por paciente y, cuando corresponda, con la estructura temporal de los datos. Se reservará un conjunto de prueba independiente para la evaluación final.

Las principales métricas consideradas serán:

| Métrica | Propósito |
|---|---|
| Sensibilidad (Recall) | Medir la capacidad de identificar pacientes con la enfermedad y reducir falsos negativos. |
| Especificidad | Evaluar la identificación de pacientes que no presentan la enfermedad. |
| Precisión (Precision) | Determinar qué proporción de las alertas positivas corresponde a casos confirmados. |
| PR-AUC | Evaluar el desempeño en problemas con clases poco frecuentes. |
| Calibración | Verificar la correspondencia entre las probabilidades estimadas y las frecuencias observadas. |

La evaluación se realizará por enfermedad o grupo clínico, evitando depender únicamente de métricas globales que puedan ocultar un desempeño deficiente en enfermedades huérfanas.

La selección considerará también el costo de inferencia, interpretabilidad, estabilidad y capacidad de generalización.

Los umbrales mínimos de desempeño y los criterios de aprobación deberán establecerse con especialistas clínicos, considerando los riesgos asociados a falsos positivos y falsos negativos.

### 7.4. Integración de resultados

La arquitectura contempla un enfoque general para enfermedades comunes y alternativas especializadas para enfermedades huérfanas.

Durante la experimentación se evaluará si conviene integrar sus resultados mediante reglas de decisión, combinación de probabilidades calibradas u otros mecanismos validados.

La estrategia seleccionada deberá evitar contradicciones entre las salidas de los modelos y contemplar casos en los que exista incertidumbre o evidencia insuficiente.

El resultado se presentará al médico como información de apoyo para la evaluación clínica, sin sustituir el diagnóstico profesional.

### 7.5. Registro y versionamiento con MLflow

Se utilizará MLflow para gestionar experimentos, comparar resultados y registrar los artefactos asociados a los modelos.

Cada ejecución conservará:

- Versión del código y referencia del dataset de entrenamiento.
- Algoritmo, hiperparámetros y configuración experimental.
- Métricas de entrenamiento, validación y prueba.
- Modelo entrenado y transformaciones de preprocesamiento.
- Versión del artefacto y estado de aprobación.

Los modelos candidatos serán comparados antes de su promoción al registro de modelos aprobados.

Solo las versiones que superen los criterios técnicos y la validación clínica podrán avanzar al proceso de CI/CD y despliegue descrito en la sección 8.


## 8. Arquitectura de despliegue y Serving

La arquitectura contempla un despliegue centralizado en infraestructura local de la institución de salud (on-premises), utilizando contenedores Docker para ejecutar los servicios de inferencia.

Se propone separar la integración y publicación de software del despliegue en producción, garantizando que únicamente las versiones aprobadas de modelos y servicios sean utilizadas durante las consultas clínicas.

### 8.1. Integración y despliegue continuo (CI/CD)

Se utilizarán GitHub, GitHub Actions, Docker y GitHub Container Registry (GHCR) para gestionar el ciclo de publicación de las aplicaciones y modelos.

El proceso será el siguiente:

1. **Control de versiones:** el código de la aplicación, configuraciones y pipelines se administrará mediante GitHub, utilizando ramas y Pull Requests.
2. **Integración continua:** GitHub Actions ejecutará pruebas unitarias, validaciones de API, verificaciones del preprocesamiento y pruebas de integración.
3. **Selección del modelo:** se recuperarán desde MLflow únicamente los modelos y artefactos que hayan superado la validación técnica y clínica.
4. **Construcción y publicación:** se generará una imagen Docker versionada que incluya FastAPI, dependencias, modelos y transformaciones aprobadas. La imagen se publicará en GHCR.
5. **Despliegue controlado:** tras la autorización correspondiente, un proceso ejecutado desde la infraestructura institucional descargará la imagen aprobada y actualizará los contenedores mediante Docker Compose.
6. **Verificación y recuperación:** se realizarán pruebas de funcionamiento posteriores al despliegue. Ante fallas, se restaurará la versión previamente aprobada (rollback).

El despliegue no se ejecutará automáticamente por el simple registro de un nuevo modelo en MLflow. La promoción a producción requerirá el cumplimiento de criterios técnicos y autorización clínica.

### 8.2. Infraestructura de inferencia

La solución se ejecutará inicialmente en un servidor institucional mediante Docker Compose.

| Componente | Tecnología | Responsabilidad |
|---|---|---|
| Interfaz médica | Aplicación web (SPA) | Capturar datos clínicos y presentar resultados |
| Punto de entrada | Nginx | Servir la aplicación, actuar como reverse proxy y distribuir solicitudes |
| Servicio predictivo | FastAPI + Pydantic | Validar solicitudes y ejecutar las predicciones |
| Contenedores de inferencia | Docker (2 réplicas) | Atender solicitudes utilizando los modelos versionados cargados en memoria |
| Auditoría | PostgreSQL | Registrar predicciones, versiones y metadatos de trazabilidad |
| Administración | Docker Compose | Configurar y administrar los servicios del despliegue piloto |

Nginx distribuirá las solicitudes entre dos réplicas del servicio FastAPI mediante balanceo de carga. Ambas ejecutarán la misma versión aprobada y tendrán disponibles los modelos generales y especializados, junto con su preprocesamiento.

El uso de dos réplicas mejora la continuidad ante fallas de un proceso, pero no proporciona alta disponibilidad frente a fallas del servidor físico. Una futura expansión podría incorporar servidores independientes y balanceo externo.

### 8.3. Flujo de inferencia online

Las predicciones se realizarán de forma síncrona mediante una API REST sobre HTTPS.

1. El médico se autentica y registra los síntomas y características clínicas del paciente.
2. La aplicación envía la solicitud al servicio de inferencia mediante Nginx.
3. FastAPI y Pydantic validan el esquema y los valores recibidos.
4. Se aplica el preprocesamiento versionado y se ejecutan los modelos correspondientes.
5. Una capa de decisión integra los resultados utilizando criterios previamente validados, identificando casos de incertidumbre o evidencia insuficiente.
6. Se presenta al médico la estimación y las advertencias correspondientes.
7. Se registra la operación en PostgreSQL para auditoría y posterior evaluación.

Los modelos se cargarán en memoria al iniciar los contenedores, evitando depender de MLflow o del Data Lake durante cada consulta.

El servicio se dimensionará para soportar el escenario supuesto de 2.000 solicitudes diarias y 20 concurrentes, con un objetivo de latencia p95 inferior a 2 segundos, sujeto a pruebas de carga.

### 8.4. Seguridad y protección de datos

La infraestructura de inferencia estará protegida mediante una red clínica con acceso restringido.

Se contemplan los siguientes mecanismos:

- HTTPS/TLS para proteger las comunicaciones.
- Autenticación institucional y autorización basada en roles.
- Firewall y segmentación de red para restringir el acceso a los servicios internos.
- Gestión segura de credenciales, evitando almacenarlas en el código o las imágenes Docker.
- Cifrado y controles de acceso para datos clínicos y registros de auditoría.
- Registro de accesos, predicciones y versiones de modelos para garantizar trazabilidad.

La solución deberá ajustarse a las políticas institucionales y a la normativa de protección de datos personales aplicable.

### 8.5. Modalidad alternativa: ejecución local

Para modelos con bajos requerimientos computacionales, se contempla la ejecución directamente en computadores autorizados de los médicos mediante Docker.

Esta modalidad incluirá el servicio de inferencia y sus artefactos versionados, sin requerir conexión permanente con la infraestructura de entrenamiento.

Su adopción dependerá de la capacidad del equipo, los controles de seguridad y los procedimientos de distribución y actualización establecidos por la institución.

La modalidad centralizada se mantiene como alternativa principal debido a las ventajas de administración, auditoría, monitoreo y actualización de los modelos.


## 9. Monitoreo y reentrenamiento

La arquitectura contempla mecanismos de monitoreo para supervisar la calidad de los datos, el desempeño predictivo y la operación del servicio clínico. Su propósito es detectar oportunamente problemas que puedan afectar la confiabilidad de las predicciones y activar procesos controlados de revisión y actualización de modelos.

Se diferencian el monitoreo operativo continuo y la evaluación periódica del desempeño predictivo, debido a que los diagnósticos confirmados pueden estar disponibles después de la consulta médica.

### 9.1. Estrategia de monitoreo

Se utilizarán Prometheus para recopilar métricas operativas, Grafana para visualizar indicadores y gestionar alertas, y Evidently para generar evaluaciones de calidad, drift y desempeño predictivo.

| Dimensión | Indicadores | Frecuencia propuesta |
|---|---|---|
| Calidad de datos | Valores faltantes, registros inválidos, cambios de esquema y valores fuera de rango | Cada carga batch y evaluación periódica de entradas |
| Drift de datos | Cambios en la distribución de síntomas, características demográficas y variables clínicas | Semanal |
| Desempeño predictivo | Sensibilidad, especificidad, precisión, PR-AUC y calibración, por enfermedad | Mensual, cuando existan suficientes diagnósticos confirmados |
| Operación del servicio | Latencia p95, errores HTTP, disponibilidad y volumen de solicitudes | Continuo |
| Drift de concepto | Cambios en la relación entre características clínicas y diagnósticos confirmados | Evaluación periódica con datos etiquetados |

La evaluación predictiva se realizará comparando las predicciones registradas con diagnósticos posteriormente confirmados, considerando la versión del modelo utilizada y la fecha de disponibilidad de cada etiqueta.

Para enfermedades huérfanas se reportará el número de casos evaluados y la incertidumbre de las métricas. Cuando la muestra sea insuficiente, no se interpretarán pequeñas variaciones como evidencia concluyente de degradación.

### 9.2. Alertas y criterios de revisión

Se establecerán alertas para identificar situaciones que requieran análisis técnico o clínico.

| Evento | Acción propuesta |
|---|---|
| Latencia p95 superior a 2 segundos bajo la carga definida | Revisar recursos, concurrencia y rendimiento de la API |
| Fallas recurrentes de ingestión o registros inválidos | Notificar al equipo de datos y revisar el pipeline |
| Drift significativo de variables clínicas | Analizar las causas y evaluar su impacto sobre las predicciones |
| Disminución relevante del desempeño predictivo | Comparar con la versión vigente y solicitar revisión clínica |
| Incorporación de nuevos casos confirmados | Evaluar si existe información suficiente para mejorar el modelo |
| Nueva evidencia médica relevante | Revisar variables, criterios clínicos y estrategia de modelado |

Los umbrales específicos de drift y degradación predictiva se definirán durante la validación inicial, tomando como referencia el desempeño del modelo aprobado y la variabilidad observada en los datos.

Una alerta no implicará automáticamente reentrenar o desplegar un modelo. Primero se determinará si el problema se relaciona con la calidad de los datos, cambios en la población, fallas operativas o degradación real del modelo.

### 9.3. Pipeline de reentrenamiento controlado

El reentrenamiento se ejecutará mediante un pipeline batch orquestado con Apache Airflow, bajo demanda o después de una revisión programada.

El proceso contempla las siguientes etapas:

1. **Activación:** evaluación mensual o solicitud de reentrenamiento autorizada ante nuevos datos, degradación confirmada o evidencia médica relevante.
2. **Preparación:** recuperación de datasets versionados de la capa Gold y aplicación de controles de calidad.
3. **Entrenamiento:** ejecución del proceso de experimentación con los datos disponibles, manteniendo la separación por paciente y los controles contra data leakage.
4. **Evaluación:** comparación del modelo candidato con la versión vigente utilizando conjuntos de evaluación independientes y métricas por enfermedad.
5. **Registro:** almacenamiento de parámetros, métricas, transformaciones y artefactos en MLflow.
6. **Aprobación:** revisión técnica y clínica de los resultados antes de autorizar una nueva versión.
7. **Despliegue:** ejecución del proceso CI/CD definido en la sección 8, incluyendo pruebas, publicación y actualización controlada del servicio.

Si el modelo candidato no supera los criterios establecidos, se conserva la versión vigente en producción.

### 9.4. Trazabilidad y recuperación

Cada versión desplegada deberá mantener una relación verificable entre el código, dataset, preprocesamiento, modelo y artefacto Docker utilizado.

Se conservarán registros de monitoreo y auditoría que permitan investigar incidentes y comparar el comportamiento de distintas versiones.

En caso de fallas o degradación posterior al despliegue, se ejecutará el procedimiento de rollback hacia una imagen y versión de modelo previamente aprobadas.

El proceso de monitoreo y reentrenamiento será independiente del servicio de inferencia, de modo que las actividades de actualización no interrumpan las consultas médicas.


## 10. Decisiones arquitectónicas, limitaciones y conclusiones

### 10.1. Decisiones arquitectónicas principales

Las decisiones tecnológicas se establecieron considerando el escenario hipotético de una institución de salud con 500.000 registros clínicos históricos, 2.000 solicitudes de predicción diarias y una infraestructura principal on-premises.

| Decisión | Tecnología seleccionada | Alternativa considerada | Justificación |
|---|---|---|---|
| Ingestión de datos | Batch incremental con Airflow | Streaming con Kafka | Los datos históricos no requieren procesamiento continuo; batch reduce la complejidad operativa. |
| Almacenamiento | MinIO + arquitectura Medallion | Data Lake administrado en la nube | Permite mantener los datos dentro de la infraestructura institucional y separar las capas Bronze, Silver y Gold. |
| Procesamiento | Pandas + Pandera | Apache Spark | El volumen inicial permite evaluar un enfoque no distribuido, sujeto a pruebas de capacidad. |
| Modelado | Experimentación con Scikit-learn y XGBoost | Selección anticipada de un único algoritmo | Permite comparar alternativas antes de elegir los modelos definitivos. |
| Registro de modelos | MLflow | Gestión manual de archivos | Proporciona versionamiento, trazabilidad y comparación de experimentos. |
| CI/CD | GitHub Actions + Docker + GHCR | Construcción y despliegue manual | Automatiza pruebas y publicación de imágenes versionadas. |
| Inferencia | FastAPI + Nginx + Docker Compose | Servicios administrados en la nube | Facilita el despliegue piloto en servidores institucionales. |
| Monitoreo | Prometheus + Grafana + Evidently | Supervisión manual | Permite observar el servicio y detectar cambios en los datos y el desempeño. |
| Reentrenamiento | Airflow + MLflow + aprobación clínica | Actualización automática sin supervisión | Permite actualizar los modelos de forma controlada y trazable. |

### 10.2. Limitaciones y consideraciones

La arquitectura presenta las siguientes limitaciones, que deberán evaluarse antes de una implementación productiva:

- **Disponibilidad de datos:** la escasez de diagnósticos confirmados para enfermedades huérfanas puede impedir el entrenamiento o la validación confiable de determinados modelos.
- **Validación clínica:** los modelos requieren evaluación con datos representativos y aprobación de especialistas antes de utilizarse como apoyo a decisiones médicas.
- **Infraestructura:** el piloto contempla un servidor principal; las dos réplicas FastAPI no garantizan alta disponibilidad ante fallas físicas del servidor.
- **Escalabilidad:** la selección inicial de Pandas y Docker Compose deberá revisarse si aumentan significativamente los volúmenes de datos, la concurrencia o los requisitos operativos.
- **Operación y seguridad:** la infraestructura on-premises requiere administración, respaldos, actualización de componentes, control de accesos y procedimientos de recuperación.
- **Dependencias externas:** GitHub y GHCR requieren conectividad y controles de acceso para los procesos de desarrollo y publicación, aunque la inferencia puede operar independientemente.
- **Dimensionamiento:** los volúmenes, frecuencias y objetivos de rendimiento definidos son supuestos de diseño pendientes de validación mediante pruebas y levantamiento de requisitos.


### 10.3. Conclusiones

La arquitectura V2 evoluciona la propuesta inicial hacia un diseño MLOps reproducible, modular y trazable, con decisiones tecnológicas justificadas y mecanismos de operación definidos. Su implementación deberá realizarse progresivamente, validando los supuestos de infraestructura, rendimiento y confiabilidad clínica antes de su utilización en un entorno asistencial.





