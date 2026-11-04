# Taller 2. Predicción de abandono (churn) de clientes bancarios

## Integrantes

- Lina Marcela Muñoz
- Yair Andrade

## Objetivo

Construir una prueba de concepto de una herramienta de predicción de abandono temprano de clientes para FinAlpes, una fintech colombiana que ofrece servicios de analítica a bancos, cooperativas y aseguradoras. A partir del perfil comercial de una muestra de clientes europeos, se entrenan y comparan modelos de Machine Learning que identifican a los clientes en riesgo de abandonar, para que el banco pueda dirigirles campañas de retención.

## Alcance

El repositorio cubre los puntos 1 a 4 del enunciado.

1. **Entendimiento y preparación de los datos.** Diagnóstico de calidad (faltantes, duplicados, categorías inconsistentes y valores imposibles), limpieza documentada y análisis exploratorio univariado y bivariado.
2. **Entrenamiento de modelos.** Regresión logística, Random Forest y Gradient Boosting, cada uno con y sin variables derivadas, usando una división estratificada 60/20/20 y búsqueda de hiperparámetros con validación cruzada.
3. **Análisis cuantitativo.** Métricas en el conjunto de prueba, intervalos de confianza por bootstrapping y pruebas estadísticas pareadas (A/B por bootstrapping, prueba t pareada y McNemar) para justificar la elección del modelo.
4. **Análisis cualitativo.** Interpretación global y de predicciones individuales con gráficos ICE y PDP, valores SHAP y LIME, y comparación entre los modelos.

## Principales hallazgos

- El 20,4% de los clientes de la muestra abandonó el banco. Por ese desbalance, los modelos se evaluaron con F1, precisión, recall y PR-AUC en lugar de exactitud.
- El modelo elegido es un **Random Forest con las variables originales**. En el conjunto de prueba obtuvo un F1 de 0,637, una precisión de 0,680 y un recall de 0,600. Su ventaja en F1 sobre los demás candidatos es estadísticamente significativa y supera el umbral relevante de 0,02.
- El Random Forest y el Gradient Boosting ordenan a los clientes por riesgo igual de bien (ROC-AUC de 0,868 y 0,867). La diferencia entre ellos está en el umbral de decisión, por lo que la elección final del umbral debe basarse en los costos y beneficios de la campaña.
- La ingeniería de características mejoró a la regresión logística en 8 puntos de F1, pero no aportó a los modelos de árboles, que capturan por sí solos las relaciones no lineales.
- El modelo se apoya sobre todo en la edad, el número de productos y la actividad del cliente. El perfil de mayor riesgo es un cliente de entre 50 y 60 años, inactivo y con un solo producto o con tres o más.

El detalle completo, las pruebas estadísticas y las limitaciones están en los notebooks.

## Organización del repositorio

```
CienciaDatosTaller2/
├── data/
│   ├── churn.csv                          # Datos originales del curso
│   └── churn_limpio.csv                   # Datos limpios, generados por el notebook 01
├── docs/
│   └── EnunciadoTaller2.pdf               # Enunciado del taller
├── 01_entendimiento_preparacion.ipynb     # Punto 1. Entendimiento, calidad, limpieza y EDA
├── 02_modelado_evaluacion.ipynb           # Puntos 2, 3 y 4. Modelado, evaluación e interpretación
├── requirements.txt                       # Dependencias con versiones probadas
└── README.md
```

## Instrucciones de ejecución

Los notebooks deben ejecutarse en este orden.

1. `01_entendimiento_preparacion.ipynb`. Carga `data/churn.csv`, diagnostica y limpia los datos, hace el análisis exploratorio y al final guarda el resultado en `data/churn_limpio.csv`.
2. `02_modelado_evaluacion.ipynb`. Carga `data/churn_limpio.csv`, entrena y compara los modelos, los evalúa en el conjunto de prueba y los interpreta.

Ambos notebooks se ejecutan de principio a fin sin errores con "Restart & Run All" y deben abrirse desde la carpeta raíz del repositorio, porque las rutas a los datos son relativas a ella. El entrenamiento del notebook 2 puede tardar varios minutos.

Los resultados de los modelos de ensamble pueden variar ligeramente con otras versiones de scikit-learn, aunque se fija la semilla `42` en todos los procesos aleatorios. Los resultados reportados se obtuvieron con las versiones de `requirements.txt`.

## Dependencias

El proyecto se probó con Python 3.13.9 y las siguientes librerías, cuyas versiones están en `requirements.txt`.

- pandas, numpy, matplotlib y seaborn para la manipulación de datos y las gráficas
- scikit-learn para los modelos, la búsqueda de hiperparámetros y las métricas
- scipy y statsmodels para las pruebas estadísticas
- shap y lime para la interpretación de los modelos
- ipykernel para ejecutar los notebooks en Jupyter o VS Code

Se pueden instalar con el siguiente comando.

```bash
pip install -r requirements.txt
```

## Declaración de uso de Inteligencia Artificial

Durante el desarrollo de este taller se usó Claude (modelo Claude Opus 5.5) como asistente de apoyo para lo siguiente.

- Proponer código en Python para la limpieza de datos, el entrenamiento de los modelos, las pruebas estadísticas y las técnicas de interpretación.
- Discutir decisiones metodológicas, como los rangos válidos para la limpieza, el diseño de la división de los datos, la búsqueda de hiperparámetros y las pruebas pareadas para comparar modelos, contrastándolas con el material y las prácticas del curso.
- Redactar y corregir el estilo de las explicaciones de los notebooks.

Adicionalmente, durante la escritura del código se tuvo activo GitHub Copilot en Visual Studio Code, por lo que en algunos momentos autocompletó fragmentos de código mientras se escribía.

