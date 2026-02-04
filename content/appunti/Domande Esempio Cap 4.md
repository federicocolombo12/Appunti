### 4.1 Deformazione Geometrica e Morphing

_Concetti chiave: Seed Vertex, Attenuazione, Griglie (FFD), Beier & Neely._

**Domande Base e Discorsive**

- **D1:** Descrivere la tecnica di deformazione basata su **Seed Vertex**.
    
    - Come si propaga lo spostamento dal vertice selezionato ai vertici adiacenti?
        
    - Qual è il ruolo della **funzione di attenuazione** (fall-off)?
        
- **D2:** Qual è la differenza tra un semplice **Cross-Dissolve** (dissolvenza incrociata) e il **Morphing** vero e proprio tra due immagini?
    
    - Perché nel Morphing è necessario il _Warping_ (distorsione geometrica) prima della fusione dei colori?
        

**Esercizio (Presente nel file "Domande esame.pdf")**

- **D3 (Deformazione a Griglia):** Data una griglia di controllo 2D che viene deformata (es. spostando un vertice della griglia), descrivere come si calcola la nuova posizione di un punto $A$ che si trova all'interno di una cella.
    
    - _Suggerimento:_ Si usa l'interpolazione bilineare basata sulle coordinate locali $(u,v)$ del punto rispetto alla cella deformata?
        

**Approfondimenti**

- **D4:** Nel contesto del Morphing basato su _Feature Lines_ (algoritmo di **Beier & Neely**), a cosa servono le linee di controllo disegnate dall'animatore?
    
    - Come influiscono sulla corrispondenza dei pixel tra l'immagine sorgente e quella di destinazione?
        

---

### 4.2 Sistemi Particellari (Particle Systems)

_Concetti chiave: Stochasticità, Ciclo di vita, Forze ambientali._

**Domande Base e Discorsive**

- **D5:** Definire un **Sistema Particellare**. Perché è considerato una tecnica "procedurale" e "stocastica" (casuale)?
    
    - Quali fenomeni naturali è adatto a simulare? (Es. Fuoco, fumo, pioggia...).
        
- **D6:** Descrivere il **Ciclo di Vita** di una particella all'interno del loop di simulazione.
    
    - (Generazione $\to$ Aggiornamento Attributi $\to$ Estinzione/Morte $\to$ Rendering).
        

**Esercizio**

- **D7:** Se volessi simulare un'esplosione, quali forze fisiche applicheresti alle particelle e come gestiresti il loro attributo "colore/trasparenza" (alpha) nel tempo?
    

**Approfondimenti**

- **D8:** Come viene risolto il problema della visualizzazione (Rendering) delle particelle? Sono veri modelli 3D o si usano tecniche come _Billboarding_ o _Streaks_?
    

---

### 4.3 Flocking (Comportamento di Stormo)

_Concetti chiave: Boids, Regole Locali, Comportamento Emergente._

**Domande Base e Discorsive (Dal file "Domande esame.pdf")**

- **D9 (Confronto):** Quali sono le differenze principali tra un Sistema Particellare classico e un sistema di **Flocking**?
    
    - _Punti chiave:_ Numero di elementi, intelligenza/consapevolezza dei vicini, complessità della fisica.
        
- **D10:** Definire il concetto di **Comportamento Emergente** (Emergent Behavior).
    
    - Come può un comportamento globale complesso (es. uno stormo che evita un ostacolo fluidamente) nascere da semplici regole locali senza un "leader" centrale?
        

**Esercizio (Le 3 Regole di Reynolds)**

- **D11:** Descrivere e illustrare le tre regole fondamentali che governano il movimento di un _Boid_ (membro dello stormo):
    
    1. **Separazione** (Separation): Evitare collisioni con i vicini.
        
    2. **Allineamento** (Alignment): Andare nella stessa direzione media dei vicini.
        
    3. **Coesione** (Cohesion): Rimanere vicini al centro del gruppo locale.
        

**Approfondimenti**

- **D12:** Nel calcolo del Flocking, qual è la differenza tra **Percezione Locale** e Percezione Globale? Perché un Boid dovrebbe considerare solo i compagni entro un certo raggio e angolo di vista?