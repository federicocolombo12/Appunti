### 3.1 Forze Fisiche: Gravità, Attrito e Molle

_Concetti chiave: F=ma, Tipi di attrito, Legge di Hooke, Smorzatori._

**Domande Base e Discorsive**

- **D1:** Qual è la differenza sostanziale tra **Attrito Statico** e **Attrito Cinetico** (o dinamico)? Quale dei due coefficienti ($\mu_s$ o $\mu_k$) è solitamente più alto e perché questo è importante per l'inizio del movimento?
    
- **D2:** Descrivere il funzionamento di un sistema **Molla-Smorzatore** (Spring-Damper). Quali sono le due forze che agiscono in opposizione e da quali parametri dipendono?
    

![Immagine di mass spring damper system diagram](https://encrypted-tbn2.gstatic.com/licensed-image?q=tbn:ANd9GcRMVStTwmYeGaJqsxSRlO4yzAn9FjJYrP69Un1-r-0WGCaqFw34e4pDcohZki8aUh5TCPtrdi7hXuw1qpUU2gxaVXvsVzf7att2Jcw6Yh0-0_tBbT8)



**Esercizio (Formule)**

- **D3:** Scrivere la legge di Hooke per una molla ideale.
    
    - Se la lunghezza a riposo è $L_{rest}$ e la lunghezza corrente è $L$, quanto vale la forza $F$?
        
- **D4:** Come viene modellata la forza di **Viscosità** in un fluido? (Scrivere la relazione tra forza viscosa e velocità).
    

**Approfondimenti**

- **D5:** In una simulazione fisica, perché l'uso di molle molto rigide (alto coefficiente $k$) può causare instabilità nell'integrazione numerica (es. con Eulero)?
    

---

### 3.2 Simulazione di Corpi Rigidi (Rigid Body Dynamics)

_Concetti chiave: Ciclo di simulazione, Vettore di Stato, Momento angolare._

**Domande Base e Discorsive (Dal file "Domande esame.pdf")**

- **D6:** Descrivere dettagliatamente il **Ciclo di Simulazione** di un corpo rigido. Quali sono i 4 passaggi logici che si ripetono ad ogni frame per aggiornare lo stato dell'oggetto?
    
    - _Output richiesto:_ Forze $\to$ Accelerazioni $\to$ Velocità $\to$ Posizione.
        
- **D7:** Qual è la differenza tra la simulazione di una **Particella** (punto materiale) e quella di un **Corpo Rigido**? Quali proprietà aggiuntive possiede il corpo rigido nel suo vettore di stato $S(t)$?
    

**Esercizio**

- **D8:** Se applichiamo una forza a un corpo rigido _non_ allineata con il suo centro di massa, quali due tipi di accelerazione generiamo?
    

**Approfondimenti**

- **D9:** Cos'è la **Gestione delle Collisioni** tramite "Impulso"? Cosa succede alla velocità di un oggetto quando urta contro un piano (considerando un coefficiente di restituzione $\epsilon$)?
    

---

### 3.3 Modelli Gerarchici e Cinematica Diretta (FK)

_Concetti chiave: Grafo di scena, Relazioni Padre-Figlio, Trasformazioni locali/globali._

**Domande Base e Discorsive**

- **D10:** Spiegare il concetto di **Cinematica Diretta** (Forward Kinematics) applicata a una struttura articolata (es. un braccio robotico). Qual è l'input (cosa controlla l'animatore) e qual è l'output?
    
- **D11:** Come vengono rappresentati i modelli gerarchici in memoria? Descrivere la struttura ad albero con nodi (corpi) e archi (giunti).
    

**Esercizio**

- **D12:** In una gerarchia composta da _Spalla_ $\to$ _Gomito_ $\to$ _Polso_, se ruoto la spalla di 45°, cosa succede alla posizione del polso nello spazio globale? E alla posizione del polso relativa al gomito?
    

**Approfondimenti**

- **D13:** Qual è il limite principale della Cinematica Diretta quando si vuole che la mano di un personaggio raggiunga ed afferri un oggetto specifico nello spazio? (Introduzione al problema della _Cinematica Inversa_).
    

---

### 3.4 Figure Articolate e Animazione Facciale

_Concetti chiave: Walk Cycle, Muscoli lineari/sfinteri, Attenuazione._

**Domande Base e Discorsive**

- **D14:** Quali sono i parametri principali che un animatore definisce per creare un **Walk Cycle** (ciclo di camminata) procedurale? (Es. Lunghezza passo, rotazione anca...).
    
- **D15:** Nell'animazione facciale basata sulla fisica, come vengono modellati i **muscoli**?
    
    - Distinguere tra muscoli lineari e muscoli sfinteri (radiali).
        

**Esercizio**

- **D16:** Descrivere l'algoritmo di deformazione della pelle (mesh) causata da un muscolo facciale.
    
    - Come varia lo spostamento di un vertice della pelle in funzione della sua distanza dal punto di inserzione del muscolo? (Concetto di _Zone of Influence_ o cono di influenza).
        

**Approfondimenti**

- **D17:** Perché l'animazione facciale anatomica (muscoli + pelle + ossa) è considerata computazionalmente più costosa rispetto al semplice _Morphing_ o _Blend Shapes_?