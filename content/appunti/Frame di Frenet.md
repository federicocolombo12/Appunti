
Finora abbiamo calcolato la **Posizione** $P(u)$ di un oggetto nel tempo. Tuttavia, un corpo rigido nello spazio ha **6 Gradi di Libertà** (6-DOF):
* 3 per la Posizione ($x, y, z$).
* 3 per l'Orientamento (Roll, Pitch, Yaw).

Se muoviamo un oggetto lungo una spline modificando solo $x,y,z$, l'oggetto traslerà rimanendo parallelo agli assi del mondo (come una scatola che scivola sul ghiaccio senza girarsi).
**Obiettivo:** Vogliamo che l'oggetto si orienti "lungo la curva", come un'auto che segue la strada o un vagone sulle montagne russe.

---

# Frenet Frame
Il metodo più automatico per orientare un oggetto basa i calcoli esclusivamente sulla geometria della curva stessa. Costruiamo un sistema di coordinate locale ortogonale $(T, N, B)$ per ogni punto della curva.

### Calcolo dei Vettori
Sfruttiamo le derivate della curva $P(u)$:

1.  **Vettore Tangente ($T$):** Indica la direzione del movimento (velocità).
    $$T(u) = \frac{P'(u)}{||P'(u)||}$$
    *(È la derivata prima normalizzata).*

2.  **Vettore Binormale ($B$):** È ortogonale al piano osculatore (il piano su cui giace la curva in quel punto).
    $$B(u) = \frac{P'(u) \times P''(u)}{||P'(u) \times P''(u)||}$$
    *(È il prodotto vettoriale tra derivata prima e seconda).*

3.  **Vettore Normale ($N$):** Punta verso il centro di curvatura (centripeto).
    $$N(u) = B(u) \times T(u)$$

### Matrice di Orientamento
Questi tre vettori formano la matrice di rotazione dell'oggetto:
$$
R_{Frenet} = \begin{bmatrix} 
T_x & N_x & B_x & 0 \\
T_y & N_y & B_y & 0 \\
T_z & N_z & B_z & 0 \\
0 & 0 & 0 & 1 
\end{bmatrix}
$$

---

## 3. I Problemi del Frenet Frame
Nonostante l'eleganza matematica, il Frenet Frame è spesso inutilizzabile in animazione reale per due gravi difetti:

### A. Il Problema della Curvatura Nulla (Undefined Normal)
Il vettore Binormale dipende dal prodotto vettoriale $P' \times P''$.
* Se la curva è una **linea retta**, la derivata seconda $P''(u)$ è zero (o parallela alla prima).
* Il prodotto vettoriale diventa nullo: $0, 0, 0$.
* Non è possibile normalizzare un vettore nullo $\rightarrow$ **Il sistema di coordinate scompare (Nan/Crash)**.
* *Risultato:* L'oggetto perde improvvisamente l'orientamento nei tratti rettilinei.

### B. Il Problema del "Flipping" (Discontinuità)
Nei punti di flesso (dove la curva cambia concavità, ad esempio in una "S"), il vettore curvatura $P''(u)$ passa istantaneamente da una parte all'altra della tangente.
* Il vettore Normale ruota istantaneamente di 180°.
* *Risultato:* L'oggetto compie uno scatto violento (flip) capovolgendosi a testa in giù in un solo frame.

---

## 4. La Soluzione: Center of Interest (COI) o "LookAt"
Per risolvere l'instabilità del Frenet Frame, introduciamo un vincolo esterno: il **COI (Center of Interest)**.
Invece di calcolare l'orientamento basandosi solo sulla curva, diciamo all'oggetto (o alla camera): *"Stai sulla curva, ma guarda verso QUEL punto"*.

### Algoritmo LookAt
Dati:
* $P(u)$: Posizione dell'oggetto (Eye).
* $COI$: Posizione del punto da guardare (Target).
* $V_{up}$: Un vettore "Up" approssimativo del mondo (di solito l'asse Y: $[0, 1, 0]$).

Costruiamo il frame $(u, v, n)$ o $(Side, Up, Forward)$:

1.  **Vettore Vista ($n$ o Forward):**
    $$n = \frac{COI - P(u)}{||COI - P(u)||}$$

2.  **Vettore Lato ($u$ o Side):**
    Calcoliamo un vettore ortogonale al piano formato dalla vista e dall'Up del mondo.
    $$u = \frac{n \times V_{up}}{||n \times V_{up}||}$$

3.  **Vettore Alto Reale ($v$ o Up):**
    Ricalcoliamo l'Up affinché sia perfettamente ortogonale agli altri due (l'Up del mondo non è quasi mai perpendicolare alla vista).
    $$v = n \times u$$

> **Nota:** Questo sistema non soffre dei problemi del Frenet Frame perché l'Up Vector è fissato esternamente e non dipende dalla curvatura della spline.

---

## 5. Influenza sulla Camera
L'approccio COI è lo standard de facto per l'animazione delle telecamere (Camera Path).

* **Disaccoppiamento:** Permette di separare la traiettoria (dove sta la camera) dall'inquadratura (cosa guarda la camera).
* **Regia Dinamica:**
    * *Esempio:* La camera vola lungo una spline complessa (montagne russe) ma il COI è fissato sul volto del protagonista seduto nel vagone.
    * La camera ruoterà fluidamente per mantenere il soggetto al centro, indipendentemente dalle curve del binario.
* **Problema Residuo (Gimbal Lock/Zenith):** Se la camera guarda esattamente verso l'alto o verso il basso (parallela al $V_{up}$ mondiale), il prodotto vettoriale $n \times V_{up}$ diventa nullo. In quei casi rari, la camera può "scattare".

### Pseudocodice LookAt

```cpp
Matrix4x4 LookAt(Vector3 eye, Vector3 target, Vector3 worldUp) {
    Vector3 forward = Normalize(target - eye);
    Vector3 side = Normalize(CrossProduct(forward, worldUp));
    Vector3 up = CrossProduct(side, forward);
    
    // Costruzione matrice (Row-Major o Column-Major dipende dal motore)
    return Matrix4x4(
        side.x,    up.x,    -forward.x,   0,
        side.y,    up.y,    -forward.y,   0,
        side.z,    up.z,    -forward.z,   0,
        0,         0,       0,            1
    );
}