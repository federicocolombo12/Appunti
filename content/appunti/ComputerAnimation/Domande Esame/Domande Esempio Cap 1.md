### 1.1 Architettura Hardware e Frame Buffer

_Concetti chiave: GPU, Memoria, Risoluzione, Profondità di colore._

**Domande Base e Discorsive**

- **D1:** Definire cos'è il **Frame Buffer**. Quali sono i due parametri fondamentali che ne determinano la dimensione in memoria e la qualità dell'immagine?


    
- **D2:** Qual è la differenza strutturale tra un'immagine definita da una "profondità" di 8 bit per pixel rispetto a una con 32 bit per pixel? (Cosa contiene quell'informazione aggiuntiva?)
    

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
    
- **D6:** Elencare e descrivere brevemente gli stadi della **Pipeline di Visualizzazione** (Rendering Pipeline) classica.
    
    - _Output richiesto:_ Vertici $\to$ Transformer $\to$ Clipper $\to$ Projector $\to$ Rasterizer $\to$ Pixel.
        

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