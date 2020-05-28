---
title: Xamarin.FormsCarouselView
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 891f1ff8ad8f254ff3a2805d08d0f7e115bb0fff
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137371"
---
# <a name="xamarinforms-carouselview"></a>Xamarin.FormsCarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

## <a name="introduction"></a>[Introduzione](introduction.md)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)È una visualizzazione per la presentazione dei dati in un layout scorrevole, in cui gli utenti possono scorrere rapidamente una raccolta di elementi.

## <a name="data"></a>[Dati](populate-data.md)

Un oggetto [`CarouselView`](xref:Xamarin.Forms.CarouselView) viene popolato con i dati impostando la relativa [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) proprietà su qualsiasi raccolta che implementa `IEnumerable` . L'aspetto di ogni elemento può essere definito impostando la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) proprietà su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) .

## <a name="layout"></a>[Layout](layout.md)

Per impostazione predefinita, un oggetto [`CarouselView`](xref:Xamarin.Forms.CarouselView) visualizzerà gli elementi in un elenco orizzontale. Tuttavia, ha anche accesso agli stessi layout di CollectionView, incluso un orientamento verticale.

## <a name="interaction"></a>[Interazione](interaction.md)

È possibile accedere all'elemento attualmente visualizzato in un oggetto [`CarouselView`](xref:Xamarin.Forms.CarouselView) tramite `CurrentItem` le `Position` proprietà e.

## <a name="empty-views"></a>[Visualizzazioni vuote](emptyview.md)

In è [`CarouselView`](xref:Xamarin.Forms.CarouselView) possibile specificare una vista vuota che fornisce feedback all'utente quando non sono disponibili dati per la visualizzazione. La vista vuota può essere una stringa, una vista o più visualizzazioni.

## <a name="scrolling"></a>[Scorrimento](scrolling.md)

Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. [`CarouselView`](xref:Xamarin.Forms.CarouselView)Definisce inoltre due [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) metodi, che scorrono gli elementi nella visualizzazione a livello di codice. Uno degli overload scorre l'elemento in corrispondenza dell'indice specificato nella visualizzazione, mentre l'altro scorre l'elemento specificato nella visualizzazione.
