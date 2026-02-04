### 5.1 L-Systems (Sistemi di Lindenmayer)

_Concetti chiave: Assioma, Regole di produzione, Riscrittura di stringhe, Turtle Graphics._

**Domande Base e Discorsive**

- **D1:** Definire cos'è un **L-System**. Per quale scopo biologico è stato originariamente inventato da Aristid Lindenmayer?
    
- **D2:** Quali sono i tre componenti fondamentali che definiscono una grammatica L-System?
    
    - (Risposta attesa: Alfabeto, Assioma iniziale, Regole di produzione).
        
- **D3:** Come viene tradotta una stringa di caratteri generata da un L-System in un'immagine grafica? Spiegare il concetto di **Turtle Graphics** (interpretazione geometrica dei simboli).
    
    - _Esempio:_ Cosa fa solitamente il comando "F"? E i comandi "+" o "-"?
        

**Esercizio (Logica procedurale)**

- **D4:** Dato il seguente L-System semplice:
    
    - **Assioma:** $F$
        
    - **Regola:** $F \to F[-F]F[+F]$
        
    - Scrivere la stringa risultante dopo la **prima iterazione** ($n=1$).
        
    - _Nota:_ I simboli `[` e `]` solitamente indicano il _push_ e _pop_ dello stato (salva posizione/angolo e ripristina), permettendo la creazione di ramificazioni.
        

**Approfondimenti**

- **D5:** Qual è la differenza tra un L-System **Deterministico** e uno **Stocastico**? Perché la versione stocastica è preferibile per la modellazione di alberi e piante realistiche in computer graphics?
    

---

### 5.2 Geometria Frattale

_Concetti chiave: Auto-similarità, Dimensione frazionaria, Ricorsione._

**Domande Base e Discorsive**

- **D6:** Definire il concetto di **Auto-similarità** (Self-similarity) in un oggetto frattale. Fare un esempio di oggetto naturale che presenta questa caratteristica (es. felce, broccolo romanesco, coste).
    
- **D7:** Perché si usa il termine "Frattale" (dal latino _fractus_ = rotto/frazionato)? Cosa indica la **Dimensione Frattale** rispetto alla classica dimensione euclidea (1D, 2D, 3D)?
    

**Esercizio**

- **D8 (Concettuale):** Spiegare il **Paradosso della Costa** (o effetto Richardson).
    
    - Se misuro il perimetro di un'isola con un righello di 1 km e poi con un righello di 1 metro, quale misura sarà maggiore? Se l'oggetto è un frattale ideale, cosa succede alla lunghezza man mano che l'unità di misura tende a zero?
        

**Approfondimenti**

- **D9:** Oltre alle piante, quali altri fenomeni naturali possono essere simulati efficacemente tramite algoritmi frattali? (Es. Montagne, Nuvole, Fulmini).
    

---

### 5.3 Teoria del Caos e Attrattori

_Concetti chiave: Sensibilità alle condizioni iniziali, Effetto Farfalla, Attrattore di Lorenz._

**Domande Base e Discorsive**

- **D10:** Spiegare il concetto di **"Sensibilità alle condizioni iniziali"** (noto popolarmente come _Effetto Farfalla_).
    
    - Cosa implica questo per la prevedibilità a lungo termine di un sistema dinamico (come il meteo o una simulazione di fumo)?
        
- **D11:** Cos'è un **Attrattore** in un sistema dinamico?
    
    - Descrivere brevemente i tre attrattori "classici": Punto fisso, Ciclo limite, Toro.
        

**Esercizio**

- **D12:** Cos'è un **Attrattore Strano** (Strange Attractor) e come si differenzia dagli attrattori classici?
    
    - Fare riferimento all'**Attrattore di Lorenz** (la famosa forma a farfalla). Le traiettorie si intersecano mai o si ripetono mai esattamente?
        

**Approfondimenti**

- **D13:** Qual è il legame tra Caos e Frattali?
    
    - (Suggerimento: La struttura geometrica di un attrattore strano ha spesso una dimensione frattale).