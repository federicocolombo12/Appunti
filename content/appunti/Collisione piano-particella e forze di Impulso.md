
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
