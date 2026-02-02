La deformazione non è solo un effetto visivo, ma uno strumento narrativo fondamentale nella [[Computer Animation]].
* **Obiettivo:** Aggiungere le nozioni di *malleabilità* e *densità* agli oggetti.
* **Espressività:** Un corpo flessibile comunica meglio l'azione e la reazione (pensate a un personaggio che salta o impatta al suolo).
* **Tecniche:**
    * **Scalamento non uniforme:** Usato per semplici operazioni di [[Squash & Stretch]] (schiacciamento e allungamento).
    * **Trasformazioni non affini:** Per distorsioni più complesse che alterano la geometria interna.

---

## 1. Deformazione tramite Spostamento dei Vertici
Un metodo semplice ("brute force") per deformare una mesh è spostare i suoi vertici propagando il movimento.

### Il concetto di Seed Vertex
1.  Si seleziona un **Seed Vertex** (vertice seme) o un gruppo di vertici.
2.  Si sposta questo vertice.
3.  Lo spostamento si **propaga** ai vertici adiacenti lungo la superficie.
4.  L'effetto viene **attenuato** in base alla distanza dal seed vertex.
![[Pasted image 20260202094532.png]]
### Funzione di Attenuazione $S(i)$
Per evitare che la mesh si "rompa" o appaia innaturale, lo spostamento deve decrescere gradualmente.
* **$i$**: La distanza topologica (numero di lati/edges) dal seed vertex.
* **$n$**: Il raggio d'azione (range) definito dall'utente (oltre $n$ lati, nessun vertice si muove).
* **$k$**: Fattore di controllo della curva di attenuazione ($k \ge 0$).

La formula matematica per il fattore di scala dello spostamento è:

$$S(i) = 1.0 - \left( \frac{i}{n+1} \right)^{k+1}$$

* Se $i = 0$ (seed vertex), $S(0) = 1.0$ (spostamento massimo).
* Se $i > n$, $S(i)$ viene considerato $0$.

---

## 2. Free-Form Deformation (FFD)
La FFD è una tecnica più avanzata e potente perché **deforma lo spazio** in cui l'oggetto è immerso, non l'oggetto direttamente.
* **Analogia:** Immaginate di immergere l'oggetto in un blocco di gelatina trasparente e poi deformare la gelatina. L'oggetto all'interno seguirà la deformazione.
* **Vantaggio:** Indipendente dalla complessità della mesh (funziona su pochi poligoni o milioni di poligoni).

### Deformazione di Griglie 2D
Partiamo dal caso semplificato: una griglia planare.
![[Pasted image 20260202094608.png]]
#### Sistema Locale vs Sistema Globale
Per applicare la FFD, dobbiamo mappare le coordinate del "Mondo" (Globali) in coordinate della "Griglia" (Locali).

1.  **Coordinate Globali ($X, Y$):** La posizione assoluta del vertice nello spazio.
2.  **Coordinate Locali ($S, T$):** La posizione normalizzata all'interno della griglia indeformata.
    * $S$ e $T$ variano solitamente tra $0$ e $1$.

#### Allineamento e Mappatura
Supponiamo di avere un oggetto (es. un triangolo) inscritto in una griglia quadrata definita da:
* $X_{min}, X_{max}$
* $Y_{min}, Y_{max}$

Per ogni vertice $P$ dell'oggetto con coordinate globali $(P_x, P_y)$, calcoliamo le coordinate locali $(s, t)$:

$$s = \frac{P_x - X_{min}}{X_{max} - X_{min}}$$
$$t = \frac{P_y - Y_{min}}{Y_{max} - Y_{min}}$$

> **Nota:** Questi valori $(s, t)$ sono "congelati". Rappresentano dove si trova il vertice *rispetto* alla griglia. Non cambieranno mai, anche se deformiamo la griglia.

---

### Calcolo delle Nuove Posizioni (Deformazione)
Quando deformiamo la griglia, spostiamo i suoi **Punti di Controllo** ($P_{ij}$).
La nuova posizione $P'$ di un vertice dell'oggetto si ottiene interpolando i nuovi punti di controllo usando i valori $(s, t)$ originali.

Per una griglia semplice (interpolazione bilineare), la formula è:

$$P'(s,t) = P_{00}(1-s)(1-t) + P_{10} \cdot s \cdot (1-t) + P_{01} \cdot (1-s) \cdot t + P_{11} \cdot s \cdot t$$

Dove:
* $P_{00}, P_{10}, P_{01}, P_{11}$ sono le nuove posizioni dei 4 angoli della cella della griglia che contiene il punto.

---

## Esercizio Pratico (Slide 11)
**Problema:** Determinare le nuove coordinate del vertice $A$ nel caso la griglia venga deformata.
![[Pasted image 20260202094858.png]]
### Dati (Scenario Tipico)
1.  **Situazione Iniziale (Griglia indeformata):**
    * La griglia è un quadrato $20 \times 20$ (esempio). Origine in $(0,0)$.
    * Quindi coordinate globali di A: $(24,5, 15,3)$.
    * Estremi griglia: $X \in [20, 28], Y \in [12, 16]$.

2.  **Passo 1: Calcolo Coordinate Locali ($s,t$)**
    * $s = (24,5 - 20) / (28 - 20) = 0.5625$
    * $t = (15.3 - 12) / (16 - 12) =0.825$
    * Il punto $A$ si trova a $(0.5, 0.5)$ nello spazio parametrico.

3.  **Situazione Deformata (Griglia modificata):**
    * Supponiamo che i vertici superiori della griglia vengano allargati (effetto trapezio).
    * $P_{00} = (0,0)$ (invariato)
    * $P_{10} = (20,0)$ (invariato)
    * $P_{01} = (-10, 20)$ (spostato a sinistra)
    * $P_{11} = (30, 20)$ (spostato a destra)

4.  **Passo 2: Calcolo della nuova posizione $A'$**
    Applichiamo l'interpolazione bilineare con $s=0.5, t=0.5$:

    $$A' = P_{00}(0.5)(0.5) + P_{10}(0.5)(0.5) + P_{01}(0.5)(0.5) + P_{11}(0.5)(0.5)$$
    $$A' = 0.25 \cdot [P_{00} + P_{10} + P_{01} + P_{11}]$$

    Sostituendo i valori:
    * $x' = 0.25 \cdot (0 + 20 - 10 + 30) = 0.25 \cdot 40 = 10$
    * $y' = 0.25 \cdot (0 + 0 + 20 + 20) = 0.25 \cdot 40 = 10$

    *Risultato in questo caso specifico:* Il punto $A'$ rimane a $(10,10)$ perché la deformazione era simmetrica rispetto al centro.
    *(N.B. Se la deformazione non fosse simmetrica, A si sposterebbe seguendo la "gelatina").*

### Concetto Chiave per l'Esame
In FFD, la complessità geometrica dell'oggetto non influenza il costo di calcolo della deformazione dei punti di controllo, ma solo il costo finale di ricalcolo dei vertici ($P'$).