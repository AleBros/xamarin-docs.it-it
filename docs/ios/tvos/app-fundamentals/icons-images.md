---
title: Utilizzo di immagini e icone
description: Questo articolo descrive la progettazione e l'utilizzo di icone e immagini all'interno di un'app Xamarin.tvOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 3e3d1663e07b16721d1aa7253e7d0150a609718e
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="working-with-icons-and-images"></a>Utilizzo di immagini e icone

_Questo articolo descrive la progettazione e l'utilizzo di icone e immagini all'interno di un'app Xamarin.tvOS._

Creazione di straordinaria icone e immagini sono una parte essenziale di sviluppo di un'esperienza coinvolgente per le app di Apple TV. Questa guida illustra i passaggi necessari per creare e includere le risorse grafiche necessarie per le app Xamarin.tvOS:

- [Immagine di avvio](#Launch-Image) -viene visualizzata un'immagine di avvio quando l'app prima di tutto è stato avviato e viene sostituito dalla schermata iniziale dell'app, una volta completata l'avvio.
- [Le immagini sovrapposte](#Layered-Images) - specifiche per Apple TV, nuovo lavoro immagini a più livelli di Apple con l'effetto di parallasse per creare un effetto 3D per gli elementi selezionati. Esistono diversi modi per [creare immagini a più livelli](#Creating-Layered-Images).
- [Icona app](#App-Icons) -le icone sono necessarie per la schermata non solo di Apple TV Home, ma per l'archivio di App. Essi deve essere fornite come un'immagine a più livelli.
- [Primi scaffale immagine](#Top-Shelf-Image) -se l'app viene posizionato sulla riga superiore della schermata, sarà necessario un'immagine di scaffale superiore per evidenziare le funzionalità dell'app. Facoltativamente, è possibile fornire [il contenuto dinamico scaffale superiore](#Dynamic-Top-Shelf-Content) per evidenziare il contenuto dell'app.
- [Gioco Center immagini](#Game-Center-Images) -se l'applicazione è un gioco e utilizza Game Center, numerose immagini aggiuntive saranno necessari.
- [Impostazione Xamarin.tvOS progetto immagini](#Setting-Xamarin.tvOS-Project-Images) -descrive i passaggi necessari per impostare l'immagine di avvio e l'icona dell'App per l'app Xamarin.tvOS.

> [!IMPORTANT]
> **Nota:** sono tutte le immagini in Apple TV con la risoluzione 1 x (`@1x`) e deve _solo_ utilizzare le immagini di queste dimensioni. Inclusi più grande, grafica con risoluzione superiore non solo è opportuno scaricare e utilizzare più memoria e archiviazione, ma è necessario essere ridimensionate in modo dinamico in fase di esecuzione e influirà negativamente sulle prestazioni di disegno.

<a name="Launch-Image" />

## <a name="launch-image"></a>Immagine di avvio

L'immagine di avvio è la prima operazione che viene visualizzata quando l'app Xamarin.tvOS inizialmente viene avviato in Apple TV, e di conseguenza, tutte le applicazioni tvOS devono fornire un'immagine di avvio. 

L'immagine di avvio viene visualizzato rapidamente e fornisce l'impressione che l'app è rapido e tempestivo. Apple TV sostituirà l'immagine di avvio con la prima schermata dell'app a breve si dopo.

Le immagini di avvio non sono un'opportunità per gli annunci o espressione artistico, esistono solo per dare l'impressione che l'app consente di avviare rapidamente ed è pronto a utilizzare.

|Dimensioni dell'immagine di avvio|Note|
|---|---|
|1920x1080px|Solo i file con estensione PNG a più livelli non|

Apple rende i suggerimenti seguenti per la progettazione di immagine di avvio dell'app:

- **Quasi identico alla prima schermata** -Your schermata di avvio deve essere più vicino prima schermata dell'app come possibili. Tra cui grafici diversi o elemento può comportare un indesiderate "flash" quando viene visualizzata la prima schermata.
- **Evitare di utilizzare testo** -immagini di avvio sono statiche e di conseguenza, non sarà localizzate prima di essere visualizzato.
- **Avvio downplay** -gli utenti perché Apple TV passano frequentemente l'App, non deve attirare l'attenzione del processo di avvio dell'app.
- **Nessun annunci o Branding** -l'immagine di avvio non deve essere utilizzato come una schermata di informazioni o includere qualsiasi personalizzazione, a meno che non è parte statica del prima schermata dell'app. Non è assolutamente consentiti annunci.

<a name="Setting-the-Launch-Image" />

### <a name="setting-the-launch-image"></a>L'impostazione dell'immagine di avvio

Per impostare l'immagine di avvio per il progetto tvOS, eseguire le operazioni seguenti:

1. Nel **Esplora**, fare doppio clic su `Assets.xcassets` per aprirlo e modificarlo: 

    [![](icons-images-images/asset01.png "Il file Assets.xcassets")](icons-images-images/asset01.png#lightbox)
2. Nel **Asset Editor**, fare clic su di `LaunchImages` asset: 

    [![](icons-images-images/asset02.png "L'asset LaunchImages")](icons-images-images/asset02.png#lightbox)
3. Fare clic su di **1 x Apple TV** voce e selezionare l'immagine di avvio o eventualmente trascinare una nuova immagine dal file system: 

    [![](icons-images-images/asset03.png "Selezionare un'immagine di avvio")](icons-images-images/asset03.png#lightbox)
4. Salvare le modifiche.

<a name="Layered-Images" />

## <a name="layered-images"></a>Immagini a più livelli

Nuovo per Apple TV, immagini a più livelli di lavoro con l'effetto di parallasse per produrre un effetto 3D che consente di impedire agli utenti in poltrona mentalmente connesso al contenuto sullo schermo tramite la chat.

Immagini a più livelli contengono da due (2) a cinque (5) separare i livelli che vengono combinati per formare un'immagine completa. Fatta eccezione per il livello di sfondo, ogni livello utilizza il relativo ordine Z con trasparenza per creare un effetto ottico di profondità. Quando l'utente interagisce con un'immagine a più livelli, livelli superiori ordinati Z di ridimensionamento e sovrapposta per creare questo effetto.

[![](icons-images-images/layered01.png "Diagramma ordinati Z immagini a più livelli")](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> **Nota:** immagini su più livelli sono necessari per le icone dell'app e sono facoltative per altri [elementi con stato attivabile](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) (ad esempio, l'immagine di scaffale superiore). Tuttavia, Apple suggerisce l'utilizzo di immagini a più livelli per le immagini che è possono ottenere lo stato attivo nell'app.




Apple rende i suggerimenti seguenti per la progettazione delle immagini a più livelli:

- **Rendere il livello di sfondo opaco** -il livello di sfondo (livello 1) **deve** opaco o si otterrà un errore quando si tenta di utilizzare l'immagine a più livelli su Apple TV. Tutti gli altri livelli possono contenere più livelli di trasparenza per migliorare l'effetto 3D.
- **Isolare in primo piano, intermedio e gli elementi di Background** -elementi principali (ad esempio, giochi caratteri) in primo piano, usare il centro per gli elementi secondari o ombreggiature. Infine, includere uno sfondo neutro per fornire una fase per i livelli superiori.
- **Mantenere il testo in primo piano** -a meno che non si desidera il testo venga nascosto da livelli più elevati, in genere deve essere nel livello superiore.
- **Utilizzare sovrapposizione semplice** -l'effetto di parallasse è stato progettato per essere impercettibile pertanto mantenere i livelli a un minimo per impedire effetti jarring, realistici.
- **Includono un'area di sicurezza** -poiché livelli superiori possono essere ritagliati durante un effetto di parallasse, è necessario compilare un bordo dell'area di sicurezza in ogni livello. Se viene visualizzato il contenuto troppo vicini al bordo di livelli, è possibile diventare ritagliato. Livelli superiori si verificheranno più e proporzioni rispetto ai livelli inferiori. Vedere il [i livelli di ridimensionamento dell'immagine](#Sizing-Image-Layers) sezione riportata di seguito.
- **Visualizzare in anteprima spesso** -immagini a più livelli deve essere visualizzato in anteprima spesso per garantire che gli effetti 3D desiderati e che nessuno dei contenuti di singoli livelli viene viene ritagliata. È necessario visualizzare l'anteprima delle immagini a più livelli sull'hardware effettivo di Apple TV, per assicurarsi che il rendering come previsto.

Quando possibile, utilizzare sempre incorporati `UIKit` controlli per visualizzare le immagini a più livelli, come otterranno automaticamente l'effetto di parallasse quando diventano lo stato attivo.

<a name="Sizing-Image-Layers" />

### <a name="sizing-image-layers"></a>Livelli di ridimensionamento dell'immagine

È importante ricordare di includere un _l'area di sicurezza_ bordo in ogni livello che costituiranno l'immagine a più livelli. Poiché i singoli livelli possono essere ridimensionati e ritagliati durante l'effetto di parallasse, il contenuto dei livelli può ritagliato se risulta troppo vicino al bordo del livello:

[![](icons-images-images/layered02.png "bordo 35 pixel")](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images" />

### <a name="creating-layered-images"></a>Creare livelli di immagini

tvOS funziona con immagini a più livelli nei formati seguenti:

- **File AUTOMOBILE** -si tratta di un formato proprietario di catalogo di Asset creato da Apple. Non si crea il file AUTOMOBILE direttamente, vengono create in fase di compilazione da qualsiasi file LSR e incluso nel bundle dell'app.
- **Le immagini LSR** -si tratta di un formato di immagine proprietarie creato da Apple. Utilizzare il [plug-in parallasse esportazione Adobe Photoshop](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip) o [anteprima parallasse](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) per creare e a più livelli di immagini di anteprima nel formato LSR.
- **Assets.xcassets** - da due (2) a cinque (5) standard `.png` formattato le immagini incluse in un catalogo di Asset che verranno compilati in un'AUTOMOBILE o LSR formattato su più livelli di immagine in fase di compilazione.
- **File di replica continua locale** -si tratta di un formato di file proprietario di Apple. File LCR servono per essere utilizzato come contenuto aggiuntivo scaricato da uno dei server di contenuto. File di replica continua locale non deve mai essere inclusi nel bundle dell'app. File di replica continua locale vengono generati dai file LSR o Photoshop usando il `layerutil` strumento della riga di comando incluso in Xcode.

<a name="The-Parallax-Previewer" />

### <a name="the-parallax-previewer"></a>Il Visualizzatore di parallasse

Apple creato il [anteprima parallasse](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) anteprima e immagini create a più livelli necessaria per le icone di App e gli elementi con stato attivabile facoltativi. L'anteprima Mostra ogni livello che costituisce l'immagine a più livelli completata:

[![](icons-images-images/layered03.png "Il Visualizzatore di parallasse")](icons-images-images/layered03.png#lightbox)

Durante l'anteprima di un'immagine a più livelli, è possibile utilizzare il mouse per ruotare l'immagine e visualizzare in anteprima l'effetto di parallasse. Utilizzare il  **+**  (più) e  **-**  (pulsanti per aggiungere e rimuovere i livelli meno).

Quando si crea una nuova immagine a più livelli, possono essere esportato nel formato LSR e inclusi nel bundle dell'app.

Per ulteriori informazioni sulla creazione e visualizzazione in anteprima le immagini a più livelli, vedere Apple [creazione grafica parallasse](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1) sezione la [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/).

<a name="App-Icons" />

## <a name="app-icons"></a>Icone di App

L'app Xamarin.tvOS richiederà non solo un'icona di App per la schermata Home Apple TV, ma anche un'icona per l'archivio di App. L'icona dell'App è il la prima modifica per rendere una grande impressione potenziali utenti e deve comunicare scopo dell'applicazione a colpo d'occhio.

[![](icons-images-images/icon01.png "L'icona dell'App")](icons-images-images/icon01.png#lightbox)

Ogni app è necessario specificare sia una piccola e una versione ingrandita della relativa icona di App. Nella schermata Home Apple TV verrà utilizzato l'icona di piccole dimensioni quando l'app viene installata. Dall'App Store viene utilizzata la versione di grandi dimensioni. Icona di grandi dimensioni App deve simulare l'aspetto della versione dell'icona piccola.

|Icona di piccole dimensioni||Icone grandi||
|---|---|---|---|
|Dimensioni effettive|400x240px|Dimensione|1280x768px|
|L'area di sicurezza dimensioni|370x222px|||
|Dimensioni con stato non attivo|300x180px|||
|Dimensione con lo stato attivo|370x222px|||

> [!IMPORTANT]
> Le icone di App deve essere fornite come **immagini a più livelli**. Vedere il [immagine a più livelli](#Layered-Images) precedente sezione per ulteriori dettagli.




Apple fornisce i seguenti suggerimenti per la creazione di icone App:

- **Fornire un singolo punto di stato attivo** : progettazione l'icona con un punto singolo posizionata direttamente al centro dell'icona.
- **Utilizzare uno sfondo semplice** : mantenere semplice lo sfondo dell'icona in modo da evidenziare i livelli superiori. È consigliabile utilizzare un colore semplice o una sfumatura sottile.
- **Limitare la quantità di testo** : poiché il nome dell'applicazione verrà visualizzata sotto l'icona quando viene selezionato dall'utente, è necessario includere il testo solo quando è essenziale per la progettazione dell'icona.
- **Non usare schermate** – schermate sono troppo complesse per un'icona e non consente all'utente visualizzare lo scopo dell'app a colpo d'occhio.
- **Mantieni icone quadrato** – tvOS applica automaticamente la maschera che leggermente consente di arrotondare gli angoli delle icone. Non includere questo arrotondamento manualmente.
- **Separare attentamente i livelli di** : il testo deve essere nell'angolo in alto la maggior parte dei livelli, gli elementi secondari nella parte centrale e uno sfondo neutro che consente i livelli superiori da utilizzare.
- **Utilizzare sfumature e ombreggiature attentamente** – sfumature e ombreggiature possono essere in conflitto con l'effetto di parallasse pertanto deve essere utilizzate con attenzione. Semplice alto verso il basso, gli stili di sfumati per chiaro-scuro funzionano meglio. Shadows in genere la scelta ottimale come tinte acute, netti.
- **Variare la trasparenza di livello** : uso di diversi livelli di trasparenza sui livelli superiori dell'icona dell'App per aumentare l'effetto 3D. Il livello di sfondo deve essere opaco o si verificherà un errore.

<a name="Setting-the-App-Icons" />

### <a name="setting-the-app-icons"></a>Impostando le icone dell'App

Per impostare le icone dell'App necessarie per il progetto tvOS, eseguire le operazioni seguenti:

1. Nel **Esplora**, fare doppio clic su `Assets.xcassets` per aprirlo e modificarlo: 

    [![](icons-images-images/asset01.png "Il fileg Assets.xcassets")](icons-images-images/asset01.png#lightbox)
2. Nel **Asset Editor**, espandere il `App Icon & Top Shelf Image` asset: 

    [![](icons-images-images/asset04.png "Espandere l'asset immagine scaffale superiore")](icons-images-images/asset04.png#lightbox)
3. Espandere quindi la `App Icon - Small` asset: 

    [![](icons-images-images/asset05.png "Espandere l'icona di App - asset di piccole dimensioni")](icons-images-images/asset05.png#lightbox)
4. Espandere quindi la `Back` asset e fare clic su di `Contents` voce: 

    [![](icons-images-images/asset06.png "Espandere quindi la risorsa Back")](icons-images-images/asset06.png#lightbox)
5. Fare clic su di **1 x voce Apple TV** e selezionare un file di immagine.
6. Ripetere i passaggi precedenti per il `Front` e `Middle` asset.
7. Ripetere gli stessi passaggi per definire il `App Icon - Large` asset.
4. Salvare le modifiche.

<a name="Top-Shelf-Image" />

## <a name="top-shelf-image"></a>Immagine di scaffale superiore

Se l'utente ha effettuato l'app Xamarin.tvOS sulla riga superiore nella schermata Home Apple TV, un'immagine di scaffale superiore grande verrà visualizzata quando l'app viene selezionato dall'utente. Questa immagine deve evidenziare le funzionalità dell'app o forniscono collegamenti diretti al relativo contenuto.

[![](icons-images-images/topshelf01.png "Esempio di immagine scaffale superiore")](icons-images-images/topshelf01.png#lightbox)

È possibile specificare l'immagine di scaffale superiore come singolo statico `.png` o `.lsr` file (vedere [creazione di immagini a più livelli](#Creating-Layered-Images)) o può essere creato dinamicamente in fase di esecuzione come una singola riga di elementi con stato attivabile (vedere [ Contenuto dinamico scaffale superiore](#Dynamic-Top-Shelf-Content) sotto).

|Dimensioni dell'immagine scaffale superiore|Note|
|---|---|
|1920x720px|File PNG statico o file .lsr su più livelli|

Apple fornisce i seguenti suggerimenti per la creazione di immagini scaffale superiore:

- **Utilizzare immagini statiche Rich** : se l'app non fornisce un contenuto dinamico, la propria immagine scaffale superiore sarà non attivabili. Utilizzare questa immagine per evidenziare le funzionalità di app o il marchio.
- **Collegamento al contenuto App** – layout dinamico di scaffale superiore forniscono un collegamento rapido per il contenuto che l'utente consente di trovare più importante nell'app. Utilizzare questa area per fornire un collegamento rapido per avviare l'app e passare immediatamente il contenuto specificato.
- **Presentare il contenuto più recente** : disegnare un utente nell'app e renderli è più contenuto scaffale superiore RTF. Utilizzare come un'area per presentare il contenuto più recente o con classificazione più alta.
- **Il contenuto personalizzato** – App preferite nella riga superiore della schermata o sul posto di utenti i più utilizzati. Utilizzare il cassetto superiore per visualizzare il contenuto che saranno interessati.
- **Gli annunci non è consentito** : gli annunci non è assolutamente consentiti dalla visualizzazione in scaffale superiore. È possibile mostrare il contenuto più recente acquistabili, ma non le informazioni sui prezzi devono essere visualizzate.

### <a name="setting-the-top-shelf-image"></a>L'impostazione dell'immagine scaffale superiore

Per impostare l'immagine di scaffale superiore necessari per il progetto tvOS, eseguire le operazioni seguenti:

1. Nel **Esplora**, fare doppio clic su `Assets.xcassets` per aprirlo e modificarlo: 

    [![](icons-images-images/asset01.png "Il file Assets.xcassets")](icons-images-images/asset01.png#lightbox)
2. Nel **Asset Editor**, espandere il `App Icon & Top Shelf Image` asset: 

    [![](icons-images-images/asset04.png "Espandere l'asset immagine scaffale superiore")](icons-images-images/asset04.png#lightbox)
3. Fare clic su di `Top Shelf Image` asset: 

    [![](icons-images-images/asset07.png "L'asset immagine scaffale superiore")](icons-images-images/asset07.png#lightbox)
5. Fare clic su di **1 x voce Apple TV** e selezionare un file di immagine.
6. Salvare le modifiche.

<a name="Dynamic-Top-Shelf-Content" />

### <a name="dynamic-top-shelf-content"></a>Contenuto dinamico scaffale superiore

Anziché visualizzare un'immagine statica di scaffale superiore, scaffale superiore può contenere una riga dinamica di [elementi con stato attivabile](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) o un set dinamico di intestazioni di scorrimento. Entrambi questi stile dinamica consente di evidenziare il contenuto fornito dall'applicazione o salto le funzionalità più utilizzate.

<a name="Sectioned-Content-Row" />

#### <a name="sectioned-content-row"></a>Riga di contenuto sezionate

Questo tipo di contenuto dinamico scaffale superiore presenta una sola riga di scorrimento, gli elementi con stato attivabile facoltativamente suddivisa in sezioni. È in genere usato per evidenziare nuovi preferiti o visualizzati di recente contenuti dell'app.

Il contenuto viene visualizzato un elenco a scorrimento singolo e orizzontale del contenuto con un'etichetta che vengono visualizzati sotto la parte di contenuto selezionato corrente (che ha attualmente lo stato attivo). Se l'utente seleziona una determinata parte del contenuto, verrà avviata l'app e deve essere eseguiti direttamente in tale contenuto.

Sono necessarie le seguenti dimensioni del contenuto:

||Poster (2:3)|Square (1:1)|HDTV (16:9)|
|---|---|---|---|
|Dimensioni effettive|404x608px|608x608px|908x512px|
|L'area di sicurezza dimensioni|380x570px|570x570px|852x479px|
|Dimensioni con stato non attivo|333x500px|500x500px|782x440px|
|Dimensione con lo stato attivo|380x570px|570x570px|852x479px|

Apple fornisce i seguenti suggerimenti per la riga sezionato contenuto:

- **Completare la riga** : È necessario fornire contenuto sufficiente da occupare l'intera larghezza dello schermo.
- **Ridimensionamento di immagini misto** – contenuto sezionato la riga è stata progettata per contenere una combinazione di dimensioni delle immagini (dall'elenco fornito in precedenza). Se si combinano le dimensioni delle immagini, tuttavia, tenere presente che verrà applicata per normalizzare la visualizzazione contenuta scalabilità aggiuntive.

<a name="Scrolling-Inset-Banners" />

#### <a name="scrolling-inset-banners"></a>Banner di margine di scorrimento

Facoltativamente, l'app Xamarin.tvOS può presentare il contenuto in scaffale superiore come automaticamente lo scorrimento e ciclo di raccolta di intestazioni che quasi tutto schermo. Questo stile viene in genere utilizzato per presentare contenuto RTF, nuovo, ad esempio nuovi programmi TV.

Oltre a scorrimento automatico, l'utente può assumere il controllo di testo e scorrere in entrambe le direzioni utilizzando remoto Siri. Apportare una piccola, movimento circolare in remoto Siri quando lo stato attivo un banner verrà attivato l'effetto di parallasse per tale intestazione.

**Immagine del banner (grandi dimensioni)**

|   |   |
|---|---|
|Dimensioni effettive|1940x624px|
|L'area di sicurezza dimensioni|1740x620px|
|Dimensioni con stato non attivo|1740x560px|
|Dimensione con lo stato attivo|1740x620px|

Banner di margine di scorrimento possono essere fornite come statico `.png` o a più livelli `.lsr` file.

Apple fornisce i seguenti suggerimenti per lo scorrimento Inset intestazioni:

- **Quantità di contenuto** -è necessario fornire un minimo di tre (3) le intestazioni per le barre di scorrimento per ritiene naturale. È necessario includere le intestazioni non più di otto (8) o rende navigazione disco rigido per l'utente finale.
- **Contenuto di testo** : se l'intestazione richiede testo deve essere incluso nell'immagine del banner. Se si usano immagini a più livelli, il testo deve essere nel livello superiore.

Vedere Apple [TVServices Framework riferimento](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) per ulteriori informazioni sull'aggiunta di un'estensione scaffale superiore all'App per fornire contenuto dinamico di scaffale superiore.

<a name="Game-Center-Images" />

## <a name="game-center-images"></a>Immagini Game Center

Se l'app Xamarin.tvOS è un gioco e di aver inserito il supporto Game Center, sono necessarie ulteriori asset immagini contenute:

- **Le icone di realizzazione** -un'immagine opaca è necessaria per ogni risultato che verrà ritagliata automaticamente in un cerchio. Obiettivi sono elementi non attivabili.
- **Disegno dashboard** -può essere un'immagine facoltativa, a condizione che verrà visualizzato nella parte superiore del dashboard dell'app Game Center. Queste immagini sono non attivabili.
- **Classifica grafica** -è necessario fornire tra uno (1) a tre (3) le immagini proporzioni 16:9 per ogni classifica che supporta l'applicazione. Queste possono essere statici `.png` o a più livelli `.lsr` file. Il disegno classifica è con stato attivabile.

||Icone di successo|Disegno di dashboard|Classifica grafica|
|---|---|---|---|
|Dimensione visibile|200x200px|923x150px|N/D|
|Dimensioni effettive|320x320px|N/D|659x371px|
|L'area di sicurezza dimensioni|N/D|N/D|618x348px|
|Dimensioni con stato non attivo|N/D|N/D|548x309px|
|Dimensione con lo stato attivo|N/D|N/D|618x348px|

Per ulteriori informazioni sull'utilizzo di Game Center, vedere Apple [Guida per programmatori Game Center](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html).

<a name="Working-with-Images" />

## <a name="working-with-images"></a>Working with Images (Uso delle immagini)

Poiché tvOS 9 è un subset di iOS 9, le stesse tecniche utilizzate per includere e visualizzare le immagini in un'app xamarin. IOS, funzionano anche per un'app Xamarin.tvOS. Consultare il nostro [visualizzazione di un'immagine](~/ios/app-fundamentals/images-icons/displaying-an-image.md) documentazione per ulteriori informazioni.

<a name="Setting-Xamarin.tvOS-Project-Images" />

## <a name="setting-xamarintvos-project-images"></a>Impostazione di progetto Xamarin.tvOS immagini

Come descritto in precedenza, tutte le app tvOS richiedono un [immagine di avvio](#Launch-Image), e [icona App](#App-Icons). Questa sezione descrive selezionando l'immagine di avvio e l'icona dell'App per il progetto di app Xamarin.tvOS dopo che sono state impostate in un catalogo di Asset.

Seguire questa procedura:

1. Nel **Esplora**, fare doppio clic su di `Info.plist` per aprirlo e modificarlo: 

    [![](icons-images-images/info01.png "Il file Info. plist")](icons-images-images/info01.png#lightbox)
2. Nel **Editor Info. plist**, selezionare il catalogo di asset (configurato in precedenza nel [impostando le icone dell'App](#Setting-the-App-Icons) sezione) per il **icone App**: 

    [![](icons-images-images/info02.png "L'Editor di Info. plist")](icons-images-images/info02.png#lightbox)
3. Successivamente, selezionare il catalogo di asset (configurato in precedenza nel [l'impostazione dell'immagine di avvio](#Setting-the-Launch-Image) sezione) per il **immagini di avvio**.
4. Salvare le modifiche.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati tutti i tipi di immagine e usate in un'app Xamarin.tvOS dimensioni. Innanzitutto analizzate le immagini di avvio, immagini a più livelli, le icone di App, immagini scaffale superiore e Game Center immagini. Quindi che trattato operazioni con le immagini dell'App Xamarin.tvOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
