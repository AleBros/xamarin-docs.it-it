---
title: Controlli XAML
description: Tutte le viste definite in xamarin. Forms possono farvi riferimento dai file XAML.
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/03/2019
ms.openlocfilehash: a8a61ac505eab8c458c49bde9184d6e96583d37f
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59020271"
---
# <a name="xaml-controls"></a>Controlli XAML

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/FormsGallery/)

Le visualizzazioni sono oggetti dell'interfaccia utente, ad esempio i dispositivi di scorrimento che sono comunemente noti come, pulsanti ed etichette *controlli* oppure *widget* in altri ambienti di programmazione grafiche. Le visualizzazioni supportate da xamarin. Forms derivano da tutte le [ `View` ](xref:Xamarin.Forms.View) classe.

Tutte le viste definite in xamarin. Forms possono farvi riferimento dai file XAML.

## <a name="views-for-presentation"></a>Viste per la presentazione

|     |     |
| --- | --- |
| <h3>BoxView</h3>Visualizza un rettangolo di colore specifico.<p align="center">![Screenshot di un BoxView](xaml-controls-images/BoxView.png "BoxView")</p>[API](xref:Xamarin.Forms.BoxView) / [Guida](https://developer.xamarin.com/guides/xamarin-forms/user-interface/boxview/) | <pre valign="center">&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>Image</h3>Consente di visualizzare una bitmap.<p align="center">![Schermata di un'immagine](xaml-controls-images/Image.png "immagine")</p>[API](xref:Xamarin.Forms.Image) / [Guida](~/xamarin-forms/user-interface/images.md) | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Label</h3>Visualizza uno o più righe di testo.<p align="center">![Schermata di un'etichetta](xaml-controls-images/Label.png "etichetta")</p>[API](xref:Xamarin.Forms.Label) / [Guida](~/xamarin-forms/user-interface/text/label.md) | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>Mappa</h3>Consente di visualizzare una mappa.<p align="center">![Screenshot di una mappa](xaml-controls-images/Map.png "mappa")</p>[API](xref:Xamarin.Forms.Maps.Map) / [Guida](~/xamarin-forms/user-interface/map.md) | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>WebView</h3>Visualizza le pagine Web o contenuto HTML.<p align="center">![Screenshot di una WebView](xaml-controls-images/WebView.png "WebView")</p>[API](xref:Xamarin.Forms.WebView) / [Guida](~/xamarin-forms/user-interface/webview.md) | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>Viste che consentono di avviare i comandi

|     |     |
| --- | --- |
| <h3>Button</h3>Visualizza il testo in un oggetto rettangolare.<p align="center">![Screenshot di un pulsante](xaml-controls-images/Button.png "pulsante")</p>[API](xref:Xamarin.Forms.Button) / [Guida](~/xamarin-forms/user-interface/button.md) | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>Visualizza un'immagine in un oggetto rettangolare.<p align="center">![Screenshot di un ImageButton](xaml-controls-images/ImageButton.png "ImageButton")</p>[API](xref:Xamarin.Forms.ImageButton) / [Guida](~/xamarin-forms/user-interface/imagebutton.md) | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>SearchBar</h3>Consente di visualizzare una barra di ricerca, per l'esecuzione di una ricerca.<p align="center">![Screenshot di un SearchBar](xaml-controls-images/SearchBar.png "SearchBar")</p>[API](xref:Xamarin.Forms.SearchBar) | <p valign="center"><pre>&lt;SearchBar Placeholder="Xamarin.Forms Property"<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>Visualizzazioni per l'impostazione di valori

|     |     |
| --- | --- |
| <h3>Slider</h3>Consente di selezionare un `double` valore dall'intervallo continuo.<p align="center">![Screenshot di un dispositivo di scorrimento](xaml-controls-images/Slider.png "dispositivo di scorrimento")</p>[API](xref:Xamarin.Forms.Slider) / [Guida](~/xamarin-forms/user-interface/slider.md) | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Gestore di istruzioni</h3>Consente di selezionare un `double` valore da un intervallo incrementale.<p align="center">![Screenshot di un gestore di istruzioni](xaml-controls-images/Stepper.png "gestore di istruzioni")</p>[API](xref:Xamarin.Forms.Stepper) / [Guida](~/xamarin-forms/user-interface/stepper.md) | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Opzione</h3>Consente di selezionare un `boolean` valore.<p align="center">![Screenshot di un commutatore](xaml-controls-images/Switch.png "commutatore")</p>[API](xref:Xamarin.Forms.Switch) | <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>DatePicker</h3>Consente di selezionare una data.<p align="center">![Screenshot di un controllo DatePicker](xaml-controls-images/DatePicker.png "DatePicker")</p>[API](xref:Xamarin.Forms.DatePicker) / [Guida](~/xamarin-forms/user-interface/datepicker.md) | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>TimePicker</h3>Consente di selezionare un'ora.<p align="center">![Screenshot di un controllo TimePicker](xaml-controls-images/TimePicker.png "TimePicker")</p>[API](xref:Xamarin.Forms.TimePicker) / [Guida](~/xamarin-forms/user-interface/timepicker.md) | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>Viste per la modifica del testo

|     |     |
| --- | --- |
| <h3>Voce</h3>Consente a una singola riga di testo che deve essere immessa e modificato.<p align="center">![Screenshot di una voce](xaml-controls-images/Entry.png "voce")</p>[API](xref:Xamarin.Forms.Entry) / [Guida](~/xamarin-forms/user-interface/text/entry.md) | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Editor</h3>Consente a più righe di testo che deve essere immessa e modificato.<p align="center">![Screenshot di un Editor](xaml-controls-images/Editor.png "etichetta")</p>[API](xref:Xamarin.Forms.Editor) / [Guida](~/xamarin-forms/user-interface/text/editor.md) | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>Viste per indicare l'attività

|     |     |
| --- | --- |
| <h3>ActivityIndicator</h3>Visualizza un'animazione per mostrare che l'applicazione è impegnata in un'attività di lunga durata, senza fornire alcuna indicazione dello stato di avanzamento.<p align="center">![Screenshot di un ActivityIndicator](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[API](xref:Xamarin.Forms.ActivityIndicator) | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>ProgressBar</h3>Visualizza un'animazione per mostrare che l'applicazione sta avanzando tramite un'attività di lunga durata.<p align="center">![Screenshot di una ProgressBar](xaml-controls-images/ProgressBar.png "ProgressBar")</p>[API](xref:Xamarin.Forms.ProgressBar) | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>Viste che consentono di visualizzare le raccolte

|     |     |
| --- | --- |
| <h3>CollectionView</h3>Visualizza un elenco scorrevole selezionabile di elementi di dati, in base alle specifiche di un layout diverso.<p align="center">![Screenshot della visualizzazione di una raccolta](xaml-controls-images/CollectionView.png "CollectionView")</p>[Guida](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />    &lt;CollectionView.ItemsLayout&gt;<br />       &lt;GridItemsLayout Orientation="Vertical"<br />                        Span="2" /&gt;<br />    &lt;/CollectionView.ItemsLayout&gt;<br />&lt;/CollectionView/&gt;</pre></p> |
| <h3>ListView</h3>Visualizza un elenco scorrevole di elementi di dati selezionabile.<p align="center">![Screenshot di un ListView](xaml-controls-images/ListView.png "ListView")</p>[API](xref:Xamarin.Forms.ListView) / [Guida](~/xamarin-forms/user-interface/listview/index.md) | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>Selezione</h3>Consente di visualizzare un elemento selezionato da un elenco di stringhe di testo.<p align="center">![Screenshot di un controllo di selezione](xaml-controls-images/Picker.png "selezione")</p>[API](xref:Xamarin.Forms.Picker) / [Guida](~/xamarin-forms/user-interface/picker/index.md) | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&lt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>Visualizza un elenco di righe interattive.<p align="center">![Screenshot di un TableView](xaml-controls-images/TableView.png "TableView")</p>[API](xref:Xamarin.Forms.TableView) / [Guida](~/xamarin-forms/user-interface/tableview.md) | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di xamarin. Forms FormsGallery](https://developer.xamarin.com/samples/FormsGallery/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentazione per le API di Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
