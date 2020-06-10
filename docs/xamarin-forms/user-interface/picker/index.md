---
title: " Xamarin.Forms Picker" Description: "la Xamarin.Forms selezione Visualizza un breve elenco di elementi da cui l'utente può selezionare un elemento. Questo articolo illustra come usare la classe Picker per selezionare un elemento di testo da un elenco di dati. "
ms. prod: Novell MS. AssetID: D4815A4B-104B-4294-951B-BD8F2EC33C86 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 02/26/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-picker"></a>Xamarin.FormsSelezione

_La visualizzazione selezione è un controllo per la selezione di un elemento di testo da un elenco di dati._

Xamarin.Forms [`Picker`](xref:Xamarin.Forms.Picker) Visualizza un breve elenco di elementi da cui l'utente può selezionare un elemento. `Picker` definisce le proprietà seguenti:

- [`Title`](xref:Xamarin.Forms.Picker.Title)di tipo `string` , che per impostazione predefinita è `null` .
- `TitleColor`di tipo [`Color`](xref:Xamarin.Forms.Color) , il colore utilizzato per visualizzare il `Title` testo.
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource)di tipo `IList` , l'elenco di origine di elementi da visualizzare, il cui valore predefinito è `null` .
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex)di tipo `int` , l'indice dell'elemento selezionato, il cui valore predefinito è-1.
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem)di tipo `object` , l'elemento selezionato, il cui valore predefinito è `null` .
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor)di tipo [`Color`](xref:Xamarin.Forms.Color) , il colore utilizzato per visualizzare il testo, il cui valore predefinito è [`Color.Default`](xref:Xamarin.Forms.Color.Default) .
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes)di tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , che per impostazione predefinita è [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) .
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily)di tipo `string` , che per impostazione predefinita è `null` .
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize)di tipo `double` , che per impostazione predefinita è-1,0.
- `CharacterSpacing`, di tipo `double` , è la spaziatura tra i caratteri dell'elemento visualizzato da `Picker` .

Tutte le proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere con stile e le proprietà possono essere destinazioni di associazioni dati. Le [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) proprietà e hanno una modalità di associazione predefinita [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , il che significa che possono essere destinazioni di associazioni dati in un'applicazione che usa l'architettura [MVC (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) . Per informazioni sull'impostazione delle proprietà dei tipi di carattere, vedere [caratteri](~/xamarin-forms/user-interface/text/fonts.md).

Un oggetto [`Picker`](xref:Xamarin.Forms.Picker) non Visualizza i dati quando viene visualizzato per la prima volta. Il valore della [`Title`](xref:Xamarin.Forms.Picker.Title) proprietà viene invece visualizzato come segnaposto nelle piattaforme iOS e Android:

[![](images/picker-initial.png "Initial Picker Display")](images/picker-initial-large.png#lightbox "Initial Picker Display")

Quando [`Picker`](xref:Xamarin.Forms.Picker) ottiene lo stato attivo, i relativi dati vengono visualizzati e l'utente può selezionare un elemento:

[![](images/picker-selection.png "Picker Selecting an Item")](images/picker-selection-large.png#lightbox "Picker Selecting an Item")

[`Picker`](xref:Xamarin.Forms.Picker)Genera un [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento quando l'utente seleziona un elemento. Dopo la selezione, l'elemento selezionato viene visualizzato da `Picker` :

![](images/picker-after-selection.png "Picker after Selection")

Esistono due tecniche per popolare un oggetto [`Picker`](xref:Xamarin.Forms.Picker) con i dati:

- Impostazione della [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) proprietà sui dati da visualizzare. Questa è la tecnica consigliata. Per ulteriori informazioni, vedere [impostazione della proprietà ItemsSource di un selettore](populating-itemssource.md).
- Aggiunta dei dati da visualizzare nella [`Items`](xref:Xamarin.Forms.Picker.Items) raccolta. Questa tecnica è stata il processo originale per popolare un [`Picker`](xref:Xamarin.Forms.Picker) con i dati. Per ulteriori informazioni, vedere [aggiunta di dati a una raccolta di elementi di selezione](populating-items.md).

## <a name="related-links"></a>Collegamenti correlati

- [Selezione](xref:Xamarin.Forms.Picker)
