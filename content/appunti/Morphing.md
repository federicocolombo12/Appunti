
Il Morphing è la metamorfosi fluida da un'immagine sorgente ($I_S$) a un'immagine destinazione ($I_D$).
* **Obiettivo:** Generare una sequenza di fotogrammi intermedi che trasformino visivamente il soggetto A nel soggetto B.
* **Componenti:**
    1.  **Warping (Deformazione Geometrica):** Distorcere la forma di A affinché coincida con la forma di B.
    2.  **Cross-Dissolve (Dissolvenza):** Miscelare i colori da A a B man mano che la forma cambia.
![[Pasted image 20260202170153.png]]
### Il Compito dell'Utente
Il computer non sa che il naso di A corrisponde al naso di B.
* **Ruolo Cruciale:** L'utente deve specificare le **corrispondenze** (features) tra le due immagini.
* **Tecniche di Specifica:**
    * **Griglie di Coordinate (Mesh Warping):** Si disegnano griglie deformabili.
    * **Linee Caratteristiche (Field Morphing):** Si tracciano linee su contorni chiave (occhi, labbra).

---

## 2. Metodo delle Griglie di Coordinate (Mesh Warping)
Questa tecnica, resa celebre da *Wolberg*, si basa sulla deformazione guidata da spline.

### Setup Iniziale
1.  L'utente definisce due griglie curvilinee (mesh):
    * Una sovrapposta all'immagine di **Partenza** ($I_S$).
    * Una sovrapposta all'immagine di **Arrivo** ($I_D$).
2.  **Vincoli:**
    * Stesso numero di punti di intersezione (nodi) su entrambe.
    * Le griglie devono coprire l'intera immagine.
    * I nodi chiave (es. angolo dell'occhio) devono corrispondere (nodo $k$ su $I_S$ = nodo $k$ su $I_D$).

### Generazione dell'Immagine Intermedia
Per creare un fotogramma al tempo $t$ (dove $0 \le t \le 1$):
1.  Si interpolano le posizioni dei nodi delle due griglie per creare una **Griglia Intermedia** ($M_t$).
    * $P_{intermedio} = (1-t) \cdot P_{partenza} + t \cdot P_{arrivo}$
2.  Questa griglia intermedia funge da "bersaglio". Dobbiamo deformare l'immagine di partenza affinché la sua griglia coincida con quella intermedia.

---

## 3. L'Algoritmo a Due Passi (2-Pass Mesh Warping)
Il problema del warping è complesso. Per semplificarlo computazionalmente, si scompone la deformazione 2D in due deformazioni 1D successive: una orizzontale (X) e una verticale (Y).

### La Griglia Ausiliaria
Per fare da ponte tra la griglia di Partenza ($M_S$) e la griglia Intermedia ($M_I$), costruiamo una **Griglia Ausiliaria** ($M_{aux}$).
* **Coordinate:**
    * $X_{aux} = X_{partenza}$ (Mantiene le X originali)
    * $Y_{aux} = Y_{intermedia}$ (Adotta le Y target)
    *(Nota: In alcune implementazioni l'ordine è invertito, ma il concetto è creare un ibrido).*
![[Pasted image 20260202170251.png]]
### Passo 1: Distorsione lungo X (First Pass)
Trasformiamo l'immagine Sorgente ($I_S$) in un'immagine temporanea.
1.  **Obiettivo:** Spostare i pixel orizzontalmente.
2.  **Procedura:**
    * Si usano le **Scanline Orizzontali**.
    * Si calcolano le intersezioni tra le linee di scansione e le linee verticali della griglia di partenza e della griglia ausiliaria.
    * Si crea una mappatura 1D che "stira" o "comprime" i pixel di ogni riga.
    * *Risultato:* Un'immagine con la geometria X corretta ma la Y ancora originale.

### Passo 2: Distorsione lungo Y (Second Pass)
Trasformiamo l'immagine temporanea nell'immagine deformata finale.
1.  **Obiettivo:** Spostare i pixel verticalmente.
2.  **Procedura:**
    * Si usa la griglia Ausiliaria (che ha X originali e Y finali) verso la griglia Intermedia.
    * Si usano **Scanline Verticali**.
    * Si mappano le righe di pixel nelle nuove posizioni verticali.
![[Pasted image 20260202170313.png]]
---

## 4. La Doppia Deformazione e il Cross-Dissolve
Per ottenere il morphing completo, l'intero processo di warping viene eseguito **due volte** per ogni fotogramma:

1.  **Warping A:** Deformo l'immagine di Partenza verso la forma Intermedia.
    * Ottengo l'immagine $I_{S \to I}$.
2.  **Warping B:** Deformo l'immagine di Arrivo (all'indietro) verso la forma Intermedia.
    * Ottengo l'immagine $I_{D \to I}$.
    ![[Pasted image 20260202170419.png]]

### Fusione Finale (Blending)
Infine, applico la dissolvenza incrociata pixel per pixel:
$$Pixel_{finale} = (1-t) \cdot Pixel(I_{S \to I}) + t \cdot Pixel(I_{D \to I})$$

* **Perché warpizzare anche l'immagine di arrivo?**
    Se usassi solo quella di partenza, man mano che la forma cambia, i dettagli dell'immagine di arrivo (es. il colore degli occhi, la texture della pelle) non apparirebbero mai correttamente. Devo deformare entrambe verso il centro per farle coincidere geometricamente prima di sfumare i colori.
# Lezione 4 (Parte 6): Morphing con Linee Caratteristiche (Field Morphing)

## 1. Il Concetto: Feature-Based Morphing
A differenza del *Mesh Warping* (dove l'utente sposta vertici di una griglia), qui l'utente ha un compito più intuitivo:
* Disegna segmenti di linea (**Linee Caratteristiche**) sopra le caratteristiche salienti dell'immagine (es. una linea lungo il naso, una lungo la bocca, una sul profilo).
* L'algoritmo calcola come deformare tutti i pixel circostanti basandosi sulla posizione relativa a queste linee.

### Vantaggi
* Più espressivo: controllo diretto sulle features (occhi, bocca).
* Nessuna "griglia fantasma" che copre l'immagine.
* Le linee possono essere posizionate ovunque, non serve una topologia fissa.

---

## 2. Matematica del Mapping (Singola Linea)
Immaginiamo di avere una **singola coppia di linee** corrispondenti:
1.  Linea sull'immagine **Sorgente** (definitita da $Q_1 \to Q_2$).
2.  Linea sull'immagine **Destinazione** (definita da $P_1 \to P_2$).

Vogliamo capire, per un pixel $X$ nell'immagine di destinazione, da dove prendere il colore nell'immagine sorgente.

### Il Sistema di Riferimento Locale $(u, v)$
Ogni linea definisce un sistema di coordinate locale per i pixel vicini:
* **$u$ (Posizione lungo la linea):** La proiezione del punto sulla linea (0 = inizio, 1 = fine).
* **$v$ (Distanza dalla linea):** La distanza perpendicolare dal segmento.

Dato un pixel $X$ nell'immagine di destinazione (quella che stiamo costruendo), calcoliamo $u$ e $v$ rispetto alla linea $P_1 P_2$:
* Vettore linea: $\vec{P} = P_2 - P_1$
* Vettore punto: $\vec{XP} = X - P_1$

$$u = \frac{\vec{XP} \cdot \vec{P}}{||\vec{P}||^2}$$
$$v = \frac{\vec{XP} \cdot \vec{P}_{\perp}}{||\vec{P}||}$$
*(Dove $\vec{P}_{\perp}$ è il vettore perpendicolare alla linea).*

### Backward Mapping (Mappatura all'indietro)
Una volta ottenuti $(u, v)$ per il pixel di destinazione, applichiamo questi valori alla linea **Sorgente** ($Q_1 Q_2$) per trovare il punto $X'$ da cui prelevare il colore.

$$X' = Q_1 + u \cdot (Q_2 - Q_1) + \frac{v \cdot (Q_2 - Q_1)_{\perp}}{||Q_2 - Q_1||}$$

In parole povere: *"Se il pixel $X$ si trova a metà della linea e distante 10 pixel a destra nella destinazione, allora prendi il colore dal punto che si trova a metà della linea e distante (proporzionalmente) a destra nella sorgente."*

---

## 3. Analisi degli Esempi (Slide 42)
Gli esempi allegati ai lucidi (Figure 11.2 a/b) mostrano come la modifica della linea caratteristica influenzi l'intera immagine.



### Caso A: Rotazione
* **Situazione:**
    * *Sorgente:* Linea verticale al centro dell'immagine.
    * *Destinazione:* La linea viene ruotata di 45°.
* **Effetto:** L'intera immagine viene ruotata. Poiché tutti i pixel sono definiti in coordinate $(u,v)$ relative alla linea, se la linea ruota, tutto il "mondo" ruota con essa per mantenere la relazione relativa costante.

### Caso B: Scalamento (Allungamento/Accorciamento)
* **Situazione:**
    * *Sorgente:* Linea lunga al centro.
    * *Destinazione:* La linea viene accorciata (i punti $P_1$ e $P_2$ sono più vicini).
* **Effetto:** L'immagine viene scalata (rimpicciolita). Se la linea di riferimento diventa più corta, i pixel mantengono il loro valore $u$ (proporzione), quindi si "avvicinano" tra loro seguendo la contrazione della linea.

---

## 4. Gestione di Linee Multiple (Cenni Avanzati)
Cosa succede se disegniamo 10 linee (naso, occhi, mento)?
Un pixel si troverà ad avere coordinate $(u,v)$ diverse per ogni linea. Chi vince?
* Si usa una **Media Pesata**.
* Lo spostamento di un pixel è la somma degli spostamenti suggeriti da ogni linea, pesati per la distanza e la lunghezza della linea.
* **Regola:** Le linee più vicine al pixel hanno un peso maggiore (influenza più forte).

### Domanda d'Esame Tipica
*"Come si calcola il colore di un pixel nel morphing se ci sono più linee caratteristiche?"*
**Risposta:** Si calcola la posizione 'sorgente' suggerita da **ogni** linea individualmente. Poi si fa una media di queste posizioni pesata in base alla distanza del pixel dalla linea (più è vicino, più la linea conta). Infine si campiona il colore alla posizione media risultante.