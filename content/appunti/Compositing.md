Il **Compositing** è il processo di combinazione di più layer di immagini separate in un'unica immagine finale. Questo approccio permette una manipolazione selettiva degli elementi della scena senza dover ricalcolare l'intera immagine (rendering) ogni volta che si apporta una modifica a un singolo oggetto.

## 1. Visualizzazione Real-time e [[Double Buffering]]

In contesti come i videogiochi o le simulazioni interattive, la fluidità è essenziale. Per evitare artefatti visivi durante il processo di disegno del fotogramma, si utilizza la tecnica del **Double Buffering**:

* **Front Buffer**: Il buffer di memoria attualmente visualizzato a video.
* **Back Buffer**: Una porzione di memoria nascosta dove l'hardware esegue il rendering del fotogramma successivo.
* **Swap**: Una volta completato il rendering nel Back Buffer, i due buffer vengono scambiati, garantendo una transizione pulita tra i fotogrammi.

## 2. Fondamenti del Compositing

Il principio base è che combinare i render di due scene separate dovrebbe produrre lo stesso risultato del rendering della scena completa:

$$Compositing(render(scena_1), render(scena_2)) = render(merge(scena_1, scena_2))$$

**Condizioni di validità:**
1. Le scene devono essere **disgiunte** spazialmente.
2. I pixel non coperti da oggetti devono essere trattati come **trasparenti**.
3. La funzione di composizione deve dare precedenza all'oggetto più vicino alla camera.

### Il Canale Alpha e l'Operatore Over
Per gestire la trasparenza e l'occlusione, si introduce il **canale Alpha ($\alpha$)**, tipicamente a 8 bit (256 livelli di grigio). 

L'operatore fondamentale è l'**Over**, che definisce come un pixel di foreground ($F$) si sovrappone a uno di background ($B$):

* **Opacità risultante:**
    $$\alpha_{F \ over \ B} = \alpha_F + (1 - \alpha_F) \alpha_B$$
* **Colore risultante (RGB):**
    $$C_{F \ over \ B} = \frac{\alpha_F C_F + (1 - \alpha_F) \alpha_B C_B}{\alpha_{F \ over \ B}}$$

> [!TIP] Terminology
> Spesso i valori RGB sono memorizzati come **pre-moltiplicati** per l'alfa per ottimizzare i calcoli computazionali.

## 3. Gestione della Profondità: Z-Value e Operatore Comp

Quando le scene non sono disgiunte, è necessario utilizzare l'informazione di profondità contenuta nello **[[Z-Buffer]]**.

### Operatore Z-min
Seleziona i valori (RGB, $\alpha$, Z) del pixel con il valore di $Z$ minore (ovvero l'oggetto più vicino).

### Operatore Comp (Composizione Avanzata)
Per risolvere le occlusioni parziali all'interno di un singolo pixel (anti-aliasing), si valuta la $Z$ ai quattro angoli del pixel. L'obiettivo è determinare la frazione $\beta$ del pixel in cui l'immagine $F$ è davanti a $B$.

Esistono 4 casi geometrici derivanti dall'interpolazione della $Z$ sui lati del pixel:
1. **Whole**: Un'immagine copre interamente l'altra.
2. **Corner**: Un angolo di un'immagine occlude l'altra.
3. **Split**: Una linea divide il pixel tra le due immagini.
4. **Two opposite corners**: Due angoli opposti appartengono a layer diversi.

Il risultato finale è un **blend lineare** pesato sulla frazione $\beta$:
$$C = \beta (F \ over \ B) + (1 - \beta) (B \ over \ F)$$

## 4. [[Motion Blur]] e Aliasing Temporale

L'aliasing non è solo spaziale ma anche temporale. Se la frequenza di campionamento (frame rate) è insufficiente rispetto alla velocità degli oggetti, si verifica l'effetto **strobing**.

* **Tecniche Tradizionali**: Uso di "linee di velocità" o deformazioni dell'oggetto lungo la traiettoria (tecnica dello *Squash & Stretch* applicata al moto).
* **Computer Animation**: Il Motion Blur può essere simulato campionando la posizione dell'oggetto in più istanti temporali all'interno dello stesso frame (campionamento discreto) o calcolando analiticamente la scia (continuo).

---

### Collegamenti Correlati
* [[Z-Buffer]]
* [[Aliasing e Filtrazione]]
* [[Pipeline di Rendering]]