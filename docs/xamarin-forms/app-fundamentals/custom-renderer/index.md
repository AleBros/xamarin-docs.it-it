---
title: Renderer personalizzato
description: Vengono visualizzate le interfacce utente xamarin. Forms usando i controlli nativi della piattaforma di destinazione, consentendo alle applicazioni di xamarin. Forms mantenere l'aspetto appropriato per ogni piattaforma. Renderer personalizzati consentono agli sviluppatori di eseguire l'override di questo processo per personalizzare l'aspetto e il comportamento dei controlli di xamarin. Forms in ciascuna piattaforma.
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 6a2ee4b09426e6b4ff6dac7e1fd5221fc5b6d750
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="custom-renderers"></a>Renderer personalizzato

_Vengono visualizzate le interfacce utente xamarin. Forms usando i controlli nativi della piattaforma di destinazione, consentendo alle applicazioni di xamarin. Forms mantenere l'aspetto appropriato per ogni piattaforma. Renderer personalizzati consentono agli sviluppatori di eseguire l'override di questo processo per personalizzare l'aspetto e il comportamento dei controlli di xamarin. Forms in ciascuna piattaforma._

## <a name="introduction-to-custom-renderersintroductionmd"></a>[Introduzione a un renderer personalizzato](introduction.md)

Renderer personalizzati forniscono un approccio potente per la personalizzazione dell'aspetto e comportamento dei controlli di xamarin. Forms. Possono essere utilizzati per lo stile piccole modifiche o sofisticati layout specifici della piattaforma e personalizzazione del comportamento. In questo articolo viene fornita un'introduzione al renderer personalizzati e viene descritto il processo per la creazione di un renderer personalizzato.

## <a name="renderer-base-classes-and-native-controlsrenderersmd"></a>[Classi base renderer e i controlli nativi](renderers.md)

Ogni controllo in xamarin. Forms è un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Questo articolo vengono elencati le classi di controlli nativi che implementano ogni pagina xamarin. Forms, layout, visualizzazione e cella renderer.

## <a name="customizing-an-entryentrymd"></a>[Personalizzazione di una voce](entry.md)

Di xamarin. Forms [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllo consente una singola riga di testo da modificare. In questo articolo viene illustrato come creare un renderer personalizzato per il `Entry` (controllo), consentendo agli sviluppatori di eseguire l'override per il rendering predefinito nativo con le personalizzazioni specifiche della piattaforma.

## <a name="customizing-a-contentpagecontentpagemd"></a>[Personalizzazione di un elemento ContentPage](contentpage.md)

Oggetto [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) è un elemento visivo che consente di visualizzare una singola visualizzazione e occupa la maggior parte della schermata. In questo articolo viene illustrato come creare un renderer personalizzato per il `ContentPage` pagina, consentendo agli sviluppatori di eseguire l'override per il rendering predefinito nativo con le personalizzazioni specifiche della piattaforma.

## <a name="customizing-a-mapmapindexmd"></a>[Personalizzazione di una mappa](map/index.md)

Xamarin.Forms.Maps fornisce un'astrazione multipiattaforma per la visualizzazione di mappe di utilizzano la mappa nativa le API in ogni piattaforma, per fornire una mappa veloce e familiare esperienza per gli utenti. In questo argomento viene illustrato come creare un renderer personalizzato per il `Map` (controllo), consentendo agli sviluppatori di eseguire l'override per il rendering predefinito nativo con le personalizzazioni specifiche della piattaforma.

## <a name="customizing-a-listviewlistviewmd"></a>[Personalizzazione di un elemento ListView](listview.md)

Un xamarin. Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) è una vista che visualizza una raccolta di dati come un elenco verticale. In questo articolo viene illustrato come creare un renderer personalizzato che incapsula i controlli elenco specifico della piattaforma e il layout di cella native, consentendo maggiore controllo sulle prestazioni del controllo elenco nativo.

## <a name="customizing-a-viewcellviewcellmd"></a>[Personalizzazione di un elemento ViewCell](viewcell.md)

Un xamarin. Forms [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) è una cella che può essere aggiunti a un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) o [ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/), che contiene una visualizzazione definita dallo sviluppatore. In questo articolo viene illustrato come creare un renderer personalizzato per un `ViewCell` che è ospitato all'interno di un xamarin. Forms `ListView` controllo. In tal modo i calcoli di layout di xamarin. Forms essere chiamato più volte durante `ListView` lo scorrimento.

## <a name="implementing-a-viewviewmd"></a>[Implementazione di un elemento View](view.md)

Controlli di interfacce utente personalizzate di xamarin. Forms devono derivare dal [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) (classe), che viene utilizzato per posizionare i controlli nella schermata e layout. In questo articolo viene illustrato come creare un renderer personalizzato per un controllo personalizzato di xamarin. Forms che consente di visualizzare un flusso video di anteprima da fotocamera del dispositivo.

## <a name="implementing-a-hybridwebviewhybridwebviewmd"></a>[Implementazione di un elemento HybridWebView](hybridwebview.md)

In questo articolo viene illustrato come creare un renderer personalizzato per un `HybridWebView` controllo personalizzato che viene descritto come migliorare i controlli specifici della piattaforma web per consentire al codice c# di essere richiamato da JavaScript.

## <a name="implementing-a-video-playervideo-playerindexmd"></a>[Implementazione di un lettore video](video-player/index.md)

In questo articolo viene illustrato come scrivere i renderer per implementare un oggetto personalizzato `VideoPlayer` controllo in grado di riprodurre video dal web, video incorporati come risorse dell'applicazione o i video archiviati nella libreria di video sul dispositivo dell'utente. Vengono illustrate diverse tecniche, inclusa l'implementazione di metodi e le proprietà associabili di sola lettura. 


## <a name="related-links"></a>Collegamenti correlati

- [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Renderer personalizzato (Video di University Xamarin)](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
- [Esempio di renderer personalizzato (Video di University Xamarin)](http://bit.ly/xf-customrenderer)
