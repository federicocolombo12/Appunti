Analizziamo ora il caso più elementare di collisione e risposta cinematica: una particella puntiforme che colpisce un piano fisso.

## Il Problema
Immaginiamo una particella che si muove con velocità costante verso un muro (piano). Dobbiamo:
1.  Capire **se** e **quando** lo attraversa.
2.  Calcolare la nuova velocità per farla **rimbalzare**.

## 1. Definizione Matematica

### L'Equazione del Piano
![[Pasted image 20260201131433.png]]
Un piano nello spazio è definito da un punto e una normale, oppure dall'equazione implicita:
$$E(p) = ax + by + cz + d = 0$$
Dove $(a,b,c)$ sono le componenti del vettore normale $N$ al piano.
* Se $E(p) > 0$: Il punto $p$ è "davanti" al piano.
* Se $E(p) = 0$: Il punto $p$ è esattamente sul piano.
* Se $E(p) < 0$: Il punto $p$ è "dietro" al piano.

### L'Aggiornamento della Particella
La particella si muove nel tempo secondo passi discreti ($t_i$). La sua posizione è aggiornata in base alla velocità media ($v_{ave}$):
$$p(t_i) = p(t_{i-1}) + \Delta t \cdot v_{ave}$$

## 2. Rilevamento dell'Intersezione (Detection)
Ad ogni passo della simulazione ($t_i$), valutiamo l'equazione del piano $E(p(t_i))$.
* Finché $E(p(t_i)) > 0$, la particella è ancora in volo davanti al muro.
* Nel momento in cui $E(p(t_i)) \le 0$, è avvenuta una collisione (la particella ha attraversato o toccato il piano) nell'intervallo di tempo tra $t_{i-1}$ e $t_i$.

## 3. Risposta Cinematica: Il Rimbalzo
![[Pasted image 20260201131511.png]]
Una volta rilevata la collisione, dobbiamo aggiornare la velocità per simulare il rimbalzo.
La logica vettoriale è la seguente:
1.  Scomponiamo la velocità $v$ in due componenti:
    * **Normale:** Perpendicolare al piano.
    * **Tangenziale:** Parallela al piano.
2.  Per ottenere un rimbalzo perfetto, dobbiamo **invertire** la componente normale della velocità, lasciando inalterata quella tangenziale.

**Formula di Riflessione:**
La nuova velocità $v'$ si calcola sottraendo due volte la proiezione della velocità sulla normale:
$$v' = v - 2(v \cdot N)N$$

## 4. Smorzamento (Damping)
Nella realtà, nessun rimbalzo è perfettamente elastico (l'energia si dissipa).
Per simulare questo effetto, introduciamo un coefficiente di smorzamento $k$ (con $0 < k < 1$).
La formula modificata diventa:

$$v' = v - (1 + k)(v \cdot N)N$$

* Se $k=1$: Rimbalzo perfettamente elastico (nessuna perdita di energia).
* Se $k \to 0$: Rimbalzo anelastico (la pallina tende a fermarsi contro il muro, "smorzandosi").

> **Nota:** Questo approccio è puramente cinematico (basato sul movimento) e produce risultati visivamente plausibili, specialmente per oggetti sferici, pur non calcolando le forze fisiche reali (massa, momento, ecc.).

# Il Metodo della Penalità

Fino ad ora abbiamo gestito i rimbalzi invertendo semplicemente la velocità (cinematica). Il **Metodo della Penalità** cerca di risolvere la collisione introducendo un modello fisico basato su forze elastiche.

## Il Concetto
L'idea è semplice: se un oggetto compenetra un altro, il sistema reagisce come se ci fosse una **molla** compressa tra i due nel punto di contatto.
Più l'oggetto penetra in profondità ($d$), più forte sarà la forza che tenta di spingerlo fuori.

### La Fisica del Metodo
1.  **La Forza Elastica (Legge di Hooke):**
    Viene generata una forza repulsiva $F$ proporzionale alla profondità di compenetrazione $d$:
    $$F = -k \cdot d$$
    Dove $k$ è la *costante elastica* della molla (rigidità).

2.  **Introduzione di Massa e Velocità:**
    A differenza dell'approccio cinematico, qui stiamo calcolando una **Forza**. Per convertire questa forza in un cambiamento di movimento, dobbiamo applicare il Secondo Principio della Dinamica ($F = m \cdot a$).
    * È necessario assegnare una **massa** ($m$) all'oggetto.
    * Calcoliamo l'accelerazione: $a = F / m$.
    * Aggiorniamo la **velocità**: $v_{new} = v_{old} + a \cdot \Delta t$.

### Problemi di questo approccio
Sebbene intuitivo, questo metodo presenta difetti notevoli:
* **Arbitrarietà:** Bisogna "inventare" i valori di $k$ (rigidità molla) e $m$ (massa). Se scelti male, l'oggetto può sembrare troppo soffice o instabile.
* **Compenetrazione:** La forza si attiva *solo* quando l'oggetto è già dentro l'altro. Non previene la collisione, la corregge dopo che è avvenuta.

---

# 9. Forze di Impulso (Impulse Forces)

Questo è l'approccio standard per i motori fisici professionali. È definito **più raffinato** perché rispetta la legge di conservazione della quantità di moto, ma è **più oneroso** computazionalmente perché richiede di trovare l'esatto istante dell'impatto e ricalcolare lo stato del sistema.

## Il Procedimento Temporale
A differenza del metodo della penalità (che accetta la compenetrazione), il metodo a impulsi cerca di evitare che gli oggetti si sovrappongano.
Se al tempo $t_i$ rileviamo una compenetrazione che non c'era a $t_{i-1}$:
1.  **Backtracking:** Si torna indietro nel tempo per trovare l'istante esatto ($t_{col}$) dell'impatto.
2.  **Calcolo Reazione:** Si calcolano le nuove velocità.
3.  **Restart:** Si fa ripartire la simulazione da $t_{col}$ con le nuove velocità.

## Ricerca dell'Istante di Collisione ($t_{col}$)
Come troviamo il momento esatto tra $t_{i-1}$ e $t_i$?

1.  **Ricerca Binaria (Iterativa):**
    * Si divide l'intervallo temporale a metà.
    * Si controlla se c'è collisione nel punto medio.
    * Si ripete dimezzando l'intervallo fino a raggiungere una precisione tollerabile.
    * *Pro:* Robusto. *Contro:* Lento (molte iterazioni).
2.  **Calcolo Analitico (Approssimazione Lineare):**
    * Si assume che tra i due istanti l'oggetto si sia mosso in linea retta a velocità costante.
    * Si calcola l'intersezione geometrica tra il segmento di traiettoria e il piano.
    * *Formula:* $t_{col} = t_{i-1} + \frac{S}{L} \cdot (t_i - t_{i-1})$ (dove $S$ è la distanza dal piano e $L$ la lunghezza del passo).
    *![[Pasted image 20260201132504.png]]

---

# 10. Risposta all'Impatto: Impulso e Corpi Rigidi
![[Pasted image 20260201132541.png]]

Una volta trovato l'istante e il punto di contatto, applichiamo un **Impulso** ($J$).
L'impulso è definito come una forza molto grande applicata per un tempo infinitesimale, capace di cambiare istantaneamente la quantità di moto ($P$) dell'oggetto.

$$J = F \cdot \Delta t = \Delta P$$

### Caratterizzare l'Elasticità ($\epsilon$)
La "rimbalzosità" della collisione è definita dal coefficiente di restituzione $\epsilon$ (epsilon), con $0 \le \epsilon \le 1$.
Esso lega la velocità relativa prima dell'urto ($v_{rel}^{-}$) a quella dopo l'urto ($v_{rel}^{+}$):

$$v_{rel}^{+} = -\epsilon \cdot v_{rel}^{-}$$

### La Velocità di un Punto su un Corpo Rigido
Qui le cose si complicano. Se un oggetto ruota (es. un cubo che cade di spigolo), la velocità del punto di impatto non dipende solo dalla velocità lineare del centro di massa, ma anche dalla rotazione.
![[Pasted image 20260201132719.png]]

La velocità $p(t)$ di un punto specifico sulla superficie dell'oggetto è:

$$p(t) = v(t) + \omega(t) \times r$$

Dove:
* $v(t)$: Velocità lineare del centro di massa.
* $\omega(t)$: Velocità angolare dell'oggetto.
* $r$: Vettore distanza dal centro di massa al punto di contatto.
* $\times$: Prodotto vettoriale.

> **Importante:** Quando applichiamo l'impulso $J$, questo modificherà **sia** la velocità lineare $v$ (spostando l'oggetto), **sia** la velocità angolare $\omega$ (facendolo ruotare), in base al momento torcente generato dall'impatto.

---
# Calcolo dell'Impulso e Aggiornamento delle Velocità

Una volta determinato che c'è stata una collisione e trovato il punto di contatto, dobbiamo applicare l'impulso $J$ per modificare lo stato dell'oggetto.

Ricordiamo che l'impulso è un vettore diretto lungo la normale di collisione $n$:
$$J = j \cdot n$$
Dove $j$ è la **magnitudine** (l'intensità scalare) dell'impulso, che è l'incognita che dobbiamo trovare.

## A. Aggiornamento delle Velocità (Linear e Angular)
L'impulso agisce istantaneamente sulle velocità dell'oggetto. Le formule di aggiornamento derivano dalle leggi di Newton e di Eulero.

### 1. Velocità Lineare ($v$)
La variazione di velocità lineare dipende dalla massa totale $M$.
$$v^+ = v^- + \frac{j \cdot n}{M}$$
* $v^+$: Velocità dopo l'urto.
* $v^-$: Velocità prima dell'urto.

### 2. Velocità Angolare ($\omega$)
La variazione di velocità angolare è più complessa perché dipende da **dove** colpiamo l'oggetto (il braccio $r$) e da **come è distribuita la massa** (il Tensore d'Inerzia $I$).
$$\omega^+ = \omega^- + I^{-1}(r \times (j \cdot n))$$

Dove:
* $I^{-1}$: Inversa della matrice del Tensore d'Inerzia (rappresenta la resistenza alla rotazione sui vari assi).
* $r$: Vettore dal centro di massa al punto di contatto.
* $\times$: Prodotto vettoriale.

---

## B. Calcolo della Magnitudine dell'Impulso ($j$)

Per calcolare $j$, usiamo il **coefficiente di restituzione** $\epsilon$ (epsilon). Sappiamo che la velocità relativa dopo l'urto deve essere una frazione $-\epsilon$ di quella prima dell'urto.

Combinando le equazioni di aggiornamento della velocità (sopra) con la definizione di elasticità, otteniamo la "Grande Formula dell'Impulso".

### La Formula Finale
$$j = \frac{-(1 + \epsilon) v_{rel}^-}{\frac{1}{M} + n \cdot (I^{-1}(r \times n) \times r)}$$

### Analisi della Formula (Cosa significa?)
Non limitatevi a memorizzarla, capiamola pezzo per pezzo:

1.  **Numeratore [$-(1 + \epsilon) v_{rel}^-$]:**
    * Rappresenta l'energia del rimbalzo.
    * Se $\epsilon = 1$ (super elasticità), il numeratore è massimo (doppia inversione di velocità).
    * $v_{rel}^-$ è la velocità con cui i punti di contatto si stavano avvicinando.

2.  **Denominatore [Termine di Massa e Inerzia]:**
    * Rappresenta l'**inerzia totale** vista dal punto di contatto.
    * $\frac{1}{M}$: È la resistenza al movimento lineare.
    * $n \cdot (I^{-1}(r \times n) \times r)$: È la resistenza al movimento rotatorio.
    * *Interpretazione fisica:* Colpire un oggetto al centro (dove $r \approx 0$ o parallelo a $n$) minimizza il termine rotatorio, rendendo l'oggetto "più pesante" da spostare. Colpirlo di striscio o lontano dal centro rende il denominatore più grande, quindi l'impulso risultante $j$ cambia per favorire la rotazione.

---
**Collegamenti:** [[Tensore d'Inerzia]], [[Prodotto Vettoriale]], [[Coordinate Locali vs Globali]]