# Teoria del Caos e Geometria Frattale

## 1. L'Effetto Farfalla e la Nascita del Caos
Tutto nasce dalla meteorologia negli anni '60 con **Edward Lorenz**.
* **Il contesto:** Lorenz usava un computer rudimentale per simulare il meteo.
* **L'esperimento:** Ripetendo una simulazione, troncò un valore decimale (es. da `0.506127` a `0.506`) pensando che una differenza di un milionesimo non cambiasse nulla.
* **Il risultato:** Il meteo simulato risultò completamente diverso dopo breve tempo.
* **Definizione:** Piccolissime variazioni nelle condizioni iniziali producono mutamenti macroscopici e divergenti nel tempo.

> "Può il battere delle ali di una farfalla in Brasile provocare un tornado in Texas?"

![[Screenshot 2026-02-03 alle 16.13.07.png]]

---

## 2. Sistemi Lineari vs Non-Lineari
La [[Computer Animation]] e la fisica classica spesso cercano di semplificare il mondo in sistemi lineari, ma la realtà è diversa.

### Sistemi Lineari (Prevedibili)
* Una piccola variazione dell'input causa una piccola variazione dell'output.
* **Esempio:** Leggi della dinamica classica, leggi di Maxwell.
* Il mondo è visto come "perline di una collana": eventi sequenziali e ordinati.

### Sistemi Non-Lineari (Caotici)
* Una variazione infinitesimale dell'input porta a risultati **imprevedibili** e **divergenti**.
* **Unicità:** È difficile riprodurre due volte lo stesso evento identico (es. due volute di fumo uguali).
* **Transizione al Caos:** Un sistema può comportarsi in modo lineare fino a una certa soglia, poi diventare caotico.
    * *Esempio:* Un rubinetto che gocciola.
    * *Flusso Laminare:* L'acqua scende dritta e liscia (prevedibile).
    * *Flusso Turbolento:* Aumentando la pressione, il getto si rompe in spruzzi disordinati (caotico).

---

## 3. I Frattali: La Geometria della Natura
Se la geometria Euclidea (linee, cerchi, quadrati) descrive le opere dell'uomo, i **Frattali** descrivono le opere della natura (nuvole, coste, montagne, piante).

### Definizione Formale (Mandelbrot)
Un **Frattale** è un insieme geometrico $F$ che soddisfa le seguenti proprietà:

1.  **Autosimilarità (Self-similarity):** $F$ è l'unione di un numero di parti che, se ingrandite, riproducono $F$ stesso. Il tutto è simile a una sua parte.![[Screenshot 2026-02-03 alle 16.13.56.png]]

2.  **Struttura Fine:** Possiede dettagli a scale arbitrariamente piccole (non diventa "liscio" se zumiamo, continua a mostrare dettagli).
3.  **Irregolarità:** Non può essere descritto dal luogo di punti che soddisfano semplici equazioni geometriche o analitiche classiche.
4.  **Dimensione Frattale ($D_F$) > Dimensione Topologica ($D_T$):**
    * La dimensione topologica è sempre un intero (Linea = 1, Piano = 2).
    * La dimensione frattale è spesso un numero frazionario.
![[Screenshot 2026-02-03 alle 16.13.35.png]]
---

## 4. Dimensione di Autosimilarità (Approfondimento)
Come si calcola la dimensione di un frattale? Si usa il concetto di "copie in scala".

Se dividiamo un oggetto in $N$ parti, ciascuna scalata di un fattore $r$ (dove $r < 1$):
* Per una **linea** (1D): divido in $N=2$ pezzi, ogni pezzo è $r=1/2$ dell'originale.
* Per un **quadrato** (2D): divido in $N=4$ pezzi, ogni pezzo ha lato $r=1/2$.

La relazione è $N = (1/r)^D$.
Da cui si ricava la **Dimensione Frattale**:
$$D = \frac{\log(N)}{\log(1/r)}$$

*Esempio (Koch Curve):* Si divide il segmento in 4 parti ($N=4$), ma la scala è 1/3 ($r=1/3$).
$$D = \frac{\log(4)}{\log(3)} \approx 1.2618$$
La curva di Koch è "più di una linea" ma "meno di un piano". Riempie lo spazio in modo più efficiente di una linea retta.