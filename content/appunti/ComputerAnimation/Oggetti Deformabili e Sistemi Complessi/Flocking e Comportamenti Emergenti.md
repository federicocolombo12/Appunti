
## 1. Introduzione: Dai Punti ai "Boids"
Il Flocking simula il comportamento aggregato di animali come uccelli, pesci o mandrie.
* **Oggetti Geometrici:** A differenza delle particelle puntiformi, qui abbiamo oggetti orientati (es. un uccello con un davanti e un dietro).
* **Quantità:** Molti oggetti, ma generalmente *meno* dei sistemi particellari (centinaia invece di decine di migliaia).
* **Consapevolezza (Awareness):** La differenza fondamentale. Ogni membro **percepisce** gli altri e reagisce.

---

## 2. La Percezione (Il "Cervello")
Ogni membro del gregge deve "vedere" il mondo per decidere come muoversi.

### Percezione Locale (Regole di Reynolds)
Ogni membro ha un **FOV (Field of View)** limitato. Non vede tutto il gregge, ma solo i vicini.
* **FOV Dinamico:** Spesso il campo visivo si restringe quando la velocità aumenta (effetto tunnel).
* **Peso dell'Importanza:** I vicini vengono valutati in base a distanza, prossimità e angolo (un uccello davanti a me conta più di uno dietro).

Le 3 Regole Locali Fondamentali:
1.  **Separation (Collision Avoidance):** "Evita di sbattere contro i vicini". Una forza repulsiva a corto raggio.
2.  **Cohesion (Stay Close):** "Stai vicino al gruppo". Una forza attrattiva verso la posizione media dei vicini.
3.  **Alignment (Velocity Matching):** "Vai dove vanno gli altri". Cerca di allineare il tuo vettore velocità a quello dei vicini (spesso posizionandosi dietro chi sta davanti per sfruttare la scia, o *drafting*).

### Percezione Globale (Opzionale)
Serve per guidare lo stormo verso un obiettivo comune o facilitare il controllo dell'animatore (anche se meno realistico biologicamente).
* **Migratory Urge:** Una forza costante che spinge tutti verso una destinazione.
* **Flock Centering:** Attrazione verso il centro geometrico dell'intero stormo.
* **Leadership:** Seguire un leader designato.

---

## 3. La Fisica del Volo (Il "Corpo")
Mentre le particelle cadono o fluttuano, i Boids spesso "volano". La fisica simula l'aerodinamica.
Le forze in gioco ($F = ma$) sono:
1.  **Forze Fisiche:**
    * **Thrust (Spinta):** Propulsione in avanti.
    * **Lift (Portanza):** Per restare in aria.
    * **Drag (Resistenza):** Attrito dell'aria.
    * **Gravity:** Peso.
2.  **Forze di Percezione:** I desideri del boid (es. "voglio allontanarmi da lui") vengono tradotti in vettori forza fittizi che agiscono sul corpo.
3.  **Forze Ambientali:** Vento, evitamento ostacoli statici.

---

## 4. Architettura di Controllo del Moto (Spiegazione Immagine Slide 17)

![[Pasted image 20260202192355.png]]
### Livello 1: Navigator Module (Strategia - "Cosa voglio fare?")
È il livello più alto, l'intelligenza.
* **Input:**
    * Posizioni dei vicini (per separazione/coesione/allineamento).
    * Ostacoli da evitare.
    * Urgenza migratoria (obiettivo globale).
* **Funzione:** Somma vettorialmente tutti questi "desideri". Ad esempio: *"Voglio andare a Nord (migrazione) ma devo spostarmi un po' a sinistra per non colpire l'albero"*.
* **Output:** Un vettore **Velocità Desiderata (Desired Velocity)**.

### Livello 2: Pilot Module (Tattica - "Come lo faccio?")
È il traduttore tra il desiderio e la fisica. Un aereo (o uccello) non può cambiare direzione istantaneamente; deve virare.
* **Input:**
    * La velocità attuale del membro.
    * La velocità desiderata (dal Navigator).
* **Funzione:** Calcola come ruotare e accelerare per allineare la velocità attuale a quella desiderata. Gestisce il *banking* (inclinazione in virata).
* **Output:** Forze e Momenti torcenti (accelerazioni lineari e angolari) da applicare.

### Livello 3: Flight Module (Esecuzione - "Fisica")
È il motore fisico puro (Solver).
* **Input:** Le forze calcolate dal Pilot + Gravità + Vento.
* **Funzione:** Integra le equazioni del moto (Eulero o simili).
* **Output:** La **Nuova Posizione** e il **Nuovo Orientamento** per il frame successivo.

---

## 5. Conclusioni: Flocking vs Particle Systems
Un confronto essenziale per l'esame:

| Caratteristica    | Particle System             | Flocking System             |
| :---------------- | :-------------------------- | :-------------------------- |
| **Numero Membri** | Altissimo (Migliaia)        | Medio/Alto (Centinaia)      |
| **Geometria**     | Punti/Sprite (Minimale)     | Oggetti orientati/Mesh      |
| **Intelligenza**  | Nessuna (Ignoranza totale)  | **Alta (Consapevolezza)**   |
| **Interazione**   | Nessuna (o solo collisioni) | Reazione ai vicini (Regole) |
| **Comportamento** | Deterministico/Caotico      | **Emergente**               |
| **Fisica**        | $F=ma$ base                 | Aerodinamica + Virate       |
