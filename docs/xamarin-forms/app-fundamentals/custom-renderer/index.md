---
title: Renderer personalizzati di xamarin. Forms
description: Renderer personalizzati consentono agli sviluppatori di eseguire l'override del rendering dei controlli nativi in ciascuna piattaforma, per personalizzare l'aspetto e comportamento dei controlli di xamarin. Forms.
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: c7ae25688b2f8635a9a89318e0b307e58add7a5a
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998744"
---
# <a name="xamarinforms-custom-renderers"></a>Renderer personalizzati di xamarin. Forms

_Vengono visualizzate le interfacce utente di xamarin. Forms con i controlli nativi della piattaforma di destinazione, consentendo alle applicazioni xamarin. Forms mantenere l'aspetto appropriato per ogni piattaforma. Renderer personalizzati consentono agli sviluppatori di eseguire l'override di questo processo per personalizzare l'aspetto e comportamento dei controlli di xamarin. Forms in ogni piattaforma._

## <a name="introduction-to-custom-renderersintroductionmd"></a>[Introduzione al renderer personalizzati](introduction.md)

Renderer personalizzati offrono un approccio potente per la personalizzazione dell'aspetto e comportamento dei controlli di xamarin. Forms. Possono essere utilizzati per lo stile piccole modifiche o layout specifici della piattaforma sofisticati e personalizzazione del comportamento. Questo articolo fornisce un'introduzione ai renderer personalizzati e illustra la procedura per creare un renderer personalizzato.

## <a name="renderer-base-classes-and-native-controlsrenderersmd"></a>[Classi di base di renderer e controlli nativi](renderers.md)

Ogni controllo di xamarin. Forms dispone di un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Questo articolo elenca le classi di controllo nativo che implementano ogni pagina xamarin. Forms, layout, visualizzazione e cella e il renderer.

## <a name="customizing-an-entryentrymd"></a>[Personalizzazione di una voce](entry.md)

Xamarin. Forms [ `Entry` ](xref:Xamarin.Forms.Entry) controllo consente una singola riga di testo che deve essere modificato. Questo articolo illustra come creare un renderer personalizzato per il `Entry` (controllo), consentendo agli sviluppatori di eseguire l'override del rendering nativo predefinito con le personalizzazioni specifiche della piattaforma.

## <a name="customizing-a-contentpagecontentpagemd"></a>[Personalizzazione di un elemento ContentPage](contentpage.md)

Oggetto [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) è un elemento visivo che consente di visualizzare una singola visualizzazione e occupa la maggior parte della schermata. Questo articolo illustra come creare un renderer personalizzato per il `ContentPage` pagina, consentendo agli sviluppatori di eseguire l'override del rendering nativo predefinito con le personalizzazioni specifiche della piattaforma.

## <a name="customizing-a-mapmapindexmd"></a>[Personalizzazione di una mappa](map/index.md)

Verifica fornisce un'astrazione di multi-piattaforma per la visualizzazione delle mappe che utilizzano la mappa nativa le API per ogni piattaforma per fornire una mappa veloce e familiare esperienza per gli utenti. In questo argomento viene illustrato come creare renderer personalizzati per il `Map` (controllo), consentendo agli sviluppatori di eseguire l'override del rendering nativo predefinito con le personalizzazioni specifiche della piattaforma.

## <a name="customizing-a-listviewlistviewmd"></a>[Personalizzazione di un elemento ListView](listview.md)

Un xamarin. Forms [ `ListView` ](xref:Xamarin.Forms.ListView) è una vista che consente di visualizzare una raccolta di dati sotto forma di elenco verticale. Questo articolo illustra come creare un renderer personalizzato che incapsula i controlli elenco di specifiche della piattaforma e i layout delle celle native, consentendo maggiore controllo sulle prestazioni del controllo elenco nativo.

## <a name="customizing-a-viewcellviewcellmd"></a>[Personalizzazione di un elemento ViewCell](viewcell.md)

Un xamarin. Forms [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) è una cella che può essere aggiunti a un [ `ListView` ](xref:Xamarin.Forms.ListView) oppure [ `TableView` ](xref:Xamarin.Forms.TableView), che contiene una visualizzazione definita dallo sviluppatore. Questo articolo illustra come creare un renderer personalizzato per un `ViewCell` ospitato all'interno di un xamarin. Forms `ListView` controllo. Questo arresta i calcoli di layout di xamarin. Forms venga chiamato più volte durante `ListView` lo scorrimento.

## <a name="implementing-a-viewviewmd"></a>[Implementazione di un elemento View](view.md)

I controlli di interfacce utente personalizzato di xamarin. Forms devono derivare dal [ `View` ](xref:Xamarin.Forms.View) classe, che viene usato per posizionare i layout e controlli sullo schermo. Questo articolo illustra come creare un renderer personalizzato per un controllo personalizzato di xamarin. Forms che consente di visualizzare un flusso video di anteprima dalla fotocamera del dispositivo.

## <a name="implementing-a-hybridwebviewhybridwebviewmd"></a>[Implementazione di un elemento HybridWebView](hybridwebview.md)

Questo articolo illustra come creare un renderer personalizzato per un `HybridWebView` controllo personalizzato, che illustra come migliorare i controlli specifici della piattaforma web per consentire al codice c# di essere richiamato da JavaScript.

## <a name="implementing-a-video-playervideo-playerindexmd"></a>[Implementazione di un lettore video](video-player/index.md)

Questo articolo illustra come scrivere i renderer per implementare una classe personalizzata `VideoPlayer` controllo in grado di riprodurre video dal web, i video incorporati come risorse dell'applicazione o video archiviati nel Catalogo video sul dispositivo dell'utente. Vengono illustrate diverse tecniche, inclusa l'implementazione di metodi e proprietà associabile in sola lettura.


## <a name="related-links"></a>Collegamenti correlati

- [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Renderer personalizzati (Video di Xamarin University)](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
- [Esempio renderer personalizzati (Video di Xamarin University)](http://bit.ly/xf-customrenderer)
