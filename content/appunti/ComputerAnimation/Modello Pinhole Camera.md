## Modello Pinhole Camera

Il modello della **Pinhole Camera** (o camera a foro stenopeico) rappresenta il concetto fondamentale per la comprensione di come un **osservatore virtuale** percepisce una scena 3D e la proietta su un piano 2D. È la base teorica della [[Proiezione Prospettica]].

### Concetto Fondamentale

Il processo di formazione dell'immagine è simulato da una scatola chiusa con un piccolo foro (il **pinhole**) di diametro infinitesimo sul davanti.

- I raggi di luce provenienti dagli oggetti entrano dal foro e colpiscono il fondo della camera, chiamato **piano della pellicola**.
![[Screenshot 2026-02-05 alle 10.52.35.png]]
    
- Il pinhole funge da **Centro di Proiezione (COP)**.
    
- L'immagine risultante sul fondo della camera è intrinsecamente **capovolta**.
    

### Modellazione Matematica

Per determinare la posizione di un punto proiettato (xp​,yp​) a partire da un punto nello spazio (x,y,z), si utilizzano le proprietà dei triangoli simili:

- Sia **d** la **distanza focale** (la distanza tra il foro e il piano della pellicola).
    
- La relazione matematica per la coordinata y è:
    
    yp​=−z/dy​
    

### Dal Modello Fisico all'Osservatore Virtuale

Sebbene nella realtà l'immagine sia dietro il foro e capovolta, nella Computer Graphics per comodità si utilizza una **convenzione virtuale**:

1. Si posiziona un **piano di vista virtuale** a distanza _d_ **davanti** al centro di proiezione.
    
2. In questo modo, l'immagine proiettata risulta **ben orientata** (non più capovolta).
    
3. Questo modello garantisce un **fuoco infinito** (tutti gli oggetti sono nitidi a prescindere dalla distanza) e una luminosità infinitesima.
    

Nelle applicazioni reali (fotocamere fisiche o occhio umano), il pinhole è sostituito da una **lente**, che introduce una [[Profondità di Campo]] limitata e possibili distorsioni.![[Screenshot 2026-01-30 alle 16.07.08.png|500]]

---

### Collegamento con la Pipeline di Rendering

Il modello pinhole viene implementato nello stadio del **Projector** della [[Pipeline di Rendering]]. Una volta che i vertici degli oggetti sono stati trasformati nel sistema di riferimento della camera (tramite il **Transformer**), il proiettore calcola la loro posizione 2D sul piano immagine utilizzando proprio queste formule prospettiche.

### Esempio Pratico: Software 3D (es. Blender)

In software come Blender, quando aggiungi una "Camera", stai definendo un'entità basata sul modello pinhole. Regolando il parametro della **Distanza Focale** (es. 35mm o 50mm), stai tecnicamente variando il rapporto tra la lunghezza della camera e il fondo, modificando così la porzione di scena inquadrata (il [[Field of View]]).
  
  