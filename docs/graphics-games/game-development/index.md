---
title: Introduzione allo sviluppo di giochi con Novell
description: Questo documento offre una panoramica di alto livello dello sviluppo di giochi con Novell, che descrive sia la modalità di creazione dei giochi sia un campionamento di tecnologie disponibili per l'uso con Novell. iOS e Novell. Android.
ms.prod: xamarin
ms.assetid: 0E3CDCD2-FBE4-49F5-A70E-8A7B937BAF1D
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: 5992e8df3080bb35fd123483e5ffb5e64f268b1a
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292665"
---
# <a name="introduction-to-game-development-with-xamarin"></a>Introduzione allo sviluppo di giochi con Novell

Lo sviluppo di giochi può essere molto interessante, soprattutto in base alla facilità di pubblicazione del lavoro sulle piattaforme mobili. Questo articolo illustra i concetti e le tecnologie correlati allo sviluppo di giochi che consentiranno di creare giochi, indipendentemente dal fatto che l'obiettivo sia quello di creare un gioco AAA di alta qualità o semplicemente programmare per divertimento.

Questo articolo include gli argomenti seguenti:

- **Giochi e concetti di programmazione non di gioco** : verranno illustrati alcuni concetti che sono univoci per lo sviluppo di giochi o che vengono condivisi con altri tipi di sviluppo, ma che meritano particolare attenzione a causa della loro importanza.
- **Team di sviluppo di giochi** : questa sezione esamina i vari ruoli in un team di sviluppatori di giochi.
- **Creazione di un'idea del gioco** : questa sezione consente di creare una nuova idea del gioco, ovvero il primo passaggio per la creazione di un nuovo gioco.
- **Tecnologia per lo sviluppo di giochi** : di seguito sono elencate alcune delle tecnologie multipiattaforma disponibili che possono migliorare la produttività come sviluppatore di giochi.

## <a name="game-vs-non-game-programming-concepts"></a>Giochi rispetto ai concetti di programmazione non di gioco

I programmatori che passano allo sviluppo di giochi sono spesso confrontati con nuovi concetti e modelli di sviluppo. In questa sezione viene presentata una panoramica di alto livello di alcuni di questi concetti.

### <a name="the-game-loop"></a>Il ciclo del gioco

Un gioco tipico richiede un movimento costante o una modifica sullo schermo in risposta all'interazione dell'utente e alla logica del gioco automatica. Questa operazione viene eseguita tramite ciò che viene in genere definito *ciclo di gioco*. Un ciclo del gioco è un tipo di istruzione di ciclo, ad esempio un ciclo while, che viene eseguito a una frequenza molto elevata, ad esempio 30 o 60 *fotogrammi al secondo*.

Di seguito è riportato un diagramma di un semplice ciclo di gioco:

![](images/image1.png "This is a diagram of a simple game loop")

Le tecnologie descritte di seguito elimineranno il ciclo while effettivo, ma nonostante questa astrazione sarà presente il concetto di aggiornamenti di ogni frame.

Le prestazioni del codice possono avere la priorità anche per i giochi più semplici. Ad esempio, una funzione che richiede 10 millisecondi per l'esecuzione potrebbe avere un impatto significativo sulle prestazioni di un gioco, soprattutto se viene chiamato più di una volta per fotogramma. Se il gioco è in esecuzione a 30 fotogrammi al secondo, significa che ogni frame deve essere eseguito in meno di 33 millisecondi. Al contrario, una funzione di questo tipo potrebbe non essere evidente se viene eseguita solo in risposta a un clic del pulsante in un'applicazione non di gioco.

I tipi comuni di logica che possono essere eseguiti ogni frame includono:

- **Lettura dell'input** : il gioco potrebbe dover verificare se l'utente ha interagito con il gioco controllando l'hardware di input, ad esempio il touchscreen, la tastiera, il mouse o il controller di gioco.
- **Spostamento** : gli oggetti che si spostano da una posizione a un'altra in genere spostano una quantità molto piccola di ogni fotogramma per dare l'illusione del movimento fluido.
- **Collisione** : per molti giochi è necessario testare spesso se i vari oggetti si sovrappongono o si intersecano. In una sezione successiva di questo articolo verrà illustrata una collisione più approfondita. Lo spostamento e la collisione possono essere gestiti da un sistema di simulazione di fisica dedicato.
- **Verifica delle condizioni specifiche del gioco** : lo stato di un gioco può essere controllato da determinate condizioni, ad esempio se il giocatore ha guadagnato abbastanza punti o se il tempo assegnato è scaduto.
- **Comportamento di intelligenza artificiale** : logica di ogni frame che può essere usata per controllare il comportamento degli oggetti che non sono controllati dal lettore, ad esempio il pattugliamento di un nemico o lo spostamento di driver avversari in una pista.
- **Rendering** : la maggior parte dei giochi aggiornerà gli elementi visualizzati sullo schermo per ogni fotogramma. Questa operazione può essere eseguita in risposta a modifiche che hanno un impatto sulla riproduzione del gioco, ad esempio un carattere che si muove attraverso un livello, o semplicemente per fornire un oggetto visivo polacco, ad esempio la nevicata o le icone animate.

Tenere presente che molte delle attività elencate sopra possono modificare lo stato dell'intera applicazione, mentre molte app non di gioco tendono a cambiare lo stato in risposta agli eventi generati.

### <a name="content-loading-and-unloading"></a>Caricamento e scaricamento di contenuto

Potrebbe essere necessario caricare e scaricare manualmente (o eliminare) il contenuto a seconda della tecnologia usata in fase di sviluppo. Il caricamento e lo scaricamento manuale delle risorse può essere necessario per diversi motivi:

- Il caricamento degli asset può richiedere molto tempo rispetto alla lunghezza di un singolo frame. Alcune risorse possono anche richiedere alcuni secondi per il caricamento, che potrebbero compromettere l'esperienza se caricata a metà del gioco. Se il tempo di caricamento è particolarmente lungo (ad esempio più di un secondo o due), è possibile che si desideri visualizzare una schermata di caricamento animato o un indicatore di stato.
- Gli asset possono utilizzare una grande quantità di RAM, in modo da richiedere una gestione attiva degli elementi caricati per adattarsi a quanto fornito dalle piattaforme di destinazione del gioco.
- Per i giochi potrebbe essere necessario visualizzare un maggior numero di risorse rispetto a quelle che possono adattarsi alla RAM. I giochi "Open World" includono spesso ambienti di grandi dimensioni che i lettori possono esplorare senza interruzioni, ovvero senza schermate di caricamento. In questo caso potrebbe essere necessario creare un sistema personalizzato per lo streaming di contenuto in e la gestione dell'utilizzo della memoria.

I formati di file personalizzati possono richiedere l'elaborazione in fase di caricamento, che richiede il codice di caricamento personalizzato.

### <a name="math"></a>Matematiche

Molti giochi richiedono una matematica più avanzata rispetto alle applicazioni non di gioco. Naturalmente, il livello di matematica dipende dalla complessità del gioco. In generale, i giochi 3D richiedono più calcoli di 2D. Fortunatamente è sempre possibile iniziare a usare giochi semplici e apprendere le informazioni man mano che si procede. Lo sviluppo di giochi può essere un ottimo modo per apprendere i calcoli matematici.

Se si ha familiarità con il piano cartesiano, che sta usando le coordinate X e Y per posizionare gli oggetti, è sufficiente iniziare a sviluppare il gioco. Di seguito viene illustrato un piano cartesiano con Y positivo che punta verso l'alto:

![](images/image2.png "This shows a Cartesian plane with positive Y pointing upward")

> [!IMPORTANT]
> Alcuni motori/API usano un sistema di coordinate in cui l'aumento del valore Y di un oggetto lo sposta in basso, mentre altri sistemi usano un sistema di coordinate in cui è attivo Y positivo. Tenere presente questo aspetto se si passa da un sistema all'altra.
Le funzioni trigonometriche, ad esempio seno e coseno, vengono comunemente usate nei giochi 2D che implementano qualsiasi forma di rotazione.

Se si prevede di creare un gioco 3D, è probabile che sia necessario acquisire familiarità con i concetti di algebra lineare (per rotazione e spostamento nello spazio 3D), oltre ad alcuni calcoli (per implementare l'accelerazione).

### <a name="content-pipelines"></a>Pipeline di contenuti

Il termine *pipeline di contenuti* si riferisce al processo che un file richiede per ottenere dal formato quando viene creato, ad esempio un file di immagine con estensione png, nel formato finale quando viene usato in un gioco. Il formato finale dipende dal tipo di contenuto utilizzato, nonché dalla tecnologia utilizzata per presentare il contenuto.

Alcune pipeline di contenuti possono essere molto veloci e non richiedono alcun intervento manuale. Ad esempio, la maggior parte dei motori di gioco e delle API possono caricare il formato di file png nel formato non elaborato. D'altra parte, è possibile che i formati più complessi, ad esempio i modelli 3D, debbano essere elaborati in un formato diverso prima del caricamento. questa elaborazione può richiedere del tempo a seconda delle dimensioni e della complessità dell'asset.

## <a name="game-development-teams"></a>Team di sviluppo di giochi

Lo sviluppo di giochi introduce nuovi ruoli e titoli per i singoli utenti interessati al processo. La maggior parte degli sviluppatori di giochi non è in grado di soddisfare l'ampia gamma di competenze necessarie per rilasciare un gioco completo, quindi sono presenti diverse discipline. Tenere presente che non si tratta di un elenco completo delle aree di sviluppo, solo alcune delle più comuni.

- **Programmatore** : la maggior parte degli utenti che leggono questo articolo rientrerà in questa categoria. Il ruolo di programmatore nello sviluppo di giochi è simile al ruolo di un programmatore in un'applicazione non di gioco. Le responsabilità includono la scrittura della logica per controllare il flusso di un gioco, lo sviluppo di sistemi per attività comuni nel contesto di un determinato progetto, l'aggiunta e la visualizzazione di contenuto e, ovviamente, la correzione di bug.
- **artista** 2D: gli artisti 2D sono responsabili della creazione di *asset 2D*. Sono inclusi i file di immagine per l'interfaccia utente grafica del gioco, le particelle, gli ambienti e i caratteri. Se il gioco che si sta sviluppando è 3D, gli artisti 2D potrebbero non essere responsabili di ambienti e caratteri. È possibile trovare immagini gratuite per il gioco all' [http://opengameart.org/](http://opengameart.org/) .
- **artisti 3D** : gli artisti 3D sono responsabili della creazione di *asset 3D*. Sono inclusi i modelli 3D per gli ambienti, i caratteri e le prop (mobili, piante e altri oggetti inanimati). Alcuni team distinguono tra gli artisti 3D e gli animatori 3D a seconda delle dimensioni del team. È possibile trovare immagini 3D gratuite per il gioco all' [http://opengameart.org/](http://opengameart.org/) .
- **Game designer** : i progettisti di gioco sono responsabili della definizione del modo in cui viene riprodotto il gioco. Questo può includere decisioni di alto livello, ad esempio l'impostazione del gioco, l'obiettivo generale del gioco e il modo in cui un giocatore passa attraverso il gioco. I progettisti di giochi possono anche essere impegnati in decisioni molto dettagliate, ad esempio il mapping dell'input alle azioni, la definizione di coefficienti per lo spostamento o i livelli e la progettazione del layout del livello. Tenere presente che la *finestra* di progettazione dei termini può fare riferimento a un progettista di giochi o a una finestra di progettazione visiva a seconda del contesto.
- **Sound Designer** : le finestre di progettazione audio sono responsabili delle risorse audio del gioco. Alcuni team possono distinguere tra i singoli responsabili della creazione di effetti acustici e Composer, mentre i team più piccoli possono avere un singolo utente responsabile di tutti i file audio.

## <a name="creating-a-game-idea"></a>Creazione di un'idea del gioco

La progettazione di un gioco può sembrare semplice: dopo l'unico requisito è "make something Fun". Sfortunatamente, molti sviluppatori si trovano a perdere quando si tratta di creare un'idea da cui avviare lo sviluppo.

La disciplina della progettazione dei giochi non è facilmente spiegabile e richiede un miglioramento analogo a quello dell'arte o della programmazione, ma questa sezione può essere utile per avviare il percorso.

I nuovi sviluppatori dovrebbero avviare Small. Può essere difficile resistere alla tentazione di ricreare un gioco video moderno di grandi dimensioni, ma i giochi più piccoli possono essere un ambiente di apprendimento migliore e l'avanzamento più rapido rende più gratificante l'esperienza.

Molti giochi, sia per lo scopo dell'apprendimento che per i giochi commerciali, iniziano come un miglioramento o una modifica a un gioco esistente. Un modo per generare idee consiste nell'esaminare altri giochi per ispirarsi. Ad esempio, è possibile prendere in considerazione un gioco di cui si è personalmente certi e provare a identificare le caratteristiche relative alla riproduzione del gioco. Potrebbe trattarsi di un'esplorazione, una padronanza dei meccanismi del gioco o la progressione di una storia. Non dimenticare di prendere in considerazione anche i giochi "retrò" durante la ricerca di nuove idee.

Un'altra tecnica per la generazione di nuove idee consiste nel considerare un genere specifico, ad esempio giochi Rompicapo, giochi di strategia o piattaforme Platform. Un genere familiare allo sviluppatore potrebbe offrire un punto di partenza valido.

La ricreazione di giochi esistenti è anche un'esperienza didattica, anche se ciò potrebbe limitare la redditività commerciale del prodotto finito. Il processo di creazione di un gioco, anche un clone accurato, offre un'esperienza didattica preziosa.

## <a name="game-development-technology"></a>Tecnologia per lo sviluppo di giochi

Gli sviluppatori che usano Novell. Android e Novell. iOS hanno un'ampia gamma di tecnologie disponibili per supportare lo sviluppo di giochi. In questa sezione verranno illustrate alcune delle soluzioni multipiattaforma più diffuse.

### <a name="monogame"></a>MonoGame

MonoGame è una versione open source multipiattaforma dell'API XNA di Microsoft. Monogame può essere usato per creare giochi per iOS, Android, Mac OS X, Linux, Windows, Windows RT, PS4, PSVita, Xbox One e switch.

Monogame non è tecnicamente un motore di gioco, bensì un'API per lo sviluppo di giochi. Questo significa che l'uso di monogame richiede la gestione diretta degli oggetti di gioco, il disegno manuale di oggetti e l'implementazione di oggetti comuni, ad esempio fotocamere e *grafici di scene* (la gerarchia padre-figlio tra oggetti di gioco).

Monogame non offre un ambiente di sviluppo visivo standard, pertanto l'uso di monogame richiede la conoscenza della programmazione.

Esempi significativi di giochi che usano monogame includono:

FEZ:

![](images/image7.png "FEZ")

Bastion

![](images/image8.jpg "Bastion")

Per iniziare a usare monogame, passare alle [guide monogame](~/graphics-games/monogame/index.md).

### <a name="urhosharp"></a>UrhoSharp

UrhoSharp è un motore 3D e 2D di alto livello multipiattaforma che può essere usato per creare scene 3D e 2D animate per le applicazioni usando geometrie, materiali, luci e fotocamere.

![](images/urhosharp.gif "UrhoSharp is a cross-platform high-level 3D and 2D engine that can be used to create animated 3D and 2D scenes")

Per iniziare, consultare le [guide UrhoSharp](~/graphics-games/urhosharp/index.md) .

### <a name="additional-technology"></a>Tecnologia aggiuntiva

Le tecnologie evidenziate in precedenza sono solo un esempio delle tecnologie disponibili. Altre tecnologie rilevanti includono:

- **Sprite Kit** – Novell fornisce supporto per il Framework di gioco di Apple sprite Kit, che consente di accedere a tutte le funzionalità dell'API nativa. Poiché sprite kit è una tecnologia creata da Apple, fornisce una profonda integrazione con il resto dell'ecosistema iOS. Ovviamente, lo sprite kit non è multipiattaforma e non può essere usato in Android. Per ulteriori informazioni sull'utilizzo di sprite Kit, vedere questo post: [https://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/](https://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/)
- **Scene Kit** -Novell fornisce anche il supporto per il Framework di Apple scene Kit, che semplifica l'implementazione di grafica 3D in app iOS. Il kit di scene è anche una tecnologia fornita da Apple, quindi presenta le considerazioni relative all'integrazione e alla piattaforma indicate sopra per Sprite Kit. Per altre informazioni su scene Kit, vedere questo post: [https://blog.xamarin.com/3d-in-ios-8-with-scene-kit/](https://blog.xamarin.com/3d-in-ios-8-with-scene-kit/)
- **OpenTK-** OpenTK (acronimo di Open Tool Kit) fornisce l'accesso OpenGL di basso livello a hardware iOS, Apple e Mac. Per ulteriori informazioni su OpenTK, vedere la pagina principale all'indirizzo: [https://opentk.net/](https://opentk.net/)

## <a name="related-links"></a>Collegamenti correlati

- [Guide monogame](~/graphics-games/monogame/index.md)
- [Guide UrhoSharp](~/graphics-games/urhosharp/index.md)
