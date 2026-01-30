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

<!-- ... existing content ... -->
### Spiegazione della Matrice
Se guardiamo le colonne della matrice moltiplicate per $G$, stiamo definendo quattro polinomi di base ($H_1, H_2, H_3, H_4$):
* Le prime due colonne pesano i punti $P_0$ e $P_3$.
* Le ultime due colonne pesano le tangenti $R_0$ e $R_3$.

### Continuità e Giunzioni
Un vantaggio di Hermite è il controllo diretto sulla continuità quando si uniscono più segmenti (*piecewise*):
* **$C^0$ (Posizione):** È garantita ponendo il punto finale del segmento $i$ coincidente con il punto iniziale del segmento $i+1$ ($P_{3, i} = P_{0, i+1}$).
* **$C^1$ (Tangenza):** È **facile da garantire**: basta imporre che il vettore tangente uscente del primo segmento sia identico a quello entrante del secondo ($R_{3, i} = R_{0, i+1}$).
* **$C^2$ (Curvatura):** **Non è garantita**. Poiché Hermite definisce solo posizioni e velocità (derivate prime), non abbiamo controllo diretto sulle derivate seconde (accelerazioni). Per ottenere $C^2$ servirebbero calcoli complessi per accordare le accelerazioni alla giunzione.

### Limiti di Hermite
Nonostante sia potente, l'approccio di Hermite non è intuitivo per l'interazione umana (Computer Aided Design):
* È difficile per un designer immaginare e inserire numericamente le componenti $x, y, z$ di un vettore tangente ($R$).
<!-- ... existing content ... -->
* Modificare la "lunghezza" del vettore tangente cambia la forma della curva in modo drastico (overshooting), ma non è visivamente prevedibile come spostare un punto.
* **Soluzione:** Si preferiscono sistemi dove le tangenti sono definite indirettamente tramite altri punti (es. Catmull-Rom o Bezier).![[Screenshot 2026-01-30 alle 17.27.01.png|500]]