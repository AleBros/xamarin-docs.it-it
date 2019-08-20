---
title: Modifiche aggiuntive ai Framework di iOS 10
description: Questo documento descrive le modifiche e i miglioramenti secondari apportati ai Framework esistenti in iOS 10 e illustra come usare questi aggiornamenti in Novell. iOS.
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: 85dad3bdd46401b1acf9a1cb5acc206508cd611f
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69620716"
---
# <a name="additional-ios-10-frameworks-changes"></a>Modifiche aggiuntive ai Framework di iOS 10

_Questo articolo illustra ulteriori modifiche o miglioramenti secondari ai Framework esistenti per iOS 10._

## <a name="av-foundation-framework-additions"></a>Aggiunte di AV Foundation Framework

Il Framework AVFoundation include i miglioramenti seguenti:

- In iOS 10 lo sviluppatore non deve più implementare comportamenti [AVPlayerItem](xref:AVFoundation.AVPlayerItem) diversi in base al tipo di contenuto. È sufficiente impostare `Rate` la proprietà e AVFoundation per determinare quando sarà disponibile contenuto sufficiente per la riproduzione senza bloccarsi.
- La nuova classe [AVCapturePhotoOutput](xref:AVFoundation.AVCaptureFileOutput) sostituisce la `AVCaptureStillImageOutput` classe deprecata e fornisce un metodo unificato per la gestione di tutti i flussi di lavoro di fotografia, fornendo un controllo sofisticato e il monitoraggio del processo di acquisizione e il supporto per nuovi funzionalità come le foto in tempo reale e il formato di acquisizione non elaborato.
- La nuova `AVPlayerLooper` classe rende più semplice il ciclo di un dato supporto durante la riproduzione.
- La `AVAssetDownloadURLSession` classe consente il download e la riproduzione successiva dei flussi HLS crittografati Fairplay.
- Per impostazione predefinita, la classe [AVCaptureSession](xref:AVFoundation.AVCaptureSession) supporta automaticamente l'acquisizione Wide-Color e Wide-Gamut quando l'hardware del dispositivo lo supporta. Per altri dettagli, vedere la Guida di riferimento per la [compatibilità dei dispositivi iOS](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599) di Apple.

## <a name="avkit-additions"></a>Aggiunte AVKit

Il Framework AVKit include ora la nuova `UpdatesNowPlayingInfoCenter` proprietà per indicare quando deve essere aggiornato il centro informazioni per la riproduzione.

## <a name="core-data-enhancements"></a>Miglioramenti dei dati principali

iOS 10 include i miglioramenti seguenti al Framework di dati principale:

- Gli oggetti [NSManagedObjectContext](xref:CoreData.NSManagedObjectContext) con gli archivi dati SQLite nella modalità Journal Wal supportano la nuova funzionalità di generazione delle query in cui i contesti degli oggetti gestiti (MOC) possono essere aggiunti a versioni specifiche del database per le transazioni di recupero e di errore future.
- Gli oggetti [NSManagedObjectContext](xref:CoreData.NSManagedObjectContext) radice supportano gli errori e il recupero simultanei senza serializzazione.
- La classe [NSPersistentStoreCoordinator](xref:CoreData.NSPersistentStoreCoordinator) gestisce un pool di archivi dati SQLite.
- Sono stati aggiunti diversi nuovi metodi pratici per `NSManagedObject` semplificare l'esecuzione di operazioni di recupero e creazione di sottoclassi.
- Utilizzo di alto livello `NSPersistenceContainer` per `NSPersistentStoreCoordinator`fare riferimento a, [NSManagedObjectModel](xref:CoreData.NSManagedObjectModel) e altre risorse di configurazione dei dati principali.

Per altre informazioni, vedere la Guida di [riferimento a Core Data Framework](https://developer.apple.com/reference/coredata)di Apple.

## <a name="core-image-enhancements"></a>Miglioramenti delle immagini principali

iOS 10 apporta i miglioramenti seguenti al framework principale dell'immagine:

- Lo sviluppatore ora può elaborare le immagini in uno spazio di colore al di fuori dello spazio dei colori di lavoro del contesto immagine principale convertendo in e fuori dallo spazio di colore prima e dopo l'elaborazione.
- Per i dispositivi iOS che usano le CPU A8 o A9, il formato di immagine non ELABORAta è ora supportato. L'immagine di base offre ora il supporto per la decodifica di immagini non ELABORAte dalla videocamera iSight incorporata o da una fotocamera di terze parti. Usare i `FilterWithImageData` metodi `FilterWithImageURL` o della classe [CIFilter](xref:CoreImage.CIFilter) per elaborare le immagini non elaborate.
- Sono stati apportati diversi miglioramenti alle prestazioni `UIImage` di rendering per il rendering (in caso di archiviazione di immagini `UIImageView` di base immagine) negli oggetti. 
- `UIImage`gli oggetti con tag Wide-Gamut eseguiranno il rendering come colore `UIImageView` Wide-gamut in oggetti su dispositivi iOS che supportano il colore Wide.
- Il codice kernel dell'immagine principale può ora richiedere formati di output pixel specifici.
- Il `ImageWithExtent` metodo della classe [CIFilter](xref:CoreImage.CIFilter) può essere usato per inserire l'elaborazione personalizzata nell'operazione di filtro. L'immagine principale richiama il callback specificato tra i filtri quando si elabora un'immagine per l'output o la visualizzazione.

Sono stati aggiunti anche i nuovi filtri per le immagini principali seguenti:

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>Aggiunte di movimento Core

Una novità di iOS 10, il Framework di movimento principale include eventi del pedometro che consentono a un'app di ricevere notifiche rapide e in tempo reale dell'utente che sospende e riprende il rilevamento durante l'esecuzione. Usare [CMPedometer](xref:CoreMotion.CMPedometer) per eseguire la registrazione per gli eventi del pedometro in primo piano o in background.

## <a name="foundation-enhancements"></a>Miglioramenti di Foundation

Sono stati apportati i miglioramenti seguenti al Framework di base per iOS 10:

- Utilizzare la nuova classe [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) per formattare le misurazioni localizzate per la visualizzazione all'utente finale.
- Usare la nuova classe [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) per creare calcoli di intervallo di data e ora, ad esempio le durate, per confrontare gli intervalli e i test per le intersezioni degli intervalli.
- Usare la nuova classe [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) per eseguire la conversione tra unità di misura diverse (uom) o eseguire calcoli su valori in UOMs diversi.

- Utilizzare le nuove classi [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) per la rappresentazione di UOMs specifici.
- Sono state aggiunte diverse nuove proprietà alla classe [NSLocal](https://developer.apple.com/reference/foundation/nslocale) per acquisire informazioni locali e i formati di visualizzazione disponibili.

## <a name="gamekit-enhancements"></a>Miglioramenti di GameKit

Sono stati apportati i miglioramenti seguenti al Framework GameKit in iOS 10:

- L' **App Game Center** è stata deprecata e rimossa da iOS. Se l'app usa GameKit, _deve_ presentare una propria interfaccia per visualizzare le funzionalità di gamekit, ad esempio le classifiche e così via. 
- Un nuovo tipo di account solo iCloud è stato implementato dalla classe [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) .
- La nuova classe [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) fornisce una soluzione generalizzata per la gestione dell'archiviazione dei dati persistente nei Game Center. `GKGameSession`gestisce un elenco di giocatori e l'app è responsabile dell'implementazione di come e quando la data del partecipante viene archiviata, recuperata o scambiata tra i giocatori. In molti casi le sessioni di gioco possono sostituire le corrispondenze basate su turni esistenti, le corrispondenze in tempo reale o i metodi di salvataggio del gioco permanenti

## <a name="gameplaykit-enhancements"></a>Miglioramenti di GameplayKit

Sono stati apportati i miglioramenti seguenti al Framework GameplayKit in iOS 10:

- Usare la nuova classe [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) per fornire percorsi a prestazioni elevate e naturali.
- È stata aggiunta la generazione del rumore procedurale che può essere usata per migliorare il realismo nelle trame all'aspetto naturale, aggiungere il realismo ai movimenti della fotocamera e contribuire a generare mondi di gioco avanzati.
- Usare il partizionamento spaziale per partizionare i dati del mondo del gioco per una ricerca efficiente.
- Un nuovo Strategy Monte Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) è stato aggiunto per un calcolo completo possibile dello spostamento.
- il supporto 3D è stato aggiunto ai comportamenti esistenti di individuazione degli agenti e dei percorsi usando le nuove classi [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) .
- Le nuove classi [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) rendono la combinazione di GameplayKit e SpriteKit più semplice che mai.
- È stata aggiunta una nuova API dell'albero delle decisioni ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) per migliorare l'intelligenza artificiale per la creazione di giochi.

## <a name="healthkit-enhancements"></a>Miglioramenti di HealthKit

Sono stati apportati i miglioramenti seguenti al Framework HealthKit in iOS 10:

- Sono state aggiunte nuove chiavi di metadati per i tipi meteorologici `HKWeatherConditionClear` ( `HKWeatherConditionCloudy`ad esempio e) e sono stati `HKWorkoutActivityTypeFlexibility` aggiunti `HKWorkoutActivityTypeWheelchairRunPace`tipi di allenamento (ad esempio e).
- La nuova `HKCDADocument` classe è stata aggiunta per rappresentare un documento formattato per l'architettura di documenti clinici (CDA).
- Usare la nuova classe [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) per specificare `ActivityType` e `LocationType` di un allenamento.
- Sono stati aggiunti i nuovi [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) e il metodo `WheelchairUse` della classe [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) per lavorare con i dati di integrità correlati a carrozzine.

## <a name="homekit-enhancements"></a>Miglioramenti di HomeKit

Sono stati apportati i miglioramenti seguenti al Framework HomeKit in iOS 10:

- Sono stati aggiunti nuovi servizi e caratteristiche.
- Un iPad può essere configurato in modo da fungere da Hub HomeKit per fornire accesso remoto agli accessori, eseguire trigger di automazione e abilitare le autorizzazioni utente condivise.
- È stato aggiunto il supporto per gli accessori della fotocamera e del campanello.
- Per gli accessori sono stati forniti più contesto e configurazioni.

Per ulteriori informazioni, vedere la documentazione [introduttiva di HomeKit](~/ios/platform/homekit.md) .

## <a name="metal-enhancements"></a>Miglioramenti Metal

Sono stati apportati i miglioramenti seguenti al Framework di metal in iOS 10:

- le app e i giochi 3D possono ora usare il _mosaico_ per eseguire in modo efficiente il rendering di scene e geometria complesse tramite la GPU.
- Fornire un controllo con granularità fine dell'allocazione delle risorse per ottimizzare le prestazioni delle app basate su Metal usando gli heap delle risorse e le destinazioni di rendering prive di memoria.
- Usare la specializzazione della funzione per creare una raccolta altamente ottimizzata di funzioni di combinazione materiale e luce per una scena.

Per altre informazioni, vedere la guida alla [programmazione](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)per i metal di Apple.

## <a name="modelio-enhancements"></a>Miglioramenti di ModelIO

Sono stati apportati i miglioramenti seguenti al Framework ModelIO in iOS 10:

- Il formato di file USD è ora supportato.
- Il supporto per il campo a distanza firmato è stato aggiunto alla classe [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) .
- Usare la nuova `MDLLightProbeIrradianceDataSource` classe per facilitare la selezione host di probe.
- Utilizzare la nuova `MDLMaterialPropertyGraph` classe per supportare facilmente le modifiche di runtime ai modelli.

## <a name="photos-enhancements"></a>Miglioramenti delle foto

Per il Framework Photos in iOS 10 sono stati apportati i miglioramenti seguenti:

- Usare le classi [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) e [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) per sfruttare i vantaggi della nuova funzionalità di processore di immagini principali per eseguire le modifiche.
- La funzionalità di modifica della foto in tempo reale è ora disponibile per le app che supportano il Framework foto e per le estensioni per la modifica di foto (per l'uso all'interno delle app Foto e fotocamera).
- Usare la nuova classe [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) per applicare le modifiche sia al video che ancora al contenuto delle foto in tempo reale.

## <a name="replaykit-enhancements"></a>Miglioramenti di ReplayKit

Sono stati apportati i miglioramenti seguenti al Framework ReplayKit in iOS 10:

- Usare le classi [RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder), [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller) e [RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller) per supportare la trasmissione di supporti registrati tramite siti di terze parti.
- L'interfaccia utente broadcast e le estensioni Broadcast Upload sono necessarie per supportare i servizi broadcast di terze parti ReplayKit nell'app.

## <a name="scenekit-enhancements"></a>Miglioramenti di SceneKit

Sono stati apportati i miglioramenti seguenti al Framework SceneKit in iOS 10:

- La classe [SCNCamera](xref:SceneKit.SCNCamera) può offrire maggiore realismo usando le funzionalità e gli effetti di HDR. Usare l'esposizione adattiva per creare effetti automatici o usare vignette, frangia dei colori e classificazione dei colori per aggiungere effetti fillmatic al gioco.
- SceneKit include ora un nuovo sistema di rendering basato su fisico (PBR) per risultati più realistici con la creazione di asset più semplice.
- Utilizzare il nuovo modello di ombreggiatura [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) per produrre un'ampia gamma di effetti ombreggiamenti realistici, richiedendo solo tre proprietà`Diffuse`fondamentali `Metalness` ( `Roughness`, e).
- Poiché l'ombreggiatura del PBR funziona meglio con l'illuminazione basata sull'ambiente `LightingEnvironment` , usare la proprietà per assegnare l'illuminazione basata sull'immagine a un'intera scena.
- Usare la `IESProfileURL` proprietà per importare i dispositivi luminosi reali che definiscono l'illuminazione in base a valori reali, ad esempio l'intensità (in lumen) e la temperatura del colore (in gradi Kelvin).
- Entrambe le funzionalità della fotocamera PBR e HDR forniscono risultati migliori rispetto alle tecniche di rendering tradizionali e, di conseguenza, SceneKit esegue ora tutti i calcoli dei colori in uno spazio di colore lineare (usando la gamma di colori P3 sulle visualizzazioni dei dispositivi a colori Wide).
- SceneKit Now color corrisponde a tutti i colori leggendo le informazioni sul profilo colori.
- SceneKit interpreta i valori dei componenti dei colori in uno spazio di colore RGB lineare per tutti i tipi di shader.
- Sia il rendering dello spazio dei colori lineare che il colore Wide possono essere disabilitati `SCNDisableWideGamut` specificando le `SCNDisableLinearSpaceRendering` `Info.plist`chiavi e nell'app.
- Compilare i primitivi del poligono arbitrari (caricati da file o generati a livello di codice) per specificare la geometria con la nuova classe [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) .
- Poiché SceneKit legge e regola le informazioni sul profilo colori nelle immagini di trama, usare cataloghi asset per tutte le immagini per assicurarsi che queste informazioni vengano fornite.

## <a name="spritekit-enhancements"></a>Miglioramenti di SpriteKit

Sono stati apportati i miglioramenti seguenti al Framework SpriteKit in iOS 10:

- Gli shader personalizzati possono fornire attributi (`SKAttribute`) che possono essere configurati separatamente da ogni nodo che usa lo shader fornendo un valore di attributo (`SKAttributeValue`).
- Tilemaps supporta ora le forme di sezione quadrata, esagonale e isometrica per i giochi 2D, 2.5 d e a scorrimento `SKTileMapMode`laterale usando `SKTileGroupRule` le `SKTileSet` classi, `SKTileGroup`e.
- Usare la nuova `SKWarpGeometry` classe per estendere o distorcere il rendering di [SKSpriteNode](xref:SpriteKit.SKSpriteNode) o [SKEffectNode](xref:SpriteKit.SKEffectNode) . La nuova classe [SKAction](xref:SpriteKit.SKAction) può essere usata per animare le transizioni tra gli effetti di distorsione.
- La classe [SKView](xref:SpriteKit.SKView) fornisce diversi nuovi metodi per fornire un controllo dettagliato su quando e come viene eseguito il rendering di una scena.

## <a name="scrollview-enhancements"></a>Miglioramenti di ScrollView

Sono stati apportati i miglioramenti seguenti al controllo ScrollView in iOS 10,3:

- `UIScrollView`includere ora la `IndexDisplayMode` proprietà per controllare la modalità di visualizzazione dell'indice mentre l'utente scorre `UIScrollViewIndexDisplayMode` come:
  - `Automatic`-La visualizzazione dell'indice è controllata dal sistema operativo.
  - `AlwaysHidden`-La visualizzazione dell'indice è sempre nascosta.

Vedere l' [esempio iOSTenThree](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree) per l'utilizzo.

## <a name="uikit-enhancements"></a>Miglioramenti di UIKit

Sono stati apportati i miglioramenti seguenti al Framework UIKit in iOS 10:

- La nuova API [UIPasteboard](xref:UIKit.UIPasteboard) fornisce nuove opzioni, ad esempio limitazioni di durata, e dichiarerà automaticamente i tipi di contenuto compatibili per i tipi di classe comuni.
- È stato aggiunto un nuovo supporto per l'animazione interrompibili completamente interattivo e basato su oggetti, che può essere collegato a movimenti. Vedere le informazioni di [riferimento sul protocollo UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating), il riferimento alla [classe UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), il riferimento al [protocollo UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), il [riferimento alla classe UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) e [ Riferimento alla classe UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) per ulteriori informazioni.
- Il nuovo `UIPreviewInteraction` e `UIPreviewInteractionDelegate` consentire all'app per sviluppatori di fornire un'interfaccia personalizzata per le operazioni di visualizzazione e pop.
- La nuova `UIAccessibilityCustomRotor` classe consente all'app di fornire funzionalità personalizzate e specifiche del contesto alle tecnologie per l'accesso facilitato, ad esempio la voce.
- Usare i `UIAccessibilityIsAssistiveTouchRunning` simboli `UIAccessibilityAssistiveTouchStatusDidChangeNotification` e per determinare se le è abilitato.
- Usare i `UIAccessibilityHearingDevicePairedEar` simboli `UIAccessibilityHearingDevicePairedEarDidChangeNotification` e per ottenere lo stato di qualsiasi supporto acustico per le IFM associato.
- Per supportare il tipo dinamico nelle etichette, i campi `PreferredFontForTextStyle` `UIFont` di testo e le caselle di testo utilizzano il nuovo metodo della classe.
- Per decidere se un elemento deve aggiornare il tipo di carattere quando le `UIContentSizeCategory` modifiche apportate `AdjustsFontForContentSizeCategory` al dispositivo, `UIContentSizeCategoryAdjusting` utilizzare la proprietà del delegato.
- Il `OpenURL` metodo`UIApplication` della classe viene chiamato in modo asincrono e ora supporta un gestore di completamento chiamato dopo il completamento dell'azione di apertura.
- Avviare la condivisione di CloudKit e modificarne le proprietà usando `UICloudSharingController` le `UICloudSharingControllerDelegate` nuove classi e.
- Sfruttare le celle prelette per migliorare l'esperienza di scorrimento di `UICollectionViews` con il nuovo `UICollectionViewDataSourcePrefetching` delegato.
- Lo sviluppatore può ora controllare l'aspetto della notifica per gli elementi della barra delle schede, ad esempio il testo e il colore di sfondo.
- Il controllo Refresh è ora supportato in tutte le sottoclassi di visualizzazione a scorrimento e visualizzazione a scorrimento `UICollectionView`(ad esempio).

## <a name="webkit-enhancements"></a>Miglioramenti di WebKit

Per il Framework WebKit in iOS 10 sono stati apportati i miglioramenti seguenti:

- Alla classe è stato aggiunto il `WKWebView` supporto per PEEK e pop. Utilizzare il `ShouldPreviewElement` metodo per determinare se una determinata visualizzazione Web deve visualizzare un'anteprima.


## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Novità di iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
