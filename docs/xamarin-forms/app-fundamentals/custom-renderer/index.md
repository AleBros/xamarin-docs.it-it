---
title: Renderer personalizzati di Xamarin.Forms
description: I renderer personalizzati consentono agli sviluppatori di eseguire l'override del rendering dei controlli nativi in ogni piattaforma per personalizzare l'aspetto e il comportamento dei controlli di Xamarin.Forms.
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/03/2019
ms.openlocfilehash: 04d40aa4cafe663113957d31bdb8a6463ba58ba5
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82516462"
---
# <a name="xamarinforms-custom-renderers"></a>Renderer personalizzati di Xamarin.Forms

_Le interfacce utente Novell. Forms vengono visualizzate usando i controlli nativi della piattaforma di destinazione, consentendo alle applicazioni Novell. Forms di mantenere l'aspetto appropriato per ogni piattaforma. I renderer personalizzati consentono agli sviluppatori di eseguire l'override di questo processo per personalizzare l'aspetto e il comportamento dei controlli Novell. Forms in ogni piattaforma._

## <a name="introduction-to-custom-renderers"></a>[Introduzione ai renderer personalizzati](introduction.md)

I renderer personalizzati offrono un approccio efficace alla personalizzazione dell'aspetto e del comportamento dei controlli di Xamarin.Forms. Possono essere usati per apportare piccole modifiche allo stile o per la personalizzazione di layout e comportamenti sofisticati specifici di una piattaforma. Questo articolo offre un'introduzione ai renderer personalizzati e illustra la procedura di creazione di un renderer personalizzato.

## <a name="renderer-base-classes-and-native-controls"></a>[Classi base renderer e controlli nativi](renderers.md)

A ogni controllo di Xamarin.Forms è associato un renderer per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Questo articolo elenca le classi del renderer e dei controlli nativi che implementano ogni pagina, layout, vista e cella di Xamarin.Forms.

## <a name="customizing-an-entry"></a>[Personalizzazione di una voce](entry.md)

Il controllo Novell. [`Entry`](xref:Xamarin.Forms.Entry) Forms consente di modificare una singola riga di testo. Questo articolo illustra come creare un renderer personalizzato per il controllo `Entry` che consente agli sviluppatori di eseguire l'override del rendering nativo predefinito usando la propria personalizzazione specifica della piattaforma.

## <a name="customizing-a-contentpage"></a>[Personalizzazione di un elemento ContentPage](contentpage.md)

Un [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto è un elemento visivo che visualizza una singola visualizzazione e occupa la maggior parte dello schermo. Questo articolo illustra come creare un renderer personalizzato per la pagina `ContentPage` che consente agli sviluppatori di eseguire l'override del rendering nativo predefinito usando la propria personalizzazione specifica della piattaforma.

## <a name="customizing-a-map-pin"></a>[Personalizzazione di un segnaposto per la mappa](map-pin.md)

Xamarin.Forms.Maps offre un'astrazione multipiattaforma per la visualizzazione delle mappe che usano le API di mapping native in ogni piattaforma, per consentire agli utenti di usufruire di un'esperienza di mapping veloce e intuitiva. In questo argomento viene illustrato come creare un renderer personalizzato per `Map` il controllo, consentendo agli sviluppatori di eseguire l'override del rendering nativo predefinito con una personalizzazione specifica della piattaforma.

## <a name="customizing-a-listview"></a>[Personalizzazione di un oggetto ListView](listview.md)

Un Novell. Forms [`ListView`](xref:Xamarin.Forms.ListView) è una vista che consente di visualizzare una raccolta di dati sotto forma di elenco verticale. Questo articolo spiega come creare un renderer personalizzato che incapsula gli elenchi specifici della piattaforma e i layout di cella nativi, consentendo un maggiore controllo sulle prestazioni dei controlli nativi.

## <a name="customizing-a-viewcell"></a>[Personalizzazione di un elemento ViewCell](viewcell.md)

Un Novell. Forms [`ViewCell`](xref:Xamarin.Forms.ViewCell) è una cella che può essere aggiunta a [`ListView`](xref:Xamarin.Forms.ListView) un [`TableView`](xref:Xamarin.Forms.TableView)oggetto o che contiene una visualizzazione definita dallo sviluppatore. Questo articolo illustra come si crea un renderer personalizzato per un oggetto `ViewCell` ospitato all'interno di un controllo `ListView` di Xamarin.Forms. Questo impedisce che i calcoli di layout di Xamarin.Forms vengano ripetutamente chiamati durante lo scorrimento di `ListView`.

## <a name="customizing-a-webview"></a>[Personalizzazione di un elemento WebView](hybridwebview.md)

Un Novell. Forms [`WebView`](xref:Xamarin.Forms.WebView) è una vista che Visualizza il contenuto Web e HTML nell'app. Questo articolo illustra come creare un renderer personalizzato che estende per consentire `WebView` la chiamata del codice C# da JavaScript.

## <a name="implementing-a-view"></a>[Implementazione di una vista](view.md)

I [`View`](xref:Xamarin.Forms.View) controlli delle interfacce utente personalizzate Novell. Forms devono derivare dalla classe, utilizzata per posizionare i layout e i controlli sullo schermo. Questo articolo spiega come creare un renderer personalizzato per un controllo personalizzato di Xamarin.Forms usato per visualizzare un flusso video di anteprima dalla fotocamera del dispositivo.

## <a name="implementing-a-video-player"></a>[Implementazione di un lettore video](video-player/index.md)

Questo articolo illustra come scrivere i renderer per implementare un controllo `VideoPlayer` personalizzato in grado di riprodurre video dal Web, video incorporati come risorse dell'applicazione o video archiviati nella raccolta di video del dispositivo dell'utente. Vengono illustrate diverse tecniche, tra cui l'implementazione di metodi e le proprietà associabili di sola lettura.
