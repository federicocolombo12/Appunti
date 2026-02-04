## Capitolo 2: Controllo del Moto e Animazione

### 2.1 Il Problema della Riparametrizzazione

Quando interpoliamo una curva (es. una Spline), spostarci di un incremento costante del parametro Δu _non_ corrisponde a spostarsi di una distanza costante nello spazio 3D.

- **Il Problema:** Se animiamo linearmente il parametro u, l'oggetto accelererà dove i punti di controllo sono distanti e rallenterà dove sono vicini.
    
- **La Soluzione:** Dobbiamo legare il movimento alla **Lunghezza d'Arco** (s), ovvero la distanza fisica percorsa lungo la curva, e non al parametro matematico u.
    

### 2.2 Lunghezza d'Arco (Arc Length) e Look-Up Tables

Per ottenere una velocità costante, dobbiamo trovare una relazione s=G(u) e, soprattutto, la sua inversa u=G−1(s). Poiché l'integrale analitico della lunghezza d'arco è spesso irrisolvibile per curve complesse, usiamo metodi numerici:

- **Differenze Dirette (Campionamento):** Campioniamo la curva in molti punti e calcoliamo le distanze lineari tra essi.
    
- **Look-Up Table (LUT):** Costruiamo una tabella che mappa:
    
    - u (parametro) → s (distanza accumulata).
        
- **Utilizzo:**
    
    1. Dato un tempo t, calcoliamo la distanza desiderata starget​.
        
    2. Cerchiamo starget​ nella colonna delle distanze della LUT.
        
    3. Troviamo il corrispondente u (interpolando linearmente tra i due valori più vicini in tabella).
        

### 2.3 Funzioni di Controllo Velocità (Ease-In / Ease-Out)

Una volta che sappiamo muoverci a velocità costante (velocità lineare), possiamo decidere "artisticamente" di alterarla.

- **Ease-In:** L'oggetto parte da fermo e accelera gradualmente (evita lo scatto iniziale robotico).
    
- **Ease-Out:** L'oggetto rallenta dolcemente prima di fermarsi.
    
- **Tecniche:**
    
    - **Interpolazione Sinusoidale:** Usa parte di una curva seno/coseno per modulare la velocità. Semplice ma limita il controllo.
        
    - **Accelerazione Parabolica:** Integrare un'accelerazione costante produce segmenti parabolici nel grafico distanza/tempo.
        

### 2.4 Orientamento e Frame di Frenet

Mentre l'oggetto si muove, dove guarda?

- **Frame di Frenet:** È un sistema di coordinate locale (u,v,w) costruito istantaneamente sulla curva.
    
    - **Tangente (w o T):** La direzione del moto (P′(u)).
        
    - **Binormale (u o B):** Perpendicolare al piano della curva (P′(u)×P′′(u)).
        
    - **Normale (v o N):** Perpendicolare agli altri due.
        
- **Problemi del Frenet:**
    
    1. **Indefinito** se la curvatura è zero (es. tratti rettilinei, dove P′′=0).
        
    2. **Flipping:** Nei punti di flesso (cambio di curvatura), il vettore normale inverte bruscamente direzione di 180°, capovolgendo l'oggetto.
        
- **Soluzione - Centro di Interesse (COI):** Si forza l'asse "frontale" a puntare verso un punto specifico (target) e si calcolano gli altri assi con prodotti vettoriali, ignorando la curvatura della linea.
    

### 2.5 Smoothing e Convoluzione

Spesso i dati di animazione (es. da Motion Capture) sono rumorosi. Per pulirli usiamo il **Filtraggio**.

- **Convoluzione:** Ogni punto della curva viene ricalcolato come media pesata dei suoi vicini.
    
- **Kernel:** È la "finestra" di pesi usata per la media.
    
    - Deve essere **simmetrico**, centrato nell'origine, e avere **area unitaria** (la somma dei pesi = 1) per non alterare la scala dell'animazione.
        
    - Esempio: Una gaussiana smussa il segnale mantenendo la forma generale.
        

### 2.6 Integrazione Numerica

Per simulazioni fisiche o calcolo preciso della lunghezza d'arco, dobbiamo integrare funzioni (passare da accelerazione →velocità → posizione).

- **Quadratura Gaussiana:** Metodo avanzato per calcolare integrali definiti (es. lunghezza arco) sommando pochi campioni ben scelti. È molto preciso.
    
- **Metodo di Eulero:**
    
    - xt+Δt​=xt​+vt​⋅Δt
        
    - Semplice ma **instabile**: l'errore si accumula rapidamente, portando l'animazione a "esplodere" (l'oggetto parte per la tangente).
        
- **Runge-Kutta (RK4):**
    
    - Lo standard de facto. Calcola la pendenza in 4 punti diversi all'interno del passo Δt e ne fa una media.
        
    - Molto più stabile e preciso di Eulero, ideale per simulazioni fisiche.
        

---

### 🏛️ Simulazione Esame: Le Domande del Professore

Ecco come questi concetti si trasformano in domande d'esame. Prova a rispondere prima di leggere i suggerimenti.

#### Domanda 1 (Teoria + Pratica)

**"Spiegare perché l'uso diretto del parametro u in una curva spline cubica non garantisce un movimento a velocità costante. Descrivere la tecnica della Look-Up Table per risolvere il problema."**

- _Il punto chiave:_ Devi menzionare che la densità dei punti di controllo altera la relazione tra Δu e la distanza spaziale. Per la LUT, descrivi i due passaggi: 1) Costruzione (campionamento u→s) e 2) Utilizzo inverso (dato strovo u).
    

#### Domanda 2 (Specifico da "Domande Esame.pdf")

**"Presentare il frame di Frenet per la gestione dell'orientamento ed evidenziarne pregi e difetti."**

- _Risposta Modello:_ Il frame di Frenet è un sistema di riferimento locale definito dalla tangente (derivata prima) e dalla curvatura (legata alla derivata seconda).
    
    - _Pregio:_ È automatico, segue perfettamente la geometria della curva.
        
    - _Difetti:_ Soffre di discontinuità (flipping) nei punti di flesso dove la curvatura cambia segno, ed è indefinito nei tratti rettilinei dove la derivata seconda è nulla.
        

#### Domanda 3 (Confronto Tecnico)

**"Nel contesto della simulazione fisica, confrontare il metodo di integrazione di Eulero con quello di Runge-Kutta (RK4). Quale sceglieresti per una simulazione di tessuti e perché?"**

- _Suggerimento:_ Eulero è O(Δt) (errore lineare), RK4 è O(Δt4). Eulero tende a divergere (il tessuto esploderebbe), RK4 è stabile.
    

**Esercizio per casa:** Se volessi applicare un effetto di "Slow Motion" a metà animazione usando una Look-Up Table, come dovresti modificare la funzione di input s(t) (distanza-tempo)? Dovrebbe essere più ripida o più piatta nella zona dello slow motion?