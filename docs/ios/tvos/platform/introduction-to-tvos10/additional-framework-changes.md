---
title: Modifiche di Framework aggiuntivi tvOS 10
description: Questo documento descrive piccole modifiche e miglioramenti apportati al Framework esistenti in iOS 10. Vengono illustrati gli aggiornamenti per AVFoundation AVKit, Core Data, grafica di base, Foundation, GameKit, GameplayKit e altro ancora.
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 4ca9856251d35a741d496b6e8b45b07e851e0e89
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60932543"
---
# <a name="additional-tvos-10-frameworks-changes"></a>Modifiche di Framework aggiuntivi tvOS 10

Oltre alle modifiche principali per tvOS, Apple ha apportato modifiche e miglioramenti per diversi framework esistente in tvOS 10.

<a name="AV-Foundation-Framework" />

## <a name="avfoundation-framework-additions"></a>Framework AVFoundation aggiunte

Il framework AVFoundation include i miglioramenti seguenti:

 - In tvOS 10, l'app non è più implementa diverse [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) comportamenti in base al tipo di contenuto. È sufficiente impostare il `Rate` proprietà e AVFoundation determineranno quando sufficiente contenuto è disponibile per la riproduzione senza blocco.
 - Il nuovo `AVPlayerLooper` classe rende più semplice eseguire il ciclo di una determinata parte del supporto durante la riproduzione.

<a name="AVKit-Framework-Enhancements" />

## <a name="avkit-framework-enhancements"></a>Miglioramenti apportati AVKit Framework

Il framework AVKit include i miglioramenti seguenti:

 - L'app ha ora controllare il comportamento di ignorare il [AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller) perciò un movimento verrà ignorato potrebbe passare all'elemento successivo nella playlist o avanzata all'interno dell'elemento corrente.

<a name="Core-Data-Enhancements" />

## <a name="core-data-enhancements"></a>Miglioramenti di dati principali

tvOS 10 include i miglioramenti seguenti per il framework di Core Data:

 - Radice [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) oggetti supporta simultaneo di eventi di errore e il recupero senza serializzazione.
 - Il [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) classe gestisce un pool di archivi dati di SQLite.
 - Il [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) oggetti con gli archivi dati di SQLite nel supporto della modalità di registrazione log write-AHEAD di nuova generazione di query in cui i contesti di oggetto gestito (MOC) possono essere aggiunti alle versioni di database specifico per il recupero futuro delle funzionalità e generare un errore per le transazioni.
 - Utilizzo di alto livello `NSPersistenceContainer` per fare riferimento al `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e altre risorse di configurazione di Core Data.
 - Sono stati aggiunti alcuni nuovi metodi pratici per `NSManagedObject` rendendo più semplice eseguire operazioni di recupero e creare sottoclassi.

Per altre informazioni, vedere di Apple [riferimento a Framework di Core Data](https://developer.apple.com/reference/coredata).

<a name="Core-Graphics-Enhancements" />

## <a name="core-graphics-enhancements"></a>Miglioramenti della grafica di base

tvOS 10 include i miglioramenti seguenti per il framework di grafica di base:

 - Il nuovo [CGColorConverterRef](https://developer.apple.com/reference/coregraphics/cgcolorconverterref) classe può essere utilizzata per eseguire una serie di conversioni di colore.

<a name="Core-Image-Enhancements" />

## <a name="core-image-enhancements"></a>Miglioramenti all'immagine di base

tvOS 10 rende i miglioramenti seguenti per il framework di immagine di base:

 - Il `ImageWithExtent` metodo per il [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) classe può essere utilizzata per inserire istruzioni di elaborazione personalizzata nell'operazione di filtro. Immagine di base visualizzato o richiamare il callback specificato da un filtro durante l'elaborazione di un'immagine per l'output.
 - L'app può ora elaborare immagini in uno spazio dei colori di fuori di spazio dei colori del contesto l'immagine di base funzionante mediante la conversione da e verso lo spazio colore prima e dopo l'elaborazione.
 - Numerosi miglioramenti delle prestazioni per il rendering sono stato apportati a `UIImage` rendering (se supportata da archivi di immagine di immagine di base) in `UIImageView` oggetti. 
 - `UIImage` scala a livello di oggetti con tag verrà eseguito il rendering come a livello di colore in `UIImageView` oggetti nei dispositivi iOS che supportano l'ampia gamma di colori.
 - Codice kernel immagine di base può ora richiedere formati di output di pixel specifico.

Inoltre, sono stati aggiunti i nuovi filtri di immagine di base seguenti:

 - `CINinePartTiled`
 - `CINinePartStretched`
 - `CIHueSaturationValueGradient`
 - `CIEdgePreserveUpsampleFilter`
 - `CIClamp`

<a name="Foundation-Enhancements" />

## <a name="foundation-enhancements"></a>Miglioramenti di Foundation

I miglioramenti seguenti sono stato apportati a framework Foundation per tvOS 10:

 - Usare le nuove [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe per rendere i calcoli degli intervalli di data e ora, ad esempio le durate, per il confronto di intervalli e i test per le intersezioni di intervallo.
 - Diverse nuove proprietà sono stati aggiunti per il [NSLocal](https://developer.apple.com/reference/foundation/nslocale) classe di acquisire informazioni locali e i formati di visualizzazione disponibile.
 - Usare le nuove [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) classe convertire tra diverse unità di misura (UDM) o eseguire calcoli sui valori di unità diversa.
 - Usare le nuove [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) classe per formattare le misurazioni localizzate per la visualizzazione all'utente finale.
 - Usare le nuove [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) classi per la rappresentazione di unità specifica.

<a name="GameKit-Enhancements" />

## <a name="gamekit-enhancements"></a>Miglioramenti di GameKit

I miglioramenti seguenti sono stato apportati a framework GameKit tvOS 10:

 - È stato implementato un nuovo tipo di account iCloud-solo per i [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) classe.
 - Il nuovo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) classe fornisce una soluzione generalizzata per la gestione di archiviazione persistente dei dati in Game Center. `GKGameSession` gestisce un elenco di giocatori e l'app è responsabile della forma che implementa i casi in cui è archiviato data partecipante, recuperato o scambiati tra i lettori. In molti casi è possibile sostituire le sessioni di giochi corrispondenze esistente basata su turni, corrispondenze in tempo reale o gioco permanente Salva i metodi.

<a name="GameplayKit-Enhancements" />

## <a name="gameplaykit-enhancements"></a>Miglioramenti di GameplayKit

I miglioramenti seguenti sono stato apportati a framework GameplayKit tvOS 10:

 - Generazione di rumore procedurale è stato aggiunto e può essere usata per migliorare il realismo nelle trame realistiche, aggiungere più realistiche per i movimenti della fotocamera e generare scenari di gioco avanzati.
 - Usare il partizionamento spaziali per partizionare i dati del mondo di giochi per la ricerca efficiente.
 - Un nuovo strategist Monte Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) è stato aggiunto per il calcolo completo spostamento possibili.
 - È stata aggiunta una nuova API di albero delle decisioni ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) per migliorare la compilazione di gioco per intelligenza artificiale.
 - È stato aggiunto supporto 3D per l'agente esistente e i comportamenti di percorso di ricerca usando le nuove [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) classi.
 - Usare le nuove [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) classe per fornire percorsi realistiche ad alte prestazioni.
 - Il nuovo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) marca classi combinazione GameplayKit e SpriteKit più facile che mai.

<a name="Metal-Enhancements" />

## <a name="metal-enhancements"></a>Miglioramenti bare metalli

I miglioramenti seguenti sono stato apportati a framework bare Metal in tvOS 10:

 - App e giochi 3D ora è possono usare _dello schema a mosaico_ eseguire in modo efficiente il rendering di scene complesse e geometry tramite GPU.
 - Utilizzare la specializzazione di funzione per creare una raccolta altamente ottimizzato di materiale e funzioni di combinazione di luce per una scena.
 - Fornire un controllo accurato di allocazione delle risorse per ottimizzare le prestazioni di Metal basati su App con risorse heap e Memoryless destinazioni di rendering.

Per altre informazioni, vedere di Apple [Guida per programmatori Metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

<a name="Metal-Performance-Shaders-Enhancements" />

## <a name="metal-performance-shaders-enhancements"></a>Miglioramenti di shader di Metal delle prestazioni

I miglioramenti seguenti sono stato apportati al framework di shader di Metal delle prestazioni in tvOS 10:

 - Molti nuovi kernel sono state aggiunte per il framework di shader di Metal sulle prestazioni per consentire all'app sfruttare i vantaggi dei calcoli altamente ottimizzata e dati in parallelo, ad esempio le conversioni di spazio di colore e le operazioni di rete neurale.

<a name="ModelIO-Enhancements" />

## <a name="modelio-enhancements"></a>Miglioramenti di ModelIO

I miglioramenti seguenti sono stato apportati a framework ModelIO tvOS 10:

 - È ora supportato il formato di file USD.
 - Usare il nuovo `MDLMaterialPropertyGraph` classe per agevolare il supporto runtime modifiche ai modelli.
 - Accesso automatico a distanza campo è stato aggiunto il supporto per la [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) classe.
 - Usare il nuovo `MDLLightProbeIrradianceDataSource` classe per assistere nella selezione host per il Probe di luce.

<a name="SceneKit-Enhancements" />

## <a name="scenekit-enhancements"></a>Miglioramenti di SceneKit

I miglioramenti seguenti sono stato apportati a framework SceneKit tvOS 10:

 - SceneKit include ora un nuovo sistema fisicamente basato su Rendering PBR () per ottenere risultati più realistici con più semplice la creazione di asset.
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

<a name="SpriteKit-Enhancements" />

## <a name="spritekit-enhancements"></a>Miglioramenti di SpriteKit

I miglioramenti seguenti sono stato apportati a framework SpriteKit tvOS 10:

 - Tilemaps supportano ora le forme di riquadro quadrato, esagonale e isometrica per 2D, 2,5 D e lo scorrimento laterale giochi usando il `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` e `SKTileSet` classi.
 - Usare le nuove `SKWarpGeometry` classe per estendere o distorcere [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) oppure [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) per il rendering. Il nuovo [SKAction](https://developer.apple.com/reference/spritekit/skaction) classe può essere utilizzata per aggiungere un'animazione transizioni tra gli effetti di warp.
 - Gli shader personalizzati possono fornire attributi (`SKAttribute`) che può essere configurata separatamente per ogni nodo che utilizza lo shader fornendo un valore di attributo (`SKAttributeValue`).
 - Il [SKView](https://developer.apple.com/reference/spritekit/skview) classe fornisce numerosi nuovi metodi per garantire il controllo con granularità fine su come e quando viene eseguito il rendering di una scena.

<a name="UIKit-Enhancements" />

## <a name="uikit-enhancements"></a>Miglioramenti di UIKit

I miglioramenti seguenti sono stato apportati a framework UIKit tvOS 10:

 - Lo stato attivo API è stato migliorato per il supporto dello stato attivo dell'elemento della visualizzazione non oltre a `UIViews`. Gli elementi che supportano la messa a fuoco _devono_ implementano il `IUIFocusItem` interfaccia.
 - Il nuovo `UIGraphicsRender` classe fornisce un metodo di creazione di bitmap o file PDF da UIKit rendering o Core Graphics orientate a oggetti e sostituisce deprecate `UIGraphicsBeginImageContext` (metodo).
 - Il `UIUserInterfaceStyle` classe è stata aggiunta per determinare quali tema dell'interfaccia utente (chiaro o scuro) è attualmente attivo.
 - È stato aggiunto il nuovo supporto di animazione completamente interattive, basato su oggetti, interrompibili e van essere collegato a movimenti. Del pleas vedere Apple [UIViewAnimating Protocol Reference](https://developer.apple.com/reference/uikit/uiviewanimating), [riferimento alla classe UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), [UITimingCurveProvider Protocol Reference](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [Riferimento alla classe UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) e [riferimento alla classe UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) per altre informazioni.
 - Il nuovo `UIPreviewInteraction` e `UIPreviewInteractionDelegate` consentire all'app fornire un'interfaccia personalizzata per le operazioni peek e pop.
 - Il nuovo `UIAccessibilityCustomRotor` classe consente all'app fornire funzionalità personalizzate e specifici del contesto di Assistive Technology, ad esempio VoiceOver.
 - Usare la `UIAccessibilityIsAssistiveTouchRunning` e `UIAccessibilityAssistiveTouchStatusDidChangeNotification` simboli per determinare se AssistiveTouch è abilitato.
 - Usare la `UIAccessibilityHearingDevicePairedEar` e `UIAccessibilityHearingDevicePairedEarDidChangeNotification` simboli per ottenere lo stato di qualsiasi abbinate accessori MFi problemi visivi.
 - Il nuovo [UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) API sono disponibili nuove opzioni (ad esempio, le limitazioni di durata) e verrà automaticamente dichiarare tipi di contenuto compatibili comuni per tipi di classe.
 - Per supportare tipi dinamici in etichette, usano i nuovi campi di testo e caselle di testo `PreferredFontForTextStyle` metodo di `UIFont` classe.
 - Per decidere se un elemento necessario aggiornarla del carattere quando i dispositivi `UIContentSizeCategory` le modifiche, usare il `AdjustsFontForContentSizeCategory` proprietà del `UIContentSizeCategoryAdjusting` delegare.
 - L'app è ora possibile controllare l'aspetto della notifica sulla barra degli elementi della scheda, ad esempio colore di testo e sfondo.
 - Il controllo di aggiornamento ora è supportato in tutte le visualizzazione a scorrimento e una visualizzazione a scorrimento sottoclassi (ad esempio `UICollectionView`).
 - Il `OpenURL` metodo del `UIApplication` classe è detta supporta ora un gestore di completamento che viene chiamato dopo l'apertura è stata completata in modo asincrono.
 - Avviare la condivisione CloudKit e modificarne le proprietà usando le nuove `UICloudSharingController` e `UICloudSharingControllerDelegate` classi.
 - Sfruttare i vantaggi delle celle prelette per migliorare l'esperienza di scorrimento `UICollectionViews` con il nuovo `UICollectionViewDataSourcePrefetching` delegare.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Che cos'è una novità a tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
