# Resumen de decisiones y resultados

Resumen de `01_serie_demanda.ipynb` y `02_pronostico.ipynb`. Los números salen de correr ambos notebooks completos (statsmodels 0.15.0).

## 01 · Serie de demanda

**Qué hace:** filtra el dataset y arma la serie mensual de autos pedidos por línea. Guarda `outputs/serie_mensual.csv` y `outputs/serie_mensual.png`, que usa el notebook 02.

**Dataset:** 2.823 filas, del 06/01/2003 al 31/05/2005, codificado en latin-1. Sin cantidades ≤ 0, sin duplicados, estados válidos.

**Decisiones:**

- **Líneas:** se conservan solo `Classic Cars` y `Vintage Cars` (1.574 de 2.823 filas). Por qué: el caso de Heritage Motors tiene solo esas dos líneas (consigna, contexto del caso).
- **Estados:** se excluyen solo los pedidos `Cancelled`. Por qué: la demanda es lo que el cliente pidió, se haya despachado o no; un cancelado no consumió componentes (lo dijo el profesor en clase + criterio propio). Quedarse solo con `Shipped` subestimaría el final de la serie: en mayo 2005, Vintage daría 98 autos en vez de 867.
- **Unidad:** un auto es una unidad de `QUANTITYORDERED`, no una fila. Por qué: cada fila es una línea de pedido con ~35 autos en promedio; contar filas daría unas 35 veces menos demanda (confirmado por la cátedra).
- **Período:** serie mensual. Por qué: los modelos necesitan un dato en cada período, y la mensual es la primera agregación sin períodos en cero (la semanal tiene 20 y la quincenal 2). Así lo pidió el profesor en clase. Además da 12 factores estacionales en vez de 52, y es como trabaja el apunte de la cátedra.
- **Precio:** se calcula como `SALES / QUANTITYORDERED`. Por qué: `PRICEEACH` está topeado en 100 (1.304 filas no cierran) (confirmado por la cátedra). No afecta la serie, que usa solo cantidades.

**Resultado:**

- **Serie resultante:** 29 meses. Clásicos 33.499, Vintage 20.609, Total 54.108.
- **Lectura:** pico en noviembre (~6.000 autos ambos años); tendencia creciente (2004 +24% vs 2003; ene-may 2005 +42% vs ene-may 2004); Clásicos son 61-63% anual pero 29-78% mes a mes.

## 02 · Pronóstico

**Qué hace:** toma la serie mensual total, compara los modelos de pronóstico de la unidad, elige Winters aditivo y sus constantes, y pronostica los 12 meses siguientes (junio 2005 a mayo 2006). Con eso obtiene la demanda anual, la reparte entre Clásicos y Vintage y calcula el desvío del error de pronóstico. Guarda `outputs/pronostico_12_meses.csv`, `outputs/demanda_anual.csv` y `outputs/pronostico.png`, que usa el notebook 03.

**Decisiones:**

- **Qué serie:** se pronostica el `Total` y se reparte entre líneas con la proporción histórica. Por qué: es la lectura literal de la consigna, que habla de "ventas agregadas" y "proporciones históricas" (consigna, sección 2).
- **Modelo:** Winters. Por qué: la serie tiene tendencia y estacionalidad, y Winters erra unas tres veces menos que los otros modelos (RMSE ~400 contra 1.100-1.450; tabla abajo). Los modelos sin estacionalidad se quedan muy cortos en noviembre (diapositivas 28, 29 y 52).
- **Versión:** aditiva. Por qué: el año creció 24% pero noviembre se mantuvo (6.009 → 5.974). Ajustando solo con 2003-2004, la aditiva se acerca más a ene-may 2005 (8.874 contra 9.716 real; la multiplicativa da 8.402), y pronostica un noviembre 2005 razonable (6.665 contra 8.180 de la multiplicativa). Criterio propio.
- **Constantes:** α = β = γ = 0,1. Por qué: el optimizador da 0, que deja el modelo sin actualizarse y el profesor lo desaconsejó en clase. Entre 0,1, 0,2 y 0,3, el 0,1 tiene menor RMSE (401 / 454 / 515), y la demanda anual casi no cambia respecto de 0 (32.635 contra 32.806). Respaldo: Guía 4, lo dicho en clase y diapositiva 28.
- **Valores iniciales:** los estima statsmodels. Por qué: el método de la diapositiva 56 necesita dos años completos antes de empezar y dejaría solo 5 meses para ajustar. Criterio propio.

**Comparación de modelos** (ventana común ene-2004 a may-2005, constantes 0,1):

| Modelo | RMSE | MAPE % |
|---|---|---|
| Media móvil k=3 / 6 / 12 | 1.352 / 1.436 / 1.124 | 53,3 / 63,3 / 39,0 |
| Suavizado exponencial | 1.195 | 38,0 |
| Holt | 1.243 | 60,0 |
| Winters multiplicativo | 400 | 19,3 |
| Winters aditivo | 411 | 21,6 |

## Resultados (`outputs/demanda_anual.csv`)

| Parámetro | Valor |
|---|---|
| Demanda anual total (jun-2005 a may-2006) | 32.635 autos |
| Clásicos (61,9%) | 20.205 autos |
| Vintage (38,1%) | 12.430 autos |
| Desvío del error mensual (RMSE, 29 meses) | 401 autos |

## Limitaciones

- Solo 29 meses y dos noviembres.
- Posible subestimación: el modelo se quedó corto en ene-may 2005.
- Desvío algo optimista: los valores iniciales se estiman con los mismos datos.
- Reparto con proporción fija subestima el desvío de cada línea (se trata en el paso 4).
