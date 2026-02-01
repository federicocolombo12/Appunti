## La Complessità della Figura Umana
Modellare una figura umana è significativamente più difficile rispetto a un robot industriale.
* **Alto numero di gradi di libertà (DOF):** Il corpo umano ha decine di articolazioni.
* **Vincoli complessi:** Le articolazioni umane non ruotano liberamente (es. il gomito non si piega all'indietro).
* **Ridondanza:** Abbiamo molti modi diversi per toccarci la punta del naso (gomito alto, gomito basso).

## B. Il Modello dell'Arto Superiore (7 DOF)
Per simulare realisticamente un braccio umano, si utilizza un modello cinematico a **7 Gradi di Libertà**.

La struttura è così composta:
1.  **Spalla (Shoulder):** Giunto sferico $\rightarrow$ **3 DOF** (Rotazione su 3 assi).
2.  **Gomito (Elbow):** Giunto a cerniera (flessione) + rotazione (pronazione/supinazione dell'avambraccio) $\rightarrow$ **1 o 2 DOF** (solitamente semplificato a 1 per la flessione, spostando la rotazione al polso o aggiungendo un giunto "twist").
3.  **Polso (Wrist):** Giunto complesso $\rightarrow$ **3 DOF** (Flessione, Deviazione, Rotazione).

**Totale:** $3 + 1 + 3 = 7$ DOF.
![[Screenshot 2026-02-01 alle 20.03.49.png]]
### Il Problema della Ridondanza
Per posizionare e orientare un oggetto nello spazio (la mano) servono esattamente **6 DOF** (3 posizione + 3 orientamento).
Poiché il braccio ne ha **7**, abbiamo $7 - 6 = 1$ Grado di Libertà "extra" (ridondante).
* Questo significa che per una stessa posizione fissa della mano e della spalla, il gomito può ancora muoversi descrivendo un arco (o cerchio) nello spazio.



## C. Limiti di Rotazione (Joint Limits)
Un aspetto critico per il realismo è la gestione dei limiti angolari, specialmente nel polso e nella spalla.
* Senza limiti, l'algoritmo di IK potrebbe scegliere una soluzione matematica valida ma biomeccanicamente impossibile (es. polso ruotato di 360°).
* I limiti sono definiti come intervalli $[\theta_{min}, \theta_{max}]$ per ogni asse di ogni giunto.
* Nella risoluzione IK, se un angolo calcolato esce dal range, viene "bloccato" (clamped) al limite più vicino.

---

## D. Strategia di Cinematica Inversa (IK) Procedurale

Per risolvere la postura del braccio data la posizione della mano, si usa spesso un approccio procedurale/geometrico che "modernizza" o semplifica l'uso della matrice Jacobiana classica, rendendolo più intuitivo per l'animazione.

### 1. Il Principio della Distanza
Si basa sull'osservazione fisica che **i giunti più lontani dall'end-effector (es. la spalla) hanno un impatto maggiore sulla posizione della mano** rispetto ai giunti vicini (es. polso).
* Una piccola rotazione della spalla sposta la mano di molto (grande braccio di leva).
* Una rotazione del polso cambia l'orientamento della mano ma sposta poco la sua posizione.

### 2. Algoritmo Geometrico (CCD - Cyclic Coordinate Descent Simile)
Invece di invertire una matrice complessa, si procede iterativamente usando il **Prodotto Vettoriale**:
![[Screenshot 2026-02-01 alle 20.05.02.png]]
Per ogni giunto, partendo (spesso) dal più lontano o iterando sulla catena:
1.  Si calcola il vettore **attuale** verso l'end-effector ($V_{curr}$).
2.  Si calcola il vettore **desiderato** verso il target ($V_{target}$).
3.  Si trova l'asse di rotazione ottimale $a$ tramite il prodotto vettoriale:
    $$a = V_{curr} \times V_{target}$$
4.  Si trova l'angolo $\theta$ tramite il prodotto scalare (coseno).
5.  Si ruota il giunto.

---

## E. La Regola Empirica: Il Piano del Gomito (Swivel Angle)
A causa della ridondanza (quel 7° grado di libertà di cui parlavamo), il computer non sa dove mettere il gomito. Matematicamente ci sono infinite soluzioni.
Per risolvere questo, si introduce una **regola empirica** (Constraint) che l'animatore deve specificare.
![[Screenshot 2026-02-01 alle 20.05.32.png]]
### Definizione del Piano Spalla-Gomito-Polso
L'animatore definisce un piano su cui devono giacere la spalla, il gomito e il polso.
* In software come Maya o Blender, questo è controllato da un **Pole Vector** (Vettore Polare) o **Swivel Angle**.
* L'algoritmo IK:
    1.  Posiziona la Spalla e il Polso (che sono fissi).
    2.  Calcola la posizione del Gomito in modo che la lunghezza delle braccia sia rispettata.
    3.  Ruota l'intero triangolo "Spalla-Gomito-Polso" attorno all'asse Spalla-Polso finché il gomito non punta verso il piano o il vettore specificato dall'animatore.



**Vantaggio:** Questo trasforma un problema mal condizionato (infinite soluzioni) in una soluzione unica e controllabile artisticamente (es. "tieni i gomiti larghi" o "tieni i gomiti stretti lungo il corpo").