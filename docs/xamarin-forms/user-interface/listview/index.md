---
title: Xamarin.Forms ListView
description: Questa guida presenta il ListView Xamarin.Forms, che può essere usato per presentare i dati negli elenchi interattivi.
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/04/2019
ms.openlocfilehash: 9d7dd98b98c7722d6fcdcb49c10e326732fa9f0b
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "70997989"
---
# <a name="xamarinforms-listview"></a>Xamarin.Forms ListView

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)

[`ListView`](xref:Xamarin.Forms.ListView)è una visualizzazione per la presentazione di elenchi di dati, in particolare gli elenchi lunghi che richiedono lo scorrimento.

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) è una vista per presentare elenchi di dati usando specifiche di layout diverse. Mira a offrire un'alternativa più flessibile ed efficiente a [`ListView`](xref:Xamarin.Forms.ListView). Per altre informazioni, vedere [CollectionView di Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="use-cases"></a>Casi d'uso

Un `ListView` controllo può essere usato in qualsiasi situazione in cui si visualizzino elenchi di dati scorrevoli. La `ListView` classe supporta azioni di contesto e data binding.

Il `ListView` controllo non deve essere confuso con [`TableView`](~/xamarin-forms/user-interface/tableview.md) il controllo. Il `TableView` controllo è un'opzione migliore quando si dispone di un elenco non associato di opzioni o dati perché consente di specificare opzioni predefinite in XAML. Ad esempio, l'app Impostazioni iOS, che dispone di un set di opzioni principalmente predefinito, è più adatta per l'uso `TableView` di un `ListView`oggetto rispetto a.

La `ListView` classe non supporta la definizione di elementi elenco in XAML, è necessario `ItemsSource` usare la proprietà o data binding `ItemTemplate` con un oggetto per definire gli elementi nell'elenco.

`ListView` È ideale per le raccolte costituite da un singolo tipo di dati. Questo requisito è dovuto al fatto che è possibile utilizzare un solo tipo di cella per ogni riga nell'elenco. Il `TableView` controllo può supportare più tipi di celle, quindi è un'opzione migliore quando è necessario visualizzare più tipi di dati.

Per ulteriori informazioni sull'associazione di dati a `ListView` un'istanza di, vedere [ListView Data Sources](~/xamarin-forms/user-interface/listview/data-and-databinding.md).

## <a name="components"></a>Componenti

Il `ListView` controllo dispone di un numero di componenti disponibili per esercitare le funzionalità native di ogni piattaforma. Questi componenti sono definiti nelle sezioni seguenti.

### <a name="headers-and-footerscustomizing-list-appearancemdheaders-and-footers"></a>[Intestazioni e piè di pagina](customizing-list-appearance.md#headers-and-footers)

I componenti dell'intestazione e del piè di pagina vengono visualizzati all'inizio e alla fine di un elenco, separati dai dati dell'elenco. Le intestazioni e i piè di pagina possono essere associati a un'origine dati separata dall'origine dati di ListView.

### <a name="groupscustomizing-list-appearancemdgrouping"></a>[Gruppi](customizing-list-appearance.md#grouping)

I dati in `ListView` un oggetto possono essere raggruppati per semplificare la navigazione. I gruppi sono in genere associati ai dati. La schermata seguente mostra un `ListView` con i dati raggruppati:

["Raggruppare dati in un controllo ListView" dati raggruppati in un controllo ListView ![](images/grouping-depth-cropped.png)](images/grouping-depth.png#lightbox "")

### <a name="cellscustomizing-cell-appearancemd"></a>[Celle](customizing-cell-appearance.md)

Gli elementi di dati `ListView` in un oggetto sono detti celle. Ogni cella corrisponde a una riga di dati. Sono presenti celle predefinite per scegliere da, o è possibile definire il proprio cella personalizzata. Le celle incorporate e personalizzate possono essere usati/definito in XAML o codice.

- Le [celle predefinite](customizing-cell-appearance.md#built-in-cells), ad esempio `TextCell` e `ImageCell`, corrispondono ai controlli nativi e sono particolarmente performanti.
  - Un [`TextCell`](customizing-cell-appearance.md#textcell) oggetto Visualizza una stringa di testo, facoltativamente con il testo del dettaglio. Testo dei dettagli viene visualizzato come una seconda riga in un carattere più piccolo con un colore.
  - Un [`ImageCell`](customizing-cell-appearance.md#imagecell) oggetto Visualizza un'immagine con testo. Viene visualizzato come `TextCell` con un'immagine a sinistra.
- [Le celle personalizzate](customizing-cell-appearance.md#custom-cells) vengono usate per presentare dati complessi. Ad esempio, è possibile usare una cella personalizzata per presentare un elenco di canzoni che includono album e artista.

La schermata seguente mostra un `ListView` elemento con ImageCell:

["ImageCell elementi in un controllo ListView" elementi ImageCell in un controllo ListView ![](images/image-cell-default-cropped.png)](images/image-cell-default.png#lightbox "")

Per ulteriori informazioni sulla personalizzazione delle celle in un `ListView`, vedere [personalizzazione dell'aspetto delle celle di ListView](customizing-cell-appearance.md).

## <a name="functionality"></a>Funzionalità

La `ListView` classe supporta diversi stili di interazione.

- Il [pull a Refresh](interactivity.md#pull-to-refresh) consente all'utente di eseguire il `ListView` pull per aggiornare il contenuto.
- Le [azioni di contesto](interactivity.md#context-actions) consentono allo sviluppatore di specificare azioni personalizzate sui singoli elementi dell'elenco. Ad esempio, è possibile implementare scorrere all'azione in iOS o azioni in Android toccare a lungo.
- [Selezione](interactivity.md#selection-and-taps) consente allo sviluppatore di aggiungere la funzionalità agli eventi di selezione e deselezione negli elementi dell'elenco.

La schermata seguente mostra un `ListView` oggetto con azioni di contesto:

Azioni di contesto ["azioni di contesto in un controllo ListView" in un controllo ListView ![](images/context-default-cropped.png)](images/context-default.png#lightbox "")

Per ulteriori informazioni sulle funzionalità di interattività di `ListView`, vedere [azioni & interattività con ListView](interactivity.md).

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo con ListView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)
- [Associazione bidirezionale (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
- [Compilato In celle (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Celle personalizzate (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Raggruppamento (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [Visualizzazione di Renderer personalizzato (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative/)
- [Interattività ListView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
