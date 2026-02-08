Gli oggetti sono rappresentazioni digitali definite tramite primitive geometriche fornite dall'API.

- **Geometria**: Si usano insiemi ridotti di primitive (punti, spezzate, poligoni, mesh) per approssimare anche forme complesse.
- **Attributi Pittorici**: Definiscono l'aspetto tramite colore, spessore, riempimento e trama (texture).

## Composizione della Scena

Per definire una scena si utilizzano strumenti di posizionamento e trasformazione:

- **Trasformazioni geometriche**: Ogni oggetto è definito in un proprio sistema di coordinate e trasportato nel sistema della scena tramite un cambio di coordinate.
- **Istanze**: Un oggetto può essere riutilizzato più volte in posizioni diverse.
- **Primitive Raster**: Elementi come pixel e bitmap (per sfondi o testo). A differenza delle primitive geometriche, sono intrinsecamente "piatte" e restano sempre perpendicolari alla direzione di vista.