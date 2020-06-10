---
title: " Xamarin.Forms CollectionView" Description: "CollectionView è una visualizzazione flessibile ed efficiente per la presentazione di elenchi di dati con specifiche di layout diverse".
ms. prod: Novell MS. AssetID: 2BC9B223-2D5C-4B09-849C-B9D578954557 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 07/24/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-collectionview"></a>Xamarin.FormsCollectionView

## <a name="introduction"></a>[Introduzione](introduction.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)È una visualizzazione flessibile ed efficiente per la presentazione di elenchi di dati usando specifiche di layout diverse.

## <a name="data"></a>[Dati](populate-data.md)

Un oggetto [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene popolato con i dati impostando la relativa [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) proprietà su qualsiasi raccolta che implementa `IEnumerable` . L'aspetto di ogni elemento nell'elenco può essere definito impostando la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) proprietà su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) .

## <a name="layout"></a>[Layout](layout.md)

Per impostazione predefinita, un oggetto [`CollectionView`](xref:Xamarin.Forms.CollectionView) visualizzerà gli elementi in un elenco verticale. È tuttavia possibile specificare gli elenchi e le griglie verticali e orizzontali.

## <a name="selection"></a>[Selezione](selection.md)

Per impostazione predefinita, [`CollectionView`](xref:Xamarin.Forms.CollectionView) la selezione è disabilitata. Tuttavia, è possibile abilitare una selezione singola o multipla.

## <a name="empty-views"></a>[Visualizzazioni vuote](emptyview.md)

In è [`CollectionView`](xref:Xamarin.Forms.CollectionView) possibile specificare una vista vuota che fornisce feedback all'utente quando non sono disponibili dati per la visualizzazione. La vista vuota può essere una stringa, una vista o più visualizzazioni.

## <a name="scrolling"></a>[Scorrimento](scrolling.md)

Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. [`CollectionView`](xref:Xamarin.Forms.CollectionView)Definisce inoltre due [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) metodi, che scorrono gli elementi nella visualizzazione a livello di codice. Uno degli overload scorre l'elemento in corrispondenza dell'indice specificato nella visualizzazione, mentre l'altro scorre l'elemento specificato nella visualizzazione.

## <a name="grouping"></a>[Raggruppamento](grouping.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)consente di visualizzare i dati raggruppati correttamente impostando la relativa `IsGrouped` proprietà su `true` .
