# Proyecto: Patrones de Riesgo Vial en Colombia: Análisis de Fallecidos por Siniestros Viales para la Toma de Decisiones

Análisis de las personas fallecidas en siniestros viales en Colombia (2024–2025), con datos oficiales de la **Agencia Nacional de Seguridad Vial (ANSV)** y el **Observatorio Nacional de Seguridad Vial (ONSV)**, certificados por el DANE.

## Objetivo

Identificar patrones de distribución territorial, tipos de vehículos involucrados y características demográficas de las víctimas, para orientar intervenciones de seguridad vial basadas en evidencia.

## ¿Como importar cuaderno en Google Colab?

1. **Una vez cargado el cuaderno en colab vamos a**
   La parte del medio izquierda, a la penultima opción que dice **Archivo** -> .. esperamos que cargue .. -> **Subimos el contenido que está DENTRO de la carpeta *Data***, y arrastramos el contenido. Una vez hecho esto podemos ejecutar el cuaderno con normalidad.

## ¿Qué se hace?

1. **Limpieza y preparación de datos**
   Se procesan 3 archivos CSV "ensuciados" (`;` como separador y coma decimal):
   - `01.Balance datos preliminares`
   - `02.Comportamiento histórico`
   - `03.Festivos y recesos`

   El pipeline de 7 pasos normaliza columnas, elimina duplicados (311), corrige texto/categorías (similitud difusa), marca valores imposibles como falsos faltantes, imputa faltantes (mediana, moda o derivados de fecha) y resuelve la geografía por código DIVIPOLA. Las 3 funciones clave: `normalizar_clave`, `limpiar_df` e `imputar_faltantes`. Resultado: **104 111 → 102 292 filas** en un dataset único trazable (`siniestros_viales_limpio.csv`).

2. **Selección de 2 modelos**
   Clasificación binaria supervisada (es urbano o rural el siniestro), con ~98 750 registros:
   - **Regresión Logística**: lineal e interpretable.
   - **Random Forest**: no lineal, de conjunto (importancias).
   Ambos se comparan con validación cruzada estratificada de 5 pliegues usando ROC-AUC.

3. **Aplicación**
   `ColumnTransformer` (numericas: mediana + `StandardScaler`; categóricas: "Sin información" + OneHot con `handle_unknown="ignore"`) dentro de `Pipeline` para evitar fuga de datos.

## Resultados

| Modelo | ROC-AUC | Accuracy |
|---|---|---|
| Regresión Logística | **0.7524 ± 0.0019** | **0.6813 ± 0.0044** |
| Random Forest | 0.7299 ± 0.0051 | 0.6672 ± 0.0041 |

Los datos sugieren que la frontera Urbana/Rural es esencialmente lineal: **gana la Regresión Logística**, el modelo más simple e interpretable.

## Hallazgos principales del EDA

- Mayor concentración en **Antioquia, Valle del Cauca, Cundinamarca y Bogotá D.C.** (~57% en zona urbana).
- **Usuarios de moto** son los más vulnerables (~57% de fallecidos); seguidos de peatones (~23%).
- Víctimas mayoritariamente hombres (~81%); edad mediana **41 años**.
- 2024 registra el mayor número de fallecidos del periodo.