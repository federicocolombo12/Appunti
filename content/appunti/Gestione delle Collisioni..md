# Fisica e Gestione delle Collisioni: Introduzione e Rilevamento

## 1. Introduzione al Problema delle Collisioni
[cite_start]In un ambiente virtuale popolato da oggetti in movimento, è inevitabile che le traiettorie si incrocino[cite: 450]. [cite_start]Se non gestiamo questi eventi, otteniamo la **compenetrazione** degli oggetti, un artefatto visivo inaccettabile per una simulazione realistica[cite: 451].

[cite_start]La gestione delle collisioni si divide in due grandi sottoproblemi che devono essere affrontati in sequenza[cite: 451]:

1.  **Collision Detection (Rilevamento):**
    * È un problema di **[[Cinematica]]**.
    * [cite_start]Coinvolge la geometria, la posizione e l'orientamento degli oggetti[cite: 453].
    * L'obiettivo è rispondere alla domanda: *"Gli oggetti si stanno toccando?"*
2.  **Collision Response (Risposta):**
    * È un problema di **[[Dinamica]]**.
    * [cite_start]Coinvolge il calcolo delle forze (es. impulso, attrito) che si generano a seguito dell'impatto[cite: 454].
    * L'obiettivo è determinare *"Come reagiscono gli oggetti all'impatto?"* (es. rimbalzo, deformazione).

---

## 2. Collision Detection: Intersezione Spazio-Temporale
Il metodo teoricamente più accurato per rilevare una collisione è considerare l'oggetto non solo nello spazio, ma nel tempo (4D). [cite_start]Questo approccio si basa sull'operazione di **estrusione**[cite: 469].

### L'Operatore di Estrusione ($Ex$)
[cite_start]Dato un oggetto $O$ e una funzione di trasformazione $\Lambda(t)$ che definisce il movimento dell'oggetto al tempo $t$, l'insieme dei punti occupati dall'oggetto nello spazio-tempo è definito come[cite: 470]:

$$Ex(\Lambda, O) = \{(x,t) | x \in \Lambda(t)(O)\}$$

Dove:
* $x$ rappresenta le coordinate spaziali.
* $t$ rappresenta l'istante temporale.
* [cite_start]La coppia $(x,t)$ indica che l'oggetto occupa il punto $x$ all'istante $t$[cite: 473].

**Condizione di Collisione:**
[cite_start]Due oggetti $A$ e $B$ collidono se e solo se le loro estrusioni spazio-temporali si intersecano[cite: 474]:
$$Ex(\Lambda_A, A) \cap Ex(\Lambda_B, B) \neq \phi$$

> [cite_start]**Nota Critica:** Sebbene matematicamente perfetto, il calcolo esatto delle estrusioni è un'operazione computazionalmente estremamente *costosa*[cite: 479]. Spesso richiede di risolvere equazioni polinomiali di grado elevato.

---

## 3. Swept Volumes vs. Estrusione
Per semplificare il calcolo, si ricorre spesso al concetto di **Swept Volume** (Volume di Scansione).

### L'Operatore Swept Volume ($Sw$)
[cite_start]Lo Swept Volume calcola il volume totale occupato dai punti dell'oggetto durante l'intero intervallo di simulazione (time span), ma "appiattendo" la dimensione temporale[cite: 482]:

$$Sw(\Lambda, O) = \{x | (\exists y,t) \text{ tale che } x = \Lambda(t)(y)\}$$

### Differenza Fondamentale: La perdita dell'informazione temporale
La differenza tra l'operatore di estrusione ($Ex$) e lo Swept Volume ($Sw$) è cruciale per la logica di rilevamento:
* **$Ex$ (Estrusione):** Mantiene la coppia $(x,t)$. Sa *dove* e *quando* un oggetto si trova in un punto.
* **$Sw$ (Swept Volume):** Mantiene solo $x$. [cite_start]Perde l'informazione temporale ($t$)[cite: 484].

**Conseguenze per il Rilevamento:**
[cite_start]L'intersezione degli Swept Volumes **non è sufficiente** a garantire che due oggetti collidano[cite: 485].
* Indica solo che i due oggetti occupano lo stesso spazio *in qualche momento* della simulazione.
* Potrebbero passare per lo stesso punto in istanti diversi (come due auto che attraversano lo stesso incrocio una dopo l'altra senza incidenti).
* [cite_start]L'intersezione degli Swept Volumes è una condizione *necessaria* ma non *sufficiente*: serve a filtrare i casi possibili, ma poi bisogna verificare il moto relativo[cite: 485].

---
**Collegamenti:** [[Cinematica Inversa]], [[Bounding Volumes]], [[Interferenza Temporale]]