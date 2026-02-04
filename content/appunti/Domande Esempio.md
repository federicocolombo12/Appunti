### 1. Hardware e Software Graphics

_Obiettivo: Capire dove "fisicamente" risiedono i dati._

- **Domanda Fondamentale:** Cos'è il _Frame Buffer_ e quali sono i suoi due parametri principali che determinano la qualità dell'immagine finale?
    
- **Esercizio Pratico (Tipico da esame):** Se ti chiedo di allocare memoria per un singolo frame a risoluzione **800x600**con una profondità di colore **RGBA (32 bit)**, quanti Byte di memoria grafica sto occupando esattamente? (Sai fare il calcolo a mente?)
    
- **Concettuale:** Qual è la differenza tra l'operato della CPU e quello della GPU nel contesto di un sistema grafico? Perché abbiamo bisogno di API grafiche (come OpenGL o DirectX) nel mezzo?
    

### 2. Modello Camera e Definizione Scena

_Obiettivo: Capire come passiamo dal mondo 3D al monitor 2D._

- **Il Modello Pinhole:** Descrivi il funzionamento di una _Pinhole Camera_. Qual è la relazione matematica fondamentale (la formula della proiezione) che lega la coordinata Y nello spazio 3D alla coordinata Yp​ sul piano immagine?
    
- **L'Osservatore:** Perché in Computer Grafica posizioniamo il piano di proiezione _davanti_ al centro di proiezione (osservatore virtuale) invece che dietro come in una vera camera oscura?
    
- **View Volume:** Cos'è il _Frustum_ di vista? A cosa servono i piani di _Near Clipping_ e _Far Clipping_?
    

### 3. Pipeline di Visualizzazione (Rendering Pipeline)

_Obiettivo: Conoscere la sequenza esatta delle operazioni._

- **Sequenza Logica:** Elenca in ordine cronologico i 4 stadi principali della pipeline _Object-Order_ (dal vertice al pixel).
    
    - _Suggerimento: inizia con il Transformer..._
        
- **Il Ruolo del Clipper:** Cosa succede esattamente durante la fase di _Clipping_? Perché è essenziale per le prestazioni non processare tutto ciò che è nella scena?
    
- **Rasterizzazione:** Come definiresti l'operazione di _Rasterizer_ a un profano? Cosa produce in output (indizio: non sono ancora "pixel" definitivi, ma...)?
    

### 4. Trasformazioni Geometriche

_Obiettivo: Padroneggiare la matematica sottostante._

- **Coordinate Omogenee:** Perché usiamo matrici 4×4 e vettori a 4 componenti (x,y,z,w)? Qual è il vantaggio pratico rispetto alle matrici 3×3?
    
    - _Domanda trabocchetto:_ Come distinguo un **punto** da un **vettore** guardando la coordinata w?
        
- **Composizione:** Il prodotto di matrici è commutativo? Se voglio ruotare un oggetto su se stesso e poi spostarlo, in che ordine devo moltiplicare le matrici di Traslazione (T) e Rotazione (R)? Scrivi la formula P′=…P.
    

### 5. Rappresentazione di Curve

_Obiettivo: Capire come descriviamo le forme._

- **Interpolazione vs Approssimazione:** Qual è la differenza sostanziale tra una curva che _interpola_ i punti e una che li _approssima_? (Pensa alle B-Spline vs Catmull-Rom).
    
- **Curve di Bézier:** Quanti punti di controllo servono per definire una curva di Bézier cubica? Che ruolo hanno i due punti interni rispetto alla tangente della curva?
    
- **Continuità:** Cosa significa garantire una continuità C1 tra due segmenti di curva? Geometricamente, come devono essere disposti i punti di controllo nel punto di giunzione?
#### 6. Continuità Geometrica vs Parametrica (Approfondimento Curve)

_Concetto:_ Come si incollano due curve?

- **Domanda Tecnica:** "Qual è la differenza tra continuità G1 (Geometrica) e C1 (Parametrica) nel punto di giunzione tra due curve spline?"