---
title: Xamarin.FormsRenderer personalizzati
description: I renderer personalizzati consentono agli sviluppatori di eseguire l'override del rendering dei controlli nativi in ogni piattaforma, per personalizzare l'aspetto e il comportamento dei Xamarin.Forms controlli.
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/03/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: be281f9b7987a8d23ba6ac93f0771e432f277d45
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138918"
---
# <a name="xamarinforms-custom-renderers"></a>Xamarin.FormsRenderer personalizzati

_Le interfacce utente Novell. Forms vengono visualizzate usando i controlli nativi della piattaforma di destinazione, consentendo alle Xamarin.Forms applicazioni di mantenere l'aspetto appropriato per ogni piattaforma. I renderer personalizzati consentono agli sviluppatori di eseguire l'override di questo processo per personalizzare l'aspetto e il comportamento dei Xamarin.Forms controlli in ogni piattaforma._

## <a name="introduction-to-custom-renderers"></a>[Introduzione ai renderer personalizzati](introduction.md)

I renderer personalizzati offrono un approccio efficace per personalizzare l'aspetto e il comportamento dei Xamarin.Forms controlli. Possono essere usati per apportare piccole modifiche allo stile o per la personalizzazione di layout e comportamenti sofisticati specifici di una piattaforma. Questo articolo offre un'introduzione ai renderer personalizzati e illustra la procedura di creazione di un renderer personalizzato.

## <a name="renderer-base-classes-and-native-controls"></a>[Classi base renderer e controlli nativi](renderers.md)

Ogni Xamarin.Forms controllo dispone di un renderer associato per ogni piattaforma che crea un'istanza di un controllo nativo. Questo articolo elenca le classi del renderer e del controllo nativo che implementano ogni Xamarin.Forms pagina, layout, visualizzazione e cella.

## <a name="customizing-an-entry"></a>[Personalizzazione di una voce](entry.md)

Il Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) controllo consente di modificare una singola riga di testo. Questo articolo illustra come creare un renderer personalizzato per il controllo `Entry` che consente agli sviluppatori di eseguire l'override del rendering nativo predefinito usando la propria personalizzazione specifica della piattaforma.

## <a name="customizing-a-contentpage"></a>[Personalizzazione di un elemento ContentPage](contentpage.md)

Un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) è un elemento visivo che visualizza una singola visualizzazione e occupa la maggior parte dello schermo. Questo articolo illustra come creare un renderer personalizzato per la pagina `ContentPage` che consente agli sviluppatori di eseguire l'override del rendering nativo predefinito usando la propria personalizzazione specifica della piattaforma.

## <a name="customizing-a-map-pin"></a>[Personalizzazione di un segnaposto per la mappa](map-pin.md)

Xamarin.Forms. Maps fornisce un'astrazione multipiattaforma per la visualizzazione di mappe che usano le API della mappa nativa su ogni piattaforma, per offrire un'esperienza di mappa rapida e familiare per gli utenti. In questo argomento viene illustrato come creare un renderer personalizzato per il `Map` controllo, consentendo agli sviluppatori di eseguire l'override del rendering nativo predefinito con una personalizzazione specifica della piattaforma.

## <a name="customizing-a-listview"></a>[Personalizzazione di un oggetto ListView](listview.md)

Un oggetto Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) è una vista in cui viene visualizzata una raccolta di dati sotto forma di elenco verticale. Questo articolo spiega come creare un renderer personalizzato che incapsula gli elenchi specifici della piattaforma e i layout di cella nativi, consentendo un maggiore controllo sulle prestazioni dei controlli nativi.

## <a name="customizing-a-viewcell"></a>[Personalizzazione di un elemento ViewCell](viewcell.md)

Un oggetto Xamarin.Forms [`ViewCell`](xref:Xamarin.Forms.ViewCell) è una cella che può essere aggiunta a un oggetto [`ListView`](xref:Xamarin.Forms.ListView) o [`TableView`](xref:Xamarin.Forms.TableView) che contiene una visualizzazione definita dallo sviluppatore. Questo articolo illustra come creare un renderer personalizzato per un oggetto `ViewCell` ospitato all'interno di un Xamarin.Forms `ListView` controllo. Questa operazione interrompe la Xamarin.Forms chiamata ripetuta dei calcoli di layout durante lo `ListView` scorrimento.

## <a name="customizing-a-webview"></a>[Personalizzazione di un elemento WebView](hybridwebview.md)

Un Xamarin.Forms [`WebView`](xref:Xamarin.Forms.WebView) è una vista che Visualizza il contenuto Web e HTML nell'app. Questo articolo illustra come creare un renderer personalizzato che estende `WebView` per consentire la chiamata del codice C# da JavaScript.

## <a name="implementing-a-view"></a>[Implementazione di una vista](view.md)

Xamarin.Formsi controlli dell'interfaccia utente personalizzati devono derivare dalla [`View`](xref:Xamarin.Forms.View) classe, utilizzata per posizionare i layout e i controlli sullo schermo. Questo articolo illustra come creare un renderer personalizzato per un Xamarin.Forms controllo personalizzato usato per visualizzare un flusso video di anteprima dalla fotocamera del dispositivo.

## <a name="implementing-a-video-player"></a>[Implementazione di un lettore video](video-player/index.md)

Questo articolo illustra come scrivere i renderer per implementare un controllo `VideoPlayer` personalizzato in grado di riprodurre video dal Web, video incorporati come risorse dell'applicazione o video archiviati nella raccolta di video del dispositivo dell'utente. Vengono illustrate diverse tecniche, tra cui l'implementazione di metodi e le proprietà associabili di sola lettura.
