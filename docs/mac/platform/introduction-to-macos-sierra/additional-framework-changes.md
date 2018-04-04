---
title: MacOS ulteriori modifiche al Framework Sierra
description: Questo articolo vengono illustrate le modifiche aggiuntive, secondarie o miglioramenti alle infrastrutture esistenti per macOS Sierra.
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: a1bc12629a84e9a06cc80882d141bf6a0c2f0c52
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="additional-macos-sierra-framework-changes"></a>MacOS ulteriori modifiche al Framework Sierra

_Questo articolo vengono illustrate le modifiche aggiuntive, secondarie o miglioramenti alle infrastrutture esistenti per macOS Sierra._

<a name="Accelerate-Framework-Enhancements" />

## <a name="accelerate-framework-enhancements"></a>Accelerare i miglioramenti apportati a Framework

Il seguente miglioramento sono state apportate a Framework accelerare per macOS Sierra:

- Aggiunta crominanza (integrale calcolo).
- Funzioni di base aggiunti per la creazione di reti neurali.
- Aggiuntive geometrica le funzioni di predicato per eseguire il test per elementi come l'intersezione di due oggetti geometrici.

<a name="AppKit-Framework-Enhancements" />

## <a name="appkit-framework-enhancements"></a>Miglioramenti apportati a Framework Appkit

Il seguente miglioramento sono state apportate a AppKit Framework per macOS Sierra:

- Numerosi miglioramenti al `NSCollectionView` , ad esempio:
    - **Le sezioni comprimibili** -consente di comprimere una sezione di visualizzazione della raccolta in una singola riga orizzontale.
    - **Mobile intestazioni** -intestazioni e piè di pagina può ora essere resa mobile (in un layout di flusso) utilizzando la stessa API di [UICollectionView](https://developer.apple.com/reference/uikit/uicollectionview) in iOS.
    - **Viste di sfondo scorrevole** -ora è possibile impostare uno sfondo di viste di raccolta da scorrere insieme al contenuto.
- Il passaggio di layout visualizzazione posticipata è stato ottimizzato ed esteso.
- L'API di trascinamento e rilascio include ora il nuovo `NSFilePromiseProvider` e `NSFilePromiseReceiver` classi per il supporto trascinare floccaggio.
- Molti costruttori comodità sono state aggiunte a controlli esistenti:
    -  `NSButton` include nuovi costruttori per la creazione di pulsanti, caselle di controllo e dei pulsanti.
    -  `NSTextField` include nuovi costruttori per la creazione di etichette di sovrascrittura e senza ritorno a capo, etichette con gli attributi e i campi di testo modificabile.
    -  `NSSegmentedControl` include nuovi costruttori per la creazione di controlli segmentati da un gruppo di etichette o immagini.
    -  `NSSlider` include nuovi costruttori per la creazione di dispositivi di scorrimento orizzontale lineare.
    -  `NSImageView` include nuovi costruttori per la creazione di viste di immagine non modificabile da una determinata `NSImage`.
-  Il nuovo `NSGridView` è stato aggiunto in un insieme di viste sub in una griglia con una variabile di dimensioni di righe e colonne che possono essere nascoste o visualizzate in modo dinamico il layout automatico.

<a name="AVFoundation-Framework-Enhancements" />

## <a name="avfoundation-framework-enhancements"></a>Miglioramenti apportati a Framework AVFoundation

Il seguente miglioramento sono state apportate a AVFoundation Framework per macOS Sierra:

- In macOS, l'applicazione non deve implementare diversi [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) comportamenti in base al tipo di contenuto. È sufficiente impostare la `Rate` proprietà e AVFoundation determinerà se sufficiente contenuto è disponibile per la riproduzione senza bloccare.
- Il nuovo `AVPlayerLooper` classe rende più semplice eseguire una determinata parte del supporto di un ciclo durante la riproduzione.
- Il `AVAssetDownloadURLSession` consente di classe per il download e una riproduzione successiva di FairPlay crittografati flussi HLS.

<a name="Core-Data-Framework-Enhancements" />

## <a name="core-data-framework-enhancements"></a>Miglioramenti di Framework dati di base

Il seguente miglioramento sono state apportate al Framework di dati principale per macOS Sierra:

- Radice [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) oggetti supporta simultaneo di generare un errore e di recupero senza serializzazione.
- Il [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) classe gestisce un pool di archivi dati di SQLite.
- Il [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) oggetti con archivi dati SQLite il supporto di modalità Journal WAL la generazione di query nuove funzionalità in contesti di oggetto gestito (MOC) possono essere aggiunti a versioni specifiche di database per il recupero futuro e generare un errore per le transazioni.
- Utilizzo di alto livello `NSPersistenceContainer` al riferimento di `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e altre risorse di configurazione di dati di base.
- Sono stati aggiunti numerosi nuovi metodi pratici `NSManagedObject` rendendo più semplice eseguire operazioni di recupero e di creare sottoclassi.

Per ulteriori informazioni, vedere Apple [Core dati Framework Reference](https://developer.apple.com/reference/coredata).

<a name="Core-Image-Framework-Enhancements" />

## <a name="core-image-framework-enhancements"></a>Miglioramenti apportati a Framework core immagine

Il seguente miglioramento sono state apportate al Framework immagine principale per macOS Sierra:

- Il `ImageWithExtent` metodo il [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) classe può essere utilizzata per inserire un'elaborazione personalizzata l'operazione di filtro. Immagine di base visualizzato o richiamare il callback specificato tra filtri durante l'elaborazione di un'immagine per l'output.
- L'app può ora elaborare immagini in uno spazio di colori di fuori di spazio colore di lavoro del contesto dell'immagine di base tramite la conversione da e verso lo spazio colore prima e dopo l'elaborazione.
- Il kernel Core immagine ora può richiedere un formato di output di pixel specifico.
- Sono stati aggiunti i nuovi filtri di immagine seguenti: `CINinePartTitled`, `CINinePartStretched`, `CIHueSaturationValueGradient`, `CIEdgePreserveUpsampleFilter` e `CIClamp`.

<a name="Foundation-Framework-Enhancements" />

## <a name="foundation-framework-enhancements"></a>Miglioramenti apportati a Framework Foundation

Il seguente miglioramento sono state apportate a Framework Foundation per macOS Sierra:

- Utilizzare il [NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) massa API per la rappresentazione, conversione e la visualizzazione di molte delle unità fisica più comuni, ad esempio, lunghezza, velocità, la durata e temperatura.
- Utilizzare il [NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter) classe per l'analisi e la generazione di ISO 8601 formattato date.
- Usare il nuovo [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe per eseguire calcoli di intervallo di data e ora, ad esempio le durate, per il confronto di intervalli e test per le intersezioni di intervallo.
- Utilizzare il [NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter) classe per analizzare gli elementi di un nome di persona da una stringa.
- Usare il nuovo [NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics) classe per ottenere le metriche per un URL di sessione di rete.

Per ulteriori informazioni, vedere Apple [note sulla versione di base per OS X v10.12 e iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html).

<a name="GameKit-Framework-Enhancements" />

## <a name="gamekit-framework-enhancements"></a>Miglioramenti apportati a Framework GameKit

Il seguente miglioramento sono state apportate a GameKit Framework per macOS Sierra:

- Il **Game Center App** è stato deprecato e rimosso dalla macOS. Se l'app Usa GameKit, si _deve_ presentare la propria interfaccia per visualizzare le caratteristiche di GameKit come classifiche e così via. 
- È stato implementato un nuovo tipo di account iCloud solo per il [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) classe.
- Il nuovo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) classe fornisce una soluzione generalizzata per gestire l'archiviazione di dati persistenti in Game Center. `GKGameSession` gestisce un elenco di lettori e l'applicazione è responsabile implementazione come e quando viene archiviata data partecipante, recuperati o scambiati tra i lettori. In molti casi le sessioni di gioco può sostituire corrispondenze esistenti basato su, corrispondenze in tempo reale o persistente gioco Salva metodi.

<a name="GamePlayKit-Framework-Enhancements" />

## <a name="gameplaykit-framework-enhancements"></a>Miglioramenti apportati a Framework GamePlayKit

Il seguente miglioramento sono state apportate a GamePlayKit Framework per macOS Sierra:

- Generazione di disturbo procedurale è stato aggiunto e può essere utilizzata per migliorare il realismo nelle trame naturali, aggiungere realismo ai movimenti fotocamera e la creazione di gioco avanzati.
- Utilizzo del partizionamento spaziali per partizionare i dati di gioco world per la ricerca efficiente.
- Nuovo responsabile delle strategie Monte Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) è stato aggiunto per il calcolo di spostamento possibili completo.
- È stata aggiunta una nuova API di albero delle decisioni ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) per migliorare al compilazione di gioco.
- È stato aggiunto il supporto 3D per l'agente esistente e i comportamenti di ricerca di percorso utilizzando il nuovo [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) classi.
- Usare il nuovo [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) classe per fornire i percorsi di ricerca naturale ad alte prestazioni.
- Il nuovo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) crea classi combinazione GameplayKit e SpriteKit più semplice che mai.

<a name="Metal-Framework-Enhancements" />

## <a name="metal-framework-enhancements"></a>Miglioramenti apportati a Framework Metal

Il seguente miglioramento sono state apportate a Framework Metal per macOS Sierra:

- App e giochi 3D ora è possono usare _a mosaico_ per il rendering in modo efficiente scene complesse e geometria tramite GPU.
- Usare la specializzazione di funzione per creare una raccolta altamente ottimizzato di materiali e funzioni di combinazione di luce per una scena.
- Fornire un controllo accurato di allocazione delle risorse per ottimizzare le prestazioni di metallo basati su App con risorse heap e Memoryless destinazioni di rendering.

Per ulteriori informazioni, vedere Apple [Guida per programmatori Metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

<a name="ModelIO-Framework-Enhancements" />

## <a name="model-io-framework-enhancements"></a>Miglioramenti al modello di Framework dei / o

Il seguente miglioramento sono state apportate per il Framework dei / o del modello per macOS Sierra:

- È ora supportato il formato del file EUR.
- Usare il nuovo `MDLMaterialPropertyGraph` classe di runtime di supportare facilmente modifiche ai modelli.
- Firma distanza campo è stato aggiunto il supporto di [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) classe.
- Usare il nuovo `MDLLightProbeIrradianceDataSource` classe per semplificare il posizionamento luce Probe.

<a name="Photos-Framework-Enhancements" />

## <a name="photos-framework-enhancements"></a>Miglioramenti apportati a Framework foto

Il seguente miglioramento sono state apportate a Framework foto per macOS Sierra:

- Modifica di foto in tempo reale è ora disponibile per le applicazioni che supportano il framework di foto e per le estensioni di modifica (per l'utilizzo all'interno di foto e fotocamera App).
- Usare il nuovo [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) classe per applicare le modifiche per il video sia ancora il contenuto delle foto in tempo reale.
- Utilizzare il [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) e [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) classi per sfruttare la nuova funzionalità di immagine Core del processore per eseguire modifiche.
- Per supportare le foto in tempo reale, il [PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto) e [PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview) classi sono state trasferite da iOS per macOS.

<a name="SceneKit-Framework-Enhancements" />

## <a name="scenekit-framework-enhancements"></a>Miglioramenti apportati a Framework SceneKit

Il seguente miglioramento sono state apportate a SceneKit Framework per macOS Sierra:

- Include ora un nuovo sistema fisicamente base per il Rendering PBR () per ottenere risultati più realistici con più semplice la creazione di asset.
- Usare il nuovo [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) ombreggiatura del modello di prodotto un'ampia gamma di effetti di ombreggiatura realistica richiedere solo tre proprietà fondamentali (`Diffuse`, `Metalness` e `Roughness`).
- Poiché PBR ombreggiatura funziona meglio con illuminazione basato sull'ambiente, utilizzare il `LightingEnvironment` proprietà per l'assegnazione basata su immagini illuminazione tan intera scena.
- Utilizzare il `IESProfileURL` proprietà da importare impianti di illuminazione reali che definiscono l'illuminazione base ai valori del mondo reale, ad esempio intensità (in lumen) e il colore di temperatura (in gradi Kelvin).
- Il [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) classe può fornire maggiore realismo utilizzando funzioni HDR ed effetti. Utilizzare l'esposizione adattivo per creare effetti automatica o utilizzare vignettatura, la smarginatura colore e la classificazione di colore per aggiungere effetti filmatic per il gioco.
- Funzionalità di fotocamera PBR sia HDR fornire risultati migliori rispetto a tecniche tradizionali per il rendering e, di conseguenza, SceneKit ora esegue tutti i calcoli di colore in uno spazio colore lineare (tramite la gamma di colori P3 su schermi di dispositivi di livello di colore).
- SceneKit ora tutti i colori corrisponde al colore, leggere le informazioni sul profilo del colore.
- SceneKit interpreta i valori dei componenti di colore in uno spazio colore RGB lineare per tutti i tipi di shader.
- Poiché SceneKit legge e modificare informazioni del profilo colori nelle immagini di trama, utilizzare Asset cataloghi per tutte le immagini per verificare che queste informazioni vengono fornite.
- Entrambi lineare per il rendering dello spazio dei colori e colore di livello può essere disabilitata specificando il `SCNDisableLinearSpaceRendering` e `SCNDisableWideGamut` chiavi dell'app `Info.plist`.
- Compilare primati poligono arbitrario (caricato da file o a livello di codice generati) per specificare la geometria con il nuovo [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) classe.

<a name="Security-Framework-Enhancements" />

## <a name="security-framework-enhancements"></a>Miglioramenti della sicurezza Framework

Il seguente miglioramento sono state apportate per il Framework di sicurezza per macOS Sierra:

- Il `SecKey` interfaccia è stata modernizzata e unificata per tutte le piattaforme (iOS, tvOS, watchOS e macOS).

<a name="SpriteKit-Framework-Enhancements" />

## <a name="spritekit-framework-enhancements"></a>Miglioramenti apportati a Framework SpriteKit

Il seguente miglioramento sono state apportate a SpriteKit Framework per macOS Sierra:

- Tilemaps ora supportano forme riquadro quadrato, vite ed effetto per 2D, D 2.5 e giochi di scorrimento sul lato utilizzando il `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` e `SKTileSet` classi.
- Usare il nuovo `SKWarpGeometry` classe per estendere o falsare [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) o [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) per il rendering. Il nuovo [SKAction](https://developer.apple.com/reference/spritekit/skaction) classe può essere utilizzata per aggiungere un'animazione transizioni tra gli effetti warp.
- Shader personalizzato può fornire attributi (`SKAttribute`) che può essere configurata separatamente per ogni nodo che utilizza lo shader fornendo un valore di attributo (`SKAttributeValue`).
- Il [SKView](https://developer.apple.com/reference/spritekit/skview) classe fornisce numerosi nuovi metodi per fornire un controllo accurato quando e come viene eseguito il rendering di una scena.

<a name="New-Frameworks" />

## <a name="new-frameworks"></a>Nuovi Framework

I framework seguenti sono state aggiunte a macOS Sierra:

- **Framework intenti** -questo framework consente all'app di esaminare le interazioni (ad esempio, le azioni di percorso o utente) ed eseguire l'azione in base a tali informazioni.
- **SafariServices Framework** -questo framework consente l'app per lo sviluppo di estensioni dell'app per Safari (ad esempio blocchi di contenuto) per iOS e Mac OS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di Mac](https://developer.xamarin.com/samples/mac/)
- [Novità di OS X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
