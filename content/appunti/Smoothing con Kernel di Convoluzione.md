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
Immagina la tua animazione non come una linea continua, ma come una sequenza discreta di campioni (frame).
Matematicamente, possiamo vedere la posizione $P(t)$ come una somma di **Funzioni a Gradino (Basis Functions)** scalate:
* Ogni frame è un "impulso".
* La convoluzione sovrappone una "finestra" (Kernel) su questi impulsi per mescolarli insieme.

### B. Il Kernel di Convoluzione
Il **Kernel** (o maschera di filtro) è un array di numeri (pesi) che definisce *come* vogliamo mescolare i punti.

**Gli Attributi del Kernel:**
1.  **Ampiezza (Width / Support):** Quanti punti vicini consideriamo?
    * Un kernel *largo* (es. 5 o 7 punti) produce uno smoothing molto forte (la curva diventa molto piatta).
    * Un kernel *stretto* (es. 3 punti) preserva più dettagli.
2.  **Pesi (Weights):** Quanto conta ogni vicino?
    * I pesi sono solitamente simmetrici rispetto al centro.
    * **Regola d'Oro:** La somma dei pesi deve essere **1** (altrimenti la curva si sposta nello spazio o cambia scala).

### C. Calcolo del Nuovo Punto
Per ottenere il punto levigato $P'_i$, sovrapponiamo il centro del kernel al punto originale $P_i$ e moltiplichiamo i vicini per i pesi corrispondenti.

Se abbiamo un Kernel di ampiezza $2k+1$ (dove $k$ è il raggio):

$$
P'_i = \sum_{j=-k}^{k} P_{i+j} \cdot w_j
$$

**Esempio Pratico (Kernel Gaussiano a 5 punti):**
Immaginiamo un kernel con pesi: $[0.1, \ 0.2, \ 0.4, \ 0.2, \ 0.1]$.
(Nota: $0.1+0.2+0.4+0.2+0.1 = 1.0$)

Il nuovo punto sarà:
$$
P'_i = (P_{i-2} \cdot 0.1) + (P_{i-1} \cdot 0.2) + (P_{i} \cdot 0.4) + (P_{i+1} \cdot 0.2) + (P_{i+2} \cdot 0.1)
$$

> **Vantaggio:** Il punto centrale ($P_i$) mantiene l'importanza maggiore (0.4), mentre i vicini lontani contano meno. Questo preserva la forma originale meglio di una media semplice.

---

