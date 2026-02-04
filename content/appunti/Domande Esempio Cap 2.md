### 2.1 Riparametrizzazione e Lunghezza d'Arco

_Concetti chiave: Parametro u vs Distanza s, Look-Up Table (LUT)._

**Domande Base e Discorsive**

- **D1:** Spiegare la differenza tra il parametro geometrico $u$ di una curva e la lunghezza d'arco $s$. Perché incrementare $u$ in modo costante non garantisce una velocità costante dell'oggetto lungo la curva?
    
- **D2:** Cos'è una _Look-Up Table_ (LUT) nel contesto del controllo del moto e qual è il suo scopo principale?
    

**Esercizio (Procedurale)**

- **D3:** Descrivere passo dopo passo l'algoritmo per costruire una tabella di riparametrizzazione (LUT) mediante il metodo delle _differenze dirette_ (campionamento).
    
    - _Input:_ Una curva $P(u)$ e un numero di campioni.
        
    - _Output:_ Tabella coppie $(u, s)$.
        
- **D4:** Data una LUT e una distanza target $s_{target}$ che cade tra due valori registrati nella tabella ($s_i$ e $s_{i+1}$), scrivere la formula per trovare il valore approssimato di $u$ (interpolazione lineare inversa).
    

**Approfondimenti**

- **D5:** Perché non è sempre possibile calcolare la lunghezza d'arco risolvendo analiticamente l'integrale della curva? Qual è il vantaggio dell'approccio numerico (es. Quadratura Gaussiana) rispetto al semplice campionamento?
    

---

### 2.2 Controllo della Velocità (Speed Control)

_Concetti chiave: Ease-in/Ease-out, Funzioni Distanza-Tempo._

**Domande Base e Discorsive**

- **D6:** Definire i concetti di _Ease-in_ e _Ease-out_. Come influenzano la percezione del peso e dell'inerzia di un oggetto animato?
    
- **D7:** Qual è la differenza tra modificare la geometria della curva e modificare la funzione distanza-tempo $s(t)$?
    

**Esercizio**

- **D8:** Disegnare qualitativamente il grafico Distanza/Tempo ($s$ su asse Y, $t$ su asse X) per un movimento che prevede: partenza lenta (ease-in), tratto a velocità costante, e arrivo brusco (nessun ease-out).
    

**Approfondimenti**

- **D9:** Confrontare l'interpolazione sinusoidale con quella parabolica (accelerazione costante) per generare l'effetto di ease-in/ease-out. Quali sono i limiti della prima?
    

---

### 2.3 Orientamento e Frame di Frenet

_Concetti chiave: Terna T-N-B, Problemi di stabilità, Centro di Interesse._

**Domande Base e Discorsive (Dal file "Domande esame.pdf")**

- **D10:** Presentare il Frame di Frenet per la gestione dell'orientamento. Come viene costruita la terna di vettori (Tangente, Normale, Binormale) a partire dalla curva?
    
- **D11:** Qual è il ruolo del _prodotto vettoriale_ (cross product) nella costruzione di un frame di orientamento locale?
    

**Esercizio**

- **D12:** Scrivere le formule per derivare i vettori $W$ (Tangente), $U$ (Binormale) e $V$ (Normale) dato il vettore posizione $P(u)$ e le sue derivate.
    

**Approfondimenti (Criticità)**

- **D13:** Discutere dettagliatamente i difetti del Frame di Frenet evidenziando:
    
    1. Il problema dei tratti rettilinei (curvatura nulla).
        
    2. Il problema del _flipping_ nei punti di flesso.
        
- **D14:** Spiegare come l'introduzione di un "Centro di Interesse" (COI) o di un vettore "Up" fisso risolve le instabilità del Frame di Frenet.
    

---

### 2.4 Smoothing e Convoluzione

_Concetti chiave: Filtraggio dati, Kernel, Motion Capture._

**Domande Base e Discorsive**

- **D15:** A cosa serve l'operazione di _Smoothing_ applicata a una traiettoria (spesso derivante da dati rumorosi di Motion Capture)?
    
- **D16:** Cos'è un _Kernel di convoluzione_?
    

**Esercizio**

- **D17:** Elencare le quattro proprietà fondamentali che un Kernel di smoothing deve possedere (es. simmetria, area unitaria...). Spiegare perché l'area sottesa deve essere pari a 1.
    

**Approfondimenti**

- **D18:** Cosa succede alla traiettoria originale se applichiamo un kernel di smoothing troppo "largo" (supporto ampio)?
    

---

### 2.5 Integrazione Numerica e Simulazione

_Concetti chiave: Eulero vs Runge-Kutta, Ciclo di simulazione._

**Domande Base e Discorsive (Dal file "Domande esame.pdf")**

- **D19:** Descrivere il **ciclo di simulazione** di un corpo rigido. (Quali sono gli step logici che si ripetono ad ogni frame?)
    
- **D20:** Cosa significa "integrare un'equazione differenziale ordinaria (ODE)" nel contesto dell'animazione fisica?
    

**Esercizio**

- **D21:** Scrivere la formula di aggiornamento della posizione secondo il **Metodo di Eulero**.
    
    - $x_{t+\Delta t} = \dots$
        

**Approfondimenti**

- **D22:** Confrontare il metodo di Eulero con il metodo di **Runge-Kutta (RK4)**.
    
    - Perché Eulero è considerato instabile per passi temporali ($\Delta t$) grandi?
        
    - Cosa fa il metodo RK4 per ottenere una maggiore precisione?