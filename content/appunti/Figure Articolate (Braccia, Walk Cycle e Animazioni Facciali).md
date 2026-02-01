## La Complessità della Figura Umana
Modellare una figura umana è significativamente più difficile rispetto a un robot industriale.
* **Alto numero di gradi di libertà (DOF):** Il corpo umano ha decine di articolazioni.
* **Vincoli complessi:** Le articolazioni umane non ruotano liberamente (es. il gomito non si piega all'indietro).
* **Ridondanza:** Abbiamo molti modi diversi per toccarci la punta del naso (gomito alto, gomito basso).

## B. Il Modello dell'Arto Superiore (7 DOF)
Per simulare realisticamente un braccio umano, si utilizza un modello cinematico a **7 Gradi di Libertà**.

La struttura è così composta:
1.  **Spalla (Shoulder):** Giunto sferico $\rightarrow$ **3 DOF** (Rotazione su 3 assi).
2.  **Gomito (Elbow):** Giunto a cerniera (flessione) + rotazione (pronazione/supinazione dell'avambraccio) $\rightarrow$ **1 o 2 DOF** (solitamente semplificato a 1 per la flessione, spostando la rotazione al polso o aggiungendo un giunto "twist").
3.  **Polso (Wrist):** Giunto complesso $\rightarrow$ **3 DOF** (Flessione, Deviazione, Rotazione).

**Totale:** $3 + 1 + 3 = 7$ DOF.
![[Screenshot 2026-02-01 alle 20.03.49.png]]
### Il Problema della Ridondanza
Per posizionare e orientare un oggetto nello spazio (la mano) servono esattamente **6 DOF** (3 posizione + 3 orientamento).
Poiché il braccio ne ha **7**, abbiamo $7 - 6 = 1$ Grado di Libertà "extra" (ridondante).
* Questo significa che per una stessa posizione fissa della mano e della spalla, il gomito può ancora muoversi descrivendo un arco (o cerchio) nello spazio.



## C. Limiti di Rotazione (Joint Limits)
Un aspetto critico per il realismo è la gestione dei limiti angolari, specialmente nel polso e nella spalla.
* Senza limiti, l'algoritmo di IK potrebbe scegliere una soluzione matematica valida ma biomeccanicamente impossibile (es. polso ruotato di 360°).
* I limiti sono definiti come intervalli $[\theta_{min}, \theta_{max}]$ per ogni asse di ogni giunto.
* Nella risoluzione IK, se un angolo calcolato esce dal range, viene "bloccato" (clamped) al limite più vicino.

---

## D. Strategia di Cinematica Inversa (IK) Procedurale

Per risolvere la postura del braccio data la posizione della mano, si usa spesso un approccio procedurale/geometrico che "modernizza" o semplifica l'uso della matrice Jacobiana classica, rendendolo più intuitivo per l'animazione.

### 1. Il Principio della Distanza
Si basa sull'osservazione fisica che **i giunti più lontani dall'end-effector (es. la spalla) hanno un impatto maggiore sulla posizione della mano** rispetto ai giunti vicini (es. polso).
* Una piccola rotazione della spalla sposta la mano di molto (grande braccio di leva).
* Una rotazione del polso cambia l'orientamento della mano ma sposta poco la sua posizione.

### 2. Algoritmo Geometrico (CCD - Cyclic Coordinate Descent Simile)
Invece di invertire una matrice complessa, si procede iterativamente usando il **Prodotto Vettoriale**:
![[Screenshot 2026-02-01 alle 20.05.02.png]]
Per ogni giunto, partendo (spesso) dal più lontano o iterando sulla catena:
1.  Si calcola il vettore **attuale** verso l'end-effector ($V_{curr}$).
2.  Si calcola il vettore **desiderato** verso il target ($V_{target}$).
3.  Si trova l'asse di rotazione ottimale $a$ tramite il prodotto vettoriale:
    $$a = V_{curr} \times V_{target}$$
4.  Si trova l'angolo $\theta$ tramite il prodotto scalare (coseno).
5.  Si ruota il giunto.

---

## E. La Regola Empirica: Il Piano del Gomito (Swivel Angle)
A causa della ridondanza (quel 7° grado di libertà di cui parlavamo), il computer non sa dove mettere il gomito. Matematicamente ci sono infinite soluzioni.
Per risolvere questo, si introduce una **regola empirica** (Constraint) che l'animatore deve specificare.
![[Screenshot 2026-02-01 alle 20.05.32.png]]
### Definizione del Piano Spalla-Gomito-Polso
L'animatore definisce un piano su cui devono giacere la spalla, il gomito e il polso.
* In software come Maya o Blender, questo è controllato da un **Pole Vector** (Vettore Polare) o **Swivel Angle**.
* L'algoritmo IK:
    1.  Posiziona la Spalla e il Polso (che sono fissi).
    2.  Calcola la posizione del Gomito in modo che la lunghezza delle braccia sia rispettata.
    3.  Ruota l'intero triangolo "Spalla-Gomito-Polso" attorno all'asse Spalla-Polso finché il gomito non punta verso il piano o il vettore specificato dall'animatore.



**Vantaggio:** Questo trasforma un problema mal condizionato (infinite soluzioni) in una soluzione unica e controllabile artisticamente (es. "tieni i gomiti larghi" o "tieni i gomiti stretti lungo il corpo").
# Locomozione: La Camminata (Walk Cycle)

## A. Perché è difficile simulare la camminata?
Camminare sembra un'azione banale, ma dal punto di vista meccanico è estremamente complessa. Viene spesso definita come una **"caduta controllata"**.

1.  **Instabilità intrinseca:** Il Centro di Massa (COM) si sposta continuamente oltre la base di appoggio. Il corpo inizia a cadere in avanti e la gamba scatta per "catturarlo" prima che tocchi terra.
2.  **Sistema Ibrido:** La dinamica alterna fasi continue (il movimento fluido della gamba in aria) a fasi discrete (l'impatto improvviso del tallone a terra).
3.  **Gestione dell'Equilibrio:** Richiede un coordinamento preciso tra forze attive (muscoli) e passive (gravità, inerzia).
4.  **Molteplici DOF:** Coinvolge la coordinazione di anche, ginocchia, caviglie, colonna vertebrale e braccia (per il controbilanciamento).

## B. Modellazione della Gamba
Per analizzare la camminata, semplifichiamo la gamba come un **manipolatore articolato planare**.
* **Struttura:** Solitamente modellata con 3 segmenti rigidi principali:
    1.  Coscia (Femore).
    2.  Gamba inferiore (Tibia).
    3.  Piede.
* **Cinematica Inversa (IK):** È essenziale per la gamba in appoggio. Quando il piede è a terra, è bloccato (vincolo fisso). È il bacino che si muove rispetto al piede. L'IK calcola gli angoli di ginocchio e anca per mantenere il piede fermo mentre il corpo avanza.
![[Screenshot 2026-02-01 alle 20.09.56.png]]
---

## C. Il Classico Walk Cycle (Le 4 Fasi Chiave)
![[Screenshot 2026-02-01 alle 20.10.09.png]]
Un ciclo di camminata completo (due passi, destro e sinistro) si divide in **Fase di Appoggio (Stance)** (60% del ciclo) e **Fase di Oscillazione (Swing)** (40% del ciclo).

Per gli animatori, si scompone in 4 pose fondamentali:

1.  **Contact (Contatto / Heel Strike):**
    * L'istante in cui il tallone della gamba avanzata tocca terra.
    * Le gambe sono alla massima estensione (apertura a compasso).
    * Il peso è distribuito al 50-50 tra le due gambe.
2.  **Recoil / Down (Ammortizzazione):**
    * Subito dopo il contatto, il ginocchio si piega per assorbire il peso del corpo.
    * È il punto più **basso** della testa nel ciclo.
    * Tutto il peso è sulla gamba di appoggio.
3.  **Passing Position (Passaggio):**
    * La gamba libera passa davanti alla gamba di appoggio.
    * Il corpo inizia a risalire.
4.  **High Point / Push-off (Spinta):**
    * Il tallone della gamba di appoggio si alza per spingere il corpo in avanti.
    * È il punto più **alto** della testa.
    * La gamba sta per staccarsi da terra (Toe-off).


![[Screenshot 2026-02-01 alle 20.10.28.png]]
---

## D. Walk Cycle vs. Run Cycle (Camminata vs Corsa)

Qual è la differenza scientifica tra camminare e correre? Non è solo la velocità.

### 1. Walk Cycle (Camminata)
* **Doppio Supporto (Double Support):** Esiste sempre un momento in cui **entrambi i piedi toccano terra** contemporaneamente (durante la fase di Contact).
* Non c'è mai un momento in cui entrambi i piedi sono staccati da terra.

### 2. Run Cycle (Corsa)
![[Screenshot 2026-02-01 alle 20.10.37.png]]
* **Fase di Volo (Flight Phase / Airborne):** Esiste un momento in cui **nessun piede tocca terra**.
* **Assenza di Doppio Supporto:** Non accade mai che entrambi i piedi siano a terra contemporaneamente.
* *Dinamica energetica:* Nella corsa, la gamba agisce come una molla che immagazzina energia elastica all'impatto e la rilascia nella spinta.



---

## E. Cinematica della Camminata ed Esempio Analitico

Se tracciamo il movimento del bacino (o del Centro di Massa) durante la camminata, notiamo che non si muove in linea retta, ma descrive curve sinusoidali complesse.
![[Screenshot 2026-02-01 alle 20.11.00.png]]
### 1. Movimento Verticale (Y)
Il bacino sale e scende due volte per ogni ciclo completo (una volta per passo).
* **Minimo:** Nella fase di *Recoil/Down* (gambe piegate).
* **Massimo:** Nella fase di *Passing/High Point* (gamba tesa).

### 2. Movimento Laterale (X)
Il peso del corpo deve spostarsi sopra la gamba di appoggio per mantenere l'equilibrio.
* Quando appoggio il piede destro, il bacino si sposta a destra.
* Quando appoggio il piede sinistro, si sposta a sinistra.

### 3. Rotazione Pelvica (L'esempio chiave)
Per allungare il passo senza dover piegare eccessivamente le gambe o cadere, il bacino ruota sull'asse verticale (Y).
* Quando la gamba destra avanza, il lato destro del bacino ruota in avanti.
* **Effetto:** Questo "allunga" virtualmente la gamba, rendendo la camminata più efficiente e la traiettoria del centro di massa meno "balzellante" (appiattisce gli archi sinusoidali), risparmiando energia.

**Esempio di Analisi:**
Immaginiamo di voler animare un cammino "furtivo" (Sneak).
* **Differenza Cinematica:** Invece di avere il tallone che impatta rigidamente (Heel Strike), il contatto avviene con la punta o la pianta (per ridurre rumore).
* **Centro di Massa:** Rimane costantemente basso (niente High Point), riducendo l'oscillazione verticale per massimizzare la stabilità.
# Animazione Facciale: Modellazione e Tecniche

## A. Creazione del Modello: La Topologia è tutto
Prima di animare, dobbiamo costruire una mesh adatta. Un modello statico bello non è necessariamente un modello animabile.

Esistono due tecniche principali di acquisizione/creazione:
1.  **Scansione 3D / Fotogrammetria:**
    * Si acquisisce un volto reale tramite laser scanner o fotografie multiple.
    * *Pro:* Realismo statico perfetto (pori, rughe).
    * *Contro:* La topologia risultante è spesso un "caos" di triangoli disordinati, inadatta alla deformazione. Richiede **Retopology**.
2.  **Modellazione Basata su Edge-Loops (Manuale):**
    * Si costruisce la mesh disponendo i poligoni in anelli concentrici (loops) attorno alle aperture naturali (occhi, bocca).
    * *Fondamentale:* Gli edge-loops devono seguire l'orientamento reale delle fibre muscolari sottostanti.
    * *Pro:* Deformazioni pulite senza artefatti di shading durante l'animazione.

---

## B. Interpolazione di Pose Chiave (Blend Shapes)
È il metodo più diffuso nell'industria cinematografica e videoludica (usato ad esempio per Gollum o nei film Pixar).

### Il Concetto
L'animatore o il modellatore crea una serie di versioni distorte della mesh base, chiamate **Target Shapes** o **Morph Targets**.
* Base: Faccia neutra.
* Target 1: Sorriso.
* Target 2: Sopracciglio alzato.

L'animazione avviene interpolando linearmente i vertici tra la posa base e i target.
$$P_{final} = P_{base} + \sum_{k=1}^{n} w_k (P_{target,k} - P_{base})$$
Dove $w_k$ è il peso (weight) dell'espressione $k$ (da 0 a 1).

### Limiti del Metodo
1.  **Linearità:** I vertici si muovono in linea retta. La pelle reale, invece, scivola sulle ossa e si gonfia curvando.
2.  **Conflitti:** Attivare insieme due shape che influenzano la stessa area (es. "Sorrido" + "Bocca aperta") può creare volumi strani se non corretti manualmente.
3.  **Storage:** Richiede di memorizzare molte copie della mesh.

---

## C. FACS e Action Units (AU)![[Screenshot 2026-02-01 alle 20.16.55.png]]
Per standardizzare le pose, l'industria adotta il **FACS (Facial Action Coding System)**, sviluppato dagli psicologi Ekman e Friesen.

* **L'idea:** Non modellare "emozioni" (Felicità, Tristezza), che sono soggettive, ma modellare **movimenti muscolari atomici**.
* **Action Units (AU):** Sono le unità minime di movimento.
    * *AU 1:* Inner Brow Raiser (Frontalis, pars medialis).
    * *AU 12:* Lip Corner Puller (Zygomaticus Major - il muscolo del sorriso).
    * *AU 46:* Wink (Occhiolino).
* **Vantaggio:** Un'emozione complessa diventa una "ricetta" di AU. Es. Felicità = AU 6 (Guance su) + AU 12 (Angoli bocca su).

---

## D. Modellazione Basata sui Muscoli (Anatomical Approach)
Invece di spostare i vertici a mano (Blend Shapes), simuliamo la struttura anatomica sottostante. Definiamo tre tipologie di muscoli virtuali:

### 1. Tipologie di Muscoli
* **Lineari (Linear Muscles):**
    * Trazionano in una direzione specifica.
    * *Esempio:* **Zigomatico Maggiore**. Collega lo zigomo all'angolo della bocca. Quando si contrae, tira l'angolo verso l'alto-esterno.
* **Laminari (Sheet Muscles):**
    * Non sono filamenti, ma "fogli" di fibre piatti. Non hanno un punto di origine puntiforme ma diffuso.
    * *Esempio:* **Frontalis** (Fronte). Solleva un'ampia area di pelle (le sopracciglia e la pelle della fronte).
* **Radiali / Sfinteri (Sphincter Muscles):**
    * Muscoli circolari che circondano gli orifizi. Quando si contraggono, stringono verso il centro (come il diaframma di una macchina fotografica).
    * *Esempio:* **Orbicularis Oculi** (attorno all'occhio), **Orbicularis Oris** (attorno alla bocca).

### 2. Attivazione e Reazione Muscolare
Come si muove la pelle (la mesh) quando il muscolo virtuale si contrae?

#### Modello A: Geometrico (Distanza dal punto di inserzione)
È un approccio cinematico veloce.
Si definisce un **vettore muscolare** (dal punto di attacco osseo al punto di inserzione nella pelle).
Ogni vertice della pelle all'interno di un raggio d'azione ($R$) viene spostato verso l'origine del muscolo.
Lo spostamento è pesato in base alla distanza e all'angolo:
* I vertici vicini al punto di inserzione si muovono molto.
* I vertici lontani o laterali si muovono poco (fall-off function, spesso cosinusoidale).
* *Difetto:* Non simula il volume (la pelle non si "arriccia" realisticamente).

#### Modello B: Fisico Viscoelastico (Mass-Spring System)
È un approccio dinamico accurato.
La pelle è modellata come una rete di masse collegate da molle e smorzatori (Mass-Spring).
1.  Il muscolo applica una **Forza** ai nodi della rete.
2.  La rete reagisce fisicamente propagando la forza.
3.  **Viscoelasticità:** La pelle ha resistenza al cambiamento (viscosità) e tende a tornare alla forma originale (elasticità).
* *Pro:* Crea rughe ed effetti di volume (bulging) automatici e realistici.
* *Contro:* Computazionalmente pesante.