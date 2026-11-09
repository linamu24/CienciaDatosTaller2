# Taller 2. Predicción de abandono (churn) de clientes bancarios

## Integrantes

- Lina Marcela Muñoz
- Yair Andrade

## Objetivo

Construir una prueba de concepto de una herramienta de predicción de abandono temprano de clientes para FinAlpes, una fintech colombiana que ofrece servicios de analítica a bancos, cooperativas y aseguradoras. A partir del perfil comercial de una muestra de clientes europeos, se entrenan y comparan modelos de Machine Learning que identifican a los clientes en riesgo de abandonar, para que el banco pueda dirigirles campañas de retención, y se estima cuánto vale usarlos en un banco colombiano.

## Alcance

El repositorio cubre los puntos 1 a 7 del enunciado.

1. **Entendimiento y preparación de los datos.** Diagnóstico de calidad (faltantes, duplicados, categorías inconsistentes y valores imposibles), limpieza documentada y análisis exploratorio univariado y bivariado.
2. **Entrenamiento de modelos.** Regresión logística, Random Forest y Gradient Boosting, con y sin variables derivadas, usando una división estratificada 60/20/20 y búsqueda de hiperparámetros con validación cruzada.
3. **Análisis cuantitativo.** Métricas en el conjunto de prueba, intervalos de confianza por bootstrapping y pruebas estadísticas pareadas (A/B por bootstrapping y McNemar) para justificar la elección del modelo.
4. **Análisis cualitativo.** Interpretación global y de predicciones individuales con gráficos ICE y PDP, valores SHAP y LIME, y comparación entre los modelos.
5. **Generación de valor.** Ganancia esperada de una campaña de retención dirigida por el modelo en un banco de 20.000 clientes con 15% de churn, umbral de decisión elegido según el dinero, punto de equilibrio en valor y en caja, VPN a 3 años, sensibilidad, Monte Carlo y limitaciones. Los supuestos tienen fuente.
6. **Insights.** Presentación corta con los hallazgos principales y la recomendación final para el negocio.
7. **Declaración de uso de IA generativa.** Está al final de este README.

## Principales hallazgos

- El 20,4% de los clientes de la muestra abandonó el banco. Por ese desbalance, los modelos se evaluaron con F1, precisión, recall y PR-AUC en lugar de exactitud.
- El abandono sube con la edad (56% entre los 51 y 60 años), es mayor en los clientes inactivos, en Alemania y en quienes tienen un solo producto o más de dos. El perfil de mayor riesgo es un cliente de entre 50 y 60 años, inactivo y con un solo producto o con tres o más.
- El modelo elegido es un **Random Forest con las variables originales**. En el conjunto de prueba obtuvo un F1 de 0,637, una precisión de 0,680 y un recall de 0,600. Su ventaja en F1 sobre la regresión logística es significativa y relevante; frente al Gradient Boosting es marginal, porque su significancia cambia entre computadores (ver la nota de reproducibilidad en la sección 3.3 del notebook 2).
- El Random Forest y el Gradient Boosting ordenan a los clientes por riesgo igual de bien (ROC-AUC de 0,868 y 0,867) y, con el umbral elegido según el dinero, dejan la misma ganancia.
- El modelo se apoya sobre todo en la edad, el número de productos y la actividad del cliente, que explican cerca de dos tercios del valor SHAP.
- **Valor para un banco de 20.000 clientes.** Con un umbral de 0,30, la campaña retiene a unos 734 clientes al año, baja el churn de 15% a 11,3% y deja COP 380 M al año (intervalo de 95% entre 349 y 410 M), es decir, COP 19.018 por cliente evaluado. Contactar a todos sin modelo deja apenas COP 15,5 M. La ventaja del umbral 0,30 sobre el 0,5 no es concluyente.
- **Retorno.** Con COP 130 M de implementación y COP 50 M al año de operación, el VPN a 3 años es de COP 508 M. En valor económico la inversión se recupera en el mes 11; en caja, en el mes 67, porque el margen de cada cliente retenido llega a lo largo de varios años. En 10.000 escenarios simulados el VPN es positivo en el 86%, y el modelo supera a la mejor opción sin modelo en el 76%. El riesgo está en la efectividad de la oferta y en el costo por contacto, no en el modelo.
- **Recomendación.** Empezar con un piloto con grupo de control para medir la efectividad real de la oferta, reentrenar el modelo con datos del banco colombiano y escalar si la oferta convence a más del 15% de los clientes en riesgo.

El detalle completo, las pruebas estadísticas y las limitaciones están en los notebooks.

## Organización del repositorio

```
CienciaDatosTaller2/
├── data/
│   ├── churn.csv                          # Datos originales del curso
│   └── churn_limpio.csv                   # Datos limpios, generados por el notebook 01
├── docs/
│   ├── EnunciadoTaller2.pdf               # Enunciado del taller
│   └── presentacion/
│       └── presentacion.pdf               # Punto 6. Presentación
├── 01_entendimiento_preparacion.ipynb     # Punto 1. Entendimiento, calidad, limpieza y EDA
├── 02_modelado_evaluacion.ipynb           # Puntos 2, 3 y 4. Modelado, evaluación e interpretación
├── 03_generacion_valor.ipynb              # Punto 5. Generación de valor
├── requirements.txt                       # Dependencias con versiones probadas
└── README.md
```

## Instrucciones de ejecución

Los notebooks deben ejecutarse en este orden.

1. `01_entendimiento_preparacion.ipynb`. Carga `data/churn.csv`, diagnostica y limpia los datos, hace el análisis exploratorio y al final guarda el resultado en `data/churn_limpio.csv`.
2. `02_modelado_evaluacion.ipynb`. Carga `data/churn_limpio.csv`, entrena y compara los modelos, los evalúa en el conjunto de prueba y los interpreta.
3. `03_generacion_valor.ipynb`. Carga `data/churn_limpio.csv`, vuelve a entrenar el modelo elegido con los mismos hiperparámetros, división y semilla, y calcula el valor para el banco. No depende de los objetos del notebook 2, así que solo necesita que el notebook 1 se haya ejecutado antes.

Los tres notebooks se ejecutan de principio a fin sin errores con "Restart & Run All" y deben abrirse desde la carpeta raíz del repositorio, porque las rutas a los datos son relativas a ella. El entrenamiento del notebook 2 puede tardar varios minutos; el notebook 3 tarda menos de un minuto.

**Reproducibilidad.** Se fija la semilla `42` en todos los procesos aleatorios y los resultados reportados se obtuvieron con las versiones de `requirements.txt`. Aun así, el Random Forest puede dar resultados ligeramente distintos en otro computador (por ejemplo, un F1 de 0,633 en lugar de 0,637). El notebook 2 lo explica en la nota de reproducibilidad de la sección 3.3.

## Dependencias

El proyecto se probó con Python 3.13 y las siguientes librerías, cuyas versiones están en `requirements.txt`.

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

