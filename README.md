# E4 · Concellos de la provincia de A Coruña

**Sistemas de Big Data · UD1 · Tema 3 · Introducción al web scraping** — Ejercicio individual

Scraping de la página de Wikipedia con la población de los 93 concellos de la provincia de A Coruña: se descarga de forma responsable, se extrae la tabla con BeautifulSoup, se limpia y valida con la propia página y se responden cuatro preguntas sobre cómo se reparte la población.

- **Fuente:** [List of municipalities in A Coruña](https://en.wikipedia.org/wiki/List_of_municipalities_in_A_Coru%C3%B1a) (Wikipedia en inglés).
- **Licencia de los datos:** CC BY-SA 4.0 (atribución y compartir igual). El CSV resultante es un dato derivado y se distribuye bajo la misma licencia.
- **Datos personales:** ninguno; solo cifras agregadas por concello.

## Estructura del repositorio

```
.
├── README.md
├── requirements.txt          # dependencias con versiones fijadas
├── notebooks/
│   └── E4_concellos.ipynb    # el ejercicio completo, ejecutado y con salidas visibles
├── output/
│   ├── top10_concellos.png          # gráfica de la pregunta 1
│   ├── distribucion_poblacion.png   # gráfica de la pregunta 2
│   └── poblacion_acumulada.png      # gráfica de la pregunta 4
└── datos/
    ├── raw/
    │   └── concellos_coruna.html   # HTML original de la página, en bytes y sin modificar
    └── clean/
        └── concellos_coruna.csv    # resultado limpio: concello, poblacion (UTF-8)
```

- `datos/raw/` guarda la página tal como llegó, para poder repetir la extracción sin volver a descargar.
- `datos/clean/` guarda el resultado final tras la limpieza.
- `output/` guarda las gráficas en PNG (además de mostrarse en el notebook).

## Cómo ejecutarlo

### 1. Clonar el repositorio

```bash
git clone https://github.com/RAnteloR/Task-E4-Scraping-Concellos.git
cd Task-E4-Scraping-Concellos
```

### 2. Crear el entorno e instalar las dependencias

Requiere **Python 3.12** (probado con 3.12 en Linux/WSL).

```bash
python3.12 -m venv .venv
source .venv/bin/activate        # en Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

### 3. Ejecutar el notebook

Con el entorno activado, desde Jupyter, VS Code o por línea de comandos:

```bash
cd notebooks
jupyter nbconvert --to notebook --execute --inplace E4_concellos.ipynb
```

El notebook se puede ejecutar de principio a fin con la carpeta `datos/` vacía: crea `datos/raw/`, `datos/clean/` y `output/` si no existen y descarga la página la primera vez. Si `datos/raw/concellos_coruna.html` ya existe, **no se vuelve a descargar**.

> Las rutas del notebook son relativas (`../datos/...`), así que debe ejecutarse con `notebooks/` como directorio de trabajo.

## Qué hace el notebook

| Sección | Qué hace |
|---|---|
| **4.1 Antes de descargar** | Comprueba con `RobotFileParser` que `robots.txt` permite descargar la página. Explica la licencia (CC BY-SA 4.0) y que la página no contiene datos personales. |
| **4.2 Descarga** | Descarga con `requests` usando un User-Agent propio (nombre del proyecto y correo) y guarda el HTML en bytes en `datos/raw/`. No descarga si el fichero ya existe. |
| **4.3 Extracción** | Localiza la tabla **por el contenido de su cabecera** (`Population…`), no por su posición, y la convierte en un DataFrame con concello y población. |
| **4.4 Limpieza** | Comprueba que todos los valores tienen el mismo formato y los convierte a entero. |
| **4.5 Validación** | Comprueba que hay 93 concellos sin repetidos, compara la suma con el total de población que dice el texto de la página y contrasta el resultado con `pd.read_html`. |
| **4.6 Preguntas** | Responde con código y una frase de interpretación a las cuatro preguntas, con tres gráficas: top 10 de concellos, distribución de la población (escala logarítmica) y población acumulada, guardadas en `output/`. |
| **4.7 Guardar** | Escribe el CSV limpio en `datos/clean/` en UTF-8. |
| **Uso de IA** | Indica qué herramientas de IA se usaron y para qué. |

## Decisiones y resultados destacados

- **robots.txt:** `RobotFileParser.read()` usa el User-Agent por defecto de urllib, que Wikipedia bloquea (403), y por eso devolvía «no permitido». Se descarga `robots.txt` con `requests` y el User-Agent propio y se le pasa al parser con `parse()`.
- **Valor con otro formato:** Boimorto aparece como `1.801` (punto como separador de miles) mientras el resto usa coma (`1,234`). Se interpreta como 1801. La corrección se confirma porque la suma de la tabla (1.128.320) coincide con el total de la página.
- **`pd.read_html`:** lee la columna como `float` y convierte Boimorto en 1,801; es la única diferencia con la extracción manual.
- **Preguntas:**
  1. Más poblados: A Coruña (249.261), Santiago de Compostela, Ferrol, Narón y Oleiros.
  2. 43 concellos (46,2 %) tienen menos de 5.000 habitantes.
  3. Los tres más poblados reúnen el 36,6 % de la población provincial.
  4. Hacen falta 8 concellos para superar la mitad de la población.

## Uso de inteligencia artificial

Se ha usado Claude Code (Anthropic) como ayuda en:

- **Revisión del código:** localizar errores al ejecutarlo, por ejemplo que `RobotFileParser.read()` devolvía «no permitido» por el bloqueo del User-Agent por defecto de urllib, y proponer la corrección.
- **Documentación:** redacción del README y repaso de las frases de interpretación para que coincidieran con los resultados reales.

Las decisiones de limpieza (el valor `1.801` de Boimorto) se han revisado y justificado por el autor, y el notebook se ha ejecutado completo, con la carpeta `datos/` vacía, para comprobar los resultados.

## Entrega

En Moodle se entrega el **notebook ejecutado, con las salidas visibles**. Debe poder ejecutarse de principio a fin con la carpeta `datos/` vacía.
