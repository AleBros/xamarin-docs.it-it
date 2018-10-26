---
title: Introduzione a iOS 11
description: Questo documento include collegamenti alle varie guide che descrivono le funzionalità di iOS 11, tra cui ARKit CoreML, MapKit, PDFKit, SiriKit, il framework Vision e altro ancora.
ms.prod: xamarin
ms.assetid: 22C38EA6-6DA9-4B92-B41B-814E589033F6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/19/2017
ms.openlocfilehash: 5e75a7872081d82c289db0312bbc7d84bce77b4e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116719"
---
# <a name="introduction-to-ios-11"></a>Introduzione a iOS 11

_Provare di nuovo iOS 11 API con Xamarin._

> [!NOTE]
> Prima di iniziare, rivedere le [introduttiva](get-started.md) pagina per istruzioni su come installare Xcode 9.

![Esempio ARKit](images/arkit.png) ![Inserimento di oggetti di AR](images/arkit2.png) ![Esempio CoreML](images/coreml.png) ![Esempio MapKit](images/mapkit.png) ![Esempio di rettangoli di visione artificiale](images/vision1.png) ![Esempio di volti visione artificiale](images/vision2.png) ![Trascinamento della selezione di esempio](images/drag-drop.png) ![Trascinamento della selezione di esempio](images/drag-drop2.png) ![Esempio di SiriKit](images/sirikit.png)

iOS 11 include molti miglioramenti e funzionalità completamente nuova per un'ampia gamma di Framework:

## <a name="preparing-your-app-for-ios-11updating-your-appindexmd"></a>[Preparazione dell'App per iOS 11](updating-your-app/index.md)

Apple ha introdotto architettura aggiornamenti, nuove modifiche visive e un processo di aggiornato iTunes Connect per iOS 11. Usare questa guida per assicurarsi che l'app xamarin. IOS sia pronto per la nuova versione.

## <a name="arkitarkitindexmd"></a>[ARKit](arkit/index.md)

ARKit riporta realtà aumentata in iOS, che consente agli utenti di interagire con il mondo tramite la fotocamera del dispositivo.
Con Xamarin, è anche possibile usare [ARKit con UrhoSharp](arkit/urhosharp.md).

## <a name="coremlcoremlmd"></a>[CoreML](coreml.md)

Modelli di Machine learning possono essere integrati nell'App per iOS 11 con CoreML. Il framework CoreML fornisce API semplice per incorporare i modelli esistenti nei progetti di app per consentire i problemi da analizzare a destra nell'app.

## <a name="corenfccorenfcmd"></a>[CoreNFC](corenfc.md)

iPhone 7 e i dispositivi più recenti possono leggere i tag quasi Field Communication (NFC), per abilitare le app rilevare i prodotti con tag, posizioni o elementi del mondo attorno a esse.

## <a name="drag-and-dropdrag-and-dropmd"></a>[Trascinamento della selezione](drag-and-drop.md)

Il framework di trascinamento della selezione offre un supporto a livello di iOS per spostare i dati direttamente da touch. Su iPad, è possibile trascinare all'interno e tra diverse app. mentre in un iPhone, è possibile trascinare solo all'interno della stessa app. È supportato per molti tipi di personalizzazione, inclusi i tipi di dati avanzati, animazioni e i movimenti multitocco di gestione.

## <a name="mapkitmapkitmd"></a>[MapKit](mapkit.md)

MapKit dispone di una serie di miglioramenti, incluso il supporto per il marcatore automatica di raggruppamento e l'aggiunta di una bussola alla visualizzazione.

## <a name="pdfkit"></a>PDFKit

PDFKit è ora disponibile in iOS 11, offrendo la creazione di PDF e alle App per la funzionalità di modifica.

## <a name="sirikitsirikitmd"></a>[SiriKit](sirikit.md)

Siri supporta ora anche altre interazioni, inclusi gli elenchi e le note e altri miglioramenti, ad esempio i nomi delle app alternativi.

## <a name="visionvisionmd"></a>[Vision](vision.md)

Offre un'ampia gamma di immagine di funzionalità di elaborazione e analisi ai dispositivi iOS, tra cui rilevamento del viso e riconoscimento CoreML modelli, nuove API di rilevamento del codice a barre, testo e orizzonte il rilevamento e rilevamento più generale di oggetti e rilevamento.

## <a name="samples"></a>Esempi

Abbiamo un numero di c# [esempi](https://developer.xamarin.com/samples/ios/iOS11/) per iniziare a usare:

* [Esempio ARKit](https://developer.xamarin.com/samples/monotouch/ios11/ARKitSample/)
* [ARKit inserimento di oggetti](https://developer.xamarin.com/samples/monotouch/ios11/ARKitPlacingObjects/)
* [ARKit e UrhoSharp](arkit/urhosharp.md)
* [Esempio CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreML)
* [Esempio di riconoscimento di immagini CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition)
* [CoreML con modelli personalizzati di Azure](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLAzureModel)
* [Esempio di lettore CoreNFC Tag](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)
* [Trascinamento della selezione di visualizzazione tabella](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView)
* [Trascinamento della selezione di visualizzazione di raccolta](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCollectionView)
* [Trascinamento della selezione di visualizzazione personalizzata](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCustomView)
* [Trascinare DragBoard di e & sempio Drop](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropDragBoard)
* [Esempio MapKit](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample)
* [Esempio di SiriKit](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)
* [Esempio di framework aggiornato foto](https://developer.xamarin.com/samples/monotouch/ios11/SamplePhotoApp/)
* [Vision e CoreML esempio](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision)
* [Esempio di rilevamento di rettangoli di visione artificiale](https://developer.xamarin.com/samples/monotouch/ios11/VisionRects)
* [Esempio di rilevamento volti di visione artificiale](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces)
* [Esempio di widget PDKFit](https://developer.xamarin.com/samples/monotouch/ios11/PDFAnnotationWidgetsAdvanced)
* [Esempio PDFKit filigrana](https://developer.xamarin.com/samples/monotouch/ios11/PDFDocumentWatermark)

## <a name="more-information"></a>Altre informazioni

Per altre informazioni su iOS 11, visitare di Apple [What ' s New in iOS 11](https://developer.apple.com/ios/) documentazione.


## <a name="related-links"></a>Collegamenti correlati

- [What ' s New in iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Esempi di Xamarin iOS 11](https://developer.xamarin.com/samples/ios/iOS11/)
