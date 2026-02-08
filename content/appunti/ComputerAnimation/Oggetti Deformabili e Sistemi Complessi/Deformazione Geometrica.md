La deformazione non è solo un effetto visivo, ma uno strumento narrativo fondamentale nella [[Index]].
* **Obiettivo:** Aggiungere le nozioni di *malleabilità* e *densità* agli oggetti.
* **Espressività:** Un corpo flessibile comunica meglio l'azione e la reazione (pensate a un personaggio che salta o impatta al suolo).
* **Tecniche:**
    * **Scalamento non uniforme:** Usato per semplici operazioni di [[Squash & Stretch]] (schiacciamento e allungamento).
    * **Trasformazioni non affini:** Per distorsioni più complesse che alterano la geometria interna.

---

## 1. Deformazione tramite Spostamento dei Vertici
Un metodo semplice ("brute force") per deformare una mesh è spostare i suoi vertici propagando il movimento.

### Il concetto di Seed Vertex
1.  Si seleziona un **Seed Vertex** (vertice seme) o un gruppo di vertici.
2.  Si sposta questo vertice.
3.  Lo spostamento si **propaga** ai vertici adiacenti lungo la superficie.
4.  L'effetto viene **attenuato** in base alla distanza dal seed vertex.
![[Pasted image 20260202094532.png]]
### Funzione di Attenuazione $S(i)$
Per evitare che la mesh si "rompa" o appaia innaturale, lo spostamento deve decrescere gradualmente.
* **$i$**: La distanza topologica (numero di lati/edges) dal seed vertex.
* **$n$**: Il raggio d'azione (range) definito dall'utente (oltre $n$ lati, nessun vertice si muove).
* **$k$**: Fattore di controllo della curva di attenuazione ($k \ge 0$).

La formula matematica per il fattore di scala dello spostamento è:

$$S(i) = 1.0 - \left( \frac{i}{n+1} \right)^{k+1}$$

* Se $i = 0$ (seed vertex), $S(0) = 1.0$ (spostamento massimo).
* Se $i > n$, $S(i)$ viene considerato $0$.

---

## 2. Free-Form Deformation (FFD)
La FFD è una tecnica più avanzata e potente perché **deforma lo spazio** in cui l'oggetto è immerso, non l'oggetto direttamente.
* **Analogia:** Immaginate di immergere l'oggetto in un blocco di gelatina trasparente e poi deformare la gelatina. L'oggetto all'interno seguirà la deformazione.
* **Vantaggio:** Indipendente dalla complessità della mesh (funziona su pochi poligoni o milioni di poligoni).

### Deformazione di Griglie 2D
Partiamo dal caso semplificato: una griglia planare.
![[Pasted image 20260202094608.png]]
#### Sistema Locale vs Sistema Globale
Per applicare la FFD, dobbiamo mappare le coordinate del "Mondo" (Globali) in coordinate della "Griglia" (Locali).

1.  **Coordinate Globali ($X, Y$):** La posizione assoluta del vertice nello spazio.
2.  **Coordinate Locali ($S, T$):** La posizione normalizzata all'interno della griglia indeformata.
    * $S$ e $T$ variano solitamente tra $0$ e $1$.

#### Allineamento e Mappatura
Supponiamo di avere un oggetto (es. un triangolo) inscritto in una griglia quadrata definita da:
* $X_{min}, X_{max}$
* $Y_{min}, Y_{max}$

Per ogni vertice $P$ dell'oggetto con coordinate globali $(P_x, P_y)$, calcoliamo le coordinate locali $(s, t)$:

$$s = \frac{P_x - X_{min}}{X_{max} - X_{min}}$$
$$t = \frac{P_y - Y_{min}}{Y_{max} - Y_{min}}$$

> **Nota:** Questi valori $(s, t)$ sono "congelati". Rappresentano dove si trova il vertice *rispetto* alla griglia. Non cambieranno mai, anche se deformiamo la griglia.

---

### Calcolo delle Nuove Posizioni (Deformazione)
Quando deformiamo la griglia, spostiamo i suoi **Punti di Controllo** ($P_{ij}$).
La nuova posizione $P'$ di un vertice dell'oggetto si ottiene interpolando i nuovi punti di controllo usando i valori $(s, t)$ originali.

Per una griglia semplice (interpolazione bilineare), la formula è:

$$P'(s,t) = P_{00}(1-s)(1-t) + P_{10} \cdot s \cdot (1-t) + P_{01} \cdot (1-s) \cdot t + P_{11} \cdot s \cdot t$$

Dove:
* $P_{00}, P_{10}, P_{01}, P_{11}$ sono le nuove posizioni dei 4 angoli della cella della griglia che contiene il punto.

---

## Esercizio Pratico (Slide 11)
**Problema:** Determinare le nuove coordinate del vertice $A$ nel caso la griglia venga deformata.
![[Pasted image 20260202094858.png]]
# Esercizio Pratico: Deformazione FFD 2D (Caso Personalizzato)

## 1. Dati del Problema
Analizziamo la configurazione iniziale fornita.

### Geometria Iniziale (Bind Pose)
* **Griglia:** Rettangolo definito da $X \in [20, 28]$ e $Y \in [12, 16]$.
    * Larghezza ($W$) = $28 - 20 = 8$
    * Altezza ($H$) = $16 - 12 = 4$
* **Punto $A$ (Originale):** $(24.5, 15.3)$

### Punti di Controllo Iniziali ($P_{ij}$)
La griglia è definita dai 4 vertici:
* $P_{00}$ (Basso-Sx): $(20, 12)$
* $P_{10}$ (Basso-Dx): $(28, 12)$
* $P_{01}$ (Alto-Sx): $(20, 16)$
* $P_{11}$ (Alto-Dx): $(28, 16)$

---

## 2. Passo 1: Mappatura (Coordinate Locali)
Calcoliamo la posizione parametrica di $A$ rispetto alla griglia. Queste coordinate $(s,t)$ sono "congelate" e non cambieranno durante la deformazione.

$$s = \frac{A_x - X_{min}}{X_{max} - X_{min}} = \frac{24.5 - 20}{8} = \frac{4.5}{8} = \mathbf{0.5625}$$

$$t = \frac{A_y - Y_{min}}{Y_{max} - Y_{min}} = \frac{15.3 - 12}{4} = \frac{3.3}{4} = \mathbf{0.825}$$

*Verifica:* Il punto si trova nella metà destra ($s > 0.5$) e nella parte alta ($t > 0.8$) della griglia.

---

## 3. Passo 2: Definizione della Deformazione
Applicando la deformazione visibile in figura (effetto trapezio), modifichiamo le posizioni dei Punti di Controllo ($P'_{ij}$).
*Ipotesi:* La base rimane ferma, la cima si allarga di 2 unità per lato.

* **$P'_{00}$**: $(20, 12)$ *(Invariato)*
* **$P'_{10}$**: $(28, 12)$ *(Invariato)*
* **$P'_{01}$**: $(18, 16)$ *(Spostato a Sinistra di 2: $20 - 2$)*
* **$P'_{11}$**: $(30, 16)$ *(Spostato a Destra di 2: $28 + 2$)*

---

## 4. Passo 3: Calcolo Nuova Posizione $A'$
Utilizziamo l'**Interpolazione Bilineare** per trovare le nuove coordinate globali.

**Formula:**
$$A'(s,t) = P'_{00}(1-s)(1-t) + P'_{10} \cdot s \cdot (1-t) + P'_{01} \cdot (1-s) \cdot t + P'_{11} \cdot s \cdot t$$

### Calcolo dei Pesi
Calcoliamo quanto ogni vertice della griglia influenza il punto $A$:

1.  **Peso Basso-Sx** $(1-s)(1-t) = (0.4375)(0.175) \approx \mathbf{0.07656}$
2.  **Peso Basso-Dx** $s(1-t) = (0.5625)(0.175) \approx \mathbf{0.09844}$
3.  **Peso Alto-Sx** $(1-s)t = (0.4375)(0.825) \approx \mathbf{0.36094}$
4.  **Peso Alto-Dx** $st = (0.5625)(0.825) \approx \mathbf{0.46406}$

> **Nota:** La somma dei pesi deve fare sempre 1. ($0.076 + 0.098 + 0.361 + 0.464 \approx 1.0$).

### Calcolo Coordinata X'
Moltiplichiamo le X dei nuovi punti di controllo per i rispettivi pesi:
$$X' = 20(0.07656) + 28(0.09844) + 18(0.36094) + 30(0.46406)$$
$$X' = 1.5312 + 2.7563 + 6.4969 + 13.9218$$
$$X' = \mathbf{24.70625}$$

### Calcolo Coordinata Y'
Moltiplichiamo le Y dei nuovi punti di controllo per i rispettivi pesi:
$$Y' = 12(0.07656) + 12(0.09844) + 16(0.36094) + 16(0.46406)$$
$$Y' = 12(0.175) + 16(0.825)$$ *(Raggruppando per righe, dato che le Y sono uguali a due a due)*
$$Y' = 2.1 + 13.2$$
$$Y' = \mathbf{15.3}$$

---

## 5. Risultato Finale
Il punto deformato $A'$ si trova alle coordinate:

# $$A' \approx (24.71, 15.3)$$

### Interpretazione
* **Coordinata X (24.5 -> 24.71):** Il punto si è spostato verso destra. Questo è corretto perché si trovava nella parte destra della griglia ($s > 0.5$) che è stata "stirata" verso l'esterno. Essendo però molto in alto ($t=0.825$), risente fortemente dell'allargamento della cima.
* **Coordinata Y (15.3 -> 15.3):** La coordinata Y non è cambiata. Questo accade perché i punti di controllo non si sono spostati verticalmente (la base è rimasta a Y=12 e la cima a Y=16).
# Lezione 4: Deformazione Polyline (Metodo Scalare Semplificato)
![[Pasted image 20260202162942.png]]
## 1. Il Concetto: Proiezione e Distanza
Per mappare un punto $P$ su un segmento (linea o "osso") che va da $Q_1$ a $Q_2$, non servono vettori complessi. Ci servono solo due numeri (scalari) che rispondono a due domande semplici:

1.  **$u$ (Il Rapporto Lungo la Linea):** Se proietto il punto sulla linea, a che percentuale del percorso tra l'inizio e la fine mi trovo?
2.  **$v$ (La Distanza dalla Linea):** Quanto è distante il punto dalla linea?

### L'Algoritmo Geometrico (Slide 14)
Immagina di tracciare una linea perpendicolare dal punto $P$ fino a toccare il segmento. Chiamiamo quel punto di contatto $K$ (la proiezione).

1.  **Calcolo di $u$ (La posizione relativa):**
    Misuriamo la distanza tra l'inizio ($Q_1$) e la proiezione ($K$). Dividiamo questa distanza per la lunghezza totale del segmento ($L$).
    $$u = \frac{\text{Distanza}(Q_1, K)}{\text{Lunghezza Totale}(Q_1, Q_2)}$$
    ![[Pasted image 20260202163002.png]]

2.  **Calcolo di $v$ (Lo scostamento):**
    È semplicemente la distanza fisica tra il punto $P$ e la sua proiezione $K$.
    $$v = \text{Distanza}(P, K)$$
![[Pasted image 20260202163040.png]]
---
### Esercizio Pratico: Calcolo di $u$ e $v$
**Dati:**
*   **Osso (Segmento):** Inizia in $Q_1(2, 3)$ e finisce in $Q_2(10, 3)$.
*   **Punto da deformare:** $P(6, 7)$.

**Svolgimento:**

1.  **Analisi del Segmento:** Il segmento è orizzontale (Y costante = 3).
    *   Lunghezza totale $L = 10 - 2 = 8$.
2.  **Trovare la Proiezione ($K$):**
    Poiché il segmento è orizzontale, la proiezione ortogonale di $P(6, 7)$ mantiene la stessa X e prende la Y del segmento.
    *   $K = (6, 3)$.
3.  **Calcolo di $u$ (Posizione relativa):**
    Distanza tra l'inizio $Q_1$ e la proiezione $K$: $6 - 2 = 4$.
    $$u = \frac{4}{8} = \mathbf{0.5}$$
    *(Il punto si trova esattamente a metà lunghezza del segmento).*
4.  **Calcolo di $v$ (Distanza):**
    Distanza tra il punto $P$ e la proiezione $K$: $7 - 3 = 4$.
    $$v = \mathbf{4}$$
    *(Il punto dista 4 unità dall'asse dell'osso).*

---


# Deformazioni Globali e FFD 3D

## 1. Deformazioni Globali (Metodo Alan Barr)
A differenza delle trasformazioni affini standard (dove la matrice è costante per tutti i punti), nelle deformazioni globali la trasformazione dipende dalla posizione del punto stesso nello spazio.

### Il Concetto Chiave
$$P' = M(P) \cdot P$$
* **$P$**: Il punto originale.
* **$M(P)$**: Una matrice di trasformazione che **varia** in funzione delle coordinate di $P$.

Vediamo due operatori classici introdotti da Alan Barr, applicati lungo l'asse $Z$.

### A. Tapering (Rastremazione)
Rimpicciolisce o ingrandisce l'oggetto man mano che ci si sposta lungo l'asse $Z$.
* L'idea è applicare uno **scalamento** su $X$ e $Y$ che dipende dal valore di $z$.
* Funzione di rastremazione $r = f(z)$.

La matrice di trasformazione diventa:
$$
M(z) = \begin{bmatrix} 
r(z) & 0 & 0 & 0 \\
0 & r(z) & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1 
\end{bmatrix}
$$

### B. Twist (Torsione)
Ruota le "fette" dell'oggetto attorno all'asse $Z$, con un angolo che aumenta progressivamente.
* L'angolo di rotazione $\theta$ è funzione di $z$: $\theta = f(z)$.
* Esempio classico: $\theta = k \cdot z$ (torsione lineare).

La matrice (una rotazione Z modificata) è:
$$
M(z) = \begin{bmatrix} 
\cos(\theta) & -\sin(\theta) & 0 & 0 \\
\sin(\theta) & \cos(\theta) & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1 
\end{bmatrix}
$$
*Dove $\theta$ varia per ogni vertice in base alla sua altezza $z$.*

---

## 2. Free-Form Deformation (FFD) 3D
Estendiamo il concetto di griglia 2D a un volume tridimensionale (Lattice).

### Setup del Lattice
1.  Si sovrappone all'oggetto un reticolo tridimensionale (parallelepipedo).
2.  Definiamo un sistema di coordinate locale $(s, t, u)$ tramite un'origine $P_0$ e tre vettori asse $S, T, U$.

### Mapping: Come trovare (s, t, u)?
![[Pasted image 20260202163837.png]]
Dato un punto $P$ nello spazio globale, dobbiamo trovare le sue coordinate locali $(s, t, u)$ tali che:
$$P = P_0 + s \cdot S + t \cdot T + u \cdot U$$
*(Dove $0 \le s,t,u \le 1$ se il punto è dentro il lattice)*.

Per isolare le singole coordinate scalari ($s, t, u$) usiamo il **Prodotto Vettoriale** (Cross Product).
L'intuizione è: per trovare la coordinata $s$ (lungo l'asse $S$), dobbiamo proiettare il punto su una direzione che sia perpendicolare agli altri due assi ($T$ e $U$).

I vettori "normali" alle facce del lattice sono:
* $$N_S = T \times U$$ (Perpendicolare al piano $TU$, serve a trovare $s$)
* $$N_T = S \times U$$ (Perpendicolare al piano $SU$, serve a trovare $t$)
* $$N_U = S \times T$$ (Perpendicolare al piano $ST$, serve a trovare $u$)

La formula per trovare la coordinata locale $s$ è:
$$s = \frac{(P - P_0) \cdot (T \times U)}{S \cdot (T \times U)}$$
*(Similmente per $t$ e $u$ permutando i vettori).
![[Pasted image 20260202163914.png]]*

> **Nota per l'Esame:** Questo passaggio serve **solo** all'inizio (Bind Pose) per "registrare" il vertice nel sistema lattice. Una volta trovati $s, t, u$, questi valori restano fissi per quel vertice.

---

## 3. Deformazione tramite Interpolazione
Una volta che abbiamo $(s, t, u)$, l'utente sposta i punti di controllo del lattice ($P_{ijk}$). Come calcoliamo la nuova posizione $P'$?

### Interpolazione Tricubica (Bernstein)
Se la griglia ha dimensioni $L \times M \times N$ (es. $3 \times 3 \times 3$), usiamo una sommatoria tripla con i polinomi di Bernstein ($B$) per garantire continuità e curve morbide.

$$P'(s,t,u) = \sum_{i=0}^{L} \sum_{j=0}^{M} \sum_{k=0}^{N} P_{ijk} \cdot B_i(s) \cdot B_j(t) \cdot B_k(u)$$

* **$P_{ijk}$**: Le nuove posizioni dei punti di controllo.
* **$B_i(s)$**: Quanto pesa l'i-esimo piano lungo l'asse S.
* **$B_j(t)$**: Quanto pesa il j-esimo piano lungo l'asse T.
* **$B_k(u)$**: Quanto pesa il k-esimo piano lungo l'asse U.

### Griglie Non Uniformi
La griglia non deve essere necessariamente composta da cubi perfetti. I punti di controllo possono essere distribuiti in modo non uniforme (es. più densi dove serve più dettaglio nella deformazione).
La formula rimane valida perché lavoriamo nello spazio parametrico normalizzato $[0,1]$.
# Composizione e Animazione con FFD

## 1. Composizione di FFD
Raramente una deformazione complessa si ottiene con un solo passaggio. Spesso è necessario combinare più griglie FFD. Esistono due approcci principali.

### A. Composizione Sequenziale (In Serie)
Applicazione di deformazioni una dopo l'altra.
* **Processo:** L'oggetto $O$ viene deformato da una griglia $FFD_1$ ottenendo $O'$. Successivamente, $O'$ viene deformato da $FFD_2$ ottenendo $O''$.
* **Formula:** $P_{finale} = FFD_2(FFD_1(P_{iniziale}))$
* **Limitazione:** Se le deformazioni sono molte, il calcolo diventa pesante e gestire l'interazione tra deformazioni sovrapposte può essere controintuitivo (es. torcere un oggetto già piegato).

### B. Composizione Gerarchica (Hierarchical FFD)
Questo è l'approccio professionale per gestire i **Livelli di Dettaglio** (LOD - Level of Detail).
* **Concetto:** Si usano griglie annidate o collegate in gerarchia padre-figlio.
* **Funzionamento:**
    1.  **Livello Base (Coarse):** Una griglia con *pochi* punti di controllo (es. $2 \times 2 \times 2$) definisce la forma generale.
    2.  **Livelli Successivi (Fine):** Griglie più dense (es. $4 \times 4 \times 4$) sono collegate alla griglia base.
* **Vantaggio Operativo:**
    * Quando muovo i punti della griglia "Padre", **tutte** le griglie "Figlio" al suo interno si spostano di conseguenza.
    * Posso poi scendere nel dettaglio e muovere i punti della griglia "Figlio" per rifiniture locali senza perdere la forma generale data dal padre.
* **Utilizzo:** Ideale per modellazione complessa (es. definire la posa di un braccio e poi aggiustare la forma del bicipite).

---

## 2. Animazione tramite FFD
La FFD non serve solo a cambiare la forma statica di un modello (Modeling), ma è un potente strumento di [[Animation]]. Esistono due modi opposti per animare con FFD.

### Metodo A: Animazione dei Punti di Controllo
È il metodo più intuitivo. L'oggetto "vive" dentro la griglia.
1.  **Setup:** L'oggetto è mappato staticamente nel lattice ($s,t,u$ costanti).
2.  **Azione:** L'animatore sposta i **Punti di Controllo** ($P_{ijk}$) nel tempo (Keyframing).
3.  **Risultato:** L'oggetto si deforma seguendo il movimento della griglia.
* *Esempi:* Un cuore che batte, un volto che parla, una palla che rimbalza (Squash & Stretch).

### Metodo B: Animazione dell'Oggetto attraverso la Griglia
Qui la griglia è statica (o ha una forma fissa deformata) e l'oggetto la attraversa.
1.  **Setup:** La griglia ha una forma deformata (es. è piegata a 90° o ristretta al centro).
2.  **Azione:** L'oggetto possiede una **Legge di Moto** (traiettoria) che lo fa muovere attraverso lo spazio occupato dalla griglia.
3.  **Processo Matematico:**
    * Ad ogni frame, il vertice dell'oggetto entra in una nuova posizione dello spazio.
    * Vengono ricalcolate le coordinate locali $(s,t,u)$ rispetto alla griglia.
    * Viene applicata la deformazione in quel punto specifico.
* **Risultato:** L'oggetto subisce la deformazione solo mentre passa nella zona influenzata.
* *Esempi:*
    * Un serpente che ha ingoiato una preda (la pancia si gonfia solo dove passa la preda).
    * Una moneta che entra in una fessura storta.
    * Un'auto che diventa "gommosa" mentre passa in un tunnel magico.

---

## Sintesi per l'Esame: Differenze Chiave
| Caratteristica | Metodo A (Anim. Punti) | Metodo B (Anim. Oggetto) |
| :--- | :--- | :--- |
| **Cosa si muove** | I punti della griglia ($P_{ijk}$) | L'oggetto ($P$) |
| **Coordinate $(s,t,u)$** | **Costanti** (calcolate una volta) | **Variabili** (ricalcolate ogni frame) |
| **Costo Computazionale** | Basso (solo interpolazione) | Alto (mapping + interpolazione) |
| **Effetto Visivo** | L'oggetto cambia forma sul posto | L'oggetto "fluisce" nella forma |