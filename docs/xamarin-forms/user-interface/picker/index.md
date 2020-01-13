---
title: Selezione Xamarin.Forms
description: Il selettore Xamarin.Forms Visualizza un breve elenco di elementi da cui l'utente può selezionare un elemento. Questo articolo illustra come usare la classe Picker per selezionare un elemento di testo da un elenco di dati.
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: c8246f8316a2a579bc890935dc4f9beecccb8dcc
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695994"
---
# <a name="xamarinforms-picker"></a>Selezione Xamarin.Forms

_La visualizzazione selezione è un controllo per la selezione di un elemento di testo da un elenco di dati._

Il [`Picker`](xref:Xamarin.Forms.Picker) Xamarin.Forms Visualizza un breve elenco di elementi da cui l'utente può selezionare un elemento. `Picker` definisce le proprietà seguenti:

- [`Title`](xref:Xamarin.Forms.Picker.Title) di tipo `string`, il cui valore predefinito è `null`.
- `TitleColor` di tipo [`Color`](xref:Xamarin.Forms.Color), il colore utilizzato per visualizzare il testo `Title`.
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) di tipo `IList`, l'elenco di origine di elementi da visualizzare, che per impostazione predefinita è `null`.
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) di tipo `int`, l'indice dell'elemento selezionato, il cui valore predefinito è-1.
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) di tipo `object`, l'elemento selezionato, che per impostazione predefinita è `null`.
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) di tipo [`Color`](xref:Xamarin.Forms.Color), il colore utilizzato per visualizzare il testo, che per impostazione predefinita è [`Color.Default`](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) di tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes), il cui valore predefinito è [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) di tipo `string`, il cui valore predefinito è `null`.
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize) di tipo `double`, il cui valore predefinito è-1,0.
- `CharacterSpacing`, di tipo `double`, è la spaziatura tra i caratteri dell'elemento visualizzato dal `Picker`.

Tutte le proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che è possibile applicare uno stile e le proprietà possono essere destinazioni di associazioni dati. Le proprietà [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) e [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) hanno una modalità di associazione predefinita [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay), il che significa che possono essere destinazioni di associazioni dati in un'applicazione che usa l'architettura [MVC (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) . Per informazioni sull'impostazione delle proprietà dei tipi di carattere, vedere [caratteri](~/xamarin-forms/user-interface/text/fonts.md).

Un [`Picker`](xref:Xamarin.Forms.Picker) non Visualizza i dati quando viene visualizzato per la prima volta. Il valore della proprietà [`Title`](xref:Xamarin.Forms.Picker.Title) viene invece visualizzato come segnaposto nelle piattaforme iOS e Android:

[![](images/picker-initial.png "Initial Picker Display")](images/picker-initial-large.png#lightbox "Initial Picker Display")

Quando il [`Picker`](xref:Xamarin.Forms.Picker) ottiene lo stato attivo, i relativi dati vengono visualizzati e l'utente può selezionare un elemento:

[![](images/picker-selection.png "Picker Selecting an Item")](images/picker-selection-large.png#lightbox "Picker Selecting an Item")

Il [`Picker`](xref:Xamarin.Forms.Picker) genera un evento di [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) quando l'utente seleziona un elemento. Dopo la selezione, l'elemento selezionato viene visualizzato dal `Picker`:

![](images/picker-after-selection.png "Picker after Selection")

Esistono due tecniche per popolare un [`Picker`](xref:Xamarin.Forms.Picker) con i dati:

- Impostazione della proprietà [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) sui dati da visualizzare. Questa è la tecnica consigliata. Per ulteriori informazioni, vedere [impostazione della proprietà ItemsSource di un selettore](populating-itemssource.md).
- Aggiunta dei dati da visualizzare nella raccolta di [`Items`](xref:Xamarin.Forms.Picker.Items) . Questa tecnica è stata il processo originale per popolare un [`Picker`](xref:Xamarin.Forms.Picker) con i dati. Per ulteriori informazioni, vedere [aggiunta di dati a una raccolta di elementi di selezione](populating-items.md).

## <a name="related-links"></a>Collegamenti correlati

- [Selezione](xref:Xamarin.Forms.Picker)
