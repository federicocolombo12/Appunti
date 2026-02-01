# Controllo del Moto e Riparametrizzazione

## 1. Introduzione: Il Problema della Velocità
In [[Computer Animation]], usiamo curve parametriche $P(u)$ per definire le traiettorie nello spazio.

* **Input:** Un parametro adimensionale $u$, tipicamente $u \in [0, 1]$.
* **Output:** Un punto nello spazio 3D.

$$
P(u) = \begin{bmatrix} x(u) \\ y(u) \\ z(u) \end{bmatrix}
$$

### Il Problema della Velocità Costante
Se incrementiamo $u$ a passi costanti (es. $0.1, 0.2...$), l'oggetto **non** si muove a velocità costante lungo la curva.
La velocità geometrica reale dipende dalla derivata della funzione:

$$
\mathbf{V}(u) = \frac{d P(u)}{d u} = P'(u)
$$

Poiché $||P'(u)||$ (la magnitudine del vettore tangente) varia lungo la curva, l'oggetto accelererà e rallenterà in modo non voluto.

> **Obiettivo:** Vogliamo muoverci in base alla **distanza** ($s$), non al parametro ($u$).

---

## 2. Lunghezza d'Arco (Arc Length)
Definiamo la funzione **Lunghezza d'Arco** $G(u)$ che calcola la distanza dall'inizio della curva fino al punto parametrico $u$.

### Formula Analitica (Integrale)
La lunghezza d'arco $s$ è l'integrale della magnitudine del vettore velocità:

$$
s = G(u) = \int_{0}^{u} || P'(t) || \, dt
$$

Espandendo la norma del vettore in 3D:

$$
s = \int_{0}^{u} \sqrt{ \left(\frac{dx}{dt}\right)^2 + \left(\frac{dy}{dt}\right)^2 + \left(\frac{dz}{dt}\right)^2 } \, dt
$$

### Il Problema Computazionale
Per le curve usate in animazione (come le [[Spline Cubiche]]), $x(t)$, $y(t)$ e $z(t)$ sono polinomi di grado 3. L'argomento sotto radice diventa un polinomio di grado **5** o **6**.

$$
\int \sqrt{ A t^4 + B t^3 + C t^2 + D t + E } \, dt
$$

> **Nota Importante:** Questo integrale **non ha soluzione analitica** in forma chiusa (non esiste una formula semplice). Dobbiamo usare metodi numerici.

---

## 3. Stima tramite Differenza Diretta (Forward Differencing)
![[Pasted image 20260201112445.png]]
Poiché non possiamo risolvere l'integrale, lo approssimiamo sommando le lunghezze di tanti piccoli segmenti lineari (corde).

Dividiamo la curva in $k$ passi. La stima è la sommatoria delle distanze euclidee:
-
$$
s \approx \sum_{i=1}^{k} || P(u_i) - P(u_{i-1}) ||
$$

Applicando il **Teorema di Pitagora 3D**:

$$
s \approx \sum_{i=1}^{k} \sqrt{ (x_i - x_{i-1})^2 + (y_i - y_{i-1})^2 + (z_i - z_{i-1})^2 }
$$

---

## 4. La Tabella di Lunghezza d'Arco (Arc Length Table)
Per evitare calcoli pesanti a ogni frame, pre-calcoliamo una **Look-up Table** $(u, s)$.

### A. Costruzione della Tabella (Pre-processing)
| Indice ($i$) | Parametro ($u_i$) | Lunghezza ($s_i$) |
| :--- | :--- | :--- |
| 0 | $0.0$ | $0.0$ |
| 1 | $u_1$ | $s_1$ |
| ... | ... | ... |
| N | $1.0$ | $L_{tot}$ |

### B. Riparametrizzazione a Runtime (Inverse Mapping)
Durante l'animazione, dato un valore di distanza desiderata $s_{target}$, dobbiamo trovare $u$ (invertire la funzione: $u = G^{-1}(s)$).

**Algoritmo:**

1.  **Ricerca:** Trova nella tabella l'indice $k$ tale che:
    $$
    s_k \le s_{target} < s_{k+1}
    $$

2.  **Interpolazione Lineare (Lerp):** Calcola il fattore $\alpha$:
    $$
    \alpha = \frac{s_{target} - s_k}{s_{k+1} - s_k}
    $$

3.  **Calcolo di u:**
    $$
    u_{target} = u_k + \alpha \cdot (u_{k+1} - u_k)
    $$

4.  **Rendering:** Usa $u_{target}$ per posizionare l'oggetto:
    $$
    Position = P(u_{target})
    $$

---
# Controllo della Velocità (Velocity Control)

## 1. La Funzione Tempo-Distanza
Dopo aver risolto il problema geometrico con la riparametrizzazione per lunghezza d'arco ($s = G(u)$), ora possiamo muoverci a velocità costante. Tuttavia, per un'animazione realistica, vogliamo controllare l'accelerazione.

Introduciamo la **Funzione Tempo-Distanza** $s = T(t)$, che mappa il tempo normalizzato alla distanza percorsa.

### La Pipeline Completa di Valutazione
Per calcolare la posizione di un oggetto al tempo $t$, concateniamo le funzioni:

1.  **Time Mapping:** $s = T(t)$ (gestisce l'accelerazione).
2.  **Inverse Arc Length:** $u = G^{-1}(s)$ (trova il parametro geometrico corrispondente alla distanza).
3.  **Curve Evaluation:** $P = P(u)$ (calcola le coordinate 3D).

$$
P_{final}(t) = P( G^{-1}( T(t) ) )
$$

---

## 2. Ease-In ed Ease-Out
In fisica, gli oggetti dotati di massa non partono e non si fermano istantaneamente (richiederebbe forza infinita).
* **Ease-In (Slow-In):** Il movimento inizia con velocità zero e accelera gradualmente.
* **Ease-Out (Slow-Out):** Il movimento rallenta gradualmente fino a velocità zero.

Matematicamente, questo significa che la derivata della funzione distanza (ovvero la velocità) deve essere nulla agli estremi:
$$
v(0) = \frac{ds}{dt}(0) = 0 \quad \text{e} \quad v(1) = \frac{ds}{dt}(1) = 0
$$

---

## 3. Interpolazione Sinusoidale (Sine Interpolation)
Un metodo classico ed elegante per ottenere un effetto **Ease-In / Ease-Out** simmetrico è utilizzare una porzione della funzione trigonometrica **Seno** (Rif. Slide 26-29).

### Derivazione della Formula
La funzione $\sin(\theta)$ ha pendenza (derivata) nulla a $-\frac{\pi}{2}$ e $+\frac{\pi}{2}$.
Vogliamo mappare il nostro tempo $t \in [0, 1]$ in questo intervallo angolare.

1.  **Mappatura angolare:**
    $$
    \theta(t) = \pi \cdot t - \frac{\pi}{2}
    $$
    * Se $t=0 \rightarrow \theta = -\pi/2$
    * Se $t=1 \rightarrow \theta = +\pi/2$

2.  **Calcolo del Seno:**
    $$
    y = \sin(\theta(t))
    $$
    Il risultato varia tra $[-1, 1]$.

3.  **Normalizzazione:**
    Portiamo il range in $[0, 1]$ per ottenere la frazione di distanza percorsa:
    $$
    f(t) = \frac{1 + \sin(\pi t - \frac{\pi}{2})}{2}
    $$

    Usando l'identità trigonometrica $\sin(\alpha - \frac{\pi}{2}) = -\cos(\alpha)$, otteniamo la formula finale semplificata:

$$
s(t) = L_{tot} \cdot \frac{1 - \cos(\pi t)}{2}
$$

Dove $L_{tot}$ è la lunghezza totale della curva.

### Analisi della Velocità
La velocità è la derivata prima della distanza:
$$
v(t) = s'(t) = L_{tot} \cdot \frac{\pi}{2} \sin(\pi t)
$$
* La velocità massima si raggiunge a $t=0.5$ ed è pari a $\frac{\pi}{2}$ volte (circa 1.57x) la velocità media.

---

## 4. Esempio Numerico (Rif. Slide 28-29)
Confrontiamo un movimento lineare (velocità costante) con uno sinusoidale su un percorso di 10 metri in 1 secondo.

| Tempo ($t$) | Distanza Lineare | Distanza Sinusoidale | Stato |
| :--- | :--- | :--- | :--- |
| **0.00** | 0.00 m | **0.00 m** | Fermo ($v=0$) |
| **0.10** | 1.00 m | **0.24 m** | Parte pianissimo (Ease-In) |
| **0.25** | 2.50 m | **1.46 m** | Sta accelerando |
| **0.50** | 5.00 m | **5.00 m** | Velocità Max (ha recuperato il ritardo) |
| **0.75** | 7.50 m | **8.53 m** | Sta frenando (ha superato il lineare) |
| **0.90** | 9.00 m | **9.75 m** | Quasi fermo (Ease-Out) |
| **1.00** | 10.00 m | **10.00 m** | Arrivo ($v=0$) |

---

### Pseudocodice Implementativo

```python
import math

def get_distance_sine_ease(t, total_length):
    # t deve essere tra 0.0 e 1.0
    if t < 0: return 0
    if t > 1: return total_length
    
    # Formula Ease-in / Ease-out Sinusoidale
    factor = (1.0 - math.cos(t * math.pi)) / 2.0
    
    return total_length * factor