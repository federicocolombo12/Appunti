
In Computer Graphics, esiste una distinzione netta tra la **modellazione** (la creazione del mondo virtuale) e la **visualizzazione** (la generazione dell'immagine di quel mondo).

## 1. Definizione della Scena (Scene Definition)

Una scena 3D è una struttura dati complessa che descrive un mondo virtuale.

- **Indipendenza:** La scena esiste indipendentemente dal fatto che venga visualizzata o meno.
    
- **Componenti:** È composta da oggetti geometrici (mesh), sorgenti di luce, materiali e una o più camere virtuali.
    
- **Gerarchie:** Gli oggetti sono spesso organizzati in strutture gerarchiche (es. un braccio è "figlio" del busto), dove la posizione di un oggetto dipende da quella del padre.
    

## 2. I Sistemi di Coordinate

Per passare dai dati grezzi del modello 3D ai pixel sul monitor, i dati geometrici attraversano diversi "spazi" o sistemi di riferimento. Ogni passaggio implica una moltiplicazione per una matrice di trasformazione specifica.

### A. Object Space (Spazio Oggetto)

È il sistema di coordinate locale in cui viene modellato l'oggetto inizialmente (es. in un software come Blender, il centro dell'oggetto è spesso l'origine (0,0,0)).

- _Caratteristica:_ I vertici sono definiti rispetto al centro dell'oggetto stesso.
    

### B. World Space (Spazio Mondo)

È il sistema di riferimento comune a tutta la scena.

- **Modeling Transformation:** È la trasformazione che porta gli oggetti dall'_Object Space_ al _World Space_. Qui definiamo dove si trovano gli oggetti nel mondo, come sono orientati e quanto sono grandi.
    


### C. Camera Space (o Eye Space)

È il sistema di riferimento dell'osservatore (la camera). È ancora uno spazio **3D**.

- **Viewing Transformation:** Sposta e ruota l'intero mondo affinché la camera si trovi nell'origine e guardi lungo l'asse Z negativo (o positivo, a seconda della convenzione).
### D. Clip Space e NDC (Normalized Device Coordinates)

Prima della proiezione finale, il volume di vista (Frustum) viene deformato in un cubo unitario canonico (spesso tra −1 e 1su tutti gli assi).

- **Clipping:** Gli oggetti che si trovano fuori da questo volume (e quindi fuori dal cono visivo) vengono tagliati o scartati.
    
- **Vantaggio:** Le operazioni di taglio (clipping) sono molto più semplici su un cubo standardizzato che su un tronco di piramide generico.
    

### E. Screen Space (Spazio Schermo)

Lo spazio 2D finale del monitor.

- **Viewport Transformation:** Le coordinate normalizzate vengono scalate per adattarsi alla risoluzione in pixel della finestra (es. 1920×1080).
- **Differenza col Camera Space:** Mentre il Camera Space è 3D e usa unità geometriche (dove è l'oggetto rispetto all'occhio?), lo Screen Space è 2D e usa coordinate intere (quale **pixel** accendo sul monitor?).

---

## 3. La Fase di Visualizzazione (Rendering Pipeline)

La sequenza logica che porta alla visualizzazione segue questi stadi fondamentali:

1. **Modeling Transformation:** Posizionamento degli oggetti nel _World Space_.
    
2. **Viewing Transformation:** Trasformazione nel _Camera Space_. Tutto è relativo all'occhio dell'osservatore.
    
3. **Projection & Clipping:**
    
    - Si definisce il tipo di proiezione (Prospettica o Ortogonale).
        
    - Si eliminano le primitive fuori dal _Frustum_ visivo.
        
    - Si trasformano i vertici in _NDC_ (Normalized Device Coordinates).
        
4. **Scan Conversion (Rasterizzazione):** 
    - È il passaggio dal mondo "continuo" (vettoriale) a quello "discreto" (pixel).
    - Le primitive geometriche (triangoli, linee) vengono analizzate per determinare quali pixel dello schermo coprono.
    - Il risultato sono i **frammenti** (potenziali pixel con dati associati come colore e profondità) che andranno nel [[Frame Buffer]].
    
5. **Visibility (Hidden Surface Removal):** Si determina quali oggetti sono visibili e quali sono coperti da altri (spesso usando lo **Z-Buffer**).

![[Pasted image 20260208111422.png]]