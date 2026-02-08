# Rappresentazione delle Curve (Parte 1)

## 1. Introduzione e Tipi di Rappresentazione
In Computer Graphics, la rappresentazione degli oggetti (e quindi delle curve) può avvenire in tre modi principali. La scelta della rappresentazione influenza la facilità di rendering e di manipolazione.

### A. Rappresentazione Esplicita
$$y = f(x)$$
* **Descrizione:** Per ogni valore di $x$ c'è un solo valore di $y$.
* **Limiti:** Non può rappresentare curve verticali (dove la pendenza è infinita) o curve chiuse (come un cerchio) che avrebbero più valori di $y$ per un singolo $x$. Inoltre, non è invariante per rotazione (se ruoto la curva, l'equazione cambia forma completamente).

### B. Rappresentazione Implicita
$$f(x, y) = 0$$
* **Esempio:** $x^2 + y^2 - R^2 = 0$ (Cerchio).
* **Descrizione:** La curva è definita come l'insieme dei punti che soddisfano l'equazione.
* **Vantaggi:** Ottima per determinare se un punto sta *dentro*, *fuori* o *sulla* curva (collision detection).
* **Limiti:** Difficile generare sequenze di punti per disegnarla (rendering).

### C. Rappresentazione Parametrica (Standard in CG)
$$x = x(u), \quad y = y(u), \quad z = z(u) \quad \text{con } u \in [0, 1]$$
* **Descrizione:** Le coordinate sono funzioni indipendenti di un parametro $u$.
* **Vantaggi:**
    1. **Flessibilità:** Può rappresentare curve chiuse, cappi e tangenti verticali.
    2. **Indipendenza:** La curva è definita in uno spazio parametrico normalizzato.
    3. **Rendering:** È banale generare punti: basta incrementare $u$ e calcolare le coordinate.

---

## 2. Controllo della Forma: Globale vs Locale
Quando modifichiamo i **Punti di Controllo** ($G$) che definiscono la curva, come reagisce la forma?

* **Controllo Globale:** La modifica di un singolo punto di controllo influenza l'intera curva, dal primo all'ultimo punto. (Es. Bezier standard, Interpolazione di Lagrange).
* **Controllo Locale:** La modifica di un punto influenza solo un tratto limitato della curva. Questo è preferibile nella modellazione complessa per non "rompere" parti già definite. (Es. B-Spline).

---

## 3. Curve Polinomiali Cubiche
In Computer Animation, la funzione parametrica scelta è quasi sempre un **polinomio di terzo grado (cubico)**.

### Perché proprio Cubiche ($n=3$)?
* **$n=1$ (Lineare):** Troppo spigolosa, niente curve.
* **$n=2$ (Quadratica):** Non ha punti di flesso ed è sempre piana (2D).
* **$n=3$ (Cubica):** È il grado minimo che garantisce:
    1. Curve non piane (3D).
    2. Presenza di punti di flesso.
    3. Continuità $C^2$ (curvatura) tra segmenti.
* **$n > 3$:** Troppo costose da calcolare e rischio di oscillazioni indesiderate.

L'equazione generale di un polinomio cubico parametrico è:
$$P(u) = \mathbf{a}u^3 + \mathbf{b}u^2 + \mathbf{c}u + \mathbf{d} \quad (u \in [0,1])$$

---

## 4. Interpolazione Lineare
È il caso base ($n=1$). Collega due punti $P_0$ e $P_1$ con una retta.

### Forma Esplicita
La curva è una somma pesata dei due punti:
$$P(u) = (1-u)P_0 + uP_1$$
Dove $(1-u)$ e $u$ sono le **funzioni di blending** (pesi).

### Forma Matriciale ($U \cdot M \cdot G$)
Possiamo riscrivere l'equazione raccogliendo la $u$:
$$P(u) = P_0 - uP_0 + uP_1 = u(P_1 - P_0) + P_0$$

In notazione vettoriale/matriciale:
* **$U$ (Variabili):** $[u, 1]$
* **$G$ (Geometria):** $\begin{bmatrix} P_0 \\ P_1 \end{bmatrix}$
* **$M$ (Matrice):** $\begin{bmatrix} -1 & 1 \\ 1 & 0 \end{bmatrix}$

$$
P(u) = 
\begin{bmatrix} u & 1 \end{bmatrix} 
\cdot 
\begin{bmatrix} -1 & 1 \\ 1 & 0 \end{bmatrix} 
\cdot 
\begin{bmatrix} P_0 \\ P_1 \end{bmatrix}
$$

---

## 5. Curve di Hermite
La curva di Hermite è una cubica determinata non solo dalle posizioni, ma dalle velocità (tangenti).

### Definizione (Vincoli)
Vogliamo una curva $P(u)$ tale che:
1. Inizia in $P_0$ ($P(0) = P_0$)
2. Finisce in $P_3$ ($P(1) = P_3$)
3. Ha velocità iniziale $R_0$ ($P'(0) = R_0$)
4. Ha velocità finale $R_3$ ($P'(1) = R_3$)
![[Screenshot 2026-01-30 alle 17.24.31.png|500]]

### Forma Matriciale ($U \cdot M \cdot G$)
Risolvendo il sistema di equazioni cubiche con i vincoli sopra, otteniamo la matrice fondamentale di Hermite ($M_H$).

* **$U$:** $[u^3, u^2, u, 1]$
* **$G$ (Vettore Geometrico):** $\begin{bmatrix} P_0 \\ P_3 \\ R_0 \\ R_3 \end{bmatrix}$ (Attenzione: include vettori tangenti!)

$$
P(u) = 
\begin{bmatrix} u^3 & u^2 & u & 1 \end{bmatrix} 
\cdot 
\begin{bmatrix}
2 & -2 & 1 & 1 \\
-3 & 3 & -2 & -1 \\
0 & 0 & 1 & 0 \\
1 & 0 & 0 & 0
\end{bmatrix}
\cdot 
\begin{bmatrix} P_0 \\ P_3 \\ R_0 \\ R_3 \end{bmatrix}
$$

### Le 4 Funzioni di Base di Hermite
Moltiplicando il vettore $U$ per la matrice $M_H$, otteniamo i quattro polinomi che fungono da pesi (Blending Functions):
$$P(u) = P_0 H_1(u) + P_3 H_2(u) + R_0 H_3(u) + R_3 H_4(u)$$

Dove:
1.  **$H_1(u) = 2u^3 - 3u^2 + 1$**: Parte da 1 e scende a 0. (Controlla la vicinanza a $P_0$).
2.  **$H_2(u) = -2u^3 + 3u^2$**: Parte da 0 e sale a 1. (Controlla la vicinanza a $P_3$).
3.  **$H_3(u) = u^3 - 2u^2 + u$**: Pesa la tangente iniziale $R_0$.
4.  **$H_4(u) = u^3 - u^2$**: Pesa la tangente finale $R_3$.

> **Nota:** La somma $H_1 + H_2$ è sempre uguale a 1 (partizione dell'unità per le posizioni), mentre $H_3$ e $H_4$ gestiscono l'influenza vettoriale della direzione.

**Nota Fondamentale: Interpretazione Vettoriale**
> *   **$P(u)$** non è un singolo numero, ma un punto 3D $\begin{bmatrix} x(u) & y(u) & z(u) \end{bmatrix}$. L'equazione matriciale va applicata **tre volte separatamente** (una per la X, una per la Y, una per la Z).
> *   **$R_0$ e $R_3$** sono **vettori** tangenti (velocità) con componenti $(x', y', z')$.
>     *   *Intuizione:* Se $P_0$ è la posizione di partenza di una macchina, $R_0$ è la direzione e la potenza con cui premi l'acceleratore alla partenza.

### Continuità e Giunzioni
Un vantaggio di Hermite è il controllo diretto sulla continuità quando si uniscono più segmenti (*piecewise*):
* **$C^0$ (Posizione):** È garantita ponendo il punto finale del segmento $i$ coincidente con il punto iniziale del segmento $i+1$ ($P_{3, i} = P_{0, i+1}$).
* **$C^1$ (Tangenza):** È **facile da garantire**: basta imporre che il vettore tangente uscente del primo segmento sia identico a quello entrante del secondo ($R_{3, i} = R_{0, i+1}$).
* **$C^2$ (Curvatura):** **Non è garantita**. Poiché Hermite definisce solo posizioni e velocità (derivate prime), non abbiamo controllo diretto sulle derivate seconde (accelerazioni). Per ottenere $C^2$ servirebbero calcoli complessi per accordare le accelerazioni alla giunzione.

### Limiti di Hermite
Nonostante sia potente, l'approccio di Hermite non è intuitivo per l'interazione umana (Computer Aided Design):
* È difficile per un designer immaginare e inserire numericamente le componenti $x, y, z$ di un vettore tangente ($R$).
* Modificare la "lunghezza" del vettore tangente cambia la forma della curva in modo drastico (overshooting), ma non è visivamente prevedibile come spostare un punto.
* **Soluzione:** Si preferiscono sistemi dove le tangenti sono definite indirettamente tramite altri punti (es. Catmull-Rom o Bezier).![[Screenshot 2026-01-30 alle 17.27.01.png|500]]

## 6. Curve di Catmull-Rom
Le curve di Hermite sono potenti ma scomode perché richiedono di definire manualmente le tangenti. La **Catmull-Rom Spline** risolve questo problema automatizzando il calcolo delle tangenti in base alla posizione  dei punti vicini.
![[Screenshot 2026-01-30 alle 17.40.36.png|500]]

### A. Calcolo Automatico del Vettore Tangente
L'idea chiave è che la tangente in un punto $P_i$ è parallela alla linea che collega il punto precedente ($P_{i-1}$) e quello successivo ($P_{i+1}$).
La formula per la tangente $\mathbf{v}_i$ nel punto $P_i$ è:

$$
\mathbf{v}_i = \frac{P_{i+1} - P_{i-1}}{2} = \tau (P_{i+1} - P_{i-1})
$$

Dove $\tau$ (tau) è la **tensione** della curva (solitamente $0.5$).
* Se $\tau = 0.5$, la tangente è esattamente la metà della distanza tra i vicini.
* Se $\tau \to 0$, la curva diventa molto "lasca" (flaccida).
* Se $\tau \to 1$, la curva diventa molto "tesa" e rigida.

### B. Forma Matriciale ($U \cdot M \cdot G$)
Per definire un segmento di curva tra $P_i$ e $P_{i+1}$, abbiamo bisogno di 4 punti di input: $P_{i-1}, P_i, P_{i+1}, P_{i+2}$.
* La curva passa **esattamente** per $P_i$ e $P_{i+1}$.
* I punti $P_{i-1}$ e $P_{i+2}$ servono solo a calcolare le tangenti.

La matrice di base $M_{Catmull}$ (con tensione $s=0.5$) è:

$$
M_{Catmull} = \frac{1}{2} \cdot
\begin{bmatrix}
-1 & 3 & -3 & 1 \\
2 & -5 & 4 & -1 \\
-1 & 0 & 1 & 0 \\
0 & 2 & 0 & 0
\end{bmatrix}
$$

$$
P(u) = [u^3, u^2, u, 1] \cdot M_{Catmull} \cdot 
\begin{bmatrix} P_{i-1} \\ P_i \\ P_{i+1} \\ P_{i+2} \end{bmatrix}
$$

---

## 7. Curve di Bezier
Le curve di Bezier sono un approccio alternativo dove i punti di controllo definiscono un poligono (il **Poligono di Controllo**) che "attrae" la curva.

### A. Definizione Generale (Sommatoria)
Una curva di Bezier di grado $n$ definita da $(n+1)$ punti di controllo è data dalla combinazione lineare dei punti tramite i **Polinomi di Bernstein**:

$$
P(u) = \sum_{k=0}^{n} P_k \cdot B_{k,n}(u) \quad \text{con } u \in [0,1]
$$

Dove $B_{k,n}(u)$ sono i **Polinomi di Bernstein**:
$$
B_{k,n}(u) = \binom{n}{k} u^k (1-u)^{n-k}
$$
(Il termine $\binom{n}{k}$ è il coefficiente binomiale).

### B. Proprietà Fondamentali (Bernstein)
1. **Partizione dell'Unità:** $\sum B_{k,n}(u) = 1$ per ogni $u$. (La curva è una media pesata dei punti).
2. **Positività:** $B_{k,n}(u) \ge 0$ nell'intervallo $[0,1]$.
3. **Simmetria:** La forma della curva non cambia se invertiamo l'ordine dei punti (solo la direzione di percorrenza cambia).

### C. Bezier Cubica ($n=3$)

![[Screenshot 2026-01-30 alle 17.41.28.png]]
È la più usata in CG. Definita da 4 punti: $P_0, P_1, P_2, P_3$.
* **Interpolazione agli estremi:** $P(0) = P_0$ e $P(1) = P_3$.
* **Tangenti:**
    * La tangente iniziale è $P'(0) = 3(P_1 - P_0)$.
    * La tangente finale è $P'(1) = 3(P_3 - P_2)$.
* **Convex Hull:** La curva giace interamente nel poligono convesso dei 4 punti.

### D. Matrice dei Coefficienti ($M_{Bezier}$)
Espandendo i polinomi di Bernstein per $n=3$, otteniamo la matrice:

$$
M_{Bezier} = 
\begin{bmatrix}
-1 & 3 & -3 & 1 \\
3 & -6 & 3 & 0 \\
-3 & 3 & 0 & 0 \\
1 & 0 & 0 & 0
\end{bmatrix}
$$

### E. Continuità tra Segmenti
Quando uniamo due curve di Bezier (Curva A e Curva B), la continuità dipende dalla geometria dei punti di giunzione:
* **Continuità $C^0$ (Posizione):** L'ultimo punto di A coincide col primo di B ($P_{A3} = P_{B0}$).
* **Continuità $C^1$ (Tangenziale - Smooth):** I tre punti alla giunzione ($P_{A2}, P_{A3}=P_{B0}, P_{B1}$) devono essere **colineari**.
![[Pasted image 20260208124545.png]]
---

## 8. B-Spline (Basis Spline)
Le B-Spline nascono per superare i due limiti principali delle Bezier:
1. **Controllo Globale:** Nelle Bezier di alto grado, spostare un punto modifica tutta la curva.
2. **Grado vincolato:** Nelle Bezier, il grado è legato al numero di punti ($n$ punti $\rightarrow$ grado $n-1$).

### A. Caratteristiche Chiave
* **Controllo Locale:** I polinomi di base delle B-Spline sono non nulli solo su un intervallo limitato di $u$. Modificare un punto $P_i$ influenza solo $k$ segmenti vicini (dove $k$ è il grado).
* **Grado Indipendente:** Posso avere 100 punti di controllo e usare una B-Spline cubica (grado 3).
* **Nodi (Knot Vector):** La sequenza dei parametri $u$ dove i polinomi si "incollano" è definita da un vettore di nodi $T = [t_0, t_1, ..., t_m]$. La molteplicità dei nodi permette di creare spigoli vivi o discontinuità intenzionali.

### B. Matrice B-Spline (Uniforme Cubica)
Per una B-Spline cubica standard (uniforme), la matrice di base approssimante è:

$$
M_{BSpline} = \frac{1}{6}
\begin{bmatrix}
-1 & 3 & -3 & 1 \\
3 & -6 & 3 & 0 \\
-3 & 0 & 3 & 0 \\
1 & 4 & 1 & 0
\end{bmatrix}
$$

*Nota:* A differenza della Bezier, la B-Spline non passa per il primo e l'ultimo punto di controllo (a meno che non si usino nodi multipli agli estremi, tecnica chiamata "Clamping").