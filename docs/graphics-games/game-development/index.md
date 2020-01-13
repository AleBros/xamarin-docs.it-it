---
title: Introduzione allo sviluppo di giochi con Xamarin
description: Questo documento fornisce una panoramica generale dello sviluppo di gioco con Xamarin, che descrivono come devono essere apportati giochi e un campionamento di tecnologie disponibili per l'uso con Xamarin.IOS e Xamarin.Android.
ms.prod: xamarin
ms.assetid: 0E3CDCD2-FBE4-49F5-A70E-8A7B937BAF1D
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: 0fa4dbbf256e005fe17841f06acf1338197784b0
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832277"
---
# <a name="introduction-to-game-development-with-xamarin"></a>Introduzione allo sviluppo di giochi con Xamarin

Sviluppo di giochi può essere molto interessante, soprattutto considerando può essere facile per pubblicare il lavoro su piattaforme per dispositivi mobili. Questo articolo illustra i concetti e tecnologie correlate allo sviluppo di giochi che consenta di creare giochi, se l'obiettivo consiste nel creare una qualità AAA giochi o semplicemente al programma per divertimento.

Questo articolo illustra gli argomenti seguenti:

- **Giochi e i concetti di programmazione non gioco** – illustra alcuni concetti che possono essere univoci per lo sviluppo di giochi, o sono condivise con altri tipi di sviluppo ma meritano qui a causa delle loro importanza.
- **Team di sviluppo di giochi** : questa sezione vengono esaminati i diversi ruoli in un team di sviluppatori di giochi.
- **Creazione di un'idea del gioco** : in questa sezione per creare una nuova idea di gioco: il primo passaggio per creare un nuovo gioco.
- **Tecnologia per lo sviluppo di giochi** : qui sono elencati alcune delle tecnologie cross-platform disponibili che consentono di migliorare la produttività come uno sviluppatore di gioco.

## <a name="game-vs-non-game-programming-concepts"></a>Visual Studio di giochi. Concetti di programmazione non di gioco

Lo spostamento in sviluppo di gioco per i programmatori spesso devono affrontare nuovi concetti e modelli di sviluppo. In questa sezione viene fornita una panoramica di alcuni di questi concetti.

### <a name="the-game-loop"></a>Il ciclo del gioco

Un tipico gioco richiede costante spostamento o modifica per la causa del problema sullo schermo in risposta all'interazione dell'utente sia automatica per la logica del gioco. Questo risultato viene ottenuto tramite ciò che viene generalmente indicato come un *ciclo del gioco*. Un ciclo del gioco è un tipo di istruzione (ad esempio un ciclo while) che viene eseguito con una frequenza molto elevata, ad esempio 30 o 60 cicli *fotogrammi al secondo*.

Di seguito è riportato un diagramma di un semplice ciclo del gioco:

![](images/image1.png "Si tratta di un diagramma di un semplice ciclo del gioco")

Le tecnologie di cui parleremo in seguito sottraggono il ciclo while effettivo, ma nonostante questa astrazione sarà presente il concetto di aggiornamenti di ogni fotogramma.

Le prestazioni del codice possono richiedere priorità anche dei giochi più semplici. Ad esempio: una funzione che accetta 10 millisecondi per l'esecuzione potrebbe avere un impatto significativo sulle prestazioni di un gioco, soprattutto se viene chiamato più volte per ogni fotogramma. Se è in esecuzione il tuo gioco a 30 fotogrammi al secondo significa che ogni frame deve essere eseguite in millisecondi in 33. Al contrario, una funzione di questo tipo potrebbe non ancora essere evidente se viene eseguita solo in risposta a un clic del pulsante in un'applicazione non di gioco.

I tipi di logica che può essere eseguita ogni frame comuni includono:

- **Lettura Input** – il gioco può essere necessario controllare se l'utente ha interagito con il gioco verificando input hardware, ad esempio il touchscreen, tastiera, mouse o di gioco.
- **Spostamento** – oggetti quali lo spostamento da un'unica posizione a altra in genere passerà molto piccolo importo ogni fotogramma per conferire un effetto ottico di fluidi.
- **Collisione** – molti giochi richiedono il test frequenti di fatto vari oggetti sono sovrapposti o sovrapposte. Si affronterà il conflitto in modo più approfondito in una sezione più avanti in questo articolo. Lo spostamento e la collisione può essere gestite da un sistema di simulazione fisica dedicata.
- **Controllo delle condizioni di gioco specifico** : lo stato di un gioco può essere controllato dal determinate condizioni, ad esempio se il giocatore ha raggiunto punti sufficienti o se tempo a disposizione è esaurito.
- **Comportamento dell'intelligenza artificiale** : logica di ogni frame che può essere utilizzata per controllare il comportamento degli oggetti che non sono controllati da Windows Media player, ad esempio patrolling di un libro enemy o lo spostamento dei driver avversario intorno a un tavolo.
- **Il rendering** : la maggior parte dei giochi aggiornerà gli elementi visualizzati nella schermata per ogni fotogramma. Questa operazione può essere eseguita in risposta alle modifiche che hanno impatto su gioco (ad esempio, un carattere di spostarsi all'interno di un livello) o semplicemente per offrire perfezionamenti visual (ad esempio, in diminuzione neve o icone animate).

Tenere presente che molte delle attività elencate in precedenza può modificare lo stato dell'intera applicazione, mentre molte app di gioco non tendono a cambiare lo stato in risposta a eventi generati.

### <a name="content-loading-and-unloading"></a>Caricamento del contenuto e lo scaricamento

Contenuto manualmente il caricamento e scaricamento (o eliminazione) potrebbe essere necessari in base alla quale tecnologia si usa in fase di sviluppo. Manuale di caricamento e scaricamento di asset possono essere necessarie per una serie di motivi:

- Gli asset possono richiedere molto tempo per caricare relativamente alla lunghezza di un singolo frame. Alcuni asset può anche richiedere secondi per caricare, quale risulterebbe notevolmente l'esperienza se caricato la modalità di gioco mid. Se il tempo di caricamento è particolarmente lungo (ad esempio più di uno o due secondi) desiderato visualizzare una schermata o indicatori di stato di caricamento.
- Asset possono utilizzare una grande quantità di RAM, che richiedono la gestione attiva della cosa viene caricato in modo da adattarlo quello fornito dalle piattaforme di destinazione del gioco.
- Giochi potrebbe essere necessario visualizzare più asset di quanto possono adattarsi alla memoria RAM. Giochi "Apri World" includono spesso ambienti di grandi dimensioni, quali i lettori possono spostarsi senza problemi: si tratta delle schermate alcun caricamento. In questo caso occorre creare un sistema personalizzato per il contenuto in streaming e la gestione dell'utilizzo della memoria.

Formati di file personalizzato potrebbe essere necessario l'elaborazione in fase di caricamento, che richiedono il caricamento personalizzato codice.

### <a name="math"></a>Math

Molti giochi richiedono matematica più avanzata rispetto alle applicazioni non di gioco. Naturalmente, il livello dei calcoli matematici dipende dalla complessità del gioco. In genere giochi 3D richiedono ulteriori math rispetto a 2D. Fortunatamente è possibile iniziare sempre con semplici giochi e informazioni su consumo. Sviluppo di giochi può essere un ottimo modo per apprendere matematico.

Se si ha familiarità con il piano cartesiano – che utilizza coordinate X e Y per posizionare gli oggetti e quindi si hanno conoscenze sufficienti per iniziare a usare lo sviluppo di giochi. Di seguito viene illustrato un piano cartesiano con positive Y che punta verso l'alto:

![](images/image2.png "Viene illustrato un piano cartesiano con positive Y che punta verso l'alto")

> [!IMPORTANT]
> Alcuni motori/API usano un sistema di coordinate in cui aumentando valore Y di un oggetto verrà spostarlo verso il basso, mentre altri sistemi utilizzano un sistema di coordinate dove Y positivo sia attivo. Tenere a mente se si stanno spostando tra i sistemi.
Funzioni trigonometriche (ad esempio seno e il coseno) vengono comunemente usate nei giochi 2D che implementano quindi alcuna forma di rotazione.

Se si prevede di sviluppare un gioco 3D si sarà probabilmente necessario acquisire familiarità con i concetti di Algebra lineare (per la rotazione e spostamento nello spazio 3D), nonché alcuni calcolo (per l'implementazione di accelerazione).

### <a name="content-pipelines"></a>Pipeline di contenuti

Il termine *pipeline di contenuti* si riferisce al processo che un file necessario per ottenere dal rispettivo formato quando creato (ad esempio un file di immagine PNG) nel relativo formato finale quando utilizzato in un gioco. Il formato finale dipende sul quale contenuto viene usato il tipo, nonché quale tecnologia viene usata per presentare il contenuto.

Alcuni le pipeline di contenuti possono essere molto veloci e richiedono un intervento manuale. Ad esempio, la maggior parte dei motori di gioco e le API possono caricare il formato di file con estensione png nel relativo formato non elaborato. D'altra parte, potrebbe essere necessario più complicati formati (ad esempio, modelli 3D) da elaborare in un formato diverso prima in fase di caricamento e l'elaborazione può richiedere alcuni minuti a seconda delle dimensioni e la complessità dell'asset.

## <a name="game-development-teams"></a>Team di sviluppo di giochi

Sviluppo di giochi introduce nuovi ruoli e i titoli per gli utenti coinvolti nel processo. La maggior parte degli sviluppatori di giochi non sono in grado di soddisfare l'ampia gamma di competenze necessarie per rilasciare un gioco completo, in modo che esiste un numero di discipline. Tenere presente che questo non è un elenco completo delle aree di sviluppo-solo alcune delle più comuni.

- **Programmatore** : la maggior parte delle persone che legge questo articolo verrà rientrano in questa categoria. Il ruolo del programmatore nello sviluppo di giochi è simile al ruolo del programmatore in un'applicazione non di gioco. Responsabilità del cliente includono la scrittura della logica per controllare il flusso di un gioco, lo sviluppo di sistemi per attività comuni nel contesto di un determinato progetto, aggiunta e la visualizzazione del contenuto e, naturalmente, la correzione di bug.
- **2D artista** – 2D alle creazioni degli artisti sono responsabili della creazione *asset 2D*. Questi includono i file di immagine per interfaccia utente grafica del gioco, particelle, ambienti e caratteri. Se il gioco che si sta sviluppando è 3D, quindi alle creazioni degli artisti 2D potrebbe non essere responsabile per gli ambienti e i caratteri. È possibile trovare gratuito art per il tuo gioco al [ http://opengameart.org/ ](http://opengameart.org/) .
- **3D alle creazioni degli artisti** – gli artisti 3D sono responsabili della creazione *gli asset 3D*. Queste includono modelli 3D per ambienti di caratteri e props (mobili, stabilimenti e altri oggetti inanimati). Alcuni team distingue gli artisti 3D esperti di animazione 3D a seconda delle dimensioni del team. È possibile trovare gratuito grafica 3D per il tuo gioco al [ http://opengameart.org/ ](http://opengameart.org/) .
- **Finestra di progettazione di giochi** -finestre di progettazione del gioco sono responsabili che definisce come viene riprodotto il gioco. Può trattarsi di prendere decisioni ad alto livello, ad esempio l'impostazione di gioco, l'obiettivo complessivo di gioco e come un lettore passa attraverso il gioco. Le finestre di progettazione di giochi possono rivelarsi coinvolti nelle decisioni molto dettagliate, ad esempio input mapping alle azioni, che definisce i coefficienti per lo spostamento o a livello di continuità e la progettazione di layout a livello. Tenere presente che il termine *progettazione* può fare riferimento a una finestra di progettazione del gioco o una finestra di progettazione visiva in base al contesto.
- **Finestra di progettazione di suoni** – audio finestre di progettazione sono responsabili per gli asset audio del gioco. Alcuni team può distinguere tra personale responsabile della creazione di effetti sonori e biografie, anche se i team più piccoli possono avere un singolo utente responsabile per tutti i file audio.

## <a name="creating-a-game-idea"></a>Creazione di un'Idea del gioco

Progettazione di un gioco risulti facili da eseguire altre operazioni dopo che tutti l'unico requisito è "lo qualificano divertente". Sfortunatamente, molti sviluppatori trovano in perdita quando giunge il momento di creare un'idea dal quale avviare lo sviluppo.

La disciplina di progettazione di giochi non facilmente include una descrizione e richiede procedure consigliate per migliorare proprio come l'arte o programmazione viene, ma in questa sezione è utili per iniziare il percorso verso il basso.

Nuovi sviluppatori devono iniziare con dimensioni ridotte. Può essere difficile resistere alla tentazione di ricreare un gioco di video di grandi dimensioni e moderno, ma più piccoli giochi possono essere un ambiente formativo migliorato e rende lo stato di avanzamento veloce per un'esperienza più gratificante.

Molti giochi, entrambi allo scopo di apprendimento, nonché commerciale giochi, iniziano come analisi utilizzo software o modifiche a un gioco esistente. Uno per generare le idee consiste nell'esaminare altri giochi per trovare l'ispirazione. Ad esempio, è possibile considerare un gioco che si desidera che personalmente e provare a identificare le caratteristiche di gioco rendono divertente. Può trattarsi di esplorazione, la padronanza dei meccanismi del gioco o prosegue lungo una storia. Non dimenticare di prendere in considerazione anche giochi "retrò" durante la ricerca di nuove idee.

Un'altra tecnica per la generazione di nuove idee consiste nel considerare un genere specifico, ad esempio rompicapo giochi, giochi di strategia o platformers. Un genere familiare agli sviluppatori potrebbe fornire un buon punto di partenza.

Per rieseguire giochi esistenti è anche un'esperienza di apprendimento, anche se tale condizione può limitare la vitalità commerciale del prodotto finito. Il processo di creazione di un gioco, anche uno che è un clone accurato, offre un'esperienza didattico preziosa.

## <a name="game-development-technology"></a>Tecnologia di sviluppo di giochi

Gli sviluppatori che usano Xamarin.Android e Xamarin.IOS hanno una vasta gamma di tecnologie disponibili per facilitare lo sviluppo di giochi. In questa sezione verrà illustrate alcune delle più diffuse soluzioni multipiattaforma.

### <a name="monogame"></a>MonoGame

MonoGame è un open-source, multipiattaforma versione della piattaforma dell'API di Microsoft XNA. MonoGame è utilizzabile per creare giochi per iOS, Android, Mac OS X, Linux, Windows, Windows RT, PS4, PSVita, Xbox One e commutatore.

MonoGame è tecnicamente non un motore gioco, ma piuttosto un sviluppo di giochi API. Ciò significa che l'utilizzo con MonoGame richiede direttamente la gestione degli oggetti giochi manualmente oggetti disegno e implementazione di oggetti comuni quali fotocamere e *grafici scena* (gerarchia padre-figlio tra gli oggetti gioco).

MonoGame non offre un ambiente di sviluppo visivo standard, in modo che l'utilizzo con MonoGame richiede la conoscenza di programmazione.

Esempi significativi di giochi con MonoGame includono:

FEZ:

![](images/image7.png "FEZ")

Bastion:

![](images/image8.jpg "Bastion")

Per iniziare a lavorare con MonoGame, passare a nostro [Guide MonoGame](~/graphics-games/monogame/index.md).

### <a name="urhosharp"></a>UrhoSharp

UrhoSharp è una multipiattaforma ad alto livello 2D e 3D motore può essere utilizzato per creare scene 2D e 3D animate per le applicazioni che usano le geometrie, materiali, luci e fotocamere.

![](images/urhosharp.gif "UrhoSharp è una multipiattaforma ad alto livello 2D e 3D motore che può essere utilizzato per creare scene 3D e 2D animate")

Consultare il [guide di UrhoSharp](~/graphics-games/urhosharp/index.md) per iniziare.

### <a name="additional-technology"></a>Tecnologie aggiuntive

Le tecnologie evidenziate sopra è solo un esempio delle tecnologie disponibili. Altre tecnologie rilevanti includono:

- **Spritekit** : Xamarin offre supporto per framework gioco Spritekit, Apple che consente di accedere a tutte le funzionalità dell'API native. Poiché Spritekit è la tecnologia di Apple, offre una stretta integrazione con il resto dell'ecosistema di iOS. Naturalmente, Spritekit non lo sviluppo multipiattaforma in modo che non può essere usato in Android. Per altre informazioni sull'uso Spritekit, vedere questo post di:  [https://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/](https://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/)
- **Scenekit** : Xamarin offre inoltre supporto per framework di Scenekit Apple, che semplifica l'implementazione di grafica 3D nelle App iOS. Scenekit è anche la tecnologia fornita da Apple, pertanto dispone di integrazione e considerazioni specifiche della piattaforma indicato in precedenza per Spritekit. Per altre informazioni sul Kit di scena, vedere questo post di: [https://blog.xamarin.com/3d-in-ios-8-with-scene-kit/](https://blog.xamarin.com/3d-in-ios-8-with-scene-kit/)
- **OpenTK –** OpenTK (che è l'acronimo di Open Tool Kit) offre accesso OpenGL a basso livello per iOS, Apple e Mac hardware. Per altre informazioni su OpenTK, vedere la pagina principale in:  [http://www.opentk.com/](http://www.opentk.com/)

## <a name="related-links"></a>Collegamenti correlati

- [Guide MonoGame](~/graphics-games/monogame/index.md)
- [Guide di UrhoSharp](~/graphics-games/urhosharp/index.md)
