---
title: Xamarin.FormsListView
description: Questa guida introduce Xamarin.Forms ListView, che può essere usato per presentare i dati negli elenchi interattivi.
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a1ff8dd5c8a8a4051cea8ce4b288c42bdbaa8d31
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139906"
---
# <a name="xamarinforms-listview"></a>Xamarin.FormsListView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)

[`ListView`](xref:Xamarin.Forms.ListView)è una visualizzazione per la presentazione di elenchi di dati, in particolare gli elenchi lunghi che richiedono lo scorrimento.

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)è una vista per la presentazione di elenchi di dati con specifiche di layout diverse. Mira a offrire un'alternativa più flessibile ed efficiente a [`ListView`](xref:Xamarin.Forms.ListView) . Per ulteriori informazioni, vedere [ Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="use-cases"></a>Casi d'uso

Un `ListView` controllo può essere usato in qualsiasi situazione in cui si visualizzino elenchi di dati scorrevoli. La `ListView` classe supporta azioni di contesto e data binding.

Il `ListView` controllo non deve essere confuso con il [`TableView`](~/xamarin-forms/user-interface/tableview.md) controllo. Il `TableView` controllo è un'opzione migliore quando si dispone di un elenco non associato di opzioni o dati perché consente di specificare opzioni predefinite in XAML. Ad esempio, l'app Impostazioni iOS, che dispone di un set di opzioni principalmente predefinito, è più adatta per l'uso di un oggetto `TableView` rispetto a `ListView` .

La `ListView` classe non supporta la definizione di elementi elenco in XAML, è necessario usare la `ItemsSource` proprietà o data binding con un oggetto `ItemTemplate` per definire gli elementi nell'elenco.

`ListView`È ideale per le raccolte costituite da un singolo tipo di dati. Questo requisito è dovuto al fatto che è possibile utilizzare un solo tipo di cella per ogni riga nell'elenco. Il `TableView` controllo può supportare più tipi di celle, quindi è un'opzione migliore quando è necessario visualizzare più tipi di dati.

Per ulteriori informazioni sull'associazione di dati a un' `ListView` istanza di, vedere [ListView Data Sources](~/xamarin-forms/user-interface/listview/data-and-databinding.md).

## <a name="components"></a>Componenti

Il `ListView` controllo dispone di un numero di componenti disponibili per esercitare le funzionalità native di ogni piattaforma. Questi componenti sono definiti nelle sezioni seguenti.

### <a name="headers-and-footers"></a>[Intestazioni e piè di pagina](customizing-list-appearance.md#headers-and-footers)

I componenti dell'intestazione e del piè di pagina vengono visualizzati all'inizio e alla fine di un elenco, separati dai dati dell'elenco. Le intestazioni e i piè di pagina possono essere associati a un'origine dati separata dall'origine dati di ListView.

### <a name="groups"></a>[Gruppi](customizing-list-appearance.md#grouping)

I dati in un oggetto `ListView` possono essere raggruppati per semplificare la navigazione. I gruppi sono in genere associati ai dati. La schermata seguente mostra un `ListView` con i dati raggruppati:

[!["Raggruppare i dati in un controllo ListView"](images/grouping-depth-cropped.png)](images/grouping-depth.png#lightbox "Dati raggruppati in un controllo ListView")

### <a name="cells"></a>[Celle](customizing-cell-appearance.md)

Gli elementi di dati in un oggetto `ListView` sono detti celle. Ogni cella corrisponde a una riga di dati. È possibile scegliere tra celle predefinite oppure definire una cella personalizzata. Sia le celle predefinite che quelle personalizzate possono essere usate/definite in XAML o nel codice.

- Le [celle predefinite](customizing-cell-appearance.md#built-in-cells), ad esempio `TextCell` e `ImageCell` , corrispondono ai controlli nativi e sono particolarmente performanti.
  - Un oggetto [`TextCell`](customizing-cell-appearance.md#textcell) Visualizza una stringa di testo, facoltativamente con il testo del dettaglio. Il testo del dettaglio viene sottoposto a rendering come seconda riga in un tipo di carattere più piccolo con un colore accentato.
  - Un oggetto [`ImageCell`](customizing-cell-appearance.md#imagecell) Visualizza un'immagine con testo. Viene visualizzato come `TextCell` con un'immagine a sinistra.
- [Le celle personalizzate](customizing-cell-appearance.md#custom-cells) vengono usate per presentare dati complessi. Ad esempio, è possibile usare una cella personalizzata per presentare un elenco di canzoni che includono album e artista.

La schermata seguente mostra un `ListView` elemento con ImageCell:

[!["ImageCell elementi in un controllo ListView"](images/image-cell-default-cropped.png)](images/image-cell-default.png#lightbox "ImageCell elementi in un controllo ListView")

Per ulteriori informazioni sulla personalizzazione delle celle in un `ListView` , vedere [personalizzazione dell'aspetto delle celle di ListView](customizing-cell-appearance.md).

## <a name="functionality"></a>Funzionalità

La `ListView` classe supporta diversi stili di interazione.

- Il [pull a Refresh](interactivity.md#pull-to-refresh) consente all'utente di eseguire il pull `ListView` per aggiornare il contenuto.
- Le [azioni di contesto](interactivity.md#context-actions) consentono allo sviluppatore di specificare azioni personalizzate sui singoli elementi dell'elenco. Ad esempio, è possibile implementare Swipe-to-Action in iOS o azioni di tipo Long-tap in Android.
- [Selezione](interactivity.md#selection-and-taps) consente allo sviluppatore di aggiungere la funzionalità agli eventi di selezione e deselezione negli elementi dell'elenco.

La schermata seguente mostra un oggetto `ListView` con azioni di contesto:

[!["Azioni di contesto in un controllo ListView"](images/context-default-cropped.png)](images/context-default.png#lightbox "Azioni di contesto in un controllo ListView")

Per ulteriori informazioni sulle funzionalità di interattività di `ListView` , vedere [azioni & interattività con ListView](interactivity.md).

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di ListView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)
- [Binding bidirezionale (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
- [Celle predefinite (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Celle personalizzate (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Raggruppamento (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [Visualizzazione renderer personalizzata (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative/)
- [Interattività ListView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
