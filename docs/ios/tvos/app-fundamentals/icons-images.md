---
title: Uso delle icone e delle immagini di tvOS in Novell
description: Questo documento descrive come usare icone e immagini in un'app tvOS compilata con Novell. Illustra le immagini di avvio, le immagini sovrapposte, l'icona dell'app e altro ancora.
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 7eca637e546324f9282aedcac48dacffa72bf246
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769265"
---
# <a name="working-with-tvos-icons-and-images-in-xamarin"></a>Uso delle icone e delle immagini di tvOS in Novell

La creazione di icone e immagini accattivanti è una parte essenziale dello sviluppo di un'esperienza utente immersiva per le app di Apple TV. Questa guida illustra i passaggi necessari per creare e includere le risorse grafiche necessarie per le app Novell. tvOS:

- [Immagine di avvio](#Launch-Image) : un'immagine di avvio viene visualizzata quando l'app viene avviata per la prima volta e viene sostituita dalla prima schermata dell'app dopo il completamento dell'avvio.
- [Immagini sovrapposte](#Layered-Images) , specifiche per Apple TV, le nuove immagini a più livelli di Apple funzionano con l'effetto parallasse per creare un effetto 3D per gli elementi selezionati. Esistono diversi modi per [creare immagini sovrapposte](#Creating-Layered-Images).
- [Icona dell'app](#App-Icons) : le icone sono necessarie solo per la schermata iniziale di Apple TV ma per l'App Store. Devono essere fornite come immagine a più livelli.
- [Image shelf top](#Top-Shelf-Image) : se l'app viene posizionata nella parte superiore della schermata iniziale, sarà necessaria un'immagine in primo piano per evidenziare le funzionalità dell'app. Facoltativamente, è possibile fornire [contenuto dinamico Top Shelf](#Dynamic-Top-Shelf-Content) per evidenziare il contenuto nell'app.
- [Immagini Game Center](#Game-Center-Images) : se l'app è un gioco e USA Game Center, saranno necessarie diverse immagini aggiuntive.
- [Impostazione delle immagini del progetto Novell. tvOS](#Setting-Xamarin.tvOS-Project-Images) : vengono illustrati i passaggi necessari per impostare l'immagine di avvio e l'icona dell'app per l'app Novell. tvOS.

> [!IMPORTANT]
> Tutte le immagini in Apple TV sono con risoluzione 1x (`@1x`) ed è consigliabile usare _solo_ immagini di queste dimensioni. Anche la grafica più grande e a risoluzione superiore non solo può richiedere tempo per il download e l'uso di più memoria e archiviazione, ma deve essere ridimensionata dinamicamente in fase di esecuzione e influisce negativamente sulle prestazioni del disegno.

<a name="Launch-Image" />

## <a name="launch-image"></a>Immagine di avvio

L'immagine di avvio è la prima cosa che viene visualizzata quando l'app Novell. tvOS viene avviata inizialmente in Apple TV e, di conseguenza, ogni app tvOS deve fornire un'immagine di avvio. 

L'immagine di avvio viene visualizzata rapidamente e offre l'impressione che l'applicazione sia veloce e reattiva. Apple TV sostituirà l'immagine di avvio con la prima schermata dell'app subito dopo.

Le immagini di avvio non sono un'opportunità per gli annunci o l'espressione artistica. sono disponibili solo per dare l'impressione che l'app venga avviata rapidamente ed è pronta per essere usata.

|Dimensioni immagine di avvio|Note|
|---|---|
|1920x1080px|Solo file con estensione png non sovrapposti|

Apple apporta i suggerimenti seguenti per progettare l'immagine di avvio dell'app:

- **Quasi identico alla prima schermata** : la schermata di avvio dovrebbe essere il più vicino possibile alla prima schermata dell'app. L'inclusione di elementi grafici o elementi diversi può comportare un "Flash" fastidioso quando viene visualizzata la prima schermata.
- **Evitare di utilizzare** le immagini di avvio del testo sono statiche e, di conseguenza, non verranno localizzate prima di essere visualizzate.
- Sottolineare **Launch** : poiché gli utenti di Apple TV passano spesso alle app, non è necessario attirare l'attenzione sul processo di avvio dell'app.
- **Nessun annuncio o personalizzazione** : l'immagine di avvio non deve essere usata come schermata informazioni o includere una personalizzazione, a meno che non si tratti di una parte statica della prima schermata dell'app. Gli annunci sono rigorosamente proibiti.

<a name="Setting-the-Launch-Image" />

### <a name="setting-the-launch-image"></a>Impostazione dell'immagine di avvio

Per impostare l'immagine di avvio per il progetto tvOS, eseguire le operazioni seguenti:

1. Nella **Esplora soluzioni**fare doppio clic `Assets.xcassets` per aprirlo per la modifica: 

    [![](icons-images-images/asset01.png "Il file assets. xcassets")](icons-images-images/asset01.png#lightbox)
2. Nell' **Editor asset**fare clic sull' `LaunchImages` asset: 

    [![](icons-images-images/asset02.png "Asset immagini")](icons-images-images/asset02.png#lightbox)
3. Fare clic sulla voce **1x Apple TV** e selezionare l'immagine di avvio oppure trascinare una nuova immagine dal file System: 

    [![](icons-images-images/asset03.png "Selezionare un'immagine di avvio")](icons-images-images/asset03.png#lightbox)
4. Salvare le modifiche.

<a name="Layered-Images" />

## <a name="layered-images"></a>Immagini sovrapposte

Una novità della Apple TV, le immagini sovrapposte funzionano con l'effetto di parallasse per produrre un effetto 3D che consente di rendere l'utente sul divano mentalmente connesso al contenuto sullo schermo attraverso la stanza.

Le immagini sovrapposte contengono da due (2) a cinque (5) livelli distinti combinati per formare un'immagine completa. Con l'eccezione del livello di sfondo, ogni livello usa l'ordine Z insieme alla trasparenza per creare un effetto ottico di profondità. Quando l'utente interagisce con un'immagine sovrapposta, i livelli superiori ordinati Z vengono ridimensionati e sovrapposti per creare questo effetto.

[![](icons-images-images/layered01.png "Immagine sovrapposta Z-diagramma ordinato")](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> Le immagini sovrapposte sono necessarie per le icone dell'app e sono facoltative per altri [elementi attivabili](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) (ad esempio, l'immagine in primo piano). Tuttavia, Apple suggerisce di usare immagini sovrapposte per qualsiasi immagine che può ottenere lo stato attivo nell'app.

Apple apporta i suggerimenti seguenti per progettare le immagini a più livelli:

- **Rendere opaco il livello di sfondo** : il livello di sfondo (livello 1) **deve** essere opaco o verrà ricevuto un errore quando si tenta di usare l'immagine sovrapposta in Apple TV. Tutti gli altri livelli possono contenere più livelli di trasparenza per migliorare l'effetto 3D.
- **Isolare gli elementi in primo piano e in background,** posizionando gli elementi prominenti (come i caratteri del gioco) in primo piano, usare il centro per gli elementi secondari o le ombreggiature. Includere infine uno sfondo neutro per fornire una fase per i livelli superiori.
- **Mantieni il testo in primo piano** , a meno che il testo non venga nascosto in base a livelli più elevati, in genere deve trovarsi nel livello superiore.
- **Usare un semplice layering** : l'effetto di parallasse è stato progettato per essere impercettibile, in modo da rendere i livelli minimi per prevenire effetti stonati e non realistici.
- **Includere un'area sicura** : poiché i livelli superiori possono essere ritagliati durante un effetto di parallasse, è necessario creare un bordo della zona sicura in ogni livello. Se si ottiene il contenuto troppo vicino al bordo dei livelli, è possibile che venga ritagliato. I livelli superiori comportano una maggiore scalabilità e ritaglio rispetto ai livelli inferiori. Vedere la sezione [ridimensionamento dei livelli immagine](#Sizing-Image-Layers) di seguito.
- L' **Anteprima spesso** le immagini a livelli devono essere visualizzate in anteprima spesso per garantire che si verifichi l'effetto 3D desiderato e che nessuno dei contenuti sui singoli livelli venga ritagliato. È necessario visualizzare in anteprima le immagini sovrapposte su hardware Apple TV reale per assicurarsi che vengano visualizzate come previsto.

Quando possibile, è consigliabile usare sempre i `UIKit` controlli predefiniti per visualizzare le immagini sovrapposte, in quanto otterranno automaticamente l'effetto di parallasse quando entrano nello stato attivo.

<a name="Sizing-Image-Layers" />

### <a name="sizing-image-layers"></a>Ridimensionamento di livelli di immagine

È importante ricordare di includere un bordo della _zona sicura_ in ogni livello che comporrà l'immagine a più livelli. Poiché i singoli livelli possono essere ridimensionati e ritagliati durante l'effetto di parallasse, il contenuto dei livelli può essere ritagliato se è troppo vicino al bordo del livello:

[![](icons-images-images/layered02.png "bordo pixel 35")](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images" />

### <a name="creating-layered-images"></a>Creazione di immagini sovrapposte

tvOS funziona con le immagini sovrapposte nei formati seguenti:

- **File auto** : formato di catalogo asset proprietario creato da Apple. Non è possibile creare direttamente i file dell'auto, che vengono creati in fase di compilazione da qualsiasi file LSR e inclusi nel bundle dell'app.
- **LSR** images: formato di immagine proprietario creato da Apple. Usare il plug-in di [Parallax Exporter Adobe Photoshop](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip) o il Visualizzatore anteprima di [parallasse](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) per creare e visualizzare in anteprima le immagini a livelli nel formato LSR.
- **Assets. xcassets** : da due (2) a cinque (5) `.png` immagini formattate standard incluse in un catalogo asset che verranno compilate in un'immagine in formato auto o LSR in fase di compilazione.
- **File LCR** : formato di file proprietario creato da Apple. I file LCR sono progettati per essere utilizzati come contenuti aggiuntivi scaricati da uno dei server di contenuti. Il file LCR non deve mai essere incluso nel bundle dell'app. I file LCR vengono generati da file LSR o Photoshop usando `layerutil` lo strumento da riga di comando incluso in Xcode.

<a name="The-Parallax-Previewer" />

### <a name="the-parallax-previewer"></a>Visualizzatore anteprima di parallasse

Apple ha creato il Visualizzatore anteprima di [parallasse](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) per l'anteprima e la creazione di immagini sovrapposte necessarie per le icone delle app e gli elementi facoltativi attivabili. Il Visualizzatore anteprima Mostra ogni livello che costituisce l'immagine sovrapposta completata:

[![](icons-images-images/layered03.png "Visualizzatore anteprima di parallasse")](icons-images-images/layered03.png#lightbox)

Quando si visualizza in anteprima un'immagine sovrapposta, è possibile usare il mouse per ruotare l'immagine e visualizzare in anteprima l'effetto di parallasse. Usare i **+** pulsanti (più) **-** e (meno) per aggiungere e rimuovere i livelli.

Quando si crea una nuova immagine sovrapposta, può essere esportata nel formato LSR e inclusa nel bundle dell'app.

Per altre informazioni sulla creazione e l'anteprima delle immagini sovrapposte, vedere la sezione relativa alla [creazione di Parallax artwork](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1) nella [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/).

<a name="App-Icons" />

## <a name="app-icons"></a>Icone dell'app

L'app Novell. tvOS richiederà non solo un'icona dell'app per la schermata iniziale di Apple TV, ma anche un'icona per l'App Store. L'icona dell'app è la prima modifica che consente di ottenere un'impressione eccezionale sul potenziale utente e deve comunicare a colpo d'occhio lo scopo dell'app.

[![](icons-images-images/icon01.png "Icona dell'app")](icons-images-images/icon01.png#lightbox)

Ogni app deve fornire una versione di piccole e grandi dimensioni dell'icona dell'app. L'icona piccola verrà usata nella schermata iniziale di Apple TV quando viene installata l'app. La versione di grandi dimensioni viene usata dall'App Store. L'icona dell'app di grandi dimensioni dovrebbe simulare l'aspetto della versione dell'icona piccola.

|Icona piccola||Icona grande||
|---|---|---|---|
|Dimensioni effettive|400x240px|Dimensione|1280x768px|
|Dimensioni zona sicure|370x222px|||
|Dimensioni con stato non attivo|300x180px|||
|Dimensioni con stato attivo|370x222px|||

> [!IMPORTANT]
> Le icone dell'app devono essere fornite come **immagini sovrapposte**. Per altri dettagli, vedere la sezione relativa all' [immagine sovrapposta](#Layered-Images) sopra.

Apple fornisce i suggerimenti seguenti per la creazione delle icone dell'app:

- **Fornire un singolo punto di interesse** : progettare l'icona con un singolo punto di messa a fuoco situato direttamente al centro dell'icona.
- **Usare uno sfondo semplice** : è sufficiente lasciare lo sfondo dell'icona in modo che i livelli superiori si trovino. Prendere in considerazione l'uso di un colore semplice o di una sfumatura sottile.
- **Limitare la quantità di testo** : poiché il nome dell'app viene visualizzato sotto l'icona quando viene selezionato dall'utente, è necessario includere solo il testo quando è essenziale per la progettazione dell'icona.
- **Non usare schermate** : le schermate sono troppo complesse per un'icona e non consentono all'utente di visualizzare lo scopo dell'app a colpo d'occhio.
- **Mantieni icone quadre** : tvOS applica automaticamente una maschera che arrotonda in modo impercettibile gli angoli delle icone. Non includere l'arrotondamento.
- **Separare attentamente i livelli** : il testo deve trovarsi sul livello superiore, gli elementi secondari al centro e uno sfondo neutro che consente di brillare i livelli superiori.
- **Utilizzare attentamente le sfumature e le ombreggiature** : le sfumature e le ombreggiature possono contrastare con l'effetto di parallasse, in modo da poterle utilizzare con cautela. Gli stili di sfumatura semplice dall'alto in basso funzionano meglio. Le ombreggiature funzionano normalmente come tinte affilate e a taglio rigido.
- **Variazione della trasparenza** dei livelli: usare diversi livelli di trasparenza nei livelli superiori dell'icona dell'app per aumentare l'effetto 3D. Il livello di sfondo deve essere opaco o verrà generato un errore.

<a name="Setting-the-App-Icons" />

### <a name="setting-the-app-icons"></a>Impostazione delle icone dell'app

Per impostare le icone dell'app necessarie per il progetto tvOS, eseguire le operazioni seguenti:

1. Nella **Esplora soluzioni**fare doppio clic `Assets.xcassets` per aprirlo per la modifica: 

    [![](icons-images-images/asset01.png "Assets. xcassets fileg")](icons-images-images/asset01.png#lightbox)
2. Nell' **Editor asset**espandere l' `App Icon & Top Shelf Image` asset: 

    [![](icons-images-images/asset04.png "Espandi l'asset immagine in primo piano")](icons-images-images/asset04.png#lightbox)
3. Espandere quindi l' `App Icon - Small` asset: 

    [![](icons-images-images/asset05.png "Espandere l'icona dell'app-Small asset")](icons-images-images/asset05.png#lightbox)
4. Espandere quindi l' `Back` asset e fare clic `Contents` sulla voce: 

    [![](icons-images-images/asset06.png "Espandere quindi il back asset")](icons-images-images/asset06.png#lightbox)
5. Fare clic sulla **voce 1x Apple TV** e selezionare un file di immagine.
6. Ripetere i passaggi precedenti per gli `Front` asset `Middle` e.
7. Ripetere quindi gli stessi passaggi per definire l' `App Icon - Large` asset.
8. Salvare le modifiche.

<a name="Top-Shelf-Image" />

## <a name="top-shelf-image"></a>Immagine in primo piano

Se l'utente ha inserito l'app Novell. tvOS nella parte superiore della schermata iniziale di Apple TV, viene visualizzata un'immagine di grandi dimensioni superiore quando l'utente seleziona l'app. Questa immagine dovrebbe evidenziare le funzionalità dell'app o fornire collegamenti diretti al relativo contenuto.

[![](icons-images-images/topshelf01.png "Esempio di immagine in primo piano")](icons-images-images/topshelf01.png#lightbox)

L'immagine in primo piano può essere fornita come un singolo file `.png` statico `.lsr` o (vedere [creazione di immagini](#Creating-Layered-Images)a più livelli) oppure può essere creata dinamicamente in fase di esecuzione come una singola riga di elementi attivabili (vedere il [contenuto dinamico Top Shelf](#Dynamic-Top-Shelf-Content) più avanti).

|Dimensioni immagine in primo piano|Note|
|---|---|
|1920x720px|File static. png o. LSR a livelli|

Apple fornisce i suggerimenti seguenti per la creazione di immagini Top Shelf:

- **Usa immagini statiche avanzate** : se l'app non fornisce un contenuto dinamico, la relativa immagine rimessa in primo piano sarà non attivabile. Usare questa immagine per evidenziare le funzionalità dell'app o del marchio.
- **Collegamento al contenuto dell'app** : i layout di scaffale dinamici principali forniscono un collegamento rapido al contenuto che l'utente trova più importante nell'app. Usare quest'area per fornire un collegamento rapido per avviare l'app e passare immediatamente al contenuto specificato.
- **Presentazione del contenuto più recente: il contenuto più completo della** piattaforma può creare un utente nell'app e renderlo più utile. Usare questa come area per presentare il contenuto più alto valutato o più recente.
- **Contenuto personalizzato** : gli utenti inseriscono le app più frequenti o preferite nella prima riga della schermata iniziale. Usare il ripiano superiore per visualizzare il contenuto a cui si è maggiormente interessati.
- **Annunci non consentiti: gli annunci non sono consentiti** in modo esplicito nella mensola superiore. È possibile visualizzare il contenuto più recente acquistabile, ma non devono essere visualizzate informazioni sui prezzi.

### <a name="setting-the-top-shelf-image"></a>Impostazione dell'immagine in primo piano

Per impostare l'immagine in primo piano necessaria per il progetto tvOS, eseguire le operazioni seguenti:

1. Nella **Esplora soluzioni**fare doppio clic `Assets.xcassets` per aprirlo per la modifica: 

    [![](icons-images-images/asset01.png "Il file assets. xcassets")](icons-images-images/asset01.png#lightbox)
2. Nell' **Editor asset**espandere l' `App Icon & Top Shelf Image` asset: 

    [![](icons-images-images/asset04.png "Espandi l'asset immagine in primo piano")](icons-images-images/asset04.png#lightbox)
3. Fare clic sull' `Top Shelf Image` asset: 

    [![](icons-images-images/asset07.png "Asset immagine in primo piano")](icons-images-images/asset07.png#lightbox)
4. Fare clic sulla **voce 1x Apple TV** e selezionare un file di immagine.
5. Salvare le modifiche.

<a name="Dynamic-Top-Shelf-Content" />

### <a name="dynamic-top-shelf-content"></a>Contenuto dinamico top shelf

Invece di visualizzare un'immagine in primo piano statica, il primo scaffale può contenere una riga dinamica di [elementi attivabili](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) o un set dinamico di banner di scorrimento. Entrambi questi stili dinamici consentono di evidenziare il contenuto fornito dall'app o di passare alle funzionalità più usate.

<a name="Sectioned-Content-Row" />

#### <a name="sectioned-content-row"></a>Riga di contenuto sezionata

Questo tipo di contenuto dinamico Top Shelf presenta una singola riga di scorrimento e gli elementi attivabili sono facoltativamente suddivisi in sezioni. Viene in genere usato per evidenziare il contenuto delle app nuove, preferite o visualizzate di recente.

Il contenuto viene presentato come singolo elenco di scorrimento orizzontale di contenuto con un'etichetta visualizzata sotto la parte corrente del contenuto selezionato (che attualmente ha lo stato attivo). Se l'utente seleziona un determinato contenuto, l'app verrà avviata e verrà diretta direttamente nel contenuto.

Saranno necessarie le seguenti dimensioni del contenuto:

||Poster (2:3)|Quadrato (1:1)|HDTV (16:9)|
|---|---|---|---|
|Dimensioni effettive|404x608px|608x608px|908x512px|
|Dimensioni zona sicure|380x570px|570x570px|852x479px|
|Dimensioni con stato non attivo|333x500px|500x500px|782x440px|
|Dimensioni con stato attivo|380x570px|570x570px|852x479px|

Apple fornisce i suggerimenti seguenti per la riga di contenuto sezionata:

- **Completare la riga** : è necessario fornire un contenuto sufficiente per estendere la larghezza dello schermo.
- **Ridimensionamento di immagini miste** : la riga di contenuto sezionata è stata progettata per mantenere una combinazione di dimensioni delle immagini (dall'elenco fornito sopra). Se si combinano dimensioni delle immagini, tuttavia, tenere presente che verrà applicata la scalabilità aggiuntiva per normalizzare la visualizzazione del contenuto.

<a name="Scrolling-Inset-Banners" />

#### <a name="scrolling-inset-banners"></a>Scorrimento di banner di inserimento

Facoltativamente, l'app Novell. tvOS può presentare il proprio contenuto nel primo scaffale come una raccolta di banner e scorrimento automatico di banner che riempiono quasi lo schermo. Questo stile viene in genere usato per presentare contenuti avanzati, come i nuovi programmi TV.

Oltre allo scorrimento automatico, l'utente può assumere il controllo dei banner e scorrere in entrambe le direzioni usando Siri remote. Facendo un piccolo movimento circolare sul Siri remoto quando un banner è attivo, verrà attivato l'effetto di parallasse per tale banner.

**Immagine banner (molto ampia)**

|   |   |
|---|---|
|Dimensioni effettive|1940x624px|
|Dimensioni zona sicure|1740x620px|
|Dimensioni con stato non attivo|1740x560px|
|Dimensioni con stato attivo|1740x620px|

Lo scorrimento di banner di inserimento può essere fornito come `.png` `.lsr` file statico o a più livelli.

Apple fornisce i suggerimenti seguenti per i banner degli incasso a scorrimento:

- **Quantità di contenuto** : è necessario fornire un minimo di tre (3) banner per far sembrare naturale lo scorrimento. Non è consigliabile includere più di otto (8) banner o rendere la navigazione più complessa per l'utente finale.
- **Testo del contenuto** : se il banner richiede testo in deve essere incluso nell'immagine del banner. Se si usano immagini sovrapposte, il testo deve trovarsi nel livello superiore.

Per altre informazioni sull'aggiunta di un'estensione dello scaffale principale all'app, vedere la Guida di [riferimento a TVServices Framework](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) di Apple per fornire contenuti dinamici in primo piano.

<a name="Game-Center-Images" />

## <a name="game-center-images"></a>Immagini Game Center

Se l'app Novell. tvOS è un gioco e hai incluso Game Center supporto, saranno necessarie diverse risorse per le immagini:

- **Icone di Achievement** : è necessaria un'immagine opaca per ogni risultato che verrà ritagliato automaticamente in un cerchio. Gli obiettivi sono elementi non attivabili.
- **Artwork del dashboard** : è possibile specificare un'immagine facoltativa che verrà visualizzata nella parte superiore del dashboard dell'app all'interno Game Center. Queste immagini non sono focalizzabili.
- **Artwork della classifica** : è necessario specificare tra una (1) e tre (3) 16:9 di immagini con proporzioni per ogni Leaderboard supportata dall'app. Possono essere `.png` `.lsr` file statici o a livelli. L'illustrazione della classifica è attivabile.

||Icone di Achievement|Illustrazioni del dashboard|Grafica Leaderboard|
|---|---|---|---|
|Dimensioni visibili|200x200px|923x150px|n/d|
|Dimensioni effettive|320x320px|n/d|659x371px|
|Dimensioni zona sicure|n/d|n/d|618x348px|
|Dimensioni con stato non attivo|n/d|n/d|548x309px|
|Dimensioni con stato attivo|n/d|n/d|618x348px|

Per ulteriori informazioni sull'utilizzo di Game Center, vedere la guida alla [programmazione Game Center](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html)di Apple.

<a name="Working-with-Images" />

## <a name="working-with-images"></a>Working with Images (Uso delle immagini)

Poiché tvOS 9 è un subset di iOS 9, le stesse tecniche usate per includere e visualizzare immagini in un'app Novell. iOS funzionano anche per un'app Novell. tvOS. Per ulteriori informazioni, vedere la documentazione di [visualizzazione di un'immagine](~/ios/app-fundamentals/images-icons/displaying-an-image.md) .

<a name="Setting-Xamarin.tvOS-Project-Images" />

## <a name="setting-xamarintvos-project-images"></a>Impostazione delle immagini del progetto Novell. tvOS

Come indicato in precedenza, tutte le app tvOS richiedono un' [immagine di avvio](#Launch-Image)e un' [icona dell'app](#App-Icons). Questa sezione illustra come selezionare l'immagine di avvio e l'icona dell'app per il progetto di app Novell. tvOS dopo che sono state impostate in un catalogo asset.

Seguire questa procedura:

1. Nella **Esplora soluzioni**fare doppio clic su `Info.plist` per aprirlo per la modifica: 

    [![](icons-images-images/info01.png "File INFO. plist")](icons-images-images/info01.png#lightbox)
2. Nell' **Editor info. plist**selezionare il catalogo assets (configurato in precedenza nella sezione [impostazione delle icone dell'app](#Setting-the-App-Icons) ) per le **Icone dell'app**: 

    [![](icons-images-images/info02.png "Editor info. plist")](icons-images-images/info02.png#lightbox)
3. Selezionare quindi il catalogo assets (configurato in precedenza nella sezione [Setting the launch image](#Setting-the-Launch-Image) ) per le **Immagini di avvio**.
4. Salvare le modifiche.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha trattato tutti i tipi di immagine e le dimensioni usati in un'app Novell. tvOS. In primo luogo, sono state analizzate immagini di avvio, immagini sovrapposte, icone dell'app, immagini di scaffale e immagini Game Center. Quindi è stato analizzato l'uso delle immagini nell'app Novell. tvOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
