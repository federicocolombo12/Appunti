

## 1. Fondamenti: La Trasformata
Una trasformata è un'operazione matematica che consente il passaggio da un dominio a un altro (es. dal dominio del Tempo/Spazio al dominio della Frequenza).
Matematicamente, per segnali discreti, corrisponde alla moltiplicazione tra un vettore (segnale) e una matrice di trasformazione.

$$\mathbf{F} = \mathbf{T} \cdot \mathbf{x}$$

Le trasformate scompongono il segnale in una somma pesata di **Funzioni Base** (es. sinusoidi).
* **Coefficienti:** I valori risultanti rappresentano i "pesi" (ampiezze) di queste funzioni base necessarie per ricostruire il segnale originale.
* **Tipologie comuni:** DFT, FFT, DCT, DWT, KLT.

---

## 2. La DCT (Discrete Cosine Transform)
![[Screenshot 2026-02-03 alle 18.09.16.png]]
Nel JPEG si utilizza la **DCT Bidimensionale (2D-DCT)**.
* **Funzione:** Converte i valori di pixel (Dominio Spaziale) in coefficienti di **Frequenza Spaziale**.
* **Perché la DCT?** Ha un'ottima proprietà di "compattazione dell'energia": concentra la maggior parte dell'informazione visiva in pochi coefficienti (basse frequenze), a cui l'occhio umano è più sensibile.


> [!INFO] Matrice delle Funzioni Base (8x8)
> La DCT scompone un blocco di immagine in 64 pattern (funzioni base), che vanno da una frequenza nulla (colore uniforme) a frequenze via via più alte (scacchiere fitte), come visibile nelle slide di riferimento (es. Slide 41).

---

## 3. La Pipeline di Compressione JPEG

### Fase 1: Conversione Spazio Colore (RGB $\to$ YUV)
Il primo passo sfrutta la fisiologia umana (maggiore sensibilità alla luminosità che al colore).
Si applica una **Trasformazione Lineare** per passare da RGB a YUV (o YCbCr):
* **Y:** Luminanza (scala di grigi).
* **U, V:** Crominanza (informazione colore).

### Fase 2: Sottocampionamento (Chroma Subsampling)
Poiché l'occhio è meno sensibile ai dettagli in U e V, questi canali vengono sottocampionati (riduzione della risoluzione spaziale).
* **Metodo:** Media dei valori di pixel adiacenti o decimazione.
* **Formati:** 4:2:2 (dimezzamento orizzontale) o 4:2:0 (dimezzamento tot.).
* **Nota:** Il canale Y viene lasciato invariato (massima risoluzione).

### Fase 3: Suddivisione in Blocchi (Splitting)
L'immagine (ogni canale separatamente) viene divisa in blocchi di **8x8 pixel**.
* Se l'immagine non è multipla di 8, viene riempita (padding).
* La DCT viene applicata indipendentemente su ogni blocco.



### Fase 4: Forward DCT (Trasformazione)
Ogni blocco 8x8 di valori $pixel(x,y)$ viene trasformato in una matrice 8x8 di coefficienti $F(u,v)$.
* **Coefficiente DC (0,0):** L'angolo in alto a sinistra. Rappresenta la media dei valori del blocco (frequenza zero). Ha il valore energetico più alto.
![[Screenshot 2026-02-03 alle 18.09.41.png|200]]
* **Coefficienti AC:** I restanti 63 valori. Rappresentano i dettagli a frequenza crescente (verso il basso a destra).

### Fase 5: Quantizzazione (Lossy Step)
Qui avviene la compressione vera e propria (con perdita di informazione). L'obiettivo è eliminare le alte frequenze che l'occhio non percepisce.

**Il Processo:**
Si divide la matrice dei coefficienti DCT per una **Quantization Table** ($Q$) predefinita e si arrotonda all'intero più vicino.

$$F_{quant}(u,v) = \text{Round} \left( \frac{F(u,v)}{Q(u,v)} \right)$$

* **Matrice Q:** Ha valori bassi in alto a sinistra (basse frequenze, conservate con precisione) e valori molto alti in basso a destra (alte frequenze).
* **Effetto:** La divisione per valori alti fa sì che molti coefficienti AC (dettagli fini) diventino **zero**.
* **Fattore di Qualità:** Scalando la tabella $Q$ si controlla la compressione:
    * *Scaling Basso:* Valori nella tabella piccoli $\to$ Meno zeri $\to$ Alta qualità, file grande.
    * *Scaling Alto:* Valori nella tabella grandi $\to$ Più zeri $\to$ Bassa qualità, file piccolo.

> [!WARNING] Risultato della Quantizzazione
> Dopo questa fase, la matrice risultante (principalmente sparsa, cioè piena di zeri nell'area delle alte frequenze) è pronta per essere codificata in modo efficiente (Zig-Zag, RLE, Huffman), di cui parleremo nel prossimo step.

![[Screenshot 2026-02-03 alle 18.10.49.png |300]]

Dopo la fase di quantizzazione (che è *lossy*), i dati vengono compressi ulteriormente utilizzando tecniche **Lossless** (senza perdita) basate sulla statistica e l'entropia dell'informazione.
![[Screenshot 2026-02-03 alle 18.11.13.png | 300]]
### Fase 6. Lettura a Zig-Zag (Linearizzazione)
La matrice quantizzata $8 \times 8$ deve essere convertita in un vettore monodimensionale ($1 \times 64$) per essere elaborata serialmente.

* **Obiettivo:** Raggruppare i coefficienti nulli (zeri).
    * Poiché la quantizzazione tende ad azzerare le alte frequenze (situate in basso a destra nella matrice), una lettura per righe classiche interromperebbe le sequenze di zeri.
    * La lettura a **Zig-Zag** scansiona la matrice partendo dalle basse frequenze (angolo in alto a sinistra) verso le alte frequenze.
* **Risultato:** Si ottiene un vettore in cui i valori significativi (non nulli) sono concentrati all'inizio, seguiti da una lunga sequenza continua di zeri alla fine.
![[Screenshot 2026-02-03 alle 18.11.29.png|200]]


---

### Fase 7. Run-Length Encoding (RLE)
Il vettore linearizzato viene compresso utilizzando una variante dell'RLE ottimizzata per i coefficienti AC (componenti alternate).

* **Logica:** Invece di memorizzare ogni zero singolarmente, si codificano le sequenze di zeri che precedono un valore non nullo.
* **Coppie (Skip, Value):**
    I dati vengono rappresentati come coppie di simboli:
    $$(Skip, Value)$$
    * **Skip:** Numero di zeri consecutivi prima del prossimo coefficiente non nullo.
    * **Value:** Il valore del coefficiente non nullo successivo.
* **EOB (End of Block):** Se i restanti coefficienti del vettore sono tutti zeri, si inserisce un marcatore speciale *EOB*, troncando di fatto la sequenza e risparmiando notevole spazio.

> [!EXAMPLE] Esempio RLE
> Vettore Zig-Zag: `52, 10, 0, 0, 3, 0, 0, 0, 0, -2, 0, 0, ... (tutti 0)`
> Codifica:
> * `(0, 52)` $\rightarrow$ 0 zeri prima del 52
> * `(0, 10)` $\rightarrow$ 0 zeri prima del 10
> * `(2, 3)`  $\rightarrow$ 2 zeri prima del 3
> * `(4, -2)` $\rightarrow$ 4 zeri prima del -2
> * `EOB`     $\rightarrow$ Fine blocco (tutto il resto è zero)

---

### Fase 8. Codifica di Huffman
L'ultimo passaggio trasforma le coppie generate dall'RLE in un flusso di bit (bitstream) finale, utilizzando la Codifica a Lunghezza Variabile (VLC).

* **Principio:** Assegnare codici binari brevi ai simboli più probabili (frequenti) e codici lunghi a quelli meno probabili.
* **Funzionamento:**
    1.  Si analizza la statistica delle frequenze di apparizione delle coppie `(Skip, Value)`.
    2.  Si costruisce un **Albero di Huffman** (o si usano tabelle standard predefinite nel JPEG).
    3.  Ogni coppia viene sostituita dalla corrispondente stringa di bit.
* **Efficienza:** Questo massimizza la densità di informazione, riducendo al minimo la dimensione finale del file su disco.

> [!SUMMARY] Riepilogo Pipeline JPEG
> RGB $\xrightarrow{\text{YUV}}$ Sottocampionamento $\xrightarrow{\text{4:2:0}}$ DCT $\xrightarrow{\text{Freq}}$ Quantizzazione $\xrightarrow{\text{Lossy}}$ Zig-Zag $\xrightarrow{\text{Vector}}$ RLE $\xrightarrow{\text{Pairs}}$ Huffman $\xrightarrow{\text{Bitstream}}$
# JPEG 2000 e Valutazione degli Algoritmi

## 1. JPEG 2000
Il JPEG 2000 è un'evoluzione dello standard JPEG che introduce un approccio matematico differente per ottenere prestazioni superiori e funzionalità avanzate.

### Caratteristiche Principali
* **Trasformata Wavelet (DWT):** Sostituisce la DCT (Discrete Cosine Transform) con la **DWT (Discrete Wavelet Transform)**.
    * La DWT opera su segnali a risoluzione multipla, analizzando sia il tempo (spazio) che la frequenza.
    * Viene applicata solitamente su blocchi più grandi (es. **64x64** pixel) o sull'intera immagine (tiling), riducendo gli artefatti "a blocchi" tipici del JPEG standard.
* **Quantizzazione e Codifica:** I coefficienti prodotti dalla DWT vengono quantizzati e poi codificati.
* **Complessità:** Richiede una potenza di calcolo circa **un ordine di grandezza superiore** (10x) rispetto al JPEG standard, sia in codifica che in decodifica.

### Funzionalità Avanzate
* **Region of Interest (ROI):** Permette di codificare zone specifiche dell'immagine (es. un volto o una targa) con qualità superiore rispetto allo sfondo.
* **Robustezza:** Include codici di risincronizzazione per gestire errori di trasmissione.
* **Scalabilità:** Consente di estrarre versioni a bassa risoluzione o bassa qualità direttamente dallo stesso bitstream compresso.



---

## 2. Confronto degli Algoritmi di Compressione

Per valutare un algoritmo di compressione si analizza il **Rapporto di Compressione ($C_R$)**:

$$
C_R = \frac{\text{Dimensione Originale}}{\text{Dimensione Codificata}}
$$

### 🟢 Lossless (Senza Perdita)
In questo caso la qualità non è un parametro (è identica all'originale). Si valuta solo la capacità di ridurre la ridondanza.
* **Rapporto Tipico:** $1,5 : 1$ fino a $2 : 1$ (o poco più per immagini semplici).
* **Focus:** Efficienza dell'algoritmo entropico (es. LZW, Deflate).

### 🔴 Lossy (Con Perdita)
Il solo rapporto di compressione non basta, poiché potrei comprimere infinitamente distruggendo l'immagine. Bisogna bilanciare il $C_R$ con la **qualità visiva**.
La valutazione della qualità avviene in due modi:
1.  **Prove Soggettive:** Test visivi con osservatori umani (costosi e lenti).
2.  **Metriche Oggettive:** Algoritmi matematici che calcolano la differenza tra l'immagine originale $I$ e quella ricostruita $K$ (di dimensioni $M \times N$).

---

## 3. Metriche Oggettive (Lossy)

### MSE (Mean Square Error)
Calcola l'errore quadratico medio tra i pixel originali e quelli compressi. Più è basso, migliore è la qualità.

$$
MSE = \frac{1}{MN} \sum_{i=0}^{M-1} \sum_{j=0}^{N-1} [I(i,j) - K(i,j)]^2
$$

### SNR (Signal-to-Noise Ratio)
Rapporto segnale-rumore. Misura la potenza del segnale rispetto al rumore introdotto dalla compressione.

$$
SNR_{db} = 10 \cdot \log_{10} \left( \frac{\sum I(i,j)^2}{\sum [I(i,j) - K(i,j)]^2} \right)
$$

### PSNR (Peak Signal-to-Noise Ratio)
È la metrica più utilizzata per le immagini. Rapporto tra il massimo valore possibile di un pixel (es. 255 per 8 bit) e l'errore (MSE).

$$
PSNR = 10 \cdot \log_{10} \left( \frac{(2^b - 1)^2}{MSE} \right)
$$

> [!TIP] Interpretazione PSNR
> * Valori più **alti** indicano una qualità migliore.
> * Tipicamente per JPEG: $30 \text{ dB} < PSNR < 50 \text{ dB}$.
> * Sotto i 30 dB la degradazione è visibilmente evidente.
![[Screenshot 2026-02-03 alle 18.12.42.png | 300]]
---
![[Screenshot 2026-02-03 alle 18.12.58.png | 300]]

![[Screenshot 2026-02-03 alle 18.13.38.png | 400]]
## 4. Simmetria del JPEG
Il JPEG standard è definito un algoritmo **Simmetrico**.
* **Significato:** La complessità computazionale e il tempo richiesto per la compressione sono approssimativamente uguali a quelli richiesti per la decompressione.
* **Funzionamento:** Le operazioni di decodifica sono matematicamente l'inverso esatto di quelle di codifica (IDCT vs DCT, Dequantizzazione vs Quantizzazione).![[Screenshot 2026-02-03 alle 18.14.15.png]]