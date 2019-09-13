---
title: Novell. Forms CarouselView
description: CarouselView è una vista per la presentazione di elenchi di dati in un layout simile a un carosello.
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 06d20341053c4f77cb72aac9e1abdc85d9f95fdb
ms.sourcegitcommit: e83035c746f165ee6d03f2e9fd0066ee4f20a9fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70908268"
---
# <a name="xamarinforms-carouselview"></a>Novell. Forms CarouselView

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

> [!IMPORTANT]
> La documentazione di CarouselView è in fase di sviluppo e alcuni collegamenti possono fare riferimento alla documentazione di CollectionView. Nella maggior parte dei casi le informazioni devono essere applicabili a causa della natura della CarouselView basata su CollectionView.

## <a name="introductionintroductionmd"></a>[Introduzione](introduction.md)

È [`CarouselView`](xref:Xamarin.Forms.CarouselView) una visualizzazione per la presentazione dei dati in un layout simile a carosello. La relativa implementazione si basa su quella [`CollectionView`](xref:Xamarin.Forms.CollectionView)di.

## <a name="datacollectionviewpopulate-datamd"></a>[Dati](../collectionview/populate-data.md)

Un [`CarouselView`](xref:Xamarin.Forms.CarouselView) oggetto viene popolato con i dati [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) impostando la relativa proprietà su `IEnumerable`qualsiasi raccolta che implementa. L'aspetto di ogni elemento nell'elenco può essere definito impostando la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) proprietà su un oggetto. [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)

## <a name="layoutlayoutmd"></a>[Layout](layout.md)

Per impostazione predefinita, [`CarouselView`](xref:Xamarin.Forms.CarouselView) un oggetto visualizzerà gli elementi in un elenco orizzontale. Tuttavia, ha anche accesso agli stessi layout di CollectionView, incluso un orientamento verticale.

## <a name="empty-viewscollectionviewemptyviewmd"></a>[Visualizzazioni vuote](../collectionview/emptyview.md)

In [`CarouselView`](xref:Xamarin.Forms.CarouselView)è possibile specificare una vista vuota che fornisce feedback all'utente quando non sono disponibili dati per la visualizzazione. La vista vuota può essere una stringa, una vista o più visualizzazioni.

## <a name="scrollingcollectionviewscrollingmd"></a>[Scorrimento](../collectionview/scrolling.md)

Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. [`CarouselView`](xref:Xamarin.Forms.CarouselView) Definisce inoltre due [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) metodi, che scorrono gli elementi nella visualizzazione a livello di codice. Uno degli overload scorre l'elemento in corrispondenza dell'indice specificato nella visualizzazione, mentre l'altro scorre l'elemento specificato nella visualizzazione.
