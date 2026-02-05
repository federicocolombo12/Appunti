In Computer Animation, non sempre lavoriamo con curve matematiche perfette (come le Spline) create da zero. Spesso importiamo dati dal mondo reale (es. **Motion Capture**).
Questi dati grezzi soffrono di **High Frequency Noise** (rumore ad alta frequenza):
* La traiettoria appare "nervosa", tremolante.
* Ci sono picchi improvvisi (spikes) non realistici per la fisica di un corpo pesante.

**Obiettivo:** Vogliamo "raccordare" questi punti bruschi per ottenere una curva morbida e fluida, senza perdere il movimento generale.
![[Pasted image 20260201122258.png]]
---

## 2. Lo Smoothing tramite Media (Moving Average)
Il metodo più intuitivo per ammorbidire una curva è la "Media Mobile".
Invece di usare la posizione esatta di un punto $P_i$, la sostituiamo con la **media** tra il punto stesso e i suoi vicini.
![[Pasted image 20260201122317.png]]

### Formula Base (Media a 3 punti)
Per ogni punto $P_i$ della traiettoria, calcoliamo la nuova posizione $P'_i$:

$$
P'_i = \frac{P_{i-1} + P_i + P_{i+1}}{3}
$$

* **Effetto:** I picchi vengono smussati perché "tirati" verso i vicini.
* **Limite:** Questo metodo è rigido. Tratta tutti i punti allo stesso modo (peso uguale = $1/3$).

---

## 3. Lo Smoothing con Kernel di Convoluzione
Per avere un controllo maggiore, generalizziamo il concetto di media introducendo la **Convoluzione**.
![[Pasted image 20260201122329.png]]
### A. Interpretazione del Segnale (Funzione a Gradini)
Immagina la tua animazione originale (i frame) non come punti, ma come una serie di **impulsi verticali** (come dei paletti piantati a terra) di altezza pari al valore del punto.
La **Convoluzione** funziona così:
1.  Prendiamo una forma geometrica (il **Kernel**) e ne mettiamo una copia sopra ogni "paletto" (frame).
2.  Scaliamo l'altezza del kernel in base all'altezza del paletto.
3.  Sommiamo tutte queste forme sovrapposte.

Il risultato è la curva rossa liscia che vedi nelle slide. La forma della curva finale dipende interamente dalla forma del Kernel che hai scelto.

## B. I Tre Kernel Fondamentali

![[Pasted image 20260201123038.png]]
### A. Box Filter (Filtro a Scatola)
* **Forma :** Un rettangolo (una linea orizzontale che cade a zero bruscamente).
* **Matematica:** Pesi tutti uguali. Es. `[0.33, 0.33, 0.33]`.
* **Significato:** È la **Media Semplice**.
    * *"Prendi i miei vicini e trattali esattamente come me."*
* **Difetto:** Non è molto naturale. Se un punto entra o esce dalla finestra di calcolo, la media cambia di colpo (discontinuità C1), creando scalettature impercettibili ma fastidiose.

### B. Tent Filter (Filtro a Tenda/Triangolo)
* **Forma nelle slide:** Un triangolo (parte da 0, sale al centro, scende a 0).
* **Matematica:** Pesi che decrescono linearmente. Es. `[0.25, 0.50, 0.25]`.
    * Il punto centrale (io) vale il doppio dei vicini.
* **Significato:** **Interpolazione Lineare**.
    * *"Io sono importante, i miei vicini un po' meno."*
* **Vantaggio:** Molto meglio del Box, garantisce continuità (C0), ma la derivata (velocità) può cambiare bruscamente sui picchi.

### C. Gaussian Filter (Filtro Gaussiano - La scelta migliore)
* **Forma nelle slide:** Una curva a campana (larga alla base, arrotondata in cima).
* **Matematica:** Pesi basati sulla funzione $e^{-x^2}$. Es. `[0.05, 0.25, 0.40, 0.25, 0.05]`.
    * Il peso scende dolcemente verso lo zero senza mai toccarlo bruscamente.
* **Significato:** È lo standard nell'animazione e nel cinema.
* **Perché si usa:** È l'unico filtro che garantisce che la curva risultante sia **liscia all'infinito** (C-infinito). Non introduce artefatti, non crea angoli, smussa in modo perfettamente organico.

---
### C. Calcolo del Nuovo Punto 
Per ottenere il punto levigato $P'_i$, sovrapponiamo il centro del kernel al punto originale $P_i$ e moltiplichiamo i vicini per i pesi corrispondenti. Se abbiamo un Kernel di ampiezza $2k+1$ (dove $k$ è il raggio): $$ P'_i = \sum_{j=-k}^{k} P_{i+j} \cdot w_j $$ **Esempio Pratico (Kernel Gaussiano a 5 punti):** Immaginiamo un kernel con pesi: $[0.1, \ 0.2, \ 0.4, \ 0.2, \ 0.1]$. (Nota: $0.1+0.2+0.4+0.2+0.1 = 1.0$) Il nuovo punto sarà: $$ P'_i = (P_{i-2} \cdot 0.1) + (P_{i-1} \cdot 0.2) + (P_{i} \cdot 0.4) + (P_{i+1} \cdot 0.2) + (P_{i+2} \cdot 0.1) $$
## C. Il Calcolo Pratico 
Come otteniamo il numero finale? Immagina di avere una "finestra scorrevole" (il Kernel) che sposti lungo la timeline.

**Esempio Numerico:**
Voglio calcolare la posizione smussata al **frame 10**.
Uso un Kernel a 3 punti (Tent): `Pesi = [0.25, 0.5, 0.25]`.

I punti originali sono:
* Frame 9: $x = 10$
* Frame 10: $x = 20$ (Picco brusco)
* Frame 11: $x = 10$

**Calcolo:**
1.  Sovrappongo il centro del Kernel (0.5) al Frame 10.
2.  I lati del Kernel (0.25) cadono sul Frame 9 e 11.
3.  Moltiplico e sommo:
    $$P'_{10} = (10 \cdot 0.25) + (20 \cdot 0.50) + (10 \cdot 0.25)$$
    $$P'_{10} = 2.5 + 10 + 2.5 = 15$$

**Risultato:** Il picco originale era **20**. Il punto smussato è **15**.
Il movimento è stato "tagliato" per renderlo più morbido.

---
