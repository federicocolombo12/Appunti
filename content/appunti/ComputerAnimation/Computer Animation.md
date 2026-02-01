# 📔 Indice Generale: Computer Animation

## 1. Fondamenti e Geometria della Visione

_In questa sezione inseriamo le basi su cui poggia tutto il rendering 3D._

- [[Hardware e Software Graphics]]: GPU, Frame Buffer e Pipeline Grafica.
    
- [[Modello Pinhole Camera]]: La geometria della proiezione e l'osservatore virtuale.
- [[Definizione Della Scena e Pipeline di Visualizzazione]]
- [[Trasformazioni Geometriche]]: Matrici 4x4, rotazioni, traslazioni e coordinate omogenee.
    
- [[Rappresentazione delle Curve]]:
    
    - Curve di Bezier e B-Spline.

## 2. Controllo del Moto e Animazione

_Come facciamo muovere gli oggetti nel tempo in modo fluido?_

- [[Percezione del Moto e Intro all'Animazione]]: Persistenza della visione, frame rate e motion blur.
    
- [[Lunghezza d'arco, Look Up Table e Interpolazione]]: Lunghezza d'arco e controllo della velocità lungo una curva.
- [[Frame di Frenet]], [[Smoothing con Kernel di Convoluzione]] e [[Tecniche di integrazione numerica]]


## 3. Cinematica e Dinamica (Corpi Rigidi)

_Passiamo dall'animazione manuale a quella assistita dalla fisica._

- [[Cinematica Diretta e Inversa]]: Muovere scheletri e gerarchie di oggetti.
    
- [[Simulazione di Corpi Rigidi]]:
    
    - Leggi di Newton applicate alla CG (F=ma).
        
    - Forze: Gravità, Attrito (Statico vs Cinetico), Viscosità.
        
    - Gestione delle Collisioni.
        

## 4. Oggetti Deformabili e Sistemi Complessi

_Oltre il corpo rigido: muscoli, tessuti e fenomeni naturali._

- [[Deformazione Geometrica]]: Seed vertex, funzioni di attenuazione e Squash & Stretch.
    
- [[Sistemi Molla-Smorzatore]]: Modellare tessuti e corpi soffici.
    
- [[Sistemi Particellari]]: Simulazione di fuoco, fumo, esplosioni.
    
- [[Flocking e Comportamenti Emergenti]]: Algoritmi per stormi e folle (Regole di Reynolds).
    

## 5. Modellazione Avanzata e Natura

_Tecniche specifiche per strutture organiche e frattali._

- [[L-Systems]]: Grammatiche formali per la crescita delle piante.
    
- [[Frattali e Caos]]: Attrattori strani e autosomiglianza nella natura.
    

## 6. Rendering, Immagini e Post-Produzione

_L'output finale dell'animazione._

- [[Formati Immagine e Video]]: Analogico vs Digitale, campionamento e quantizzazione.
    
- [[Pipeline di Rendering]]: Transformer, Clipper, Projector e Rasterizer.
    
- [[Compositing]]:
    
    - Alpha Blending e Z-Buffer.
        
    - Nodi di Blender (Input, Output, Intermediate).
        
    - Double Buffering per il real-time.