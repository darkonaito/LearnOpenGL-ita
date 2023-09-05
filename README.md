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

Le parti in blu sono quelle che possiamo alterare:

![Graphic Pipeline](https://i.imgur.com/W9mIk9D.png)

Come input alla pipeline grafica passiamo una lista di coordinate 3D che formano un triangolo in un array, qua chiamato "Vertex Data"; è una collezione di vertici. Un vertice è una collezione di dati in coordinate 3D.

Il vertex data è rappresentato utilizzando "vertex attributes" che possono contenere ogni tipo di dato che vogliamo; tuttavia, per semplicità assumeremo che ogni vertice consiste in solo una posizione 3D e un valore di colore.

_- Per far saperere ad OpenGL cosa farne della tua collezione di coordinate e colori, è necessario dargli un suggerimento su che tipo di "render types" vuoi che formi con quei dati. Questi suggerimenti son chiamati "primitivi" e vengono dati ad OpenGL quando si usano i comandi per il disegno._

La prima parte della pipeline è la "vertex shader", che prende come imput un singolo vertice. Il suo scopo principale è quello di trasformare queste coordinate 3D in coordinate differenti; questa shader ci permette di effettuare alcune modifiche sui vertex attributes.

Lo stadio "primitive assembly" prende come input tutti i vertici dalla vertex shader che formano un primitivo e assembla tutti i punti in tale forma primitiva.

L'output del primitive assembly viene passato alla "geometry shader". Questa prende come input una collezione di vertici che formano un primitivo e ha l'abilità di di generare altre forme tramite la creaione di nuovi vertici.

L'output della geometry shader e poi passato allo stadio della rasterizzazione, che mappa i primitivi ai corrispondenti pixel sullo schermo, risultando in "fragments" utilizzabili dalla fragment shader. Prima che questa venga eseguita, viene performato del clipping, che scarta tutti i frammenti che sono fuori dallo spazio visivo, per aumentare la performance.

_- Un frammento in OpenGL è tutto ciò di cui esso ha bisogno per renderizzare un singolo pixel._

Lo scopo principale della fragment shader è quello di calcolare il colore finale di un pixel, e questo è solitamente lo stadio dove avvengono tutti gli effetti di OpenGL più avanzati. Spesso la fragment shader contiene dati sulla scena 3D che può utilizzare per calcolare il colore finale di un pixel (luci, ombre, colore della luce, etc).

Dopo che tutti i valori di colore son stati determinati, l'oggetto finale passa attraverso un ultimo stadio chiamato "alpha test" e "blending stage". Questo stadio controlla il valore "depth" e "stencil" del frammento e li utilizza poi per controllare se il frammento risultante è davanti o dietro altri oggetti e se quindi debba essere scartato  meno.

Questo stadio controlla anche i valori alpha, che definiscono l'opacità di un oggetto, e mescola gli oggetti conseguentemente. Il colore finale del pixel potrebbe essere completamente diverso quando si renderizzano più oggetti.

Come vedi, la pipeline è molto complessa e contiene molte parti configurabili. Per quasi tutti i casi, sarà necessario agire solamente sulla vertex e la fragment shaders. La shader geometrica è opzionale e spesso viene lasciata quella di default.

In OpenGL moderno siamo obbligati a definire almeno una vertex shader e una fragment shader, essendo che non ve ne sono di default. Per questo motivo è spesso difficile iniziare ad imparare OpenGL moderno, dato che è necessaria una certa conoscenza prima di esser in grado di renderizzare anche solo il primo triangolo.

### Vertex input

Prima di iniziare a disegnare qualcosa, dobbiamo prima fornire ad OpenGL dei dati di vertici. Essendo OpenGL una libreria grafica 3D, tutte le coordinate che specifichiamo sono tridimensionali (x, y, z).

OpenGL non trasforma tutte le tue coordinate 3D in pixel 2D sul tuo schermo: processa solamente coordinate 3D che siano in un range specifico, tra -1.0 e 1.0 su tutte e tre le assi. 
Tutte le coordinate entro questo che viene chiamato "normalized device coordinates range" finiranno sul tuo schermo.

Dato che vogliamo renderizzare un singolo triangolo, dobbiamo specificare tree vertici, ognuno con una coordinata 3D. Le definiamo in coordinate del dispositivo normalizzate in un array di ```float```:
```cpp
float vertices[] = {
    -0.5f, -0.5f, 0.0f,
     0.5f, -0.5f, 0.0f,
     0.0f,  0.5f, 0.0f
};
```
Dato che OpenGL funziona con coordinate 3D, renderizziamo un triangolo 2D ponendo ogni vertice con la coordinata ```z``` di 0.0. In questo modo la profondità del triangolo rimane la stessa, facendolo sembrare 2D.

- ***Normalized Device Coordinates (NDC)***
Una volta che le tue coordinate dei vertici son stati processati nella vertex shader, devono essere in coordinate del dispositivo normalizzate, che è un piccolo spazio dove i valori delle tre coordinate variano da -1.0 a 1.0. Qualsiasi coordinata che si trovi fuori da questo range verrà scartata e non sarà visibile sul tuo schermo.
L'asse delle y punta verso l'alto, e le coordinate (0, 0) sono al centro del grafico.
Le tue coordinate NDC saranno trasformate a coordinate "screen-space" tramite il "viewport transform" utilizzando i dati che hai fornito tramite ```glViewport```.

Questi dati dei vertici che abbiamo definito vogliamo adesso inviarli come input al primo processo della pipeline, la vertex shader. Ciò si attua creando della memoria sulla GPU dove immagazzinare i vertex data, configurando come OpenGL debba interpretare la memoria e come inviare i dati alla scheda grafica.

La vertex shader processerà tanti vertici quanti glie ne vengono indicati dalla sua memoria.

Gestiamo tale memoria tramita i "vertex buffer objects (VBO)", che possono memorizzare un grande numero di vertici nella memoria della GPU.

Il loro vantaggio è che ci permettono di inviare grandi gruppi di dati alla scheda grafica, anziché inviare un vertice alla volta.
Quello di inviare dati dalla CPU alla GPU è un processo relativamente lento, quindi cerchiamo di mandare più dati possibili in una volta sola.

Una volta che i dati sono nella memoria della scheda grafica, la vertex shader ha un accesso quasi istantaneo ai vertici, e ciò ci garantisce una velocità non da poco.

Come ogni oggetto in OpenGL, questo buffer ha un ID unico. Per crearne uno utilizziamo la funzione ```glGenBuffers```:
```cpp
unsigned int VBO;
glGenBuffers(1, &VBO);
```
OpenGL ha tanti tipi di buffer, e il tipo di buffer di un buffer di vertici è ```GL_ARRAY_BUFFER```.
OpenGL ci permette di abbinare diversi buffer in una volta, purché siano di tipo differente. 

Possiamo abbinare il buffer appena creato al target ```GL_ARRAY_BUFFER``` tramite la funzione ```glBindBuffer```:
```cpp
glBindBuffer(GL_ARRAY_BUFFER, VBO);
```
D'ora in poi, ogni chiamata a buffer che facciamo (riferita al target ```GL_ARRAY_BUFFER```) sarà usata per configurare il buffer attualmente abbinato, nel nostro caso VBO. Dopodiché, possiamo effettuare una chiamata alla funzione ```glBufferData```, che copia i dati dei vertici definiti precedentemente dentro la memoria del buffer.
```cpp
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
```
```glBufferData``` è una funzione che copia dei dati definiti dall'utente nel buffer momentaneamente abbinato.

Il suo primo argomento è il tipo di buffer dentro cui vogliamo copiare i dati; Il secondo argomento specifica la grandezza in byte dei dati; Il terzo parametro sono i dati veri e propri.

Il quarto parametro specifica come vogliamo che la scheda grafica tratti i dati ricevuti. Può accettare tre argomenti:
* ```GL_STREAM_DRAW``` : I dati sono impostati una sola volta e usati dalla GPU per poche volte.
* ```GL_STATIC_DRAW``` : I dati sono impostati una sola volta e usati tante volte.
* ```GL_DYNAMIC_DRAW```: I dati cambiano molto spesso e sono usati tante volte.

I dati riguardanti la posizioen del triangolo non cambiano, sono usati molti, e rimangono gli stessi per ogni chiamata di rendering, quindi il suo tipo di utilizzo dev'essere ```GL_STATIC_DRAW```.

Se, per esempio, avessimo un buffer con dati che tendono a cambiare spesso, un tipo di utilizzo ```GL_DYNAMIC_DRAW``` ci assicura che la scheda grafica ponga qui dati in un posto nella memoria che ne consenta un accesso veloce.

Per ora, abbiamo immagazzinato i dati dei vertici nella memoria della GPU, tramite un oggetto vertex buffer chiamato VBO. Come prossima cosa, vogliamo creare delle shaders che processino questi dati.

### Vertex shader

La prima cosa che dobbiamo fare è scrivere la vertex shader nel linguaggio GLSL, poi compilarla in modo tale da poterla utilizzare nella nostra applicazione. 

Qui sotto vi è il codice di una vertex shader estremamente basilare in GLSL:
```cpp
#version 330 core
layout (location = 0) in vec3 aPos;

void main()
{
g   l_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);
}
```
Come puoi notare, GLSL si assomiglia al C. Ogni shader inizia con una dichiarazione della propria versione; inoltre, specifichiamo esplicitamente che siamo intenzionati ad utilizzare le funzionalità core.

Successivamente, dichiariamo gli attributi dei vertici in input tramite la parola chiave ```in```.
Per adesso, ci interessano solamente i dati della posizione, quindi necessitiamo di un solo vertex attribute.

GLSL ha un tipo di dato "vector", che contiene dagli 1 ai 4 float, in base alla cifra suffissa.
Dato che ogni vertici ha una coordinata 3D, creiamo una variabile di input ```vec3``` con il nome ```aPos```. Specifichiamo inoltre la localizzazione della variabile di input tramite ```layout (location = 0)```, di cui capirai dopo lo scopo.

* Vettori: i vettori rappresentano posizioni e direzioni nello spazio, e hanno proprietà utili.
Un vettore in GLSL ha una grandezza massima di 4, e ognuno dei suoi valori può essere ottenuto tramite ```vec.x, vec.y, vec.z, vec.w```. Il valore vec.w non è utilizzato come una posizione, ma è utile per una cosa chiamata "perspective division", di cui si parlerà più in avanti.

La vertex shader in question è probabilmente la pià semplice che si possa immaginare, in quanto non abbiamo processato nessun dato; tutti i dati son stati reinviati come output della shader.

Nelle applicazioni reali, i dati in ingresso solitamente non son normalizzati, e vanno quindi prima trasformati in coordinate che cadano entro la regione visibile di OpenGL.

### Compiling a shader

Prendiamo il codice sorgente della shader e lo immagazziniamo in una stringa C costante.
```cpp
const char *vertexShaderSource = "#version 330 core\n"
    "layout (location = 0) in vec3 aPos;\n"
    "void main()\n"
    "{\n"
    " gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
    "}\0"
;
```
Per utilizzare questa shader, OpenGL deve compilarla dinamicamente durante l'esecuzione del programma.

La prima cosa che dobbiamo fare è creare un shader object, a cui ci riferisce tramite un ID. Salviamo la shader come un ```unsigned int``` e creiamo la shader con ```glCreateShader```:
```cpp
unsigned int vertexShader;
vertexShader = glCreateShader(GL_VERTEX_SHADER);
```
Forniamo il tipo di shader che vogliamo creare come argomento a ```glCreateShader```.
Successivamente colleghiamo il codice sorgente della shader all'oggetto shader e compiliamo:
```cpp
glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
glCompileShader(vertexShader);
```
La funzione ```glShaderSource``` richiede l'oggetto shader da compilare come primo argomento. Il secondo specifica quante stringe stiamo passando come codice sorgente, in questo caso una. Il terzo parametro è l'effettivo codice sorgente, e il quarto parametro possiamo lasciarlo a ```NULL```.

* Puoi verificare se e quali errori si son verificati durante la compilazione della shade in questo modo:
```cpp
int success;
char infoLog[512];
glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &success);
```
Se la compilazione non è andata a buon finte, poi ottenere il relativo messaggio di errore tramite ```glGetShaderInfoLog```:
```cpp
if(!success)
{
    glGetShaderInfoLog(vertexShader, 512, NULL, infoLog);
    std::cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n"
            << infoLog << std::endl;
}
```
### Fragment shader

Lo scopo focale della fragment shader è quello di calcolare il colore di output dei tuoi pixel.

* I colori nella grafica computerizzata son rappresentati come array di 4 valori: rosso, verde, blu e alfa (opacità), solitamente abbreviati come RGBA. Quando definiamo un colore in OpenGL o GLSL settiamo il valore di ogni componente da 0.0 a 1.0.
```cpp
#version 330 core
out vec4 FragColor;
void main()
{
    FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);
}
```
La fragment shader richiede una variabile di output soltanto, che consiste in un vettore di quattro elementi che rappresenta il colore finale del pixel. Possiamo dichiarare valori di output tramite la parola chiave ```out```, che qui abbiamo rinominato ```FragColor```.

### Shader program
Per usare le shader che abbiamo appena compilato, dobbiamo linkarle a un shader program object e poi attivarle quando renderizziamo oggetti. Le shader del shader program attivato verranno usate quando effettuermo chiamate di rendering.

Quando linkiamo le shader in un programma, l'output di ogni shader viene usato come input della prossima. Qua potresti ottenere errori di linking se gli output e input non corrispondono.

Creiamo un programma così:
```cpp
unsigned int shaderProgram;
shaderProgram = glCreateProgram();
```
La funzione ```glCreateProgram``` crea un programma e restituisce il suo ID. Adesso dobbiamo collegare le shader precedentemente compilate al program object e linkarle tramite ```glLinkProgram```:
```cpp
glAttachShader(shaderProgram, vertexShader);
glAttachShader(shaderProgram, fragmentShader);
glLinkProgram(shaderProgram);
```
Per controllare se il linking è andato a buon fine possiamo fare così:
```cpp
glGetProgramiv(shaderProgram, GL_LINK_STATUS, &success);
if(!success) {
    glGetProgramInfoLog(shaderProgram, 512, NULL, infoLog);
...
}
```
Il risultato è un oggetto programma che possiamo attivare chiamando ```glUseProgram```:
```cpp
glUseProgram(shaderProgram);
```
Non scordiamo di eliminare le shader una volta che le abbiamo linkate: non ci servono più!:
```cpp
glDeleteShader(vertexShader);
glDeleteShader(fragmentShader);
```
Per adesso, abbiamo inviato i dati dei vertex in input alla GPU, e le abbiamo spiegato come dovrebbe processare i dati dei vertici all'interno delle due shaders. Tuttavia, OpenGL ancora non sa come interpretare i dati dei vertici in memoria e come collegarli agli attributi della vertex shader. 

### Linking Vertex Attributes

La vertex shader ci permette di specificare qualsiasi input vogliamo sottoforma di vertex attributes, e mentre ciò ci dona grande flessibilitò, significa anche che dobbiamo manualmente specificare quali parti dei nostri dati in input vanno ad un certo vertex attribute nella shader. Ciò significa che dobbiamo specificare ad OpenGL come interpretare i dati dei vertici prima di renderizzare.

I nostri dati del buffer di buffer son formati in questo modo:

![](https://i.imgur.com/mM7k2pT.png)

* I dati sulla posizione son immagazzinati come valori a virgola mobila a 32 bit (4 byte).
* Ogni posizione è composta da tre di questi valori.
* Non ci son spazi tra i vari gruppi di valori, sono "tightly packed".
* Il primo dato si trova all'inizio del buffer.

Con queste conoscenze a disposizione, possiamo dire ad OpenGL come interpretare i dati dei vertici utilizzando la funzione ```glVertexAttribPointer```:
```cpp
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float),
                      (void*)0
);
glEnableVertexAttribArray(0);
```
* Il primo parametro specifica che vertex attribute vogliamo configurare. Abbiamo specificato la localizzazione dell'attributo ```position``` con ```layout (location = 0)```. Questo impìosta la posizione dell'attributo a 0 e, dato che vogliamo passare dati a questo attributo, passiamo come argomento 0.
* Il prossimo aromgento specifica la grandezza di ogni attributo.
* Il terzo argomento specifica il tipo di dato, in questo caso ```GL_FLOAT```.
* Il parametro successivo specifica se vogliamo che i dati vengano normalizzati. Nel nostro caso no, quindi lasciamo ```GL_FALSE```.
* Il quinto argomento è detto "stride" e indica quanto spazio c'è tra vertex attribute consecutivi. Dato che un gruppo di dati di posizioni è sempre posizionato 3 ```float``` dopo il precedente, forniamo tale valore, 3, come stride. In questo caso, essendo l'array strettamente impachettato, avremmo potuto specificare come stride 0, e lasciare fosse OpenGL a determinarlo in automatico.
* L'ultimo argomento specifica l'offset di dove i dati iniziano nel buffer. In questo caso non v'è offset alcuno.

_Ogni vertex attribute prende i suoi dati dalla memoria gestita da un VBO, e il VBO che viene utilizzato è quello attualmente abbinato a ```GL_ARRAY_BUFFER``` quando si chiama ```glVertexAttribPointer```_.

Adesso che abbiamo specificato come OpenGL debba interpretare i dati dei vertici, è necessario abilitarli utilizzando ```glEnableVertexAttribArray```, fornendogli come argomento la posizione del vertex attribute.

E' tutto pronto: abbiamo inizializzato i dati dei vertici in un buffer utilizzando un vertex buffer object, abbiamo messo su una shader vertex e una fragment e detto ad OpenGL di linkare i dati dei vertici agli attribuiti dei vertici della vertex shader.

Disegnare un oggetto, in OpenGL, funziona più o meno così:
```cpp
// 0. copy our vertices array in a buffer for OpenGL to use
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
// 1. then set the vertex attributes pointers
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float),
                      (void*)0
);
glEnableVertexAttribArray(0);
// 2. use our shader program when we want to render an object
glUseProgram(shaderProgram);
// 3. now draw the object
someOpenGLFunctionThatDrawsOurTriangle();
```
Dobbiamo ripetere questo processo ogni volta che vogliamo disegnare un oggetto. Abbinare i buffer objects e configurare tutti i vertex attributes per ognuno di questi oggetti diventa subito un processo tedioso.

### Vertex Array Object
Un vertex array object (VAO) può essere abbinato esattamente come un vertex buffer object, e ogni successiva chiamata a attributi dei vertici sarà memorizzata in quel VAO.

Questo ha il vantaggio che quando si configurano dei vertex buffer pointers è necessario effettuare tali chiamate una sola volta e, quando si vuole disegnare l'oggetto, si può semplicemente abbinare il VAO corrispondente. Ciò rende molto facile passare da una configurazione all'altra.

_OpenGL core ci obbliga ad utilizzare un VAO, così che sappia che fare dei nostri vertici in input._

Un vertex array memorizza le seguenti cose:
* Chiamate a ```glEnableVertexAttribArray``` o a ```glDisableVertexAttribArray```.
* Configurazioni di attributi di vertici tramite ```glVertexAttribPointer```.
* Vertex buffer objects associati a attributi dei vertici tramite chiamate a ```glVertexAttribPointer```.

![](https://i.imgur.com/ctfftrX.png)

```cpp
unsigned int VAO;
glGenVertexArrays(1, &VAO);
```
Per usare un VAO tutto ciò che si deve fare è abbinarlo utilizzando ```glBindVertexArray```. Da lì in poi si devono abbinare/configurare i VBO e attribute pointer corrispondenti e poi dis-abbinare il VAO per un utilizzo successivo.

Appena vogliamo disegnare un oggetto, semplicemente abbiniamo il VAO con le impostazioni preferite prima di disegnarlo.
```cpp
// ..:: Initialization code (done once (unless your object frequently changes)) :: ..
// 1. bind Vertex Array Object
glBindVertexArray(VAO);
// 2. copy our vertices array in a buffer for OpenGL to use
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
// 3. then set our vertex attributes pointers
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof     (float), (void*)0)
;
glEnableVertexAttribArray(0);
[...]
// ..:: Drawing code (in render loop) :: ..
// 4. draw the object
glUseProgram(shaderProgram);
glBindVertexArray(VAO);
someOpenGLFunctionThatDrawsOurTriangle();
```
Solitamente, quando si vogliono disegnare più oggetti, per prima cosa si generano e configurano tutti i VAO, e li si memorizza per un utilizzo successivo. Quando vogliamo disegnare uno di quelli oggetti, prendiamo il VAO corrispondente, lo abbiniamo, disegnamo l'oggetto e poi dis-abbiniamo il VAO nuovamente.

### The triangle we've all been waiting for

Per disegnare un oggetto a nostra scelta, OpenGL ci fornisce la funzione ```glDrawArrays```, che disegna primitivi utilizzando la sheder attualmente attiva, la configurazione di vertex attribute precedentemente definita e i dati dei vertici del VBO.
```cpp
glUseProgram(shaderProgram);
glBindVertexArray(VAO);
glDrawArrays(GL_TRIANGLES, 0, 3);
```
La funzione ```glDrawArrays``` prende come primo argomento il tipo di primitivo che vogliamo disegnare. Il secondo argomento specifica l'indice di inizio dell'array di vertici che vogliamo disegnare - lo impostiamo a zero. L'ultimo argomento specifica quanti vertici vogliamo disegnare - in questo caso 3.

![](https://i.imgur.com/6WaoYcd.png)

### Element Buffer Objects
Un'ultima cosa di cui è necessario parlare quando si tratta di renderizzare vertici è l'oggetto buffer degli elementi, EBO.

Immagina di voler disegnare un rettangolo invece che un triangolo. Possiamo disegnarlo utilizzando due triangoli. Si generano i seguenti vertici:
```cpp
float vertices[] = {
    // first triangle
     0.5f, 0.5f, 0.0f, // top right
     0.5f, -0.5f, 0.0f, // bottom right
    -0.5f, 0.5f, 0.0f, // top left
    // second triangle
     0.5f, -0.5f, 0.0f, // bottom right
    -0.5f, -0.5f, 0.0f, // bottom left
    -0.5f,  0.5f, 0.0f // top left
};
```
Come puoi vedere, ci sono delle ripetizioni nei vertici che abbiamo specificato. 
Lo stesso rettangolo si potrebbe specificare con solo 4 vertici, invece che 6. Questa situazione non farà che peggiorare una volta che andremo a utilizzare modelli complessi con oltra i mille triangoli.

Una soluzione potrebbe essere quella di memorizzare solo i vertici unici e poi specificare in che ordine vogliamo siano disegnati; un element buffer object funziona esattamente così.

Un EBO è un buffer, proprio come un buffer di vertici, che immagazina gli indici che OpenbGL utilizza per decidere che vertici disegnare. Questa tecnica chiamata "index drawing" è esattamente la soluzione al nostro problema. 

Per iniziare specifichiamo i vertici (quelli unici) e gli indici da usare per disegnare il rettangolo:
```cpp
float vertices[] = {
     0.5f,  0.5f, 0.0f, // top right
     0.5f, -0.5f, 0.0f, // bottom right
    -0.5f, -0.5f, 0.0f, // bottom left
    -0.5f,  0.5f, 0.0f // top left
};
unsigned int indices[] = { // note that we start from 0!
    0, 1, 3, // first triangle
    1, 2, 3 // second triangle
};
```
Come prossimo passo creiamo il buffer di elementi:
```cpp
unsigned int EBO;
glGenBuffers(1, &EBO);
```
Abbiniamo poi il EBO e copiamo gli indici dentro il buffer tramite ```glBufferData```. Queste chiamate sono da specificare tra una chiamata di abbinamento e una di dis-abbinamento, e questa volta specifichiamo ```GL_ELEMENT_ARRAY_BUFFER``` come tipo di buffer.
```cpp
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices,
             GL_STATIC_DRAW)
;
```
L'ultima cosa da fare è rimpiazzare ```glDrawArrays``` con ```glDrawElements``` per indicare che vogliamo renderizzare i triangoli da un buffer di indici. Quando si usa ```glDrawElements``` si disegna utilizzando gli indici 
