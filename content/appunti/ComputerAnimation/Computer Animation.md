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
- [[Frame di Frenet]], [[Smoothing con Kernel di Convoluzione]] 
- [[Tecniche di integrazione numerica (Quadratura Gaussiana,  Metodo di Eulero e Metodo di Runge-Kutta)]]

## 3. Cinematica e Dinamica (Corpi Rigidi)

_Passiamo dall'animazione manuale a quella assistita dalla fisica._
- Simulazione di Corpi Rigidi:
	- [[Gestione delle Collisioni.]] 
		
        
    - [[Collisione piano-particella e forze di Impulso]], [[Gravità, Attrito (Statico vs Cinetico), Viscosità e Sistema Molla-Smorzatore.]]
	    
    - [[Simulazione di un Corpo Rigido (Ciclo di Simulazione, Cinematica di un punto, Moto Circolare e Cinematica del corpo Rigido)]]
		
		
	    
     - [[Modelli Gerarchici e Strutture Articolate, Cinematica Diretta e Inversa]]: Muovere scheletri e gerarchie di oggetti.
		 
	- [[Figure Articolate (Braccia, Walk Cycle e Animazioni Facciali)]]


## 4. Oggetti Deformabili e Sistemi Complessi

_Oltre il corpo rigido: muscoli, tessuti e fenomeni naturali._

- [[Deformazione Geometrica]] e [[Morphing]]: Seed vertex, funzioni di attenuazione e Squash & Stretch.
    
- [[Sistemi Particellari]] e  [[Flocking e Comportamenti Emergenti]]:: Simulazione di fuoco, fumo, esplosioni, Algoritmi per stormi e folle (Regole di Reynolds)..
  
## 5. Modellazione Avanzata e Natura

_Tecniche specifiche per strutture organiche e frattali._

- [[L-Systems]]: Grammatiche formali per la crescita delle piante.
    
- [[Frattali e Caos]]: Attrattori strani e autosomiglianza nella natura.
    

## 6. Rendering, Immagini e Post-Produzione

_L'output finale dell'animazione._

- [[Analogico e Digitale]], [[Rappresentazione dei Colori]], [[Metodi di Compressione]] ([[Jpeg]] è il più importante e da ricordare)
    
- [[Pipeline di Rendering]]: Transformer, Clipper, Projector e Rasterizer.
    
- [[Compositing]]:
    
    - Alpha Blending e Z-Buffer.
        
    - Nodi di Blender (Input, Output, Intermediate).
        
    - Double Buffering per il real-time.