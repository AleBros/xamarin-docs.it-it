---
title: Viste Novell. Forms
description: Le visualizzazioni Novell. Forms sono i blocchi predefiniti delle interfacce utente per dispositivi mobili multipiattaforma. Questo articolo elenca le visualizzazioni incluse in Novell. Forms.
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/16/2020
ms.openlocfilehash: 4164941e4ed8d484699e52eece86085f1c761c91
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82516921"
---
# <a name="xamarinforms-views"></a>Viste Novell. Forms

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Le visualizzazioni Novell. Forms sono i blocchi predefiniti delle interfacce utente per dispositivi mobili multipiattaforma._

Le visualizzazioni sono oggetti dell'interfaccia utente, ad esempio etichette, pulsanti e dispositivi di scorrimento, comunemente noti come *controlli* o *widget* in altri ambienti di programmazione grafica. Le visualizzazioni supportate da Novell. Forms derivano tutte [`View`](xref:Xamarin.Forms.View) dalla classe. Possono essere suddivise in diverse categorie:

## <a name="views-for-presentation"></a>Visualizzazioni per la presentazione

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView)Visualizza un rettangolo a tinta unita [`Color`](xref:Xamarin.Forms.BoxView.Color) colorato dalla proprietà. `BoxView`ha una richiesta di dimensioni predefinita di 40x40. Per altre dimensioni, assegnare le [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) proprietà [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) e.<br /><br />[API Documentation](xref:Xamarin.Forms.BoxView) / [Guide](~/xamarin-forms/user-interface/boxview.md)Guida / alla documentazione dell'API[esempio 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration), [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/), [4](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife), [5](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)e [6](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) | [![Esempio di BoxView](views-images/BoxView.png "Esempio di BoxView")](views-images/BoxView-Large.png#lightbox "Esempio di BoxView")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="expander"></a>Expander

|     |     |
| --- | --- |
| `Expander`fornisce un contenitore espandibile per ospitare qualsiasi contenuto ed è costituito da un'intestazione e da un contenuto. Impostare la `Header` proprietà su un [`View`](xref:Xamarin.Forms.View) oggetto che verrà visualizzato come intestazione e la `Content` proprietà su un oggetto [`View`](xref:Xamarin.Forms.View) che verrà visualizzato quando l'intestazione viene espansa con un tocco.<br /><br />[Guide](~/xamarin-forms/user-interface/expander.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos) di guida | [![Esempio di espansore](views-images/Expander.png "Esempio di espansore")](views-images/Expander-Large.png#lightbox "Esempio di espansore")<br /> [Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ExpanderDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ExpanderDemoPage.xaml) |
|     |     |

### <a name="label"></a>Label

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label)Visualizza le stringhe di testo a riga singola o i blocchi di testo a più righe, con formattazione costante o variabile. Impostare la [`Text`](xref:Xamarin.Forms.Label.Text) proprietà su una stringa per la formattazione costante oppure impostare la [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) proprietà su un [`FormattedString`](xref:Xamarin.Forms.FormattedString) oggetto per la formattazione delle variabili.<br /><br />[API Documentation](xref:Xamarin.Forms.Label) / [Guide](~/xamarin-forms/user-interface/text/label.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) di guida alla documentazione API | [![Esempio di etichetta](views-images/Label.png "Esempio di etichetta")](views-images/Label-Large.png#lightbox "Esempio di etichetta")<br /> [Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>Image

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image)Visualizza una bitmap. Le bitmap possono essere scaricate sul Web, incorporate come risorse nel progetto comune o nei progetti di piattaforma oppure create usando un `Stream` oggetto .NET.<br /><br />[API Documentation](xref:Xamarin.Forms.Image) / [Guide](~/xamarin-forms/user-interface/images.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages) di guida alla documentazione API | [![Esempio di immagine](views-images/Image.png "Esempio di immagine")](views-images/Image-Large.png#lightbox "Esempio di immagine")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="map"></a>Mappa

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map)Visualizza una mappa. È necessario installare il pacchetto NuGet **Novell. Forms. Maps** . Android e piattaforma UWP (Universal Windows Platform) richiedono una chiave di autorizzazione map.<br /><br />[API Documentation](xref:Xamarin.Forms.Maps.Map) / [Guide](~/xamarin-forms/user-interface/map/index.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps/) di guida alla documentazione API | [![Esempio di mappa](views-images/Map.png "Esempio di mappa")](views-images/Map-Large.png#lightbox "Esempio di mappa")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

### <a name="mediaelement"></a>MediaElement

|     |     |
| --- | --- |
| [`MediaElement`](xref:Xamarin.Forms.MediaElement)Riproduce video o audio. Il supporto può essere riprodotto da un URL o da un file locale, a seconda [`Source`](xref:Xamarin.Forms.MediaElement.Source) che la proprietà sia impostata [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource) su o [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource).<br /><br />[API Documentation](xref:Xamarin.Forms.MediaElement) / [Guide](~/xamarin-forms/user-interface/mediaelement.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos) di guida alla documentazione API | [![Esempio di MediaElement](views-images/MediaElement.png "Esempio di MediaElement")](views-images/MediaElement-Large.png#lightbox "Esempio di MediaElement")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MediaElementDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MediaElementDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView)Visualizza la grafica OpenGL nei progetti iOS e Android. Il piattaforma UWP (Universal Windows Platform) non è supportato. Per i progetti iOS e Android è necessario un riferimento all'assembly **OpenTK-1,0** o **OpenTK** versione 1.0.0.0. `OpenGLView`è più facile da usare in un progetto condiviso. Se usato in una libreria di .NET Standard, sarà necessario anche un servizio di dipendenza (come illustrato nel codice di esempio).<br /><br />Si tratta dell'unica funzionalità grafica incorporata in Novell. Forms, ma un'applicazione Novell. Forms può anche eseguire il [`SkiaSharp`](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)rendering della [`UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md)grafica usando, o.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![Esempio di OpenGLView](views-images/OpenGLView.png "Esempio di OpenGLView")](views-images/OpenGLView-Large.png#lightbox "Esempio di OpenGLView")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView)Visualizza le pagine Web o il contenuto HTML, a seconda [`Source`](xref:Xamarin.Forms.WebView.Source) che la proprietà sia impostata [`UriWebViewSource`](xref:Xamarin.Forms.UrlWebViewSource) su un [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource) oggetto o.<br /><br />[API Documentation](xref:Xamarin.Forms.WebView) / [Guide](~/xamarin-forms/user-interface/webview.md)Guida / alla documentazione dell'API[esempio 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview) e [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) | [![Esempio di WebView](views-images/WebView.png "Esempio di WebView")](views-images/WebView-Large.png#lightbox "Esempio di WebView")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>Visualizzazioni che avviano i comandi

### <a name="button"></a>Button

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button)è un oggetto rettangolare che Visualizza il testo e genera un [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento quando viene premuto.<br /><br />[API Documentation](xref:Xamarin.Forms.Button) / [Guide](~/xamarin-forms/user-interface/button.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos/) di guida alla documentazione API | [![Esempio di pulsante](views-images/Button.png "Esempio di pulsante")](views-images/Button-Large.png#lightbox "Esempio di pulsante")<br /> [Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton`è un oggetto rettangolare che visualizza un'immagine e che genera un `Clicked` evento quando viene premuto.<br /><br /> [Guide](~/xamarin-forms/user-interface/imagebutton.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) di guida | [![Esempio di ImageButton](views-images/ImageButton.png "Esempio di ImageButton")](views-images/ImageButton-Large.png#lightbox "Esempio di ImageButton")<br /> [Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs) |
|     |     |

### <a name="radiobutton"></a>RadioButton

|     |     |
| --- | --- |
| `RadioButton`consente la selezione di un'opzione da un set e genera un `CheckedChanged` evento quando viene eseguita la selezione.<br /><br />[Guide](~/xamarin-forms/user-interface/radiobutton.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/) di guida | [![Esempio di RadioButton](views-images/RadioButton.png "Esempio di RadioButton")](views-images/RadioButton-Large.png#lightbox "Esempio di RadioButton")<br /> [Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RadioButtonDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RadioButtonDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RadioButtonDemoPage.xaml.cs) |
|     |     |

### <a name="refreshview"></a>RefreshView

|     |     |
| --- | --- |
| `RefreshView`è un controllo contenitore che fornisce funzionalità pull-to-refresh per il contenuto scorrevole. Il `ICommand` valore definito dalla `Command` proprietà viene eseguito quando viene attivato un aggiornamento e la `IsRefreshing` proprietà indica lo stato corrente del controllo.<br /><br /> [Guide](~/xamarin-forms/user-interface/refreshview.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) di guida | [![Esempio di RefreshView](views-images/RefreshView.png "Esempio di RefreshView")](views-images/RefreshView-Large.png#lightbox "Esempio di RefreshView")<br /> [Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RefreshViewDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar)Visualizza un'area in cui l'utente deve digitare una stringa di testo e un pulsante (o un tasto della tastiera) che segnala all'applicazione di eseguire una ricerca. La [`Text`](xref:Xamarin.Forms.InputView.Text) proprietà fornisce l'accesso al testo e l' [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) evento indica che è stato premuto il pulsante.<br /><br />[API Documentation](xref:Xamarin.Forms.SearchBar) / [Guide](~/xamarin-forms/user-interface/searchbar.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/) di guida alla documentazione API | [![Esempio di SearchBar](views-images/SearchBar.png "Esempio di SearchBar")](views-images/SearchBar-Large.png#lightbox "Esempio di SearchBar")<br /> [Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

### <a name="swipeview"></a>SwipeView

|     |     |
| --- | --- |
| `SwipeView`è un controllo contenitore che esegue il wrapping di un elemento di contenuto e fornisce voci di menu di scelta rapida che vengono rivelate da un gesto di scorrimento. Ogni voce di menu è rappresentata `SwipeItem`da un oggetto, `Command` che dispone di una proprietà `ICommand` che esegue un oggetto quando viene toccato l'elemento.<br /><br /> [Guide](~/xamarin-forms/user-interface/swipeview.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) di guida | [![Esempio di SwipeView](views-images/SwipeView.png "Esempio di SwipeView")](views-images/SwipeView-Large.png#lightbox "Esempio di SwipeView")<br /> [Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwipeViewDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Visualizzazioni per impostare i valori

### <a name="checkbox"></a>CheckBox

|     |     |
| --- | --- |
| `CheckBox`consente all'utente di selezionare un valore booleano usando un tipo di pulsante che può essere selezionato o vuoto. La `IsChecked` proprietà è lo stato di `CheckBox`e l' `CheckedChanged` evento viene generato quando lo stato cambia.<br /><br />Documentazione dell'API/[esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos) di [Guida](~/xamarin-forms/user-interface/checkbox.md) /  | [![Esempio di casella di controllo](views-images/CheckBox.png "Esempio di casella di controllo")](views-images/CheckBox-Large.png#lightbox "Esempio di casella di controllo")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CheckBoxPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CheckBoxPage.xaml) |
|     |     |

### <a name="slider"></a>Slider

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider)consente all'utente di selezionare un `double` valore da un intervallo continuo specificato con le [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) proprietà [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) e.<br /><br />[API Documentation](xref:Xamarin.Forms.Slider) / [Guide](~/xamarin-forms/user-interface/slider.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) di guida alla documentazione API | [![Esempio di dispositivo di scorrimento](views-images/Slider.png "Esempio di dispositivo di scorrimento")](views-images/Slider-Large.png#lightbox "Esempio di dispositivo di scorrimento")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>Gestore di istruzioni

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper)consente all'utente di selezionare un `double` valore da un intervallo di valori incrementali specificato con le [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum)proprietà [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum), e [`Increment`](xref:Xamarin.Forms.Stepper.Increment) .<br /><br />[API Documentation](xref:Xamarin.Forms.Stepper)  / [Guide](~/xamarin-forms/user-interface/stepper.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) di guida alla documentazione API | [![Esempio di stepper](views-images/Stepper.png "Esempio di stepper")](views-images/Stepper-Large.png#lightbox "Esempio di stepper")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>Opzione

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch)assume la forma di un'opzione di attivazione/disattivazione per consentire all'utente di selezionare un valore booleano. La [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) proprietà è lo stato dell'opzione e l' [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) evento viene generato quando lo stato cambia.<br /><br />[API Documentation](xref:Xamarin.Forms.Switch) / [Guide](~/xamarin-forms/user-interface/switch.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/) di guida alla documentazione API | [![Esempio di switch](views-images/Switch.png "Esempio di switch")](views-images/Switch-Large.png#lightbox "Esempio di switch")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker)consente all'utente di selezionare una data con il selettore di data della piattaforma. Impostare un intervallo di date consentite con [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) le [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) proprietà e. La [`Date`](xref:Xamarin.Forms.DatePicker.Date) proprietà è la data selezionata e l'evento [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) viene generato quando la proprietà viene modificata.<br /><br />[API Documentation](xref:Xamarin.Forms.DatePicker) / [Guide](~/xamarin-forms/user-interface/datepicker.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) di guida alla documentazione API | [![Esempio di DatePicker](views-images/DatePicker.png "Esempio di DatePicker")](views-images/DatePicker-Large.png#lightbox "Esempio di DatePicker")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker)consente all'utente di selezionare un'ora con la selezione ora piattaforma. La [`Time`](xref:Xamarin.Forms.TimePicker.Time) proprietà è l'ora selezionata. Un'applicazione può monitorare le modifiche nella `Time` proprietà installando un gestore per l' [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento.<br /><br />[API Documentation](xref:Xamarin.Forms.TimePicker) / [Guide](~/xamarin-forms/user-interface/timepicker.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) di guida alla documentazione API | [![Esempio di TimePicker](views-images/TimePicker.png "Esempio di TimePicker")](views-images/TimePicker-Large.png#lightbox "Esempio di TimePicker")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>Visualizzazioni per modificare il testo

Queste due classi derivano dalla [`InputView`](xref:Xamarin.Forms.InputView) classe, che definisce la [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) proprietà.

### <a name="entry"></a>Voce

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry)consente all'utente di immettere e modificare una singola riga di testo. Il testo è disponibile come [`Text`](xref:Xamarin.Forms.InputView.Text) proprietà e gli [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) eventi e [`Completed`](xref:Xamarin.Forms.Entry.Completed) vengono generati quando il testo viene modificato o l'utente segnala il completamento toccando il tasto INVIO.<br /><br />Usare un [`Editor`](#editor) per l'immissione e la modifica di più righe di testo.<br /><br />[API Documentation](xref:Xamarin.Forms.Entry) / [Guide](~/xamarin-forms/user-interface/text/entry.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) di guida alla documentazione API | [![Esempio di voce](views-images/Entry.png "Esempio di voce")](views-images/Entry-Large.png#lightbox "Esempio di voce")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

### <a name="editor"></a>Editor

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor)consente all'utente di immettere e modificare più righe di testo. Il testo è disponibile come [`Text`](xref:Xamarin.Forms.InputView.Text) proprietà e gli [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) eventi e [`Completed`](xref:Xamarin.Forms.Editor.Completed) vengono generati quando il testo viene modificato o l'utente segnala il completamento.<br /><br />Utilizzare una [`Entry`](#entry) visualizzazione per l'immissione e la modifica di una singola riga di testo.<br /><br />[API Documentation](xref:Xamarin.Forms.Editor) / [Guide](~/xamarin-forms/user-interface/text/editor.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) di guida alla documentazione API | [![Esempio di voce](views-images/Editor.png "Esempio di editor")](views-images/Editor-Large.png#lightbox "Esempio di editor")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>Visualizzazioni per indicare l'attività

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)Usa un'animazione per indicare che l'applicazione è impegnata in un'attività di lunga durata senza fornire alcuna indicazione dello stato di avanzamento. La [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) proprietà controlla l'animazione.<br /><br />Se lo stato dell'attività è noto, usare [`ProgressBar`](#progressbar) invece.<br /><br />[API Documentation](xref:Xamarin.Forms.ActivityIndicator) / [Guide](~/xamarin-forms/user-interface/activityindicator.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/) di guida alla documentazione API | [![Esempio di ActivityIndicator](views-images/ActivityIndicator.png "Esempio di ActivityIndicator")](views-images/ActivityIndicator-Large.png#lightbox "Esempio di ActivityIndicator")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)Usa un'animazione per mostrare che l'applicazione sta avanzando attraverso un'attività di lunga durata. Impostare la [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) proprietà su valori compresi tra 0 e 1 per indicare lo stato di avanzamento.<br /><br />Se lo stato dell'attività non è noto, utilizzare [`ActivityIndicator`](#activityindicator) invece.<br /><br />[API Documentation](xref:Xamarin.Forms.ProgressBar) / [Guide](~/xamarin-forms/user-interface/progressbar.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/) di guida alla documentazione API | [![Esempio di ProgressBar](views-images/ProgressBar.png "Esempio di ProgressBar")](views-images/ProgressBar-Large.png#lightbox "Esempio di ProgressBar")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Visualizzazioni che visualizzano le raccolte

### <a name="carouselview"></a>CarouselView

|     |     |
| --- | --- |
| [`CarouselView`](xref:Xamarin.Forms.CarouselView)consente di visualizzare un elenco scorrevole di elementi di dati. Impostare la `ItemsSource` proprietà su una raccolta di oggetti e impostare la `ItemTemplate` proprietà su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto che descrive la modalità di formattazione degli elementi. L' `CurrentItemChanged` evento segnala che l'elemento attualmente visualizzato è stato modificato, che è disponibile come `CurrentItem` proprietà.<br /><br />[Guide](~/xamarin-forms/user-interface/carouselview/index.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/) di guida | [![Esempio di CarouselView](views-images/CarouselView.png "Esempio di CarouselView")](views-images/CarouselView-Large.png#lightbox "Esempio di CarouselView")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselViewDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselViewDemoPage.xaml) |
|     |     |

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| [`CollectionView`](xref:Xamarin.Forms.CollectionView)consente di visualizzare un elenco scorrevole di elementi di dati selezionabili, utilizzando specifiche del layout diverse. Mira a offrire un'alternativa più flessibile ed efficiente a [`ListView`](xref:Xamarin.Forms.ListView). Impostare la `ItemsSource` proprietà su una raccolta di oggetti e impostare la `ItemTemplate` proprietà su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto che descrive la modalità di formattazione degli elementi. L' `SelectionChanged` evento segnala che è stata effettuata una selezione, disponibile come `SelectedItem` proprietà.<br /><br />[Guide](~/xamarin-forms/user-interface/collectionview/index.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/) di guida | [![Esempio di CollectionView](views-images/CollectionView.png "Esempio di CollectionView")](views-images/CollectionView-Large.png#lightbox "Esempio di CollectionView")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) |
|     |     |

### <a name="indicatorview"></a>IndicatorView

|     |     |
| --- | --- |
| `IndicatorView`Visualizza gli indicatori che rappresentano il numero di elementi in `CarouselView`un oggetto. Impostare la `CarouselView.IndicatorView` proprietà sull' `IndicatorView` oggetto per visualizzare gli indicatori per l' `CarouselView`oggetto. <br /><br />[Guide](~/xamarin-forms/user-interface/indicatorview.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/) di guida | [![Esempio di IndicatorView](views-images/IndicatorView.png "Esempio di IndicatorView")](views-images/IndicatorView-Large.png#lightbox "Esempio di IndicatorView")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/IndicatorViewDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/IndicatorViewDemoPage.xaml) |
|     |     |

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView)deriva da [`ItemsView`](xref:Xamarin.Forms.ItemsView`1) e visualizza un elenco scorrevole di elementi di dati selezionabili. Impostare la [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) proprietà su una raccolta di oggetti e impostare la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) proprietà su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto che descrive la modalità di formattazione degli elementi. L' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento segnala che è stata effettuata una selezione, disponibile come [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) proprietà.<br /><br />[API Documentation](xref:Xamarin.Forms.ListView) / [Guide](~/xamarin-forms/user-interface/listview/index.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview/) di guida alla documentazione API | [![Esempio di ListView](views-images/ListView.png "Esempio di ListView")](views-images/ListView-Large.png#lightbox "Esempio di ListView")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

### <a name="picker"></a>Selezione

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker)Visualizza un elemento selezionato da un elenco di stringhe di testo e consente di selezionare tale elemento quando la visualizzazione viene toccata. Impostare la [`Items`](xref:Xamarin.Forms.Picker.Items) proprietà su un elenco di stringhe o la [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) proprietà su una raccolta di oggetti. L' [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento viene generato quando viene selezionato un elemento.<br /><br />`Picker` Consente di visualizzare l'elenco degli elementi solo quando viene selezionato. Usare [`ListView`](#listview) o [`TableView`](#tableview) per un elenco scorrevole che rimane nella pagina.<br /><br />[API Documentation](xref:Xamarin.Forms.Picker) / [Guide](~/xamarin-forms/user-interface/picker/index.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo) di guida alla documentazione API | [![Esempio di selezione](views-images/Picker.png "Esempio di selezione")](views-images/Picker-Large.png#lightbox "Esempio di selezione")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView)Visualizza un elenco di righe di tipo [`Cell`](xref:Xamarin.Forms.Cell) con intestazioni e sottointestazioni facoltative. Impostare la [`Root`](xref:Xamarin.Forms.TableView.Root) proprietà su un oggetto di tipo [`TableRoot`](xref:Xamarin.Forms.TableRoot)e aggiungere [`TableSection`](xref:Xamarin.Forms.TableSection) oggetti a tale `TableRoot`oggetto. Ognuno `TableSection` è una raccolta di `Cell` oggetti.<br /><br />[API Documentation](xref:Xamarin.Forms.TableView) / [Guide](~/xamarin-forms/user-interface/tableview.md) / [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview) di guida alla documentazione API | [![Esempio di TableView](views-images/TableView.png "Esempio di TableView")](views-images/TableView-Large.png#lightbox "Esempio di TableView")<br />[Codice C# per questa](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewFormDemoPage.cs) / pagina[XAML pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewFormDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Novell. Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Esempi di Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentazione per le API di Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
