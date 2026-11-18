# Resumen de decisiones y resultados

Resumen de `01_serie_demanda.ipynb`, `02_pronostico.ipynb` y `03_abc_parametros.ipynb`. Los números salen de correr los tres notebooks completos (statsmodels 0.15.0).

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

**Qué hace:** toma la serie mensual total, compara los modelos de pronóstico de la unidad, elige Winters aditivo y sus constantes, y pronostica los 12 meses siguientes (junio 2005 a mayo 2006). Con eso obtiene la demanda anual, la reparte entre Clásicos y Vintage y calcula el desvío del error de pronóstico, del total y de cada línea. Guarda `outputs/pronostico_12_meses.csv`, `outputs/demanda_anual.csv` y `outputs/pronostico.png`, que usa el notebook 03.

**Decisiones:**

- **Qué serie:** se pronostica el `Total` y se reparte entre líneas con la proporción histórica. Por qué: es la lectura literal de la consigna, que habla de "ventas agregadas" y "proporciones históricas" (consigna, sección 2).
- **Modelo:** Winters. Por qué: la serie tiene tendencia y estacionalidad, y Winters erra unas tres veces menos que los otros modelos (RMSE ~400 contra 1.100-1.450; tabla abajo). Los modelos sin estacionalidad se quedan muy cortos en noviembre (diapositivas 28, 29 y 52).
- **Versión:** aditiva. Por qué: el año creció 24% pero noviembre se mantuvo (6.009 → 5.974). Ajustando solo con 2003-2004, la aditiva se acerca más a ene-may 2005 (8.874 contra 9.716 real; la multiplicativa da 8.402), y pronostica un noviembre 2005 razonable (6.665 contra 8.180 de la multiplicativa). Criterio propio.
- **Constantes:** α = β = γ = 0,1. Por qué: el optimizador da 0, que deja el modelo sin actualizarse y el profesor lo desaconsejó en clase. Entre 0,1, 0,2 y 0,3, el 0,1 tiene menor RMSE (401 / 454 / 515), y la demanda anual casi no cambia respecto de 0 (32.635 contra 32.806). Respaldo: Guía 4, lo dicho en clase y diapositiva 28.
- **Valores iniciales:** los estima statsmodels. Por qué: el método de la diapositiva 56 necesita dos años completos antes de empezar y dejaría solo 5 meses para ajustar. Criterio propio.
- **Desvío de cada línea:** RMSE de la venta real de la línea contra su parte del pronóstico (pronóstico total × proporción). Por qué: así el desvío incluye que la mezcla entre líneas cambia mes a mes (Clásicos va de 29% a 78%), algo que el reparto fijo no ve. Criterio propio. El 03 lo usa para el desvío de cada componente.

**Comparación de modelos** (ventana común ene-2004 a may-2005, constantes 0,1):

| Modelo | RMSE | MAPE % |
|---|---|---|
| Media móvil k=3 / 6 / 12 | 1.352 / 1.436 / 1.124 | 53,3 / 63,3 / 39,0 |
| Suavizado exponencial | 1.195 | 38,0 |
| Holt | 1.243 | 60,0 |
| Winters multiplicativo | 400 | 19,3 |
| Winters aditivo | 411 | 21,6 |

**Resultado** (`outputs/demanda_anual.csv`):

| Parámetro | Valor |
|---|---|
| Demanda anual total (jun-2005 a may-2006) | 32.635 autos |
| Clásicos (61,9%) | 20.205 autos |
| Vintage (38,1%) | 12.430 autos |
| Desvío del error mensual, total (RMSE, 29 meses) | 401 autos |
| Desvío del error mensual, Clásicos | 320 autos |
| Desvío del error mensual, Vintage | 152 autos |

**Limitaciones:**

- Solo 29 meses y dos noviembres.
- Posible subestimación: el modelo se quedó corto en ene-may 2005.
- Desvío algo optimista: los valores iniciales se estiman con los mismos datos.
- Reparto con proporción fija: el pronóstico de cada línea erra más que el del total; por eso el desvío de cada línea se mide contra su propia venta real.

## 03 · ABC, XYZ y parámetros de riesgo

**Qué hace:** clasifica los 11 componentes con ABC (por valor de uso anual) y XYZ (por coeficiente de variación), y para los clase A calcula los parámetros de riesgo que usan las políticas. Lee `outputs/serie_mensual.csv`, `outputs/demanda_anual.csv` y el dataset crudo, para el precio de los autos. Guarda `outputs/componentes.csv`, `outputs/abc_componentes.png` y `outputs/parametros.csv`, que usa el notebook 04.

**Decisiones:**

- **Demanda del componente:** demanda anual de la línea que lo usa × uso por auto; los componentes de "Ambos" usan el total. Por qué: la consigna pide usar los costos unitarios y las proporciones históricas de venta desagregada (consigna, sección 2).
- **Cortes ABC:** A hasta el 80% del valor acumulado, B hasta el 95%, C el resto. Por qué: regla de Pareto (respaldo de la cátedra: Miguel Miranda, *Sistemas de optimización de stocks*, bibliografía de la materia). Con un corte de 75% la clase A no cambia.
- **Cortes XYZ:** X con CV < 20%, Y entre 20% y 50%, Z > 50%, con la serie mensual de la línea de cada componente. Por qué: el 20% es el límite del apunte para tratar la demanda como determinística (44/73); el 50% es criterio propio.
- **Qué componentes siguen:** solo los clase A pasan a las políticas A y B. Por qué: la consigna dice que el ABC y el XYZ sirven para identificar los componentes sobre los que se aplican los modelos (consigna, sección 2).
- **K y c1:** K = $300 por pedido y c1 = 20% del costo unitario por año (consigna, contexto del caso). Todo va por año.
- **c2:** 5% del precio del auto (compensación de la consigna). El precio es `SALES / QUANTITYORDERED × 1000`, promedio por línea, sin cancelados (indicado por la cátedra; mismos autos que el 01). Criterio propio: la tapicería usa el precio promedio de las dos líneas, y las llantas vintage dividen el c2 por 4, porque el descuento es por auto.
- **Desvío del componente:** desvío del error de pronóstico de su línea (del 02) × uso por auto; "Ambos" usa el del total. Por qué: el RMSE estima la desviación estándar del error de pronóstico (*Modelos de Pronósticos*, diapositiva 15). No se usa el CV, porque incluye el pico de noviembre, que el pronóstico ya anticipa.
- **Demanda en el lead time:** normal, con E[x] = d · L y σx = σ · √L (apunte, 65/73). El lead time se pasa a meses con semanas × 12 / 52. Criterio propio: el apunte redondea a 4 semanas por mes (66/73).
- **Chequeo de la política A:** Hadley-Whitin converge si q̃ ≥ q̂ (apunte, 52/73). Los seis componentes cumplen.

**Resultado ABC/XYZ** (`outputs/componentes.csv`, `outputs/abc_componentes.png`):

| Clase | Componentes | % del valor |
|---|---|---|
| A | Carrocería artesanal, motor V8, motor en línea, carrocería estándar, tapicería, llantas vintage | 81% |
| B | Transmisión, cubiertas, llantas regulares | 16% |
| C | Inyección, carburadores | 3% |

- **XYZ:** los 11 componentes son Z (CV mensual: Clásicos 75,0%, Vintage 69,6%, Total 71,7%). Con el criterio por mes del apunte (44/73), la demanda es probabilística no estacionaria.
- **Precio promedio del auto:** Clásico $115.238, Vintage $90.499, ambas líneas $105.815.

**Resultado parámetros** (`outputs/parametros.csv`; K = $300 para todos):

| Componente | D anual | c1 | c2 | σ mensual | L (meses) | E[x] | σx |
|---|---|---|---|---|---|---|---|
| Carrocería Artesanal de Época | 12.430 | 3.000 | 4.525 | 152 | 2,31 | 2.390 | 231 |
| Motor de Alto Rendimiento V8 | 20.205 | 1.800 | 5.762 | 320 | 1,38 | 2.331 | 377 |
| Motor de Cilindros en Línea Raro | 12.430 | 2.400 | 4.525 | 152 | 2,77 | 2.868 | 253 |
| Carrocería Estándar (Fibra) | 20.205 | 1.300 | 5.762 | 320 | 0,92 | 1.554 | 308 |
| Tapicería de Cuero Premium | 32.635 | 800 | 5.291 | 401 | 1,85 | 5.021 | 545 |
| Juego de Llantas Vintage Espec. (×4) | 49.720 | 500 | 1.131 | 608 | 1,15 | 4.781 | 653 |

**Limitaciones:**

- La transmisión queda en B por poco: 10,2% del valor, contra 11,1% de las llantas vintage.
- El reparto con proporción fija define el valor de cada componente; si cambia la mezcla, cambian los valores. El total pronosticado no afecta las clases.
- Los cortes de 80% y 95% son convenciones (Miranda); con 75% el resultado es el mismo.
- No se puede comprobar con 29 meses que la demanda en el lead time sea normal.
- σx = σ · √L supone que los errores de meses distintos son independientes.
- Con 4 semanas por mes, como el apunte, L y E[x] serían un 8% más altos.
- Si una sola llanta faltante ya demorara el auto, el c2 de las llantas vintage sería 4 veces mayor.
