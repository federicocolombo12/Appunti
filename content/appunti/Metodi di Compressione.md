## 1. Run-Length Encoding (RLE)
La RLE è una tecnica di compressione **Lossless** molto semplice, basata sulla ridondanza spaziale di valori consecutivi identici.

* **Principio:** Sostituisce sequenze di pixel con lo stesso valore con una coppia `(conteggio, valore)`.
* **Funzionamento:** Scansiona la matrice e raggruppa le sequenze.
    * Esempio sequenza: `AAAAABBB`
    * Codifica RLE: `5A3B`
* **Ottimizzazione:** Spesso utilizzata in combinazione con la codifica di **Huffman** per codificare le lunghezze delle sequenze (run).
* **Utilizzo:** Standard per i **FAX** (dove ci sono molte sequenze lunghe di bianco o nero).



---

## 2. GIF (Graphics Interchange Format)
Formato storico progettato per il trasferimento di immagini su reti (CompuServe).
![[Screenshot 2026-02-03 alle 18.07.16.png | 300]]

* **Algoritmo:** Utilizza la compressione **LZW (Lempel-Ziv-Welch)**.
    * È una compressione *basata su dizionario*: sostituisce sequenze ripetute di dati con riferimenti a un dizionario costruito dinamicamente.

> [!INFO] Focus: LZW (Lempel-Ziv-Welch)
> A differenza di Huffman (statistico), LZW è **adattivo** e basato su dizionario.
> * **Funzionamento:** Man mano che legge i dati, costruisce un "vocabolario" di sequenze ricorrenti.
> * **Processo:** Quando trova una sequenza già nota seguita da un nuovo carattere, crea una nuova voce nel dizionario per questa combinazione più lunga e ne emette il codice.
> * **Vantaggio:** Non è necessario salvare il dizionario nel file. Il decoder lo ricostruisce autonomamente durante la decompressione seguendo la stessa logica dei dati in ingresso.

* **Caratteristiche:**
    * **Profondità:** Limitata a **8 bit** (Palette di 256 colori).
    * **Efficienza:** Ottima per immagini con **colori omogenei** (grafiche, loghi, disegni animati).
    * **Inefficienza:** Scarsa per immagini complesse o fotografie (necessita di *dithering* o riduzione colori drastica).
* **Funzionalità:**
    * Supporta l'**interlacciamento**: permette di visualizzare una versione a bassa risoluzione dell'immagine mentre il file viene scaricato (memorizzazione non lineare delle linee).
    * Supporta la trasparenza (un solo colore della palette reso trasparente).

---

## 3. PNG (Portable Network Graphics)
Sviluppato come rimpiazzo *open* e libero da brevetti (all'epoca su LZW) del GIF. È raccomandato dal **W3C** e standard **ISO**.

* **Tipo:** Compressione **Lossless** (senza perdita).
* **Algoritmo:** Combina due fasi:
    1.  **Predizione (Filtering):** Trasforma i valori dei pixel in differenze rispetto ai vicini (alto, sinistra, ecc.) per aumentare la ridondanza.
    2.  **Codifica Entropica (DEFLATE):** È la fase finale di compressione vera e propria.
        * I dati pre-elaborati dal filtro vengono compressi con **DEFLATE**, che combina due tecniche:
        * **LZ77 (Sliding Window):** Rimuove le ripetizioni sostituendo sequenze di dati già visti con riferimenti (distanza, lunghezza).
        * **Codifica di Huffman:** Comprime i simboli risultanti assegnando codici binari più corti ai valori statisticamente più frequenti.
* **Supporto Colore:**
    * *Grayscale* (scala di grigi).
    * *Palette* (come GIF).
    * *True Color* (fino a 48 bit).
* **Canale Alpha:** Supporta trasparenza variabile (non solo on/off come GIF), ideale per compositing.
* **Interlacciamento:** Supporta l'algoritmo Adam7 per il caricamento progressivo.

---

## 4. JPEG (Joint Photographic Experts Group)
Il JPEG non è un singolo algoritmo, ma uno standard creato da un gruppo di ricerca internazionale (ISO/IEC e ITU-T) per definire la codifica di immagini a **tono continuo** (fotografie naturali).

* **Riferimenti Normativi:** ISO/IEC 10918 / ITU-T Rec T.81.
* **Obiettivo:** Standardizzare la compressione per immagini naturali (sia grayscale che a colori), dove la perdita di informazioni ad alta frequenza è poco visibile all'occhio umano.

> [!IMPORTANT] Filosofia dello Standard JPEG
> Lo standard JPEG definisce le **specifiche per la decompressione** (il formato del bitstream e come deve essere interpretato) e fornisce delle **linee guida** per la compressione.
> * Non impone *come* realizzare il compressore, ma quali regole il flusso di dati (bitstream) deve rispettare per essere decodificato correttamente da un decoder standard.
> * Questo lascia libertà agli sviluppatori di ottimizzare l'encoder (velocità vs qualità) pur mantenendo la compatibilità.

