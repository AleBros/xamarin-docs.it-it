---
title: Novell. Forms (CollectionView)
description: CollectionView è una visualizzazione flessibile ed efficiente per la presentazione di elenchi di dati con specifiche di layout diverse.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 8050d952556ce0b55a7ce72bc5f25de903fee6e5
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696996"
---
# <a name="xamarinforms-collectionview"></a>Novell. Forms (CollectionView)

## <a name="introductionintroductionmd"></a>[Introduzione](introduction.md)

Il [`CollectionView`](xref:Xamarin.Forms.CollectionView) è una visualizzazione flessibile ed efficiente per presentare elenchi di dati usando specifiche di layout diverse.

## <a name="datapopulate-datamd"></a>[Dati](populate-data.md)

Una [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene popolata con i dati impostando la relativa proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) su qualsiasi raccolta che implementi `IEnumerable`. L'aspetto di ogni elemento nell'elenco può essere definito impostando la proprietà [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) su una [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).

## <a name="layoutlayoutmd"></a>[Layout](layout.md)

Per impostazione predefinita, un [`CollectionView`](xref:Xamarin.Forms.CollectionView) visualizzerà gli elementi in un elenco verticale. È tuttavia possibile specificare gli elenchi e le griglie verticali e orizzontali.

## <a name="selectionselectionmd"></a>[Selezione](selection.md)

Per impostazione predefinita, [`CollectionView`](xref:Xamarin.Forms.CollectionView) selezione è disabilitata. Tuttavia, è possibile abilitare una selezione singola o multipla.

## <a name="empty-viewsemptyviewmd"></a>[Visualizzazioni vuote](emptyview.md)

In [`CollectionView`](xref:Xamarin.Forms.CollectionView), è possibile specificare una vista vuota che fornisce feedback all'utente quando non sono disponibili dati per la visualizzazione. La vista vuota può essere una stringa, una vista o più visualizzazioni.

## <a name="scrollingscrollingmd"></a>[Scorrimento](scrolling.md)

Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. Inoltre, [`CollectionView`](xref:Xamarin.Forms.CollectionView) definisce due metodi [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) , che a livello di codice scorrono gli elementi nella visualizzazione. Uno degli overload scorre l'elemento in corrispondenza dell'indice specificato nella visualizzazione, mentre l'altro scorre l'elemento specificato nella visualizzazione.

## <a name="groupinggroupingmd"></a>[Raggruppamento](grouping.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) possibile visualizzare i dati raggruppati correttamente impostando la relativa proprietà `IsGrouped` su `true`.
