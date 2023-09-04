# Getting started
## OpenGL
OpenGL è principalmente considerata un API che ci mette a disposizione un vasto insieme di funzioni che ci permettono di manipolare immagini e grafica. Tuttavia, OpenGL in sè non è effettivamente un API, ma piuttosto una specifica, sviluppata e mantenuta da www.khronos.org.

La specifica OpenGL detta esattamente quale debba essere l'output e il risultato di ogni funzione e cosa tale funzione debba fare. E' poi responsabilità degli sviluppatori che implementano questa specifica trovare una soluzione per come questa funzione debba operare.

Le versioni sviluppate di OpenGL sono libere di avere implementazioni differenti, purché il loro risultato sia come da specifica.

Le persone che sviluppano le librerie OpenGL sono solitamente i costruttori delle schede grafiche.

_- Quando vi è un bug nell'implementazione, solitamente aggiornare i driver della propria scheda video è una soluzione efficace._

### Core-profile vs Immediate mode
Tempo addietro, usare OpenGL era sinonimo di sviluppare in "immediate mode", che era un metodo semplice per disegnare grafiche.

Gli sviluppatori col tempo diventarono sempre più bramosi di maggiore flessibilità e le specifiche divennero di conseguenza più flessibili; gli sviluppatori ottennero più controllo delle loro grafiche.

La "immediate mode" è particolarmente facile da usare e capire, ma anche estremamente inefficiente. Per questa ragione la specifica iniziò a deprecare le funzionalità della "immediate mode" dalla versione 3.2, e inizò a spingere gli sviluppatori ad utilizzare invece la modalità di OpenGL "core-profile", che è una sezione della specifica di OpenGL che ha rimosso tutte le vecchie funzionalità deprecate.

Quando si usa il "core-profile" di OpenGL, questo ci obbliga ad usare pratiche moderne.

La "immediate mode" astraeva molto dalle effettive operazioni che OpenGL svolgeva, e mentre er facile da imparare, in questo modo era difficile capire come OpenGL effettivamente funzionasse. 
L'approccio moderno richiede allo sviluppatore di comprendere realmente OpenGL e la programmazione grafica, e, mentre è un po' difficile, garantisce una maggiore flessibilità, più efficienza e soprattutto una maggiore comprensione della programmazione grafica.

Tutte le versioni successive ad OpenGL 3.3 aggiungono utili feature extra senza cambiare i meccanismi principali; le nuove versioni introducono solamente nuovi metodo pià efficienti o utili per svolgere le stesse operazioni.

Tutti i concetti e le tecniche rimangono le stesse per tutte le versioni moderne di OpenGL, quindi è perfettamente valido imparare utilizzando la versione 3.3 per poi imparare eventualmente anche le nuove funzionalità.