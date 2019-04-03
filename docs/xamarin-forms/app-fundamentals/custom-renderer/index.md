---
title: Renderer personalizzati di Xamarin.Forms
description: I renderer personalizzati consentono agli sviluppatori di eseguire l'override del rendering dei controlli nativi in ogni piattaforma per personalizzare l'aspetto e il comportamento dei controlli di Xamarin.Forms.
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 3a59cbb8385fe201d7eef086092c9b393a21cc71
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57666363"
---
# <a name="xamarinforms-custom-renderers"></a>Renderer personalizzati di Xamarin.Forms

_In Xamarin.Forms il rendering delle interfacce utente viene eseguito con i controlli nativi della piattaforma di destinazione, consentendo alle applicazioni Xamarin.Forms di mantenere l'aspetto appropriato per ogni piattaforma. I renderer personalizzati consentono agli sviluppatori di eseguire l'override di questo processo per personalizzare l'aspetto e il comportamento dei controlli di Xamarin.Forms in ogni piattaforma._

## <a name="introduction-to-custom-renderersintroductionmd"></a>[Introduzione ai renderer personalizzati](introduction.md)

I renderer personalizzati offrono un approccio efficace alla personalizzazione dell'aspetto e del comportamento dei controlli di Xamarin.Forms. Possono essere usati per apportare piccole modifiche allo stile o per la personalizzazione di layout e comportamenti sofisticati specifici di una piattaforma. Questo articolo offre un'introduzione ai renderer personalizzati e illustra la procedura di creazione di un renderer personalizzato.

## <a name="renderer-base-classes-and-native-controlsrenderersmd"></a>[Classi di base e controlli nativi del renderer](renderers.md)

A ogni controllo di Xamarin.Forms è associato un renderer per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Questo articolo elenca le classi del renderer e dei controlli nativi che implementano ogni pagina, layout, vista e cella di Xamarin.Forms.

## <a name="customizing-an-entryentrymd"></a>[Personalizzazione di una voce](entry.md)

Il controllo [`Entry`](xref:Xamarin.Forms.Entry) di Xamarin.Forms consente la modifica di una singola riga di testo. Questo articolo illustra come creare un renderer personalizzato per il controllo `Entry` che consente agli sviluppatori di eseguire l'override del rendering nativo predefinito usando la propria personalizzazione specifica della piattaforma.

## <a name="customizing-a-contentpagecontentpagemd"></a>[Personalizzazione di un elemento ContentPage](contentpage.md)

Un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) è un elemento visivo che visualizza una singola vista e occupa la maggior parte della schermata. Questo articolo illustra come creare un renderer personalizzato per la pagina `ContentPage` che consente agli sviluppatori di eseguire l'override del rendering nativo predefinito usando la propria personalizzazione specifica della piattaforma.

## <a name="customizing-a-mapmapindexmd"></a>[Personalizzazione di una mappa](map/index.md)

Xamarin.Forms.Maps offre un'astrazione multipiattaforma per la visualizzazione delle mappe che usano le API di mapping native in ogni piattaforma, per consentire agli utenti di usufruire di un'esperienza di mapping veloce e intuitiva. Questo argomento spiega come creare renderer personalizzati per il controllo `Map` che consente agli sviluppatori di eseguire l'override del rendering nativo predefinito usando la propria personalizzazione specifica della piattaforma.

## <a name="customizing-a-listviewlistviewmd"></a>[Personalizzazione di un elemento ListView](listview.md)

L'oggetto [`ListView`](xref:Xamarin.Forms.ListView) di Xamarin.Forms è una vista che visualizza una raccolta di dati sotto forma di elenco verticale. Questo articolo spiega come creare un renderer personalizzato che incapsula gli elenchi specifici della piattaforma e i layout di cella nativi, consentendo un maggiore controllo sulle prestazioni dei controlli nativi.

## <a name="customizing-a-viewcellviewcellmd"></a>[Personalizzazione di un elemento ViewCell](viewcell.md)

Un oggetto [`ViewCell`](xref:Xamarin.Forms.ViewCell) di Xamarin.Forms è una cella che può essere aggiunta a un oggetto [`ListView`](xref:Xamarin.Forms.ListView) o [`TableView`](xref:Xamarin.Forms.TableView), che contiene una vista definita dallo sviluppatore. Questo articolo illustra come si crea un renderer personalizzato per un oggetto `ViewCell` ospitato all'interno di un controllo `ListView` di Xamarin.Forms. Questo impedisce che i calcoli di layout di Xamarin.Forms vengano ripetutamente chiamati durante lo scorrimento di `ListView`.

## <a name="implementing-a-viewviewmd"></a>[Implementazione di un elemento View](view.md)

I controlli delle interfacce utente personalizzate di Xamarin.Forms devono derivare dalla classe [`View`](xref:Xamarin.Forms.View), che viene usata per posizionare layout e controlli sullo schermo. Questo articolo spiega come creare un renderer personalizzato per un controllo personalizzato di Xamarin.Forms usato per visualizzare un flusso video di anteprima dalla fotocamera del dispositivo.

## <a name="implementing-a-hybridwebviewhybridwebviewmd"></a>[Implementazione di un elemento HybridWebView](hybridwebview.md)

Questo articolo spiega come creare un renderer personalizzato per un controllo personalizzato `HybridWebView`, che dimostra come si possono ottimizzare i controlli Web specifici della piattaforma per consentire al codice C# di essere richiamato da JavaScript.

## <a name="implementing-a-video-playervideo-playerindexmd"></a>[Implementazione di un lettore video](video-player/index.md)

Questo articolo illustra come scrivere i renderer per implementare un controllo `VideoPlayer` personalizzato in grado di riprodurre video dal Web, video incorporati come risorse dell'applicazione o video archiviati nella raccolta di video del dispositivo dell'utente. Vengono illustrate diverse tecniche, tra cui l'implementazione di metodi e le proprietà associabili di sola lettura.


## <a name="related-links"></a>Collegamenti correlati

- [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Custom Renderers (Xamarin University Video)](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/) (Video di Xamarin University sui renderer personalizzati)
