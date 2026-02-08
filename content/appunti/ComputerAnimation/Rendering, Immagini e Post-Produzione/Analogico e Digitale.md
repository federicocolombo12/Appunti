La rappresentazione delle immagini varia sostanzialmente tra il dominio analogico e quello digitale.

### 📺 Immagini Analogiche
Nelle immagini analogiche (video), l'immagine è costruita tramite linee di scansione (fotogrammi):
* **Standard US (NTSC):** 525 linee.
* **Standard PAL:** 625 linee.

### 💻 Immagini Digitali
Un'immagine digitale è una discretizzazione spaziale composta da **pixel** (*picture elements*) organizzati in una griglia di righe e colonne.
* Ogni pixel possiede un valore di intensità.
* Un'immagine monocromatica è rappresentabile come una matrice $Y$ di dimensioni $M \times N$:

$$
Y(i,j) = \text{intensità luminosa al pixel } (i,j)
$$

Dove:
* $M$ = numero di righe.
* $N$ = numero di colonne.

---

## 2. Risoluzione
La risoluzione quantifica il dettaglio dell'immagine e può essere espressa in:
* **Densità:** *ppp* (pixel per pollice) o *dpi* (dots per inch).
* **Dimensione matrice:** es. $1024 \times 768$.

> [!EXAMPLE] Esempio di Calcolo Occupazione Memoria
> Consideriamo un'immagine monocromatica di dimensioni fisiche **4x4 pollici** acquisita a **300 dpi**.
>
> **1. Calcolo del numero totale di pixel:**
> $$N_{pixel} = (4 \cdot 300) \times (4 \cdot 300) = 1200 \times 1200 = 1.440.000 \text{ pixel}$$
>
> **2. Calcolo occupazione memoria (con profondità 8 bit):**
> $$Memoria = 1.440.000 \text{ pixel} \times 8 \text{ bit/pixel} = 11.520.000 \text{ bit}$$
>
> Convertendo in Byte e Megabyte:
> $$ \frac{11.520.000}{8} = 1.440.000 \text{ Byte} \approx 1,37 \text{ MB}$$

---

## 3. Percezione Visiva
La risoluzione necessaria è strettamente legata alla capacità dell'occhio umano di distinguere i dettagli. L'occhio integra i pixel discreti in un'immagine continua se visti dalla giusta distanza.

* **Potere risolutivo dell'occhio:** L'occhio distingue due punti se sottendono un angolo visivo minimo di **1' (un primo d'arco)**.
* **Corrispondenza metrica:** Questo angolo corrisponde a circa **0,3 mm** osservati da **1 metro** di distanza.

---

## 4. Quantizzazione e Livelli di Grigio
Ogni valore di intensità $Y(i,j)$ è memorizzato utilizzando un numero di bit $b$ (profondità di bit).

### Range Dinamico
Il valore di un pixel varia nell'intervallo intero:
$$[0, 2^b - 1]$$

Per $b = 8$ bit (standard per immagini monocromatiche):
* $2^8 = 256$ livelli di grigio (da 0 a 255).
* A livello sperimentale, 8 bit sono sufficienti per rappresentare correttamente le sfumature di grigio percepibili dall'occhio.

### Tipi di Quantizzazione
1.  **Uniforme:** Gli intervalli di intensità sono equispaziati.
2.  **Non Uniforme:** Gli intervalli variano (es. scala logaritmica) per adattarsi meglio alla risposta non lineare dell'occhio umano.

### Bit-Plane Slicing (Piani di Bit)
Un'immagine a 8 bit può essere analizzata come la sovrapposizione di **8 piani binari** (piani da 1 bit):
* **Piani Elevati (MSB - Most Significant Bits):** Contengono le informazioni strutturali e i dati visivamente più significativi.
* **Piani Bassi (LSB - Least Significant Bits):** Contengono i dettagli fini, ma spesso costituiscono solo rumore visivo.
![[Screenshot 2026-02-03 alle 16.50.46.png]]