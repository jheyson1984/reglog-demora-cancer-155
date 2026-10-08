# Regresión logística: demora mayor a 90 días entre inicio de síntomas y consulta en cáncer de mama y cuello uterino (Colombia, 2025)

Curso: Introducción a Machine Learning (604027), Especialización en Analítica y Ciencia de Datos, Universidad de Cundinamarca.
Autor: Jheyson Morales.

## Objetivo

Clasificar con regresión logística si una mujer notificada al SIVIGILA por el evento 155 tardó más de 90 días entre el inicio de síntomas y la consulta (1 = sí, 0 = no), a partir de departamento de residencia, edad, régimen de afiliación, estrato y área de residencia.

## Datos

- Fuente: Instituto Nacional de Salud (INS), Portal SIVIGILA, búsqueda de microdatos.
- Evento 155 (cáncer de la mama y cuello uterino), año 2025: 18.717 registros y 69 columnas. Base nominal depurada, sin datos de identificación personal.
- La base no se incluye en el repositorio. Ver `data/LEEME_DATOS.md` para descargarla.

## Estructura del repositorio

```
.
├── notebooks/
│   └── RegLog_demora_cancer_155.ipynb   # cuaderno con todo el flujo (Google Colab)
├── data/
│   └── LEEME_DATOS.md                   # cómo obtener la base
├── requirements.txt
└── README.md
```

## Cómo ejecutar

1. Descargar la base como se indica en `data/LEEME_DATOS.md`.
2. Abrir el cuaderno con el botón "Open in Colab".
3. Ejecutar la primera celda y subir el archivo `Datos_2025_155.xlsx`.
4. Ejecutar las demás celdas en orden.

## Flujo del análisis

1. **Carga y limpieza.** Demora = fecha de consulta menos fecha de inicio de síntomas. Se excluyen 1.391 registros: demora mayor a 365 días, estrato vacío y residencia en el exterior. Quedan 17.326.
2. **Variable objetivo.** `demorada` = 1 si la demora supera 90 días. Punto de corte tomado de Richards et al. (1999). Resultado: 3.958 casos positivos (22,8 %); clases desbalanceadas.
3. **Predictores y división.** Variables indicadoras con referencia en Bogotá, régimen contributivo y cabecera municipal. División 75/25 con `random_state=42` y `stratify=y`, para conservar el 22,8 % en ambos grupos.
4. **Escalado.** `StandardScaler` sobre edad y estrato, ajustado solo con el grupo de entrenamiento.
5. **Entrenamiento.** Modelo 1: `LogisticRegression(max_iter=1000)`. Modelo 2: igual, con `class_weight="balanced"` para manejar el desbalance. Los demás parámetros quedan con los valores por defecto de scikit-learn.
6. **Evaluación.** Matriz de confusión, accuracy, precision, recall y F1; probabilidades con `predict_proba`; cambio de umbral de 0,50 a 0,25; histograma de probabilidades.
7. **Interpretación.** Odds ratio de los predictores.

## Resultados principales (grupo de prueba, 4.332 casos, 990 demoradas)

| Versión | Accuracy | Precision | Recall | Demoradas encontradas | Falsas alarmas |
|---|---|---|---|---|---|
| Modelo 1, umbral 0,50 | 0,773 | 0,53 | 0,07 | 65 | 57 |
| Modelo 1, umbral 0,25 | 0,684 | 0,36 | 0,47 | 468 | 847 |
| Modelo 2, `class_weight="balanced"` | 0,649 | 0,34 | 0,55 | 546 | 1.078 |

Precision y recall corresponden a la clase "demorada". Responder "no" a todos los casos daría un accuracy de 0,771.

Odds ratio (modelo 1): régimen subsidiado frente a contributivo 1,25; rural disperso frente a cabecera 1,37; centro poblado frente a cabecera 1,08; edad 1,07 y estrato 0,93 por cada desviación estándar.

## Limitaciones

- Los cinco predictores separan poco a las dos clases: las probabilidades de demoradas y no demoradas se superponen.
- El punto de corte de 90 días proviene de evidencia en cáncer de mama; la base incluye también cáncer de cuello uterino.
- scikit-learn aplica regularización por defecto, por lo que los odds ratio son aproximados.

## Referencia

Richards, M. A., Westcombe, A. M., Love, S. B., Littlejohns, P., y Ramirez, A. J. (1999). Influence of delay on survival in patients with breast cancer: a systematic review. *The Lancet, 353*(9159), 1119-1126.
