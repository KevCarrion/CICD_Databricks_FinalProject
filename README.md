📘 Renewable Energy Generation Analytics – Azure Databricks Medallion Architecture
🚀 Project Overview

El presente proyecto tiene como objetivo analizar el desempeño operativo de activos de generación de energía renovable, integrando datos de producción y variables hidrológicas para construir indicadores clave de desempeño (KPIs) orientados a la toma de decisiones.

A partir de datasets estructurados en formato CSV almacenados en un Data Lake en Azure, se busca:

Evaluar la producción energética por central

Analizar la disponibilidad operativa

Calcular el factor de planta (Capacity Factor)

Medir el impacto de indisponibilidades

Integrar variables hidrológicas relevantes

Construir una capa analítica lista para dashboards ejecutivos

Para lograrlo, se implementa una arquitectura Medallion en Azure Databricks que permite estructurar el flujo de datos desde su estado crudo hasta su forma analítica optimizada, garantizando gobernanza, trazabilidad y escalabilidad.

El resultado final no es únicamente un ETL, sino una plataforma analítica estructurada y gobernada para el monitoreo del desempeño energético.

🏗 Arquitectura General
Arquitectura Medallion

La solución se basa en el patrón Medallion (Bronze → Silver → Golden), bajo Unity Catalog y orquestado mediante Databricks Workflows.

🔹 Capa RAW (Azure Data Lake Gen2)

Fuente: Archivos CSV

Autenticación: Managed Identity

Ubicación: Azure Data Lake Storage Gen2

Restricción del proyecto:

❌ No se utiliza DBFS como raw

❌ No se utilizan Volumes como raw

Esta capa contiene la información original sin transformación, asegurando integridad y trazabilidad.

🥉 Bronze – Extract

Objetivo: Preservar la data en su estado más cercano al origen.

Características:

Lectura directa desde ADLS

Escritura en formato Delta

Inclusión de metadatos:

ingestion_timestamp

source_file

environment

En esta capa no se aplican reglas de negocio complejas; solo estructuración básica y persistencia confiable.

🥈 Silver – Transform

Objetivo: Mejorar la calidad y consistencia de los datos.

Transformaciones realizadas:

Cast de tipos de datos

Limpieza de valores nulos

Eliminación de duplicados

Validaciones de reglas de negocio

Estandarización de columnas

Integración entre datasets (producción + hidrología)

Esta capa representa datos estructurados y confiables listos para análisis.

🥇 Golden – Load (Capa Analítica)

Objetivo: Generar tablas optimizadas para consumo analítico y visualización.

Se construyen:

Agregaciones por central

KPIs de desempeño

Indicadores de disponibilidad

Factor de planta

Impacto de indisponibilidades

Métricas consolidadas por periodo

Se aplican optimizaciones:

OPTIMIZE

ZORDER

Tablas Delta optimizadas para consulta

Esta capa alimenta directamente los dashboards ejecutivos.

⚙️ Workflow Implementado

El pipeline es orquestado mediante un Databricks Job estructurado de la siguiente manera:

1️⃣ PrepAmb

Preparación del entorno

Validación de catálogos y esquemas

Inicialización de variables

2️⃣ Ingesta Paralela (Bronze)

ingest_generation

ingest_hydrology

Permite cargar múltiples fuentes de manera concurrente, mejorando eficiencia.

3️⃣ Transform

Procesa Bronze → Silver

Aplica reglas de negocio

Integra datasets

4️⃣ Load

Genera capa Golden

Calcula KPIs

Aplica optimización Delta

5️⃣ Grants

Asigna permisos en Unity Catalog

Control de acceso por roles:

DataEngineers

DataScientists

Este flujo garantiza:

Orquestación controlada

Separación clara de responsabilidades

Gobernanza estructurada

🔐 Seguridad y Gobernanza

Acceso a ADLS exclusivamente mediante Managed Identity

Unity Catalog para:

Aislamiento de entornos

Control de acceso basado en roles

Gobernanza centralizada

Separación entre entorno Dev y Prod

Sin credenciales embebidas en código

🌎 Entornos
Desarrollo

Rama: develop

Workspace: Dev

Uso: pruebas y validaciones

Producción

Rama: main

Workspace: Prod

Despliegue vía CI/CD

🔄 CI/CD – GitHub Actions

El proyecto integra un pipeline automatizado que:

Valida cambios en Pull Requests

Despliega notebooks automáticamente

Actualiza Workflows

Permite promoción controlada a producción

Esto asegura:

Versionamiento formal

Reproducibilidad

Control de cambios

Buenas prácticas DevOps

📊 Dashboard Final

Las tablas Golden alimentan dashboards en Databricks con:

Producción total por central

Tendencia del factor de planta

Análisis de indisponibilidades

Comparativos por periodo

KPIs ejecutivos consolidados

La capa Golden está optimizada para consultas analíticas de alto rendimiento.

📁 Estructura del Repositorio
project-root/
│
├── notebooks/
│   ├── Preparacion_ambiente.py
│   ├── Ingest_hydro_generation_bronze.py
│   ├── Ingest_hydrology_bronze.py
│   ├── Transform.py
│   ├── Load.py
│   └── Grants.py
│
├── workflows/
│   └── etl_workflow.json
│
├── .github/workflows/
│   └── deploy.yml
│
├── Medallion_architecture_FinalProject.png
├── Workflow_completed.png
└── README.md
🎯 Cumplimiento del Proyecto Final

El proyecto cumple con:

Uso de arquitectura Medallion

ETL completo en PySpark

Uso obligatorio de Managed Identity

Integración de mínimo dos datasets

Gobernanza mediante Unity Catalog

Integración CI/CD

Visualización final

🏁 Ejecución del Proyecto

Cargar archivos CSV en el contenedor RAW de ADLS

Validar permisos de Managed Identity

Ejecutar Workflow en entorno Dev

Validar tablas Silver y Golden

Promover a Producción mediante merge a main

Visualizar dashboard en Databricks
