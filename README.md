# Laboratorio 4: Construcción y Optimización de Portafolios Multi-Activo

**Curso:** Finanzas Computacionales  
**Programa:** Magíster en Business Analytics — Universidad Adolfo Ibáñez (UAI)  
**Autor:** Francisco Andrés Cisternas García  
**Fecha:** Noviembre 2026  

---

## 📌 Descripción del Proyecto

Este proyecto aborda el diseño, calibración y evaluación comparativa de carteras de inversión institucionales combinando tres grandes familias de modelos de asignación de activos:
1. **Markowitz (Media-Varianza / MVO):** Maximización del ratio de Sharpe y mínima varianza.
2. **Black-Litterman (BL):** Fusión de equilibrio de mercado (CAPM inverso) con visiones cuantitativas relativas y absolutas.
3. **Risk Parity (Equal Risk Contribution / ERC):** Asignación que iguala la contribución marginal de riesgo de cada activo.

El universo de inversión cuenta con **6 activos reales distribuidos en 3 clases de activos**:
- **Renta Variable Defensiva:** Coca-Cola (`KO`) y Johnson & Johnson (`JNJ`).
- **Renta Variable Crecimiento/Tecnología:** Alphabet (`GOOGL`) y NVIDIA (`NVDA`).
- **Renta Fija:** Vanguard Total Bond Market ETF (`BND`).
- **Materias Primas:** United States Oil Fund (`USO`).

El desempeño se compara frente a un **Benchmark Institucional (60/30/10)** bajo un esquema riguroso con partición temporal: **Dentro de Muestra (2018–2023)** para la calibración y **Fuera de Muestra (2023–2026)** para la validación del desempeño real, drawdowns y análisis de robustez (bootstrap y rotación).

---

## 📁 Estructura del Repositorio

- **`Taller_4_Portafolio.ipynb`**: Notebook principal que contiene todo el desarrollo del código, optimizaciones numéricas, pruebas estadísticas y visualizaciones.
- **`reporte_taller4.md`**: Informe ejecutivo de recomendación para el comité de inversiones.
- **`mis_precios_taller4.csv`**: Respaldo local de precios diarios ajustados de los activos (garantiza reproducibilidad sin conexión).
- **`requirements.txt`**: Lista de dependencias del entorno Python.
- **`graficos_taller4.zip`**: Archivo comprimido con todos los gráficos en alta resolución listos para el reporte.
- **`graficos_taller4/`** *(generada al descomprimir el `.zip` o al ejecutar el notebook)*: Carpeta donde se alojan las imágenes enlazadas en `reporte_taller4.md`.

> **⚠️ Nota importante para visualizar los gráficos en `reporte_taller4.md`:**  
> Para que las imágenes insertadas en `reporte_taller4.md` se visualicen correctamente de forma local sin necesidad de ejecutar antes el notebook, **descomprima el archivo `graficos_taller4.zip` en la raíz del proyecto** (de modo que los archivos `.png` queden dentro de la carpeta `graficos_taller4/`). Alternativamente, al ejecutar todas las celdas de `Taller_4_Portafolio.ipynb`, la carpeta `graficos_taller4/` se creará y poblará automáticamente.

---

## 🚀 Cómo Ejecutar el Proyecto

### Opción 1: Entorno Local (VS Code / Jupyter Notebook)

1. **Clonar o descargar** este repositorio en tu equipo.
2. **Crear y activar un entorno virtual** (recomendado):
   ```bash
   python -m venv .venv
   # En Windows:
   .venv\Scripts\activate
   # En macOS/Linux:
   source .venv/bin/activate
   ```
3. **Instalar dependencias**:
   ```bash
   pip install -r requirements.txt
   ```
4. **Abrir y ejecutar el notebook**:
   - Abrir `Taller_4_Portafolio.ipynb` en VS Code o JupyterLab.
   - Seleccionar **"Run All"** (Ejecutar todas las celdas) en orden secuencial.

> **Nota sobre los datos:** El código intenta descargar los precios más recientes vía `yfinance`. Si no hay conexión o la API no responde, utiliza automáticamente el archivo local `mis_precios_taller4.csv`.

### Opción 2: Google Colab

1. Subir `Taller_4_Portafolio.ipynb` y `mis_precios_taller4.csv` a tu entorno de Colab.
2. Instalar las librerías adicionales en la primera celda si no están presentes:
   ```python
   !pip install riskfolio-lib quantstats yfinance
   ```
3. Ejecutar todas las celdas en orden.

---

## 🖼️ Celda Final: Exportación y Descarga de Gráficos

Al final del notebook (`Taller_4_Portafolio.ipynb`) se incluye una celda dedicada exclusivamente a la **descarga y guardado de gráficos**:

### ¿Por qué existe esta celda?
1. **Calidad y automatización para el informe:** En lugar de capturar pantallas manualmente (lo cual degrada la resolución y formato), esta celda exporta automáticamente las 9 figuras del análisis en **alta resolución (300 DPI)** con márgenes ajustados (`bbox_inches="tight"`).
2. **Facilidad de uso en Colab:** Al ejecutarse en Google Colab, detecta el entorno y **descarga automáticamente un archivo `.zip` (`graficos_taller4.zip`)** al navegador del usuario.
3. **Organización local:** En entornos locales, crea la carpeta `graficos_taller4/` y empaqueta el `.zip`, permitiendo enlazar directamente las imágenes al informe ejecutivo (`reporte_taller4.md`).

### Gráficos generados:
1. `01_matriz_correlacion.png`: Matriz de correlación y desacoplamiento de activos.
2. `02_normalidad_retornos.png`: Pruebas de colas pesadas (histograma log y gráfico Q-Q).
3. `03_frontera_eficiente_markowitz.png`: Frontera eficiente MVO con Línea de Mercado de Capitales (LMC).
4. `04_bl_retornos_pesos.png`: Comparación de retornos y pesos de Black-Litterman frente al mercado y Markowitz.
5. `05_bl_sensibilidad_confianza.png`: Curvas de sensibilidad a la convicción de las visiones subjetivas.
6. `06_bl_pie_asignacion.png`: Distribución porcentual del portafolio Black-Litterman.
7. `07_capital_vs_riesgo.png`: Repartición de capital versus contribución porcentual al riesgo.
8. `08_evolucion_riqueza_drawdown.png`: Curvas de riqueza acumulada y perfil de caídas fuera de muestra (2023–2026).
9. `09_bootstrap_sensibilidad_pesos.png`: Resiliencia y estabilidad de pesos ante remuestreos bootstrap.

---

## 🛠️ Principales Librerías Utilizadas

- **`numpy`** y **`pandas`**: Manipulación matricial y series de tiempo financieras.
- **`scipy`**: Optimización numérica (`minimize`) y pruebas estadísticas (Jarque-Bera, normalidad).
- **`riskfolio-lib`**: Optimización avanzada de portafolios (Black-Litterman y Risk Parity).
- **`quantstats`**: Cálculo de métricas financieras institucionales (Sharpe, Sortino, Calmar, Drawdowns).
- **`matplotlib`**: Generación de visualizaciones técnicas.
