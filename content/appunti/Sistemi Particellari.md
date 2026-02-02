Un sistema particellare è una tecnica usata per modellare oggetti "sfocati" o fluidi che non hanno una forma geometrica fissa e rigida.
* **Oggetti Semplici:** Le singole particelle sono primitive geometriche elementari, spesso renderizzate come **Sorgenti di Luce Puntiformi** (Point Light Sources) o piccoli sprite.
* **Numerosità:** Si parla di *tanti* oggetti ("Lots of objects").
* **Moto Semplice:** Ogni particella segue leggi fisiche di base.
* **Nessuna Collisione Interna:** Per efficienza, solitamente si **ignorano le collisioni tra particelle** (calcolare collisioni tra 10.000 particelle sarebbe troppo costoso).

---

## 2. Il Ciclo di Vita (Calcoli per Frame)
A differenza di un personaggio che "esiste sempre", le particelle nascono e muoiono continuamente. Il motore di animazione esegue questi passaggi in sequenza **ad ogni frame**:

1.  **Terminazione:** Elimina le particelle "scadute" (la cui vita è finita).
2.  **Update (Aggiornamento):** Aggiorna gli attributi delle particelle vive (posizione, velocità, colore) basandosi sulle regole di controllo.
3.  **Generazione:** Crea nuove particelle e assegna loro gli attributi iniziali.
4.  **Rendering:** Disegna tutte le particelle vive.

### Il Diagramma del Ciclo
La vita di una particella segue questo flusso:
$$\text{Nascita (Birth)} \rightarrow \text{Reazione all'Ambiente} \rightarrow \text{Morte (Death)}$$

---

## 3. Generazione delle Particelle
Come nascono le particelle? Non è un processo casuale puro, ma un **Processo Random Controllato** (Stochastic Process).

* **Distribuzione:**
    * **Nello Spazio:** Nascono da un emettitore (es. un punto, un cerchio, un volume).
    * **Nel Tempo:** Non nascono tutte insieme, ma vengono emesse con un certo rateo (es. 100 particelle al secondo).
* **Sincronizzazione (Gestione Memoria):**
    È fondamentale sincronizzare la generazione con la terminazione.
    * *Obiettivo:* Mantenere un **numero limitato (bounded)** di particelle vive in ogni istante. Se ne nascono più di quante ne muoiono, il sistema rallenta fino a bloccarsi.

---

## 4. Attributi e Struttura Dati
Ogni singola particella è definita da un set di dati.

### Attributi della Particella
1.  **Posizione:** $(x, y, z)$
2.  **Velocità:** Vettore di movimento.
3.  **Forma:** (Opzionale, spesso sono punti).
4.  **Attributi di Rendering:** Colore, Trasparenza (Alpha). Spesso variano nel tempo (es. il fumo diventa trasparente mentre sale).
5.  **Life Expectancy (Aspettativa di vita):** Un valore pseudo-random che determina quanti frame vivrà la particella.

### Rappresentazione del Sistema (Array)
Il sistema gestisce un grande array di strutture dati, contenenti per ogni particella:
* Posizione
* Velocità
* **Forza Accumulata** (la somma delle forze che agiscono su di essa in quel frame)
* Massa

---

## 5. Fisica e Aggiornamento (Solver)
Come si muovono le particelle? Usiamo la fisica newtoniana di base ($F=ma$).

### Procedura di Aggiornamento (Step 2 del Ciclo)
Per ogni particella viva:
1.  **Clear Forces:** Azzera l'accumulatore delle forze (le forze del frame precedente non contano più).
2.  **Accumulate New Forces:** Somma tutte le forze attive:
    * *Forze Unarie:* Gravità, Viscosità (resistenza dell'aria).
    * *Forze Ambientali:* Vento, campi di forza, repulsione da ostacoli.
3.  **Compute Acceleration & Velocity:**
    * $a = \frac{F_{tot}}{m}$
    * $v_{new} = v_{old} + a \cdot \Delta t$
4.  **Update Position:**
    * $P_{new} = P_{old} + v_{media} \cdot \Delta t$

> **Nota:** Si assume che l'accelerazione sia **costante** durante il piccolo intervallo di tempo $\Delta t$ (metodo di Eulero).

### Tipi di Forze Comuni
* **Gravità:** $F = mg$ (tira verso il basso).
* **Viscosità (Drag):** $F = -k \cdot v$ (forza opposta alla velocità, rallenta la particella).
* **Repulsione/Collisione:** Se la particella colpisce il suolo, rimbalza (cambia verso della velocità) o muore.

---

## 6. Conclusione: Caratteristiche Chiave
Per riassumere, un Sistema Particellare si riconosce da questi tratti distintivi (utili per il confronto con il Flocking):

1.  **Molti Membri:** Gestisce migliaia di entità.
2.  **Fisica Semplice:** Calcoli rapidi per permettere il real-time.
3.  **No Knowledge (Ignoranza):** La particella **non sa** dell'esistenza delle altre particelle (non si evitano, non si seguono).
4.  **Geometria Minimale:** Punti o sprite semplici.
5.  **Comportamento Emergente:** Da regole locali semplici (es. "cadi e diventa trasparente") emerge un effetto globale complesso (es. "una cascata d'acqua").