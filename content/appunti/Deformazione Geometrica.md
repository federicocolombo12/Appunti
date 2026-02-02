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
![[Pasted image 20260202094317.png]]
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

#### Sistema Locale vs Sistema Globale
Per applicare la FFD, dobbiamo mappare le coordinate del "Mondo" (Globali) in coordinate della "Griglia" (Locali).

1.  **Coordinate Globali ($X, Y$):** La posizione assoluta del vertice nello spazio.
2.  **Coordinate Locali ($S, T$):** La posizione normalizzata all'interno della griglia indeformata.
    * $S$ e $T$ variano solitamente tra $0$ e $1$.

#### Allineamento e Mappatura (Slide 8 - Situazione Iniziale)
Supponiamo di avere un oggetto (es. un triangolo) inscritto in una griglia quadrata definita da:
* $X_{min}, X_{max}$
* $Y_{min}, Y_{max}$

Per ogni vertice $P$ dell'oggetto con coordinate globali $(P_x, P_y)$, calcoliamo le coordinate locali $(s, t)$:

$$s = \frac{P_x - X_{min}}{X_{max} - X_{min}}$$
$$ t = \frac{P_