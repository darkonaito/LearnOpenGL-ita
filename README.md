>Un ringraziamento speciale va a Tilo', benemenito editore.

# Getting started
## OpenGL
OpenGL è principalmente considerata un'API che ci mette a disposizione un vasto insieme di funzioni che ci permettono di manipolare immagini e grafica. Tuttavia, OpenGL in sè non è effettivamente un'API, ma piuttosto una specifica, sviluppata e mantenuta da www.khronos.org.

La specifica OpenGL detta esattamente quale debba essere l'output e il risultato di ogni funzione e cosa tale funzione debba fare. E' poi responsabilità degli sviluppatori che implementano questa specifica trovare una soluzione per come questa funzione debba operare.

Le versioni sviluppate di OpenGL sono libere di avere implementazioni differenti, purché il loro risultato sia come da specifica.

Le persone che sviluppano le librerie OpenGL sono solitamente i costruttori delle schede grafiche.

_- Quando vi è un bug nell'implementazione, solitamente aggiornare i driver della propria scheda video è una soluzione efficace._

### Core-profile vs Immediate mode
Tempo addietro, usare OpenGL era sinonimo di sviluppare in "immediate mode", che era un metodo semplice per disegnare grafiche.

Gli sviluppatori col tempo diventarono sempre più bramosi di maggiore flessibilità e le specifiche divennero di conseguenza più flessibili; gli sviluppatori ottennero più controllo delle loro grafiche.

La "immediate mode" è particolarmente facile da usare e capire, ma anche estremamente inefficiente. Per questa ragione la specifica iniziò a deprecare le funzionalità della "immediate mode" dalla versione 3.2, e inizò a spingere gli sviluppatori ad utilizzare invece la modalità di OpenGL "core-profile", che è una sezione della specifica di OpenGL che ha rimosso tutte le vecchie funzionalità deprecate.

Quando si usa il "core-profile" di OpenGL, si è obbligati ad utilizzare pratiche moderne.

La "immediate mode" astraeva molto dalle effettive operazioni che OpenGL svolgeva, e mentre era facile da imparare, in questo modo era difficile capire come OpenGL effettivamente funzionasse. 
L'approccio moderno richiede allo sviluppatore di comprendere realmente OpenGL e la programmazione grafica, e, mentre è un po' difficile, garantisce una maggiore flessibilità, più efficienza e soprattutto una maggiore comprensione della stessa.

Tutte le versioni successive ad OpenGL 3.3 aggiungono utili feature extra senza cambiare i meccanismi principali; le nuove versioni introducono solamente nuovi metodi più efficienti o utili per svolgere le stesse operazioni.

Tutti i concetti e le tecniche rimangono le stesse per tutte le versioni moderne di OpenGL, quindi è perfettamente valido imparare utilizzando la versione 3.3 per poi imparare eventualmente anche le nuove funzionalità.

### Extensions
Una grande feature di OpenGL è il suo supporto delle estensioni. Ogni volta che una compagnia grafica inventa una nuova tecnica o una nuova ottimizzazione importante per il rendering, queste vengono solitamente poste in estensioni implementate nei driver.
Se l'hardware su cui viene eseguita un'applicazione supporta tali estensioni, gli sviluppatori possono utilizzare le funzionalità offerte dall'estensione.

Quando un'estension è popolare o particolarmente utile è possibile che diventi parte di una futura versione di OpenGL.

Lo sviluppatore deve interrogare la macchina per vedere se l'estensione che ha intenzione di utilizzare è disponibile:
```cpp
if(GL_ARB_extension_name)
{
    // Do cool new and modern stuff supported by hardware
}
else
{
    // Extension not supported: do it the old way
}
```

Con la versione 3.3 di OpenGL raramente si necessita di estensioni.

### State machine
OpenGL è una grande "state machine": una collezione di variabili che definiscono come OpenGL debba operare sul momento. Lo stato di OpenGL è comunemente chiamato il "contesto" OpenGL. Quando usiamo OpenGL, spesso cambiamo il suo stato modificando delle opzioni, manipolando buffer e renderizziamo utilizzando il contesto corrente.

Lavorando con OpenGL ci troveremo davanti varie funzioni che cambiano il suo stato, ed altre che lo utilizzano.

### Objects
Le librerie OpenGL sono scritte in C e ciò permette loro di essere derivate in altri linguaggi.

Uno dei costrutti utilizzati in OpenGL sono gli oggetti: gli oggetti sono collezioni di opzioni che rappresentano una parte dello stato di OpenGL. Potremmo avere un oggetto rappresentante le impostazioni della finestra, e potremmo impostare la sua grandezza, quanti colori supporta eccetera.
```cpp
struct object_name {
    float option1;
    int option2;
    char[] name;
};
```

La cosa bella dell'usare tali oggetti è che possiamo definirne più di uno nella nostra applicazione, impostare le loro opzioni e, ogni volta che avviamo un'operazione che usufruisce dello stato OpenGL, abbiniamo l'oggetto con le impostazioni che preferiamo.

Ci sono per esempio oggetti che fungono da contenitori per dati di modelli 3D, e, ogni volta che vogliamo disegnare uno di questi, abbiniamo l'oggetto in questione.

## Hello Window
### Viewport

Prima di andare avanti, dobbiamo specificare a OpenGL la grandezza della finestra, così che sappia come mostrare i dati e le coordinate rispetto ad essa. Possiamo impostare tali dimensioni con la funzione ```glVewport```:
```cpp
glViewport(0, 0, 800, 600);
```
I primi due parametri indicano la posizione dell'angolo in basso a sinistra della finestra, mentre gli altri due impostano la larghezza e altezza di essa in pixel.

Possiamo impostare dimensioni minori di quelle della finestra: in questo caso, tutto il rendering di OpenGL sarebbe mostrato in una finestra più piccola e potremmo, per esempio, mostrare ulteriori elementi fuori dal viewport di OpenGL.

_- Dietro le scene, OpenGL usa i dati specificati con ```glViewport``` per trasformare le coordinate 2D in coordinate del tuo schermo._

### Double buffer

Quando un'applicazione disegna in un singolo buffer, l'immagine risultante potrebbe presentare problemi di flickering. Ciò è dovuto al fatto che l'immagine mostrata non è disegnata in un istante, ma a pixel per pixel (solitamente da sinistra a destra e dall'alto al basso). 

Visto che quest'immagine viene mostrata all'utente quando sta ancor venendo disegnata, il risultato potrebbe presentare artefatti.

Per evitare tale errore, si può ricorrere a un doppio buffer.

Il "front buffer" contiene l'immagine completa che viene mostrata nello schermo, mentre i comandi di rendering disegnano sul "back buffer". 

Non appena i comandi di rendering son finito, i due buffer vengono scambiati così l'immagine viene mostrata senza che venga ancora disegnata.

### Rendering

Per verificare che tutto funzioni, proviamo a ripulire lo schermo con un colore a nostra scelta.
Lo facciamo all'inizio del frame, così da cancellare tutti i risultati del frame precedente.

Possiamo pulire il "color buffer" dello schermo utilizzando ```glClear```, a cui passiamo i "buffer bits" per specificare che buffer vogliamo pulire. Per ora ci interessano solo i colori, quindi puliremo solo il buffer del colore.
```cpp
glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
glClear(GL_COLOR_BUFFER_BIT);
```
Notare che si specifica il colore con cui pulire il buffer utilizzando ```glClearColor```.
Ogni volta che chiamiamo ```glClear```, l'intero buffer verrà riempito col colore indicato da ```glClearColor```.

_- La funzione ```glClearColor``` è una funzione che imposta lo stato, mentre ```glClear``` è una funzione che usa lo stato._

## Hello Triangle
In OpenGL, tutto è nello spazio tridimensionale, ma lo schermo e la finestra sono array bidimensionali di pixel, per cui grande parte del compito di OpenGL è quello di trasformare tutte le coordinate tridimensionali in pixel bidimensionali che stiano sul tuo schermo.

La pipeline grafica prende come input una serie di coordinate 3D e le trasforma in pixel colorati sul tuo schermo. 
Questa può essere divisa in due grandi parti: la prima trasofrma le coordinate 3D in coordinate 2D, e la seconda trasforma queste coordinate 2D in pixel colorati.

La pipeline può essere divisa in più step, ognuno dei quali necessita dell'output della precedente come proprio input. Tutti questi step sono altamente specializzati, e possono facilmente essere svolti in parallelo.

Per via della loro natura parallela, le schede grafiche di oggi hanno migliaia di piccoli processori che trattano i dati dentro la pipeline. Questi processori eseguono piccoli programmi sulla GPU chiamati "shaders".

Alcune di queste shaders sono configurabili dallo sviluppatore, e ciò ci permette di scriverne di nostre, che rimpiazziono quelle di default. Ciò ci da molto più controllo sulle singolari parti della pipeline.

Le shaders son scritte nel OpenGL Shading Language, GLSL.
![Graphic Pipeline](https://imgur.com/W9mIk9D)

