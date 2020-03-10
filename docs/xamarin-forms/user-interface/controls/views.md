---
title: Visualizzazioni di xamarin. Forms
description: Le visualizzazioni di xamarin. Forms sono i blocchi predefiniti di interfacce utente per dispositivi mobili multipiattaforma. Questo articolo elenca le viste che sono inclusi in xamarin. Forms.
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/14/2020
ms.openlocfilehash: 09bcb49db7f257a415518b259672ca8e776cdbc4
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78917277"
---
# <a name="xamarinforms-views"></a>Visualizzazioni di xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Le visualizzazioni Novell. Forms sono i blocchi predefiniti delle interfacce utente per dispositivi mobili multipiattaforma._

Le visualizzazioni sono oggetti dell'interfaccia utente, ad esempio etichette, pulsanti e dispositivi di scorrimento, comunemente noti come *controlli* o *widget* in altri ambienti di programmazione grafica. Le visualizzazioni supportate da Novell. Forms derivano tutte dalla classe [`View`](xref:Xamarin.Forms.View) . Possono essere suddivisi in diverse categorie:

## <a name="views-for-presentation"></a>Visualizzazioni per la presentazione

### <a name="label"></a>Etichetta

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label) Visualizza le stringhe di testo a riga singola o i blocchi di testo a più righe, con formattazione costante o variabile. Impostare la proprietà [`Text`](xref:Xamarin.Forms.Label.Text) su una stringa per la formattazione costante oppure impostare la proprietà [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) su un oggetto [`FormattedString`](xref:Xamarin.Forms.FormattedString) per la formattazione delle variabili.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Label) / [Guida](~/xamarin-forms/user-interface/text/label.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Esempio di etichetta](views-images/Label.png "Esempio di etichetta")](views-images/Label-Large.png#lightbox "Esempio di etichetta")<br /> codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) |
|     |     |

### <a name="image"></a>Immagine

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image) Visualizza una bitmap. Le bitmap possono essere scaricate sul Web, incorporate come risorse nel progetto comune o nei progetti di piattaforma oppure create usando un oggetto `Stream` .NET.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Image) / [Guida](~/xamarin-forms/user-interface/images.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages) | [![Esempio di immagine](views-images/Image.png "Esempio di immagine")](views-images/Image-Large.png#lightbox "Esempio di immagine")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) |
|     |     |

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView) Visualizza un rettangolo a tinta unita colorato dalla proprietà [`Color`](xref:Xamarin.Forms.BoxView.Color) . `BoxView` ha una richiesta di dimensioni predefinite di 40x40. Per altre dimensioni, assegnare le proprietà [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) .<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.BoxView) / [Guida](~/xamarin-forms/user-interface/boxview.md) / [esempio 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration), [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/), [4](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife), [5](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)e [6](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) | [![Esempio di BoxView](views-images/BoxView.png "Esempio di BoxView")](views-images/BoxView-Large.png#lightbox "Esempio di BoxView")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView) Visualizza pagine Web o contenuto HTML, a seconda che la proprietà [`Source`](xref:Xamarin.Forms.WebView.Source) sia impostata su un [`UriWebViewSource`](xref:Xamarin.Forms.UrlWebViewSource) o un oggetto [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource) .<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.WebView) / [Guida](~/xamarin-forms/user-interface/webview.md) / [esempio 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview) e [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) | [![Esempio di WebView](views-images/WebView.png "Esempio di WebView")](views-images/WebView-Large.png#lightbox "Esempio di WebView")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView) Visualizza la grafica OpenGL nei progetti iOS e Android. Non vi è alcun supporto per la piattaforma Windows universale. Per i progetti iOS e Android è necessario un riferimento all'assembly **OpenTK-1,0** o **OpenTK** versione 1.0.0.0. `OpenGLView` è più facile da usare in un progetto condiviso. Se usato in una libreria di .NET Standard, sarà necessario anche un servizio di dipendenza (come illustrato nel codice di esempio).<br /><br />Si tratta dell'unica funzionalità grafica incorporata in Novell. Forms, ma un'applicazione Novell. Forms può anche eseguire il rendering della grafica usando [`SkiaSharp`](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)o [`UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md).<br /><br />[Documentazione delle API](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![Esempio di OpenGLView](views-images/OpenGLView.png "Esempio di OpenGLView")](views-images/OpenGLView-Large.png#lightbox "Esempio di OpenGLView")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) |
|     |     |

### <a name="map"></a>Mappa

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map) Visualizza una mappa. È necessario installare il pacchetto NuGet **Novell. Forms. Maps** . Android e Universal Windows Platform richiedono una chiave di autorizzazione della mappa.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Maps.Map) / [Guida](~/xamarin-forms/user-interface/map/index.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps/) | [![Esempio di mappa](views-images/Map.png "Esempio di mappa")](views-images/Map-Large.png#lightbox "Esempio di mappa")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) |
|     |     |

### <a name="mediaelement"></a>MediaElement

|     |     |
| --- | --- |
| [`MediaElement`](xref:Xamarin.Forms.MediaElement) riproduce video o audio. Il supporto può essere riprodotto da un URL o da un file locale, a seconda che la proprietà [`Source`](xref:Xamarin.Forms.MediaElement.Source) sia impostata su un [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource) o un [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource).<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.MediaElement) / [Guida](~/xamarin-forms/user-interface/mediaelement.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos) | [![Esempio di MediaElement](views-images/MediaElement.png "Esempio di MediaElement")](views-images/MediaElement-Large.png#lightbox "Esempio di MediaElement")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MediaElementDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MediaElementDemoPage.cs) |
|     |     |

## <a name="views-that-initiate-commands"></a>Visualizzazioni che avviano i comandi

### <a name="button"></a>Pulsante

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button) è un oggetto rettangolare che Visualizza il testo e genera un evento di [`Clicked`](xref:Xamarin.Forms.Button.Clicked) quando è stato premuto.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Button) / [Guida](~/xamarin-forms/user-interface/button.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos/) | [![Esempio di pulsante](views-images/Button.png "Esempio di pulsante")](views-images/Button-Large.png#lightbox "Esempio di pulsante")<br /> codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton` è un oggetto rettangolare che visualizza un'immagine e che genera un evento di `Clicked` quando è stato premuto.<br /><br /> [Guida](~/xamarin-forms/user-interface/imagebutton.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![Esempio di ImageButton](views-images/ImageButton.png "Esempio di ImageButton")](views-images/ImageButton-Large.png#lightbox "Esempio di ImageButton")<br /> codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs) |
|     |     |

### <a name="refreshview"></a>RefreshView

|     |     |
| --- | --- |
| `RefreshView` è un controllo contenitore che fornisce funzionalità pull-to-refresh per il contenuto scorrevole. Il `ICommand` definito dalla proprietà `Command` viene eseguito quando viene attivato un aggiornamento e la proprietà `IsRefreshing` indica lo stato corrente del controllo.<br /><br /> [Guida](~/xamarin-forms/user-interface/refreshview.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![Esempio di RefreshView](views-images/RefreshView.png "Esempio di RefreshView")](views-images/RefreshView-Large.png#lightbox "Esempio di RefreshView")<br /> codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RefreshViewDemoPage.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar) Visualizza un'area in cui l'utente deve digitare una stringa di testo e un pulsante (o un tasto della tastiera) che segnala all'applicazione di eseguire una ricerca. La proprietà [`Text`](xref:Xamarin.Forms.InputView.Text) fornisce l'accesso al testo e l'evento [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) indica che è stato premuto il pulsante.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.SearchBar) / [Guida](~/xamarin-forms/user-interface/searchbar.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/) | [![Esempio di SearchBar](views-images/SearchBar.png "Esempio di SearchBar")](views-images/SearchBar-Large.png#lightbox "Esempio di SearchBar")<br /> codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) |
|     |     |

### <a name="swipeview"></a>SwipeView

|     |     |
| --- | --- |
| `SwipeView` è un controllo contenitore che esegue il wrapping di un elemento di contenuto e fornisce voci di menu di scelta rapida rivelate da un gesto di scorrimento. Ogni voce di menu è rappresentata da un `SwipeItem`, che dispone di una proprietà `Command` che esegue un `ICommand` quando viene toccato l'elemento.<br /><br /> [Guida](~/xamarin-forms/user-interface/swipeview.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) | [![Esempio di SwipeView](views-images/SwipeView.png "Esempio di SwipeView")](views-images/SwipeView-Large.png#lightbox "Esempio di SwipeView")<br /> codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwipeViewDemoPage.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Visualizzazioni per impostare i valori

### <a name="checkbox"></a>CheckBox

|     |     |
| --- | --- |
| `CheckBox` consente all'utente di selezionare un valore booleano usando un tipo di pulsante che può essere selezionato o vuoto. La proprietà `IsChecked` è lo stato della `CheckBox`e l'evento `CheckedChanged` viene generato quando lo stato cambia.<br /><br />Documentazione dell'API/ [guida](~/xamarin-forms/user-interface/checkbox.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos) | [![Esempio di casella di controllo](views-images/CheckBox.png "Esempio di casella di controllo")](views-images/CheckBox-Large.png#lightbox "Esempio di casella di controllo")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CheckBoxPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CheckBoxPage.cs) |
|     |     |

### <a name="slider"></a>Dispositivo di scorrimento

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider) consente all'utente di selezionare un valore `double` da un intervallo continuo specificato con le proprietà [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) e [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) .<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Slider) / [Guida](~/xamarin-forms/user-interface/slider.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) | [![Esempio di dispositivo di scorrimento](views-images/Slider.png "Esempio di dispositivo di scorrimento")](views-images/Slider-Large.png#lightbox "Esempio di dispositivo di scorrimento")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) |
|     |     |

### <a name="stepper"></a>Gestore di istruzioni

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper) consente all'utente di selezionare un valore `double` da un intervallo di valori incrementali specificati con le proprietà [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum), [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum)e [`Increment`](xref:Xamarin.Forms.Stepper.Increment) .<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Stepper)  / [Guida](~/xamarin-forms/user-interface/stepper.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) | [![Esempio di stepper](views-images/Stepper.png "Esempio di stepper")](views-images/Stepper-Large.png#lightbox "Esempio di stepper")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) |
|     |     |

### <a name="switch"></a>Opzione

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch) assume la forma di un'opzione di attivazione/disattivazione per consentire all'utente di selezionare un valore booleano. La proprietà [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) è lo stato dell'opzione e l'evento [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) viene generato quando lo stato cambia.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Switch) / [Guida](~/xamarin-forms/user-interface/switch.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/) | [![Esempio di switch](views-images/Switch.png "Esempio di switch")](views-images/Switch-Large.png#lightbox "Esempio di switch")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker) consente all'utente di selezionare una data con il selettore di data della piattaforma. Impostare un intervallo di date consentite con le proprietà [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) e [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) . La proprietà [`Date`](xref:Xamarin.Forms.DatePicker.Date) è la data selezionata e l'evento [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) viene generato quando la proprietà viene modificata.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.DatePicker) / [Guida](~/xamarin-forms/user-interface/datepicker.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) | [![Esempio di DatePicker](views-images/DatePicker.png "Esempio di DatePicker")](views-images/DatePicker-Large.png#lightbox "Esempio di DatePicker")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker) consente all'utente di selezionare un'ora con la selezione ora piattaforma. La proprietà [`Time`](xref:Xamarin.Forms.TimePicker.Time) è l'ora selezionata. Un'applicazione può monitorare le modifiche apportate alla proprietà `Time` installando un gestore per l'evento [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) .<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.TimePicker) / [Guida](~/xamarin-forms/user-interface/timepicker.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) | [![Esempio di TimePicker](views-images/TimePicker.png "Esempio di TimePicker")](views-images/TimePicker-Large.png#lightbox "Esempio di TimePicker")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) |
|     |     |

## <a name="views-for-editing-text"></a>Visualizzazioni per modificare il testo

Queste due classi derivano dalla classe [`InputView`](xref:Xamarin.Forms.InputView) , che definisce la proprietà [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) .

### <a name="entry"></a>Voce

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry) consente all'utente di immettere e modificare una singola riga di testo. Il testo è disponibile come proprietà [`Text`](xref:Xamarin.Forms.InputView.Text) e gli eventi [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) e [`Completed`](xref:Xamarin.Forms.Entry.Completed) vengono generati quando il testo viene modificato o l'utente segnala il completamento toccando il tasto INVIO.<br /><br />Usare un [`Editor`](#editor) per l'immissione e la modifica di più righe di testo.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Entry) / [Guida](~/xamarin-forms/user-interface/text/entry.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Esempio di voce](views-images/Entry.png "Esempio di voce")](views-images/Entry-Large.png#lightbox "Esempio di voce")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) |
|     |     |

### <a name="editor"></a>Editor

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor) consente all'utente di immettere e modificare più righe di testo. Il testo è disponibile come proprietà [`Text`](xref:Xamarin.Forms.InputView.Text) e gli eventi [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) e [`Completed`](xref:Xamarin.Forms.Editor.Completed) vengono generati quando il testo viene modificato o l'utente segnala il completamento.<br /><br />Utilizzare una visualizzazione [`Entry`](#entry) per l'immissione e la modifica di una singola riga di testo.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Editor) / [Guida](~/xamarin-forms/user-interface/text/editor.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Esempio di voce](views-images/Editor.png "Esempio di editor")](views-images/Editor-Large.png#lightbox "Esempio di editor")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) |
|     |     |

## <a name="views-to-indicate-activity"></a>Visualizzazioni per indicare l'attività

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) usa un'animazione per indicare che l'applicazione è impegnata in un'attività di lunga durata senza fornire alcuna indicazione dello stato di avanzamento. La proprietà [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) controlla l'animazione.<br /><br />Se lo stato dell'attività è noto, utilizzare invece un [`ProgressBar`](#progressbar) .<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.ActivityIndicator) / [Guida](~/xamarin-forms/user-interface/activityindicator.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/) | [![Esempio di ActivityIndicator](views-images/ActivityIndicator.png "Esempio di ActivityIndicator")](views-images/ActivityIndicator-Large.png#lightbox "Esempio di ActivityIndicator")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) |
|     |     |

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) usa un'animazione per mostrare che l'applicazione sta procedendo attraverso un'attività di lunga durata. Impostare la proprietà [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) su valori compresi tra 0 e 1 per indicare lo stato di avanzamento.<br /><br />Se lo stato dell'attività non è noto, utilizzare invece un [`ActivityIndicator`](#activityindicator) .<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.ProgressBar) / [Guida](~/xamarin-forms/user-interface/progressbar.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/) | [![Esempio di ProgressBar](views-images/ProgressBar.png "Esempio di ProgressBar")](views-images/ProgressBar-Large.png#lightbox "Esempio di ProgressBar")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Visualizzazioni che visualizzano le raccolte

### <a name="carouselview"></a>CarouselView

|     |     |
| --- | --- |
| [`CarouselView`](xref:Xamarin.Forms.CarouselView) Visualizza un elenco scorrevole di elementi di dati. Impostare la proprietà `ItemsSource` su una raccolta di oggetti e impostare la proprietà `ItemTemplate` su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che descriva la modalità di formattazione degli elementi. L'evento `CurrentItemChanged` segnala che l'elemento attualmente visualizzato è stato modificato, disponibile come proprietà `CurrentItem`.<br /><br />[Guida](~/xamarin-forms/user-interface/carouselview/index.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/) | [![Esempio di CarouselView](views-images/CarouselView.png "Esempio di CarouselView")](views-images/CarouselView-Large.png#lightbox "Esempio di CarouselView")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselViewDemoPage.cs) |
|     |     |

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| [`CollectionView`](xref:Xamarin.Forms.CollectionView) Visualizza un elenco scorrevole di elementi di dati selezionabili, utilizzando specifiche del layout diverse. Mira a offrire un'alternativa più flessibile ed efficiente ai [`ListView`](xref:Xamarin.Forms.ListView). Impostare la proprietà `ItemsSource` su una raccolta di oggetti e impostare la proprietà `ItemTemplate` su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che descriva la modalità di formattazione degli elementi. L'evento `SelectionChanged` segnala che è stata effettuata una selezione, disponibile come proprietà `SelectedItem`.<br /><br />[Guida](~/xamarin-forms/user-interface/collectionview/index.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/) | [![Esempio di CollectionView](views-images/CollectionView.png "Esempio di CollectionView")](views-images/CollectionView-Large.png#lightbox "Esempio di CollectionView")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs) |
|     |     |

### <a name="indicatorview"></a>IndicatorView

|     |     |
| --- | --- |
| `IndicatorView` Visualizza gli indicatori che rappresentano il numero di elementi in una `CarouselView`. Impostare la proprietà `CarouselView.IndicatorView` sull'oggetto `IndicatorView` per visualizzare gli indicatori per il `CarouselView`. <br /><br />[Guida](~/xamarin-forms/user-interface/indicatorview.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/) | [![Esempio di IndicatorView](views-images/IndicatorView.png "Esempio di IndicatorView")](views-images/IndicatorView-Large.png#lightbox "Esempio di IndicatorView")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/IndicatorViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/IndicatorViewDemoPage.cs) |
|     |     |

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView) deriva da [`ItemsView`](xref:Xamarin.Forms.ItemsView`1) e visualizza un elenco scorrevole di elementi di dati selezionabili. Impostare la proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) su una raccolta di oggetti e impostare la proprietà [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che descriva la modalità di formattazione degli elementi. L'evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) segnala che è stata effettuata una selezione, disponibile come proprietà [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) .<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.ListView) / [Guida](~/xamarin-forms/user-interface/listview/index.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview/) | [![Esempio di ListView](views-images/ListView.png "Esempio di ListView")](views-images/ListView-Large.png#lightbox "Esempio di ListView")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) |
|     |     |

### <a name="picker"></a>Selezione

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker) Visualizza un elemento selezionato da un elenco di stringhe di testo e consente di selezionare tale elemento quando la visualizzazione viene toccata. Impostare la proprietà [`Items`](xref:Xamarin.Forms.Picker.Items) su un elenco di stringhe o la proprietà [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) su una raccolta di oggetti. L'evento [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) viene generato quando viene selezionato un elemento.<br /><br />Il `Picker` Visualizza l'elenco degli elementi solo quando viene selezionato. Usare un [`ListView`](#listview) o [`TableView`](#tableview) per un elenco scorrevole che rimane nella pagina.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Picker) / [Guida](~/xamarin-forms/user-interface/picker/index.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo) | [![Esempio di selezione](views-images/Picker.png "Esempio di selezione")](views-images/Picker-Large.png#lightbox "Esempio di selezione")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) |
|     |     |

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView) Visualizza un elenco di righe di tipo [`Cell`](xref:Xamarin.Forms.Cell) con intestazioni e sottotitoli facoltativi. Impostare la proprietà [`Root`](xref:Xamarin.Forms.TableView.Root) su un oggetto di tipo [`TableRoot`](xref:Xamarin.Forms.TableRoot)e aggiungere [`TableSection`](xref:Xamarin.Forms.TableSection) oggetti a tale `TableRoot`. Ogni `TableSection` è una raccolta di oggetti `Cell`.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.TableView) / [Guida](~/xamarin-forms/user-interface/tableview.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview) | [![Esempio di TableView](views-images/TableView.png "Esempio di TableView")](views-images/TableView-Large.png#lightbox "Esempio di TableView")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewFormDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewFormDemoPage.cs) |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Novell. Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Esempi di Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentazione per le API di Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
