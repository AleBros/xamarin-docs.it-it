---
title: Modifiche aggiuntive per i Framework tvOS 10
description: Questo documento descrive le modifiche e i miglioramenti secondari apportati ai Framework esistenti in iOS 10. Vengono illustrati gli aggiornamenti di AVFoundation, AVKit, core data, Core graphics, Foundation, GameKit, GameplayKit e altro ancora.
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 5b9dfb354f33f67c73b415f8c109ebdc27dcdb6d
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292666"
---
# <a name="additional-tvos-10-frameworks-changes"></a>Modifiche aggiuntive per i Framework tvOS 10

Oltre alle principali modifiche apportate a tvOS, Apple ha apportato modifiche e miglioramenti a diversi Framework esistenti in tvOS 10.

<a name="AV-Foundation-Framework" />

## <a name="avfoundation-framework-additions"></a>Aggiunte a AVFoundation Framework

Il Framework AVFoundation include i miglioramenti seguenti:

- In tvOS 10, l'app non implementa più comportamenti [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) diversi in base al tipo di contenuto. È sufficiente impostare la proprietà `Rate` e AVFoundation determinerà quando sarà disponibile contenuto sufficiente per la riproduzione senza bloccarsi.
- La nuova classe `AVPlayerLooper` rende più semplice il ciclo di un dato supporto durante la riproduzione.

<a name="AVKit-Framework-Enhancements" />

## <a name="avkit-framework-enhancements"></a>Miglioramenti di AVKit Framework

Il Framework AVKit include i miglioramenti seguenti:

- L'app ora ha il controllo sul comportamento ignorato del [AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller) , quindi un movimento ignorato può passare all'elemento successivo nella playlist o avanzare all'interno dell'elemento corrente.

<a name="Core-Data-Enhancements" />

## <a name="core-data-enhancements"></a>Miglioramenti dei dati principali

tvOS 10 include i miglioramenti seguenti al Framework di dati principale:

- Gli oggetti [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) radice supportano gli errori e il recupero simultanei senza serializzazione.
- La classe [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) gestisce un pool di archivi dati SQLite.
- Gli oggetti [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) con gli archivi dati SQLite nella modalità Journal Wal supportano la nuova funzionalità di generazione delle query in cui i contesti degli oggetti gestiti (MOC) possono essere aggiunti a versioni specifiche del database per le transazioni di recupero e di errore future.
- Uso del `NSPersistenceContainer` generale per fare riferimento al `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e ad altre risorse di configurazione dei dati principali.
- Sono stati aggiunti diversi nuovi metodi pratici per `NSManagedObject` semplificare l'esecuzione di operazioni di recupero e creazione di sottoclassi.

Per altre informazioni, vedere la Guida di [riferimento a Core Data Framework](https://developer.apple.com/reference/coredata)di Apple.

<a name="Core-Graphics-Enhancements" />

## <a name="core-graphics-enhancements"></a>Miglioramenti della grafica di base

tvOS 10 include i miglioramenti seguenti al Framework di grafica principale:

- La nuova classe CGColorConverterRef può essere utilizzata per eseguire una serie di conversioni di colori.

<a name="Core-Image-Enhancements" />

## <a name="core-image-enhancements"></a>Miglioramenti delle immagini principali

tvOS 10 apporta i miglioramenti seguenti al framework principale dell'immagine:

- Il metodo `ImageWithExtent` della classe [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) può essere usato per inserire l'elaborazione personalizzata nell'operazione di filtro. L'immagine principale richiama il callback specificato tra i filtri quando si elabora un'immagine per l'output o la visualizzazione.
- L'app ora può elaborare le immagini in uno spazio di colore al di fuori dello spazio dei colori di lavoro del contesto immagine principale convertendo in e fuori dallo spazio di colore prima e dopo l'elaborazione.
- Sono stati apportati diversi miglioramenti alle prestazioni di rendering per `UIImage` il rendering (in caso di archiviazione di immagini di base immagine) negli oggetti `UIImageView`.
- `UIImage` oggetti con tag Wide-Gamut viene eseguito il rendering come colore Wide-gamut in `UIImageView` oggetti sui dispositivi iOS che supportano il colore Wide.
- Il codice kernel dell'immagine principale può ora richiedere formati di output pixel specifici.

Sono stati aggiunti anche i nuovi filtri per le immagini principali seguenti:

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

<a name="Foundation-Enhancements" />

## <a name="foundation-enhancements"></a>Miglioramenti di Foundation

Sono stati apportati i miglioramenti seguenti al Framework di base per tvOS 10:

- Usare la nuova classe [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) per creare calcoli di intervallo di data e ora, ad esempio le durate, per confrontare gli intervalli e i test per le intersezioni degli intervalli.
- Sono state aggiunte diverse nuove proprietà alla classe [NSLocal](https://developer.apple.com/reference/foundation/nslocale) per acquisire informazioni locali e i formati di visualizzazione disponibili.
- Usare la nuova classe [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) per eseguire la conversione tra unità di misura diverse (uom) o eseguire calcoli su valori in UOMs diversi.
- Utilizzare la nuova classe [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) per formattare le misurazioni localizzate per la visualizzazione all'utente finale.
- Utilizzare le nuove classi [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) per la rappresentazione di UOMs specifici.

<a name="GameKit-Enhancements" />

## <a name="gamekit-enhancements"></a>Miglioramenti di GameKit

Per il Framework GameKit in tvOS 10 sono stati apportati i miglioramenti seguenti:

- Un nuovo tipo di account solo iCloud è stato implementato dalla classe [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) .
- La nuova classe [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) fornisce una soluzione generalizzata per la gestione dell'archiviazione dei dati persistente nei Game Center. `GKGameSession` gestisce un elenco di giocatori e l'app è responsabile del modulo che implementa come e quando la data del partecipante viene archiviata, recuperata o scambiata tra i giocatori. In molti casi le sessioni di gioco possono sostituire le corrispondenze basate su turni esistenti, le corrispondenze in tempo reale o i metodi di salvataggio del gioco permanenti

<a name="GameplayKit-Enhancements" />

## <a name="gameplaykit-enhancements"></a>Miglioramenti di GameplayKit

Per il Framework GameplayKit in tvOS 10 sono stati apportati i miglioramenti seguenti:

- È stata aggiunta la generazione del rumore procedurale che può essere usata per migliorare il realismo nelle trame all'aspetto naturale, aggiungere il realismo ai movimenti della fotocamera e contribuire a generare mondi di gioco avanzati.
- Usare il partizionamento spaziale per partizionare i dati del mondo del gioco per una ricerca efficiente.
- Un nuovo Strategy Monte Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) è stato aggiunto per un calcolo completo possibile dello spostamento.
- È stata aggiunta una nuova API dell'albero delle decisioni ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) per migliorare l'intelligenza artificiale per la creazione di giochi.
- il supporto 3D è stato aggiunto ai comportamenti esistenti di individuazione degli agenti e dei percorsi usando le nuove classi [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) .
- Usare la nuova classe [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) per fornire percorsi a prestazioni elevate e naturali.
- Le nuove classi [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) rendono la combinazione di GameplayKit e SpriteKit più semplice che mai.

<a name="Metal-Enhancements" />

## <a name="metal-enhancements"></a>Miglioramenti Metal

Sono stati apportati i miglioramenti seguenti al Framework metal in tvOS 10:

- le app e i giochi 3D possono ora usare il _mosaico_ per eseguire in modo efficiente il rendering di scene e geometria complesse tramite la GPU.
- Usare la specializzazione della funzione per creare una raccolta altamente ottimizzata di funzioni di combinazione materiale e luce per una scena.
- Fornire un controllo con granularità fine dell'allocazione delle risorse per ottimizzare le prestazioni delle app basate su Metal usando gli heap delle risorse e le destinazioni di rendering prive di memoria.

Per altre informazioni, vedere la guida alla [programmazione](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)per i metal di Apple.

<a name="Metal-Performance-Shaders-Enhancements" />

## <a name="metal-performance-shaders-enhancements"></a>Miglioramenti degli shader performance metal

Sono stati apportati i miglioramenti seguenti a Metal Performance shaders Framework in tvOS 10:

- Molti nuovi kernel sono stati aggiunti a Metal Performance shaders Framework per consentire all'app di sfruttare i vantaggi offerti da calcoli paralleli a ottimizzazione elevata, ad esempio le conversioni dello spazio dei colori e le operazioni di rete neurale.

<a name="ModelIO-Enhancements" />

## <a name="modelio-enhancements"></a>Miglioramenti di ModelIO

Per il Framework ModelIO in tvOS 10 sono stati apportati i miglioramenti seguenti:

- Il formato di file USD è ora supportato.
- Utilizzare la nuova classe `MDLMaterialPropertyGraph` per supportare facilmente le modifiche di runtime ai modelli.
- Il supporto per il campo a distanza firmato è stato aggiunto alla classe [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) .
- Usare la nuova classe `MDLLightProbeIrradianceDataSource` per facilitare la selezione del posizionamento Probe leggero.

<a name="SceneKit-Enhancements" />

## <a name="scenekit-enhancements"></a>Miglioramenti di SceneKit

Per il Framework SceneKit in tvOS 10 sono stati apportati i miglioramenti seguenti:

- SceneKit include ora un nuovo sistema di rendering basato su fisico (PBR) per risultati più realistici con la creazione di asset più semplice.
- Utilizzare il nuovo modello di ombreggiatura [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) per produrre un'ampia gamma di effetti ombreggiamenti realistici, richiedendo solo tre proprietà fondamentali (`Diffuse`, `Metalness` e `Roughness`).
- Poiché l'ombreggiatura del PBR funziona meglio con l'illuminazione basata sull'ambiente, usare la proprietà `LightingEnvironment` per assegnare l'illuminazione basata su immagini all'intera scena.
- Usare la proprietà `IESProfileURL` per importare i dispositivi luminosi reali che definiscono la base illuminotecnica sui valori reali, ad esempio l'intensità (in lumen) e la temperatura del colore (in gradi Kelvin).
- La classe [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) può offrire maggiore realismo usando le funzionalità e gli effetti di HDR. Usare l'esposizione adattiva per creare effetti automatici o usare vignette, frangia dei colori e classificazione dei colori per aggiungere effetti FILMATIC al gioco.
- Entrambe le funzionalità della fotocamera PBR e HDR forniscono risultati migliori rispetto alle tecniche di rendering tradizionali e, di conseguenza, SceneKit esegue ora tutti i calcoli dei colori in uno spazio di colore lineare (usando la gamma di colori P3 sulle visualizzazioni dei dispositivi a colori Wide).
- SceneKit Now color corrisponde a tutti i colori leggendo le informazioni sul profilo colori.
- SceneKit interpreta i valori dei componenti dei colori in uno spazio di colore RGB lineare per tutti i tipi di shader.
- Poiché SceneKit legge e regola le informazioni sul profilo colori nelle immagini di trama, usare cataloghi asset per tutte le immagini per assicurarsi che queste informazioni vengano fornite.
- Sia il rendering dello spazio dei colori lineare che il colore Wide possono essere disabilitati specificando le chiavi `SCNDisableLinearSpaceRendering` e `SCNDisableWideGamut` nel `Info.plist`dell'app.
- Compilare i primitivi del poligono arbitrari (caricati da file o generati a livello di codice) per specificare la geometria con la nuova classe [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/documentation/scenekit/scngeometryprimitivetype/scngeometryprimitivetypepolygon) .

<a name="SpriteKit-Enhancements" />

## <a name="spritekit-enhancements"></a>Miglioramenti di SpriteKit

Per il Framework SpriteKit in tvOS 10 sono stati apportati i miglioramenti seguenti:

- Tilemaps supporta ora le forme di sezione quadrata, esagonale e isometrica per i giochi 2D, 2.5 D e a scorrimento laterale usando le classi `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` e `SKTileSet`.
- Usare la nuova classe `SKWarpGeometry` per estendere o distorcere il rendering di [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) o [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) . La nuova classe [SKAction](https://developer.apple.com/reference/spritekit/skaction) può essere usata per animare le transizioni tra gli effetti di distorsione.
- Gli shader personalizzati possono fornire attributi (`SKAttribute`) che possono essere configurati separatamente da ogni nodo che usa lo shader fornendo un valore di attributo (`SKAttributeValue`).
- La classe [SKView](https://developer.apple.com/reference/spritekit/skview) fornisce diversi nuovi metodi per fornire un controllo dettagliato su quando e come viene eseguito il rendering di una scena.

<a name="UIKit-Enhancements" />

## <a name="uikit-enhancements"></a>Miglioramenti di UIKit

Per il Framework UIKit in tvOS 10 sono stati apportati i miglioramenti seguenti:

- L'API messa a fuoco è stata migliorata per supportare lo stato attivo degli elementi non visualizzabili oltre al `UIViews`. Gli elementi che supportano lo stato attivo _devono_ implementare l'interfaccia `IUIFocusItem`.
- La nuova classe `UIGraphicsRender` fornisce un metodo orientato a oggetti per la creazione di bitmap o file PDF dal rendering UIKit o dalla grafica principale e sostituisce il metodo `UIGraphicsBeginImageContext` deprecato.
- È stata aggiunta la classe `UIUserInterfaceStyle` per determinare il tema dell'interfaccia utente (scuro o chiaro) attualmente attivo.
- È stato aggiunto il supporto per animazioni interrompibili completamente interattive, basate su oggetti e Van collegato ai movimenti. Per ulteriori informazioni, vedere le informazioni di [riferimento sul protocollo UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating), il riferimento alla classe [UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), il riferimento al [protocollo UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), il [riferimento alla classe UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) e il [riferimento alla classe UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) .
- La nuova `UIPreviewInteraction` e `UIPreviewInteractionDelegate` consentono all'app di fornire un'interfaccia personalizzata per le operazioni di visualizzazione e pop.
- La nuova classe `UIAccessibilityCustomRotor` consente all'app di fornire funzionalità personalizzate e specifiche del contesto alle tecnologie per l'accesso facilitato, ad esempio la voce.
- Usare i simboli `UIAccessibilityIsAssistiveTouchRunning` e `UIAccessibilityAssistiveTouchStatusDidChangeNotification` per determinare se le è abilitato.
- Usare i simboli `UIAccessibilityHearingDevicePairedEar` e `UIAccessibilityHearingDevicePairedEarDidChangeNotification` per ottenere lo stato di qualsiasi supporto per le udienze IFM associato.
- La nuova API [UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) fornisce nuove opzioni, ad esempio limitazioni di durata, e dichiarerà automaticamente i tipi di contenuto compatibili per i tipi di classe comuni.
- Per supportare il tipo dinamico nelle etichette, i campi di testo e le caselle di testo utilizzano il nuovo metodo `PreferredFontForTextStyle` della classe `UIFont`.
- Per decidere se un elemento deve aggiornare il tipo di carattere quando i dispositivi `UIContentSizeCategory` cambiano, utilizzare la proprietà `AdjustsFontForContentSizeCategory` del delegato `UIContentSizeCategoryAdjusting`.
- L'app può ora controllare l'aspetto della notifica per gli elementi della barra delle schede, ad esempio il testo e il colore di sfondo.
- Il controllo Refresh in è ora supportato in tutte le sottoclassi di visualizzazione a scorrimento e visualizzazione a scorrimento, ad esempio `UICollectionView`.
- Il metodo `OpenURL` della classe `UIApplication` viene chiamato in modo asincrono ora supporta un gestore di completamento chiamato dopo il completamento dell'apertura.
- Avviare la condivisione di CloudKit e modificarne le proprietà usando le nuove classi `UICloudSharingController` e `UICloudSharingControllerDelegate`.
- Sfruttare le celle prelette per migliorare l'esperienza di scorrimento di `UICollectionViews` con il nuovo delegato di `UICollectionViewDataSourcePrefetching`.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [Novità di tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
