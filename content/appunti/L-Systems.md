
La modellazione della vegetazione è una sfida unica in [[Computer Animation]]. Le piante esibiscono una **complessità arbitraria** (migliaia di foglie, rami intricati) pur mantenendo una **struttura ramificata vincolata**.

### Caratteristiche fondamentali:
* **Sorgente singola:** Una pianta cresce partendo da un unico punto.
* **Sviluppo temporale:** La struttura si ramifica e gli elementi si allungano nel tempo.
* **Self-similarity:** Spesso i rami più piccoli assomigliano all'intera pianta (frattali).
![[Pasted image 20260202195203.png]]
### Tecniche di Modellazione
Per rappresentare questa complessità si utilizzano tre approcci principali:
1. **[[Frattali]]**: Utili per la natura auto-similare, ma a volte geometricamente troppo rigidi.
2. **[[Sistemi Particellari]]**: Possono simulare volumi e chiome, ma mancano di struttura topologica rigida.
3. **[[L-Systems]]**: Il metodo più efficace per descrivere la topologia e la crescita biologica basandosi su regole di riscrittura di stringhe.

---

## 2. Morfologia delle Piante (Componenti Strutturali)
Per scrivere un L-System credibile, dobbiamo conoscere l'anatomia che stiamo simulando 
![[Pasted image 20260202195216.png]]
### Componenti Base
I moduli fondamentali che compongono la pianta sono:
* **Radice (Root):** Tipicamente invisibile e ignorata nel rendering, a meno che non sia esposta.
* **Gemma (Bud):** L'apice vegetativo da cui avviene la crescita.
* **Fusto (Stem):** L'asse di supporto.
* **Foglia (Leaf):** Organo laminare per la fotosintesi.
* **Fiore (Flower):** Organo riproduttivo.

### Tipologie di Fusto
* **Piante Erbacee:** Struttura leggera, piccola, ramificazione regolare. Non risentono eccessivamente della gravità (possono essere modellate con regole più semplici).
* **Piante Legnose (Woody):** Pesanti, strutturate. La ramificazione deve prevedere interferenze e subisce deformazioni dovute alla gravità e al vento (richiedono simulazione fisica accoppiata all'L-System).

---

## 3. Topologia e Ramificazione
Il fusto emerge dal terreno e cresce verticalmente. La sua struttura è segmentata in moduli ripetitivi.

* **Nodo:** Il punto in cui le foglie o i rami si attaccano al fusto.
* **Internodo:** La porzione di fusto compresa tra due nodi successivi.

### Pattern di Disposizione Fogliare (Fillotassi)
Le foglie nascono dalle gemme secondo pattern regolari:
* **Alternate:** Una foglia per nodo, su lati alterni.
* **Opposte:** Due foglie per nodo, una di fronte all'altra.
* **A Spirale:** Una foglia per nodo, ma ruotata di un angolo costante (spesso legato alla sezione aurea) rispetto alla precedente.

### Schemi di Ramificazione (Branching Patterns)
(Riferimento **Slide 3**). La crescita della struttura assiale può avvenire secondo due modalità principali:

#### A. Ramificazione Monopodiale
Esiste un asse principale (il tronco) che cresce indefinitamente e rimane dominante. I rami laterali sono subordinati e tipicamente più piccoli.
* *Esempio:* Abeti, Pini.
* *In L-System:* Il simbolo dell'apice principale viene preservato nella regola di riscrittura, generando rami laterali come "parentesi".

#### B. Ramificazione Dicotomica (o Simpodiale)
L'apice del fusto si divide in due nuovi apici di uguale importanza (o quasi). Non c'è un tronco centrale dominante che prosegue dritto all'infinito; la struttura si biforca costantemente.
* *Esempio:* Alcune felci, alghe, o alberi decidui complessi.
* *In L-System:* Il simbolo dell'apice viene sostituito da due nuovi simboli divergenti.
![[Pasted image 20260202195710.png]]
---

## 4. Le Gemme: Il Motore della Crescita
Le gemme sono embrioni di tessuto vegetale. La loro gestione è cruciale negli L-System contestuali.

### Classificazione per Posizione
* **Gemma Terminale (Apicale):** Situata alla fine del ramo/fusto. Determina l'allungamento.
* **Gemma Laterale (Ascellare):** Situata lungo il fusto (all'ascella della foglia). Determina la ramificazione laterale.

### Classificazione per Funzione
* **Vegetative:** Danno origine a nuovi fusti e foglie.
* **Floreali:** Danno origine ai fiori (terminando la crescita di quel ramo).
![[Pasted image 20260202195723.png]]
### Stato della Gemma
* **Attiva:** Produce nuovi moduli al passo successivo.
* **Dormiente:** Non produce nulla finché un segnale (es. taglio del ramo principale o cambio di stagione) non la attiva.

---

## 5. Fattori di Crescita Cellulare
La simulazione non è solo geometrica ma simula processi biologici. La crescita di una cellula o di un modulo è influenzata da:
1. **Invecchiamento (Aging):** I rami diventano più spessi e rigidi col tempo.
2. **Trasmissione (Lineage):** Informazioni passate dalla cellula madre alla figlia (es. nutrienti).
3. **Trofismo:** Risposta a stimoli esterni.
    * *Fototropismo:* Crescita verso la luce.
    * *Gravitropismo:* Crescita contro (o verso) la gravità.
4. **Ostacoli:** Collision detection con l'ambiente o con la pianta stessa (self-collision).
![[Pasted image 20260202195754.png]]

# L-System: Logica Formale e Turtle Graphics

## 1. Definizione e Classificazione
Gli **L-System** (Lindenmayer Systems) sono sistemi di riscrittura parallela di stringhe introdotti dal biologo Aristid Lindenmayer nel 1968.

### D0L-Systems (Deterministic 0-Context)
Il caso più semplice è il **D0L-System**.
* **D (Deterministico):** Per ogni simbolo dell'alfabeto esiste **una sola** regola di produzione (o nessuna). Non c'è ambiguità su come sostituire un simbolo.
* **0 (Context-Free):** La riscrittura di un simbolo dipende solo dal simbolo stesso, non dai suoi vicini (sinistra o destra).

---

## 2. Componenti del Sistema
Un L-System è definito da una tupla composta da:
1.  **Alfabeto ($V$):** L'insieme dei simboli validi (es. $\{F, +, -, A\}$).
2.  **Assioma ($\omega$):** La stringa iniziale (stato al tempo $t=0$).
3.  **Regole di Produzione ($P$):** Definiscono come ogni simbolo viene trasformato al passo successivo.
    * Struttura: `Predecessore (α) -> Successore (β)`
    * *Nota:* Se un simbolo non ha una regola esplicita, si assume la regola identità ($A \rightarrow A$).

### Il Processo di Derivazione
Le regole vengono applicate **in parallelo** e simultaneamente a tutti i caratteri della stringa corrente.
* *Iterazione $n$:* Stringa di input.
* *Iterazione $n+1$:* Ogni carattere della stringa $n$ viene sostituito dal suo successore definito nelle regole.
![[Pasted image 20260202201933.png]]
---

## 3. Interpretazione Geometrica
Una volta generata la stringa finale (dopo $N$ iterazioni), questa deve essere tradotta in immagine. Esistono due metodi principali:

### A. Sostituzione Geometrica Diretta
Ogni simbolo della stringa viene sostituito da un oggetto grafico predefinito.
* *Esempio:* In una stringa `XXTTXX`, ogni `X` piazza un segmento e ogni `T` piazza una forma a "V".
* *Limite:* Metodo rigido, poco adatto a strutture ramificate complesse e continue.
![[Pasted image 20260202201945.png]]
### B. Interpretazione [[Turtle Graphics]]
La stringa viene letta come una sequenza di comandi impartiti a un cursore (la "Tartaruga") che si muove nello spazio cartesiano.
* **Stato della Tartaruga:** È definito dalla terna $(x, y, \alpha)$.
    * $x, y$: Posizione cartesiana corrente.
    * $\alpha$: Orientamento (angolo) corrente rispetto a un asse di riferimento.
* **Parametri Globali:**
    * $d$: Passo di avanzamento (step size).
    * $\delta$: Passo angolare (angle increment).

#### Comandi Fondamentali
Ecco come i simboli modificano lo stato $(x, y, \alpha)$:

| Simbolo | Azione | Descrizione | Nuovo Stato Matematico |
| :--- | :--- | :--- | :--- |
| **F** | Move & Draw | Avanza di $d$ disegnando una linea. | $\begin{cases} x' = x + d \cdot \cos(\alpha) \\ y' = y + d \cdot \sin(\alpha) \\ \alpha' = \alpha \end{cases}$ |
| **f** | Move (No Draw) | Avanza di $d$ **senza** disegnare (salto). | *Idem come sopra (cambiano x,y ma non traccia linea).* |
| **+** | Turn Left | Ruota a sinistra (senso antiorario) di $\delta$. | $\alpha' = \alpha + \delta$ |
| **-** | Turn Right | Ruota a destra (senso orario) di $\delta$. | $\alpha' = \alpha - \delta$ |

---

![[Pasted image 20260202202009.png]]ù
![[Pasted image 20260202202018.png]]
# Bracketed L-Systems: Ramificazioni Complesse

## 1. Il Problema della Continuità
Negli L-System semplici (lineari), la tartaruga disegna una linea continua. Una volta disegnato un segmento, lo stato si è spostato.
* **Limite:** Impossibile disegnare un albero dove da un singolo nodo partono due o più rami distinti, perché la tartaruga non saprebbe come "tornare indietro" al nodo dopo aver disegnato il primo ramo.

## 2. La Soluzione: Stack (Pila)
Si introducono due nuovi simboli nell'alfabeto che agiscono sulla memoria della Tartaruga.

* **`[` (Push - Inserzione):**
    * Salva lo stato corrente della tartaruga $(x, y, \alpha, color, width)$ in cima a uno **Stack** (Pila LIFO - Last In First Out).
    * *Significato biologico:* "Inizia una diramazione da questo punto".
* **`]` (Pop - Estrazione):**
    * Recupera (toglie) l'ultimo stato salvato dallo Stack e teletrasporta la tartaruga a quelle coordinate e orientamento.
    * *Significato biologico:* "Ho finito di disegnare questo ramo, torno al punto di attaccatura per continuare il fusto principale o fare un altro ramo".

### Struttura Logica
L'uso delle parentesi trasforma la struttura da lineare a **gerarchica/ricorsiva**.
* Una stringa bilanciata di parentesi rappresenta un albero topologico.
* **Esempio:** `F[+F]F`
    1. `F`: Disegna fusto.
    2. `[`: Salva posizione (NODO).
    3. `+F`: Ruota e disegna ramo laterale.
    4. `]`: Torna al NODO (senza disegnare, salta indietro).
    5. `F`: Continua il fusto principale.

---
### Esercizio 1: Interpretazione della Stringa

**Stringa:** `FF[+F[-F[-F[+F]F]F]FF][-FF[+F[-F]F[-F]]F]`

Per disegnare la struttura (o descriverla all'esame), dobbiamo indentare mentalmente ogni volta che vediamo una `[` e tornare indietro quando vediamo una `]`.

**Analisi Strutturale:**

1. **`FF`**: Fusto iniziale (lunghezza 2 unità), verticale.
    
2. **`[ ... ]` (Primo Blocco - Ramo Sinistro)**:
    
    - Al termine del fusto, la tartaruga salva la posizione (Nodo principale).
        
    - `+`: Ruota a Sinistra.
        
    - `F`: Disegna segmento.
        
    - `[-F[-F[+F]F]F]`: Da qui parte una sotto-ramificazione verso destra (`-`) complessa (nidificata).
        
    - `FF`: Il ramo sinistro principale prosegue per 2 unità.
        
    - _`]` Fine Ramo Sinistro: Si torna al Nodo principale sul fusto._
        
3. **`[ ... ]` (Secondo Blocco - Ramo Destro)**:
    
    - Siamo tornati al Nodo principale.
        
    - `-`: Ruota a Destra.
        
    - `FF`: Disegna ramo lungo 2 unità.
        
    - `[+F[-F]F[-F]]`: Da qui parte una sotto-ramificazione a sinistra (`+`) con varie foglie/spine.
        
    - `F`: Il ramo destro termina con un ultimo segmento.
        
    - _`]` Fine Ramo Destro: Si torna al Nodo principale._
        
4. (Fine della stringa): La pianta finisce qui. Non c'è crescita apicale ulteriore oltre il nodo di biforcazione.
    

**Risultato Visivo:**

È una pianta a forma di **Y**. Ha un tronco basso (`FF`) che si divide in due grossi rami principali (uno a sx, uno a dx). Entrambi i rami sono "pelosi" o complessi, avendo a loro volta ramificazioni interne.
![[Pasted image 20260202204558.png]]
---

### ✍️ Esercizio 2: Derivazione e Disegno

**Dati:**

- Assioma $S = FFABFACFF$
    
- $A = [+FBF]$
    
- $B = [-FFCF]$
    
- $C = F$
    

**Obiettivo:** Trovare la geometria finale sostituendo i simboli logici ($A, B, C$) con i comandi di disegno ($F, +, -, [, ]$).

**Passo 1: Risoluzione delle dipendenze (Sostituzione)**

Sostituiamo le regole partendo dalle più interne per capire cosa sono i moduli A e B.

- Dato che $C = F$, allora $B$ diventa: `[-FFFF]`
    
- Dato che $B = [-FFFF]$ e $C = F$, allora $A$ diventa: `[+F[-FFFF]F]`
    

**Passo 2: Espansione dell'Assioma S**

Sostituiamo nell'assioma:

`S` = `FF` + `A` + `B` + `F` + `A` + `C` + `FF`

Diventa:

`FF` `[+F[-FFFF]F]` `[-FFFF]` `F` `[+F[-FFFF]F]` `F` `FF`

**Passo 3: Analisi del Disegno (Layout)**

Immaginiamo di percorrerla:

1. **`FF`**: Tronco base (2 unità).
    
2. **`[+F[-FFFF]F]` (Ex Modulo A)**:
    
    - Salva posizione.
        
    - Gira a SX (`+`), avanza 1 (`F`).
        
    - Da lì, spara un ramo laterale a DX lungo 4 (`[-FFFF]`).
        
    - Avanza ancora 1 (`F`).
        
    - _Chiude ramo, torna al tronco._
        
3. **`[-FFFF]` (Ex Modulo B)**:
    
    - Salva posizione (siamo ancora sul tronco principale, allo stesso punto di prima!).
        
    - Gira a DX (`-`), avanza 4 (`FFFF`).
        
    - _Chiude ramo, torna al tronco._
        
4. **`F`**: Il tronco principale cresce di 1 unità verso l'alto.
    
5. **`[+F[-FFFF]F]` (Ex Modulo A)**:
    
    - Ripete la struttura del punto 2 (ramo a sinistra con sotto-ramo).
        
6. **`F`**: Tronco cresce di 1 unità (Ex Modulo C).
    
7. **`FF`**: Tronco cresce di 2 unità finali.
    

**Risultato Visivo:**

Un fusto alto e dritto.

- Al nodo basso: escono un ramo complesso a sinistra e un ramo lungo e dritto a destra.
    
- Poi il fusto sale.
    
- Al nodo alto: esce solo un ramo complesso a sinistra.
    
- Il fusto termina con una punta dritta.
![[Pasted image 20260202204857.png]]