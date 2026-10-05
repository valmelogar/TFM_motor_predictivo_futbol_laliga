# Motor predictivo de resultados de fútbol - LaLiga

Proyecto desarrollado como parte de un Trabajo Fin de Máster (TFM) del Máster en Inteligencia Artificial de UTAMED.

El proyecto desarrolla un **motor predictivo de resultados de partidos de LaLiga española mediante técnicas de Machine Learning**, utilizando información histórica disponible antes de cada encuentro. El objetivo principal es estudiar hasta qué punto las variables históricas de rendimiento permiten predecir las tres posibles categorías de resultado:

- **HomeWin**: victoria del equipo local.
- **Draw**: empate.
- **AwayWin**: victoria del equipo visitante.

El repositorio contiene el notebook principal del proyecto, en el que se realiza el proceso completo de preparación de datos, ingeniería de variables, entrenamiento, comparación y evaluación de modelos.

---

## 1. Objetivo del proyecto

El objetivo general del proyecto es desarrollar un motor predictivo de resultados de fútbol basado en datos abiertos y reproducible mediante Python.

De forma específica, el proyecto aborda:

1. La obtención y preparación de datos históricos de fútbol.
2. El filtrado de los partidos correspondientes a LaLiga española.
3. La construcción de variables históricas calculadas exclusivamente con información previa a cada partido.
4. La creación de un conjunto de datos preparado para clasificación multiclase.
5. El entrenamiento y comparación de distintos modelos de Machine Learning.
6. La evaluación de los modelos mediante métricas adecuadas para un problema con desbalance entre clases.
7. La selección de un modelo final para la predicción de resultados.

Como complemento, el TFM plantea de forma teórica una futura herramienta de visualización táctica interactiva orientada a facilitar la interpretación de los resultados para perfiles profesionales del fútbol.

---

## 2. Dataset

El proyecto utiliza el **European Soccer Database**, publicado en Kaggle por **Hugo Mathien**.

Fuente:

https://www.kaggle.com/datasets/hugomathien/soccer

El dataset contiene información histórica de partidos y equipos de distintas competiciones europeas durante el periodo 2008-2016.

En este proyecto se utiliza exclusivamente la información correspondiente a **LaLiga española**.

### Descarga del dataset

El dataset puede descargarse directamente desde Kaggle:

https://www.kaggle.com/datasets/hugomathien/soccer

No se incluye el dataset original dentro de este repositorio.

El usuario debe descargar los archivos correspondientes y colocarlos en una carpeta local destinada a los datos antes de ejecutar el notebook.

---

## 3. Metodología

El proyecto sigue un pipeline de Machine Learning orientado a la predicción prepartido:

```text
European Soccer Database
          ↓
   Filtrado de LaLiga
          ↓
   Limpieza y preparación
          ↓
 Ingeniería de variables
          ↓
 División temporal
          ↓
 Entrenamiento de modelos
          ↓
 Comparación
          ↓
 Evaluación final
```

Una característica fundamental del proyecto es que las variables predictoras se construyen utilizando únicamente información disponible **antes del partido que se pretende predecir**.

Esto permite evitar el **data leakage** y aproximar el experimento a un escenario real de predicción prepartido.

---

## 4. Construcción de variables

La principal aportación metodológica del proyecto es la construcción de variables históricas de rendimiento.

Para ello, se reconstruye cronológicamente el historial de cada equipo y se generan variables a partir de sus partidos anteriores.

Entre los principales grupos de variables se encuentran:

### Forma reciente

Información correspondiente a los últimos cinco partidos de cada equipo dentro de la temporada, incluyendo:

- Puntos obtenidos.
- Victorias.
- Empates.
- Derrotas.
- Media de goles marcados.
- Media de goles recibidos.

### Rendimiento histórico acumulado

Variables relacionadas con el rendimiento acumulado del equipo:

- Partidos disputados previamente.
- Puntos por partido.
- Media de goles marcados.
- Media de goles recibidos.

### Rendimiento local / visitante

Se diferencia el comportamiento del equipo en función de su condición:

- Rendimiento histórico del equipo local jugando en casa.
- Rendimiento histórico del equipo visitante jugando fuera de casa.

### Contexto de temporada

Se incluyen variables relacionadas con la situación competitiva previa:

- Puntos acumulados.
- Diferencia de goles.
- Posición previa en la clasificación.

### Diferencias entre equipos

También se construyen variables comparativas entre ambos equipos, como diferencias en:

- Puntos recientes.
- Puntos acumulados.
- Goles marcados.
- Goles recibidos.
- Posición en la clasificación.

Todas estas variables se calculan respetando el orden temporal.

---

## 5. División temporal

Para evitar utilizar información futura y simular un escenario realista de predicción, los datos se dividen temporalmente por temporadas.

| Conjunto      | Temporadas        |
| ------------- | ----------------- |
| Entrenamiento | 2008-09 a 2013-14 |
| Validación    | 2014-15           |
| Test          | 2015-16           |

Esta estrategia permite entrenar los modelos con información pasada y evaluar posteriormente su comportamiento sobre partidos temporalmente posteriores.

No se utiliza una división aleatoria convencional, ya que podría mezclar información de temporadas anteriores y posteriores.

---

## 6. Modelos evaluados

Se comparan diferentes enfoques de clasificación multiclase.

### Regresión Logística Multinomial

Se utiliza como modelo base debido a su simplicidad e interpretabilidad.

También se evalúa una versión balanceada mediante ponderación de clases para reducir el sesgo hacia las categorías mayoritarias.

### Random Forest

Se evalúan diferentes configuraciones de Random Forest:

- Random Forest estándar.
- Random Forest regularizado.
- Random Forest regularizado y balanceado.

Este algoritmo permite capturar relaciones no lineales e interacciones entre variables.

### XGBoost

Se incorpora como modelo de boosting para comprobar si un algoritmo más sofisticado puede mejorar los resultados obtenidos por los modelos anteriores.

Todos los modelos se entrenan y comparan bajo las mismas condiciones de entrenamiento y validación.

---

## 7. Métricas de evaluación

Debido al desbalance entre las tres categorías de resultado, la métrica principal utilizada para la comparación es el **Macro F1-score**.

El Macro F1 calcula el F1-score de cada clase y posteriormente obtiene la media otorgando el mismo peso a:

- Victoria local.
- Empate.
- Victoria visitante.

Además, se analizan otras métricas:

- Accuracy.
- Precision.
- Recall.
- F1-score por clase.
- Matriz de confusión.

La utilización del Macro F1 permite evaluar de manera más equilibrada el comportamiento del modelo sobre las tres categorías.

---

## 8. Resultados principales

Durante la fase de validación, el modelo que obtuvo el mejor equilibrio entre las tres clases fue el:

**Random Forest balanceado**

Resultado principal en validación:

**Macro F1 = 49,25 %**

El modelo presentó los siguientes F1-score por clase:

| Clase              | F1-score |
| ------------------ | -------: |
| Victoria local     |     66 % |
| Empate             |     34 % |
| Victoria visitante |     47 % |

Posteriormente, el modelo seleccionado se evaluó sobre el conjunto de test, que no había sido utilizado durante el entrenamiento.

Resultados sobre test:

- **Accuracy: 46,6 %**
- **Macro F1: 43,8 %**

Resultados por clase en test:

| Clase              | Precision | Recall | F1-score |
| ------------------ | --------: | -----: | -------: |
| Victoria local     |      69 % |   56 % |     62 % |
| Empate             |      23 % |   34 % |     27 % |
| Victoria visitante |         — |      — |     42 % |

El resultado muestra que la **victoria local** continúa siendo la categoría mejor predicha, mientras que el **empate** representa la mayor dificultad para el modelo.

> Nota: los valores completos de las métricas y matrices de confusión deben consultarse en el notebook y en la documentación del TFM.

---

## 9. Visualización táctica

Como evolución futura del proyecto, el TFM plantea una propuesta teórica de herramienta de visualización táctica interactiva.

La herramienta propuesta integraría:

- Información histórica de los equipos.
- Estadísticas de rendimiento.
- Predicciones del modelo.
- Probabilidades de las tres categorías.
- Visualizaciones tácticas y espaciales.

Entre las tecnologías que podrían emplearse para una futura implementación se encuentran:

- `mplsoccer`
- `Streamlit`
- `Plotly Dash`
- Power BI

La implementación completa de esta herramienta queda fuera del alcance del presente repositorio.

---

## 10. Reproducibilidad

El proyecto está diseñado siguiendo criterios de reproducibilidad:

- Uso de datos abiertos.
- Separación temporal entre entrenamiento, validación y test.
- Construcción de variables exclusivamente con información previa al partido.
- Comparación de varios modelos.
- Uso de semillas cuando corresponde.
- Documentación del proceso de preparación de datos y modelado.

Para reproducir los resultados es necesario disponer del dataset original y ejecutar el notebook siguiendo el flujo indicado.

---

## 11. Limitaciones

El proyecto presenta algunas limitaciones importantes:

### Periodo temporal

Los datos utilizados corresponden al periodo 2008-2016, por lo que no representan necesariamente el fútbol profesional actual.

### Ausencia de datos de eventos

El modelo se basa principalmente en variables históricas agregadas y no incorpora información detallada de eventos como:

- Pases.
- Tiros.
- Posesión.
- Goles esperados.
- Córners.
- Secuencias de juego.

### Incertidumbre del fútbol

El resultado de un partido está condicionado por factores difíciles de modelizar, así como por la naturaleza estocástica del deporte.

### Predicción del empate

El empate constituye la categoría más difícil de predecir dentro del problema planteado.

### Optimización de modelos

No se realizó una búsqueda exhaustiva de hiperparámetros ni una exploración completa de arquitecturas más complejas.

---

## 12. Futuras líneas de investigación

Entre las posibles extensiones del proyecto se plantean:

1. Incorporar datos de temporadas más recientes.
2. Ampliar el volumen de datos utilizado para el entrenamiento.
3. Incorporar datos detallados de eventos de partidos.
4. Utilizar fuentes como **StatsBomb Open Data**.
5. Integrar información procedente de casas de apuestas.
6. Explorar técnicas de interpretabilidad como **SHAP** y **LIME**.
7. Optimizar los hiperparámetros de los modelos.
8. Evaluar modelos y arquitecturas adicionales.
9. Desarrollar completamente la herramienta de visualización táctica interactiva.
10. Integrar las predicciones y estadísticas en una interfaz orientada a entrenadores y analistas.

---

## 13. Autoría

Proyecto desarrollado como parte de un Trabajo Fin de Máster del Máster en Inteligencia Artificial de UTAMED.

**Autor:** Valme Lopez

**Curso académico:** 2025-2026

**Dirección:** Manuel Riad

---

## 14. Referencia del dataset

Mathien, H. (s. f.). _European Soccer Database_. Kaggle.

Dataset disponible en:

https://www.kaggle.com/datasets/hugomathien/soccer

---

## 15. Licencia

Este repositorio contiene el código desarrollado para el proyecto académico.

El dataset utilizado pertenece a su correspondiente fuente y no se redistribuye dentro de este repositorio. Para conocer las condiciones de uso y licencia de los datos, debe consultarse la página original de Kaggle:

https://www.kaggle.com/datasets/hugomathien/soccer

La licencia del código de este repositorio puede establecerse de forma independiente según las necesidades del autor.
