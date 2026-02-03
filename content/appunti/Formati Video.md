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

| Standard | Utilizzo Principale | Note |
| :--- | :--- | :--- |
| **MPEG-1** | Video CD (VCD) | Qualità simile al VHS. |
| **MPEG-2** | DVD, TV Satellitare (DVB) | Le prime 5 parti sono identiche a MPEG-1, ma ne aggiunge altre 5 per gestire interlacciamento e qualità broadcast. |
| **MPEG-4** | Internet, Streaming | Evoluzione per bitrate bassi e oggetti multimediali. Implementazioni note: **DivX, Xvid, FFmpeg**. |
| **MPEG-4 Part 10** | Alta definizione | Noto anche come **AVC** o **H.264**. Standard attuale per Blu-ray e Streaming HD. |

### Famiglia ITU-T (Telecommunication Standardization Sector)
Standard nati specificamente per le telecomunicazioni (videoconferenze).

| Standard | Utilizzo | Note |
| :--- | :--- | :--- |
| **H.261** | Videoconferenza (ISDN) | Obsoleto. Primo standard pratico. |
| **H.263** | Streaming, Videochiamate | Ottimizzato per bitrate molto bassi. |
| **H.264** | HD, Streaming, Conf. | Identico a MPEG-4 Part 10 (progetto congiunto JVT). |
| **H.265 (HEVC)**| 4K/UHD | Evoluzione di H.264 (non citato nel prompt ma successore logico). |

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