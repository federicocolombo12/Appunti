## 1. Fondamenti: La Trasformata
Una trasformata è un'operazione matematica che consente il passaggio da un dominio a un altro (es. dal dominio del Tempo/Spazio al dominio della Frequenza).
Matematicamente, per segnali discreti, corrisponde alla moltiplicazione tra un vettore (segnale) e una matrice di trasformazione.

$$\mathbf{F} = \mathbf{T} \cdot \mathbf{x}$$

Le trasformate scompongono il segnale in una somma pesata di **Funzioni Base** (es. sinusoidi).
* **Coefficienti:** I valori risultanti rappresentano i "pesi" (ampiezze) di queste funzioni base necessarie per ricostruire il segnale originale.
* **Tipologie comuni:** DFT, FFT, DCT, DWT, KLT.

---

## 2. La DCT (Discrete Cosine Transform)
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