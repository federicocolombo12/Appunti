Un segnale video è tecnicamente una rapida successione di immagini statiche che, grazie alla persistenza retinica, creano l'illusione del movimento.

### Parametri Fondamentali
* **Frame Rate (FPS):** Frequenza dei fotogrammi.
    * **Cinema:** 24 fps (pellicola 35mm).
    * **PAL (Europa):** 25 fps.
    * **NTSC (USA):** 30 fps (precisamente 29.97).
* **Aspect Ratio:** Rapporto tra larghezza e altezza ($L:A$).
    * **4:3:** TV tradizionale (CRT).
    * **16:9:** Standard moderno e Cinema.
* **Interlacciamento:**
    * Ogni frame è composto da due **campi** (semiquadri) intrecciati (linee pari e dispari).
    * In PAL: 50 semiquadri al secondo = 25 frame completi.



---

## 1. Il Problema della Larghezza di Banda
Il video digitale non compresso genera una quantità di dati enorme, ingestibile per i supporti fisici standard o per la trasmissione.

> [!EXAMPLE] Calcolo del Bitrate (Video SD Uncompressed)
> Consideriamo un segnale PAL standard:
> * **Risoluzione:** $720 \times 576$ pixel.
> * **Refresh:** 25 frame/s (50 semiquadri).
> * **Profondità Colore:** 3 Byte/pixel (24 bit).
>
> **Data Rate:**
> $$Rate = 720 \times 576 \times 25 \times 3 \approx 31.104.000 \text{ Byte/s} \approx 31 \text{ MB/s}$$
>
> **Occupazione per un film di 2 ore (7200 sec):**
> $$\text{Size} = 31 \text{ MB/s} \times 7200 \text{ s} \approx 223.200 \text{ MB} \approx 223 \text{ GB}$$
>
> **Confronto Supporto:** Un DVD ha capacità $\approx 4.7 \text{ GB}$.
> **Conclusione:** È necessaria una compressione drastica.

---

## 3. Tecniche di Compressione Video
Per codificare (Encoder) e visualizzare (Decoder) i dati, si usano due strategie principali:

### A. Compressione Intra-Frame (Spaziale)
* Ogni fotogramma viene compresso individualmente (come se fosse un'immagine JPEG).
* **Esempio:** Motion JPEG (MJPEG).
* **Efficienza:** Bassa ($10:1$ - $20:1$).
* **Uso:** Editing video (facile tagliare in qualsiasi punto).

### B. Compressione Inter-Frame (Temporale)
* Sfrutta la ridondanza tra fotogrammi successivi (spesso lo sfondo non cambia, cambiano solo gli oggetti che si muovono).
* Si codificano solo le differenze rispetto ai frame precedenti.
* **Efficienza:** Molto alta ($50:1$ - $100:1$).
* **Uso:** Streaming, DVD, Broadcast.

---

## 4. Famiglie di Codec e Standard

### Famiglia MPEG (Moving Picture Experts Group)
Lo standard MPEG è strutturato in **Parti** (specifiche aree dello standard) e ogni parte in **Layer**.

| Standard           | Utilizzo Principale       | Note                                                                                                               |
| :----------------- | :------------------------ | :----------------------------------------------------------------------------------------------------------------- |
| **MPEG-1**         | Video CD (VCD)            | Qualità simile al VHS.                                                                                             |
| **MPEG-2**         | DVD, TV Satellitare (DVB) | Le prime 5 parti sono identiche a MPEG-1, ma ne aggiunge altre 5 per gestire interlacciamento e qualità broadcast. |
| **MPEG-4**         | Internet, Streaming       | Evoluzione per bitrate bassi e oggetti multimediali. Implementazioni note: **DivX, Xvid, FFmpeg**.                 |
| **MPEG-4 Part 10** | Alta definizione          | Noto anche come **AVC** o **H.264**. Standard attuale per Blu-ray e Streaming HD.                                  |

### Famiglia ITU-T (Telecommunication Standardization Sector)
Standard nati specificamente per le telecomunicazioni (videoconferenze).

| Standard         | Utilizzo                 | Note                                                              |
| :--------------- | :----------------------- | :---------------------------------------------------------------- |
| **H.261**        | Videoconferenza (ISDN)   | Obsoleto. Primo standard pratico.                                 |
| **H.263**        | Streaming, Videochiamate | Ottimizzato per bitrate molto bassi.                              |
| **H.264**        | HD, Streaming, Conf.     | Identico a MPEG-4 Part 10 (progetto congiunto JVT).               |
| **H.265 (HEVC)** | 4K/UHD                   | Evoluzione di H.264 (non citato nel prompt ma successore logico). |

### Altri Codec Proprietari e Open
* **Microsoft:** WMV7 (Windows Media Video), VC-1 (Standardizzato SMPTE).
* **RealNetworks:** RealVideo.
* **Open Source:** Xiph.org (Theora, etc.).

---

## 5. Il Container (Formato Contenitore)
Il file video che vediamo sul computer non è il codec, ma un **Container**.
Il Container è un "pacchetto" che sincronizza e ingloba flussi diversi:
1.  Stream Video (es. MPEG-4).
2.  Stream Audio (es. MP3 o AAC).
3.  Metadati / Sottotitoli.

**Container più diffusi:**
* **AVI** (Audio Video Interleave - Microsoft, legacy).
* **MOV** (QuickTime - Apple).
* **3GP** (Mobile devices).
* **VOB** (Video Object - Struttura dei DVD, contiene MPEG-2).
* **OGM** (Ogg Media).
* **RealMedia** (.rm).
* **DMF** (DivX Media Format).
# MPEG: Overview e Tipologie di Frame

Lo standard MPEG utilizza tecniche sofisticate per sfruttare la ridondanza spaziale e temporale del video. La codifica si basa sulla suddivisione dell'immagine in **Macroblocchi**:
* **Luminanza (Y):** $16 \times 16$ pixel.
* **Crominanza (U, V):** $8 \times 8$ pixel per ciascun componente (data la minore sensibilità dell'occhio al colore).

---

## 1. Tipologie di Frame (GOP Structure)
MPEG definisce tre tipi di fotogrammi (Frames) per bilanciare la necessità di compressione elevata con quella di accesso rapido e qualità.

### 🟢 I-Frames (Intra-coded Frames)
* **Definizione:** Sono frame "self-contained" (autonomi), codificati senza fare riferimento ad altre immagini.
* **Tecnica:** Usano una compressione **Intra-frame** simile al JPEG:
    1.  Suddivisione del macroblocco in blocchi $8 \times 8$.
    2.  Applicazione della **DCT**.
    3.  **Quantizzazione:** Utilizza un valore costante per ogni coefficiente DCT.
* **Scopo:**
    * Punti di accesso casuale (Random Access) per lo scorrimento del video.
    * Blocco di riferimento per la risincronizzazione in caso di errori.
    * Hanno il rapporto di compressione più basso (file più grande).

### 🔵 P-Frames (Predictive-coded Frames)
* **Definizione:** Frame predittivi che utilizzano la ridondanza temporale **causale** (guardano indietro).
* **Dipendenze:** Richiedono il precedente *I-frame* o *P-frame* per la codifica/decodifica.
* **Motion Estimation:** Si basano sulla stima del movimento. Solo le differenze (residui) e i vettori di moto vengono salvati.
* **Compressione:** Migliore rispetto agli I-Frames.

### 🟡 B-Frames (Bidirectionally-predictive Frames)
* **Definizione:** Frame che sfruttano la ridondanza temporale **non causale** (bidirezionale).
* **Dipendenze:** Richiedono informazioni sia dal frame **precedente** (passato) che da quello **successivo** (futuro).
* **Caratteristiche:**
    * Massimo tasso di compressione (poiché interpolano informazioni già note).
    * Non possono essere usati come riferimento per altri frame (in standard base).



---

## 2. Motion Estimation (Stima del Movimento)
Per i frame P e B, l'algoritmo cerca dove si sono spostati i macroblocchi rispetto al frame di riferimento.

> [!IMPORTANT] Filosofia dello Standard
> MPEG **non specifica** l'algoritmo di stima del movimento (che può essere proprietario e variare per performance/qualità), ma specifica rigorosamente come **codificare il risultato** (i vettori di moto e l'errore residuo) affinché qualsiasi decoder standard possa leggerlo.

**Algoritmi di Matching più diffusi:**
Sono computazionalmente intensivi e cercano di minimizzare l'errore tra il blocco attuale e quello di riferimento:
1.  **SAD (Sum of Absolute Differences):** Più veloce.
    $$SAD = \sum |Block_{curr} - Block_{ref}|$$
2.  **SSD (Sum of Squared Differences):** Più preciso ma oneroso (penalizza maggiormente i grandi errori).
    $$SSD = \sum (Block_{curr} - Block_{ref})^2$$

---

## 3. Ordine di Codifica vs. Visualizzazione
L'uso dei **B-Frames** (che necessitano del futuro per essere costruiti) introduce una complessità fondamentale: l'ordine dei frame nel flusso dati (Bitstream) non coincide con l'ordine in cui li vediamo a schermo.

* **Display Order (Ordine Temporale):** $I_1, B_2, B_3, P_4, B_5, \dots$
* **Transmission/Decoding Order:**
    Per decodificare $B_2$ e $B_3$, il decoder deve conoscere prima $P_4$.
    Quindi l'ordine di trasmissione sarà: $I_1, P_4, B_2, B_3, \dots$

> [!SUMMARY] Motivazioni dell'Architettura MPEG
> 1.  **Accesso Rapido:** Garantito dagli I-Frames.
> 2.  **Efficienza di Codifica:** Massimizzata dai P-Frames.
> 3.  **Sfruttamento Ridondanza Temporale:** Portata all'estremo dai B-Frames.