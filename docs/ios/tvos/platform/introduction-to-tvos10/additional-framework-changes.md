---
title: Modifiche di Framework aggiuntive tvOS 10
description: Questo articolo vengono illustrate le modifiche aggiuntive, secondarie o miglioramenti alla Framework esistenti per tvOS 10.
ms.topic: article
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 004abfffd3a100b7a25a9647fe233fd676f61143
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="additional-tvos-10-frameworks-changes"></a>Modifiche di Framework aggiuntive tvOS 10

_Questo articolo vengono illustrate le modifiche aggiuntive, secondarie o miglioramenti alla Framework esistenti per tvOS 10._


Oltre alle modifiche essenziali per tvOS, Apple ha apportato modifiche e miglioramenti per diversi Framework esistenti tvOS 10.

<a name="AV-Foundation-Framework" />

## <a name="av-foundation-framework-additions"></a>Aggiunte di Framework AV Foundation

Il framework AVFoundation include i miglioramenti seguenti:

 - In tvOS 10, l'app non implementa diverse [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) comportamenti in base al tipo di contenuto. È sufficiente impostare la `Rate` proprietà e AVFoundation determinerà se sufficiente contenuto è disponibile per la riproduzione senza bloccare.
 - Il nuovo `AVPlayerLooper` classe rende più semplice eseguire una determinata parte del supporto di un ciclo durante la riproduzione.

<a name="AVKit-Framework-Enhancements" />

## <a name="avkit-framework-enhancements"></a>Miglioramenti apportati a Framework AVKit

Il framework AVKit include i miglioramenti seguenti:

 - L'applicazione dispone ora di controllare il comportamento di ignorare il [AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller) un movimento verrà ignorato potrebbe lo spostamento all'elemento successivo della playlist o anticipo all'interno dell'elemento corrente.

<a name="Core-Data-Enhancements" />

## <a name="core-data-enhancements"></a>Miglioramenti di dati di base

tvOS 10 include i miglioramenti seguenti per il framework di dati principali:

 - Radice [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) oggetti supporta simultaneo di generare un errore e di recupero senza serializzazione.
 - Il [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) classe gestisce un pool di archivi dati di SQLite.
 - Il [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) oggetti con archivi dati SQLite il supporto di modalità Journal WAL la generazione di query nuove funzionalità in contesti di oggetto gestito (MOC) possono essere aggiunti a versioni specifiche di database per il recupero futuro e generare un errore per le transazioni.
 - Utilizzo di alto livello `NSPersistenceContainer` al riferimento di `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e altre risorse di configurazione di dati di base.
 - Sono stati aggiunti numerosi nuovi metodi pratici `NSManagedObject` rendendo più semplice eseguire operazioni di recupero e di creare sottoclassi.

Per ulteriori informazioni, vedere Apple [Core dati Framework Reference](https://developer.apple.com/reference/coredata).

<a name="Core-Graphics-Enhancements" />

## <a name="core-graphics-enhancements"></a>Miglioramenti di grafica di base

tvOS 10 include i miglioramenti seguenti per il framework di grafica di base:

 - Il nuovo [CGColorConverterRef](https://developer.apple.com/reference/coregraphics/cgcolorconverterref) classe può essere utilizzata per eseguire una serie di conversioni di colore.

<a name="Core-Image-Enhancements" />

## <a name="core-image-enhancements"></a>Miglioramenti all'immagine di base

tvOS 10 rende i miglioramenti seguenti per il framework di immagine di base:

 - Il `ImageWithExtent` metodo il [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) classe può essere utilizzata per inserire un'elaborazione personalizzata l'operazione di filtro. Immagine di base visualizzato o richiamare il callback specificato tra filtri durante l'elaborazione di un'immagine per l'output.
 - L'app può ora elaborare immagini in uno spazio di colori di fuori di spazio colore di lavoro del contesto dell'immagine di base tramite la conversione da e verso lo spazio colore prima e dopo l'elaborazione.
 - Numerosi miglioramenti delle prestazioni di rendering apportati per `UIImage` il rendering (se supportato da archivi immagine immagine Core) in `UIImageView` oggetti. 
 - `UIImage` gli oggetti con tag wide-gamma verrà eseguito il rendering come livello di colore in `UIImageView` oggetti nei dispositivi iOS che supportano colori ampia.
 - Codice kernel immagine di base può richiedere formati di output del pixel specifico.

Inoltre, sono stati aggiunti i nuovi filtri immagine di base seguenti:

 - `CINinePartTiled`
 - `CINinePartStretched`
 - `CIHueSaturationValueGradient`
 - `CIEdgePreserveUpsampleFilter`
 - `CIClamp`

<a name="Foundation-Enhancements" />

## <a name="foundation-enhancements"></a>Miglioramenti di Foundation

I miglioramenti seguenti sono stati apportati a framework Foundation per tvOS 10:

 - Usare il nuovo [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe per eseguire calcoli di intervallo di data e ora, ad esempio le durate, per il confronto di intervalli e test per le intersezioni di intervallo.
 - Diverse nuove proprietà sono stati aggiunti i [NSLocal](https://developer.apple.com/reference/foundation/nslocale) classe per acquisire informazioni locali e i formati di visualizzazione disponibili.
 - Usare il nuovo [NSMeasuerment](https://developer.apple.com/reference/foundation/nsmeasurement) classe per convertire tra diverse unità di misura (UDM) o eseguire calcoli sui valori di unità diversa.
 - Usare il nuovo [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) classe per formattare le misurazioni localizzate per la visualizzazione per l'utente finale.
 - Usare il nuovo [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) classi per la rappresentazione di unità specifiche.

<a name="GameKit-Enhancements" />

## <a name="gamekit-enhancements"></a>Miglioramenti di GameKit

I miglioramenti seguenti sono stati apportati a framework GameKit tvOS 10:

 - È stato implementato un nuovo tipo di account iCloud solo per il [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) classe.
 - Il nuovo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) classe fornisce una soluzione generalizzata per gestire l'archiviazione di dati persistenti in Game Center. `GKGameSession` gestisce un elenco di lettori e l'applicazione è responsabile implementazione come e quando viene archiviata data partecipante, recuperati o scambiati tra i lettori. In molti casi le sessioni di gioco può sostituire corrispondenze esistenti basato su, corrispondenze in tempo reale o persistente gioco Salva metodi.

<a name="GameplayKit-Enhancements" />

## <a name="gameplaykit-enhancements"></a>Miglioramenti di GameplayKit

I miglioramenti seguenti sono stati apportati a framework GameplayKit tvOS 10:

 - Generazione di disturbo procedurale è stato aggiunto e può essere utilizzata per migliorare il realismo nelle trame naturali, aggiungere realismo ai movimenti fotocamera e la creazione di gioco avanzati.
 - Utilizzo del partizionamento spaziali per partizionare i dati di gioco world per la ricerca efficiente.
 - Nuovo responsabile delle strategie Monte Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) è stato aggiunto per il calcolo di spostamento possibili completo.
 - È stata aggiunta una nuova API di albero delle decisioni ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) per migliorare al compilazione di gioco.
 - È stato aggiunto il supporto 3D per l'agente esistente e i comportamenti di ricerca di percorso utilizzando il nuovo [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) classi.
 - Usare il nuovo [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) classe per fornire i percorsi di ricerca naturale ad alte prestazioni.
 - Il nuovo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) crea classi combinazione GameplayKit e SpriteKit più semplice che mai.

<a name="Metal-Enhancements" />

## <a name="metal-enhancements"></a>Miglioramenti metalli

I miglioramenti seguenti sono stati apportati a framework Metal tvOS 10:

 - App e giochi 3D ora è possono usare _a mosaico_ per il rendering in modo efficiente scene complesse e geometria tramite GPU.
 - Usare la specializzazione di funzione per creare una raccolta altamente ottimizzato di materiali e funzioni di combinazione di luce per una scena.
 - Fornire un controllo accurato di allocazione delle risorse per ottimizzare le prestazioni di metallo basati su App con risorse heap e Memoryless destinazioni di rendering.

Per ulteriori informazioni, vedere Apple [Guida per programmatori Metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

<a name="Metal-Performance-Shaders-Enhancements" />

## <a name="metal-performance-shaders-enhancements"></a>Miglioramenti di prestazioni Metal shader

I miglioramenti seguenti sono stati apportati a framework Metal prestazioni shader tvOS 10:

 - Molti kernel nuovo sono state aggiunte al framework Metal prestazioni shader per consentire all'applicazione in modo da sfruttare calcoli altamente ottimizzata, i dati in parallelo, ad esempio le conversioni di spazio colore e le operazioni di rete neurale.

<a name="ModelIO-Enhancements" />

## <a name="modelio-enhancements"></a>Miglioramenti di ModelIO

I miglioramenti seguenti sono stati apportati a framework ModelIO tvOS 10:

 - È ora supportato il formato del file EUR.
 - Usare il nuovo `MDLMaterialPropertyGraph` classe di runtime di supportare facilmente modifiche ai modelli.
 - Firma distanza campo è stato aggiunto il supporto di [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) classe.
 - Usare il nuovo `MDLLightProbeIrradianceDataSource` classe per semplificare il posizionamento luce Probe.

<a name="SceneKit-Enhancements" />

## <a name="scenekit-enhancements"></a>Miglioramenti di SceneKit

I miglioramenti seguenti sono stati apportati a framework SceneKit tvOS 10:

 - SceneKit include ora un nuovo sistema fisicamente base per il Rendering PBR () per ottenere risultati più realistici con più semplice la creazione di asset.
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

<a name="SpriteKit-Enhancements" />

## <a name="spritekit-enhancements"></a>Miglioramenti di SpriteKit

I miglioramenti seguenti sono stati apportati a framework SpriteKit tvOS 10:

 - Tilemaps ora supportano forme riquadro quadrato, vite ed effetto per 2D, D 2.5 e giochi di scorrimento sul lato utilizzando il `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` e `SKTileSet` classi.
 - Usare il nuovo `SKWarpGeometry` classe per estendere o falsare [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) o [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) per il rendering. Il nuovo [SKAction](https://developer.apple.com/reference/spritekit/skaction) classe può essere utilizzata per aggiungere un'animazione transizioni tra gli effetti warp.
 - Shader personalizzato può fornire attributi (`SKAttribute`) che può essere configurata separatamente per ogni nodo che utilizza lo shader fornendo un valore di attributo (`SKAttributeValue`).
 - Il [SKView](https://developer.apple.com/reference/spritekit/skview) classe fornisce numerosi nuovi metodi per fornire un controllo accurato quando e come viene eseguito il rendering di una scena.

<a name="UIKit-Enhancements" />

## <a name="uikit-enhancements"></a>Miglioramenti di UIKit

I miglioramenti seguenti sono stati apportati a framework UIKit tvOS 10:

 - Lo stato attivo API è stato migliorato per supportare lo stato attivo dell'elemento non visualizza inoltre a `UIViews`. Gli elementi che supportano lo stato attivo _deve_ implementare il `IUIFocusItem` interfaccia.
 - Il nuovo `UIGraphicsRender` classe fornisce un metodo orientata agli oggetti di creazione di bitmap o file PDF da UIKit rendering o grafica di base e sostituisce deprecate `UIGraphicsBeginImageContext` metodo.
 - La `UIUserInterfaceStyle` classe è stato aggiunto per determinare il tema dell'interfaccia utente (chiaro o scuro) è attualmente attivo.
 - È stato aggiunto il nuovo supporto di animazione completamente interattivo, oggetto, interrompibili e furgone essere collegati ai movimenti. Del pleas vedere Apple [UIViewAnimating Protocol Reference](https://developer.apple.com/reference/uikit/uiviewanimating), [riferimento alla classe UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), [UITimingCurveProvider Protocol Reference](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [Riferimento alla classe UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) e [riferimento alla classe UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) per ulteriori informazioni.
 - Il nuovo `UIPreviewInteraction` e `UIPreviewInteractionDelegate` consentire all'app di app per fornire un'interfaccia personalizzata per le operazioni di lettura e pop.
 - Il nuovo `UIAccessibilityCustomRotor` classe consente all'app fornire funzionalità personalizzate, specifiche per il contesto a dispositivi di assistive technology, ad esempio Voice su.
 - Utilizzare il `UIAccessibilityIsAssistiveTouchRunning` e `UIAccessibilityAssistiveTouchStatusDidChangeNotification` simboli per determinare se AssistiveTouch è abilitata.
 - Utilizzare il `UIAccessibilityHearingDevicePairedEar` e `UIAccessibilityHearingDevicePairedEarDidChangeNotification` simboli per ottenere lo stato di qualsiasi abbinato IFM udito visivi.
 - Il nuovo [UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) API sono disponibili nuove opzioni (ad esempio i limiti di durata) e verrà automaticamente dichiarare tipi di contenuto compatibili per i tipi di classi comuni.
 - Per supportare tipi dinamici nelle etichette, usano i nuovi campi di testo e caselle di testo `PreferredFontForTextStyle` metodo la `UIFont` classe.
 - Per decidere se un elemento necessario aggiornarla carattere quando i dispositivi `UIContentSizeCategory` le modifiche, utilizzare il `AdjustsFontForContentSizeCategory` proprietà del `UIContentSizeCategoryAdjusting` delegato.
 - L'app è ora possibile controllare l'aspetto del badge per gli elementi della barra della scheda ad esempio il colore di sfondo e di testo.
 - Il controllo Aggiorna ora supportati in tutte le visualizzazioni di scorrimento e scorrimento sottoclassi (ad esempio `UICollectionView`).
 - Il `OpenURL` metodo la `UIApplication` classe viene chiamata in modo asincrono supporta ora un gestore di completamento che viene chiamato una volta completata l'apertura.
 - Avviare la condivisione CloudKit e modificarne le proprietà utilizzando il nuovo `UICloudSharingController` e `UICloudSharingControllerDelegate` classi.
 - Sfruttare i vantaggi di celle caricate in background per migliorare l'esperienza di scorrimento `UICollectionViews` con il nuovo `UICollectionViewDataSourcePrefetching` delegato.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Novità di tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
