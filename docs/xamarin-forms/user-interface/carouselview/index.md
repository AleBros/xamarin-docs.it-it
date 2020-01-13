---
title: Xamarin.Forms CarouselView
description: CarouselView è una visualizzazione per la presentazione dei dati in un layout scorrevole, in cui gli utenti possono scorrere rapidamente una raccolta di elementi.
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
ms.openlocfilehash: 816c1b6e4ab497d0ada0f80fa3ad4800912587c3
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696981"
---
# <a name="xamarinforms-carouselview"></a>Xamarin.Forms CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

## <a name="introductionintroductionmd"></a>[Introduzione](introduction.md)

Il [`CarouselView`](xref:Xamarin.Forms.CarouselView) è una vista per la presentazione dei dati in un layout scorrevole, in cui gli utenti possono scorrere una raccolta di elementi.

## <a name="datapopulate-datamd"></a>[Dati](populate-data.md)

Una [`CarouselView`](xref:Xamarin.Forms.CarouselView) viene popolata con i dati impostando la relativa proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) su qualsiasi raccolta che implementi `IEnumerable`. L'aspetto di ogni elemento può essere definito impostando la proprietà [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) su una [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).

## <a name="layoutlayoutmd"></a>[Layout](layout.md)

Per impostazione predefinita, un [`CarouselView`](xref:Xamarin.Forms.CarouselView) visualizzerà gli elementi in un elenco orizzontale. Tuttavia, ha anche accesso agli stessi layout di CollectionView, incluso un orientamento verticale.

## <a name="interactioninteractionmd"></a>[Interazione](interaction.md)

È possibile accedere all'elemento attualmente visualizzato in un [`CarouselView`](xref:Xamarin.Forms.CarouselView) tramite le proprietà `CurrentItem` e `Position`.

## <a name="empty-viewsemptyviewmd"></a>[Visualizzazioni vuote](emptyview.md)

In [`CarouselView`](xref:Xamarin.Forms.CarouselView), è possibile specificare una vista vuota che fornisce feedback all'utente quando non sono disponibili dati per la visualizzazione. La vista vuota può essere una stringa, una vista o più visualizzazioni.

## <a name="scrollingscrollingmd"></a>[Scorrimento](scrolling.md)

Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. Inoltre, [`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce due metodi [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) , che a livello di codice scorrono gli elementi nella visualizzazione. Uno degli overload scorre l'elemento in corrispondenza dell'indice specificato nella visualizzazione, mentre l'altro scorre l'elemento specificato nella visualizzazione.
