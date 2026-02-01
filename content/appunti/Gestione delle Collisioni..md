
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