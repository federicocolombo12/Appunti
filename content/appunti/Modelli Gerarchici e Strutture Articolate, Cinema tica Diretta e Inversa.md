Per animare oggetti complessi (come un essere umano o un robot industriale), non possiamo gestire ogni parte come un oggetto isolato. Dobbiamo definire delle relazioni di parentela (Parent-Child).
Questa strutturazione è la base necessaria per poter applicare successivamente la **[[Cinematica Diretta]] (Forward Kinematics)** e la **[[Cinematica Inversa]] (Inverse Kinematics)**.
![[Screenshot 2026-02-01 alle 19.46.24.png]]
## A. Cosa sono e a cosa servono
Un modello gerarchico è un sistema composto da corpi rigidi (detti **Links**) connessi tra loro da snodi (detti **Giunti** o Joints).

* **Scopo:** Permettere la propagazione del movimento. Se muovo la "spalla" (padre), tutto il "braccio" (figlio) deve muoversi di conseguenza, mantenendo però la possibilità di ruotare indipendentemente attorno al gomito.
* **Struttura Logica:** Si utilizza un **Albero (Tree)** o un Grafo Aclicico Diretto (DAG).

## B. I Componenti: Links e Giunti

### 1. I Links (Nodi)
Sono le parti solide e rigide (es. l'osso dell'omero, il telaio di un'auto).
In termini di teoria dei grafi, questi rappresentano i **Nodi**.
* Contengono la geometria (la mesh da renderizzare).
* Hanno una massa e un tensore d'inerzia (per la fisica).

### 2. I Giunti (Joints/Archi)
Sono i meccanismi che collegano due link e permettono il movimento relativo.
In termini di teoria dei grafi, rappresentano gli **Archi** che definiscono la relazione padre-figlio.
Esistono vari tipi di giunti, classificati per Gradi di Libertà (DOF):
* **Giunto Prismatico (Sliding):** Permette solo la traslazione lungo un asse (es. pistone idraulico, l'antenna telescopica).
* **Giunto Rotoidale (Revolute):** Permette la rotazione su un asse (es. cerniera, gomito).
* **Giunto Sferico (Ball-and-Socket):** Permette la rotazione su 3 assi (es. la spalla).

## C. Rappresentazione a Grafo (Scene Graph)

Il sistema viene modellato come un albero gerarchico. 
* **Root (Radice):** È l'oggetto base che ancora tutto il sistema al mondo (es. il bacino di un personaggio o la base fissa di un robot).
* **Leaf (Foglia):** Sono le estremità finali (es. le punte delle dita o l'end-effector di un robot).

### Ereditarietà delle Trasformazioni
La regola d'oro dei modelli gerarchici è: **"Il figlio eredita le trasformazioni del padre"**.

Se definiamo $M_{locale}$ come la matrice che posiziona un oggetto rispetto al suo genitore:
* Posizione Spalla (rispetto al mondo) = $M_{spalla}$
* Posizione Gomito (rispetto alla spalla) = $M_{gomito}$
* Posizione Mano (rispetto al gomito) = $M_{mano}$

La posizione globale (World Space) della mano non è solo $M_{mano}$, ma il prodotto di tutte le matrici fino alla radice:
$$M_{ManoGlobale} = M_{spalla} \cdot M_{gomito} \cdot M_{mano}$$

## D. Analisi degli Esempi (Riferimento Slide 29-30)

### 1. La Catena Cinematica Semplice (Robot Arm)
Immagina un braccio robotico composto da Base $\to$ Braccio $\to$ Avambraccio $\to$ Pinza.
* **Concetto:** Questa è una serie lineare di trasformazioni nidificate.
* Ogni nodo ha un solo figlio.
* Se ruoto la Base di 90°, l'intero braccio ruota solidalmente, ma le coordinate locali della Pinza rispetto all'Avambraccio restano identiche $(0,0,0)$.
* Matematicamente: $P_{world} = T_{base} \cdot R_{base} \cdot T_{braccio} \cdot R_{braccio} \dots$

### 2. La Ramificazione (Humanoid/Tree)
Immagina un busto che si collega a tre elementi: Testa, Braccio Destro, Braccio Sinistro.
* **Concetto:** Qui la gerarchia si divide.
* Il "Torso" è padre di tre nodi distinti.
* Se muovo il Torso, *tutti* e tre i figli si muovono.
* Se muovo il Braccio Destro, la Testa e il Braccio Sinistro *non* subiscono effetti (sono "fratelli", non discendenti).

## E. Visita dell'Albero e Struttura a Stack

Per disegnare (renderizzare) questa struttura sullo schermo, il computer deve calcolare la posizione globale di ogni pezzo. Come fa? Esegue una **visita in profondità (Depth-First Search)** dell'albero.

### Perché lo Stack (Pila)?
Lo Stack è la struttura dati perfetta per gestire la gerarchia perché funziona secondo il principio LIFO (Last In, First Out), permettendo di "ricordare" la posizione del padre mentre si visitano i figli.

**Algoritmo di Visita (Pseudocodice Grafico):**

1.  Parto dalla Radice (es. Torso).
2.  Applico la trasformazione del Torso.
3.  Disegno il Torso.
4.  Devo andare al Braccio? **Push dello Stack** (Salvo lo stato attuale della matrice del Torso).
5.  Applico trasformazione Braccio (moltiplico la matrice corrente per quella del braccio).
6.  Disegno Braccio.
7.  Ho finito col Braccio e devo disegnare la Testa? **Pop dello Stack**.
    * *Risultato:* Facendo Pop, elimino la matrice del Braccio e mi ritrovo magicamente con la matrice del Torso attiva.
8.  Applico trasformazione Testa e disegno.

Senza lo Stack, per disegnare la Testa dopo il Braccio, dovrei ricalcolare tutte le matrici da zero partendo dalla radice. Con lo Stack, posso "salvare e ricaricare" i checkpoint lungo l'albero.

---
**Collegamenti:** [[Matrici di Trasformazione]], [[Scena Graph]], [[OpenGL Matrix Stack]]