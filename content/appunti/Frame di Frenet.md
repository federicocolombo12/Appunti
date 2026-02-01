Fino ad ora abbiamo controllato la posizione $P(t)$. Ma un oggetto (o una camera) ha bisogno di un sistema di riferimento locale completo per sapere "dove sta guardando".
Questo sistema è definito da tre vettori ortogonali unitari: **u, v, w**.

* **Posizione:** $P(t)$
* **Orientamento:** Matrice di rotazione definita dai versori $\{u, v, w\}$.

---

## Frenet Frame
Il primo metodo per calcolare automaticamente questi vettori si basa esclusivamente sulla geometria della curva.

Definiamo i tre vettori $\{u, v, w\}$ sfruttando le derivate della curva:

1.  **Vettore w (Tangente):** È la direzione del movimento.
    $$w(t) = \frac{P'(t)}{||P'(t)||}$$

2.  **Vettore u (Binormale):** È perpendicolare al piano su cui giace la curva (piano osculatore) ed è il prodotto vettoriale tra derivata prima e seconda
    $$u(t) = \frac{P'(t) \times P''(t)}{||P'(t) \times P''(t)||}$$

3.  **Vettore v (Normale):** Completa la terna (punta verso il centro di curvatura) ed è il prodotto vettoriale dei due vettori precedenti
    $$v(t) = w(t) \times u(t)$$

### Matrice di Orientamento (Frenet)
$$
M_{Frenet} = \begin{bmatrix} 
u_x & v_x & w_x & 0 \\
u_y & v_y & w_y & 0 \\
u_z & v_z & w_z & 0 \\
0 & 0 & 0 & 1 
\end{bmatrix}
$$

### I Problemi del Frenet Frame
1.  **Indefinito su rette:** Se la curva è dritta, $P''(t) = 0$. Il prodotto vettoriale per calcolare **u** diventa nullo ($0,0,0$) e il sistema collassa (non si può orientare l'oggetto).
2.  **Discontinuità (Flipping):** Nei punti di flesso (cambio di curvatura, es. in una "S"), il vettore accelerazione $P''(t)$ cambia lato istantaneamente. Il vettore **u** e **v** ruotano di 180° in un solo frame, capovolgendo l'oggetto.

---

## La Soluzione: Center of Interest (COI)
Per risolvere i problemi del Frenet Frame (instabilità), introduciamo un controllo esterno: il **COI** (punto da guardare).
Costruiamo la base $\{u, v, w\}$ combinando la posizione sulla curva e il vettore "Up" del mondo ($V_{up} = [0, 1, 0]$).

### Algoritmo di Costruzione (LookAt)

1.  **Vettore w (View Vector / Forward):**
    Calcoliamo la direzione dalla posizione $P$ verso il punto di interesse $COI$.
    $$w = \frac{COI - P}{||COI - P||}$$

2.  **Vettore u (Side Vector / Right):**
    Calcoliamo il vettore laterale come prodotto vettoriale tra la vista ($w$) e l'alto del mondo ($V_{up}$). Questo garantisce che l'orizzonte sia dritto.
    $$u = \frac{w \times V_{up}}{||w \times V_{up}||}$$

3.  **Vettore v (Up Vector Reale):**
    Ricalcoliamo il vettore "alto" locale affinché sia ortogonale agli altri due (l'$Up$ del mondo non è quasi mai perpendicolare a $w$).
    $$v = u \times w$$

