La simulazione di corpi rigidi (Rigid Body Dynamics) è lo standard industriale per creare scene dinamiche realistiche in giochi e film.
Mentre un sistema particellare è facile da calcolare, non può rappresentare oggetti solidi perché manca del concetto di **orientamento** e **volume**.

Un Corpo Rigido è un oggetto ideale che:
* Ha una massa e un volume.
* **Non si deforma mai:** La distanza $|p_i - p_j|$ tra due punti qualsiasi dell'oggetto è costante $\forall t$.
* Ha **6 Gradi di Libertà (6-DOF):**
    * 3 per la Traslazione ($x, y, z$).
    * 3 per la Rotazione (orientamento nello spazio).

## Il Ciclo di Simulazione (Simulation Loop)
Per animare questi corpi, il motore fisico esegue un ciclo continuo (loop) ad ogni frame o sottostep temporale.
![[Screenshot 2026-02-01 alle 19.11.34.png]]

**Fasi del Ciclo:**
1.  **Calcolo delle Forze:** Si sommano tutte le forze agenti (gravità, vento, spinte).
2.  **Integrazione:** Si usano le leggi di Newton per aggiornare velocità e posizioni ($F \to a \to v \to p$).
3.  **Collision Detection:** Si verifica se gli oggetti si compenetrano.
4.  **Collision Resolution:** Si applicano impulsi per risolvere le compenetrazioni e gestire i rimbalzi.
5.  **Rendering:** Si disegna la scena aggiornata.

---

## 3. Cinematica del Punto Materiale (Ripasso)
Prima di far ruotare le cose, ricordiamo come si muove un singolo punto nello spazio.
La sua **Legge Oraria** è definita dalla funzione posizione $x(t)$.

* **Posizione:** $x(t) : \mathbb{R} \to \mathbb{R}^3$
* **Velocità Lineare ($v$):** La derivata prima della posizione rispetto al tempo.
    $$v(t) = \dot{x}(t) = \frac{d}{dt}x(t)$$
* **Accelerazione Lineare ($a$):** La derivata prima della velocità (o seconda della posizione).
    $$a(t) = \dot{v}(t) = \ddot{x}(t)$$

---

## 4. Il Moto Circolare e la Velocità Angolare
Quando passiamo ai corpi rigidi, i punti non si muovono solo in linea retta, ma ruotano attorno a un centro (o asse).

### Matrice di Rotazione $R(t)$
L'orientamento di un oggetto cambia nel tempo. Matematicamente, questo è descritto da una matrice di rotazione $3 \times 3$, $R(t)$, che trasforma le coordinate locali in coordinate globali.

### Velocità Angolare ($\omega$)
La velocità con cui l'orientamento cambia è descritta dal vettore **Velocità Angolare** $\omega(t)$ (omega).
Questo vettore contiene due informazioni cruciali:
1.  **Direzione:** L'asse attorno al quale l'oggetto sta ruotando istantaneamente.
2.  **Modulo ($|\omega|$):** La velocità della rotazione in radianti al secondo.

### Relazione Fondamentale: $\dot{r}(t) = \omega(t) \times r(t)$
Come calcoliamo la velocità lineare $\dot{r}$ di un punto che sta ruotando?
Immaginiamo un punto $p$ su un oggetto che ruota attorno all'origine. Il vettore posizione è $r(t)$.
La sua velocità istantanea è data dal **prodotto vettoriale** tra la velocità angolare e il raggio (posizione):

$$\dot{r}(t) = \omega(t) \times r(t)$$

**Significato Fisico:**
* La velocità è perpendicolare sia all'asse di rotazione ($\omega$) sia al raggio vettore ($r$).
* Più il punto è lontano dall'asse (modulo di $r$ grande), più veloce si muove linearmente.
![[Screenshot 2026-02-01 alle 19.14.20.png]]

---

## 5. Cinematica del Corpo Rigido
Un corpo rigido combina i due moti visti sopra: **Trasla** (come un punto) e **Ruota** (come una sfera).

La posizione $p(t)$ di un qualsiasi punto arbitrario sull'oggetto rigido, al tempo $t$, è data da:

$$p(t) = x(t) + R(t)p_0$$

Dove:
* $p(t)$: Posizione del punto nel mondo globale (World Space).
* $x(t)$: Posizione del **Centro di Massa** o dell'origine dell'oggetto (componente traslazionale).
* $R(t)$: Matrice di rotazione corrente (componente rotazionale).
* $p_0$: Posizione originale del punto rispetto al centro dell'oggetto (Coordinate Locali o Body Space). Questo vettore $p_0$ è costante perché l'oggetto è rigido!

**La Complessità:**
Mentre per le particelle dovevamo trovare solo $x(t)$, per i corpi rigidi dobbiamo risolvere nel tempo sia $x(t)$ (facile) che $R(t)$ (difficile, perché coinvolge l'orientamento 3D).

---
**Collegamenti:** [[Prodotto Vettoriale]], [[Matrici di Trasformazione]], [[Coordinate Locali vs Globali]]
# Cinematica Avanzata e Dinamica del Corpo Rigido

## A. L'Evoluzione della Matrice di Rotazione $R(t)$
Abbiamo detto che un corpo ruota con velocità angolare $\omega(t)$. Ma come influenza questo la matrice di orientamento $R(t)$ istante per istante?

Sappiamo che per un singolo vettore $r$, la velocità è $\dot{r} = \omega \times r$.
Poiché le **colonne** della matrice di rotazione $R(t)$ sono vettori unitari che rappresentano gli assi locali dell'oggetto ($x', y', z'$), la stessa regola si applica a ciascuna colonna.

Possiamo scrivere la derivata temporale della matrice di rotazione come:

$$\dot{R}(t) = \omega(t)^* R(t)$$

Dove $\omega(t)^*$ è l'operatore matriciale del prodotto vettoriale (una matrice antisimmetrica costruita dal vettore $\omega$).
In termini più intuitivi: **La variazione dell'orientamento è il prodotto vettoriale tra la velocità angolare e l'orientamento attuale.**

---

## B. Posizione Globale di un Punto Arbitrario
Dato un punto $q$ solidale con l'oggetto (cioè un vertice della mesh che non si muove rispetto all'oggetto stesso), vogliamo trovare la sua posizione nello spazio globale $p(t)$.

La formula di trasformazione "Body to World" è:

$$p(t) = R(t)q + x(t)$$

Dove:
* $x(t)$: È la posizione globale dell'origine dell'oggetto (solitamente il Centro di Massa) in quel momento.
* $R(t)q$: È il punto ruotato, ma ancora relativo all'origine dell'oggetto.
* $q$: Coordinate locali del punto (costanti).

**Velocità del punto:**
Derivando l'equazione sopra, otteniamo la velocità totale del punto nello spazio:
$$\dot{p}(t) = \omega(t) \times (R(t)q) + v(t)$$
(Velocità tangenziale dovuta alla rotazione + Velocità lineare del corpo).

---

## C. Il Centro di Massa (Center of Mass - CM)
Perché in fisica non usiamo un punto a caso come origine ($0,0,0$ locale), ma calcoliamo sempre il Centro di Massa?

**Definizione:**
Il CM è la media pesata della posizione di tutte le particelle che compongono l'oggetto.
* Per un sistema discreto (punti): $x_{cm} = \frac{\sum m_i p_i}{\sum m_i}$
* Per un corpo continuo (integrale): $x_{cm} = \frac{\int \rho(r) r dr}{M}$
    (dove $\rho(r)$ è la densità nel punto $r$ e $M$ è la massa totale).

**Il Ruolo Cruciale in Computer Graphics:**
Scegliere il CM come origine del sistema di riferimento locale ($x(t)$) ci permette di **disaccoppiare** il moto:
1.  La **Traslazione** del corpo è descritta puramente dal movimento del CM come se fosse una particella.
2.  La **Rotazione** avviene attorno al CM.
Se usassimo un altro punto, la rotazione introdurrebbe delle traslazioni parassite, complicando enormemente le equazioni.

---

## D. Dinamica: Forze e Momenti

Ora passiamo dalle descrizioni del movimento (Cinematica) alle cause del movimento (Dinamica).

### 1. Forza Lineare ($F$)
È l'agente che modifica il moto traslatorio.
Secondo la Seconda Legge di Newton, la forza totale agente sul CM determina la sua accelerazione:

$$F_{tot} = \sum F_i = M \ddot{x}(t)$$

* Non importa dove la forza colpisce l'oggetto: l'effetto sull'accelerazione lineare del CM è lo stesso (una spinta sul bordo sposta il CM esattamente come una spinta al centro).
* L'accelerazione risultante è $a = F_{tot} / M$.

### 2. Momento di Torsione (Torque - $\tau$)
![[Screenshot 2026-02-01 alle 19.21.01.png | 400]]
Se la forza colpisce l'oggetto in un punto $p$ diverso dal CM ($x$), si genera un momento che tende a far ruotare l'oggetto.
Il momento $\tau_i$ generato da una forza $F_i$ applicata nel punto $p_i$ è:

$$\tau_i = (p_i - x(t)) \times F_i$$

* $(p_i - x(t))$ è il "braccio" della forza.
* Il Momento Totale è la somma di tutti i momenti: $\tau_{tot} = \sum \tau_i$.
* Il Torque è la causa dell'accelerazione angolare.

# Quantità di Moto e Momento Angolare

## A. Quantità di Moto Lineare ($P$) e Conservazione
La quantità di moto (Linear Momentum) di un oggetto è definita come:
$$P(t) = M v(t)$$

**Legge di Conservazione:**
Dalla legge di Newton ($F = \dot{P}$), ne consegue che se la forza totale agente sul sistema è zero ($F_{tot} = 0$), allora $\dot{P} = 0$, il che significa che **$P(t)$ è costante**.
* Questo è fondamentale nelle collisioni: in un sistema isolato, la quantità di moto totale prima e dopo l'urto si conserva.

## B. Momento Angolare ($L$)
Il Momento Angolare (Angular Momentum) è l'analogo rotazionale di $P$.
$$L(t) = I(t) \omega(t)$$
Mentre $P$ è legato alla velocità lineare, $L$ è legato alla velocità angolare, ma la relazione è mediata da $I(t)$ (Tensore d'Inerzia).

**Dinamica del Momento Angolare:**
Analogamente alla forza lineare, la derivata del momento angolare è uguale al **Momento Torcente (Torque)** totale applicato:
$$\dot{L}(t) = \tau(t)$$
Se non ci sono momenti torcenti esterni ($\tau = 0$), il momento angolare si conserva (l'oggetto continua a ruotare indefinitamente sullo stesso asse).

---

# 8. Il Tensore d'Inerzia ($I$)

La relazione $L = I \omega$ non è una semplice moltiplicazione scalare. $I$ è una **Matrice $3 \times 3$**, chiamata Tensore d'Inerzia.
Essa descrive come la massa è distribuita attorno al centro di massa.

$$
I = \begin{bmatrix}
I_{xx} & I_{xy} & I_{xz} \\
I_{yx} & I_{yy} & I_{yz} \\
I_{zx} & I_{zy} & I_{zz}
\end{bmatrix}
$$

È una matrice simmetrica ($I_{xy} = I_{yx}$, ecc.).

## Calcolo dei Termini della Matrice
Come riempiamo questa matrice? Dipende se consideriamo l'oggetto come punti discreti o massa continua.

### 1. Caso Discreto (Sommatoria su masse puntiformi)
Se l'oggetto è composto da $N$ particelle con massa $m_k$ e posizione locale $r_k = (x_k, y_k, z_k)$:

* **Termini Diagonali (Momenti d'Inerzia):**
  Rappresentano la resistenza alla rotazione attorno agli assi principali.
  $$I_{xx} = \sum m_k (y_k^2 + z_k^2)$$
  $$I_{yy} = \sum m_k (x_k^2 + z_k^2)$$
  $$I_{zz} = \sum m_k (x_k^2 + y_k^2)$$

* **Termini Fuori Diagonale (Prodotti d'Inerzia):**
  Rappresentano gli squilibri nella distribuzione della massa (segno meno davanti!).
  $$I_{xy} = - \sum m_k (x_k y_k)$$
  $$I_{xz} = - \sum m_k (x_k z_k)$$
  *(e così via per gli altri)*

### 2. Caso Continuo (Integrale)
Se l'oggetto è solido, sostituiamo la sommatoria con un integrale volumetrico sulla densità $\rho$:
$$I_{xx} = \int_V \rho(r) (y^2 + z^2) dr$$

> **Nota:** Nella pratica della computer graphics, spesso si approssima il tensore d'inerzia usando la forma del **Bounding Box** o del **Convex Hull** dell'oggetto per semplificare i calcoli.

---

# 9. Equazione di Stato di un Corpo Rigido
![[Pasted image 20260209170317.png]]
Per simulare l'evoluzione del corpo rigido nel tempo, dobbiamo memorizzare le variabili che ne descrivono completamente la configurazione in un istante $t$. Queste variabili formano il vettore di stato $X(t)$.

## Cosa memorizziamo?
È prassi comune memorizzare la Quantità di Moto ($P, L$) invece delle velocità ($v, \omega$), perché $P$ ed $L$ sono grandezze conservate e le equazioni differenziali sono più semplici ($\dot{P}=F, \dot{L}=\tau$).

Il vettore di stato $X(t)$ contiene solitamente **12 o 13 componenti**:
1.  $x(t)$: Posizione (3 componenti).
2.  $R(t)$: Orientamento (Matrice $3x3$ o Quaternione, quindi 9 o 4 componenti).
3.  $P(t)$: Quantità di Moto Lineare (3 componenti).
4.  $L(t)$: Momento Angolare (3 componenti).

## Calcolo delle Grandezze Derivate
Dallo stato $X(t)$ possiamo calcolare le velocità (necessarie per aggiornare la posizione):
1.  **Velocità Lineare:** $v(t) = \frac{P(t)}{M}$
2.  **Velocità Angolare:** $\omega(t) = I(t)^{-1} L(t)$

## Il Problema del Tensore d'Inerzia Variabile
Qui sorge un problema critico:
Mentre l'oggetto ruota, la distribuzione della sua massa rispetto agli assi globali ($x,y,z$ del mondo) cambia continuamente. Quindi **$I(t)$ cambia ad ogni frame**, e invertire una matrice $3 \times 3$ ad ogni step è costoso.

### Soluzione: Coordinate del Corpo (Body Coordinates)
Calcoliamo il tensore d'inerzia **una volta sola** in coordinate locali (all'inizio della simulazione): chiamiamolo $I_{body}$. Questo è costante (l'oggetto è rigido!).

La relazione tra $I(t)$ (nel mondo) e $I_{body}$ (locale) è data dalla matrice di rotazione $R(t)$:

$$I(t) = R(t) I_{body} R(t)^T$$

E, cosa ancora più utile, la sua inversa:
$$I(t)^{-1} = R(t) I_{body}^{-1} R(t)^T$$

**Algoritmo di Aggiornamento:**
1.  Precalcoliamo $I_{body}^{-1}$ (una sola volta).
2.  Ad ogni step, usiamo l'orientamento attuale $R(t)$ per calcolare $I(t)^{-1}$ globale.
3.  Calcoliamo $\omega(t) = I(t)^{-1} L(t)$.

---
**Collegamenti:** [[Quaternioni]], [[Inversione di Matrice]], [[Integrazione Numerica]]