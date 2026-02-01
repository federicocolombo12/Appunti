# Forze Esterne e Ambientali

Oltre alle forze impulsive generate dagli urti, sugli oggetti agiscono forze continue durante tutta la simulazione.

## A. Gravità
È la forza più semplice e onnipresente.
$$F_g = m \cdot g$$
Dove $g$ è l'accelerazione di gravità (di solito $\approx 9.81 m/s^2$ verso il basso).
* **Nota implementativa:** Poiché $F=ma$, l'accelerazione dovuta alla gravità è costante per tutti gli oggetti, indipendentemente dalla massa ($a = F/m = (mg)/m = g$).

## B. Attrito (Frizione)
L'attrito è una forza che si oppone al movimento tra due superfici a contatto. Dobbiamo distinguere due stati fondamentali:

### 1. Attrito Statico (Static Friction)
Agisce quando l'oggetto è **fermo** ma una forza esterna cerca di muoverlo.
* La forza di attrito statico $f_s$ bilancia esattamente la forza applicata, fino a un limite massimo.
* **Soglia di rottura:** $f_s \le \mu_s \cdot N$
  * $\mu_s$: Coefficiente di attrito statico.
  * $N$: Forza Normale (pressione contro la superficie).
* Se la forza applicata supera $\mu_s N$, l'oggetto inizia a muoversi e si passa al caso cinetico.

### 2. Attrito Cinetico (Kinetic Friction)
Agisce quando l'oggetto è **in movimento**. Si oppone alla direzione della velocità.
$$F_k = -(\mu_k \cdot N) \cdot \frac{v}{|v|}$$
* $\mu_k$: Coefficiente di attrito cinetico (generalmente $\mu_k < \mu_s$).
* Questo spiega perché serve più forza per *iniziare* a spingere un armadio che per *continuare* a spingerlo.

## C. Viscosità (Smorzamento dell'Aria/Fluido)
A differenza dell'attrito (che dipende dal contatto), la viscosità è la resistenza del mezzo in cui l'oggetto si muove (aria, acqua).
Dipende dalla velocità:
$$F_{viscosity} = -k \cdot v$$
* Serve a "scaricare" energia dal sistema, altrimenti un oggetto lanciato nel vuoto virtuale non si fermerebbe mai.

---

# Corpi Deformabili: Sistemi Massa-Molla

Abbandoniamo l'ipotesi di "Corpo Rigido". Un corpo deformabile è modellato come un insieme di particelle (masse) collegate da forze elastiche.

## Il Sistema Molla-Smorzatore (Spring-Damper)
È l'unità fondamentale. Collega due particelle $A$ e $B$.

### 1. La Forza Elastica (Legge di Hooke)
Tende a riportare la molla alla sua lunghezza di riposo ($L_0$).
$$F_s = -k_s \cdot (|L| - L_0) \cdot \frac{L}{|L|}$$
* $k_s$: Costante elastica (Rigidezza/Stiffness). Alto = molla dura; Basso = molla molle.
* $L$: Vettore distanza attuale tra le particelle ($P_a - P_b$).
* Se la molla è compressa ($|L| < L_0$), spinge in fuori. Se è estesa ($|L| > L_0$), tira in dentro.

### 2. Lo Smorzatore (Damper)
Una molla ideale oscilla all'infinito. Per simulare la realtà, dobbiamo dissipare l'energia cinetica dell'oscillazione.
Lo smorzatore agisce sulla **velocità relativa** tra le due particelle proiettata lungo l'asse della molla:
$$F_d = -k_d \cdot (v_{rel} \cdot \frac{L}{|L|}) \cdot \frac{L}{|L|}$$
* $k_d$: Coefficiente di smorzamento (Damping).
* Senza questo termine, una gelatina tremolerebbe per sempre senza mai fermarsi.

---

# Spring Mesh (Reti di Molle)

Per simulare oggetti 3D o tessuti (Cloth Simulation), colleghiamo molteplici masse in una rete (Mesh).
Tuttavia, collegare solo i vicini diretti non basta: la struttura collasserebbe o si comporterebbe in modo strano (effetto "tessuto super elastico").

Per strutturare una Spring Mesh stabile si usano tre tipi di collegamenti:

1.  **Molle Strutturali (Structural Springs):**
    * Collegano ogni nodo ai suoi vicini diretti (su/giù, destra/sinistra).
    * Gestiscono la compressione e l'estensione di base.
    * *Problema:* Non resistono al "taglio" (la maglia può deformarsi a rombo).
2.  **Molle di Taglio (Shear Springs):**
    * Collegano i nodi in **diagonale**.
    * Impediscono che un quadrato di tessuto diventi un rombo, mantenendo la rigidità planare.
3.  **Molle di Flessione (Bend Springs):**
    * Collegano un nodo al nodo **successivo al vicino** (saltandone uno: es. nodo $i$ con $i+2$).
    * Impediscono che il tessuto si pieghi su sé stesso troppo facilmente (resistenza alla piegatura).
    * Senza di queste, il tessuto sembrerebbe seta sottilissima; con molle di flessione forti, sembra cuoio o cartone.

---
**Collegamenti:** [[Legge di Hooke]], [[Cloth Simulation]], [[Integrazione Numerica]]