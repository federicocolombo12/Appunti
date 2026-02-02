
In un ambiente virtuale popolato da oggetti in movimento, è inevitabile che le traiettorie si incrocino. Se non gestiamo questi eventi, otteniamo la **compenetrazione** degli oggetti, un artefatto visivo inaccettabile per una simulazione realistica.

La gestione delle collisioni si divide in due grandi sottoproblemi che devono essere affrontati in sequenza:

1.  **Collision Detection (Rilevamento):**
    * È un problema di **[[Cinematica]]**.
    * Coinvolge la geometria, la posizione e l'orientamento degli oggetti.
    * L'obiettivo è rispondere alla domanda: *"Gli oggetti si stanno toccando?"*
2.  **Collision Response (Risposta):**
    * È un problema di **[[Dinamica]]**.
    * Coinvolge il calcolo delle forze (es. impulso, attrito) che si generano a seguito dell'impatto.
    * L'obiettivo è determinare *"Come reagiscono gli oggetti all'impatto?"* (es. rimbalzo, deformazione).

---

## 1. Collision Detection: Intersezione Spazio-Temporale
Il metodo teoricamente più accurato per rilevare una collisione è considerare l'oggetto non solo nello spazio, ma nel tempo (4D). Questo approccio si basa sull'operazione di **estrusione**.

### L'Operatore di Estrusione ($Ex$)
Dato un oggetto $O$ e una funzione di trasformazione $\Lambda(t)$ che definisce il movimento dell'oggetto al tempo $t$, l'insieme dei punti occupati dall'oggetto nello spazio-tempo è definito come:

$$Ex(\Lambda, O) = \{(x,t) | x \in \Lambda(t)(O)\}$$

Dove:
* $x$ rappresenta le coordinate spaziali.
* $t$ rappresenta l'istante temporale.
* La coppia $(x,t)$ indica che l'oggetto occupa il punto $x$ all'istante $t$.

**Condizione di Collisione:**
Due oggetti $A$ e $B$ collidono se e solo se le loro estrusioni spazio-temporali si intersecano:
$$Ex(\Lambda_A, A) \cap Ex(\Lambda_B, B) \neq \phi$$

> **Nota Critica:** Sebbene matematicamente perfetto, il calcolo esatto delle estrusioni è un'operazione computazionalmente estremamente *costosa*. Spesso richiede di risolvere equazioni polinomiali di grado elevato.

---

## 2. Swept Volumes vs. Estrusione
Per semplificare il calcolo, si ricorre spesso al concetto di **Swept Volume** (Volume di Scansione).

### L'Operatore Swept Volume ($Sw$)
Lo Swept Volume calcola il volume totale occupato dai punti dell'oggetto durante l'intero intervallo di simulazione (time span), ma "appiattendo" la dimensione temporale:

$$Sw(\Lambda, O) = \{x | (\exists y,t) \text{ tale che } x = \Lambda(t)(y)\}$$

### Differenza Fondamentale: La perdita dell'informazione temporale
La differenza tra l'operatore di estrusione ($Ex$) e lo Swept Volume ($Sw$) è cruciale per la logica di rilevamento:
* **$Ex$ (Estrusione):** Mantiene la coppia $(x,t)$. Sa *dove* e *quando* un oggetto si trova in un punto.
* **$Sw$ (Swept Volume):** Mantiene solo $x$. Perde l'informazione temporale ($t$).

**Conseguenze per il Rilevamento:**
L'intersezione degli Swept Volumes **non è sufficiente** a garantire che due oggetti collidano
# 3. Gestione del Tempo e Ottimizzazione

## A. Rilevamento di Interferenze Multiple (Campionamento)
Come abbiamo visto, l'uso degli *Swept Volumes* perde l'informazione temporale. Per risolvere questo problema, le tecniche di rilevamento di interferenze multiple selezionano un **insieme discreto di istanti di tempo** in cui eseguire il test di collisione.

### Il Fattore Critico: La Frequenza di Campionamento
La scelta di "ogni quanto" controllare (frequenza) è un parametro cruciale che bilancia accuratezza e prestazioni:
* **Campionamento troppo grossolano:** C'è il rischio di "mancare" una collisione se l'interazione avviene interamente tra due istanti campionati (l'oggetto attraversa l'ostacolo senza venire rilevato).
* **Campionamento troppo fine:** Il costo computazionale diventa eccessivo, rallentando la simulazione.

### La Progressione Aritmetica
Il metodo più semplice per definire gli istanti di controllo è utilizzare una progressione aritmetica uniforme.
Dati un istante iniziale $t_s$ e un istante finale $t_f$, e deciso un numero $n$ di test da eseguire, l'istante $i$-esimo è dato da:

$$t_i = t_s + \frac{i(t_f - t_s)}{n}$$

Questa formula divide l'intervallo di tempo in $n$ segmenti uguali.

---

## B. Parametrizzazione delle Traiettorie
A differenza del campionamento (che è approssimato), le tecniche di parametrizzazione cercano di determinare **esattamente** l'istante della collisione.

### Il Metodo Analitico
Si esprimono le traiettorie degli oggetti come **funzioni del parametro tempo**. Matematicamente, si cerca di risolvere un sistema di equazioni per trovare il valore $t$ in cui i volumi si intersecano.

### Il Limite della Complessità
La complessità di questo calcolo dipende direttamente dalla complessità del moto:
* Per moti lineari semplici, è risolvibile.
* Per traiettorie arbitrarie (es. curve complesse o rotazioni), l'equazione risultante può facilmente diventare un polinomio di **ordine 5 o superiore**.
* Poiché non esistono soluzioni analitiche generali per polinomi di grado $\ge 5$, spesso anche questo metodo richiede soluzioni numeriche approssimate.

---

## C. Ottimizzazione: Gerarchie Limitanti (Bounding Volume Hierarchies)
L'efficienza totale di un algoritmo di collision detection dipende da due fattori:
1.  La velocità del singolo test di intersezione.
2.  Il numero totale di volte che il test viene eseguito.

Poiché testare la collisione tra mesh poligonali complesse (magari composte da migliaia di triangoli) è lentissimo, si utilizzano le **Gerarchie di Volumi Limitanti**.

### L'Approccio Gerarchico
L'idea è incapsulare l'oggetto complesso all'interno di volumi geometrici semplici (sfere, cubi) facili da testare.
Questo approccio offre due vantaggi fondamentali:

1.  **Rifiuto Rapido (Early Rejection):** È possibile rilevare una *non-intersezione* già al primo livello della gerarchia. Se i volumi contenitori (es. le sfere che avvolgono gli interi oggetti) non si toccano, non serve controllare i poligoni interni.
2.  **Riduzione dello Spazio di Ricerca:** Se i volumi contenitori collidono, si scende di un livello nella gerarchia, riducendo la porzione di spazio e di geometria da considerare per il test successivo.

**Algoritmo Logico:**
1.  Testa i volumi limitanti "padre".
2.  SE collidono $\rightarrow$ Testa i volumi limitanti "figli".
3.  SE i figli collidono $\rightarrow$ Procedi fino alle foglie (gli oggetti veri e propri o parti di essi).
# 6. Ottimizzazione Spaziale e Bounding Volumes

Per evitare di testare ogni oggetto contro ogni altro oggetto ($O(n^2)$), dobbiamo organizzare lo spazio o semplificare la geometria degli oggetti.

## A. Suddivisione dello Spazio (Space Partitioning)
Queste tecniche dividono l'intero ambiente virtuale in regioni per determinare rapidamente quali oggetti sono vicini tra loro.

1.  **Octree:**
2. ![[Pasted image 20260201131150.png]]
    * È una struttura gerarchica ad albero.
    * Lo spazio cubico iniziale viene diviso in 8 ottanti (cubi più piccoli).
    * Se un ottante contiene troppi oggetti, viene suddiviso ricorsivamente in altri 8 sotto-ottanti.
    * *Vantaggio:* Ottimo per scene con densità variabile di oggetti.
3.  **Griglie Uniformi (Uniform Grids):**
    * Lo spazio viene diviso in una griglia di celle di dimensioni fisse.
    * Ogni oggetto viene assegnato alle celle che occupa.
    * *Vantaggio:* Accesso rapidissimo ($O(1)$) per trovare i vicini.
    * *Svantaggio:* Spreco di memoria se la scena è vuota; inefficiente se gli oggetti hanno dimensioni molto diverse.
    * ![[Pasted image 20260201131205.png]]
4.  **BSP (Binary Space Partitioning):**
    * Divide lo spazio ricorsivamente usando piani di taglio (spesso coincidenti con i poligoni della scena).
    * Crea un albero binario: "davanti al piano" vs "dietro al piano".
    * *Uso:* Storicamente fondamentale per i motori di gioco indoor (es. Doom/Quake) per gestire visibilità e collisioni.

## B. Bounding Volumes (Volumi Limitanti)
Invece di dividere lo spazio, avvolgiamo gli oggetti complessi in forme geometriche semplici.

1.  **AABB (Axis-Aligned Bounding Box):**
2. ![[Pasted image 20260201131233.png]]
    * Parallelepipedo con facce allineate agli assi $X, Y, Z$.
    * *Pro:* Test di intersezione velocissimo (basta confrontare le coordinate min/max).
    * *Contro:* Non ruota con l'oggetto. Se l'oggetto ruota, l'AABB deve essere ricalcolato e può diventare molto più grande dell'oggetto (molto spazio vuoto).
3.  **OBB (Oriented Bounding Box):**
    * Parallelepipedo che ruota solidale con l'oggetto.
    * *Pro:* "Fitta" (aderisce) molto meglio all'oggetto rispetto all'AABB (meno falsi positivi).
    * *Contro:* Test di intersezione matematicamente più oneroso.
4.  **Sphere Tree (Gerarchia di Sfere):**
    * L'oggetto è approssimato da un insieme di sfere gerarchiche.
    * *Pro:* Invariante alla rotazione (una sfera ruotata è sempre uguale). Calcolo della distanza banalissimo (distanza tra centri < somma raggi).
    * ![[Pasted image 20260201131247.png]]

---

