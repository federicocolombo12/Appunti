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
Poiché non possiamo risolvere l'integrale, lo approssimiamo sommando le lunghezze di tanti piccoli segmenti lineari (corde).

Dividiamo la curva in $k$ passi. La stima è la sommatoria delle distanze euclidee:

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

### Pseudocodice Implementativo
```python
# 1. Calcolo distanza target
distanza_target = velocita * tempo

# 2. Ricerca nella tabella
idx = find_index(table, distanza_target)

# 3. Interpolazione inversa
alpha = (distanza_target - table[idx].s) / (table[idx+1].s - table[idx].s)
u_final = table[idx].u + alpha * (table[idx+1].u - table[idx].u)

# 4. Valutazione curva
draw( P(u_final) )