Le trasformazioni geometriche sono operazioni matematiche che permettono di modificare la posizione, l'orientamento e la dimensione degli oggetti nello spazio 3D. In CG, queste operazioni vengono eseguite tramite il **prodotto tra matrici e vettori**.

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