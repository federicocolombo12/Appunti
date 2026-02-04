
## Introduzione: Il Problema della Velocità
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

## 1. Lunghezza d'Arco (Arc Length)
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

### 1. Stima tramite Differenza Diretta (Forward Differencing)
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

### 2. La Tabella di Lunghezza d'Arco (Arc Length Table)
Per evitare calcoli pesanti a ogni frame, pre-calcoliamo una **Look-up Table** $(u, s)$.

### A. Costruzione della Tabella (Pre-processing)
| Indice ($i$) | Parametro ($u_i$) | Lunghezza ($s_i$) |
| :----------- | :---------------- | :---------------- |
| 0            | $0.0$             | $0.0$             |
| 1            | $u_1$             | $s_1$             |
| ...          | ...               | ...               |
| N            | $1.0$             | $L_{tot}$         |

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
## 2.Controllo della Velocità (Velocity Control)

### 1. La Funzione Tempo-Distanza
Dopo aver risolto il problema geometrico con la riparametrizzazione per lunghezza d'arco ($s = G(u)$), ora possiamo muoverci a velocità costante. Tuttavia, per un'animazione realistica, vogliamo controllare l'accelerazione.

Introduciamo la **Funzione Tempo-Distanza** $s = T(t)$, che mappa il tempo normalizzato alla distanza percorsa.
![[Pasted image 20260201113152.png]]
### La Pipeline Completa di Valutazione
Per calcolare la posizione di un oggetto al tempo $t$, concateniamo le funzioni:

1.  **Time Mapping:** $s = T(t)$ (gestisce l'accelerazione).
2.  **Inverse Arc Length:** $u = G^{-1}(s)$ (trova il parametro geometrico corrispondente alla distanza).
3.  **Curve Evaluation:** $P = P(u)$ (calcola le coordinate 3D).

$$
P_{final}(t) = P( G^{-1}( T(t) ) )
$$

---

### 2. Ease-In ed Ease-Out
In fisica, gli oggetti dotati di massa non partono e non si fermano istantaneamente (richiederebbe forza infinita).
* **Ease-In (Slow-In):** Il movimento inizia con velocità zero e accelera gradualmente.
* **Ease-Out (Slow-Out):** Il movimento rallenta gradualmente fino a velocità zero.

Matematicamente, questo significa che la derivata della funzione distanza (ovvero la velocità) deve essere nulla agli estremi:
$$
v(0) = \frac{ds}{dt}(0) = 0 \quad \text{e} \quad v(1) = \frac{ds}{dt}(1) = 0
$$

---

### 3. Interpolazione Sinusoidale (Sine Interpolation)
Un metodo classico ed elegante per ottenere un effetto **Ease-In / Ease-Out** simmetrico è utilizzare una porzione della funzione trigonometrica **Seno** (Rif. Slide 26-29).

![[Pasted image 20260201114252.png]]
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

### 4.Controllo della Velocità a Tratti (Piecewise Velocity Control)

### Il Concetto: Profilo di Velocità Trapezoidale
A differenza dell'interpolazione sinusoidale (che cambia velocità continuamente), questo metodo suddivide il movimento in **tre fasi distinte** per garantire un tratto centrale a velocità perfettamente uniforme. È il metodo standard per macchinari, robotica e animazioni di telecamere.

Le tre fasi sono:
1.  **Accelerazione Costante** (Ease-In parabolico).
2.  **Velocità Costante** (Tratto lineare).
3.  **Decelerazione Costante** (Ease-Out parabolico).

---

### Formulazione Matematica
Definiamo il tempo normalizzato $t \in [0, 1]$.
Dividiamo l'intervallo temporale usando due parametri:
* $t_1$: Momento in cui finisce l'accelerazione.
* $t_2$: Momento in cui inizia la decelerazione.

### Il Grafico della Velocità $v(t)$
La velocità segue un profilo a trapezio:
* Sale linearmente da 0 a $V_{max}$ nel tempo $t_1$.
* Resta costante a $V_{max}$ tra $t_1$ e $t_2$.
* Scende linearmente da $V_{max}$ a 0 nel tempo $(1 - t_2)$.

$$
v(t) = \begin{cases} 
\frac{V_{max}}{t_1} \cdot t & \text{se } 0 \le t < t_1 \\
V_{max} & \text{se } t_1 \le t \le t_2 \\
V_{max} - \frac{V_{max}}{1-t_2} \cdot (t - t_2) & \text{se } t_2 < t \le 1
\end{cases}
$$

> **Nota Fondamentale:** Per percorrere una distanza unitaria ($s=1$) in tempo unitario ($t=1$), l'area sotto il trapezio deve essere 1. Questo vincolo ci permette di calcolare $V_{max}$:
>
> $$V_{max} = \frac{2}{1 + (t_2 - t_1)}$$

---

### La Funzione Distanza-Tempo $s(t)$
Integrando la velocità, otteniamo la posizione $s(t)$. La curva risultante è composta da una parabola (concavità alta), una retta e una parabola (concavità bassa).

Le formule definitive per $s(t)$:

#### Fase 1: Accelerazione ($0 \le t < t_1$)
Moto uniformemente accelerato.
$$
s(t) = \frac{V_{max}}{2 \cdot t_1} \cdot t^2
$$

#### Fase 2: Velocità Costante ($t_1 \le t \le t_2$)
Moto rettilineo uniforme. La posizione parte dalla fine della fase 1 ($S_1 = \frac{V_{max} \cdot t_1}{2}$).
$$
s(t) = \frac{V_{max} \cdot t_1}{2} + V_{max} \cdot (t - t_1)
$$

#### Fase 3: Decelerazione ($t_2 < t \le 1$)
Moto uniformemente decelerato.
$$
s(t) = 1 - \frac{V_{max}}{2 \cdot (1 - t_2)} \cdot (1 - t)^2
$$

### 6. Controllo della Velocità ad Accelerazione Costante (Parabolic Ease)
![[Pasted image 20260201115621.png]]
Mentre l'interpolazione sinusoidale (Sine Ease) offre un'accelerazione che varia continuamente (molto naturale/organica), in alcuni contesti (robotica, macchinari, movimenti di camera specifici) si preferisce avere un'**accelerazione costante**.

Questo profilo di velocità è detto **Trapezoidale** e genera una curva di distanza composta da segmenti parabolici e lineari.

#### Differenza Chiave
* **Seno:** Accelerazione variabile (parte da 0, picco, torna a 0).
* **Parabolica:** Accelerazione costante (scatto iniziale costante, poi zero, poi frenata costante).

---

#### Il Profilo di Velocità (Velocity Profile)
Immaginiamo il grafico della velocità $v(t)$ su un tempo normalizzato $[0, 1]$.
Il grafico forma un **Trapezio** diviso in tre fasi temporali:

1.  **$0 \to t_1$ (Ease-In):** La velocità sale linearmente da 0 a $V_{max}$ (Accelerazione Costante).
2.  **$t_1 \to t_2$ (Tratto Costante):** La velocità rimane fissa a $V_{max}$ (Accelerazione Nulla).
3.  **$t_2 \to 1$ (Ease-Out):** La velocità scende linearmente da $V_{max}$ a 0 (Decelerazione Costante).
![[Pasted image 20260201115608.png]]
---

#### Calcolo dell'Area Sottesa (Fondamentale)
In fisica, l'integrale della velocità è la distanza. Poiché stiamo lavorando su curve normalizzate, la distanza totale percorsa deve essere **1.0**.
Quindi, **l'area del trapezio deve essere uguale a 1**.

Sfruttiamo questa proprietà per trovare l'unica incognita: la velocità massima ($V_{max}$).

$$
\text{Area} = \frac{(\text{Base Maggiore} + \text{Base Minore}) \cdot \text{Altezza}}{2} = 1
$$

Dove:
* **Base Maggiore:** Durata totale $= 1.0$
* **Base Minore:** Durata del tratto costante $= t_2 - t_1$
* **Altezza:** $V_{max}$

$$
1 = \frac{(1 + (t_2 - t_1)) \cdot V_{max}}{2}
$$

Risolvendo per $V_{max}$, otteniamo la formula fondamentale per dimensionare la curva:

$$
V_{max} = \frac{2}{1 + t_2 - t_1}
$$

> **Esempio:** Se accelero per il 20% del tempo ($t_1=0.2$) e decelero per l'ultimo 20% ($t_2=0.8$):
> $$V_{max} = \frac{2}{1 + 0.6} = \frac{2}{1.6} = 1.25$$
> La velocità di crociera deve essere il 25% più veloce della media per recuperare il tempo perso in accelerazione.

---

#### Le Equazioni del Moto $s(t)$
Una volta trovato $V_{max}$, definiamo la funzione posizione $s(t)$ a tratti (Piecewise Function).
![[Pasted image 20260201115728.png]]

### Fase 1: Accelerazione ($0 \le t < t_1$)
È un moto uniformemente accelerato ($s = \frac{1}{2}at^2$).
L'accelerazione è la pendenza della rampa: $a = \frac{V_{max}}{t_1}$.

$$
s(t) = \frac{V_{max}}{2 t_1} \cdot t^2
$$

### Fase 2: Velocità Costante ($t_1 \le t \le t_2$)
È un moto rettilineo uniforme.
Partiamo dalla posizione raggiunta alla fine della fase 1 ($S_{t1} = \frac{V_{max} t_1}{2}$).

$$
s(t) = \frac{V_{max} t_1}{2} + V_{max} \cdot (t - t_1)
$$

### Fase 3: Decelerazione ($t_2 < t \le 1$)
È un moto uniformemente decelerato.
Per semplicità di calcolo, possiamo vederla come "Distanza Totale (1) meno il tratto che manca percorrendo la parabola al contrario".

$$
s(t) = 1 - \frac{V_{max} \cdot (1 - t)^2}{2(1 - t_2)}
$$

---
