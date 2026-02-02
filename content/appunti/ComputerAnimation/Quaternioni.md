
I quaternioni sono un'estensione dei numeri complessi utilizzata per calcolare rotazioni nello spazio 3D in modo efficiente e stabile.

## Struttura

Un quaternione è composto da 4 numeri (uno scalare e un vettore 3D):  

## Perché si usano?

Rispetto ad altri metodi di rotazione, offrono vantaggi significativi:

### Vs. Angoli di Eulero

- **Nessun Gimbal Lock**: Evitano il problema della perdita di un grado di libertà quando due assi di rotazione si allineano.
- **Interpolazione migliore**: Permettono l'interpolazione sferica lineare (**SLERP**), essenziale per animazioni fluide tra due orientamenti.

### Vs. Matrici di Rotazione

- **Efficienza**: Richiedono meno memoria (4 float contro 9 o 16) e meno calcoli per essere combinati.
- **Stabilità**: È computazionalmente meno costoso correggere gli errori di arrotondamento (normalizzazione) in un quaternione rispetto a una matrice.