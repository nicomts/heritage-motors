# TP Integrador de Investigación Operativa: Heritage Motors

Pronóstico de demanda y políticas de inventario para los componentes críticos de Heritage Motors S.A. Investigación Operativa, UTN FRCU.

Consigna del trabajo: [docs/consigna.md](docs/consigna.md).

Resumen de decisiones y resultados de los notebooks: [docs/resumen.md](docs/resumen.md).

## Estructura

```
.
├── docs/
│   ├── consigna.md                 # consigna del TP
│   └── resumen.md                  # resumen de decisiones y resultados
├── data/
│   └── sales_data_sample_raw.csv   # dataset original, sin modificar
├── outputs/                        # lo generan los notebooks; no editar a mano
├── 01_serie_demanda.ipynb          # serie mensual de demanda
├── 02_pronostico.ipynb             # pronóstico de la demanda
├── 03_abc_parametros.ipynb         # ABC/XYZ y parámetros de riesgo
├── 04_politicas.ipynb              # políticas A y B, almacén y sensibilidad (pendiente)
└── README.md
```

Cada notebook guarda sus resultados en `outputs/` y el siguiente los lee de ahí, así que se corren en orden: 01, 02, 03, 04.

## Requisitos

Python 3.10 o más nuevo, que es lo que pide `statsmodels`. Para ver qué versión tenés:

```bash
python3 --version
```


## Instalación (una sola vez)

1. Cloná el repo y entrá a la carpeta:

   ```bash
   git clone https://github.com/nicomts/heritage-motors.git
   cd heritage-motors
   ```

2. Verificá que esté el dataset en `data/sales_data_sample_raw.csv`. Se versiona junto con el repo; si falta, no se puede correr nada.

3. Creá un entorno virtual y activalo:

   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   ```

   En Windows, la activación es `.venv\Scripts\activate`.

4. Instalá las librerías:

   ```bash
   pip install pandas matplotlib statsmodels jupyterlab
   ```


## Ejecución

Cada vez que vuelvas a trabajar, activá el entorno primero, desde la carpeta del repo:

```bash
source .venv/bin/activate
```

### Con Jupyter Lab

1. Ejecutá `jupyter lab`. Se abre en el navegador.
2. Abrí `01_serie_demanda.ipynb`.
3. Menú **Kernel → Restart Kernel and Run All Cells**.
4. Repetí con el notebook siguiente.

### Con VS Code

1. Instalá las extensiones **Python** y **Jupyter**.
2. Abrí la carpeta del repo y después el notebook.
3. Arriba a la derecha, en **Select Kernel**, elegí el Python de `.venv`.
4. Tocá **Restart** y después **Run All**.

Si una celda con `print` queda en blanco, volvé a hacer **Restart** y **Run All**: es una falla de VS Code al mostrar la salida, no del notebook.

## Verificación

En `01_serie_demanda.ipynb`, la subsección **Verificación** de la sección 6 muestra:

- La línea `Meses: 29`.
- Una tabla con los autos de todo el período: `Clasicos 33499`, `Vintage 20609` y `Total 54108`.

Además, en `outputs/` aparecen `serie_mensual.csv` y `serie_mensual.png`.

En `02_pronostico.ipynb`:

- La sección 2 muestra `Meses: 29 | desde 2003-01 hasta 2005-05`. Si da error al leer `outputs/serie_mensual.csv`, falta correr el 01. Si da error al importar `statsmodels`, falta instalarla en el entorno.
- La sección 7 muestra `Demanda anual total: 32634 autos` y `Desvío del error mensual: 401 autos`, con los desvíos por línea: `Clásicos: 320 autos` y `Vintage: 152 autos`.

Además, en `outputs/` aparecen `pronostico_12_meses.csv`, `demanda_anual.csv` y `pronostico.png`.

Estos números se obtuvieron con statsmodels 0.15.0. Otra versión puede dar diferencias chicas, porque la librería estima los valores iniciales del modelo con un optimizador.

En `03_abc_parametros.ipynb`:

- La sección 1 muestra `Meses de la serie: 29`. Si da error al leer `outputs/demanda_anual.csv`, falta correr el 02.
- La sección 5 muestra `Componentes clase A: 6 | clases: AZ`.
- La sección 6.2 muestra `Autos: Clásicos 33499 | Vintage 20609`, los mismos totales que el 01.
- La sección 6.5 muestra `True` en la columna `¿cumple?` para los seis componentes.

Además, en `outputs/` aparecen `componentes.csv`, `abc_componentes.png` y `parametros.csv`.

## Reglas de trabajo

- Antes de copiar un número al informe, reiniciar el kernel y correr todo el notebook. Un notebook recuerda variables de celdas que ya se borraron o se corrieron en otro orden.
- No editar a mano los archivos de `outputs/`: se regeneran al correr los notebooks.
