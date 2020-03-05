---
title: Modifiche aggiuntive al framework macOS Sierra
description: Questo documento descrive le modifiche e i miglioramenti secondari ai Framework esistenti introdotti in macOS Sierra. Esamina le modifiche apportate a accelerate Framework, AppKit, AVFoundation, core data, Core Image, Foundation e altro ancora.
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 44468d3f2d323065161c290f2df8e6f0e89d3def
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292667"
---
# <a name="additional-macos-sierra-framework-changes"></a>Modifiche aggiuntive al framework macOS Sierra

<a name="Accelerate-Framework-Enhancements" />

## <a name="accelerate-framework-enhancements"></a>Miglioramenti a Framework di accelerazione

I miglioramenti seguenti sono stati apportati al Framework di accelerazione per macOS Sierra:

- Aggiunta di quadratura (calcolo integrale).
- Aggiunta di funzioni di base per la creazione di reti neurali.
- Sono state aggiunte funzioni di predicato geometrico per testare elementi come l'intersezione di due oggetti geometrici.

<a name="AppKit-Framework-Enhancements" />

## <a name="appkit-framework-enhancements"></a>Miglioramenti di AppKit Framework

Sono state apportate le seguenti funzionalità avanzate al Framework di AppKit per macOS Sierra:

- Sono stati apportati diversi miglioramenti a `NSCollectionView`, ad esempio:
  - **Sezioni comprimibili** : consente all'utente di comprimere una sezione di visualizzazione raccolta in una singola riga orizzontale.
  - **Intestazioni mobili** : le intestazioni e i piè di pagina possono essere ora sospesi (in un layout di flusso) usando la stessa API di [UICollectionView](https://developer.apple.com/reference/uikit/uicollectionview) in iOS.
  - **Visualizzazioni in background scorrevoli** : è ora possibile impostare uno sfondo per le visualizzazioni di raccolta per scorrere insieme al contenuto.
- Il passaggio di layout della visualizzazione posticipato è stato ottimizzato ed esteso.
- L'API di trascinamento della selezione include ora le nuove classi `NSFilePromiseProvider` e `NSFilePromiseReceiver` per supportare l'affollamento del trascinamento.
- Ai controlli esistenti sono stati aggiunti diversi costruttori pratici:
  - `NSButton` include nuovi costruttori per la creazione di pulsanti di comando, caselle di controllo e pulsanti di opzione.
  - `NSTextField` include nuovi costruttori per la creazione di etichette di wrapping e non di wrapping, etichette con attributi e campi di testo modificabili.
  - `NSSegmentedControl` include nuovi costruttori per la creazione di controlli segmentati da un gruppo di etichette o immagini.
  - `NSSlider` include nuovi costruttori per la creazione di dispositivi di scorrimento lineari orizzontali.
  - `NSImageView` include nuovi costruttori per la creazione di visualizzazioni di immagine non modificabili da un determinato `NSImage`.
- Il nuovo `NSGridView` è stato aggiunto al layout automatico di una raccolta di visualizzazioni secondarie in una griglia con righe e colonne di dimensioni variabili che possono essere nascoste o visualizzate dinamicamente.

<a name="AVFoundation-Framework-Enhancements" />

## <a name="avfoundation-framework-enhancements"></a>Miglioramenti di AVFoundation Framework

Sono state apportate le seguenti funzionalità avanzate al Framework di AVFoundation per macOS Sierra:

- In macOS, l'app non deve più implementare comportamenti [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) diversi in base al tipo di contenuto. È sufficiente impostare la proprietà `Rate` e AVFoundation determinerà quando sarà disponibile contenuto sufficiente per la riproduzione senza bloccarsi.
- La nuova classe `AVPlayerLooper` rende più semplice il ciclo di un dato supporto durante la riproduzione.
- La classe `AVAssetDownloadURLSession` consente il download e la riproduzione successiva dei flussi HLS crittografati FairPlay.

<a name="Core-Data-Framework-Enhancements" />

## <a name="core-data-framework-enhancements"></a>Miglioramenti di Core Data Framework

Sono stati apportati i miglioramenti seguenti al Framework di dati principale per macOS Sierra:

- Gli oggetti [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) radice supportano gli errori e il recupero simultanei senza serializzazione.
- La classe [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) gestisce un pool di archivi dati SQLite.
- Gli oggetti [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) con gli archivi dati SQLite nella modalità Journal Wal supportano la nuova funzionalità di generazione delle query in cui i contesti degli oggetti gestiti (MOC) possono essere aggiunti a versioni specifiche del database per le transazioni di recupero e di errore future.
- Uso del `NSPersistenceContainer` generale per fare riferimento al `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e ad altre risorse di configurazione dei dati principali.
- Sono stati aggiunti diversi nuovi metodi pratici per `NSManagedObject` semplificare l'esecuzione di operazioni di recupero e creazione di sottoclassi.

Per altre informazioni, vedere la Guida di [riferimento a Core Data Framework](https://developer.apple.com/reference/coredata)di Apple.

<a name="Core-Image-Framework-Enhancements" />

## <a name="core-image-framework-enhancements"></a>Miglioramenti di Core Image Framework

Per macOS Sierra sono stati apportati i miglioramenti seguenti al framework principale dell'immagine:

- Il metodo `ImageWithExtent` della classe [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) può essere usato per inserire l'elaborazione personalizzata nell'operazione di filtro. L'immagine principale richiama il callback specificato tra i filtri quando si elabora un'immagine per l'output o la visualizzazione.
- L'app ora può elaborare le immagini in uno spazio di colore al di fuori dello spazio dei colori di lavoro del contesto immagine principale convertendo in e fuori dallo spazio di colore prima e dopo l'elaborazione.
- Il kernel principale dell'immagine può ora richiedere un formato di output di pixel specifico.
- Sono stati aggiunti i nuovi filtri immagine seguenti: `CINinePartTitled`, `CINinePartStretched`, `CIHueSaturationValueGradient`, `CIEdgePreserveUpsampleFilter` e `CIClamp`.

<a name="Foundation-Framework-Enhancements" />

## <a name="foundation-framework-enhancements"></a>Miglioramenti di Foundation Framework

Di seguito sono riportati i miglioramenti apportati al Framework di base per macOS Sierra:

- Usare l'API [NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) per la rappresentazione, la conversione e la visualizzazione di molte delle unità fisiche più comuni, ad esempio la massa, la lunghezza, la velocità, la durata e la temperatura.
- Usare la classe [NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter) per l'analisi e la generazione di date formattate ISO 8601.
- Usare la nuova classe [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) per creare calcoli di intervallo di data e ora, ad esempio le durate, per confrontare gli intervalli e i test per le intersezioni degli intervalli.
- Usare la classe [NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter) per analizzare gli elementi del nome di una persona da una stringa.
- Usare la nuova classe [NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics) per ottenere le metriche per una sessione di rete URL.

Per altre informazioni, vedere le note sulla [versione di Apple Foundation per OS X v 10.12 e iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html).

<a name="GameKit-Framework-Enhancements" />

## <a name="gamekit-framework-enhancements"></a>Miglioramenti di GameKit Framework

Sono state apportate le seguenti funzionalità avanzate al Framework di GameKit per macOS Sierra:

- L' **App Game Center** è stata deprecata e rimossa da MacOS. Se l'app usa GameKit, _deve_ presentare una propria interfaccia per visualizzare le funzionalità di gamekit, ad esempio le classifiche e così via.
- Un nuovo tipo di account solo iCloud è stato implementato dalla classe [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) .
- La nuova classe [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) fornisce una soluzione generalizzata per la gestione dell'archiviazione dei dati persistente nei Game Center. `GKGameSession` gestisce un elenco di giocatori e l'app è responsabile del modulo che implementa come e quando la data del partecipante viene archiviata, recuperata o scambiata tra i giocatori. In molti casi le sessioni di gioco possono sostituire le corrispondenze basate su turni esistenti, le corrispondenze in tempo reale o i metodi di salvataggio del gioco permanenti

<a name="GamePlayKit-Framework-Enhancements" />

## <a name="gameplaykit-framework-enhancements"></a>Miglioramenti di GamePlayKit Framework

Sono state apportate le seguenti funzionalità avanzate al Framework di GamePlayKit per macOS Sierra:

- È stata aggiunta la generazione del rumore procedurale che può essere usata per migliorare il realismo nelle trame all'aspetto naturale, aggiungere il realismo ai movimenti della fotocamera e contribuire a generare mondi di gioco avanzati.
- Usare il partizionamento spaziale per partizionare i dati del mondo del gioco per una ricerca efficiente.
- Un nuovo Strategy Monte Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) è stato aggiunto per un calcolo completo possibile dello spostamento.
- È stata aggiunta una nuova API dell'albero delle decisioni ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) per migliorare l'intelligenza artificiale per la creazione di giochi.
- il supporto 3D è stato aggiunto ai comportamenti esistenti di individuazione degli agenti e dei percorsi usando le nuove classi [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) .
- Usare la nuova classe [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) per fornire percorsi a prestazioni elevate e naturali.
- Le nuove classi [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) rendono la combinazione di GameplayKit e SpriteKit più semplice che mai.

<a name="Metal-Framework-Enhancements" />

## <a name="metal-framework-enhancements"></a>Miglioramenti di Metal Framework

Sono stati apportati i miglioramenti seguenti al Framework di metal per macOS Sierra:

- le app e i giochi 3D possono ora usare il _mosaico_ per eseguire in modo efficiente il rendering di scene e geometria complesse tramite la GPU.
- Usare la specializzazione della funzione per creare una raccolta altamente ottimizzata di funzioni di combinazione materiale e luce per una scena.
- Fornire un controllo con granularità fine dell'allocazione delle risorse per ottimizzare le prestazioni delle app basate su Metal usando gli heap delle risorse e le destinazioni di rendering prive di memoria.

Per altre informazioni, vedere la guida alla [programmazione](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)per i metal di Apple.

<a name="ModelIO-Framework-Enhancements" />

## <a name="model-io-framework-enhancements"></a>Miglioramenti del Framework di I/O del modello

Per macOS Sierra sono stati apportati i miglioramenti seguenti al Framework di I/O del modello:

- Il formato di file USD è ora supportato.
- Utilizzare la nuova classe `MDLMaterialPropertyGraph` per supportare facilmente le modifiche di runtime ai modelli.
- Il supporto per il campo a distanza firmato è stato aggiunto alla classe [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) .
- Usare la nuova classe `MDLLightProbeIrradianceDataSource` per facilitare la selezione del posizionamento Probe leggero.

<a name="Photos-Framework-Enhancements" />

## <a name="photos-framework-enhancements"></a>Miglioramenti di Photo Framework

Per macOS Sierra sono stati apportati i miglioramenti seguenti al Framework Photos:

- La funzionalità di modifica della foto in tempo reale è ora disponibile per le app che supportano il Framework foto e per le estensioni per la modifica di foto (per l'uso all'interno delle app Foto e fotocamera).
- Usare la nuova classe [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) per applicare le modifiche sia al video che ancora al contenuto delle foto in tempo reale.
- Usare le classi [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) e [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) per sfruttare i vantaggi della nuova funzionalità di processore di immagini principali per eseguire le modifiche.
- Per supportare le foto Live, le classi [PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto) e [PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview) sono state trasferite da iOS a MacOS.

<a name="SceneKit-Framework-Enhancements" />

## <a name="scenekit-framework-enhancements"></a>Miglioramenti di SceneKit Framework

Sono state apportate le seguenti funzionalità avanzate al Framework di SceneKit per macOS Sierra:

- Include ora un nuovo sistema di rendering fisico (PBR, Physical based rendering) per risultati più realistici con la creazione di asset più semplice.
- Utilizzare il nuovo modello di ombreggiatura [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) per produrre un'ampia gamma di effetti ombreggiamenti realistici, richiedendo solo tre proprietà fondamentali (`Diffuse`, `Metalness` e `Roughness`).
- Poiché l'ombreggiatura del PBR funziona meglio con l'illuminazione basata sull'ambiente, usare la proprietà `LightingEnvironment` per assegnare l'illuminazione basata su immagini all'intera scena.
- Usare la proprietà `IESProfileURL` per importare i dispositivi luminosi reali che definiscono la base illuminotecnica sui valori reali, ad esempio l'intensità (in lumen) e la temperatura del colore (in gradi Kelvin).
- La classe [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) può offrire maggiore realismo usando le funzionalità e gli effetti di HDR. Usare l'esposizione adattiva per creare effetti automatici o usare vignette, frangia dei colori e classificazione dei colori per aggiungere effetti FILMATIC al gioco.
- Entrambe le funzionalità della fotocamera PBR e HDR forniscono risultati migliori rispetto alle tecniche di rendering tradizionali e, di conseguenza, SceneKit esegue ora tutti i calcoli dei colori in uno spazio di colore lineare (usando la gamma di colori P3 sulle visualizzazioni dei dispositivi a colori Wide).
- SceneKit Now color corrisponde a tutti i colori leggendo le informazioni sul profilo colori.
- SceneKit interpreta i valori dei componenti dei colori in uno spazio di colore RGB lineare per tutti i tipi di shader.
- Poiché SceneKit legge e regola le informazioni sul profilo colori nelle immagini di trama, usare cataloghi asset per tutte le immagini per assicurarsi che queste informazioni vengano fornite.
- Sia il rendering dello spazio dei colori lineare che il colore Wide possono essere disabilitati specificando le chiavi `SCNDisableLinearSpaceRendering` e `SCNDisableWideGamut` nel `Info.plist`dell'app.
- Compilare i primitivi del poligono arbitrari (caricati da file o generati a livello di codice) per specificare la geometria con la nuova classe [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/documentation/scenekit/scngeometryprimitivetype/scngeometryprimitivetypepolygon?language=objc) .

<a name="Security-Framework-Enhancements" />

## <a name="security-framework-enhancements"></a>Miglioramenti del Framework di sicurezza

Nel Framework di sicurezza sono stati apportati i miglioramenti seguenti per macOS Sierra:

- L'interfaccia `SecKey` è stata modernizzata e unificata in tutte le piattaforme (iOS, tvOS, watchos e macOS).

<a name="SpriteKit-Framework-Enhancements" />

## <a name="spritekit-framework-enhancements"></a>Miglioramenti di SpriteKit Framework

Sono state apportate le seguenti funzionalità avanzate al Framework di SpriteKit per macOS Sierra:

- Tilemaps supporta ora le forme di sezione quadrata, esagonale e isometrica per i giochi 2D, 2.5 D e a scorrimento laterale usando le classi `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` e `SKTileSet`.
- Usare la nuova classe `SKWarpGeometry` per estendere o distorcere il rendering di [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) o [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) . La nuova classe [SKAction](https://developer.apple.com/reference/spritekit/skaction) può essere usata per animare le transizioni tra gli effetti di distorsione.
- Gli shader personalizzati possono fornire attributi (`SKAttribute`) che possono essere configurati separatamente da ogni nodo che usa lo shader fornendo un valore di attributo (`SKAttributeValue`).
- La classe [SKView](https://developer.apple.com/reference/spritekit/skview) fornisce diversi nuovi metodi per fornire un controllo dettagliato su quando e come viene eseguito il rendering di una scena.

<a name="New-Frameworks" />

## <a name="new-frameworks"></a>Nuovi Framework

Per macOS Sierra sono stati aggiunti i Framework seguenti:

- **Framework Intent** : questo Framework consente all'app di esaminare le interazioni, ad esempio le azioni relative a posizione o utente, ed eseguire azioni in base a tali informazioni.
- **SafariServices Framework** : questo Framework consente all'app di sviluppare estensioni di app per Safari, ad esempio i blocchi di contenuto, per MacOS e iOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [Novità di OS X 10,12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
