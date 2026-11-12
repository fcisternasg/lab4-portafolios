# Reporte Taller 4 — Se recomienda Black-Litterman por liderar el desempeño ajustado por riesgo (Sharpe 2,04) con baja rotación; se descartan Markowitz por concentración y Risk Parity por menor retorno

**Autor(es):** Francisco Andrés Cisternas García · **Fecha de corte de los datos:** 2023-01-04 (entrenamiento) / 2026-11-10 (total) · **Notebook:** `Taller_4_Portafolio.ipynb` https://github.com/fcisternasg/lab4-portafolios

## 1. Recomendación

Se recomienda implementar el portafolio **Black-Litterman** (calibrado con nivel de confianza base $c = 1,0$ y $\tau = 0,05$), por alcanzar la mayor eficiencia ajustada por riesgo fuera de muestra entre los modelos optimizados (**Sharpe 2,04** y **Sortino 3,19**). Se descarta **Markowitz** pese a su mayor retorno nominal, por hiperconcentrar el capital en pocos activos y sufrir una severa inestabilidad ante remuestreos (rotación de 39,8%), y se descarta **Risk Parity** por sacrificar excesivo retorno (13,63% vs. 18,76%) y eficiencia (Sharpe 1,90) al sobreponderar renta fija. La asignación recomendada de Black-Litterman calibrada dentro de muestra (2018–2023, 1.260 días) se estructura en:
- **43,8% en Renta Fija (`BND`)**: Actúa como ancla de baja volatilidad y descorrelación.
- **48,2% en Renta Variable**: Diversificada entre activos defensivos (`JNJ` 18,3%, `KO` 7,6%) y crecimiento tecnológico (`GOOGL` 14,6%, `NVDA` 7,7%).
- **8,0% en Materias Primas (`USO`)**: Cobertura adicional mediante exposición a petróleo.

Esta cartera logra fuera de muestra un retorno anualizado de **18,76%**, volatilidad de **8,22%** y un **Drawdown máximo acotado a -8,20%** (vs. -11,00% del Benchmark), con rotación mínima (**3,7%**) y **3,8 activos efectivos**.

Tabla de pesos del portafolio recomendado (Black-Litterman):

| Activo | Clase | Peso |
|---|---|---|
| **KO** (Coca-Cola) | Renta Variable Defensiva | 7,6% |
| **JNJ** (Johnson & Johnson) | Renta Variable Defensiva | 18,3% |
| **GOOGL** (Alphabet) | Renta Variable Crecimiento | 14,6% |
| **NVDA** (NVIDIA) | Renta Variable Crecimiento | 7,7% |
| **BND** (Vanguard Bond ETF) | Renta Fija | 43,8% |
| **USO** (US Oil Fund) | Materias Primas | 8,0% |

## 2. Universo y datos

- **Activos y clases:** 6 activos de 3 clases sobre el Dow Jones (`^DJI`) más ETFs de bonos y commodities:
  - *Renta variable defensiva:* Coca-Cola (`KO`) y Johnson & Johnson (`JNJ`), con demandas inelásticas y flujos estables.
  - *Renta variable de crecimiento:* Alphabet (`GOOGL`) y NVIDIA (`NVDA`), expuestas a expansión tecnológica e IA.
  - *Renta fija y cobertura:* Vanguard Total Bond Market ETF (`BND`) y United States Oil Fund (`USO`), para renta fija agregada y petróleo.
- **Período y frecuencia:** Precios diarios (**2.226 días**, 2018-01-03 a 2026-11-10) vía Yahoo Finance con respaldo en `mis_precios_taller4.csv`.
- **Benchmark:** Cartera institucional **60/30/10** (60% acciones equi-ponderadas al 15%, 30% `BND` y 10% `USO`).
- **Hechos clave de los datos:**
  - *Descorrelación:* `BND` y `USO` presentan correlaciones nulas o negativas con las acciones ($-0,08$ a $0,15$), actuando como amortiguadores. Las acciones correlacionan por bloque (`KO`/`JNJ`: $0,51$; `GOOGL`/`NVDA`: $0,54$).
  - *No-normalidad:* Se rechaza normalidad (Jarque-Bera $p < 0,05$) por alta curtosis en exceso (`BND` 32,64; `USO` 12,78; $1/N$ 9,42) y asimetría negativa, exigiendo evaluar riesgo de cola (Sortino y Drawdown).

![Los bonos y el petróleo se mantienen desacoplados de las acciones, actuando como escudo amortiguador](graficos_taller4/01_matriz_correlacion.png)

![Los retornos presentan colas pesadas y asimetría negativa, rechazando la normalidad (Jarque-Bera p < 0,05)](graficos_taller4/02_normalidad_retornos.png)

## 3. Cómo se armó cada portafolio

### 3.1 Markowitz
- **Restricciones:** 100% del capital invertido y solo posiciones largas ($w_i \ge 0$), evitando apalancamiento y ventas en corto por control de liquidez y pérdidas ilimitadas.
- **Frontera eficiente y carteras notables:** En muestra completa, **Mínima Varianza** concentra 91,5% en `BND` ($\sigma = 5,57\%$, Sharpe 0,11), mientras **Máximo Sharpe** concentra 92,2% en tres acciones (`NVDA` 34,2%, `KO` 32,0%, `JNJ` 26,0%), excluyendo bonos (`BND` 0,0%). In-sample (2018–2023), Máximo Sharpe se hiperconcentra en `KO` (46,1%), `NVDA` (39,9%) y `JNJ` (14,0%), con solo **2,6 activos efectivos**.

![La frontera eficiente de Markowitz logra un Sharpe de 1,15 pero concentra el capital en pocos activos e ignora los bonos](graficos_taller4/03_frontera_eficiente_markowitz.png)

### 3.2 Black-Litterman
- **Punto de partida ($w_{\text{mkt}}$ y $\delta$):** Estructura 60/30/10 ponderando las acciones según su peso en el Dow Jones (`GOOGL` 20,7%, `JNJ` 20,2%, `NVDA` 12,1%, `KO` 7,0%), 30% `BND` y 10% `USO`, con aversión al riesgo $\delta = 2,5$ (estándar institucional) para obtener el equilibrio implícito $\Pi = \delta \Sigma w_{\text{mkt}}$.
- **Visiones ($P$, $Q$ y $\Omega$):** Formuladas sin sesgo de anticipación (previas a 2023-01-04), con matriz de incertidumbre $\Omega = \text{diag}(P(\tau \Sigma)P^\top)/c$ ($\tau = 0,05$, $c = 1,0$):
  - *Visión 1 (relativa):* Tecnología (`NVDA`, `GOOGL`) superará a defensivos (`KO`, `JNJ`) en **6% anual** ($Q_1 = 0,06$, $P_1 = [-0,5,\; -0,5,\; 0,5,\; 0,5,\; 0,\; 0]$) por inversión en IA.
  - *Visión 2 (absoluta):* `BND` rentará **1% anual sobre $r_f$** ($Q_2 = 0,01$, $P_2 = [0,\; 0,\; 0,\; 0,\; 1,\; 0]$) ante la desaceleración inflacionaria.
- **Ajuste y sensibilidad:** Modera el retorno histórico de `NVDA` (de 52,7% a 14,5% en muestra completa) y diversifica en los 6 activos (`BND` 47,0% full-sample / 43,8% in-sample). A menor confianza ($c \to 0$) converge a $w_{\text{mkt}}$; a mayor convicción ($c > 1$) se inclina hacia las visiones de forma estable.

![Black-Litterman ancla los retornos históricos inflados al equilibrio y evita la concentración extrema de Markowitz](graficos_taller4/04_bl_retornos_pesos.png)

![A menor confianza en las visiones el portafolio converge al mercado; a mayor convicción se inclina hacia las visiones](graficos_taller4/05_bl_sensibilidad_confianza.png)

### 3.3 Risk parity
- **Pesos y contribución al riesgo:** Prescinde de retornos esperados e iguala la contribución al riesgo (ERC): cada activo aporta **16,67%** al riesgo total ($\sigma_p = 8,72\%$), sobreponderando `BND` (54,8% full-sample / 57,7% in-sample) y limitando `NVDA` (5,1% / 4,7%).
- **Comparación:** Supera al inverso de volatilidad ($1/\sigma$) al incorporar correlaciones conjuntas, y evita que dos tecnológicas concentren el 58% del riesgo como ocurre en $1/N$.

![Repartir el capital en partes iguales (1/N) concentra el riesgo en tecnología; Risk Parity iguala la contribución al 16,67% por activo](graficos_taller4/07_capital_vs_riesgo.png)

## 4. Comparación

Período de estimación: **2018-01-03 a 2023-01-04 (1.260 días)** · Período de evaluación: **2023-01-05 a 2026-11-10 (966 días)**

| Portafolio | Sharpe dentro | Retorno fuera | Volatilidad fuera | Sharpe fuera | Sortino | TE | IR | MDD | Calmar |
|---|---|---|---|---|---|---|---|---|---|
| **Markowitz** | 0,71 | 39,32% | 19,06% | 1,96 | 3,21 | 11,72% | 1,21 | -14,46% | 3,14 |
| **Black-Litterman** | 0,47 | 18,76% | 8,22% | 2,04 | 3,19 | 3,77% | -1,68 | -8,20% | 2,47 |
| **Risk parity** | 0,38 | 13,63% | 6,12% | 1,90 | 2,96 | 6,31% | -1,82 | -6,03% | 2,38 |
| **Mínima Varianza** | -0,16 | 4,39% | 5,32% | 0,45 | 0,65 | 10,57% | -1,96 | -6,79% | 0,64 |
| **1/N** | 0,53 | 28,84% | 12,39% | 2,17 | 3,45 | 2,82% | 1,33 | -13,25% | 2,44 |
| **Benchmark** | 0,55 | 25,09% | 10,64% | 2,17 | 3,47 | 0,00% | — | -11,00% | 2,53 |

![Fuera de muestra, Black-Litterman lidera el ratio de Sharpe (2,04) entre los modelos optimizados con un drawdown controlado (-8,20%)](graficos_taller4/08_evolucion_riqueza_drawdown.png)

**Criterio del comité y evidencia fuera de muestra:**
- **Métricas decisivas:** Ante colas pesadas, el comité prioriza **Sharpe, Sortino y Max Drawdown (MDD)** junto con baja rotación.
- **Black-Litterman** lidera entre los modelos optimizados (**Sharpe 2,04**, **Sortino 3,19**). Su IR negativo (-1,68) frente al Benchmark se debe a su mayor resguardo en bonos (43,8% vs. 30,0%) durante el rally tecnológico 2023–2026; a cambio, reduce la volatilidad a **8,22%** y acota el **MDD a -8,20%** (superando el -11,00% del Benchmark y -13,25% de $1/N$).
- **Markowitz** logra el mayor retorno (39,32%) solo por apostar 39,9% a `NVDA`, sufriendo la mayor volatilidad (**19,06%**) y el peor Drawdown (**-14,46%**).
- **Risk Parity** y **Mínima Varianza** contienen las caídas (-6,03% y -6,79%), pero su concentración en bonos (57,7% y 92,5%) deprime su retorno (13,63% y 4,39%) y su Sharpe (1,90 y 0,45).

## 5. Robustez y riesgos

![Ante remuestreos bootstrap, Markowitz sufre cambios drásticos de pesos mientras Black-Litterman mantiene una asignación anclada y estable](graficos_taller4/09_bootstrap_sensibilidad_pesos.png)

- **Estabilidad y rotación (Bootstrap B1–B8):** Markowitz es altamente frágil, con rotación promedio de **39,8%** y rango de hasta 70,6% en `BND`. **Black-Litterman** reduce la rotación a **3,7%** (rango máximo de 12,1% en `BND`) y **Risk Parity** a **2,1%** (rango de 7,2%), garantizando bajos costos de transacción.
- **Escenarios de quiebre de la recomendación:**
  - *Fallo en visiones:* Reversión en el ciclo de IA donde el sector tecnológico rinda por debajo del defensivo.
  - *Shock de tasas e inflación:* Estanflación con alzas de tasas que castiguen a `BND` y reactiven correlaciones positivas entre renta fija y acciones.
- **Limitaciones:** Covarianza histórica estática sin encogimiento (Ledoit-Wolf), ponderaciones sin rebalanceo dinámico walk-forward y costos de transacción no modelados.

## 6. Anexo (opcional)

- **Parámetros y entorno:** $r_f = 2,0\%$, factor de anualización $\text{ANN} = 252$, $\delta = 2,5$, $\tau = 0,05$. Ejecutado con `Riskfolio-Lib 7.4.1`, `quantstats 0.0.86`, `scipy`, `numpy` y `pandas`.
