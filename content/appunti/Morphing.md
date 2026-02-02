
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

---

## 4. La Doppia Deformazione e il Cross-Dissolve
Per ottenere il morphing completo, l'intero processo di warping viene eseguito **due volte** per ogni fotogramma:

1.  **Warping A:** Deformo l'immagine di Partenza verso la forma Intermedia.
    * Ottengo l'immagine $I_{S \to I}$.
2.  **Warping B:** Deformo l'immagine di Arrivo (all'indietro) verso la forma Intermedia.
    * Ottengo l'immagine $I_{D \to I}$.

### Fusione Finale (Blending)
Infine, applico la dissolvenza incrociata pixel per pixel:
$$Pixel_{finale} = (1-t) \cdot Pixel(I_{S \to I}) + t \cdot Pixel(I_{D \to I})$$

* **Perché warpizzare anche l'immagine di arrivo?**
    Se usassi solo quella di partenza, man mano che la forma cambia, i dettagli dell'immagine di arrivo (es. il colore degli occhi, la texture della pelle) non apparirebbero mai correttamente. Devo deformare entrambe verso il centro per farle coincidere geometricamente prima di sfumare i colori.