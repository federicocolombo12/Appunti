# Modelli di Colore e Introduzione alla Compressione

## 1. Il Modello RGB (Red, Green, Blue)
Il modello RGB è di tipo **additivo** e si basa sulla tricromia, corrispondente alla fisiologia dell'occhio umano (i coni sensibili a Rosso, Verde e Blu).

### Profondità di Colore (Bit Depth)
La qualità del colore dipende dal numero di bit assegnati per pixel:
* **8 bit:** 256 colori (spesso usata con *palette* o *lookup table*).
* **16 bit (High Color):** $2^{16} \approx 65.000$ colori.
* **24 bit (True Color):** 8 bit per canale (R, G, B).
    * $2^8 \times 2^8 \times 2^8 = 16.777.216$ colori.
    * A livello sperimentale, **8 bit per canale** sono considerati sufficienti per ingannare l'occhio umano e percepire un'immagine fotorealistica.

### Struttura Matematica
Un'immagine RGB è rappresentata come l'unione di **3 matrici indipendenti** (canali):
$$
\text{Immagine} = \{ M_R(i,j), M_G(i,j), M_B(i,j) \}
$$

> [!EXAMPLE] Esempio di Occupazione Memoria
> Per un'immagine $1024 \times 768$ in **True Color** (24 bit = 3 Byte per pixel):
>
> $$\text{Memoria} = 1024 \times 768 \times 3 \text{ Byte}$$
> $$= 2.359.296 \text{ Byte} \approx 2.304 \text{ kB} \approx 2,25 \text{ MB}$$

---

## 2. Il Modello YUV (Luminanza e Crominanza)
Il modello YUV separa l'informazione di luminosità da quella del colore.
* **Y (Luminanza/Luma):** Rappresenta l'intensità luminosa (immagine in scala di grigi). Fondamentale per la retro-compatibilità con le vecchie TV in bianco e nero.
* **U e V (Crominanza):** Trasportano le informazioni sul colore.

### Principio di Compressione
L'occhio umano è molto più sensibile alle variazioni di luminosità (Y) che a quelle di colore (U, V).
Questo permette di applicare il **Chroma Subsampling** (sottocampionamento della crominanza):
* Si mantiene la risoluzione piena su $Y$.
* Si riduce la risoluzione (e quindi i bit) su $U$ e $V$ senza degradare significativamente la percezione visiva.



### Notazione del Sottocampionamento (J:a:b)
Le codifiche più comuni definiscono quanto colore viene preservato rispetto alla luminosità:

| Formato | Descrizione | Utilizzo |
| :--- | :--- | :--- |
| **4:4:4** | Nessun sottocampionamento. Y, U, V hanno la stessa risoluzione. | Studi professionali, grafica alta qualità. |
| **4:2:2** | La risoluzione orizzontale del croma è dimezzata. | Standard broadcast TV professionale. |
| **4:1:1** | Croma campionato ogni 4 pixel orizzontali. | Formati consumer (es. NTSC DV). |
| **4:2:0** | (Molto comune) Croma sottocampionato sia orizzontalmente che verticalmente. | JPEG, DVD, Blu-ray, Streaming. |

---

## 3. Categorie di Compressione
La riduzione della dimensione dei dati (necessaria dato il peso delle immagini raw) si divide in due grandi famiglie:

### 1. Lossless (Senza Perdita)
* Permette di ricostruire **esattamente** i dati originali bit per bit.
* Nessuna degradazione dell'immagine.
* Fattore di compressione limitato (es. 2:1).
* *Es: PNG, TIFF (LZW), ZIP.*

### 2. Lossy (Con Perdita)
* Elimina le informazioni che l'occhio umano fatica a percepire (sfruttando modelli psicovisivi).
* L'immagine ricostruita è un'approssimazione dell'originale.
* Fattori di compressione elevati (es. 10:1, 50:1).
* *Es: JPEG, HEIF.*