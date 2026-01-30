# Hardware e Pipeline Grafica

## 1. Architettura Hardware

Un sistema grafico moderno separa i compiti tra la CPU (processore centrale) e una scheda dedicata.

- **GPU (Graphics Processing Unit):** È un processore specializzato nel calcolo parallelo. A differenza della CPU, la GPU è progettata per eseguire simultaneamente migliaia di operazioni matematiche semplici (come le moltiplicazioni di matrici 4x4), essenziali per la grafica 3D.
    
- **Graphic Memory:** Memoria dedicata che ospita le texture, i vertici dei modelli e i dati necessari alla GPU.
    
- **[[Frame Buffer]]:** Una porzione di memoria dedicata alla memorizzazione dell'immagine come insieme di pixel.
    
    - **Risoluzione:** Numero di pixel (es. 1600×1200).
        
    - **Profondità (Bit Depth):** Quanti bit sono usati per ogni pixel (es. 8 bit per colore, 24/32 bit totali). Determina quanti colori diversi possiamo visualizzare.
        

---

## 3. La Pipeline Grafica (Architettura Software)

La pipeline è il processo logico che trasforma una scena 3D astratta in un'immagine 2D sul monitor. Si divide in quattro stadi principali:

### A. Transformer (Trasformatore)

Applica le **trasformazioni geometriche** ai vertici degli oggetti.

- Sposta gli oggetti nel mondo (World Space).
    
- Sposta gli oggetti nel sistema di riferimento della camera (Camera Space).
    
- _Vedi anche:_ [[Trasformazioni Geometriche]] e matrici 4x4.
    

### B. Clipper (Ritagliatore)

Si occupa di scartare tutto ciò che non è visibile.

- Se un oggetto è fuori dal "cono di vista" (Viewing Frustum), il Clipper lo elimina per non sprecare calcoli nelle fasi successive.
    

### C. Projector (Proiettore)

Trasforma le coordinate 3D in coordinate 2D.

- Utilizza il [[Modello Pinhole Camera]] per calcolare dove ogni punto 3D appare sul piano dell'immagine.
    

### D. Rasterizer (Rasterizzatore)

È la fase finale: trasforma i dati geometrici (punti, linee, poligoni) in **pixel** all'interno del [[Frame Buffer]].

- Qui avvengono le operazioni di colorazione e l'applicazione delle texture.
    

---

## 3. Ottimizzazione della Visualizzazione

Per evitare che l'utente veda l'immagine mentre viene "disegnata" (causando sfarfallio), si usa una tecnica chiamata:

- **Double Buffering:** Si usano due buffer. La GPU disegna sul _Back Buffer_ (nascosto), mentre il monitor legge dal _Front Buffer_. Quando il disegno è pronto, i due buffer vengono scambiati (Swap).