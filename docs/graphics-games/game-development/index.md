---
title: Introduzione allo sviluppo di giochi con Xamarin
description: "La natura di sviluppo di giochi può variare notevolmente dallo sviluppo di altri tipi di App. In questo articolo viene fornita un'introduzione allo sviluppo di gioco con tecnologie che possono essere utilizzate con xamarin. IOS e xamarin. Fornisce una descrizione di alto livello di come vengono effettuati i giochi sia un campionamento delle tecnologie disponibili per l'uso con xamarin. IOS e xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: 0E3CDCD2-FBE4-49F5-A70E-8A7B937BAF1D
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 9d1ce2da87d6f169efb5431f734695f6876cf3f0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-game-development-with-xamarin"></a>Introduzione allo sviluppo di giochi con Xamarin

_La natura di sviluppo di giochi può variare notevolmente dallo sviluppo di altri tipi di App. In questo articolo viene fornita un'introduzione allo sviluppo di gioco con tecnologie che possono essere utilizzate con xamarin. IOS e xamarin. Fornisce una descrizione di alto livello di come vengono effettuati i giochi sia un campionamento delle tecnologie disponibili per l'uso con xamarin. IOS e xamarin._

Lo sviluppo di giochi può essere molto interessante, particolarmente semplice può essere per pubblicare il lavoro su piattaforme per dispositivi mobili. In questo articolo vengono descritti i concetti e alle tecnologie correlate per lo sviluppo di gioco che consentirà di creare giochi, se l'obiettivo consiste nel creare un AAA di alta qualità gioco o solo al programma per fun.

In questo articolo vengono illustrati gli argomenti seguenti:

- **Gioco e concetti relativi alla programmazione di gioco non** – si esamineranno alcuni concetti che sono univoci per lo sviluppo di gioco, o sono condivise con altri tipi di sviluppo ma meritano qui a causa delle loro importanza.
- **Il team di sviluppo di giochi** : questa sezione vengono esaminati i diversi ruoli in un team di sviluppatori di giochi.
- **Creazione di una gioco idea** : in questa sezione consentono di creare una nuova idea di gioco: il primo passaggio di creazione di un nuovo gioco.
- **Tecnologia di sviluppo di giochi** – qui verranno elencati alcune delle tecnologie multipiattaforma disponibili che possono migliorare la produttività degli sviluppatori gioco.


# <a name="game-vs-non-game-programming-concepts"></a>Visual Studio di gioco. Concetti relativi alla programmazione non gioco

I programmatori lo spostamento in sviluppo di giochi spesso devono affrontare nuovi concetti e modelli di sviluppo. In questa sezione viene presentata una panoramica di alcuni di questi concetti.


## <a name="the-game-loop"></a>Il ciclo di gioco

Un tipico gioco richiede lo spostamento di costante o alla modifica di essere eseguite sullo schermo in risposta all'interazione dell'utente sia automatico logica del gioco. Grazie a ciò che viene generalmente indicato come un *ciclo gioco*. Un ciclo di gioco è un tipo di istruzione (ad esempio un ciclo while) che viene eseguito con una frequenza molto elevata, ad esempio 30 o 60 ciclo *fotogrammi al secondo*.

Di seguito è un diagramma di un ciclo di gioco semplice:

![](images/image1.png "Si tratta di un diagramma di un semplice ciclo di gioco")

Le tecnologie che vengono discussi seguito astrarre il ciclo while effettivo, ma nonostante questa astrazione sarà presente il concetto di aggiornamenti di ogni fotogramma.

Le prestazioni del codice possono richiedere la priorità anche il più semplice di giochi. Ad esempio: una funzione che accetta 10 millisecondi per l'esecuzione potrebbe avere un impatto significativo sulle prestazioni di un gioco, soprattutto se viene chiamato più di una volta per fotogramma. Se il gioco è in esecuzione 30 frame al secondo significa che ogni frame deve essere eseguito nel 33 in millisecondi. Al contrario, tale funzione non anche siano evidente se viene eseguito solo in risposta a un clic del pulsante in un'applicazione non gioco.

I tipi di logica che può essere eseguita ogni fotogramma comuni includono:

- **Lettura Input** : il gioco potrebbe essere necessario controllare se l'utente ha interagito con il gioco controllando l'hardware di input, ad esempio il touchscreen, tastiera, mouse o di gioco.
- **Lo spostamento** : move da un'unica posizione a un'altra in genere sposterà molto piccoli oggetti importo ogni frame per conferire un effetto ottico di fluidi.
- **Collisione** : molti dei giochi richiedono il test frequenti di se diversi oggetti sono sovrapposti o che si intersecano. Tratteremo conflitti in modo approfondito più avanti in questo articolo. Lo spostamento e i conflitti possono essere gestiti da un sistema di simulazione fisica dedicata.
- **Controllo delle condizioni specifiche di gioco** : è possibile controllare lo stato di un gioco da determinate condizioni, ad esempio se il lettore è realizzato punti sufficienti o se è esaurito tempo a disposizione.
- **Il comportamento AI** : logica di ogni fotogramma che può essere utilizzata per controllare il comportamento degli oggetti che non sono controllati da Windows Media player, ad esempio patrolling di un nemico o lo spostamento dei driver avversario intorno a un tavolo.
- **Il rendering** : la maggior parte dei giochi aggiornerà gli elementi visualizzati nella schermata di ogni frame. Questa operazione può essere eseguita in risposta alle modifiche che hanno impatto sul gioco (ad esempio un carattere di spostarsi all'interno di un livello) o semplicemente per fornire visual polacco (ad esempio, in diminuzione nevicate o icone animate).

Tenere presente che molte delle attività elencate in precedenza può modificare lo stato dell'intera applicazione, mentre molte applicazioni non gioco tendono a cambiare lo stato in risposta a eventi generati.


## <a name="content-loading-and-unloading"></a>Contenuto durante il caricamento e scaricamento

Contenuto manualmente il caricamento e scaricamento (o eliminazione) potrebbe essere necessarie a seconda di quale tecnologia si utilizza in fase di sviluppo. Manualmente durante il caricamento e scaricamento degli asset potrebbero essere necessari per una serie di motivi:

 - Asset potrebbero richiedere molto tempo per caricare relativamente alla lunghezza di un singolo fotogramma. Alcuni asset può richiedere anche secondi da caricare, risulterebbe notevolmente l'esperienza se la modalità di gioco mid caricato. Se il tempo di caricamento è particolarmente lungo (ad esempio più di uno o due secondi) si desidera mostrare un animata durante il caricamento dello schermo o indicatori di stato.
 - Asset consumano molta RAM, richiedere la gestione attiva di ciò che viene caricato in modo da adattarsi a quello fornito da piattaforme di destinazione del gioco.
 - Giochi potrebbero essere necessario visualizzare altre risorse possono adattarsi alla memoria RAM. "Aprire World" giochi includono spesso ambienti di grandi dimensioni quali giocatori possano spostarsi facilmente: con alcuna schermata di caricamento. In questo caso occorre creare un sistema personalizzato per il contenuto in streaming e la gestione dell'utilizzo della memoria.

Formati di file personalizzati potrebbe essere necessario l'elaborazione in fase di caricamento, che richiede codice personalizzato durante il caricamento.


## <a name="math"></a>Math

Molti dei giochi richiedono matematiche più avanzate rispetto alle applicazioni non del gioco. Naturalmente, il livello di matematica dipende dalla complessità del gioco. In generale, giochi 3D richiedono ulteriori matematiche di 2D. Fortunatamente, è possibile iniziare sempre con semplici giochi e approfondire. Sviluppo di giochi può essere un modo eccellente per apprendere math!

Se si ha familiarità con il piano cartesiano – che utilizza le coordinate X e Y per posizionare gli oggetti e quindi si informazioni sufficienti per informazioni introduttive allo sviluppo di gioco. Di seguito viene illustrato un piano cartesiano con positivo Y che punta verso l'alto:

![](images/image2.png "Viene illustrato un piano cartesiano con positivo Y che punta verso l'alto")

> [!IMPORTANT]
> Alcuni motori/API utilizzano un sistema di coordinate in cui aumentare il valore di Y dell'oggetto verrà spostata verso il basso, mentre altri sistemi utilizzano un sistema di coordinate in cui Y positivo sia attivo. Tenere presente questo presente se si desidera spostare tra i sistemi.
Funzioni trigonometriche (ad esempio seno e coseno) vengono comunemente utilizzate nei giochi 2D che implementano quindi alcuna forma di rotazione.



Se si prevede di effettuare un gioco 3D si sarà probabilmente necessario avere familiarità con concetti da Algebra lineare (per la rotazione e spostamento nello spazio 3D), nonché alcuni calcolo (per l'implementazione di accelerazione).


## <a name="content-pipelines"></a>Pipeline di contenuto

Il termine *pipeline contenuta* si riferisce al processo che accetta un file da ottenere dal formato quando creato (ad esempio un file di immagine PNG) nel relativo formato finale, quando utilizzato in un gioco. Il formato finale varia in cui tipo di contenuto utilizzato e che la tecnologia viene utilizzata per presentare il contenuto.

Alcune pipeline contenute potrebbero essere molto veloci e non richiedono alcun intervento manuale. Ad esempio, la maggior parte delle API e motori di giochi possono caricare il formato del file con estensione png nel relativo formato non elaborato. D'altra parte, potrebbero essere più complicati formati (ad esempio i modelli 3D) da elaborare in un formato diverso prima fase di caricamento e l'elaborazione può richiedere alcuni minuti a seconda delle dimensioni e la complessità dell'asset.


# <a name="game-development-teams"></a>Team di sviluppo di giochi

Sviluppo di giochi introduce nuovi ruoli e i titoli per utenti singoli coinvolti nel processo. La maggior parte degli sviluppatori di giochi non sono in grado di soddisfare l'ampia gamma di competenze necessarie per rilasciare un gioco completo, in modo che un numero di discipline esiste. Tenere presente che questo non è un elenco completo delle aree di sviluppo – solo alcuni dei più comuni.

- **Programmatore** : la maggior parte delle persone, leggere questo articolo verrà rientrano in questa categoria. Il ruolo del programmatore nello sviluppo di gioco è simile al ruolo del programmatore in un'applicazione non gioco. Responsabilità comprendono la scrittura logica per controllare il flusso di un gioco, lo sviluppo di sistemi per le attività comuni nel contesto di un determinato progetto, aggiunta e la visualizzazione di contenuto e – naturalmente: la correzione di bug.
- **2D artista** – artisti 2D sono responsabili della creazione *asset 2D*. Includono i file di immagine per il gioco GUI, particelle, ambienti e caratteri. Se il gioco che si sta sviluppando 3D, artisti 2D non possono essere responsabili per ambienti e i caratteri. È possibile trovare libero grafica per il gioco in [http://opengameart.org/](http://opengameart.org/) .
- **Artisti 3D** – artisti 3D sono responsabili della creazione *asset 3D*. Queste includono modelli 3D per ambienti, i caratteri e props (mobili piante e altri oggetti inanimati). Alcuni team di distinguere tra artisti 3D e a seconda delle dimensioni del team di esperti di animazione 3D. È possibile trovare libero grafica 3D per il gioco in [http://opengameart.org/](http://opengameart.org/) .
- **Finestra di progettazione di gioco** – giochi finestre di progettazione sono responsabili per la definizione di come viene riprodotto il gioco. Può trattarsi di alto livello decisioni in merito ad esempio l'impostazione del gioco, l'obiettivo complessivo di gioco e modalità di avanzamento di un lettore tramite il gioco. Giochi finestre di progettazione può essere coinvolti nelle decisioni molto dettagliate, ad esempio input di mapping di azioni, anche la definizione di coefficienti per lo spostamento o livello di gruppo di continuità e la progettazione di layout a livello. Tenere presente che il termine *progettazione* può fare riferimento a una finestra di progettazione del gioco o una finestra di progettazione visiva a seconda del contesto.
- **Audio progettazione** – audio finestre di progettazione sono responsabili dell'asset audio di un gioco. Alcuni team è possibile distinguere tra responsabili per la creazione di effetti sonori e compositori, mentre il team di dimensioni ridotte potrebbe essere un singolo utente responsabile per tutti i file audio.


# <a name="creating-a-game-idea"></a>Creazione di un'Idea di gioco

Progettazione di un gioco è ammesso facili da eseguire altre operazioni – dopo che tutto l'unico requisito è "fare qualcosa divertente". Sfortunatamente, molti sviluppatori trovano perdite al momento di creare un'idea dal quale avviare lo sviluppo.

Non è facilmente spiegata la disciplina di progettazione di gioco e richiede procedure consigliate per migliorare così come la locandina o programmazione, ma in questa sezione è utili per iniziare il percorso verso il basso.

Nuovi sviluppatori devono iniziare. Può essere difficile la parte relativa a ricreare un gioco moderno e di grandi dimensioni, ma rende lo stato di avanzamento più veloce per un'esperienza telefonata e giochi più piccoli possibile un ambiente di apprendimento migliore.

Molti dei giochi, entrambi allo scopo di apprendimento, nonché commerciale giochi, inizia come un miglioramento o la modifica di un gioco esistente. Un modo per generare idee è esaminati altri giochi per ispirato. Ad esempio, è possibile considerare un gioco che si desidera personale e provare a identificare le caratteristiche di gioco renderlo divertente. Potrebbe essere l'esplorazione, la padronanza dei meccanismi del gioco o passare a una storia. Non dimenticare di prendere in considerazione anche giochi "retro" durante la ricerca di nuove idee.

Un'altra tecnica per la generazione di nuove idee è opportuno considerare un genere specifico, ad esempio puzzle giochi, giochi strategia o platformers. Un genere familiare per lo sviluppatore potrebbe fornire un buon punto di partenza.

Per rieseguire giochi esistenti è anche un'esperienza di apprendimento, anche se tale condizione può limitare redditività del prodotto finito. Il processo di creazione di un gioco, anche quello che è un clone accurato, offre un'esperienza di didattico importante.


# <a name="game-development-technology"></a>Tecnologia di sviluppo di giochi

Gli sviluppatori che usano xamarin e xamarin. IOS avere un'ampia gamma di tecnologie disponibili per facilitare lo sviluppo di gioco. In questa sezione verrà illustrate alcune delle soluzioni multipiattaforma più diffuse.


## <a name="cocossharp"></a>CocosSharp

CocosSharp è una versione open source, multipiattaforma del motore di giochi 2D Cocos. Il motore consente di accedere a Android, iOS, Mac OS X, Windows Desktop, Windows RT e Windows Phone.

CocosSharp illustra un semplice programmatore API per lo sviluppo di gioco 2D. L'aumento delle dimensioni in modalità di gioco sui dispositivi mobili ha contribuito a reignite la diffusione di sviluppo di gioco 2D per rendere la tecnologia di vitale CocosSharp hobby e commerciali progetti simili. Viene fornito come file di codice o DLL di origine (che possono essere ottenuti tramite NuGet) ma non offre un editor visivo; Pertanto, qualsiasi interazione con il motore CocosSharp richiede conoscenze di programmazione.

Per informazioni introduttive su CocosSharp, consultare il nostro [CocosSharp Guide](~/graphics-games/cocossharp/index.md).

Il gioco Ninjas arrabbiata viene creato con CocosSharp e può essere un buon punto di partenza se si sta cercando un gioco già in esecuzione per più piattaforme:

![](images/image3.png "Il gioco a cui è stato creato con CocosSharp Ninjas arrabbiata")

È possibile scaricarlo e ottenere ulteriori informazioni di [pagina AngryNinjas Github](https://github.com/xamarin/AngryNinjas).


## <a name="monogame"></a>MonoGame

MonoGame è un open source, tra la versione della piattaforma dell'API di Microsoft XNA. MonoGame consente di creare giochi per iOS, Android, Mac OS X, Linux, Windows, Windows RT e Windows Phone.

A differenza di CocosSharp, MonoGame è tecnicamente non un gioco motore, ma piuttosto un gioco API per lo sviluppo. Ciò significa che l'utilizzo con MonoGame richiede direttamente la gestione degli oggetti di giochi manualmente oggetti disegno e implementazione di oggetti comuni, ad esempio fotocamere e *grafici scena* (la gerarchia padre-figlio tra gli oggetti di giochi). Per comprendere la differenza, prendere in considerazione che CocosSharp si basa su MonoGame. MonoGame consente di generalizzare di alcune tecnologie specifiche della piattaforma, ad esempio grafici, rendering e audio, mentre CocosSharp fornisce il codice per l'organizzazione e l'implementazione della logica di gioco.

MonoGame non offre un ambiente di sviluppo visual standard, l'utilizzo di MonoGame richiede conoscenze di programmazione.

Esempi rilevanti di giochi usando MonoGame includono:

FEZ:

![](images/image7.png "FEZ")

Bastion:

![](images/image8.jpg "Bastion")

Per iniziare a lavorare con MonoGame, visitare il nostro [MonoGame Guide](~/graphics-games/monogame/index.md).


## <a name="urhosharp"></a>UrhoSharp

UrhoSharp è una multipiattaforma ad alto livello 2D e 3D motore può essere utilizzato per creare scene di animazione 2D e 3D per le applicazioni con geometrie, materiali, luci e fotocamere.

![](images/urhosharp.gif "UrhoSharp è una multipiattaforma ad alto livello 2D e 3D motore che può essere utilizzato per creare animate scene 2D e 3D")

Estrarre il [UrhoSharp Guide](~/graphics-games/urhosharp/index.md) per iniziare.

## <a name="additional-technology"></a>Tecnologia aggiuntiva

Le tecnologie evidenziate sopra è solo un esempio delle tecnologie disponibili. Altre tecnologie importanti includono:

- **Kit sprite** – Xamarin offre supporto per framework gioco Sprite Kit, Apple che consente di accedere a tutte le funzionalità dell'API native. Poiché Sprite Kit è una tecnologia di Apple, fornisce una stretta integrazione con il resto dell'ecosistema di iOS. Naturalmente, Sprite Kit non è multipiattaforma pertanto non può essere utilizzato in Android. Per ulteriori informazioni sull'utilizzo Sprite Kit, vedere questo post: [http://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/](http://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/)
- **Kit di scena** – Xamarin offre inoltre supporto per il framework di scena Kit di Apple, che semplifica l'implementazione di grafica 3D in App iOS. Kit di scena è anche tecnologia fornita da Apple, quindi presenta l'integrazione e considerazioni specifiche della piattaforma sopra indicate per Sprite Kit. Per ulteriori informazioni sui Kit scena, vedere questo post: [http://blog.xamarin.com/3d-in-ios-8-with-scene-kit/](http://blog.xamarin.com/3d-in-ios-8-with-scene-kit/)
- **OpenTK –** OpenTK (che è l'acronimo di Open Tool Kit) fornisce l'accesso di iOS e Apple Mac OpenGL basso livello hardware. Per ulteriori informazioni su OpenTK, vedere la pagina principale in: [http://www.opentk.com/](http://www.opentk.com/)


# <a name="summary"></a>Riepilogo

In questo articolo vengono illustrati i concetti principali di sviluppo di giochi e fornisce informazioni su come iniziare a creare giochi. Dopo aver completato questo articolo, i passaggi successivi devono scegliere la tecnologia e iniziare a lavorare attraverso la serie di esercitazioni collegate nelle precedenti sezioni appropriate.

## <a name="related-links"></a>Collegamenti correlati

- [Guide CocosSharp](~/graphics-games/cocossharp/index.md)
- [Guide MonoGame](~/graphics-games/monogame/index.md)
- [Guide UrhoSharp](~/graphics-games/urhosharp/index.md)
