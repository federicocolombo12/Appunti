## 1. Percezione dell'Animazione: Mito vs Realtà

Perché vediamo il movimento dove non c'è? È fondamentale distinguere tra credenza popolare e spiegazione scientifica.

### Il mito della "Persistenza della Visione"

- **Definizione:** È la teoria (errata) secondo cui l'immagine rimane impressa sulla retina per una frazione di secondo (circa 1/25 o 1/10 di secondo), fondendosi con la successiva.
    
- **Realtà:** La persistenza della visione spiega solo perché non vediamo il "nero" tra un frame e l'altro (evita il _flicker_), ma **non spiega il movimento**. Se fosse l'unica causa, vedremmo una scia di immagini sovrapposte, non un moto fluido.
    

### La spiegazione reale: Fenomeno Phi e Movimento Beta

Il movimento è una ricostruzione attiva del **cervello**, non dell'occhio.

- **[[Fenomeno Phi]] (o movimento Beta):** È un processo psicofisico (studiato dalla Gestalt, es. Max Wertheimer) in cui il cervello interpreta una successione di immagini statiche leggermente diverse come un unico oggetto in movimento.
    
- **Key Concept:** L'animazione è un'"illusione cognitiva", non retinica.
    

### Artefatti Visivi

Quando i parametri non sono corretti, l'illusione si rompe:

- **[[Flicker]] (Sfarfallio):** Avviene se il _refresh rate_ è troppo basso (sotto i 50-60Hz). L'occhio percepisce l'intervallo nero tra i frame.
    
- **[[Motion Blur]] (Sfocatura):** In una cinepresa reale, un oggetto veloce appare sfocato perché l'otturatore rimane aperto per un certo tempo. In Computer Graphics, i frame sono nitidi (tempo zero). Senza _motion blur_ artificiale, oggetti veloci causano **Strobing** (sembrano saltellare in modo innaturale).
    

---

## 2. Le Origini: Dal Pre-Cinema al Cinematografo

L'uomo ha sempre cercato di simulare il movimento prima ancora dell'invenzione della pellicola. Questi dispositivi sono spesso oggetto di domande d'esame.

### Dispositivi Ottici (Pre-Cinema)

1. **Lanterna Magica (1600s):** Proiettore di immagini statiche su vetro tramite una fonte di luce (candela/lampada). Non è animazione, ma è il "padre" del proiettore.
    
2. **[[Taumatropio]] (1824):** Un dischetto con due immagini diverse sui lati (es. uccello e gabbia). Facendolo ruotare velocemente, le immagini si sovrappongono per la _persistenza della visione_. **Nota:** Non è vero movimento, è solo fusione statica.
    
3. **[[Fenachistoscopio]] (1832):** Disco con fessure radiali e disegni sequenziali. Guardando attraverso le fessure in uno specchio, si vede il movimento ciclico. È il primo vero dispositivo di animazione.
    
4. **[[Zootropo]] (1834):** Cilindro con fessure verticali. Permetteva a più persone di guardare l'animazione contemporaneamente. Difetto: le immagini appaiono distorte e meno luminose.
    
5. **[[Prassinoscopio]] (1877):** Evoluzione dello Zootropo. Usa specchi al centro invece delle fessure. Risultato: immagini più luminose, nitide e stabili (migliore esperienza visiva).
    

### La Nascita del Cinema

- **Cinematografo (Fratelli Lumière, 1895):** Unisce la ripresa, la stampa e la proiezione in un unico strumento. Segna il passaggio dalla fruizione individuale (Zootropo) a quella collettiva in sala.
    

---

## 3. L'Animazione Tradizionale e la Multiplane Camera

Con la Disney, l'animazione diventa industria. La comprensione di questa _pipeline_ è essenziale perché la Computer Graphics moderna (CG) ne eredita la terminologia.

### La Pipeline Tradizionale (Disney)

1. **Storyboard:** Disegni schizzati che raccontano la storia visiva (come un fumetto).
    
2. **Layout:** Definizione precisa degli sfondi e delle posizioni dei personaggi.
    
3. **Key Animation (Keyframe):** L'animatore _Senior_ disegna solo i fotogrammi estremi (chiavi) che definiscono l'azione (es. inizio e fine salto).
    
4. **In-betweening (Intercalazione):** L'assistente (_Junior_) disegna tutti i frame intermedi per fluidificare il moto. Oggi questo lo fa il computer!.
    
5. **Ink & Paint:** I disegni vengono trasferiti su fogli di acetato trasparente (**Cels**) e colorati sul retro.
    

### La [[Multiplane Camera]] (Camera Multipiano)

Inventata e perfezionata dagli studi Disney (William Garity) negli anni '30 (usata in _The Old Mill_ e _Biancaneve_).

- **Problema:** Nel 2D classico, sfondi e personaggi sembrano piatti. Se la camera zooma, tutto si ingrandisce allo stesso modo, distruggendo l'illusione di profondità.
    
- **Soluzione:** Una struttura verticale alta svariati metri con **piani di vetro multipli**.
    
    - Gli elementi della scena (primo piano, personaggi, sfondo lontano) sono posti su vetri diversi a diverse altezze.
        
    - La camera guarda dall'alto verso il basso.
        
- **Effetto Parallasse:** Muovendo i piani di vetro a **velocità diverse** (gli oggetti vicini si muovono più velocemente, quelli lontani più lentamente), si crea un effetto di profondità 3D realistico.
    

---

Ti lascio questo video straordinario in cui lo stesso **Walt Disney** spiega la Multiplane Camera. È fondamentale per capire visivamente il concetto di parallasse che useremo poi in Computer Graphics.

[Disney spiega la Multiplane Camera](https://www.youtube.com/watch?v=ytJTE_HD9mc)

## Tecniche di Animazione (Storiche e Moderne)

Queste tecniche rappresentano l'evoluzione del medium. Spesso l'esame chiede di confrontarle o di spiegare come la CG le ha simulate.

### Ink & Paint (Inchiostrazione e Coloritura)

- **Tradizionale:** Una volta approvati i disegni su carta, venivano trasferiti (inchiostrati) su fogli di acetato trasparente (**Cels**) e poi colorati (dipinti) sul retro con vernice acrilica. Questo permetteva di sovrapporli agli sfondi.
    
- **Digitale (Digital Ink & Paint):** Introdotto dalla Disney con il sistema **CAPS** (fine anni '80). I disegni vengono scansionati e colorati digitalmente.
    
    - _Vantaggio:_ Palette colori infinita, livelli trasparenti, nessuna polvere o graffio sui cel.
        

### Shooting on Twos vs Ones

Riguarda la frequenza dei fotogrammi (Frame Rate). Il cinema va a **24 fps**.

- **On Ones (A uno):** Un disegno diverso per ogni fotogramma (24 disegni al secondo). Movimento fluidissimo, molto costoso. Usato per azioni veloci o telecamera in movimento.
    
- **On Twos (A due):** Ogni disegno viene fotografato per due fotogrammi consecutivi (12 disegni al secondo). È lo standard dell'animazione tradizionale. L'occhio percepisce comunque fluidità.
    
- **On Threes/Fours:** Usato negli Anime giapponesi (limitata) per risparmiare budget o per momenti statici/dialoghi.
    

### Full Animation vs Limited Animation

- **Full Animation (Piena):**
    
    - Standard Disney classico.
        
    - Priorità alla fluidità del movimento (spesso _on ones_).
        
    - Recitazione complessa, personaggi che si muovono interamente.
        
- **Limited Animation (Limitata):**
    
    - Standard Hanna-Barbera (es. _Scooby Doo_) o Anime televisivi.
        
    - Priorità al risparmio e alla velocità di produzione.
        
    - Uso estensivo di cicli ripetuti (camminate), _shooting on threes_, e animazione parziale (si muove solo la bocca o gli occhi su un corpo statico).
        

### Rotoscoping

- Tecnica inventata da **Max Fleischer**.
    
- Consiste nel filmare attori reali e ricalcare i loro movimenti fotogramma per fotogramma.
    
- _Esempio:_ La danza di _Biancaneve_ o le spade laser in _Star Wars_. In CG si usa per catturare movimenti realistici (precursore del Motion Capture).
    

### Stop Motion (Passo uno)

- Animazione di oggetti fisici (pupazzi, plastilina - _Claymation_) mossi impercettibilmente a mano e fotografati frame per frame.
    
- _Esempio:_ _Nightmare Before Christmas_, _Wallace & Gromit_.
    
- Ha una "matericità" unica che la CG cerca spesso di imitare.
    

### Tecnica Mista (Tradizionale + Live Action)

- Integrare personaggi disegnati in riprese dal vero.
    
- Richiede una pianificazione perfetta (es. _Chi ha incastrato Roger Rabbit_ o _Mary Poppins_). In CG oggi è la norma (VFX nei film Marvel).
    

---

## 2. I Principi della Computer Animation

Basati sul famoso paper di **John Lasseter** (Pixar, 1987) che applicava i 12 principi dell'animazione Disney alla grafica 3D. Eccone i fondamentali per l'esame:

### A. Squash and Stretch (Compressione e Dilatazione)

- Il principio più importante. Definisce la rigidità e la massa di un oggetto.
    
- Un oggetto flessibile, quando impatta, si schiaccia (**Squash**) e quando accelera si allunga (**Stretch**).
    
- **Regola d'oro:** Il **volume** deve rimanere costante. Se schiaccio in Y, devo allargare in X e Z.
    

### B. Anticipation (Anticipazione)

- Un'azione non deve mai avvenire improvvisamente. Il pubblico deve essere preparato.
    
- _Esempio:_ Prima di tirare un pugno, il personaggio tira indietro il braccio. Prima di saltare, si flette sulle ginocchia.
    
- Guida l'occhio dello spettatore.
    

### C. Staging (Messa in scena)

- Presentare l'azione in modo che sia inequivocabile.
    
- Uso della silhouette chiara, inquadratura corretta, evitare dettagli che distraggono.
    

### D. Follow Through & Overlapping Action

- **Follow Through:** Quando il corpo si ferma, le parti molli (capelli, vestiti, coda) continuano a muoversi per inerzia.
    
- **Overlapping:** Le parti del corpo non si muovono tutte insieme (effetto robot), ma con sfasamenti temporali (prima il braccio, poi l'avambraccio, poi la mano).
    

### E. Timing

- La velocità di un'azione definisce il peso e l'emozione.
    
- Più frame = azione lenta (pesante, stanca).
    
- Meno frame = azione veloce (leggera, energica).
    

---

## 3. Fisica e Simulazione

In CG, non animiamo tutto a mano. Usiamo la fisica per il realismo secondario.

- **Dinamica dei Corpi Rigidi (Rigid Body):** Calcola collisioni, gravità e rimbalzi per oggetti che non si deformano (es. un muro che crolla).
    
- **Sistemi Particellari:** Usati per fenomeni fluidi o caotici (fuoco, fumo, pioggia, sciami). Non si anima la singola particella, ma si definiscono regole globali (vita, velocità, gravità).
    
- **Forward Kinematics (FK) vs Inverse Kinematics (IK):**
    
    - **FK:** Ruoto la spalla -> si muove il gomito -> si muove la mano. (Bene per archi di movimento ampi).
        
    - **IK:** Posiziono la mano -> il computer calcola come piegare gomito e spalla. (Essenziale per i piedi a terra o mani che afferrano oggetti).
        

### Azioni Estetiche vs Azioni Fisiche

- **Fisiche:** Rispondono alle leggi di Newton (gravità, inerzia). Necessarie per la credibilità.
    
- **Estetiche:** Violano la fisica per espressività (es. un personaggio che rimane sospeso in aria prima di cadere - _Coyote time_, o occhi che escono dalle orbite). Un buon animatore sa quando rompere la fisica per lo stile.
    

---

## 4. La Pipeline di Produzione e NLE

### Pipeline di Produzione (Il flusso di lavoro)

Dividiamo il lavoro in 3 macro-fasi:

1. **Pre-Produzione:**
    
    - _Idea/Script:_ La sceneggiatura.
        
    - _Storyboard:_ Disegni sequenziali delle inquadrature.
        
    - _Animatic:_ Storyboard montato con tempi e sonoro provvisorio.
        
    - _Concept Art:_ Design di personaggi e ambienti.
        
2. **Produzione:**
    
    - _Modellazione:_ Creazione della geometria 3D.
        
    - _Rigging:_ Inserimento dello scheletro per muovere il modello.
        
    - _Layout:_ Posizionamento approssimativo di camere e oggetti (il "set").
        
    - _Animation:_ Movimento vero e proprio.
        
    - _Shading/Texturing:_ Definizione dei materiali (colore, riflessione).
        
    - _Lighting:_ Illuminazione della scena.
        
    - _Rendering:_ Calcolo dell'immagine finale.
        
3. **Post-Produzione:**
    
    - _Compositing:_ Unione dei vari livelli (render pass) e aggiunta di effetti 2D.
        
    - _Color Correction:_ Bilanciamento finale dei colori.
        
    - _NLE (Non-Linear Editing):_ Montaggio video finale (Premiere, Avid, Final Cut).
        

### Cos'è l'NLE (Non-Linear Editing)?

- A differenza del montaggio su pellicola (fisico e lineare, devi tagliare e incollare in ordine) o su nastro magnetico, l'**NLE** è digitale.
    
- **Accesso Casuale:** Puoi accedere istantaneamente a qualsiasi clip video, spostarla, tagliarla e modificarla senza distruggere il file originale (_non-destructive_).
    
- È il cuore del montaggio moderno.