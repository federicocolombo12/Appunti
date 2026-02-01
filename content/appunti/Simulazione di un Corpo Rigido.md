La simulazione di corpi rigidi (Rigid Body Dynamics) è lo standard industriale per creare scene dinamiche realistiche in giochi e film.
Mentre un sistema particellare è facile da calcolare, non può rappresentare oggetti solidi perché manca del concetto di **orientamento** e **volume**.

Un Corpo Rigido è un oggetto ideale che:
* Ha una massa e un volume.
* **Non si deforma mai:** La distanza $|p_i - p_j|$ tra due punti qualsiasi dell'oggetto è costante $\forall t$.
* Ha **6 Gradi di Libertà (6-DOF):**
    * 3 per la Traslazione ($x, y, z$).
    * 3 per la Rotazione (orientamento nello spazio).

## 2. Il Ciclo di Simulazione (Simulation Loop)
Per animare questi corpi, il motore fisico esegue un ciclo continuo (loop) ad ogni frame o sottostep temporale.

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
* La velocità è perpendicolare sia all'asse di rotazione ($\omega$) sia al raggio vettorre ($r$).
* Più il punto è lontano dall'asse (modulo di $r$ grande), più veloce si muove linearmente.

---

## 5. Cinematica del Corpo Rigido (Putting it together)
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