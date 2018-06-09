---
title: Selezione di xamarin. Forms
description: La selezione di xamarin. Forms consente di visualizzare un breve elenco di elementi, da cui l'utente può selezionare un elemento. In questo articolo viene illustrato come utilizzare la classe di selezione per selezionare un elemento di testo da un elenco di dati.
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: 82ae36a7be139e2a93d0e5c43c4bad355c49f217
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245039"
---
# <a name="xamarinforms-picker"></a>Selezione di xamarin. Forms

_La visualizzazione di selezione è un controllo per la selezione di un elemento di testo da un elenco di dati._

Il xamarin. Forms [ `Picker` ](xref:Xamarin.Forms.Picker) Visualizza un breve elenco di elementi, da cui l'utente può selezionare un elemento. `Picker` definisce otto proprietà:

- [`Title`](xref:Xamarin.Forms.Picker.Title) di tipo `string`, che per impostazione predefinita `null`.
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) di tipo `IList`, l'elenco di origine degli elementi da visualizzare, che per impostazione predefinita `null`.
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) di tipo `int`, l'indice dell'elemento selezionato, che per impostazione predefinita su -1.
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) di tipo `object`, l'elemento selezionato, che per impostazione predefinita `null`.
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) di tipo [ `Color` ](xref:Xamarin.Forms.Color), il colore usato per visualizzare il testo, che per impostazione predefinita [ `Color.Default` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/).
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) di tipo [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes), che per impostazione predefinita [ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) di tipo `string`, che per impostazione predefinita `null`.
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize) di tipo `double`, che per impostazione predefinita -1,0.

Sono supportate da tutte le otto proprietà [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetti, che significa che si possono applicare stili e le proprietà possono essere destinazioni dei data binding. Il [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) e [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) proprietà hanno una modalità di associazione predefinito di [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), il che significa che possono essere destinazioni delle associazioni dati in un'applicazione che utilizza il [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) architettura. Per informazioni sull'impostazione delle proprietà del tipo di carattere, vedere [tipi di carattere](~/xamarin-forms/user-interface/text/fonts.md).

Un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) non Mostra tutti i dati quando viene innanzitutto visualizzato. Al contrario, il valore della relativa [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Title/) proprietà viene visualizzata come segnaposto nelle piattaforme Android e iOS:

[![](images/picker-initial.png "Selezione visualizzazione iniziale")](images/picker-initial-large.png#lightbox "selezione visualizzazione iniziale")

Quando il [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) riceve lo stato attivo, i dati viene visualizzato e l'utente può selezionare un elemento:

[![](images/picker-selection.png "Selezione di un elemento di selezione")](images/picker-selection-large.png#lightbox "selezione selezione di un elemento")

Il [ `Picker` ](xref:Xamarin.Forms.Picker) viene attivato un [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento quando l'utente seleziona un elemento. Dopo la selezione, viene visualizzato l'elemento selezionato per il `Picker`:

![](images/picker-after-selection.png "Selezione dopo la selezione")

Sono disponibili due tecniche per la compilazione di un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) con i dati:

- L'impostazione di [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) proprietà ai dati da visualizzare. Questa è la tecnica consigliata, che è stata introdotta in xamarin. Forms 2.3.4. Per ulteriori informazioni, vedere [impostando proprietà ItemsSource un prelievo](populating-itemssource.md).
- Aggiunta di dati da visualizzare per il [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) insieme. Questa tecnica è il processo originale per la compilazione di un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) con i dati. Per ulteriori informazioni, vedere [dati aggiunta alla raccolta di elementi di un controllo di selezione](populating-items.md).

## <a name="related-links"></a>Collegamenti correlati

- [Selezione](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
