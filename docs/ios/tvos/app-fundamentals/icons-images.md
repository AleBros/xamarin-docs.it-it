---
title: Utilizzo di tvOS icone e immagini in Xamarin
description: Questo documento descrive come usare le icone e immagini in un'app tvOS compilate con Xamarin. Illustra le immagini di avvio, le immagini sovrapposte, l'icona dell'app e altro ancora.
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: dab1b0f7bf7aabb4dfcfbfdcb5e202baa48e664d
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865159"
---
# <a name="working-with-tvos-icons-and-images-in-xamarin"></a>Utilizzo di tvOS icone e immagini in Xamarin

Creazione di accattivante icone e immagini sono un aspetto essenziale dello sviluppo di un'esperienza utente coinvolgenti per le app Apple TV. Questa guida illustra i passaggi necessari per creare e includere le risorse grafiche necessarie per le app xamarin. tvos:

- [Immagine di avvio](#Launch-Image) -un'immagine di avvio viene visualizzato quando l'app prima di tutto viene avviato e viene sostituito da prima schermata dell'app dopo aver completato l'avvio.
- [Immagini su più livelli](#Layered-Images) : specifiche di Apple TV, nuovo lavoro immagini a più livelli di Apple con l'effetto di parallasse per creare un effetto 3D per gli elementi selezionati. Esistono diversi modi per [creare le immagini sovrapposte](#Creating-Layered-Images).
- [Icona dell'app](#App-Icons) -le icone sono necessarie per la schermata non solo Apple TV Home, ma non per l'App Store. Essi deve essere fornite come un'immagine a più livelli.
- [Primi immagine in evidenza](#Top-Shelf-Image) -se l'app viene posizionato sulla riga superiore della schermata iniziale, è necessario un'immagine in evidenza per evidenziare le funzionalità dell'app. Facoltativamente, è possibile fornire [il contenuto dinamico scaffale superiore](#Dynamic-Top-Shelf-Content) per evidenziare il contenuto nell'app.
- [Game Center immagini](#Game-Center-Images) -se l'app è un gioco e utilizza Game Center, numerose immagini aggiuntive sono necessarie.
- [Impostazione di immagini di progetto xamarin. tvos](#Setting-Xamarin.tvOS-Project-Images) -illustra i passaggi necessari per impostare l'immagine di avvio e l'icona dell'App per le app xamarin. tvos.

> [!IMPORTANT]
> Tutte le immagini in Apple TV sono la risoluzione 1 x (`@1x`) e dovrebbe _solo_ usare immagini di queste dimensioni. Tra cui dimensioni maggiori, grafica con risoluzione superiore non solo è opportuno scaricare e usare più memoria e archiviazione, ma dovranno essere ridimensionate in modo dinamico in fase di esecuzione e influiranno negativamente le prestazioni di disegno.

<a name="Launch-Image" />

## <a name="launch-image"></a>Immagine di avvio

L'immagine di avvio è la prima cosa che viene visualizzata quando l'app xamarin. tvos viene inizialmente avviato sull'Apple TV e di conseguenza, ogni app tvOS deve fornire un'immagine di avvio. 

L'immagine di avvio viene visualizzato rapidamente e dando l'impressione che l'app è veloce e reattivo. Apple TV sostituirà l'immagine di avvio con la prima schermata dell'app al più presto vi dopo.

Immagini di avvio non è un'opportunità per gli annunci o espressione artistico, esistono solo per dare l'impressione che l'app avvia rapidamente e sia pronto da usare.

|Dimensioni immagine di avvio|Note|
|---|---|
|1920x1080px|Solo i file con estensione PNG a più livelli non|

Apple rende i suggerimenti seguenti per la progettazione di immagini di avvio dell'app:

- **Quasi identico alla prima schermata** -Your schermata di avvio deve essere il più vicino prima schermata dell'app come possibili. Tra cui immagini grafiche diverse o elemento può comportare un indesiderate "flash" quando viene visualizzata la prima schermata.
- **Evitare usando testo** -immagini di avvio sono statiche e di conseguenza, non verrà localizzate prima di essere visualizzato.
- **Avvio di tralasciare** -gli utenti perché Apple TV passano frequentemente l'App, è consigliabile non attirare l'attenzione sul processo di avvio delle app.
- **Senza annunci o personalizzazione** -immagine di avvio non deve essere utilizzato come una schermata di informazioni o includere qualsiasi personalizzazione, a meno che non fa parte statica della prima schermata dell'app. Non è assolutamente consentiti annunci.

<a name="Setting-the-Launch-Image" />

### <a name="setting-the-launch-image"></a>L'impostazione dell'immagine di avvio

Per impostare l'immagine di avvio per il progetto tvOS, attenersi alla procedura seguente:

1. Nel **Esplora soluzioni**, fare doppio clic su `Assets.xcassets` per aprirlo e modificarlo: 

    [![](icons-images-images/asset01.png "Il file assets. xcassets")](icons-images-images/asset01.png#lightbox)
2. Nel **Editor Asset**, fare clic su di `LaunchImages` asset: 

    [![](icons-images-images/asset02.png "L'asset LaunchImages")](icons-images-images/asset02.png#lightbox)
3. Fare clic sui **1x Apple TV** voce e selezionare l'immagine di avvio o, facoltativamente, trascinare una nuova immagine dal file system: 

    [![](icons-images-images/asset03.png "Selezionare un'immagine di avvio")](icons-images-images/asset03.png#lightbox)
4. Salvare le modifiche.

<a name="Layered-Images" />

## <a name="layered-images"></a>Immagini sovrapposte

Familiarità con Apple TV, lavoro immagini a più livelli con l'effetto di parallasse per produrre un effetto 3D che consente di mantenere l'utente da usare sul divano mentalmente connesso per il contenuto della schermata tutta la stanza.

Le immagini sovrapposte contengono da due (2) a cinque (5) separare i livelli che vengono combinati per formare un'immagine completa. Fatta eccezione per il livello di sfondo, ogni livello Usa l'ordine Z con trasparenza per creare un effetto ottico di profondità. Quando l'utente interagisce con un'immagine a più livelli, livelli superiori di ordine Z vengano ridimensionati e overlapped per creare questo effetto.

[![](icons-images-images/layered01.png "Diagramma di ordine Z di immagini a più livelli")](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> Le immagini sovrapposte sono necessari per le icone dell'app e sono facoltative per altri [elementi con stato attivabile](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) (ad esempio, l'immagine in evidenza). Tuttavia, Apple suggerisce l'uso di immagini a più livelli per le immagini che è possano ottenere lo stato attivo nell'app.




Apple rende i suggerimenti seguenti per la progettazione delle immagini a più livelli:

- **Rendere il livello di sfondo opaco** -il livello di sfondo (livello 1) **necessario** essere opache o si verificherà un errore quando si prova a usare l'immagine a più livelli in Apple TV. Tutti gli altri livelli possono contenere più livelli di trasparenza per migliorare l'effetto 3D.
- **Isolare in primo piano, intermedio e gli elementi in Background** -posizionare gli elementi principali (ad esempio giochi caratteri) in primo piano, utilizzare il centro per gli elementi secondari o ombreggiature. Infine, includere uno sfondo neutro per garantire una fase per i livelli superiori.
- **Mantenere il testo in primo piano** -a meno che non si desidera che il testo per essere nascosto da livelli superiori, in genere deve essere nel livello superiore.
- **Usare livelli semplice** -l'effetto di parallasse è stato progettato per essere minima, pertanto, mantenere i livelli a un minimo per evitare effetti jarring, se sono.
- **Includere una zona Safe** -perché durante un effetto di parallasse possono essere ritagliati livelli superiori, è necessario per compilare un bordo l'area di sicurezza in ogni livello. Se viene visualizzato il contenuto troppo vicini al bordo di livelli, è possibile diventare ritagliato. Livelli superiori si verificheranno altre e proporzioni rispetto ai livelli inferiori. Vedere le [i livelli dell'immagine di ridimensionamento](#Sizing-Image-Layers) sezione riportata di seguito.
- **Visualizzare in anteprima spesso** -immagini a più livelli deve essere visualizzato in anteprima frequentemente per assicurarsi che si verifica l'effetto 3D desiderato e che viene viene ritagliata none del contenuto nei singoli livelli. È necessario visualizzare in anteprima le immagini a più livelli su hardware Apple TV effettivo per assicurarsi che il rendering come previsto.

Quando possibile, è necessario utilizzare sempre l'elemento predefinito `UIKit` controlli per visualizzare le immagini a più livelli, mano a mano che verranno automaticamente l'effetto di parallasse quando arrivano messa a fuoco.

<a name="Sizing-Image-Layers" />

### <a name="sizing-image-layers"></a>Livelli dell'immagine di ridimensionamento

È importante ricordare di includere un _l'area di sicurezza_ bordo in ogni livello per comporre l'immagine a più livelli. Perché i singoli livelli possono essere ridimensionati e ritagliati durante l'effetto di parallasse, il contenuto dei livelli può essere ritagliato se risulta troppo vicino al bordo del livello:

[![](icons-images-images/layered02.png "bordo 35 pixel")](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images" />

### <a name="creating-layered-images"></a>Creazione di immagini su più livelli

tvOS funziona con le immagini a più livelli nei formati seguenti:

- **File AUTOMOBILE** -si tratta di un formato proprietario catalogo Asset creato da Apple. Non creare direttamente i file AUTOMOBILE, vengono create in fase di compilazione da qualsiasi file LSR e inclusi nel bundle dell'app.
- **Le immagini LSR** -si tratta di un formato di immagine proprietarie di Apple. Usare la [parallasse Exporter Adobe Photoshop plug-in](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip) o il [Visualizzatore anteprima parallasse](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) per creare e visualizzare in anteprima nel formato LSR immagini a più livelli.
- **Assets. xcassets** : da due (2) a cinque (5) standard `.png` formattate le immagini incluse in un catalogo di Asset che verranno compilate in un'AUTOMOBILE o LSR formattato su più livelli di immagine in fase di compilazione.
- **I file di LCR** -si tratta di un formato di file proprietari creato da Apple. File LCR sono destinati a essere utilizzato come contenuto aggiuntivo scaricato da uno dei server di contenuti. File LCR non deve mai essere incluso nel bundle dell'app. Vengono generati file di LCR dai file di Photoshop o LSR utilizzando il `layerutil` strumento da riga di comando incluso in Xcode.

<a name="The-Parallax-Previewer" />

### <a name="the-parallax-previewer"></a>Il Visualizzatore anteprima parallasse

Apple creato il [Visualizzatore anteprima parallasse](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) anteprima e immagini create su più livelli necessaria per le icone dell'App e gli elementi con stato attivabile facoltativo. Il Visualizzatore anteprima viene visualizzato ogni livello che costituisce l'immagine a più livelli completata:

[![](icons-images-images/layered03.png "The Parallax Previewer")](icons-images-images/layered03.png#lightbox)

Durante l'anteprima di un'immagine a più livelli, è possibile utilizzare il mouse per ruotare l'immagine e visualizzare in anteprima l'effetto di parallasse. Usare la **+** (segno più) e **-** (pulsanti per aggiungere e rimuovere i livelli meno).

Quando si crea una nuova immagine a più livelli, può essere esportato nel formato LSR e inclusi nel bundle dell'app.

Per altre informazioni sulla creazione e la visualizzazione in anteprima le immagini a più livelli, vedere di Apple [creazione grafica parallasse](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1) sezione il [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/).

<a name="App-Icons" />

## <a name="app-icons"></a>Icone dell'App

L'app xamarin. tvos richiederà non solo un'icona di App per la schermata Home Apple TV, ma anche un'icona per l'App Store. Icona dell'App è alla prima modifica per rendere un'ottima impressione di all'utente potenziali e deve comunicare scopo dell'app in modo immediato.

[![](icons-images-images/icon01.png "Icona dell'App")](icons-images-images/icon01.png#lightbox)

Ogni app deve fornire un piccolo sia una versione ingrandita della relativa icona di App. Verrà utilizzata la piccola icona nella schermata di Apple TV Home quando l'app viene installata. Viene utilizzata la versione di grandi dimensioni per l'App Store. Icona dell'App di grandi dimensioni dovrà simulare l'aspetto della versione dell'icona piccola.

|Icona piccola||Icona grande||
|---|---|---|---|
|Dimensioni effettive|400x240px|Dimensione|1280x768px|
|L'area di sicurezza delle dimensioni|370x222px|||
|Dimensione con stato non attivo|300x180px|||
|Dimensione con lo stato attivo|370x222px|||

> [!IMPORTANT]
> Le icone dell'App deve essere fornite come **immagini sovrapposte**. Vedere le [immagine a più livelli](#Layered-Images) sezione precedente per altri dettagli.




Apple offre i seguenti suggerimenti per la creazione di icone dell'App:

- **Fornire un singolo punto di stato attivo** : progettazione dell'icona con un punto di attivazione singola posizionata direttamente al centro dell'icona.
- **Usare un semplice sfondo** : semplicità di sfondo dell'icona in modo da mettere in evidenza i livelli superiori. È consigliabile usare un semplice colore o la sfumatura meno evidente.
- **Limitare la quantità di testo** : dal momento che il nome dell'applicazione verrà visualizzati sotto l'icona quando viene selezionato dall'utente, è necessario includere testo solo quando è essenziale per la progettazione dell'icona.
- **Non usare schermate** – screenshot sono troppo complessi per un'icona e non consente all'utente visualizzare lo scopo dell'app in modo immediato.
- **Mantenere le icone quadrato** – tvOS applica automaticamente una maschera che leggermente consente di arrotondare gli angoli delle icone. Non includere questo arrotondamento manualmente.
- **Separare attentamente livelli Your** – testo deve essere nell'angolo in alto la maggior parte dei livelli, gli elementi secondari nella parte centrale e uno sfondo neutro che consente i livelli superiori per gli sviluppatori.
- **Utilizzare sfumature e ombreggiature con attenzione** – sfumature e ombreggiature possono essere in conflitto con l'effetto di parallasse pertanto deve essere utilizzate con attenzione. Semplice alto verso il basso, a chiaro-scuro sfumatura stili funzionano meglio. Shadows normalmente funzionano meglio come tinte ben strutturate, netti.
- **Variare la trasparenza di livello** : uso di diversi livelli di trasparenza nei livelli superiori dell'icona dell'App per aumentare l'effetto 3D. Il livello di sfondo deve essere opaco o si verificherà un errore.

<a name="Setting-the-App-Icons" />

### <a name="setting-the-app-icons"></a>Impostare le icone dell'App

Per impostare le icone dell'App obbligatorio per il progetto tvOS, attenersi alla procedura seguente:

1. Nel **Esplora soluzioni**, fare doppio clic su `Assets.xcassets` per aprirlo e modificarlo: 

    [![](icons-images-images/asset01.png "Il fileg assets. xcassets")](icons-images-images/asset01.png#lightbox)
2. Nel **Editor Asset**, espandere il `App Icon & Top Shelf Image` asset: 

    [![](icons-images-images/asset04.png "Espandere l'asset di immagine in evidenza")](icons-images-images/asset04.png#lightbox)
3. Espandere quindi la `App Icon - Small` asset: 

    [![](icons-images-images/asset05.png "Espandere l'icona di App - asset di piccole dimensioni")](icons-images-images/asset05.png#lightbox)
4. Espandere quindi il `Back` asset e fare clic su di `Contents` voce: 

    [![](icons-images-images/asset06.png "Espandere quindi l'asset Back")](icons-images-images/asset06.png#lightbox)
5. Fare clic sui **1x voce Apple TV** e selezionare un file di immagine.
6. Ripetere i passaggi precedenti per il `Front` e `Middle` asset.
7. Ripetere gli stessi passaggi per definire il `App Icon - Large` asset.
8. Salvare le modifiche.

<a name="Top-Shelf-Image" />

## <a name="top-shelf-image"></a>Immagine in evidenza

Se l'utente ha effettuato l'app xamarin. tvos sulla riga superiore nella schermata Home Apple TV, un'immagine in evidenza grande verrà visualizzata quando l'app viene selezionato dall'utente. Questa immagine deve evidenziare le funzionalità dell'app o forniscono collegamenti diretti al relativo contenuto.

[![](icons-images-images/topshelf01.png "Esempio di immagine in evidenza superiore")](icons-images-images/topshelf01.png#lightbox)

È possibile specificare l'immagine in evidenza come un singolo valore statico `.png` oppure `.lsr` file (vedere [creazione di immagini a più livelli](#Creating-Layered-Images)) o può essere creato in modo dinamico in fase di esecuzione come una singola riga degli elementi con stato attivabile (vedere [ Contenuto dinamico scaffale superiore](#Dynamic-Top-Shelf-Content) sotto).

|Dimensioni dell'immagine scaffale superiore|Note|
|---|---|
|1920x720px|PNG statici o file .lsr a più livelli|

Apple offre i seguenti suggerimenti per la creazione delle immagini scaffale superiore:

- **Usare immagini statiche Rich** : se l'app non è incluso un contenuto dinamico, relativa immagine in evidenza sarà non attivabile. Usare questa immagine per evidenziare le funzionalità dell'app oppure il tuo marchio.
- **Collegamento al contenuto dell'App** – di layout dinamici scaffale superiore forniscono un collegamento rapido per il contenuto che l'utente trova più importante nell'app. Utilizzare quest'area per fornire un collegamento rapido per avviare l'app e passare immediatamente il contenuto specificato.
- **Presentare il contenuto più recente** – contenuto RTF evidenza può disegnare un utente nell'app e li da usare in altre. Usare come un'area per presentare il contenuto più recente o più votato più alto.
- **Contenuto personalizzato** – luogo agli utenti i più usati o App preferite nella riga superiore della schermata iniziale. Usare l'evidenza per visualizzare il contenuto che sono più interessati.
- **Gli annunci non consentito** – Ads sono proibita venga visualizzata nuovamente nell'evidenza. È possibile mostrare il contenuto più recente acquistabile, ma non devono essere visualizzate alcun informazioni sui prezzi.

### <a name="setting-the-top-shelf-image"></a>Impostare l'immagine in evidenza

Per impostare l'immagine in evidenza necessari per il progetto tvOS, attenersi alla procedura seguente:

1. Nel **Esplora soluzioni**, fare doppio clic su `Assets.xcassets` per aprirlo e modificarlo: 

    [![](icons-images-images/asset01.png "Il file assets. xcassets")](icons-images-images/asset01.png#lightbox)
2. Nel **Editor Asset**, espandere il `App Icon & Top Shelf Image` asset: 

    [![](icons-images-images/asset04.png "Espandere l'asset di immagine in evidenza")](icons-images-images/asset04.png#lightbox)
3. Fare clic su di `Top Shelf Image` asset: 

    [![](icons-images-images/asset07.png "L'asset di immagine in evidenza")](icons-images-images/asset07.png#lightbox)
4. Fare clic sui **1x voce Apple TV** e selezionare un file di immagine.
5. Salvare le modifiche.

<a name="Dynamic-Top-Shelf-Content" />

### <a name="dynamic-top-shelf-content"></a>Contenuto dinamico scaffale superiore

Invece di visualizzare un'immagine in evidenza statico, l'evidenza può contenere una riga dinamica della [elementi con stato attivabile](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) o un set dinamico di scorrimento banner. Entrambi questi stile dinamici consentono evidenziare i contenuti forniti da un'app o un salto nel relativo funzionalità più utilizzate.

<a name="Sectioned-Content-Row" />

#### <a name="sectioned-content-row"></a>Riga di contenuto sezionate

Questo tipo di contenuto dinamico di evidenza presenta una singola riga di scorrimento, attivabile elementi facoltativamente suddivise in sezioni. È in genere viene utilizzato per evidenziare nuovo, preferito o visualizzati per ultimi contenuto dell'app.

Il contenuto viene presentato come un elenco a scorrimento single, orizzontale del contenuto con un'etichetta che vengono visualizzati sotto la parte di contenuto selezionato corrente (che ha attualmente lo stato attivo). Se l'utente seleziona una determinata parte del contenuto, verrà avviata l'app e deve essere eseguite direttamente in tale contenuto.

Dimensioni contenuto seguenti sono necessarie:

||Poster (2:3)|Square (1:1)|HDTV (16:9)|
|---|---|---|---|
|Dimensioni effettive|404x608px|608x608px|908x512px|
|L'area di sicurezza delle dimensioni|380x570px|570x570px|852x479px|
|Dimensione con stato non attivo|333x500px|500x500px|782x440px|
|Dimensione con lo stato attivo|380x570px|570x570px|852x479px|

Apple offre i seguenti suggerimenti per la riga sezionato contenuto:

- **Completare la riga** : È necessario fornire contenuto sufficiente da coprire l'intera larghezza dello schermo.
- **Ridimensionamento di immagini misto** – riga The sezionato contenuto è stato progettato per contenere una combinazione di dimensioni delle immagini (dall'elenco fornito sopra). Se si combinano le dimensioni delle immagini, tuttavia, tenere presente che la scalabilità aggiuntiva verrà applicata per normalizzare la visualizzazione del contenuto.

<a name="Scrolling-Inset-Banners" />

#### <a name="scrolling-inset-banners"></a>Lo scorrimento Inset banner

Facoltativamente, l'app xamarin. tvos può presentare il contenuto in scaffale superiore come automaticamente lo scorrimento e ciclo di raccolta di intestazioni che quasi riempire lo schermo. Questo stile viene in genere usato per presentare contenuto avanzato, nuovo, ad esempio nuovi programmi TV.

Oltre allo scorrimento automatico, l'utente può assumere il controllo di intestazioni e scorrere in entrambe le direzioni utilizzando la remoti per Siri. Apportare una piccola, movimento circolare nel sistema remoto Siri quando lo stato attivo un banner verrà attivato l'effetto di parallasse per tale intestazione.

**Immagine del banner (grandi dimensioni)**

|   |   |
|---|---|
|Dimensioni effettive|1940x624px|
|L'area di sicurezza delle dimensioni|1740x620px|
|Dimensione con stato non attivo|1740x560px|
|Dimensione con lo stato attivo|1740x620px|

Lo scorrimento Inset banner uno è possibile specificare come un valore statico `.png` o a più livelli `.lsr` file.

Apple offre i seguenti suggerimenti per lo scorrimento Inset intestazioni:

- **Quantità di contenuto** -è necessario fornire un minimo di tre (3) banner per lo scorrimento naturali. È consigliabile includere le intestazioni non più di otto (8) o lo spostamento rendere difficile per gli utenti finali.
- **Testo del contenuto** : se l'intestazione richiede testo deve essere incluso nell'immagine del banner. Se si usano immagini a più livelli, il testo deve essere nel livello superiore.

Vedere di Apple [riferimento a Framework TVServices](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) per altre informazioni sull'aggiunta di un'estensione dello scaffale superiore all'App per fornire contenuto dinamico di evidenza.

<a name="Game-Center-Images" />

## <a name="game-center-images"></a>Immagini di Game Center

Se l'app xamarin. tvos è un gioco e sia incluso il supporto di Game Center, sono necessarie più asset immagini contenute:

- **Le icone traguardo** -un'immagine opaco è necessaria per ogni obiettivo che verrà ritagliato automaticamente in un cerchio. Traguardi sono elementi non è attivabile.
- **Dashboard opere** -può essere un'immagine facoltativa, a condizione che verrà visualizzato nella parte superiore del dashboard dell'app all'interno di Game Center. Queste immagini sono non attivabili.
- **Tabellone punteggi opere** -è necessario specificare tra uno (1) per tre (3) le immagini di proporzioni 16:9 per ogni gruppo di punteggi supportata dall'app. Questi possono essere statiche `.png` o a più livelli `.lsr` file. La grafica classifica è attivabile.

||Icone di realizzazione|Oggetto grafico del dashboard|Disegno di Leaderboard|
|---|---|---|---|
|Dimensioni visibile|200x200px|923x150px|n/d|
|Dimensioni effettive|320x320px|n/d|659x371px|
|L'area di sicurezza delle dimensioni|n/d|n/d|618x348px|
|Dimensione con stato non attivo|n/d|n/d|548x309px|
|Dimensione con lo stato attivo|n/d|n/d|618x348px|

Per altre informazioni sull'utilizzo di Game Center, vedere di Apple [Guida alla programmazione di Game Center](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html).

<a name="Working-with-Images" />

## <a name="working-with-images"></a>Working with Images (Uso delle immagini)

Poiché un subset di iOS 9, le stesse tecniche utilizzate per includere e visualizzare le immagini in un'app xamarin. IOS, tvOS 9 funzionano anche per un'app xamarin. tvos. Vedere la [visualizzazione di un'immagine](~/ios/app-fundamentals/images-icons/displaying-an-image.md) per altre informazioni.

<a name="Setting-Xamarin.tvOS-Project-Images" />

## <a name="setting-xamarintvos-project-images"></a>Impostazione di progetto xamarin. tvos immagini

Come indicato in precedenza, tutte le app tvOS richiedono una [immagine di avvio veloce](#Launch-Image), e [icona dell'App](#App-Icons). Questa sezione illustra la selezione di immagini di avvio e l'icona dell'App per il progetto di app xamarin. tvos dopo che sono stati impostati in un catalogo di Asset.

Seguire questa procedura:

1. Nel **Esplora soluzioni**, fare doppio clic il `Info.plist` per aprirlo e modificarlo: 

    [![](icons-images-images/info01.png "Il file Info. plist")](icons-images-images/info01.png#lightbox)
2. Nel **Editor Info. plist**, selezionare il catalogo di asset (configurato in precedenza nel [impostando le icone dell'App](#Setting-the-App-Icons) sezione) per il **icone dell'App**: 

    [![](icons-images-images/info02.png "The Info.Plist Editor")](icons-images-images/info02.png#lightbox)
3. Successivamente, selezionare il catalogo di asset (configurato in precedenza nel [l'impostazione dell'immagine di avvio veloce](#Setting-the-Launch-Image) sezione) per il **immagini di avvio**.
4. Salvare le modifiche.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato tutti i tipi di immagine e le dimensioni usate in un'app xamarin. tvos. In primo luogo, contemplati immagini di avvio, immagini a più livelli, le icone dell'App, immagini scaffale superiore e le immagini di Game Center. Quindi trattato funzionante con immagini nell'app xamarin. tvos.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
