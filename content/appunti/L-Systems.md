
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

---

## 4. Le Gemme: Il Motore della Crescita
Le gemme sono embrioni di tessuto vegetale. La loro gestione è cruciale negli L-System contestuali.

### Classificazione per Posizione
* **Gemma Terminale (Apicale):** Situata alla fine del ramo/fusto. Determina l'allungamento.
* **Gemma Laterale (Ascellare):** Situata lungo il fusto (all'ascella della foglia). Determina la ramificazione laterale.

### Classificazione per Funzione
* **Vegetative:** Danno origine a nuovi fusti e foglie.
* **Floreali:** Danno origine ai fiori (terminando la crescita di quel ramo).

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