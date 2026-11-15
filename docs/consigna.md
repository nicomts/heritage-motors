# Consigna

# Modelos de Pronóstico e Inventario
**Objetivo:** Aplicar modelos de pronóstico de series de tiempo y modelos probabilísticos de inventario con restricciones (EOQ con Nivel de Servicio vs. EOQ con Faltantes Admitidos) para definir una política de reaprovisionamiento de componentes críticos y dimensionar la capacidad de un nuevo almacén.

## Contexto del Caso: Heritage Motors S.A.
**Heritage Motors S.A.** es un fabricante y restaurador argentino con múltiples puntos de venta en el país, especializado en dos líneas de vehículos de alto valor: **Autos Clásicos** (restauraciones y *restomods*) y **Autos Vintage** (vehículos raros y de época). La incertidumbre de la demanda de estos vehículos de nicho, junto con los altos costos de los componentes y los largos *Lead Times* (LT), genera un complejo problema de gestión de inventario.

El objetivo central es diseñar una política de reabastecimiento que optimice el **Costo Total Esperado (CTE)** y al mismo tiempo cumpla con la necesidad de **dimensionar el nuevo almacén central**.

Para la gestión del inventario de **Heritage Motors S.A.**, se han definido varios parámetros clave. El costo fijo por orden de compra se establece en $300. El costo de mantener el inventario se fija en el 20% del Costo Unitario del componente anualmente. En caso de no poder satisfacer la demanda de inmediato, se aplica una política de compensación que consiste en un descuento del 5% sobre el precio del auto al cliente por la demora.

A continuación se listan los componentes de inventario críticos para la producción de estas dos clases de vehículos.

| **Componente (Clase A Potencial)** | **Auto Foco** | **Costo Unitario ($)** | **Uso por Auto (Ratio)** | **Volumen (m³/unidad)** | **Lead Time (L, Semanas)** |
| --- | --- | --- | --- | --- | --- |
| Motor de Alto Rendimiento V8 | Clásico | 9,000 | 1 | 0.8 | 6 |
| Motor de Cilindros en Línea Raro | Vintage | 12,000 | 1 | 0.9 | 12 |
| Carrocería Artesanal de Época | Vintage | 15,000 | 1 | 4.0 | 10 |
| Carrocería Estándar (Fibra) | Clásico | 6,500 | 1 | 3.5 | 4 |
| Transmisión de 5 Velocidades | Ambos | 3,500 | 1 | 0.4 | 4 |
| Sistema de Inyección Electrónica | Clásico | 1,200 | 1 | 0.1 | 3 |
| Set de Carburadores Dobles | Vintage | 900 | 1 | 0.1 | 5 |
| Tapicería de Cuero Premium | Ambos | 4,000 | 1 | 0.5 | 8 |
| Juego de Llantas Vintage Espec. | Vintage | 2,500 | 4 | 0.15 | 5 |
| Llantas Regulares Cromados | Clásico | 400 | 4 | 0.1 | 2 |
| Cubiertas de Alta Gama (Neumáticos) | Ambos | 250 | 4 | 0.1 | 2 |

## 2. Requerimientos del Trabajo Práctico
La primera etapa consiste en el estudio de la demanda. En esta fase se debe comprender la demanda y la incertidumbre. Se partirá del análisis histórico de ventas agregadas para aplicar un **Pronóstico de Series de Tiempo**. Este paso es fundamental para determinar la Demanda Promedio Anual Total y la Desviación Estándar del Error de Pronóstico a nivel de período (ej. semanal).

Posteriormente, se utilizarán los costos unitarios y las proporciones históricas de venta desagregada para realizar un Análisis ABC y XYZ. Esto permitirá identificar los Componentes sobre los cuales recaerá la aplicación de los modelos avanzados de inventario.

La segunda fase se centra en la aplicación de los modelos de inventario, utilizando como base el modelo de Cantidad Económica de Pedido para los Componentes seleccionados en la fase anterior. Para eso se compararán dos enfoques:

- Óptima por Costos
- Basada en un nivel de servicio (tomando ɑ = 0.05).

Finalmente en esta etapa, y como puente a la inversión en infraestructura, se realizará el **Análisis de Restricción de Capacidad**. Se calculará la **Capacidad Mínima de Almacén** requerida para implementar cada política, basándonos en el **Inventario Máximo** de cada componente y su volumen físico.

La etapa conclusiva validará la solidez de los resultados a través de un **Análisis de Sensibilidad**.

Se explorará la sensibilidad al Costo de Agotamiento, analizando cómo una variación de 30% por exceso o por defecto en este parámetro impacta el CTE de la Política A. Además, se medirá la **Sensibilidad al Riesgo**, analizando el efecto que tiene un aumento del **15%** en la incertidumbre sobre el Stock de Seguridad y el CTE de ambas políticas, midiendo su vulnerabilidad ante una mayor volatilidad del mercado.

El informe culminará con las **Recomendaciones Finales**: una recomendación formal sobre la **Política de Inventario** a implementar (A o B), justificada por el análisis de costos y robustez, y una propuesta concreta para el **Dimensionamiento del Almacén a Construir**.

## 3. Estructura de Presentación
El trabajo debe presentarse como un **Informe Técnico-Ejecutivo** siguiendo la estructura detallada en el punto anterior (Resumen Ejecutivo, Análisis Preliminar, Modelos y Sensibilidad). La calidad de las tablas comparativas y la claridad de la argumentación son criterios clave de evaluación.

# Rúbrica de Evaluación

## Criterios y Rúbrica de Evaluación del Trabajo Práctico
| **Sección del Informe** | **Peso (Puntos)** | **Criterios Clave de Evaluación** |
| --- | --- | --- |
| **I. Modelado y Preparación** | **25 Puntos** | **Pronóstico, ABC, Costos y Riesgo** |
| Pronóstico (Series de Tiempo) | 8 | Aplicación justificada del modelo. Obtención precisa de media y desviación estándar. |
| Clasificación ABC y XYZ | 7 | Correcto cálculo del Valor de Uso Anual para todos los componentes. Identificación precisa de las clases. |
| Parámetros de Riesgo | 10 | Cálculo preciso y justificado del **Costo de Agotamiento** y la **Desviación Estándar** para cada Componente. |
| **II. Aplicación de Modelos y Optimización** | **45 Puntos** | **Comparación de Políticas y Restricción** |
| Política A (Hadley/Within) | 15 | Correcta aplicación del algoritmo o método para encontrar la solución **óptima por costos**. |
| Política B (NS=95%) | 12 | Correcto cálculo de **Q**, **SS** y **CTE**. Uso adecuado del factor de seguridad. |
| Nivel de Servicio Resultante | 8 | Correcto cálculo del **Nivel de Servicio** que se alcanza con la política óptima por costos. |
| Restricción de Capacidad | 10 | Correcto cálculo de la **Capacidad Mínima Requerida**. Análisis de la factibilidad de las políticas frente a esta restricción. |
| **III. Análisis y Conclusiones** | **30 Puntos** | **Sensibilidad y Recomendación Estratégica** |
| Sensibilidad a Costos | 10 | Análisis claro y representación gráfica/tabular de cómo las variaciones afectan el CTE y el Nivel de Servicio en ambas políticas. |
| Sensibilidad a Riesgo | 8 | Demostración de cómo la incertidumbre obliga a aumentar el **Stock de Seguridad (SS)**. |
| Recomendación de Política | 7 | Justificación sólida de la política recomendada (A vs. B). |
| Recomendación de Almacén | 5 | Propuesta de **dimensionamiento del almacén**. |

## Rúbrica Detallada para Evaluación
| **Criterio de Logro** | **Puntaje** | **Descripción del Desempeño** |
| --- | --- | --- |
| **Dominio Excepcional** | 90 - 100% | El trabajo demuestra dominio técnico y pensamiento estratégico. Todos los cálculos son precisos. La comparación de modelos y el análisis de sensibilidad son rigurosos y las conclusiones son perfectamente justificadas. El informe es profesional y fácil de seguir. |
| **Alto Logro** | 75 - 89% | El trabajo es técnicamente sólido, con pocos errores menores en los cálculos. La aplicación de los modelos (EOQ, Hadley/Within) es correcta. La justificación de la elección de la política es clara, pero el análisis de sensibilidad podría ser más profundo. |
| **Logro Adecuado** | 60 - 74% | Se aplicaron correctamente la mayoría de los modelos (ej. el pronóstico y el EOQ son correctos, pero la aplicación del algoritmo de Hadley/Within o la restricción de capacidad tiene errores conceptuales o de cálculo). Las conclusiones son superficiales o no están totalmente respaldadas por el análisis. |
| **Desempeño Básico** | 45 - 59% | Se aplicaron incorrectamente modelos fundamentales (ej. falla en cálculos). Faltan secciones clave (ej. el análisis de sensibilidad es omitido). |
| **No Satisfactorio** | < 45% | Falla en el modelado inicial (pronóstico o ABC) o aplicación incorrecta de los modelos EOQ/probabilísticos. El trabajo es incompleto y no demuestra comprensión de los conceptos centrales de la Investigación Operativa. |

# Resumen Ejecutivo

# Análisis Preliminar

# Modelos

# Sensibilidad

# Conclusiones


