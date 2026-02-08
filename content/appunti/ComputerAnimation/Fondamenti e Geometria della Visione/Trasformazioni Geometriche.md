Le trasformazioni geometriche sono operazioni matematiche che permettono di modificare la posizione, l'orientamento e la dimensione degli oggetti nello spazio 3D. In CG, queste operazioni vengono eseguite tramite il **prodotto tra matrici e vettori**.
# Fondamenti Teorici: Scena, Vettori e Coordinate

Prima di manipolare i vertici con le matrici, è fondamentale comprendere la struttura matematica dello spazio in cui operiamo, partendo dalla definizione della scena fino alla necessità algebrica delle coordinate omogenee.

---

## 1. Definizione della Scena e Trasformazioni
In Computer Graphics, una scena è composta da oggetti posizionati in un mondo virtuale.
Ogni oggetto è definito inizialmente nel proprio **spazio locale** e deve essere posizionato nello spazio della scena (World Space).

### Trasformazioni Affini
Le operazioni utilizzate per posizionare e deformare gli oggetti (Traslazione, Rotazione, Scalamento) appartengono alla famiglia delle **Trasformazioni Affini**.
* **Definizione:** Una trasformazione è affine se **conserva le linee rette**.
  * Trasformando una retta, si ottiene ancora una retta.
  * *Nota:* Il parallelismo tra rette viene conservato, ma gli angoli e le lunghezze possono cambiare (es. nello scalamento non uniforme).
![[Pasted image 20260208111632.png]]
---

## 2. La Fase di Visualizzazione (Viewing)
Una volta composta la scena, avviene il processo di visualizzazione, che richiede specifici cambi di sistema di riferimento.

1.  **Visualizzazione (Viewing):** La scena viene osservata da un punto di vista ($COP$) e con una certa direzione.
2.  **Trasformazioni Rigide:** Per portare gli oggetti nel sistema di riferimento della camera, si usano trasformazioni che spostano e ruotano il mondo. Queste sono dette **Rigide** perché conservano:
    * Le linee rette (sono affini).
    * Le **distanze** tra i punti (la forma non cambia).
    * Gli **angoli**.
3.  **Proiezioni:** Il volume di vista 3D viene infine proiettato in 2D tramite trasformazioni **Proiettive** (Ortogonali o Prospettiche).

---

## 3. Basi e Spazi Vettoriali
Per rappresentare matematicamente lo spazio 3D, utilizziamo l'algebra vettoriale lineare.

### Base Vettoriale
Uno spazio vettoriale è definito da una **Base**, ovvero tre vettori linearmente indipendenti $\{v_1, v_2, v_3\}$.
Un vettore generico $w$ si esprime univocamente come combinazione lineare della base:

$$
w = a_1v_1 + a_2v_2 + a_3v_3
$$

I coefficienti scalari formano la rappresentazione matriciale del vettore:
$$
a = \begin{bmatrix} a_1 \\ a_2 \\ a_3 \end{bmatrix}
$$

---

## 4. Il Sistema di Riferimento (Frame)
Qui risiede il concetto teorico cruciale. Una base vettoriale definisce direzioni e distanze, ma **non ha una posizione** nello spazio (i vettori sono invarianti per traslazione).
Per definire la posizione di un punto, serve un **punto fisso di riferimento**: l'**Origine** ($P_0$).

### Definizione di Frame
Un Sistema di Riferimento (Frame) è definito da una quaterna composta da tre vettori base e un punto origine:

$$
\text{Frame} = \{v_1, v_2, v_3, P_0\}
$$

### L'Ambiguità Punto/Vettore
All'interno di un Frame, la rappresentazione matematica di punti e vettori differisce concettualmente:

* **Vettore ($w$):** È una differenza tra punti. Non dipende dall'origine.
    $$
    w = a_1v_1 + a_2v_2 + a_3v_3
    $$
* **Punto ($P$):** È definito come uno spostamento partendo dall'origine.
    $$
    P = P_0 + a_1v_1 + a_2v_2 + a_3v_3
    $$

Entrambi, però, sembrano usare gli stessi tre coefficienti $(a_1, a_2, a_3)$, creando un'ambiguità se usassimo solo vettori $3 \times 1$.

---

## 5. Coordinate Omogenee: La Soluzione Formale
Per risolvere l'ambiguità e rappresentare algebricamente la differenza tra punto e vettore, manipoliamo le equazioni precedenti rendendo esplicito il coefficiente dell'Origine $P_0$.

### La Derivazione Matematica
Riscriviamo le equazioni come prodotto scalare fittizio:

1.  **Vettore:** L'origine non c'è, quindi il suo coefficiente è **0**.
    $$
    w = a_1v_1 + a_2v_2 + a_3v_3 + \mathbf{0} \cdot P_0
    $$
2.  **Punto:** L'origine c'è, quindi il suo coefficiente è **1**.
    $$
    P = a_1v_1 + a_2v_2 + a_3v_3 + \mathbf{1} \cdot P_0
    $$

### La 4ª Coordinata ($w$)
Da questa distinzione nasce la rappresentazione a **4 coordinate omogenee**. Ogni entità geometrica è rappresentata da una matrice colonna $4 \times 1$:

**Rappresentazione del Punto ($w=1$):**
$$
\mathbf{p} = \begin{bmatrix} x \\ y \\ z \\ 1 \end{bmatrix}
$$

**Rappresentazione del Vettore ($w=0$):**
$$
\mathbf{v} = \begin{bmatrix} x \\ y \\ z \\ 0 \end{bmatrix}
$$

### Conclusione
Questo formalismo giustifica l'uso delle matrici $4 \times 4$ in Computer Graphics:
* Permette di trattare punti e vettori in modo unificato.
* Permette di includere la **Traslazione** (che agisce sui punti ma non sui vettori) come una semplice moltiplicazione matriciale.
## Coordinate Omogenee e Matrici

## 1. La Matrice di Trasformazione 4x4 Generica
In Computer Graphics, una trasformazione affine generica combina una trasformazione lineare (rotazione/scala) e una traslazione in un'unica struttura:

$$
M = 
\begin{bmatrix}
r_{00} & r_{01} & r_{02} & t_x \\
r_{10} & r_{11} & r_{12} & t_y \\
r_{20} & r_{21} & r_{22} & t_z \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

Dove:
- La sottomatrice $3 \times 3$ (gli elementi $r$) definisce **Rotazione** e **Scalamento**.
- L'ultima colonna $(t_x, t_y, t_z)$ definisce la **Traslazione**.
- L'ultima riga $[0, 0, 0, 1]$ serve a mantenere la proprietà affine.

---

## 2. Perché la "w" è magica: Punti vs Vettori
Matematicamente, la distinzione tra un punto nello spazio e un vettore direzione è gestita dalla quarta coordinata $w$.

### Caso A: Trasformazione di un Punto ($w=1$)
Il punto viene ruotato E traslato. La traslazione ha effetto.

$$
\begin{bmatrix}
r_{00} & r_{01} & r_{02} & t_x \\
r_{10} & r_{11} & r_{12} & t_y \\
r_{20} & r_{21} & r_{22} & t_z \\
0 & 0 & 0 & 1
\end{bmatrix}
\cdot
\begin{bmatrix} x \\ y \\ z \\ 1 \end{bmatrix}
=
\begin{bmatrix} 
(r \cdot P) + t_x \cdot 1 \\ 
(r \cdot P) + t_y \cdot 1 \\ 
(r \cdot P) + t_z \cdot 1 \\ 
1 
\end{bmatrix}
$$

### Caso B: Trasformazione di un Vettore ($w=0$)
Il vettore (es. una normale) viene SOLO ruotato. La traslazione viene annullata dalla moltiplicazione per zero.

$$
\begin{bmatrix}
r_{00} & r_{01} & r_{02} & t_x \\
r_{10} & r_{11} & r_{12} & t_y \\
r_{20} & r_{21} & r_{22} & t_z \\
0 & 0 & 0 & 1
\end{bmatrix}
\cdot
\begin{bmatrix} x \\ y \\ z \\ 0 \end{bmatrix}
=
\begin{bmatrix} 
(r \cdot V) + t_x \cdot 0 \\ 
(r \cdot V) + t_y \cdot 0 \\ 
(r \cdot V) + t_z \cdot 0 \\ 
0 
\end{bmatrix}
$$

---

## 3. Matrice di Cambio Base (Camera Matrix)
Quando posizioniamo una camera, stiamo definendo un sistema di coordinate locale rispetto al mondo. La matrice è composta dai vettori unitari della base locale ($\mathbf{u}, \mathbf{v}, \mathbf{n}$) e dalla posizione dell'origine $O$.

$$
M_{camera} = 
\begin{bmatrix}
u_x & v_x & n_x & O_x \\
u_y & v_y & n_y & O_y \\
u_z & v_z & n_z & O_z \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

- $\mathbf{u}$ (Vettore Right/X locale)
- $\mathbf{v}$ (Vettore Up/Y locale)
- $\mathbf{n}$ (Vettore Forward/Z locale - *spesso invertito in -Z*)
- $\mathbf{O}$ (Posizione della camera nel mondo)

## 4. Le Matrici Fondamentali (Affine)

Ecco le matrici standard da utilizzare nei calcoli. Ricorda che stiamo usando la convenzione **Column-Major** (vettori colonna moltiplicati a destra della matrice).

### A. Traslazione (Translation)
Sposta un punto aggiungendo valori alle coordinate. È l'unica trasformazione che sfrutta la quarta colonna.

$$
T(t_x, t_y, t_z) = 
\begin{bmatrix}
1 & 0 & 0 & t_x \\
0 & 1 & 0 & t_y \\
0 & 0 & 1 & t_z \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

**Inversa:** $T^{-1}(t) = T(-t)$

### B. Scalamento (Scaling)
Moltiplica le coordinate lungo gli assi.
- Se $s > 1$: Ingrandimento.
- Se $0 < s < 1$: Riduzione.
- Se $s < 0$: Riflessione (specchio).

$$
S(s_x, s_y, s_z) = 
\begin{bmatrix}
s_x & 0 & 0 & 0 \\
0 & s_y & 0 & 0 \\
0 & 0 & s_z & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

**Inversa:** $S^{-1}(s) = S(1/s)$

### C. Rotazioni (Rotations)
Ruotano un punto di un angolo $\theta$ (in radianti) attorno a un asse, seguendo la regola della mano destra (senso antiorario).

**Rotazione su X:**
$$
R_x(\theta) = 
\begin{bmatrix}
1 & 0 & 0 & 0 \\
0 & \cos\theta & -\sin\theta & 0 \\
0 & \sin\theta & \cos\theta & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

**Rotazione su Y:** (Attenzione ai segni!)
$$
R_y(\theta) = 
\begin{bmatrix}
\cos\theta & 0 & \sin\theta & 0 \\
0 & 1 & 0 & 0 \\
-\sin\theta & 0 & \cos\theta & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

**Rotazione su Z:**
$$
R_z(\theta) = 
\begin{bmatrix}
\cos\theta & -\sin\theta & 0 & 0 \\
\sin\theta & \cos\theta & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

**Inversa:** $R^{-1}(\theta) = R(-\theta) = R^T(\theta)$ (Matrice Trasposta).

---

## 5. Composizione di Trasformazioni
La potenza delle matrici 4x4 sta nella possibilità di combinare sequenze complesse in un'unica matrice $M$.

### Regola dell'Ordine (Right-to-Left)
Se vogliamo applicare a un punto $P$ prima una rotazione $R$, poi una traslazione $T$ e infine uno scalamento $S$, l'ordine matematico è:

$$
P_{finale} = S \cdot T \cdot R \cdot P_{iniziale}
$$

La matrice composta sarà:
$$M_{totale} = S \cdot T \cdot R$$

### Non-Commutatività
L'ordine è fondamentale. Scambiare le matrici cambia il risultato geometrico.
$$T \cdot R \neq R \cdot T$$

- **$T \cdot R$ (Ruota poi Trasla):** Ruoto l'oggetto attorno all'origine (0,0,0), POI lo sposto dove deve andare. È il modo standard per posizionare oggetti orientati.
- **$R \cdot T$ (Trasla poi Ruota):** Sposto l'oggetto, POI ruoto tutto il mondo (oggetto incluso) attorno all'origine. L'oggetto orbiterà attorno al centro del mondo.

### Esempio Classico: Rotazione su Pivot Arbitrario
Per ruotare un oggetto attorno a un punto $P_f$ (invece che attorno all'origine), devo comporre tre matrici:
1. Traslo per portare il pivot all'origine: $T(-P_f)$
2. Ruoto: $R(\theta)$
3. Ritraslo indietro: $T(P_f)$

$$M_{pivot} = T(P_f) \cdot R(\theta) \cdot T(-P_f)$$