# Clasificación de fraude en apertura de cuentas bancarias (BAF, NeurIPS 2022)

Trabajo Práctico de **Programación Estadística** — Maestría en Econometría, Universidad Torcuato Di Tella.

**Autor:** Augusto Anton Baragiotta
**Fecha:** Septiembre 2026

## Descripción

Este proyecto implementa un modelo de **regresión logística** para predecir la probabilidad de que una solicitud de apertura de cuenta bancaria online sea fraudulenta, minimizando los falsos positivos (cuentas genuinas catalogadas como fraudulentas).

El trabajo cubre el flujo completo: análisis exploratorio, tratamiento de valores faltantes, selección de variables por Information Value (IV), binning con Weight of Evidence (WoE), modelado, evaluación sobre datos no vistos, optimización del umbral de clasificación y comparación con una estrategia de undersampling.

## Datos

Se utiliza el **Bank Account Fraud (BAF) Dataset Suite** (Jesus et al., NeurIPS 2022), publicado por Feedzai en Kaggle. En particular, se trabaja con el archivo `Base.csv`.

- **Observaciones:** 1.000.000 de solicitudes
- **Tasa de fraude:** ~1,1 % (dataset fuertemente desbalanceado)
- **Variable objetivo:** `fraud_bool` (1 = fraude, 0 = legítimo)
- **Dimensión temporal:** `month` (0 a 7), meses simulados pensados para estudiar drift

> El dataset **no se incluye en el repositorio** por su tamaño. Descargalo desde Kaggle (*Bank Account Fraud Dataset Suite (NeurIPS 2022)*) y ubicá `Base.csv` en la carpeta `data/`.

## Metodología

1. **Análisis exploratorio:** cobertura temporal, tipos de variables y desbalance de clases.
2. **Valores faltantes:** varias variables codifican la ausencia de dato con `-1`. Se recodificaron como `NA` explícito, ya que `woebin()` asigna a los faltantes un bin propio y así el modelo puede capturar si no informar un dato es en sí mismo una señal de fraude.
3. **Selección de variables por IV:** se descartaron las variables con IV bajo según los criterios habituales de la industria.
4. **Split train/test estratificado (70/30):** se preservó la proporción de fraude en ambos conjuntos. El test se evalúa una sola vez, al final.
5. **Binning y WoE:** los bins se ajustan únicamente con el set de entrenamiento y se aplican luego al test, para evitar fuga de información.
6. **Modelo:** regresión logística (`glm`, familia binomial) sobre las variables transformadas a WoE.
7. **Evaluación:** curva ROC y AUC, curva Precision-Recall, matriz de confusión y métricas derivadas (accuracy, precisión, sensibilidad, especificidad, F1, balanced accuracy).
8. **Umbral de clasificación:** dado el desbalance, el umbral clásico de 0,5 clasifica casi todo como no fraude. Se buscó el umbral que maximiza el F1 (0,11).
9. **Undersampling:** se comparó el modelo original contra uno entrenado con clases balanceadas 50/50.

## Resultados

Completá esta sección con los valores que obtengas al correr el script:

| Métrica | Modelo original | Undersampling |
|---|---|---|
| AUC ROC | _completar_ | _completar_ |
| AUC PR | _completar_ | — |
| F1 (umbral 0,11) | _completar_ | — |

El undersampling mejoró el AUC apenas en 0,001 respecto del modelo original.

## Estructura del repositorio

```
.
├── README.md
├── data/
│   └── Base.csv              # no incluido (descargar de Kaggle)
├── R/
│   └── trabajo_practico.R    # script completo del análisis
├── images/                   # gráficos usados en el informe
└── informe/
    └── informe.tex           # informe en LaTeX
```

## Requisitos

- R (>= 4.1)
- Paquetes: `tidyverse`, `patchwork`, `scorecard`, `pROC`, `PRROC`, `glmnet`, `rsample`

Instalación:

```r
install.packages(c("tidyverse", "patchwork", "scorecard",
                   "pROC", "PRROC", "glmnet", "rsample"))
```

## Cómo reproducir

1. Cloná el repositorio:
```bash
   git clone https://github.com/<usuario>/<repositorio>.git
```
2. Descargá `Base.csv` desde Kaggle y guardalo en `data/`.
3. Abrí `R/trabajo_practico.R` en RStudio, o ejecutalo desde la terminal:
```bash
   Rscript R/trabajo_practico.R
```
4. El script usa la semilla `261395`, por lo que el split y los resultados son reproducibles.

## Referencias

- Jesus, S., Pombal, J., Alves, D., Cruz, A., Saleiro, P., Ribeiro, R., Gama, J., Bizarro, P. (2022). *Turning the Tables: Biased, Imbalanced, Dynamic Tabular Datasets for ML Evaluation*. NeurIPS 2022 Datasets and Benchmarks Track.
