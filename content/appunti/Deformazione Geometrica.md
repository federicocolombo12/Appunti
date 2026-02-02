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
# Esercizio Pratico: Deformazione FFD 2D (Caso Personalizzato)

## 1. Dati del Problema
Analizziamo la configurazione iniziale fornita.

### Geometria Iniziale (Bind Pose)
* **Griglia:** Rettangolo definito da $X \in [20, 28]$ e $Y \in [12, 16]$.
    * Larghezza ($W$) = $28 - 20 = 8$
    * Altezza ($H$) = $16 - 12 = 4$
* **Punto $A$ (Originale):** $(24.5, 15.3)$

### Punti di Controllo Iniziali ($P_{ij}$)
La griglia è definita dai 4 vertici:
* $P_{00}$ (Basso-Sx): $(20, 12)$
* $P_{10}$ (Basso-Dx): $(28, 12)$
* $P_{01}$ (Alto-Sx): $(20, 16)$
* $P_{11}$ (Alto-Dx): $(28, 16)$

---

## 2. Passo 1: Mappatura (Coordinate Locali)
Calcoliamo la posizione parametrica di $A$ rispetto alla griglia. Queste coordinate $(s,t)$ sono "congelate" e non cambieranno durante la deformazione.

$$s = \frac{A_x - X_{min}}{X_{max} - X_{min}} = \frac{24.5 - 20}{8} = \frac{4.5}{8} = \mathbf{0.5625}$$

$$t = \frac{A_y - Y_{min}}{Y_{max} - Y_{min}} = \frac{15.3 - 12}{4} = \frac{3.3}{4} = \mathbf{0.825}$$

*Verifica:* Il punto si trova nella metà destra ($s > 0.5$) e nella parte alta ($t > 0.8$) della griglia.

---

## 3. Passo 2: Definizione della Deformazione
Applicando la deformazione visibile in figura (effetto trapezio), modifichiamo le posizioni dei Punti di Controllo ($P'_{ij}$).
*Ipotesi:* La base rimane ferma, la cima si allarga di 2 unità per lato.

* **$P'_{00}$**: $(20, 12)$ *(Invariato)*
* **$P'_{10}$**: $(28, 12)$ *(Invariato)*
* **$P'_{01}$**: $(18, 16)$ *(Spostato a Sinistra di 2: $20 - 2$)*
* **$P'_{11}$**: $(30, 16)$ *(Spostato a Destra di 2: $28 + 2$)*

---

## 4. Passo 3: Calcolo Nuova Posizione $A'$
Utilizziamo l'**Interpolazione Bilineare** per trovare le nuove coordinate globali.

**Formula:**
$$A'(s,t) = P'_{00}(1-s)(1-t) + P'_{10} \cdot s \cdot (1-t) + P'_{01} \cdot (1-s) \cdot t + P'_{11} \cdot s \cdot t$$

### Calcolo dei Pesi
Calcoliamo quanto ogni vertice della griglia influenza il punto $A$:

1.  **Peso Basso-Sx** $(1-s)(1-t) = (0.4375)(0.175) \approx \mathbf{0.07656}$
2.  **Peso Basso-Dx** $s(1-t) = (0.5625)(0.175) \approx \mathbf{0.09844}$
3.  **Peso Alto-Sx** $(1-s)t = (0.4375)(0.825) \approx \mathbf{0.36094}$
4.  **Peso Alto-Dx** $st = (0.5625)(0.825) \approx \mathbf{0.46406}$

> **Nota:** La somma dei pesi deve fare sempre 1. ($0.076 + 0.098 + 0.361 + 0.464 \approx 1.0$).

### Calcolo Coordinata X'
Moltiplichiamo le X dei nuovi punti di controllo per i rispettivi pesi:
$$X' = 20(0.07656) + 28(0.09844) + 18(0.36094) + 30(0.46406)$$
$$X' = 1.5312 + 2.7563 + 6.4969 + 13.9218$$
$$X' = \mathbf{24.70625}$$

### Calcolo Coordinata Y'
Moltiplichiamo le Y dei nuovi punti di controllo per i rispettivi pesi:
$$Y' = 12(0.07656) + 12(0.09844) + 16(0.36094) + 16(0.46406)$$
$$Y' = 12(0.175) + 16(0.825)$$ *(Raggruppando per righe, dato che le Y sono uguali a due a due)*
$$Y' = 2.1 + 13.2$$
$$Y' = \mathbf{15.3}$$

---

## 5. Risultato Finale
Il punto deformato $A'$ si trova alle coordinate:

# $$A' \approx (24.71, 15.3)$$

### Interpretazione
* **Coordinata X (24.5 -> 24.71):** Il punto si è spostato verso destra. Questo è corretto perché si trovava nella parte destra della griglia ($s > 0.5$) che è stata "stirata" verso l'esterno. Essendo però molto in alto ($t=0.825$), risente fortemente dell'allargamento della cima.
* **Coordinata Y (15.3 -> 15.3):** La coordinata Y non è cambiata. Questo accade perché i punti di controllo non si sono spostati verticalmente (la base è rimasta a Y=12 e la cima a Y=16).