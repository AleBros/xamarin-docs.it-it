---
title: Modifiche aggiuntive iOS 10 Framework
description: Questo documento descrive piccole modifiche e miglioramenti apportati al Framework esistenti in iOS 10 e illustra come rendere utilizzare questi aggiornamenti in xamarin. IOS.
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: 6ae08264218c8f959b351f059d73fc0aebfea39e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118084"
---
# <a name="additional-ios-10-frameworks-changes"></a>Modifiche aggiuntive iOS 10 Framework

_Questo articolo illustra le modifiche aggiuntive, secondarie o miglioramenti alle infrastrutture esistenti per iOS 10._

## <a name="av-foundation-framework-additions"></a>Aggiunte di Framework AV Foundation

Il framework AVFoundation include i miglioramenti seguenti:

- In iOS 10, lo sviluppatore non deve più implementare diversi [AVPlayerItem](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/) comportamenti in base al tipo di contenuto. È sufficiente impostare il `Rate` proprietà e AVFoundation determineranno quando sufficiente contenuto è disponibile per la riproduzione senza blocco.
- Il nuovo [AVCapturePhotoOutput](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureFileOutput/) classe sostituisce deprecate `AVCaptureStillImageOutput` classe e fornisce un metodo unificato per la gestione di tutti i flussi di lavoro fotografia fornendo sofisticato controllo e il monitoraggio del processo di acquisizione e supporto per nuove funzionalità quali Live foto e il formato di acquisizione non ELABORATO.
- Il nuovo `AVPlayerLooper` classe rende più semplice eseguire il ciclo di una determinata parte del supporto durante la riproduzione.
- Il `AVAssetDownloadURLSession` classe consente il download e la successiva riproduzione di FairPlay crittografati flussi HLS.
- Per impostazione predefinita, il [AVCaptureSession](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureSession/) classe supporta automaticamente l'acquisizione a livello di colori, a livello di gamma quando l'hardware del dispositivo lo supporta. Vedere di Apple [iOS Device compatibilità Reference](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599) per altri dettagli.

## <a name="avkit-additions"></a>Aggiunte AVKit

Il framework AVKit include ora il nuovo `UpdatesNowPlayingInfoCenter` proprietà per indicare quando il centro di informazioni d'azione ora deve essere aggiornato.

## <a name="core-data-enhancements"></a>Miglioramenti di dati principali

iOS 10 include i miglioramenti seguenti per il framework di Core Data:

- Il [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) oggetti con gli archivi dati di SQLite nel supporto della modalità di registrazione log write-AHEAD di nuova generazione di query in cui i contesti di oggetto gestito (MOC) possono essere aggiunti alle versioni di database specifico per il recupero futuro delle funzionalità e generare un errore per le transazioni.
- Radice [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) oggetti supporta simultaneo di eventi di errore e il recupero senza serializzazione.
- Il [NSPersistentStoreCoordinator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/) classe gestisce un pool di archivi dati di SQLite.
- Sono stati aggiunti alcuni nuovi metodi pratici per `NSManagedObject` rendendo più semplice eseguire operazioni di recupero e creare sottoclassi.
- Utilizzo di alto livello `NSPersistenceContainer` per fare riferimento al `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectModel/) e altre risorse di configurazione di Core Data.

Per altre informazioni, vedere di Apple [riferimento a Framework di Core Data](https://developer.apple.com/reference/coredata).

## <a name="core-image-enhancements"></a>Miglioramenti all'immagine di base

iOS 10 rende i miglioramenti seguenti per il framework di immagine di base:

- Lo sviluppatore può ora elaborare immagini in uno spazio dei colori di fuori di spazio dei colori del contesto l'immagine di base funzionante mediante la conversione da e verso lo spazio colore prima e dopo l'elaborazione.
- Per i dispositivi iOS che usano i A8 o A9 CPU, è ora supportato il formato di immagine non ELABORATA. Immagine di base fornisce ora supporto per la decodifica delle immagini non ELABORATE da una fotocamera iSight incorporato o dalla fotocamera 3rd party. Usare la `FilterWithImageData` o `FilterWithImageURL` metodi delle [CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/) classe per l'elaborazione di immagini non ELABORATE.
- Numerosi miglioramenti delle prestazioni per il rendering sono stato apportati a `UIImage` rendering (se supportata da archivi di immagine di immagine di base) in `UIImageView` oggetti. 
- `UIImage` scala a livello di oggetti con tag verrà eseguito il rendering come a livello di colore in `UIImageView` oggetti nei dispositivi iOS che supportano l'ampia gamma di colori.
- Codice kernel immagine di base può ora richiedere formati di output di pixel specifico.
- Il `ImageWithExtent` metodo per il [CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/) classe può essere utilizzata per inserire istruzioni di elaborazione personalizzata nell'operazione di filtro. Immagine di base visualizzato o richiamare il callback specificato da un filtro durante l'elaborazione di un'immagine per l'output.

Inoltre, sono stati aggiunti i nuovi filtri di immagine di base seguenti:

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>Aggiunte di movimento core

Nuovo ai dispositivi iOS 10, il framework di movimento Core include eventi pedometer quale attivare un'app per la ricezione veloci e notifiche in tempo reale dell'utente sospensione e ripresa di rilevamento durante l'esecuzione. Usare la [CMPedometer](https://developer.xamarin.com/api/type/CoreMotion.CMPedometer/) per registrare gli eventi pedometer foreground o background.

## <a name="foundation-enhancements"></a>Miglioramenti di Foundation

I miglioramenti seguenti sono stato apportati a framework Foundation per iOS 10:

- Usare le nuove [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) classe per formattare le misurazioni localizzate per la visualizzazione all'utente finale.
- Usare le nuove [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe per rendere i calcoli degli intervalli di data e ora, ad esempio le durate, per il confronto di intervalli e i test per le intersezioni di intervallo.
- Usare le nuove [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) classe convertire tra diverse unità di misura (UDM) o eseguire calcoli sui valori di unità diversa.

- Usare le nuove [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) classi per la rappresentazione di unità specifica.
- Diverse nuove proprietà sono stati aggiunti per il [NSLocal](https://developer.apple.com/reference/foundation/nslocale) classe di acquisire informazioni locali e i formati di visualizzazione disponibile.

## <a name="gamekit-enhancements"></a>Miglioramenti di GameKit

I miglioramenti seguenti sono stato apportati a framework GameKit in iOS 10:

- Il **Game Center App** è deprecato ed è stato rimosso da iOS. Se l'app Usa GameKit, si _necessario_ presentare la propria interfaccia per visualizzare le funzionalità GameKit come tabelloni punteggi e così via. 
- È stato implementato un nuovo tipo di account iCloud-solo per i [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) classe.
- Il nuovo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) classe fornisce una soluzione generalizzata per la gestione di archiviazione persistente dei dati in Game Center. `GKGameSession` gestisce un elenco di giocatori e l'app è responsabile dell'implementazione come e quando Data partecipante è archiviato, recuperato o scambiato tra i lettori. In molti casi è possibile sostituire le sessioni di giochi corrispondenze esistente basata su turni, corrispondenze in tempo reale o gioco permanente Salva i metodi.

## <a name="gameplaykit-enhancements"></a>Miglioramenti di GameplayKit

I miglioramenti seguenti sono stato apportati a framework GameplayKit in iOS 10:

- Usare le nuove [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) classe per fornire percorsi realistiche ad alte prestazioni.
- Generazione di rumore procedurale è stato aggiunto e può essere usata per migliorare il realismo nelle trame realistiche, aggiungere più realistiche per i movimenti della fotocamera e generare scenari di gioco avanzati.
- Usare il partizionamento spaziali per partizionare i dati del mondo di giochi per la ricerca efficiente.
- Un nuovo strategist Monte Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) è stato aggiunto per il calcolo completo spostamento possibili.
- È stato aggiunto supporto 3D per l'agente esistente e i comportamenti di percorso di ricerca usando le nuove [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) classi.
- Il nuovo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) marca classi combinazione GameplayKit e SpriteKit più facile che mai.
- È stata aggiunta una nuova API di albero delle decisioni ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) per migliorare la compilazione di gioco per intelligenza artificiale.

## <a name="healthkit-enhancements"></a>Miglioramenti di HealthKit

I miglioramenti seguenti sono stato apportati al framework di HealthKit in iOS 10:

- Sono state aggiunte nuove chiavi di metadati per i tipi di meteo (come `HKWeatherConditionClear` e `HKWeatherConditionCloudy`) e i tipi per gli allenamenti (ad esempio `HKWorkoutActivityTypeFlexibility` e `HKWorkoutActivityTypeWheelchairRunPace`) sono state aggiunte.
- Il nuovo `HKCDADocument` classe è stata aggiunta per rappresentare un'architettura di documento clinico (CDA) formattati documento.
- Usare le nuove [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) classe per specificare le `ActivityType` e `LocationType` di accinge.
- Il nuovo [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) e il `WheelchairUse` metodo per il [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) classe sono state aggiunte per l'utilizzo con sedia correlati i dati di integrità.

## <a name="homekit-enhancements"></a>Miglioramenti di HomeKit

I miglioramenti seguenti sono stato apportati per il framework HomeKit in iOS 10:

- Sono stati aggiunti nuovi servizi e le caratteristiche.
- Un iPad può essere configurato per agire come un HomeKit Hub per fornire l'accesso remoto degli accessori, eseguire i trigger di automazione e abilitare condiviso le autorizzazioni utente.
- È stato aggiunto il supporto per accessories fotocamere e di campanella.
- Viene fornite più contesto e le configurazioni per accessories.

Vedere la [Introduzione a HomeKit](~/ios/platform/homekit.md) per altre informazioni.

## <a name="metal-enhancements"></a>Miglioramenti bare metalli

I miglioramenti seguenti sono stato apportati a framework bare Metal in iOS 10:

- App e giochi 3D ora è possono usare _dello schema a mosaico_ eseguire in modo efficiente il rendering di scene complesse e geometry tramite GPU.
- Fornire un controllo accurato di allocazione delle risorse per ottimizzare le prestazioni di Metal basati su App con risorse heap e Memoryless destinazioni di rendering.
- Utilizzare la specializzazione di funzione per creare una raccolta altamente ottimizzato di materiale e funzioni di combinazione di luce per una scena.

Per altre informazioni, vedere di Apple [Guida per programmatori Metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

## <a name="modelio-enhancements"></a>Miglioramenti di ModelIO

I miglioramenti seguenti sono stato apportati a framework ModelIO in iOS 10:

 - È ora supportato il formato di file USD.
 - Accesso automatico a distanza campo è stato aggiunto il supporto per la [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) classe.
 - Usare il nuovo `MDLLightProbeIrradianceDataSource` classe per assistere nella selezione host per il Probe di luce.
 - Usare il nuovo `MDLMaterialPropertyGraph` classe per agevolare il supporto runtime modifiche ai modelli.

## <a name="photos-enhancements"></a>Miglioramenti della foto

I miglioramenti seguenti sono stato apportati al framework di foto in iOS 10:

- Usare la [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) e [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) classi possa sfruttare i vantaggi della nuova funzionalità di immagine di Core del processore per eseguire modifiche.
- Modifica foto in tempo reale è ora disponibile per le app che supportano il framework di foto e per le estensioni di modifica foto (per l'uso all'interno di foto e fotocamera App).
- Usare le nuove [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) classe per applicare le modifiche per il video sia ancora il contenuto della foto in tempo reale.

## <a name="replaykit-enhancements"></a>Miglioramenti di ReplayKit

I miglioramenti seguenti sono stato apportati a framework ReplayKit in iOS 10:

- Usare la [RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder), [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller) e [RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller) registrate di classi per supportare la trasmissione di contenuti multimediali grazie a 3rd party siti.
- Le estensioni dell'interfaccia utente trasmissione e trasmissione caricare devono supportare ReplayKit 3rd party broadcast services nell'app.

## <a name="scenekit-enhancements"></a>Miglioramenti di SceneKit

I miglioramenti seguenti sono stato apportati al framework di SceneKit in iOS 10:

- Il [SCNCamera](https://developer.xamarin.com/api/type/SceneKit.SCNCamera/) classe può offrire maggiore realismo usando funzionalità HDR e gli effetti. Usare l'esposizione adattivo per creare effetti automatico o usare vignettatura, la smarginatura colore e la classificazione di colore per aggiungere effetti fillmatic al gioco.
- SceneKit include ora un nuovo sistema fisicamente basato su Rendering PBR () per ottenere risultati più realistici con più semplice la creazione di asset.
- Usare le nuove [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) ombreggiatura del modello di prodotto una vasta gamma di effetti di ombreggiatura realistica questa tecnologia richiede solo tre proprietà fondamentali (`Diffuse`, `Metalness` e `Roughness`).
- Poiché PBR ombreggiatura funziona meglio con illuminazione basata su ambiente, usare il `LightingEnvironment` proprietà per l'assegnazione basata su immagini di illuminazione per un'intera scena.
- Usare il `IESProfileURL` proprietà da importare impianti reali che definiscono l'illuminazione basato sui valori reali, ad esempio intensità (in lumen) e la temperatura di colore (in gradi Kelvin).
- Le funzionalità di fotocamera PBR sia HDR offrono risultati migliori rispetto a tecniche tradizionali per il rendering e, di conseguenza, SceneKit esegue ora tutti i calcoli di colore in uno spazio dei colori lineare (usando la gamma di colori P3 su schermi di dispositivi a livello di colore).
- SceneKit ora tutti i colori corrisponde al colore, leggere le informazioni sul profilo del colore.
- SceneKit interpreta i valori di componente di colore in uno spazio dei colori RGB lineare per tutti i tipi di shader.
- Entrambi lineare per il rendering dello spazio dei colori e a livello di colore può essere disabilitata specificando il `SCNDisableLinearSpaceRendering` e `SCNDisableWideGamut` le chiavi dell'app `Info.plist`.
- Compilare primati poligono arbitrario (caricato da file o a livello di codice generati) per specificare la geometria con il nuovo [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) classe.
- Poiché SceneKit legge e modificare informazioni sul profilo di colore nelle immagini di trama, utilizzare cataloghi Asset per tutte le immagini per assicurarsi che queste informazioni sono disponibili.

## <a name="spritekit-enhancements"></a>Miglioramenti di SpriteKit

I miglioramenti seguenti sono stato apportati a framework SpriteKit in iOS 10:

- Gli shader personalizzati possono fornire attributi (`SKAttribute`) che può essere configurata separatamente per ogni nodo che utilizza lo shader fornendo un valore di attributo (`SKAttributeValue`).
- Tilemaps supportano ora le forme di riquadro quadrato, esagonale e isometrica per 2D, 2,5 D e lo scorrimento laterale giochi usando il `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` e `SKTileSet` classi.
- Usare le nuove `SKWarpGeometry` classe per estendere o distorcere [SKSpriteNode](https://developer.xamarin.com/api/type/SpriteKit.SKSpriteNode/) oppure [SKEffectNode](https://developer.xamarin.com/api/type/SpriteKit.SKEffectNode/) per il rendering. Il nuovo [SKAction](https://developer.xamarin.com/api/type/SpriteKit.SKAction/) classe può essere utilizzata per aggiungere un'animazione transizioni tra gli effetti di warp.
- Il [SKView](https://developer.xamarin.com/api/type/SpriteKit.SKView/) classe fornisce numerosi nuovi metodi per garantire il controllo con granularità fine su come e quando viene eseguito il rendering di una scena.

## <a name="scrollview-enhancements"></a>Miglioramenti di ScrollView

I miglioramenti seguenti sono stato apportati al controllo ScrollView in iOS 10.3:

- `UIScrollView` ora includono il `IndexDisplayMode` proprietà per controllare la modalità di visualizzazione dell'indice mentre viene effettuato uno scorrimento all'utente come un `UIScrollViewIndexDisplayMode` di:
    - `Automatic` -La visualizzazione dell'indice è controllata dal sistema operativo.
    - `AlwaysHidden` -La visualizzazione dell'indice è sempre nascosto.

Vedere le [iOSTenThree esempio](https://developer.xamarin.com/samples/monotouch/iOS10/iOSTenThree) per l'utilizzo.

## <a name="uikit-enhancements"></a>Miglioramenti di UIKit

I miglioramenti seguenti sono stato apportati a framework UIKit in iOS 10:

- Il nuovo [UIPasteboard](https://developer.xamarin.com/api/type/UIKit.UIPasteboard/) API sono disponibili nuove opzioni (ad esempio, le limitazioni di durata) e verrà automaticamente dichiarare tipi di contenuto compatibili comuni per tipi di classe.
- Nuovo supporto per l'animazione completamente interattive, basato su oggetti, interrompibili è stato aggiunto e può essere collegato ai movimenti. Del pleas vedere Apple [UIViewAnimating Protocol Reference](https://developer.apple.com/reference/uikit/uiviewanimating), [riferimento alla classe UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), [UITimingCurveProvider Protocol Reference](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [Riferimento alla classe UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) e [riferimento alla classe UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) per altre informazioni.
- Il nuovo `UIPreviewInteraction` e `UIPreviewInteractionDelegate` consentire all'app per gli sviluppatori di fornire un'interfaccia personalizzata per le operazioni peek e pop.
- Il nuovo `UIAccessibilityCustomRotor` classe consente all'app fornire funzionalità personalizzate e specifici del contesto di Assistive Technology, ad esempio VoiceOver.
- Usare la `UIAccessibilityIsAssistiveTouchRunning` e `UIAccessibilityAssistiveTouchStatusDidChangeNotification` simboli per determinare se AssistiveTouch è abilitato.
- Usare la `UIAccessibilityHearingDevicePairedEar` e `UIAccessibilityHearingDevicePairedEarDidChangeNotification` simboli per ottenere lo stato di qualsiasi abbinate accessori MFi problemi visivi.
- Per supportare tipi dinamici in etichette, usano i nuovi campi di testo e caselle di testo `PreferredFontForTextStyle` metodo di `UIFont` classe.
- Per decidere se un elemento necessario aggiornare il tipo di carattere quando il dispositivo `UIContentSizeCategory` le modifiche, usare il `AdjustsFontForContentSizeCategory` proprietà del `UIContentSizeCategoryAdjusting` delegare.
- Il `OpenURL` metodo di `UIApplication` classe viene chiamato in modo asincrono e supporta ora un gestore di completamento che viene chiamato dopo che è stata completata l'azione di apertura.
- Avviare la condivisione CloudKit e modificarne le proprietà usando le nuove `UICloudSharingController` e `UICloudSharingControllerDelegate` classi.
- Sfruttare i vantaggi delle celle prelette per migliorare l'esperienza di scorrimento `UICollectionViews` con il nuovo `UICollectionViewDataSourcePrefetching` delegare.
- Lo sviluppatore è ora possibile controllare l'aspetto della notifica per l'elemento della barra schede (ad esempio il colore di sfondo e testo).
- Il controllo di aggiornamento è ora supportato in tutte le visualizzazione a scorrimento e sottoclassi visualizzazione scorrimento (ad esempio `UICollectionView`).

## <a name="webkit-enhancements"></a>Miglioramenti di WebKit

I miglioramenti seguenti sono stato apportati al framework di WebKit in iOS 10:

- Visualizzazione e il pop supporto è stato aggiunto per il `WKWebView` classe. Usare il `ShouldPreviewElement` metodo per determinare se una visualizzazione web specificato deve visualizzare un'anteprima.


## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Novità in iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
