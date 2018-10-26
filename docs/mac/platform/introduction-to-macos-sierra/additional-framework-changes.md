---
title: Aggiuntive macOS Sierra Framework modifiche
description: Questo documento descrive i miglioramenti e modifiche minori alle infrastrutture esistenti, introdotti in macOS Sierra. Esamina le modifiche per il framework Accelerate AppKit, AVFoundation, Core Data, immagine di base, Foundation e più.
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 6ed827c018931e5b79887dc355f136e2a84623d6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111051"
---
# <a name="additional-macos-sierra-framework-changes"></a>Aggiuntive macOS Sierra Framework modifiche

<a name="Accelerate-Framework-Enhancements" />

## <a name="accelerate-framework-enhancements"></a>Accelera i miglioramenti apportati Framework

I miglioramenti seguenti apportati a Framework accelerare per macOS Sierra:

- Aggiunta crominanza (integrale calcolo).
- Funzioni di base aggiunte per la creazione di reti neurali.
- Aggiunte le funzioni di predicato geometriche da testare per operazioni come l'intersezione di due oggetti geometrici.

<a name="AppKit-Framework-Enhancements" />

## <a name="appkit-framework-enhancements"></a>Miglioramenti apportati AppKit Framework

Sono stati apportati i miglioramenti seguenti per il Framework di AppKit per macOS Sierra:

- Numerosi miglioramenti al `NSCollectionView` , ad esempio:
    - **Le sezioni comprimibili** -consente all'utente di comprimere una sezione di visualizzazione dell'insieme in una singola riga orizzontale.
    - **Mobile intestazioni** -le intestazioni e piè di pagina possono essere spostate ora (in un layout di flusso) usando la stessa API [UICollectionView](https://developer.apple.com/reference/uikit/uicollectionview) in iOS.
    - **Viste in Background scorrevole** -uno sfondo di viste di raccolta può ora essere impostato su scorrere insieme al contenuto.
- Il passaggio di layout di visualizzazione rinviata è stato ottimizzato ed esteso.
- L'API di trascinamento e rilascio include ora il nuovo `NSFilePromiseProvider` e `NSFilePromiseReceiver` trascinare classi di supporto uccelli.
- Diversi costruttori di praticità sono state aggiunte a controlli esistenti:
    -  `NSButton` include i nuovi costruttori per la creazione di pulsanti di comando, pulsanti di opzione e caselle di controllo.
    -  `NSTextField` include i nuovi costruttori per la creazione di etichette di ritorno a capo e non a capo automatico, le etichette con Attribute e i campi di testo modificabile.
    -  `NSSegmentedControl` include i nuovi costruttori per la creazione di controlli segmentati da un gruppo di etichette o immagini.
    -  `NSSlider` include i nuovi costruttori per la creazione di dispositivi di scorrimento orizzontale lineare.
    -  `NSImageView` include nuovi costruttori per creare le visualizzazioni di immagini non modificabili da un determinato `NSImage`.
-  Il nuovo `NSGridView` è stato aggiunto al layout automatico dimensioni di una raccolta di visualizzazioni secondarie in una griglia con variabile di righe e colonne che possono essere nascoste o visualizzate in modo dinamico.

<a name="AVFoundation-Framework-Enhancements" />

## <a name="avfoundation-framework-enhancements"></a>Miglioramenti apportati AVFoundation Framework

I miglioramenti seguenti apportati a AVFoundation Framework per macOS Sierra:

- In macOS, l'app non è più necessario implementare diversi [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) comportamenti in base al tipo di contenuto. È sufficiente impostare il `Rate` proprietà e AVFoundation determineranno quando sufficiente contenuto è disponibile per la riproduzione senza blocco.
- Il nuovo `AVPlayerLooper` classe rende più semplice eseguire il ciclo di una determinata parte del supporto durante la riproduzione.
- Il `AVAssetDownloadURLSession` classe consente il download e la successiva riproduzione di FairPlay crittografati flussi HLS.

<a name="Core-Data-Framework-Enhancements" />

## <a name="core-data-framework-enhancements"></a>Miglioramenti apportati a Framework core Data

Sono stati apportati i miglioramenti seguenti per il Framework di dati di Core per macOS Sierra:

- Radice [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) oggetti supporta simultaneo di eventi di errore e il recupero senza serializzazione.
- Il [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) classe gestisce un pool di archivi dati di SQLite.
- Il [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) oggetti con gli archivi dati di SQLite nel supporto della modalità di registrazione log write-AHEAD di nuova generazione di query in cui i contesti di oggetto gestito (MOC) possono essere aggiunti alle versioni di database specifico per il recupero futuro delle funzionalità e generare un errore per le transazioni.
- Utilizzo di alto livello `NSPersistenceContainer` per fare riferimento al `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e altre risorse di configurazione di Core Data.
- Sono stati aggiunti alcuni nuovi metodi pratici per `NSManagedObject` rendendo più semplice eseguire operazioni di recupero e creare sottoclassi.

Per altre informazioni, vedere di Apple [riferimento a Framework di Core Data](https://developer.apple.com/reference/coredata).

<a name="Core-Image-Framework-Enhancements" />

## <a name="core-image-framework-enhancements"></a>Miglioramenti apportati a Framework immagine core

Sono stati apportati i miglioramenti seguenti per il Framework di immagine di Core per macOS Sierra:

- Il `ImageWithExtent` metodo per il [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) classe può essere utilizzata per inserire istruzioni di elaborazione personalizzata nell'operazione di filtro. Immagine di base visualizzato o richiamare il callback specificato da un filtro durante l'elaborazione di un'immagine per l'output.
- L'app può ora elaborare immagini in uno spazio dei colori di fuori di spazio dei colori del contesto l'immagine di base funzionante mediante la conversione da e verso lo spazio colore prima e dopo l'elaborazione.
- Il kernel di immagine di base può ora richiedere un formato di output di pixel specifico.
- Sono stati aggiunti i nuovi filtri di immagine seguenti: `CINinePartTitled`, `CINinePartStretched`, `CIHueSaturationValueGradient`, `CIEdgePreserveUpsampleFilter` e `CIClamp`.

<a name="Foundation-Framework-Enhancements" />

## <a name="foundation-framework-enhancements"></a>Miglioramenti apportati a Framework Foundation

I miglioramenti seguenti apportati a Framework Foundation per macOS Sierra:

- Usare la [NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) massa di API per la rappresentazione, conversione e la visualizzazione di molte delle unità fisiche più comuni, ad esempio, lunghezza, velocità, la durata e temperature.
- Usare la [NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter) classe per l'analisi e generazione di ISO 8601 formattato date.
- Usare le nuove [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe per rendere i calcoli degli intervalli di data e ora, ad esempio le durate, per il confronto di intervalli e i test per le intersezioni di intervallo.
- Usare la [NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter) classe analizzare gli elementi del nome di una persona da una stringa.
- Usare le nuove [NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics) classe per ottenere le metriche per un URL della sessione di rete.

Per altre informazioni, vedere di Apple [Foundation note sulla versione per iOS 10 e OS X v10.12](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html).

<a name="GameKit-Framework-Enhancements" />

## <a name="gamekit-framework-enhancements"></a>Miglioramenti apportati GameKit Framework

I miglioramenti seguenti apportati a GameKit Framework per macOS Sierra:

- Il **Game Center App** è deprecato ed è stato rimosso da macOS. Se l'app Usa GameKit, si _necessario_ presentare la propria interfaccia per visualizzare le funzionalità GameKit come tabelloni punteggi e così via. 
- È stato implementato un nuovo tipo di account iCloud-solo per i [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) classe.
- Il nuovo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) classe fornisce una soluzione generalizzata per la gestione di archiviazione persistente dei dati in Game Center. `GKGameSession` gestisce un elenco di giocatori e l'app è responsabile della forma che implementa i casi in cui è archiviato data partecipante, recuperato o scambiati tra i lettori. In molti casi è possibile sostituire le sessioni di giochi corrispondenze esistente basata su turni, corrispondenze in tempo reale o gioco permanente Salva i metodi.

<a name="GamePlayKit-Framework-Enhancements" />

## <a name="gameplaykit-framework-enhancements"></a>Miglioramenti apportati GamePlayKit Framework

I miglioramenti seguenti apportati a GamePlayKit Framework per macOS Sierra:

- Generazione di rumore procedurale è stato aggiunto e può essere usata per migliorare il realismo nelle trame realistiche, aggiungere più realistiche per i movimenti della fotocamera e generare scenari di gioco avanzati.
- Usare il partizionamento spaziali per partizionare i dati del mondo di giochi per la ricerca efficiente.
- Un nuovo strategist Monte Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) è stato aggiunto per il calcolo completo spostamento possibili.
- È stata aggiunta una nuova API di albero delle decisioni ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) per migliorare la compilazione di gioco per intelligenza artificiale.
- È stato aggiunto supporto 3D per l'agente esistente e i comportamenti di percorso di ricerca usando le nuove [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) classi.
- Usare le nuove [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) classe per fornire percorsi realistiche ad alte prestazioni.
- Il nuovo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) marca classi combinazione GameplayKit e SpriteKit più facile che mai.

<a name="Metal-Framework-Enhancements" />

## <a name="metal-framework-enhancements"></a>Miglioramenti apportati Framework bare metal

I miglioramenti seguenti apportati a Framework bare Metal per macOS Sierra:

- App e giochi 3D ora è possono usare _dello schema a mosaico_ eseguire in modo efficiente il rendering di scene complesse e geometry tramite GPU.
- Utilizzare la specializzazione di funzione per creare una raccolta altamente ottimizzato di materiale e funzioni di combinazione di luce per una scena.
- Fornire un controllo accurato di allocazione delle risorse per ottimizzare le prestazioni di Metal basati su App con risorse heap e Memoryless destinazioni di rendering.

Per altre informazioni, vedere di Apple [Guida per programmatori Metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

<a name="ModelIO-Framework-Enhancements" />

## <a name="model-io-framework-enhancements"></a>Miglioramenti al modello di Framework dei / o

Sono stati apportati i miglioramenti seguenti per il Framework dei / o del modello per macOS Sierra:

- È ora supportato il formato di file USD.
- Usare il nuovo `MDLMaterialPropertyGraph` classe per agevolare il supporto runtime modifiche ai modelli.
- Accesso automatico a distanza campo è stato aggiunto il supporto per la [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) classe.
- Usare il nuovo `MDLLightProbeIrradianceDataSource` classe per assistere nella selezione host per il Probe di luce.

<a name="Photos-Framework-Enhancements" />

## <a name="photos-framework-enhancements"></a>Miglioramenti apportati a Framework foto

I miglioramenti seguenti apportati a Framework foto per macOS Sierra:

- Modifica foto in tempo reale è ora disponibile per le app che supportano il framework di foto e per le estensioni di modifica foto (per l'uso all'interno di foto e fotocamera App).
- Usare le nuove [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) classe per applicare le modifiche per il video sia ancora il contenuto della foto in tempo reale.
- Usare la [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) e [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) classi possa sfruttare i vantaggi della nuova funzionalità di immagine di Core del processore per eseguire modifiche.
- Per supportare Live foto, il [PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto) e [PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview) classi sono state convertite da iOS per macOS.

<a name="SceneKit-Framework-Enhancements" />

## <a name="scenekit-framework-enhancements"></a>Miglioramenti apportati a Framework di SceneKit

I miglioramenti seguenti apportati a SceneKit Framework per macOS Sierra:

- Include ora un nuovo sistema fisicamente basato su Rendering PBR () per ottenere risultati più realistici con più semplice la creazione di asset.
- Usare le nuove [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) ombreggiatura del modello di prodotto una vasta gamma di effetti di ombreggiatura realistica questa tecnologia richiede solo tre proprietà fondamentali (`Diffuse`, `Metalness` e `Roughness`).
- Poiché PBR ombreggiatura funziona meglio con illuminazione basata su ambiente, usare il `LightingEnvironment` proprietà per l'assegnazione basata su immagini di illuminazione per tan intera scena.
- Usare il `IESProfileURL` proprietà da importare impianti reali che definiscono l'illuminazione di base sui valori reali, ad esempio intensità (in lumen) e il colore di temperatura (in gradi Kelvin).
- Il [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) classe può offrire maggiore realismo usando funzionalità HDR e gli effetti. Usare l'esposizione adattivo per creare effetti automatico o usare vignettatura, la smarginatura colore e la classificazione di colore per aggiungere effetti filmatic al gioco.
- Le funzionalità di fotocamera PBR sia HDR offrono risultati migliori rispetto a tecniche tradizionali per il rendering e, di conseguenza, SceneKit esegue ora tutti i calcoli di colore in uno spazio dei colori lineare (usando la gamma di colori P3 su schermi di dispositivi a livello di colore).
- SceneKit ora tutti i colori corrisponde al colore, leggere le informazioni sul profilo del colore.
- SceneKit interpreta i valori di componente di colore in uno spazio dei colori RGB lineare per tutti i tipi di shader.
- Poiché SceneKit legge e modificare informazioni sul profilo di colore nelle immagini di trama, utilizzare cataloghi Asset per tutte le immagini per assicurarsi che queste informazioni sono disponibili.
- Entrambi lineare per il rendering dello spazio dei colori e a livello di colore può essere disabilitata specificando il `SCNDisableLinearSpaceRendering` e `SCNDisableWideGamut` le chiavi dell'app `Info.plist`.
- Compilare primati poligono arbitrario (caricato da file o a livello di codice generati) per specificare la geometria con il nuovo [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) classe.

<a name="Security-Framework-Enhancements" />

## <a name="security-framework-enhancements"></a>Miglioramenti apportati ai Framework di sicurezza

Sono stati apportati i miglioramenti seguenti per il Framework di sicurezza per macOS Sierra:

- Il `SecKey` interfaccia è stata modernizzata e unificata per tutte le piattaforme (iOS, tvOS, watchOS e macOS).

<a name="SpriteKit-Framework-Enhancements" />

## <a name="spritekit-framework-enhancements"></a>Miglioramenti apportati Framework SpriteKit

I miglioramenti seguenti apportati a SpriteKit Framework per macOS Sierra:

- Tilemaps supportano ora le forme di riquadro quadrato, esagonale e isometrica per 2D, 2,5 D e lo scorrimento laterale giochi usando il `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` e `SKTileSet` classi.
- Usare le nuove `SKWarpGeometry` classe per estendere o distorcere [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) oppure [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) per il rendering. Il nuovo [SKAction](https://developer.apple.com/reference/spritekit/skaction) classe può essere utilizzata per aggiungere un'animazione transizioni tra gli effetti di warp.
- Gli shader personalizzati possono fornire attributi (`SKAttribute`) che può essere configurata separatamente per ogni nodo che utilizza lo shader fornendo un valore di attributo (`SKAttributeValue`).
- Il [SKView](https://developer.apple.com/reference/spritekit/skview) classe fornisce numerosi nuovi metodi per garantire il controllo con granularità fine su come e quando viene eseguito il rendering di una scena.

<a name="New-Frameworks" />

## <a name="new-frameworks"></a>Nuovi Framework

I framework seguenti sono state aggiunte a macOS Sierra:

- **Framework Intent** -questo framework consentire all'app esaminare le interazioni (ad esempio le azioni di percorso o un utente) e agire su tali informazioni.
- **SafariServices Framework** -questo framework consentire all'app sviluppare le estensioni dell'app per Safari (ad esempio blocchi di contenuto) per iOS e macOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di Mac](https://developer.xamarin.com/samples/mac/)
- [Novità in OS X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
