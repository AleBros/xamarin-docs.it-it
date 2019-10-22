---
title: Controlli XAML
description: È possibile fare riferimento a tutte le viste definite in Novell. Forms da file XAML.
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2019
ms.openlocfilehash: f146fc25af5b5c62acece5c736522773e6dc455d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "69976524"
---
# <a name="xaml-controls"></a>Controlli XAML

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

Le visualizzazioni sono oggetti dell'interfaccia utente, ad esempio etichette, pulsanti e dispositivi di scorrimento, comunemente noti come *controlli* o *widget* in altri ambienti di programmazione grafica. Le visualizzazioni supportate da Novell. Forms derivano tutte dalla classe [`View`](xref:Xamarin.Forms.View) .

È possibile fare riferimento a tutte le viste definite in Novell. Forms da file XAML.

## <a name="views-for-presentation"></a>Visualizzazioni per la presentazione

|     |     |
| --- | --- |
| <h3>BoxView</h3>Visualizza un rettangolo di un colore specifico.<p align="center">![Screenshot di un BoxView](xaml-controls-images/BoxView.png "BoxView")</p>[Guida](~/xamarin-forms/user-interface/boxview.md)  /  [API](xref:Xamarin.Forms.BoxView) | <pre valign="center">&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>Immagine</h3>Visualizza una bitmap.<p align="center">![Screenshot di un'immagine](xaml-controls-images/Image.png "Immagine")</p>[Guida](~/xamarin-forms/user-interface/images.md)  /  [API](xref:Xamarin.Forms.Image) | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Label</h3>Consente di visualizzare una o più righe di testo.<p align="center">![Screenshot di un'etichetta](xaml-controls-images/Label.png "Label")</p>[Guida](~/xamarin-forms/user-interface/text/label.md)  /  [API](xref:Xamarin.Forms.Label) | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>Mappa</h3>Visualizza una mappa.<p align="center">![Screenshot di una mappa](xaml-controls-images/Map.png "Mappa")</p>[Guida](~/xamarin-forms/user-interface/map.md)  /  [API](xref:Xamarin.Forms.Maps.Map) | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>WebView</h3>Visualizza le pagine Web o il contenuto HTML.<p align="center">![Screenshot di una WebView](xaml-controls-images/WebView.png "WebView")</p>[Guida](~/xamarin-forms/user-interface/webview.md)  /  [API](xref:Xamarin.Forms.WebView) | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>Viste che avviano comandi

|     |     |
| --- | --- |
| <h3>Button</h3>Visualizza il testo in un oggetto rettangolare.<p align="center">![Screenshot di un pulsante](xaml-controls-images/Button.png "Button")</p>[Guida](~/xamarin-forms/user-interface/button.md)  /  [API](xref:Xamarin.Forms.Button) | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>Visualizza un'immagine in un oggetto rettangolare.<p align="center">![Screenshot di un ImageButton](xaml-controls-images/ImageButton.png "ImageButton")</p>[Guida](~/xamarin-forms/user-interface/imagebutton.md)  /  [API](xref:Xamarin.Forms.ImageButton) | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>SearchBar</h3>Consente di visualizzare una barra di ricerca per l'esecuzione di una ricerca.<p align="center">![Screenshot di un oggetto SearchBar](xaml-controls-images/SearchBar.png "SearchBar")</p>[Guida](~/xamarin-forms/user-interface/searchbar.md)  /  [API](xref:Xamarin.Forms.SearchBar) | <p valign="center"><pre>&lt;SearchBar Placeholder="Xamarin.Forms Property"<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>Visualizzazioni per l'impostazione di valori

|     |     |
| --- | --- |
| <h3>CheckBox</h3>Consente la selezione di un valore `boolean`.<p align="center">![Screenshot di una casella di controllo](xaml-controls-images/CheckBox.png "CheckBox")</p> [Guida](~/xamarin-forms/user-interface/checkbox.md) | <p valign="center"><pre>&lt;CheckBox IsChecked="true"<br />          HorizontalOptions="Center"<br />          VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Slider</h3>Consente la selezione di un valore di `double` da un intervallo continuo.<p align="center">![Screenshot di un dispositivo di scorrimento](xaml-controls-images/Slider.png "Slider")</p>[Guida](~/xamarin-forms/user-interface/slider.md)  /  [API](xref:Xamarin.Forms.Slider) | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Gestore di istruzioni</h3>Consente la selezione di un valore `double` da un intervallo incrementale.<p align="center">![Screenshot di un Stepper](xaml-controls-images/Stepper.png "Gestore di istruzioni")</p>[Guida](~/xamarin-forms/user-interface/stepper.md)  /  [API](xref:Xamarin.Forms.Stepper) | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Opzione</h3>Consente la selezione di un valore `boolean`.<p align="center">![Screenshot di un'opzione](xaml-controls-images/Switch.png "Opzione")</p>[Guida](~/xamarin-forms/user-interface/switch.md)  /  [API](xref:Xamarin.Forms.Switch)| <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>DatePicker</h3>Consente la selezione di una data.<p align="center">![Screenshot di un DatePicker](xaml-controls-images/DatePicker.png "DatePicker")</p>[Guida](~/xamarin-forms/user-interface/datepicker.md)  /  [API](xref:Xamarin.Forms.DatePicker) | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>TimePicker</h3>Consente la selezione di un'ora.<p align="center">![Screenshot di un TimePicker](xaml-controls-images/TimePicker.png "TimePicker")</p>[Guida](~/xamarin-forms/user-interface/timepicker.md)  /  [API](xref:Xamarin.Forms.TimePicker) | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>Visualizzazioni per la modifica del testo

|     |     |
| --- | --- |
| <h3>Voce</h3>Consente l'immissione e la modifica di una singola riga di testo.<p align="center">![Screenshot di una voce](xaml-controls-images/Entry.png "Voce")</p>[Guida](~/xamarin-forms/user-interface/text/entry.md)  /  [API](xref:Xamarin.Forms.Entry) | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Editor</h3>Consente l'immissione e la modifica di più righe di testo.<p align="center">![Screenshot di un editor](xaml-controls-images/Editor.png "Label")</p>[Guida](~/xamarin-forms/user-interface/text/editor.md)  /  [API](xref:Xamarin.Forms.Editor) | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>Visualizzazioni per indicare l'attività

|     |     |
| --- | --- |
| <h3>ActivityIndicator</h3>Visualizza un'animazione per indicare che l'applicazione è impegnata in un'attività di lunga durata, senza fornire indicazioni sullo stato di avanzamento.<p align="center">![Screenshot di un ActivityIndicator](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[Guida](~/xamarin-forms/user-interface/activityindicator.md)  /  [API](xref:Xamarin.Forms.ActivityIndicator) | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>ProgressBar</h3>Visualizza un'animazione per mostrare che l'applicazione sta procedendo attraverso un'attività di lunga durata.<p align="center">![Screenshot di un ProgressBar](xaml-controls-images/ProgressBar.png "ProgressBar")</p>[Guida](~/xamarin-forms/user-interface/progressbar.md)  /  [API](xref:Xamarin.Forms.ProgressBar) | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>Viste che visualizzano le raccolte

|     |     |
| --- | --- |
| <h3>CollectionView</h3>Consente di visualizzare un elenco scorrevole di elementi di dati selezionabili, utilizzando specifiche del layout diverse.<p align="center">![Screenshot di un CollectionView](xaml-controls-images/CollectionView.png "CollectionView")</p>[Guida](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />    &lt;CollectionView.ItemsLayout&gt;<br />       &lt;GridItemsLayout Orientation="Vertical"<br />                        Span="2" /&gt;<br />    &lt;/CollectionView.ItemsLayout&gt;<br />&lt;/CollectionView/&gt;</pre></p> |
| <h3>ListView</h3>Consente di visualizzare un elenco scorrevole di elementi di dati selezionabili.<p align="center">![Screenshot di un controllo ListView](xaml-controls-images/ListView.png "ListView")</p>[Guida](~/xamarin-forms/user-interface/listview/index.md)  /  [API](xref:Xamarin.Forms.ListView) | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>Selezione</h3>Visualizza un elemento SELECT da un elenco di stringhe di testo.<p align="center">![Screenshot di un selettore](xaml-controls-images/Picker.png "Selezione")</p>[Guida](~/xamarin-forms/user-interface/picker/index.md)  /  [API](xref:Xamarin.Forms.Picker) | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&lt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>Visualizza un elenco di righe interattive.<p align="center">![Screenshot di un TableView](xaml-controls-images/TableView.png "TableView")</p>[Guida](~/xamarin-forms/user-interface/tableview.md)  /  [API](xref:Xamarin.Forms.TableView) | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Novell. Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Esempi di Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentazione per le API di Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
