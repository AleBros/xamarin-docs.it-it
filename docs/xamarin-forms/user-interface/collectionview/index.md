---
title: Xamarin.Forms CollectionView
description: La visualizzazione di raccolta è una visualizzazione flessibile ed efficiente per presentare elenchi di dati usando le specifiche di un layout diverso.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/15/2019
---

# <a name="xamarinforms-collectionview"></a>Xamarin.Forms CollectionView

![Anteprima](~/media/shared/preview.png)

> [!IMPORTANT]
> Il `CollectionView` è attualmente un'anteprima e non dispone di alcune delle proprie funzionalità pianificato. Inoltre, l'API può cambiare quando viene completata l'implementazione.

`CollectionView` è una vista per presentare elenchi di dati usando le specifiche di un layout diverso. L'obiettivo è fornire una più flessibile ed efficace alternativa per [ `ListView` ](xref:Xamarin.Forms.ListView). Mentre il `CollectionView` e `ListView` sono simili, esistono alcune differenze significative:

- `CollectionView` dispone di un modello di layout flessibile che consente di essere visualizzato orizzontalmente o verticalmente, in un elenco o una griglia dati.
- `CollectionView` è presente alcun concetto di celle. Al contrario, un modello di dati consente di definire l'aspetto di ogni elemento di dati nell'elenco.
- `CollectionView` utilizza automaticamente sulla virtualizzazione fornita dai controlli nativi sottostanti.
- `CollectionView` Consente di ridurre la superficie dell'API [ `ListView` ](xref:Xamarin.Forms.ListView). Molte proprietà e gli eventi da `ListView` non sono presenti nel `CollectionView`.
- `CollectionView` non sono inclusi i separatori incorporati.

`CollectionView` è disponibile nelle pre-versioni 4.0 di xamarin. Forms. Tuttavia, è attualmente in fase sperimentale e può essere usato solo aggiungendo la riga seguente di codice per il `AppDelegate` classe in iOS o alle `MainActivity` classe in Android, prima di chiamare `Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!NOTE]
> `CollectionView` è disponibile solo in iOS e Android.

## <a name="populate-collectionview-with-datapopulate-datamd"></a>[Popolare CollectionView con i dati](populate-data.md)

Oggetto `CollectionView` viene popolata con i dati tramite l'impostazione relativa `ItemsSource` a qualsiasi insieme che implementa `IEnumerable`. L'aspetto di ogni elemento dell'elenco può essere definita impostando il `ItemTemplate` proprietà per un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate).

## <a name="specify-collectionview-layoutlayoutmd"></a>[Specificare il layout di visualizzazione di raccolta](layout.md)

Per impostazione predefinita, un `CollectionView` visualizzerà i relativi elementi in un elenco verticale. Tuttavia, è possibile specificare grids ed elenchi orizzontali e verticali.

## <a name="set-collectionview-selection-modeselectionmd"></a>[Impostare la modalità di selezione di visualizzazione di raccolta](selection.md)

Per impostazione predefinita, `CollectionView` la selezione è disabilitata. Tuttavia, può essere abilitata selezione singola e multiple.

## <a name="display-an-emptyview-when-data-is-unavailableemptyviewmd"></a>[Visualizzare un EmptyView quando i dati non sono disponibili](emptyview.md)

In `CollectionView`, è possibile specificare una vista vuota che fornisce feedback all'utente quando non sono disponibili per la visualizzazione dati. La vista vuota può essere una stringa, una vista o più visualizzazioni.

## <a name="scroll-an-item-into-viewscrollingmd"></a>[Scorrere un elemento all'interno della visualizzazione](scrolling.md)

Quando un tessere magnetiche utente per avviare un'operazione di scorrimento, la posizione finale dello scorrimento può essere controllata in modo che gli elementi vengono visualizzati completamente. È inoltre `CollectionView` vengono definiti due `ScrollTo` metodi, che a livello di programmazione scorrere gli elementi all'interno della visualizzazione. Uno degli overload scorre l'elemento in corrispondenza dell'indice specificato all'interno della visualizzazione, mentre l'altro scorre l'elemento specificato all'interno della visualizzazione.
