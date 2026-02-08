
In Computer Animation usiamo l'integrazione numerica per due scopi ben distinti. È fondamentale non confonderli all'esame.

### A. Integrazione Definita (Calcolo di un valore fisso)
* **Obiettivo:** Calcolare l'area sotto una curva nota o la lunghezza di un arco.
* **Problema:** Risolvere $\int_a^b f(x) dx$.
* **Tecnica Migliore:** **Quadratura Gaussiana**.
* **Utilizzo:** Calcolo della *Arc Length* per la riparametrizzazione delle curve (vedi lezioni precedenti).

### B. Integrazione di ODE (Equazioni Differenziali Ordinarie)
* **Obiettivo:** Simulare la fisica. Conosciamo lo stato attuale e le forze, vogliamo trovare lo stato futuro.
* **Problema:** Dato $\frac{dx}{dt} = f(x, t)$, trovare $x(t + \Delta t)$.
* **Tecnica Migliore:** **Runge-Kutta (RK4)**.
* **Utilizzo:** Motori fisici, simulazione di tessuti, particelle, collisioni.

---

## 1. Quadratura Gaussiana (Gaussian Quadrature)


È una tecnica per approssimare il valore di un integrale definito.

### Perché è speciale?
A differenza di metodi geometrici semplici (come la regola del trapezio o dei rettangoli) che campionano la funzione a intervalli regolari, la Quadratura Gaussiana sceglie **punti specifici (ottimali)** e pesi specifici per ottenere la massima precisione con il minor numero di campioni.

### La Formula
L'integrale viene approssimato come una somma pesata:

$$
\int_{-1}^{1} f(x) dx \approx \sum_{i=1}^{n} w_i f(x_i)
$$

Dove:
* $n$: Numero di punti di campionamento (sample points).
* $x_i$: I punti di valutazione (detti **Nodi**). Non sono scelti a caso, ma sono le radici dei **Polinomi di Legendre**.
* $w_i$: I **Pesi** associati a ciascun nodo.

> **Esempio:** Con soli **5 punti** di campionamento, la Quadratura Gaussiana ottiene una precisione che la regola del trapezio otterrebbe con centinaia di punti. È estremamente efficiente per calcolare la lunghezza delle curve in tempo reale.

---

## 3. Integrazione di ODE: Il Metodo di Eulero
Passiamo ora alla simulazione fisica (Initial Value Problem).
Vogliamo muovere un oggetto nel tempo.

* **Stato:** Posizione $x$, Velocità $v$.
* **Input:** Passo temporale $h$ (o $\Delta t$).

### Metodo di Eulero (Euler Integration)
![[Pasted image 20260201123802.png]]
È il metodo più semplice e intuitivo, basato sulla serie di Taylor troncata al primo ordine.
Proiettiamo lo stato futuro seguendo la tangente (derivata) attuale.

**Formule di aggiornamento:**
1.  **Nuova Posizione:** $x_{new} = x_{old} + v_{old} \cdot h$
2.  **Nuova Velocità:** $v_{new} = v_{old} + a_{old} \cdot h$ (dove $a = F/m$)

**Problemi di Eulero:**
1.  **Errore:** L'errore si accumula linearmente ($O(h)$). La soluzione diverge rapidamente dalla realtà.
2.  **Instabilità:** Tende ad aggiungere energia al sistema. Un pendolo simulato con Eulero non rallenta, ma oscilla sempre più forte fino a "esplodere" (spirale verso l'esterno).
3.  **Richiede passi piccolissimi:** Per essere stabile, $h$ deve essere minuscolo, rendendo la simulazione lenta.

---

## 4. Il Metodo di Runge-Kutta (RK4)


Per risolvere l'instabilità di Eulero, usiamo la famiglia di metodi Runge-Kutta. Lo standard *de facto* in Computer Graphics è il **RK4 (Quarto Ordine)**.
### Concetto Chiave
Invece di fidarsi ciecamente della pendenza all'inizio del passo (come Eulero), RK4 "sonda" la pendenza in **4 punti diversi** all'interno dell'intervallo temporale e ne fa una media ponderata.

**Definizione dei 4 coefficienti ($k$):**
Supponiamo che la nostra equazione differenziale sia $\frac{dx}{dt} = f(t, x)$.

1.  **$k_1$ (Pendenza Iniziale):**
    $$ k_1 = f(t_{old}, x_{old}) $$
    *È la pendenza esatta all'inizio dell'intervallo (identico a Eulero).*

2.  **$k_2$ (Stima a metà - A):**
    $$ k_2 = f(t_{old} + \frac{h}{2}, x_{old} + k_1 \frac{h}{2}) $$
    *Ci spostiamo a metà tempo ($h/2$) e stimiamo la posizione usando la pendenza $k_1$.*

3.  **$k_3$ (Stima a metà - B):**
    $$ k_3 = f(t_{old} + \frac{h}{2}, x_{old} + k_2 \frac{h}{2}) $$
    *Ci spostiamo di nuovo a metà tempo, ma questa volta stimiamo la posizione usando la pendenza $k_2$ appena trovata.*

4.  **$k_4$ (Stima Finale):**
    $$ k_4 = f(t_{old} + h, x_{old} + k_3 h) $$
    *Ci spostiamo alla fine dell'intervallo ($h$) stimando la posizione tramite l'ultima pendenza calcolata $k_3$.*

### Formula Finale
Il passo finale è una media pesata dove i punti centrali contano di più:
$$
x_{new} = x_{old} + \frac{h}{6} (k_1 + 2k_2 + 2k_3 + k_4)
$$

### Vantaggi
* **Precisione:** Errore dell'ordine di $O(h^4)$. Molto preciso.
* **Stabilità:** Può gestire passi temporali ($h$) molto più grandi senza esplodere.
* **Costo:** Richiede 4 valutazioni delle forze per frame (4 volte più lento di Eulero per singolo step), ma permette step molto più ampi, quindi alla fine è spesso più efficiente.

