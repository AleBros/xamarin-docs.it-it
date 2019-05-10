---
title: Xamarin.Forms CollectionView
description: La visualizzazione di raccolta è una visualizzazione flessibile ed efficiente per presentare elenchi di dati usando le specifiche di un layout diverso.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: a6cb6e695a4c19627b183060a7636320f8083ee2
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65048135"
---
# <a name="xamarinforms-collectionview"></a>Xamarin.Forms CollectionView

![](~/media/shared/preview.png "Questa API è ancora in versione definitiva")

## <a name="introductionintroductionmd"></a>[Introduzione](introduction.md)

Il `CollectionView` è una visualizzazione flessibile ed efficiente per presentare elenchi di dati usando le specifiche di un layout diverso.

## <a name="datapopulate-datamd"></a>[Dati](populate-data.md)

Oggetto `CollectionView` viene popolata con i dati tramite l'impostazione relativa `ItemsSource` a qualsiasi insieme che implementa `IEnumerable`. L'aspetto di ogni elemento dell'elenco può essere definita impostando il `ItemTemplate` proprietà per un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate).

## <a name="layoutlayoutmd"></a>[Layout](layout.md)

Per impostazione predefinita, un `CollectionView` visualizzerà i relativi elementi in un elenco verticale. Tuttavia, è possibile specificare grids ed elenchi orizzontali e verticali.

## <a name="selectionselectionmd"></a>[Selezione](selection.md)

Per impostazione predefinita, `CollectionView` la selezione è disabilitata. Tuttavia, può essere abilitata selezione singola e multiple.

## <a name="empty-viewsemptyviewmd"></a>[Viste vuote](emptyview.md)

In `CollectionView`, è possibile specificare una vista vuota che fornisce feedback all'utente quando non sono disponibili per la visualizzazione dati. La vista vuota può essere una stringa, una vista o più visualizzazioni.

## <a name="scrollingscrollingmd"></a>[Scorrimento](scrolling.md)

Quando un tessere magnetiche utente per avviare un'operazione di scorrimento, la posizione finale dello scorrimento può essere controllata in modo che gli elementi vengono visualizzati completamente. È inoltre `CollectionView` vengono definiti due `ScrollTo` metodi, che a livello di programmazione scorrere gli elementi all'interno della visualizzazione. Uno degli overload scorre l'elemento in corrispondenza dell'indice specificato all'interno della visualizzazione, mentre l'altro scorre l'elemento specificato all'interno della visualizzazione.
