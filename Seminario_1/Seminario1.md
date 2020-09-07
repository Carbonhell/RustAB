---
marp: true
backgroundColor: #3B2E2A
color: #C8C9DB
theme: gaia
---
<!-- _backgroundColor: #E1E1DB -->
<!-- _color: #262625 -->
<style type="text/css">
    img {
  	    background-color: transparent!important;
    }
    img[alt~="center"] {
      display: block;
      margin: 0 auto;
    }
    .language-rust {
      background-color: black!important;
    }
</style>


# Visualizzazione di simulazioni agent-based con Rust e Amethyst
By Francesco Foglia

[![width:200px](https://www.rust-lang.org/logos/rust-logo-256x256.png)](https://www.rust-lang.org/) [![width:200px](https://book.amethyst.rs/stable/images/amethyst_emblem.png)](https://amethyst.rs/)

---

# Cosa sono le simulazioni agent-based?
L'obiettivo delle simulazioni agent-based è di trovare delle caratteristiche (regole) del sistema interessato in base al comportamento delle singole unità (agenti) e come interagiscono fra di loro.

---

# Cos'è Rust?
### Rust è un linguaggio di programmazione di sistemi che si può esprimere attraverso tre keyword:
- Performance
- Reliability
- Productivity

---

## Performance
Comparabile a C. Un programma scritto con Rust è già molto performante di base rispetto a programmi scritti in altri linguaggi, come Java o Go, grazie alla mancanza di un garbage collector e alle varie ottimizzazioni eseguite dal potente compiler

---

## Reliability
Varie classi di bug vengono rimosse al compile-time (la gestione della memoria permette, inoltre, la "fearless concurrency")

---

## Productivity
Può sembrare difficile da usare, ma in realtà il linguaggio collabora con noi tramite errori ben definiti, una documentazione robusta e una community amichevole, assieme a Cargo, un package manager versatile e facile da usare

---

# Rust e Game Dev
La situazione attuale di Rust nell'ambiente del game dev si può esprimere tramite la [citazione](https://arewegameyet.rs/):
## "Almost. We have the blocks, bring your own glue."
I "blocchi", ovvero le componenti, esistono e coprono la maggior parte dei bisogni di un developer di videogiochi.

---

![center](rust_gamedev_blocks.png)

---

# Cos'è Amethyst?
### Amethyst è un tentativo nel creare questa "colla", un game engine che ha scelto come sue fondamenta performance, modularità e un approccio orientato ai dati

---

## Performance
La scelta di un modello entità-componenti (ECS) come base della logica del gioco rende possibile parallelizzare gran parte di ciò che succede "dietro il sipario", permettendo allo sviluppatore di preoccuparsi di meno del parallelismo

---

## Modularità
I vari moduli usati da Amethyst (rendy come renderer, basato su gfx-hal, rodio per la parte audio...) sono facilmente scambiabili con alternative facilmente integrabili

---

## Data-oriented
Le entità del gioco sono semplicemente contenitori di dati, le operazioni sono relative a determinati tipi di dati (esempio: spostare una entità da un punto ad un altro) e non hanno bisogno dell'intera mole di dati rispettiva all'entità (la cache viene riempita solo con i dati che vogliamo effettivamente trattare)

---

# Data-driven
Il gioco viene strutturato in modo da lasciar decidere ai dati (per esempio, delle singole istanze di una particolare entità)

---
<!-- Viral16/07/2020
I didn't create a list of rendy mistakes.
In short, rendy has terrible usability.
It inherits generic parameter from gfx instead, while it should hide it.
It has very-high level abstraction - frame-graph. And very low-level in every other aspect.
Focusing on the frame-graph was a mistake as well. It should be natural abstraction over graphics API. But in rendy API usability was sacrificed for frame-graph.
I had had no experience writing graphics applications (not an expert today, but hey, at least I've made a few simple ones) and frame-graph turned out half-broken. And so we created another abstraction over frame-graph in amethyst_rendy. Which still puzzles amethyst users.
As a result almost no one wants to touch rendering in amethyst.-->
# Struttura di Amethyst
- Amethyst_animation
- Amethyst_assets (da rimuovere in favore di [atelier-assets](https://github.com/amethyst/atelier-assets))
- Amethyst_audio ([Rodio](https://github.com/RustAudio/rodio))
- Amethyst_input ([Winit](https://github.com/rust-windowing/winit))
- Amethyst_gltf ([glTF](https://github.com/gltf-rs/gltf)(3d))
- Amethyst_network ([laminar](https://github.com/amethyst/laminar))
- Amethyst_rendy ([rendy](https://github.com/amethyst/rendy), astrazione di gfx-hal, punto debole)
- Amethyst_ui (Capacità UI estremamente basiche)
- Amethyst_core ([Specs](https://github.com/amethyst/specs), da scambiare con [Legion](https://github.com/TomGillen/legion))

---
<!-- 
- State design pattern (basato su un automa a pila))
- ECS (entità le cui caratteristiche dipendono dalle componenti che le compongono)
- Resources (container di dati indipendenti)
- World (container di resources)
- System (contenente la logica del gioco, eseguiti in parallelo se possibile)
- Dispatcher (praticamente gli scheduler dei systems. Rispettano le regole della gestione della memoria di rust, massimizzando il parallelismo)
- Event channel (coda di eventi broadcast per la comunicazione tra sistemi di tipo 1-N)
 -->
# Concetti di Amethyst
- State design pattern
- ECS
- Resources
- World
- System
- Dispatcher
- Event channel

---

# Caratteristiche comuni con Unity
- Entrambi gli engines sono basati su un modello ECS, anche se nel dettaglio vengono gestiti in modo diverso (Unity: funzionalità implementate dal singolo componente, MonoBehaviour) (Amethyst: funzionalità implementare su una categoria di componenti, System)
- Prefabs (Amethyst: structs che implementano il trait PrefabData, le singole istanze sono ron files che non hanno bisogno di ricompilazione)

---

- Unity forza le proprie regole sullo sviluppatore, il quale può specificare la logica del gioco o altro, ma non può scendere nei dettagli, poiché è un game engine closed source.
Amethyst permette all'utente di cambiare i pezzi che lo compongono, dando più potere ad esso, ma da ciò derivano anche più responsabilità e più pericoli.

---

![center](philosophy_game_engine_engine.png)

---

# Estensioni: ImGUI bindings
La crate dedicata all'UI è molto basilare, per questo motivo è stata realizzata un'altra crate, amethyst_imgui, per poter creare un UI basato su Dear ImGUI (C/C++), con dei binding appositi per rendere safe l'utilizzo.
## Perché Dear ImGUI?
Le caratteristiche di questa libreria GUI sono: velocità, portabilità e autosufficienza (bloat-free), assieme ad uno sviluppo veloce.

---

![height:620px center](https://raw.githubusercontent.com/wiki/ocornut/imgui/web/v167/v167-misc.png)

---

# Estensioni: specs_physics
Una delle componenti considerate fondamentali nei game engine odierni è il motore fisico. Amethyst non ne ha uno di base, ma esiste questa crate che adatta il motore fisico più popolare e non basato su bindings, nphysics https://nphysics.org/

---

# Estensioni: Georust
Nei framework moderni per le simulazioni ad agenti, è presente il supporto ai dati geospaziali (GIS data) per caricare l'ambiente nel quale avviene la simulazione. In Rust, esiste il supporto per le primitive geospaziali (punti, linee, poligoni), assieme ad altre librerie di supporto, ma non esiste una libreria per la visualizzazione di tali dati.

---

# Perché visualizzare queste simulazioni?
L'obiettivo delle simulazioni è quello di ricreare un sistema complesso a partire da singole unità (agenti). Tramite la visualizzazione di tale simulazione, possiamo osservare una valida riproduzione di tale sistema, e notare particolarità dell'intero sistema o di gruppi di agenti.

---

Inoltre, la visualizzazione rende anche la simulazione più semplice da capire, quindi più effettiva, associando alle sue componenti dei simboli con un forte significato.

![width:400px](masonflockers.gif) ![width:400px](masonantforaging.gif)

---

# Strumenti attualmente esistenti
- MASON:
  - Java (Core / Models)
  - MVC
  - Single core, multiple threads
- NetLogo:
  - Scala->Java (Core); NetLogo (Models)
  - Single core, single thread

---
COMPLETARE?
# Vantaggi rispetto ai framework esistenti

RustAB, grazie al parallelismo offerto da Rust e Amethyst, può sfruttare al massimo i vari core a disposizione.


---

# Pro e contro di Amethyst
\+ Engine altamente parallelo e performante grazie a Specs e Rendy, a sua volta basato su gfx-hal, una API molto simile a Vulkan che permette di usare come backend tutte le piattaforme più importanti

\+ Free e open-source (MIT/Apache 2.0)

\+ Supporto al 2D e al 3D, a glTF e al caricamento di asset in parallelo.

---

\- La parte grafica è alquanto limitata, in particolare per la visualizzazione di dati tramite grafici. ImGUI permette la creazione di soli due tipi di grafici: istogrammi e a linee, quindi potrebbe essere necessario l'utilizzo di una libreria dedicata, come [Plotters](https://github.com/38/plotters).

\- La visualizzazione di dati GIS va fatta manualmente e la parte di grafica 2D è limitata al semplice rendering di sprites. Disegnare linee o altre primitive in modo dinamico sembra possibile solo tramite un componente di debug o attraverso amethyst-rendy, ovvero direttamente tramite le API del renderer.

---

\- Mancanza di features fondamentali e instabilità di quelle esistenti, come ad esempio lo switch del renderer (che, a cose fatte, sembra non essere stata una decisione giusta) che ha quasi fermato il progetto, o l'inattività di crates fondamentali come quella dedicata alla fisica, oltre al recente cambiamento della crate alla base dell'ECS (Specs->Legion)

\- L'unico adattatore per un motore fisico esistente (specs-physics) sembra essere inattivo, la causa potrebbe essere lo switch dell'ECS. (Ultimo commit: 4 maggio, dipendenza nphysics vecchia di 3 versioni)

\- Ancora molto lavoro da fare per arrivare ad una versione realmente competitiva con gli engine attuali

---

# Esempi di giochi basati su Amethyst
#### Space Menace https://github.com/amethyst/space-menace
![](https://github.com/amethyst/space-menace/raw/master/demo.gif)

---

#### Dwarf seeks fortune
![](https://github.com/amethyst/dwarf_seeks_fortune/raw/master/docs/screenshots/gameplay.gif)

---

Esempi di codice rust-amethyst per la creazione di gadget grafici basilari (bottoni etc, la lista in info.txt)

# Esempi di codice
##### "Hello world" di Amethyst
```rust
let app_root = application_root_dir()?;
let resources = app_root.join("resources");
let game_data = GameDataBuilder::default();
let mut game = Application::new(resources, state::MyState, game_data)?;
game.run();
```

---

# Esempi di codice
##### Stato
```rust
pub struct MyState;
impl SimpleState for MyState {}
```

---

# Altri game engines in Rust
Purtroppo, in ambito game dev siamo ancora agli stadi iniziali. Però, ci sono molti tentativi atti a produrre un valido game engine che può contrastare i giganti quali Unity e Unreal Engine.

---

# Bevy Engine
In un certo senso è uno spin-off di Amethyst, molto recente (10 agosto 2020) e con obiettivi specifici che possono essere riassunti in due parole: user experience. Si differenzia per la scelta di ottimizzare i tempi di compilazione (famoso punto debole di Rust), l'uso di un renderer diverso (Wgpu, molto più semplice di Rendy) e un focus sull'UI (l'obiettivo è di unificare UI dei giochi con l'UI dell'editor). Attualmente gli sviluppatori di Bevy collaborano con quelli di Amethyst fortunatamente.

---



---

# Rust AB Visualization

Lo sviluppo del framework atto alla visualizzazione segue un processo simile a quello implementato da MASON, adattandolo però all'ambiente basato su entità e componenti.

---

# Architettura

L'idea di base è quella di usare questo framework come plug-in con una interfaccia ben definita per gestire i vari tipi di eventi della simulazione (step, interazione fra agenti...)

---

# Requisiti lato modellatore
Lo sviluppatore del modello aggiunge delle specifiche chiamate nel codice del suo modello, specificando in che modo le varie componenti vanno visualizzate. Inoltre, la comunicazione fra simulazione e visualizzazione è bidirezionale ma ben separata: tramite un UI, deve essere possibile lanciare degli eventi con dati specifici per aggiornare i dati della simulazione.

---

# Esempi esistenti di simulazioni agent based implementate in Rust

---

#### A/B Street (https://github.com/dabreegster/abstreet)

![](https://github.com/dabreegster/abstreet/raw/master/book/exploring_traffic.gif)

---

# A/B Street: peculiarità
- Gioco realizzato per permettere a chiunque di risolvere problemi relativi al traffico di Seattle
- Chiaro esempio di una simulazione agent-based sviluppata in Rust, altamente parallela e di ottima qualità
- Possibile modello da seguire per un framework generalizzato che permette la simulazione anche di modelli di altre categorie
- Mappa creata a partire da dati GIS (quindi una simile integrazione è possibile, ma non semplice)

---
DA STUDIARE
# Rust agent based models
https://github.com/facorread/rust-agent-based-models

---

# Obiettivi futuri
# Breve termine
- Implementazione iniziale POC tramite le librerie scelte per visualizzare una semplice simulazione (Flockers)

- Creazione di un semplice inspector per le operazioni base sulla simulazione (start/stop/pause, modifica attributi di agenti, modifica attributi globali)

---

# Medio termine
?

---

# Lungo termine
- Creazione di adapter specifici per framework esistenti (MASON, NetLogo) per permettere la visualizzazione di modelli già esistenti e creati ad-hoc per questi framework (tramite JNA: https://github.com/java-native-access/jna, https://github.com/drrb/java-rust-example)

--- 

# Bibliografia:
- NetLogo itself: Wilensky, U. 1999. NetLogo. http://ccl.northwestern.edu/netlogo/. Center for Connected Learning and Computer-Based Modeling, Northwestern University. Evanston, IL.
- Amethyst: https://amethyst.rs/ (Questa presentazione non è affiliata in alcun modo con la Fondazione Amethyst o con qualsiasi prodotto relativo ad essi)