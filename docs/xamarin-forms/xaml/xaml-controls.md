---
title: Controlli XAML
description: È possibile fare riferimento a tutte le viste definite in Xamarin.Forms da file XAML.
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 445ef85f661d945bda25203f35dea787e64dc9b0
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138424"
---
# <a name="xaml-controls"></a>Controlli XAML

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

Le visualizzazioni sono oggetti dell'interfaccia utente, ad esempio etichette, pulsanti e dispositivi di scorrimento, comunemente noti come *controlli* o *widget* in altri ambienti di programmazione grafica. Le visualizzazioni supportate da Xamarin.Forms tutti derivano dalla [`View`](xref:Xamarin.Forms.View) classe.

È possibile fare riferimento a tutte le viste definite in Xamarin.Forms da file XAML.

## <a name="views-for-presentation"></a>Visualizzazioni per la presentazione

|     |     |
| --- | --- |
| <h3>BoxView</h3>Visualizza un rettangolo di un colore specifico.<p align="center">![Screenshot di un BoxView](xaml-controls-images/BoxView.png "BoxView")</p>[API](xref:Xamarin.Forms.BoxView)  /  di [Guida](~/xamarin-forms/user-interface/boxview.md) di | <pre valign="center">&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>Expander</h3>Fornisce un contenitore espandibile per ospitare qualsiasi contenuto.<p align="center">![Screenshot di un espansore](xaml-controls-images/Expander.png "Expander")</p>[Guida](~/xamarin-forms/user-interface/expander.md) | <pre>&lt;Expander&gt;<br />    &lt;Expander.Header&gt;<br />        &lt;Label Text=&quot;Baboon&quot; /&gt;<br />    &lt;/Expander.Header&gt;<br />    &lt;Image Source=&quot;Baboon.png&quot;<br />           Aspect=&quot;AspectFill&quot; /&gt;<br />&lt;/Expander&gt;</pre></p> |
| <h3>Immagine</h3>Visualizza una bitmap.<p align="center">![Screenshot di un'immagine](xaml-controls-images/Image.png "Image")</p>[API](xref:Xamarin.Forms.Image)  /  di [Guida](~/xamarin-forms/user-interface/images.md) di | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Etichetta</h3>Consente di visualizzare una o più righe di testo.<p align="center">![Screenshot di un'etichetta](xaml-controls-images/Label.png "Label")</p>[API](xref:Xamarin.Forms.Label)  /  di [Guida](~/xamarin-forms/user-interface/text/label.md) di | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>Mappa</h3>Visualizza una mappa.<p align="center">![Screenshot di una mappa](xaml-controls-images/Map.png "Mappa")</p>[API](xref:Xamarin.Forms.Maps.Map)  /  di [Guida](~/xamarin-forms/user-interface/map/index.md) di | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>MediaElement</h3>Riproduzione di video o audio.<p align="center">![Screenshot di un MediaElement](xaml-controls-images/MediaElement.png "MediaELement")</p>[API](xref:Xamarin.Forms.MediaElement)  /  di [Guida](~/xamarin-forms/user-interface/mediaelement.md) di | <p valign="center"><pre>&lt;MediaElement Source="https://sec.ch9.ms/ch9/XamarinShow_mid.mp4"<br />              AutoPlay="True"<br />              ShowsPlaybackControls="True" /&gt;</pre></p> |
| <h3>WebView</h3>Visualizza le pagine Web o il contenuto HTML.<p align="center">![Screenshot di una WebView](xaml-controls-images/WebView.png "WebView")</p>[API](xref:Xamarin.Forms.WebView)  /  di [Guida](~/xamarin-forms/user-interface/webview.md) di | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>Visualizzazioni che avviano i comandi

|     |     |
| --- | --- |
| <h3>Button</h3>Visualizza il testo in un oggetto rettangolare.<p align="center">![Screenshot di un pulsante](xaml-controls-images/Button.png "Button")</p>[API](xref:Xamarin.Forms.Button)  /  di [Guida](~/xamarin-forms/user-interface/button.md) di | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>Visualizza un'immagine in un oggetto rettangolare.<p align="center">![Screenshot di un ImageButton](xaml-controls-images/ImageButton.png "ImageButton")</p>[API](xref:Xamarin.Forms.ImageButton)  /  di [Guida](~/xamarin-forms/user-interface/imagebutton.md) di | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>RadioButton</h3>Consente la selezione di un'opzione da un set.<p align="center">![Screenshot di un RadioButton](xaml-controls-images/RadioButton.png "RadioButton")</p>[Guida](~/xamarin-forms/user-interface/radiobutton.md) | <p valign="center"><pre>&lt;RadioButton Text=&quot;Pineapple&quot;<br/>             CheckedChanged=&quot;OnRadioButtonCheckedChanged&quot; /&gt;</pre></p> |
| <h3>RefreshView</h3>Fornisce funzionalità pull-to-refresh per il contenuto scorrevole.<p align="center">![Screenshot di un RefreshView](xaml-controls-images/RefreshView.png "RefreshView")</p>[Guida](~/xamarin-forms/user-interface/refreshview.md) | <p valign="center"><pre>&lt;RefreshView IsRefreshing="{Binding IsRefreshing}"<br />             Command="{Binding RefreshCommand}" &gt;<br />    &lt;!-- Scrollable control goes here --&gt;<br />&lt;/RefreshView&gt;</pre></p> |
| <h3>SearchBar</h3> Accetta l'input dell'utente usato per eseguire una ricerca.<p align="center">![Screenshot di un oggetto SearchBar](xaml-controls-images/SearchBar.png "SearchBar")</p>[Guida](~/xamarin-forms/user-interface/searchbar.md) | <p valign="center"><pre>&lt;SearchBar Placeholder=&quot;Enter search term&quot;<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
| <h3>SwipeView</h3> Fornisce voci di menu di scelta rapida rivelate da un gesto di scorrimento.<p align="center">![Screenshot di un SwipeView](xaml-controls-images/SwipeView.png "SwipeView")</p>[Guida](~/xamarin-forms/user-interface/swipeview.md) | <p valign="center"><pre>&lt;SwipeView&gt;<br />    &lt;SwipeView.LeftItems&gt;<br />        &lt;SwipeItems&gt;<br />            &lt;SwipeItem Text="Delete"<br />                       IconImageSource="delete.png"<br />                       BackgroundColor="LightPink"<br />                       Invoked="OnDeleteInvoked" /&gt;<br />        &lt;/SwipeItems&gt;<br />    &lt;/SwipeView.LeftItems&gt;<br />    &lt;!-- Content --&gt;<br />&lt;/SwipeView&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>Visualizzazioni per impostare i valori

|     |     |
| --- | --- |
| <h3>CheckBox</h3>Consente la selezione di un `boolean` valore.<p align="center">![Screenshot di una casella di controllo](xaml-controls-images/CheckBox.png "CheckBox")</p> [Guida](~/xamarin-forms/user-interface/checkbox.md) | <p valign="center"><pre>&lt;CheckBox IsChecked="true"<br />          HorizontalOptions="Center"<br />          VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Slider</h3>Consente la selezione di un `double` valore da un intervallo continuo.<p align="center">![Screenshot di un dispositivo di scorrimento](xaml-controls-images/Slider.png "Slider")</p>[API](xref:Xamarin.Forms.Slider)  /  di [Guida](~/xamarin-forms/user-interface/slider.md) di | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Gestore di istruzioni</h3>Consente la selezione di un `double` valore da un intervallo incrementale.<p align="center">![Screenshot di un Stepper](xaml-controls-images/Stepper.png "Gestore di istruzioni")</p>[API](xref:Xamarin.Forms.Stepper)  /  di [Guida](~/xamarin-forms/user-interface/stepper.md) di | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Opzione</h3>Consente la selezione di un `boolean` valore.<p align="center">![Screenshot di un'opzione](xaml-controls-images/Switch.png "Opzione")</p>[API](xref:Xamarin.Forms.Switch)  /  di [Guida](~/xamarin-forms/user-interface/switch.md) di| <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>DatePicker</h3>Consente la selezione di una data.<p align="center">![Screenshot di un DatePicker](xaml-controls-images/DatePicker.png "DatePicker")</p>[API](xref:Xamarin.Forms.DatePicker)  /  di [Guida](~/xamarin-forms/user-interface/datepicker.md) di | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>TimePicker</h3>Consente la selezione di un'ora.<p align="center">![Screenshot di un TimePicker](xaml-controls-images/TimePicker.png "TimePicker")</p>[API](xref:Xamarin.Forms.TimePicker)  /  di [Guida](~/xamarin-forms/user-interface/timepicker.md) di | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>Visualizzazioni per modificare il testo

|     |     |
| --- | --- |
| <h3>Voce</h3>Consente l'immissione e la modifica di una singola riga di testo.<p align="center">![Screenshot di una voce](xaml-controls-images/Entry.png "Voce")</p>[API](xref:Xamarin.Forms.Entry)  /  di [Guida](~/xamarin-forms/user-interface/text/entry.md) di | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Editor</h3>Consente l'immissione e la modifica di più righe di testo.<p align="center">![Screenshot di un editor](xaml-controls-images/Editor.png "Etichetta")</p>[API](xref:Xamarin.Forms.Editor)  /  di [Guida](~/xamarin-forms/user-interface/text/editor.md) di | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>Visualizzazioni per indicare l'attività

|     |     |
| --- | --- |
| <h3>ActivityIndicator</h3>Visualizza un'animazione per indicare che l'applicazione è impegnata in un'attività di lunga durata, senza fornire indicazioni sullo stato di avanzamento.<p align="center">![Screenshot di un ActivityIndicator](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[API](xref:Xamarin.Forms.ActivityIndicator)  /  di [Guida](~/xamarin-forms/user-interface/activityindicator.md) di | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>ProgressBar</h3>Visualizza un'animazione per mostrare che l'applicazione sta procedendo attraverso un'attività di lunga durata.<p align="center">![Screenshot di un ProgressBar](xaml-controls-images/ProgressBar.png "ProgressBar")</p>[API](xref:Xamarin.Forms.ProgressBar)  /  di [Guida](~/xamarin-forms/user-interface/progressbar.md) di | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>Visualizzazioni che visualizzano le raccolte

|     |     |
| --- | --- |
| <h3>CarouselView</h3>Consente di visualizzare un elenco scorrevole di elementi di dati.<p align="center">![Screenshot di un CarouselView](xaml-controls-images/CarouselView.png "CarouselView")</p>[Guida](~/xamarin-forms/user-interface/carouselview/index.md) | <p valign="center"><pre>&lt;CarouselView ItemsSource="{Binding Monkeys}"&gt;<br/>              ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p>|
| <h3>CollectionView</h3>Consente di visualizzare un elenco scorrevole di elementi di dati selezionabili, utilizzando specifiche del layout diverse.<p align="center">![Screenshot di un CollectionView](xaml-controls-images/CollectionView.png "CollectionView")</p>[Guida](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />    &lt;CollectionView.ItemsLayout&gt;<br />       &lt;GridItemsLayout Orientation="Vertical"<br />                        Span="2" /&gt;<br />    &lt;/CollectionView.ItemsLayout&gt;<br />&lt;/CollectionView/&gt;</pre></p> |
| <h3>IndicatorView</h3>Visualizza gli indicatori che rappresentano il numero di elementi in un oggetto `CarouselView` .<p align="center">![Screenshot di un IndicatorView](xaml-controls-images/IndicatorView.png "IndicatorView")</p>[Guida](~/xamarin-forms/user-interface/indicatorview.md) | <p valign="center"><pre>&lt;IndicatorView x:Name="indicatorView"<br />               IndicatorColor="LightGray"<br />               SelectedIndicatorColor="DarkGray" /&gt;</pre></p> |
| <h3>ListView</h3>Consente di visualizzare un elenco scorrevole di elementi di dati selezionabili.<p align="center">![Screenshot di un controllo ListView](xaml-controls-images/ListView.png "ListView")</p>[API](xref:Xamarin.Forms.ListView)  /  di [Guida](~/xamarin-forms/user-interface/listview/index.md) di | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>Selezione</h3>Visualizza un elemento SELECT da un elenco di stringhe di testo.<p align="center">![Screenshot di un selettore](xaml-controls-images/Picker.png "Selezione")</p>[API](xref:Xamarin.Forms.Picker)  /  di [Guida](~/xamarin-forms/user-interface/picker/index.md) di | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&gt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>Visualizza un elenco di righe interattive.<p align="center">![Screenshot di un TableView](xaml-controls-images/TableView.png "TableView")</p>[API](xref:Xamarin.Forms.TableView)  /  di [Guida](~/xamarin-forms/user-interface/tableview.md) di | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.FormsEsempio FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsCampioni](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentazione API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
