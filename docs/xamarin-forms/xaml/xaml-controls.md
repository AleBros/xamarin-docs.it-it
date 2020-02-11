---
title: Controlli XAML
description: È possibile fare riferimento a tutte le viste definite in Xamarin.Forms da file XAML.
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2019
ms.openlocfilehash: c1163ba49b987b8ebd3702d296b103b8a2e91d84
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490232"
---
# <a name="xaml-controls"></a>Controlli XAML

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

Le visualizzazioni sono oggetti dell'interfaccia utente, ad esempio i dispositivi di scorrimento che sono comunemente noti come, pulsanti ed etichette *controlli* oppure *widget* in altri ambienti di programmazione grafiche. Le visualizzazioni supportate da Xamarin.Forms derivano da tutte le [`View`](xref:Xamarin.Forms.View) classe.

È possibile fare riferimento a tutte le viste definite in Xamarin.Forms da file XAML.

## <a name="views-for-presentation"></a>Viste per la presentazione

|     |     |
| --- | --- |
| <h3>BoxView</h3>Visualizza un rettangolo di un colore specifico.<p align="center">![Screenshot di un BoxView](xaml-controls-images/BoxView.png "BoxView")</p>[Guida](xref:Xamarin.Forms.BoxView) / [all'API](~/xamarin-forms/user-interface/boxview.md) | <pre valign="center">&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>Immagine</h3>Visualizza una bitmap.<p align="center">![Screenshot di un'immagine](xaml-controls-images/Image.png "Immagine")</p>[Guida](xref:Xamarin.Forms.Image) / [all'API](~/xamarin-forms/user-interface/images.md) | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Label</h3>Consente di visualizzare una o più righe di testo.<p align="center">![Screenshot di un'etichetta](xaml-controls-images/Label.png "Label")</p>[Guida](xref:Xamarin.Forms.Label) / [all'API](~/xamarin-forms/user-interface/text/label.md) | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>Mappa</h3>Visualizza una mappa.<p align="center">![Screenshot di una mappa](xaml-controls-images/Map.png "Mappa")</p>[Guida](xref:Xamarin.Forms.Maps.Map) / [all'API](~/xamarin-forms/user-interface/map/index.md) | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>WebView</h3>Visualizza le pagine Web o il contenuto HTML.<p align="center">![Screenshot di una WebView](xaml-controls-images/WebView.png "WebView")</p>[Guida](xref:Xamarin.Forms.WebView) / [all'API](~/xamarin-forms/user-interface/webview.md) | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>Viste che consentono di avviare i comandi

|     |     |
| --- | --- |
| <h3>Button</h3>Visualizza il testo in un oggetto rettangolare.<p align="center">![Screenshot di un pulsante](xaml-controls-images/Button.png "Button")</p>[Guida](xref:Xamarin.Forms.Button) / [all'API](~/xamarin-forms/user-interface/button.md) | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>Visualizza un'immagine in un oggetto rettangolare.<p align="center">![Screenshot di un ImageButton](xaml-controls-images/ImageButton.png "ImageButton")</p>[Guida](xref:Xamarin.Forms.ImageButton) / [all'API](~/xamarin-forms/user-interface/imagebutton.md) | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>RefreshView</h3>Fornisce funzionalità pull-to-refresh per il contenuto scorrevole.<p align="center">![Screenshot di un RefreshView](xaml-controls-images/RefreshView.png "RefreshView")</p>[Guida](~/xamarin-forms/user-interface/refreshview.md) | <p valign="center"><pre>&lt;RefreshView IsRefreshing="{Binding IsRefreshing}"<br />             Command="{Binding RefreshCommand}" &gt;<br />    &lt;!-- Scrollable control goes here --&gt;<br />&lt;/RefreshView&gt;</pre></p> |
| <h3>SearchBar</h3> Accetta l'input dell'utente usato per eseguire una ricerca.<p align="center">![Screenshot di un oggetto SearchBar](xaml-controls-images/SearchBar.png "SearchBar")</p>[Guida](~/xamarin-forms/user-interface/searchbar.md) | <p valign="center"><pre>&lt;SearchBar Placeholder=&quot;Enter search term&quot;<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
| <h3>SwipeView</h3> Fornisce voci di menu di scelta rapida rivelate da un gesto di scorrimento.<p align="center">![Screenshot di un SwipeView](xaml-controls-images/SwipeView.png "SwipeView")</p>[Guida](~/xamarin-forms/user-interface/swipeview.md) | <p valign="center"><pre>&lt;SwipeView&gt;<br />    &lt;SwipeView.LeftItems&gt;<br />        &lt;SwipeItems&gt;<br />            &lt;SwipeItem Text="Delete"<br />                       IconImageSource="delete.png"<br />                       BackgroundColor="LightPink"<br />                       Invoked="OnDeleteInvoked" /&gt;<br />        &lt;/SwipeItems&gt;<br />    &lt;/SwipeView.LeftItems&gt;<br />    &lt;!-- Content --&gt;<br />&lt;/SwipeView&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>Visualizzazioni per l'impostazione di valori

|     |     |
| --- | --- |
| <h3>CheckBox</h3>Consente la selezione di un valore `boolean`.<p align="center">![Screenshot di una casella di controllo](xaml-controls-images/CheckBox.png "CheckBox")</p> [Guida](~/xamarin-forms/user-interface/checkbox.md) | <p valign="center"><pre>&lt;CheckBox IsChecked="true"<br />          HorizontalOptions="Center"<br />          VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Slider</h3>Consente la selezione di un valore di `double` da un intervallo continuo.<p align="center">![Screenshot di un dispositivo di scorrimento](xaml-controls-images/Slider.png "Slider")</p>[Guida](xref:Xamarin.Forms.Slider) / [all'API](~/xamarin-forms/user-interface/slider.md) | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Gestore di istruzioni</h3>Consente la selezione di un valore `double` da un intervallo incrementale.<p align="center">![Screenshot di un Stepper](xaml-controls-images/Stepper.png "Gestore di istruzioni")</p>[Guida](xref:Xamarin.Forms.Stepper) / [all'API](~/xamarin-forms/user-interface/stepper.md) | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Opzione</h3>Consente la selezione di un valore `boolean`.<p align="center">![Screenshot di un'opzione](xaml-controls-images/Switch.png "Opzione")</p>[Guida](xref:Xamarin.Forms.Switch) / [all'API](~/xamarin-forms/user-interface/switch.md)| <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>DatePicker</h3>Consente la selezione di una data.<p align="center">![Screenshot di un DatePicker](xaml-controls-images/DatePicker.png "DatePicker")</p>[Guida](xref:Xamarin.Forms.DatePicker) / [all'API](~/xamarin-forms/user-interface/datepicker.md) | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>TimePicker</h3>Consente la selezione di un'ora.<p align="center">![Screenshot di un TimePicker](xaml-controls-images/TimePicker.png "TimePicker")</p>[Guida](xref:Xamarin.Forms.TimePicker) / [all'API](~/xamarin-forms/user-interface/timepicker.md) | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>Viste per la modifica del testo

|     |     |
| --- | --- |
| <h3>Voce</h3>Consente l'immissione e la modifica di una singola riga di testo.<p align="center">![Screenshot di una voce](xaml-controls-images/Entry.png "Voce")</p>[Guida](xref:Xamarin.Forms.Entry) / [all'API](~/xamarin-forms/user-interface/text/entry.md) | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Editor</h3>Consente l'immissione e la modifica di più righe di testo.<p align="center">![Screenshot di un editor](xaml-controls-images/Editor.png "Label")</p>[Guida](xref:Xamarin.Forms.Editor) / [all'API](~/xamarin-forms/user-interface/text/editor.md) | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>Viste per indicare l'attività

|     |     |
| --- | --- |
| <h3>ActivityIndicator</h3>Visualizza un'animazione per indicare che l'applicazione è impegnata in un'attività di lunga durata, senza fornire indicazioni sullo stato di avanzamento.<p align="center">![Screenshot di un ActivityIndicator](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[Guida](xref:Xamarin.Forms.ActivityIndicator) / [all'API](~/xamarin-forms/user-interface/activityindicator.md) | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>ProgressBar</h3>Visualizza un'animazione per mostrare che l'applicazione sta procedendo attraverso un'attività di lunga durata.<p align="center">![Screenshot di un ProgressBar](xaml-controls-images/ProgressBar.png "ProgressBar")</p>[Guida](xref:Xamarin.Forms.ProgressBar) / [all'API](~/xamarin-forms/user-interface/progressbar.md) | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>Viste che consentono di visualizzare le raccolte

|     |     |
| --- | --- |
| <h3>CarouselView</h3>Consente di visualizzare un elenco scorrevole di elementi di dati.<p align="center">![Screenshot di un CarouselView](xaml-controls-images/CarouselView.png "CarouselView")</p>[Guida](~/xamarin-forms/user-interface/carouselview/index.md) | <p valign="center"><pre>&lt;CarouselView ItemsSource="{Binding Monkeys}"&gt;<br/>              ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p>|
| <h3>CollectionView</h3>Consente di visualizzare un elenco scorrevole di elementi di dati selezionabili, utilizzando specifiche del layout diverse.<p align="center">![Screenshot di un CollectionView](xaml-controls-images/CollectionView.png "CollectionView")</p>[Guida](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />    &lt;CollectionView.ItemsLayout&gt;<br />       &lt;GridItemsLayout Orientation="Vertical"<br />                        Span="2" /&gt;<br />    &lt;/CollectionView.ItemsLayout&gt;<br />&lt;/CollectionView/&gt;</pre></p> |
| <h3>IndicatorView</h3>Visualizza gli indicatori che rappresentano il numero di elementi in un `CarouselView`.<p align="center">![Screenshot di un IndicatorView](xaml-controls-images/IndicatorView.png "IndicatorView")</p>[Guida](~/xamarin-forms/user-interface/indicatorview.md) | <p valign="center"><pre>&lt;IndicatorView ItemsSourceBy="carouselView"<br />               IndicatorColor="LightGray"<br />               SelectedIndicatorColor="DarkGray" /&gt;</pre></p> |
| <h3>ListView</h3>Consente di visualizzare un elenco scorrevole di elementi di dati selezionabili.<p align="center">![Screenshot di un controllo ListView](xaml-controls-images/ListView.png "ListView")</p>[Guida](xref:Xamarin.Forms.ListView) / [all'API](~/xamarin-forms/user-interface/listview/index.md) | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>Selezione</h3>Visualizza un elemento SELECT da un elenco di stringhe di testo.<p align="center">![Screenshot di un selettore](xaml-controls-images/Picker.png "Selezione")</p>[Guida](xref:Xamarin.Forms.Picker) / [all'API](~/xamarin-forms/user-interface/picker/index.md) | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&lt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>Visualizza un elenco di righe interattive.<p align="center">![Screenshot di un TableView](xaml-controls-images/TableView.png "TableView")</p>[Guida](xref:Xamarin.Forms.TableView) / [all'API](~/xamarin-forms/user-interface/tableview.md) | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Xamarin.Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Esempi di Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentazione per le API di Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
