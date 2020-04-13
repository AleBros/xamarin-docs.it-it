---
title: Renderer personalizzati di Xamarin.Forms
description: I renderer personalizzati consentono agli sviluppatori di eseguire l'override del rendering dei controlli nativi in ogni piattaforma per personalizzare l'aspetto e il comportamento dei controlli di Xamarin.Forms.
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/03/2019
ms.openlocfilehash: b87e713f89951d03408fa559bcf6e02cdae65e28
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "74824250"
---
# <a name="xamarinforms-custom-renderers"></a>Renderer personalizzati di Xamarin.Forms

_Il rendering delle interfacce utente di Xamarin.Forms viene eseguito utilizzando i controlli nativi della piattaforma di destinazione, consentendo alle applicazioni Xamarin.Forms di mantenere l'aspetto appropriato per ogni piattaforma. I Renderer personalizzati consentono agli sviluppatori di eseguire l'override di questo processo per personalizzare l'aspetto e il comportamento dei controlli Xamarin.Forms in ogni piattaforma._

## <a name="introduction-to-custom-renderers"></a>[Introduzione ai renderer personalizzatiIntroduction to custom renderers](introduction.md)

I renderer personalizzati offrono un approccio efficace alla personalizzazione dell'aspetto e del comportamento dei controlli di Xamarin.Forms. Possono essere usati per apportare piccole modifiche allo stile o per la personalizzazione di layout e comportamenti sofisticati specifici di una piattaforma. Questo articolo offre un'introduzione ai renderer personalizzati e illustra la procedura di creazione di un renderer personalizzato.

## <a name="renderer-base-classes-and-native-controls"></a>[Classi base del renderer e controlli nativiRenderer base classes and native controls](renderers.md)

A ogni controllo di Xamarin.Forms è associato un renderer per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Questo articolo elenca le classi del renderer e dei controlli nativi che implementano ogni pagina, layout, vista e cella di Xamarin.Forms.

## <a name="customizing-an-entry"></a>[Personalizzazione di una voce](entry.md)

Il controllo Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) consente di modificare una singola riga di testo. Questo articolo illustra come creare un renderer personalizzato per il controllo `Entry` che consente agli sviluppatori di eseguire l'override del rendering nativo predefinito usando la propria personalizzazione specifica della piattaforma.

## <a name="customizing-a-contentpage"></a>[Personalizzazione di un elemento ContentPage](contentpage.md)

A [`ContentPage`](xref:Xamarin.Forms.ContentPage) è un elemento visivo che visualizza una singola visualizzazione e occupa la maggior parte dello schermo. Questo articolo illustra come creare un renderer personalizzato per la pagina `ContentPage` che consente agli sviluppatori di eseguire l'override del rendering nativo predefinito usando la propria personalizzazione specifica della piattaforma.

## <a name="customizing-a-map"></a>[Personalizzazione di una mappa](map/index.md)

Xamarin.Forms.Maps offre un'astrazione multipiattaforma per la visualizzazione delle mappe che usano le API di mapping native in ogni piattaforma, per consentire agli utenti di usufruire di un'esperienza di mapping veloce e intuitiva. Questo argomento spiega come creare renderer personalizzati per il controllo `Map` che consente agli sviluppatori di eseguire l'override del rendering nativo predefinito usando la propria personalizzazione specifica della piattaforma.

## <a name="customizing-a-listview"></a>[Personalizzazione di un oggetto ListView](listview.md)

Un Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) è una visualizzazione che visualizza una raccolta di dati come elenco verticale. Questo articolo spiega come creare un renderer personalizzato che incapsula gli elenchi specifici della piattaforma e i layout di cella nativi, consentendo un maggiore controllo sulle prestazioni dei controlli nativi.

## <a name="customizing-a-viewcell"></a>[Personalizzazione di un elemento ViewCell](viewcell.md)

Un oggetto Xamarin.Forms [`ViewCell`](xref:Xamarin.Forms.ViewCell) è una cella [`ListView`](xref:Xamarin.Forms.ListView) [`TableView`](xref:Xamarin.Forms.TableView)che può essere aggiunta a o , che contiene una visualizzazione definita dallo sviluppatore. Questo articolo illustra come si crea un renderer personalizzato per un oggetto `ViewCell` ospitato all'interno di un controllo `ListView` di Xamarin.Forms. Questo impedisce che i calcoli di layout di Xamarin.Forms vengano ripetutamente chiamati durante lo scorrimento di `ListView`.

## <a name="customizing-a-webview"></a>[Personalizzazione di un elemento WebView](hybridwebview.md)

Un file Xamarin.Forms [`WebView`](xref:Xamarin.Forms.WebView) è una visualizzazione che visualizza contenuto Web e HTML nell'app. In questo articolo viene illustrato come creare `WebView` un renderer personalizzato che estende l'oggetto per consentire la richiamata di codice C ' da JavaScript.

## <a name="implementing-a-view"></a>[Implementazione di una vista](view.md)

Xamarin.Forms i controlli delle interfacce [`View`](xref:Xamarin.Forms.View) utente personalizzate devono derivare dalla classe , che viene utilizzata per posizionare layout e controlli sullo schermo. Questo articolo spiega come creare un renderer personalizzato per un controllo personalizzato di Xamarin.Forms usato per visualizzare un flusso video di anteprima dalla fotocamera del dispositivo.

## <a name="implementing-a-video-player"></a>[Implementazione di un lettore video](video-player/index.md)

Questo articolo illustra come scrivere i renderer per implementare un controllo `VideoPlayer` personalizzato in grado di riprodurre video dal Web, video incorporati come risorse dell'applicazione o video archiviati nella raccolta di video del dispositivo dell'utente. Vengono illustrate diverse tecniche, tra cui l'implementazione di metodi e le proprietà associabili di sola lettura.
