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
![[Screenshot 2026-02-01 alle 19.46.40.png]]
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
![[Screenshot 2026-02-01 alle 19.47.43.png]]
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
# Cinematica Diretta e Inversa: Soluzioni Analitiche

## A. Definizioni Fondamentali

### 1. Cinematica Diretta (Forward Kinematics - FK)
È il processo "naturale".
* **Input:** Gli angoli dei giunti ($\theta_1, \theta_2, \dots, \theta_n$).
* **Output:** La posizione e l'orientamento finale dell'end-effector (la "mano") nello spazio $(x, y, z)$.
* **Caratteristiche:**
    * Problema matematicamente semplice.
    * Soluzione **unica** (per un set di angoli, c'è una sola posizione finale).
    * Si risolve con moltiplicazioni di matrici: $M_{totale} = M_1(\theta_1) \cdot M_2(\theta_2) \dots$

### 2. Cinematica Inversa (Inverse Kinematics - IK)
È il processo inverso, quello che serve realmente all'animatore ("Voglio che la mano tocchi quel bicchiere").
* **Input:** La posizione desiderata dell'end-effector $(x, y, z)$.
* **Output:** Gli angoli dei giunti necessari per raggiungerla ($\theta_1, \theta_2, \dots$).
* **Caratteristiche:**
    * Problema molto complesso (non lineare).
    * **Mal condizionato:** Può avere *una* soluzione, *molteplici* soluzioni (ridondanza), o *nessuna* soluzione (target irraggiungibile).

---

## B. La Soluzione Analitica (Analytical Solver)

Esistono due approcci per risolvere l'IK: Numerico (iterativo) e Analitico (esatto).
La soluzione analitica usa formule geometriche chiuse (trigonometria) per trovare gli angoli direttamente.
* **Pro:** Velocissima e precisa.
* **Contro:** Applicabile solo a catene semplici (pochi gradi di libertà) e specifiche.
![[Screenshot 2026-02-01 alle 19.52.20.png]]
### Analisi Approfondita: Il Manipolatore Planare a 2 Link (Slide 36)
Immaginiamo un braccio 2D con due segmenti di lunghezza $L_1$ e $L_2$.
Vogliamo raggiungere il punto target $P(x, y)$. Dobbiamo trovare $\theta_1$ (spalla) e $\theta_2$ (gomito).

**Passaggio 1: Trovare l'angolo del gomito ($\theta_2$)**
Usiamo il **Teorema del Coseno (Carnot)** sul triangolo formato da: Origine, Gomito, Target.
Sia $r$ la distanza dall'origine al target: $r^2 = x^2 + y^2$.
Secondo il teorema:
$$r^2 = L_1^2 + L_2^2 - 2 L_1 L_2 \cos(\alpha)$$
Dove l'angolo interno $\alpha$ è legato a $\theta_2$ (spesso $\theta_2 = \pi - \alpha$ o relazione simile in base al sistema di riferimento).

Isolando il coseno:
$$\cos(\theta_2) = \frac{x^2 + y^2 - L_1^2 - L_2^2}{2 L_1 L_2}$$

Da qui ricaviamo $\theta_2$ usando l'arcocoseno ($\operatorname{acos}$).

**Passaggio 2: Trovare l'angolo della spalla ($\theta_1$)**
Una volta noto $\theta_2$, $\theta_1$ si calcola come differenza tra due angoli:
1.  L'angolo verso il target: $\operatorname{atan2}(y, x)$.
2.  L'angolo interno dovuto alla geometria del braccio (che dipende da $L_2$ e $\theta_2$).

Formula generale semplificata:
$$\theta_1 = \operatorname{atan2}(y, x) - \operatorname{atan2}(L_2 \sin \theta_2, L_1 + L_2 \cos \theta_2)$$
![[Screenshot 2026-02-01 alle 19.52.41.png]]
---

## C. Analisi degli Esempi (Slide 38 e 39)

Quando applichiamo le formule analitiche (in particolare l'arcocoseno), incontriamo i limiti fisici e matematici del sistema.

### Esempio 1: Soluzioni Multiple (Slide 38)![[Screenshot 2026-02-01 alle 19.52.59.png]]
Supponiamo che il target $(x,y)$ sia all'interno dell'area raggiungibile (Workspace).
L'operazione matematica $\operatorname{acos}(valore)$ restituisce due possibili valori angolari: uno positivo e uno negativo ($\pm \alpha$).

* **Significato Fisico:** Esistono due modi per toccare lo stesso punto.
    1.  **Gomito in alto (Elbow Up):** $\theta_2$ positivo.
    2.  **Gomito in basso (Elbow Down):** $\theta_2$ negativo.
* Il software deve *scegliere* quale soluzione usare (spesso quella più vicina alla configurazione attuale per minimizzare il movimento).

### Esempio 2: Nessuna Soluzione (Slide 39)
Cosa succede se il target è troppo lontano?
Matematicamente, se la distanza $r = \sqrt{x^2+y^2}$ è maggiore della somma delle braccia ($L_1 + L_2$), nell'equazione del coseno (vedi sopra) accade questo:

$$\frac{x^2 + y^2 - L_1^2 - L_2^2}{2 L_1 L_2} > 1$$

Poiché il dominio della funzione $\operatorname{acos}(v)$ è $[-1, 1]$, cercare di calcolare l'arcocoseno di un numero maggiore di 1 genera un errore matematico (o un numero complesso).
* **Significato Fisico:** Il target è fuori dalla portata (**Unreachable**).
* **Gestione:** Il sistema deve "clampare" il braccio alla massima estensione possibile nella direzione del target.
![[Screenshot 2026-02-01 alle 19.53.26.png]]
---
**Collegamenti:** [[Teorema del Coseno]], [[Spazio di Lavoro (Workspace)]], [[Jacobiana]]