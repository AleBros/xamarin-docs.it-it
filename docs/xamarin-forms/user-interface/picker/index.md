---
title: Selezione di xamarin. Forms
description: La selezione di xamarin. Forms consente di visualizzare un breve elenco di elementi, da cui l'utente può selezionare un elemento. Questo articolo illustra come usare la classe di selezione per selezionare un elemento di testo da un elenco di dati.
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: c852cd29197b000ed1ff53853d64cfa25fb699e7
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996597"
---
# <a name="xamarinforms-picker"></a>Selezione di xamarin. Forms

_La visualizzazione di selezione è un controllo per la selezione di un elemento di testo da un elenco di dati._

Xamarin. Forms [ `Picker` ](xref:Xamarin.Forms.Picker) Visualizza un breve elenco di elementi, da cui l'utente può selezionare un elemento. `Picker` definisce otto proprietà:

- [`Title`](xref:Xamarin.Forms.Picker.Title) typu `string`, che per impostazione predefinita `null`.
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) typu `IList`, l'elenco di origine degli elementi da visualizzare, che per impostazione predefinita `null`.
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) di tipo `int`, l'indice dell'elemento selezionato, impostazione predefinita è -1.
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) typu `object`, l'elemento selezionato, che per impostazione predefinita `null`.
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) typu [ `Color` ](xref:Xamarin.Forms.Color), il colore utilizzato per visualizzare il testo, che per impostazione predefinita [ `Color.Default` ](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) typu [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes), che per impostazione predefinita [ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) typu `string`, che per impostazione predefinita `null`.
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize) di tipo `double`, che per impostazione predefinita -1,0.

Tutte le proprietà di otto sono supportate da [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetti, che indica che essi possono essere personalizzati con stili e le proprietà possono essere destinazioni di associazioni dati. Il [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) e [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) proprietà hanno una modalità di associazione predefinita [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), il che significa che possono essere destinazioni di associazioni dati in un'applicazione che usa il [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) architettura. Per informazioni sull'impostazione delle proprietà del tipo di carattere, vedere [tipi di carattere](~/xamarin-forms/user-interface/text/fonts.md).

Oggetto [ `Picker` ](xref:Xamarin.Forms.Picker) non Mostra tutti i dati quando viene innanzitutto visualizzato. Al contrario, il valore del relativo [ `Title` ](xref:Xamarin.Forms.Picker.Title) proprietà viene visualizzata come segnaposto nelle piattaforme Android e iOS:

[![](images/picker-initial.png "Iniziale selettore Display")](images/picker-initial-large.png#lightbox "iniziale Selezione visualizzazione")

Quando la [ `Picker` ](xref:Xamarin.Forms.Picker) riceve lo stato attivo, i dati viene visualizzato e l'utente può selezionare un elemento:

[![](images/picker-selection.png "Selezione di un elemento di selezione")](images/picker-selection-large.png#lightbox "selezione selezionando un elemento")

Il [ `Picker` ](xref:Xamarin.Forms.Picker) viene attivato un [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento quando l'utente seleziona un elemento. Dopo la selezione, l'elemento selezionato viene visualizzato per il `Picker`:

![](images/picker-after-selection.png "Selezione dopo la selezione")

Esistono due tecniche per l'inserimento in una [ `Picker` ](xref:Xamarin.Forms.Picker) con i dati:

- Impostando il [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) proprietà ai dati da visualizzare. Si tratta della tecnica consigliata, che è stata introdotta in xamarin. Forms 2.3.4. Per altre informazioni, vedere [impostazione proprietà ItemsSource del controllo di selezione](populating-itemssource.md).
- Aggiunta di dati da visualizzare per il [ `Items` ](xref:Xamarin.Forms.Picker.Items) raccolta. Questa tecnica era il processo originale per l'inserimento in una [ `Picker` ](xref:Xamarin.Forms.Picker) con i dati. Per altre informazioni, vedere [aggiunta di dati alla raccolta di elementi del controllo di selezione](populating-items.md).

## <a name="related-links"></a>Collegamenti correlati

- [Selezione](xref:Xamarin.Forms.Picker)
