---
title: Novell. Forms (CollectionView)
description: CollectionView è una visualizzazione flessibile ed efficiente per la presentazione di elenchi di dati con specifiche di layout diverse.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: b3841ed1287c980212ce37078f38f4984393c414
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888598"
---
# <a name="xamarinforms-collectionview"></a>Novell. Forms (CollectionView)

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

## <a name="introductionintroductionmd"></a>[Introduzione](introduction.md)

È [`CollectionView`](xref:Xamarin.Forms.CollectionView) una visualizzazione flessibile ed efficiente per la presentazione di elenchi di dati usando specifiche di layout diverse.

## <a name="datapopulate-datamd"></a>[Dati](populate-data.md)

Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) oggetto viene popolato con i dati [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) impostando la relativa proprietà su `IEnumerable`qualsiasi raccolta che implementa. L'aspetto di ogni elemento nell'elenco può essere definito impostando la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) proprietà su un oggetto. [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)

## <a name="layoutlayoutmd"></a>[Layout](layout.md)

Per impostazione predefinita, [`CollectionView`](xref:Xamarin.Forms.CollectionView) un oggetto visualizzerà gli elementi in un elenco verticale. È tuttavia possibile specificare gli elenchi e le griglie verticali e orizzontali.

## <a name="selectionselectionmd"></a>[Selezione](selection.md)

Per impostazione predefinita [`CollectionView`](xref:Xamarin.Forms.CollectionView) , la selezione è disabilitata. Tuttavia, è possibile abilitare una selezione singola o multipla.

## <a name="empty-viewsemptyviewmd"></a>[Visualizzazioni vuote](emptyview.md)

In [`CollectionView`](xref:Xamarin.Forms.CollectionView)è possibile specificare una vista vuota che fornisce feedback all'utente quando non sono disponibili dati per la visualizzazione. La vista vuota può essere una stringa, una vista o più visualizzazioni.

## <a name="scrollingscrollingmd"></a>[Scorrimento](scrolling.md)

Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. [`CollectionView`](xref:Xamarin.Forms.CollectionView) Definisce inoltre due [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) metodi, che scorrono gli elementi nella visualizzazione a livello di codice. Uno degli overload scorre l'elemento in corrispondenza dell'indice specificato nella visualizzazione, mentre l'altro scorre l'elemento specificato nella visualizzazione.

## <a name="groupinggroupingmd"></a>[Raggruppamento](grouping.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)consente di visualizzare i dati raggruppati correttamente impostando la `true`relativa `IsGrouped` proprietà su.
