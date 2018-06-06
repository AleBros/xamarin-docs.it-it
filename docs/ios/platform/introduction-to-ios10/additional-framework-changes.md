---
title: Modifiche di Framework iOS aggiuntive 10
description: Questo documento descrive modifiche minori e miglioramenti apportati al Framework esistenti in iOS 10 e viene illustrato come utilizzare questi aggiornamenti in xamarin. IOS.
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 4b9a230157593b66446e2949e57a925d94208752
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787559"
---
# <a name="additional-ios-10-frameworks-changes"></a>Modifiche di Framework iOS aggiuntive 10

_Questo articolo vengono illustrate le modifiche aggiuntive, secondarie o miglioramenti alla Framework esistenti per iOS 10._

## <a name="av-foundation-framework-additions"></a>Aggiunte di Framework AV Foundation

Il framework AVFoundation include i miglioramenti seguenti:

- In iOS 10, lo sviluppatore non deve implementare diversi [AVPlayerItem](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/) comportamenti in base al tipo di contenuto. È sufficiente impostare la `Rate` proprietà e AVFoundation determinerà se sufficiente contenuto è disponibile per la riproduzione senza bloccare.
- Il nuovo [AVCapturePhotoOutput](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureFileOutput/) classe sostituisce deprecate `AVCaptureStillImageOutput` classe e fornisce un metodo unificato per la gestione di tutti i flussi di lavoro fotografia di monitoraggio del processo di acquisizione e offrire un controllo complesso e supporto per le nuove funzionalità, ad esempio le foto in tempo reale e il formato di acquisizione non elaborati.
- Il nuovo `AVPlayerLooper` classe rende più semplice eseguire una determinata parte del supporto di un ciclo durante la riproduzione.
- Il `AVAssetDownloadURLSession` consente di classe per il download e una riproduzione successiva di FairPlay crittografati flussi HLS.
- Per impostazione predefinita, il [AVCaptureSession](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureSession/) classe supporta automaticamente l'acquisizione a livello di colore, ampia quando supporta l'hardware del dispositivo. Vedere Apple [iOS Device compatibilità Reference](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599) per altri dettagli.

## <a name="avkit-additions"></a>Aggiunte AVKit

Il framework AVKit include ora il nuovo `UpdatesNowPlayingInfoCenter` proprietà per indicare quando il centro di informazioni di gioco ora devono essere aggiornato.

## <a name="core-data-enhancements"></a>Miglioramenti di dati di base

iOS 10 include i miglioramenti seguenti per il framework di dati principali:

- Il [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) oggetti con archivi dati SQLite il supporto di modalità Journal WAL la generazione di query nuove funzionalità in contesti di oggetto gestito (MOC) possono essere aggiunti a versioni specifiche di database per il recupero futuro e generare un errore per le transazioni.
- Radice [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) oggetti supporta simultaneo di generare un errore e di recupero senza serializzazione.
- Il [NSPersistentStoreCoordinator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/) classe gestisce un pool di archivi dati di SQLite.
- Sono stati aggiunti numerosi nuovi metodi pratici `NSManagedObject` rendendo più semplice eseguire operazioni di recupero e di creare sottoclassi.
- Utilizzo di alto livello `NSPersistenceContainer` al riferimento di `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectModel/) e altre risorse di configurazione di dati di base.

Per ulteriori informazioni, vedere Apple [Core dati Framework Reference](https://developer.apple.com/reference/coredata).

## <a name="core-image-enhancements"></a>Miglioramenti all'immagine di base

iOS 10 rende i miglioramenti seguenti per il framework di immagine di base:

- Lo sviluppatore può ora elaborare immagini in uno spazio di colori di fuori di spazio colore di lavoro del contesto dell'immagine di base tramite la conversione da e verso lo spazio colore prima e dopo l'elaborazione.
- Per i dispositivi iOS che usano le A8 o A9 CPU, è ora supportato il formato di immagine non ELABORATO. Immagine di base fornisce ora supporto per la decodifica delle immagini non ELABORATE da una camera iSight incorporato o da una fotocamera parte 3. Utilizzare il `FilterWithImageData` o `FilterWithImageURL` metodi il [CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/) classe per l'elaborazione di immagini non ELABORATE.
- Numerosi miglioramenti delle prestazioni di rendering apportati per `UIImage` il rendering (se supportato da archivi immagine immagine Core) in `UIImageView` oggetti. 
- `UIImage` gli oggetti con tag wide-gamma verrà eseguito il rendering come livello di colore in `UIImageView` oggetti nei dispositivi iOS che supportano colori ampia.
- Codice kernel immagine di base può richiedere formati di output del pixel specifico.
- Il `ImageWithExtent` metodo il [CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/) classe può essere utilizzata per inserire un'elaborazione personalizzata l'operazione di filtro. Immagine di base visualizzato o richiamare il callback specificato tra filtri durante l'elaborazione di un'immagine per l'output.

Inoltre, sono stati aggiunti i nuovi filtri immagine di base seguenti:

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>Aggiunte di movimento core

Nuovo in iOS 10, il framework di movimento Core include eventi pedometer che consentono la rapida e ricevere notifiche in tempo reale dell'utente, sospensione e ripresa di rilevamento durante l'esecuzione di un'app. Utilizzare il [CMPedometer](https://developer.xamarin.com/api/type/CoreMotion.CMPedometer/) per registrare gli eventi pedometer in primo piano o in background.

## <a name="foundation-enhancements"></a>Miglioramenti di Foundation

I miglioramenti seguenti sono stati apportati a framework Foundation per iOS 10:

- Usare il nuovo [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) classe per formattare le misurazioni localizzate per la visualizzazione per l'utente finale.
- Usare il nuovo [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe per eseguire calcoli di intervallo di data e ora, ad esempio le durate, per il confronto di intervalli e test per le intersezioni di intervallo.
- Usare il nuovo [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) classe per convertire tra diverse unità di misura (UDM) o eseguire calcoli sui valori di unità diversa.

- Usare il nuovo [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) classi per la rappresentazione di unità specifiche.
- Diverse nuove proprietà sono stati aggiunti i [NSLocal](https://developer.apple.com/reference/foundation/nslocale) classe per acquisire informazioni locali e i formati di visualizzazione disponibili.

## <a name="gamekit-enhancements"></a>Miglioramenti di GameKit

I miglioramenti seguenti sono stati apportati a framework GameKit iOS 10:

- Il **Game Center App** è stato deprecato e rimosso da iOS. Se l'app Usa GameKit, si _deve_ presentare la propria interfaccia per visualizzare le caratteristiche di GameKit come classifiche e così via. 
- È stato implementato un nuovo tipo di account iCloud solo per il [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) classe.
- Il nuovo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) classe fornisce una soluzione generalizzata per gestire l'archiviazione di dati persistenti in Game Center. `GKGameSession` gestisce un elenco di lettori e l'applicazione è responsabile dell'implementazione come e quando Data partecipante è archiviato, recuperato o scambiato tra i lettori. In molti casi le sessioni di gioco può sostituire corrispondenze esistenti basato su, corrispondenze in tempo reale o persistente gioco Salva metodi.

## <a name="gameplaykit-enhancements"></a>Miglioramenti di GameplayKit

I miglioramenti seguenti sono stati apportati a framework GameplayKit iOS 10:

- Usare il nuovo [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) classe per fornire i percorsi di ricerca naturale ad alte prestazioni.
- Generazione di disturbo procedurale è stato aggiunto e può essere utilizzata per migliorare il realismo nelle trame naturali, aggiungere realismo ai movimenti fotocamera e la creazione di gioco avanzati.
- Utilizzo del partizionamento spaziali per partizionare i dati di gioco world per la ricerca efficiente.
- Nuovo responsabile delle strategie Monte Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) è stato aggiunto per il calcolo di spostamento possibili completo.
- È stato aggiunto il supporto 3D per l'agente esistente e i comportamenti di ricerca di percorso utilizzando il nuovo [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) classi.
- Il nuovo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) crea classi combinazione GameplayKit e SpriteKit più semplice che mai.
- È stata aggiunta una nuova API di albero delle decisioni ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) per migliorare al compilazione di gioco.

## <a name="healthkit-enhancements"></a>Miglioramenti di HealthKit

I miglioramenti seguenti sono stati apportati a framework HealthKit iOS 10:

- Nuove chiavi di metadati sono stati aggiunti per i tipi di meteo (ad esempio `HKWeatherConditionClear` e `HKWeatherConditionCloudy`) e tipi allenamenti (ad esempio `HKWorkoutActivityTypeFlexibility` e `HKWorkoutActivityTypeWheelchairRunPace`) sono stati aggiunti.
- Il nuovo `HKCDADocument` classe è stata aggiunta per rappresentare un'architettura di documento clinici (CDA) formato di documento.
- Usare il nuovo [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) classe per specificare il `ActivityType` e `LocationType` di accinge.
- Il nuovo [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) e `WheelchairUse` metodo il [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) classe sono stati aggiunti per l'utilizzo con sedia integrità dati correlati.

## <a name="homekit-enhancements"></a>Miglioramenti di HomeKit

I miglioramenti seguenti sono stati apportati a framework HomeKit iOS 10:

- Sono state aggiunte le caratteristiche e i nuovi servizi.
- Un iPad può essere configurato per operare come un HomeKit Hub per fornire l'accesso remoto di accessori, eseguire i trigger di automazione e abilitare condiviso autorizzazioni utente.
- È stato aggiunto il supporto per accessories fotocamere e di campanello della porta.
- Per accessories sono state fornite più contesto e le configurazioni.

Consultare il nostro [Introduzione a HomeKit](~/ios/platform/homekit.md) documentazione per ulteriori informazioni.

## <a name="metal-enhancements"></a>Miglioramenti metalli

I miglioramenti seguenti sono stati apportati a Metal framework iOS 10:

- App e giochi 3D ora è possono usare _a mosaico_ per il rendering in modo efficiente scene complesse e geometria tramite GPU.
- Fornire un controllo accurato di allocazione delle risorse per ottimizzare le prestazioni di metallo basati su App con risorse heap e Memoryless destinazioni di rendering.
- Usare la specializzazione di funzione per creare una raccolta altamente ottimizzato di materiali e funzioni di combinazione di luce per una scena.

Per ulteriori informazioni, vedere Apple [Guida per programmatori Metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

## <a name="modelio-enhancements"></a>Miglioramenti di ModelIO

I miglioramenti seguenti sono stati apportati a framework ModelIO iOS 10:

 - È ora supportato il formato del file EUR.
 - Firma distanza campo è stato aggiunto il supporto di [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) classe.
 - Usare il nuovo `MDLLightProbeIrradianceDataSource` classe per semplificare il posizionamento luce Probe.
 - Usare il nuovo `MDLMaterialPropertyGraph` classe di runtime di supportare facilmente modifiche ai modelli.

## <a name="photos-enhancements"></a>Miglioramenti di foto

I miglioramenti seguenti sono stati apportati al framework di foto in iOS 10:

- Utilizzare il [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) e [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) classi per sfruttare la nuova funzionalità di immagine Core del processore per eseguire modifiche.
- Modifica di foto in tempo reale è ora disponibile per le applicazioni che supportano il framework di foto e per le estensioni di modifica (per l'utilizzo all'interno di foto e fotocamera App).
- Usare il nuovo [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) classe per applicare le modifiche per il video sia ancora il contenuto delle foto in tempo reale.

## <a name="replaykit-enhancements"></a>Miglioramenti di ReplayKit

I miglioramenti seguenti sono stati apportati a framework ReplayKit iOS 10:

- Utilizzare il [RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder), [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller) e [RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller) classi per supportare la trasmissione di registrati supporti tramite 3rd party siti.
- Le estensioni dell'interfaccia utente di trasmissione e di trasmissione caricare devono supportare ReplayKit 3rd party broadcast servizi nell'app.

## <a name="scenekit-enhancements"></a>Miglioramenti di SceneKit

I miglioramenti seguenti sono stati apportati a framework SceneKit iOS 10:

- Il [SCNCamera](https://developer.xamarin.com/api/type/SceneKit.SCNCamera/) classe può fornire maggiore realismo utilizzando funzioni HDR ed effetti. Utilizzare l'esposizione adattivo per creare effetti automatico o utilizzare vignettatura, la smarginatura colore e la classificazione di colore per aggiungere effetti fillmatic per il gioco.
- SceneKit include ora un nuovo sistema fisicamente base per il Rendering PBR () per ottenere risultati più realistici con più semplice la creazione di asset.
- Usare il nuovo [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) ombreggiatura del modello di prodotto un'ampia gamma di effetti di ombreggiatura realistica richiedere solo tre proprietà fondamentali (`Diffuse`, `Metalness` e `Roughness`).
- Poiché PBR ombreggiatura funziona meglio con illuminazione basato sull'ambiente, utilizzare il `LightingEnvironment` proprietà per l'assegnazione basata su immagini illuminazione per un'intera scena.
- Utilizzare il `IESProfileURL` proprietà da importare impianti di illuminazione reali che definiscono l'illuminazione in base ai valori del mondo reale, ad esempio intensità (in lumen) e la temperatura colore (in gradi Kelvin).
- Funzionalità di fotocamera PBR sia HDR fornire risultati migliori rispetto a tecniche tradizionali per il rendering e, di conseguenza, SceneKit ora esegue tutti i calcoli di colore in uno spazio colore lineare (tramite la gamma di colori P3 su schermi di dispositivi di livello di colore).
- SceneKit ora tutti i colori corrisponde al colore, leggere le informazioni sul profilo del colore.
- SceneKit interpreta i valori dei componenti di colore in uno spazio colore RGB lineare per tutti i tipi di shader.
- Entrambi lineare per il rendering dello spazio dei colori e colore di livello può essere disabilitata specificando il `SCNDisableLinearSpaceRendering` e `SCNDisableWideGamut` chiavi dell'app `Info.plist`.
- Compilare primati poligono arbitrario (caricato da file o a livello di codice generati) per specificare la geometria con il nuovo [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) classe.
- Poiché SceneKit legge e modificare informazioni del profilo colori nelle immagini di trama, utilizzare Asset cataloghi per tutte le immagini per verificare che queste informazioni vengono fornite.

## <a name="spritekit-enhancements"></a>Miglioramenti di SpriteKit

I miglioramenti seguenti sono stati apportati a framework SpriteKit iOS 10:

- Shader personalizzato può fornire attributi (`SKAttribute`) che può essere configurata separatamente per ogni nodo che utilizza lo shader fornendo un valore di attributo (`SKAttributeValue`).
- Tilemaps ora supportano forme riquadro quadrato, vite ed effetto per 2D, D 2.5 e giochi di scorrimento sul lato utilizzando il `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` e `SKTileSet` classi.
- Usare il nuovo `SKWarpGeometry` classe per estendere o falsare [SKSpriteNode](https://developer.xamarin.com/api/type/SpriteKit.SKSpriteNode/) o [SKEffectNode](https://developer.xamarin.com/api/type/SpriteKit.SKEffectNode/) per il rendering. Il nuovo [SKAction](https://developer.xamarin.com/api/type/SpriteKit.SKAction/) classe può essere utilizzata per aggiungere un'animazione transizioni tra gli effetti warp.
- Il [SKView](https://developer.xamarin.com/api/type/SpriteKit.SKView/) classe fornisce numerosi nuovi metodi per fornire un controllo accurato quando e come viene eseguito il rendering di una scena.

## <a name="scrollview-enhancements"></a>Miglioramenti elemento ScrollView

I miglioramenti seguenti sono stati apportati al controllo elemento ScrollView in iOS 10.3:

- `UIScrollView` includono ora il `IndexDisplayMode` proprietà per controllare la modalità di visualizzazione dell'indice mentre l'utente scorre come un `UIScrollViewIndexDisplayMode` di:
    - `Automatic` -La visualizzazione dell'indice è controllata dal sistema operativo.
    - `AlwaysHidden` -La visualizzazione dell'indice è sempre nascosto.

Vedere il [iOSTenThree esempio](https://developer.xamarin.com/samples/monotouch/iOS10/iOSTenThree) per l'utilizzo.

## <a name="uikit-enhancements"></a>Miglioramenti di UIKit

I miglioramenti seguenti sono stati apportati a framework UIKit iOS 10:

- Il nuovo [UIPasteboard](https://developer.xamarin.com/api/type/UIKit.UIPasteboard/) API sono disponibili nuove opzioni (ad esempio i limiti di durata) e verrà automaticamente dichiarare tipi di contenuto compatibili per i tipi di classi comuni.
- Nuovo supporto di animazione completamente interattivo, oggetto, che può essere interrotto è stato aggiunto e può essere collegato ai movimenti. Del pleas vedere Apple [UIViewAnimating Protocol Reference](https://developer.apple.com/reference/uikit/uiviewanimating), [riferimento alla classe UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), [UITimingCurveProvider Protocol Reference](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [Riferimento alla classe UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) e [riferimento alla classe UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) per ulteriori informazioni.
- Il nuovo `UIPreviewInteraction` e `UIPreviewInteractionDelegate` consentono all'app per sviluppatori di fornire un'interfaccia personalizzata per le operazioni di lettura e pop.
- Il nuovo `UIAccessibilityCustomRotor` classe consente all'app fornire funzionalità personalizzate, specifiche per il contesto a dispositivi di assistive technology, ad esempio Voice su.
- Utilizzare il `UIAccessibilityIsAssistiveTouchRunning` e `UIAccessibilityAssistiveTouchStatusDidChangeNotification` simboli per determinare se AssistiveTouch è abilitata.
- Utilizzare il `UIAccessibilityHearingDevicePairedEar` e `UIAccessibilityHearingDevicePairedEarDidChangeNotification` simboli per ottenere lo stato di qualsiasi abbinato IFM udito visivi.
- Per supportare tipi dinamici nelle etichette, usano i nuovi campi di testo e caselle di testo `PreferredFontForTextStyle` metodo la `UIFont` classe.
- Per decidere se un elemento è necessario aggiornare il tipo di carattere quando il dispositivo `UIContentSizeCategory` le modifiche, utilizzare il `AdjustsFontForContentSizeCategory` proprietà del `UIContentSizeCategoryAdjusting` delegato.
- Il `OpenURL` metodo la `UIApplication` classe viene chiamato in modo asincrono e supporta ora un gestore di completamento che viene chiamato una volta completata l'azione di apertura.
- Avviare la condivisione CloudKit e modificarne le proprietà utilizzando il nuovo `UICloudSharingController` e `UICloudSharingControllerDelegate` classi.
- Sfruttare i vantaggi di celle caricate in background per migliorare l'esperienza di scorrimento `UICollectionViews` con il nuovo `UICollectionViewDataSourcePrefetching` delegato.
- Lo sviluppatore è ora possibile controllare l'aspetto del badge per gli elementi della barra della scheda (ad esempio il colore di sfondo e di testo).
- Il controllo di aggiornamento è ora supportato in tutte le visualizzazione a scorrimento e sottoclassi visualizzazione scorrimento (ad esempio `UICollectionView`).

## <a name="webkit-enhancements"></a>Miglioramenti di WebKit

I miglioramenti seguenti sono stati apportati a framework WebKit iOS 10:

- Peek e supporto pop è stato aggiunto il `WKWebView` classe. Utilizzare il `ShouldPreviewElement` metodo per determinare se una visualizzazione web specificato deve visualizzare un'anteprima.


## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Novità di iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
