
Analizziamo ora il caso più elementare di collisione e risposta cinematica: una particella puntiforme che colpisce un piano fisso.

## Il Problema
Immaginiamo una particella che si muove con velocità costante verso un muro (piano). Dobbiamo:
1.  Capire **se** e **quando** lo attraversa.
2.  Calcolare la nuova velocità per farla **rimbalzare**.

## 1. Definizione Matematica

### L'Equazione del Piano
![[Pasted image 20260201131433.png]]
Un piano nello spazio è definito da un punto e una normale, oppure dall'equazione implicita:
$$E(p) = ax + by + cz + d = 0$$
Dove $(a,b,c)$ sono le componenti del vettore normale $N$ al piano.
* Se $E(p) > 0$: Il punto $p$ è "davanti" al piano.
* Se $E(p) = 0$: Il punto $p$ è esattamente sul piano.
* Se $E(p) < 0$: Il punto $p$ è "dietro" al piano.

### L'Aggiornamento della Particella
La particella si muove nel tempo secondo passi discreti ($t_i$). La sua posizione è aggiornata in base alla velocità media ($v_{ave}$):
$$p(t_i) = p(t_{i-1}) + \Delta t \cdot v_{ave}$$

## 2. Rilevamento dell'Intersezione (Detection)
Ad ogni passo della simulazione ($t_i$), valutiamo l'equazione del piano $E(p(t_i))$.
* Finché $E(p(t_i)) > 0$, la particella è ancora in volo davanti al muro.
* Nel momento in cui $E(p(t_i)) \le 0$, è avvenuta una collisione (la particella ha attraversato o toccato il piano) nell'intervallo di tempo tra $t_{i-1}$ e $t_i$.

## 3. Risposta Cinematica: Il Rimbalzo
![[Pasted image 20260201131511.png]]
Una volta rilevata la collisione, dobbiamo aggiornare la velocità per simulare il rimbalzo.
La logica vettoriale è la seguente:
1.  Scomponiamo la velocità $v$ in due componenti:
    * **Normale:** Perpendicolare al piano.
    * **Tangenziale:** Parallela al piano.
2.  Per ottenere un rimbalzo perfetto, dobbiamo **invertire** la componente normale della velocità, lasciando inalterata quella tangenziale.

**Formula di Riflessione:**
La nuova velocità $v'$ si calcola sottraendo due volte la proiezione della velocità sulla normale:
$$v' = v - 2(v \cdot N)N$$

## 4. Smorzamento (Damping)
Nella realtà, nessun rimbalzo è perfettamente elastico (l'energia si dissipa).
Per simulare questo effetto, introduciamo un coefficiente di smorzamento $k$ (con $0 < k < 1$).
La formula modificata diventa:

$$v' = v - (1 + k)(v \cdot N)N$$

* Se $k=1$: Rimbalzo perfettamente elastico (nessuna perdita di energia).
* Se $k \to 0$: Rimbalzo anelastico (la pallina tende a fermarsi contro il muro, "smorzandosi").

> **Nota:** Questo approccio è puramente cinematico (basato sul movimento) e produce risultati visivamente plausibili, specialmente per oggetti sferici, pur non calcolando le forze fisiche reali (massa, momento, ecc.).
> 