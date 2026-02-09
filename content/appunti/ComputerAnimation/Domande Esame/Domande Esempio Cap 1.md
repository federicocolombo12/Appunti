### 1.1 Architettura Hardware e Frame Buffer

_Concetti chiave: GPU, Memoria, Risoluzione, Profondità di colore._

**Domande Base e Discorsive**

- **D1:** Definire cos'è il **Frame Buffer**. Quali sono i due parametri fondamentali che ne determinano la dimensione in memoria e la qualità dell'immagine?

Il **Frame Buffer** è un'area di memoria (solitamente nella VRAM) che contiene l'immagine finale, sotto forma di matrice di pixel, pronta per essere inviata al display.
I due parametri fondamentali sono:
1. **Risoluzione:** Il numero totale di pixel (larghezza $\times$ altezza).
2. **Profondità di Colore (Bit Depth):** La quantità di memoria assegnata a ogni pixel (determina quanti colori diversi possono essere rappresentati).
La dimensione totale in memoria è data dal prodotto: $Risoluzione \times BitDepth$.
    
- **D2:** Qual è la differenza strutturale tra un'immagine definita da una "profondità" di 8 bit per pixel rispetto a una con 32 bit per pixel? (Cosa contiene quell'informazione aggiuntiva?)
Un'immagine a **8 bit** solitamente è in **scala di grigi**, dove l'unico canale presente rappresenta l'intensità luminosa (luminanza) con 256 livelli possibili.  
Un'immagine a **32 bit** (spesso definita RGBA) organizza invece la memoria in **4 canali** da 8 bit ciascuno:

1. **RGB (24 bit):** Tre canali per i colori primari (Rosso, Verde, Blu), permettendo "True Color".
2. **Alpha Channel (8 bit):** Un canale aggiuntivo che definisce la trasparenza (o opacità) del pixel, fondamentale per il compositing.
    

**Esercizio (Presente nel file "Domande esame.pdf")**

- **D3:** Calcolare la memoria necessaria per immagazzinare una singola immagine con risoluzione **1024x1024 pixel** e profondità di colore **32 bit** per pixel.
    
    - _Nota per lo svolgimento:_ Ricorda di convertire i bit in Byte (8 bit = 1 Byte) prima di dare il risultato finale in MB.
        

**Approfondimenti**

- **D4:** Descrivere l'architettura generale di un sistema grafico moderno, distinguendo il ruolo della CPU da quello della GPU. Perché la GPU è ottimizzata per il calcolo parallelo?
    

---

### 1.2 Geometria della Camera e Pipeline Grafica

_Concetti chiave: Pinhole Camera, Frustum, Pipeline Object-Order._

**Domande Base e Discorsive (Dal file "Domande esame.pdf")**

- **D5:** Descrivere il modello **Pinhole Camera** (Camera Oscura). Qual è la relazione matematica fondamentale che lega la coordinata $Y$ nello spazio 3D alla coordinata proiettata $y_p$ sul piano immagine?

Il modello **Pinhole Camera** descrive la proiezione prospettica ideale in cui tutti i raggi di luce passano attraverso un singolo punto detto **Centro di Proiezione** (o occhio).

In **Fisica**, il piano immagine è posto dietro il foro, generando un'immagine capovolta.
In **Computer Grafica**, si utilizza un **Piano Immagine Virtuale** posto *davanti* al centro di proiezione (a una distanza focale $d$), ottenendo così un'immagine dritta (non invertita).

Sfruttando la similitudine dei triangoli, la relazione matematica per la coordinata proiettata $y_p$ di un punto a altezza $Y$ e profondità $Z$ è:
$$y_p = d \cdot \frac{Y}{Z}$$
(La divisione per $Z$ è ciò che crea l'effetto prospettico, rimpicciolendo gli oggetti distanti).

- **D6:** Elencare e descrivere brevemente gli stadi della **Pipeline di Visualizzazione** (Rendering Pipeline) classica.
    
Gli stadi principali della **pipeline di visualizzazione** sono: **Transformation**, **Projection**, **Clipping** e **Rasterization**.

1. **Transformation (Model & View):** I vertici dell'oggetto vengono trasformati dallo _spazio locale_ allo _spazio globale_ (_World Space_). Successivamente, vengono trasformati nello _spazio vista_ (_Camera Space_) per posizionare il mondo rispetto all'occhio dell'osservatore.
    
2. **Projection:** Definisce il volume di vista (**Frustum**). I vertici vengono proiettati dallo spazio camera allo _spazio di ritaglio_ (_Clip Space_). Qui viene applicata la deformazione prospettica (o ortografica).
    
3. **Clipping:** Le primitive geometriche vengono confrontate con i limiti del Frustum. Quelle esterne vengono scartate, mentre quelle parzialmente visibili vengono "tagliate" per rientrare nel volume visibile.
    
4. **Rasterization:** Dopo la mappatura su schermo (_Screen Space_), le primitive geometriche (vettori) vengono convertite in una griglia discreta di pixel detti **frammenti**. Per ogni frammento viene calcolato il colore finale (tramite _Shading_) e viene gestita la visibilità (tramite **Z-Buffer**) prima di scrivere il risultato nel **Frame Buffer**
**Esercizio**

- **D7:** Disegnare schematicamente il **Volume di Vista (View Volume)** o Frustum. Indicare chiaramente dove si trovano il _Near Clipping Plane_ e il _Far Clipping Plane_.
    
- **D8:** Se un oggetto si trova dietro la camera (coordinata $Z$ positiva, assumendo la camera guardi verso $Z$ negativo), in quale fase della pipeline viene scartato?
    

**Approfondimenti**

- **D9:** Perché in Computer Grafica il piano di proiezione viene posizionato _davanti_ al centro di proiezione (Virtual Image Plane) invece che dietro (come nella fisica reale)?
    
---

### 1.3 Trasformazioni Geometriche

_Concetti chiave: Coordinate Omogenee, Matrici 4x4, Composizione._

**Domande Base e Discorsive**

- **D10:** Perché in Computer Grafica si utilizzano le **Coordinate Omogenee** (aggiungendo la quarta componente $w$)? Quale vantaggio offrono per le trasformazioni affini come la traslazione?

Le **Coordinate Omogenee** permettono di rappresentare la **traslazione** come una moltiplicazione matriciale (linearizzazione), uniformandola a rotazione e scalatura all'interno di matrici $4\times4$.
    
La componente $w$ distingue la natura geometrica:
1.  **Punti ($w=1$):** Rappresentano una posizione. Moltiplicando per la matrice, il termine di traslazione (quarta colonna) viene sommato.
2.  **Vettori ($w=0$):** Rappresentano una direzione (differenza tra due punti). Avendo $w=0$, il termine di traslazione viene annullato, il che è corretto poiché un vettore (direzione) non cambia se viene spostato nello spazio.

Il vantaggio principale è la **Composizione**: diverse trasformazioni possono essere combinate pre-moltiplicando le matrici in un'unica matrice risultante.
    
- **D11:** Qual è la differenza matematica tra la rappresentazione di un **Punto** e quella di un **Vettore** in coordinate omogenee? (Indizio: guarda il valore di $w$).
    

**Esercizio**

- **D12:** Data una sequenza di trasformazioni: "Ruotare l'oggetto di 90° su X" ($R$) e successivamente "Traslalo di 5 unità su Y" ($T$), scrivere l'ordine corretto di moltiplicazione delle matrici per un punto $P$.
    
    - È $P' = R \cdot T \cdot P$ oppure $P' = T \cdot R \cdot P$? (Considerando la notazione a colonna standard).
        

**Approfondimenti**

- **D13:** Spiegare perché il prodotto di matrici non è commutativo e cosa comporta questo nell'animazione gerarchica (es. braccio robotico).
    

---

### 1.4 Rappresentazione di Curve (Fondamenti)

_Concetti chiave: Bezier, Interpolazione vs Approssimazione._

**Domande Base e Discorsive**

- **D14:** Spiegare la differenza tra una curva che **interpola** i punti di controllo e una che li **approssima**. Le curve di Bézier interpolano tutti i loro punti?
    
- **D15:** Descrivere le proprietà geometriche di una curva di **Bézier cubica**. Quanti punti di controllo sono necessari per definirla?
    

**Esercizio**

- **D16:** Data una curva di Bézier definita dai punti $P_0, P_1, P_2, P_3$:
    
    - Qual è il punto di partenza della curva?
        
    - Qual è la direzione della tangente iniziale? (Scrivere la formula vettoriale legata a $P_0$ e $P_1$).
        

**Approfondimenti**

- **D17:** Definire i concetti di continuità **$G1$ (Geometrica)** e **$C1$ (Parametrica)** nel punto di giunzione tra due curve. Quale delle due garantisce una velocità costante attraverso la giunzione?
    

---

### 1.5 Percezione dell'Animazione (Lezione 1)

_Concetti chiave: Persistenza, Motion Blur, Aliasing Temporale._

**Domande Base e Discorsive**

- **D18:** Spiegare perché la _Persistenza della Visione_ (fenomeno retinico) non è la spiegazione corretta per la percezione del movimento fluido (fenomeno cognitivo/corticale).
    
- **D19:** Cos'è il **Motion Blur** e perché è necessario aggiungerlo sinteticamente nelle animazioni al computer?
    

**Approfondimenti**

- **D20:** Cosa si intende per _Strobing_ o _Aliasing Temporale_? In quali condizioni si verifica (alta velocità vs frame rate)?