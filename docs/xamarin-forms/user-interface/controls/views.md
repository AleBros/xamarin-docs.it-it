---
title: Xamarin.FormsVisualizzazioni
description: Xamarin.Formsle visualizzazioni sono i blocchi predefiniti delle interfacce utente per dispositivi mobili multipiattaforma. Questo articolo elenca le visualizzazioni incluse in Xamarin.Forms .
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8c15626e405645d28a785c32d276860f9751ea25
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84132379"
---
# <a name="xamarinforms-views"></a>Xamarin.FormsVisualizzazioni

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Le visualizzazioni Novell. Forms sono i blocchi predefiniti delle interfacce utente per dispositivi mobili multipiattaforma._

Le visualizzazioni sono oggetti dell'interfaccia utente, ad esempio etichette, pulsanti e dispositivi di scorrimento, comunemente noti come *controlli* o *widget* in altri ambienti di programmazione grafica. Le visualizzazioni supportate da Xamarin.Forms tutti derivano dalla [`View`](xref:Xamarin.Forms.View) classe. Possono essere suddivise in diverse categorie:

## <a name="views-for-presentation"></a>Visualizzazioni per la presentazione

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView)Visualizza un rettangolo a tinta unita colorato dalla [`Color`](xref:Xamarin.Forms.BoxView.Color) Proprietà. `BoxView`ha una richiesta di dimensioni predefinita di 40x40. Per altre dimensioni, assegnare le [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) proprietà e.<br /><br />[Documentazione API](xref:Xamarin.Forms.BoxView)  /  [Guida](~/xamarin-forms/user-interface/boxview.md)  /  di [Esempio 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration), [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/), [4](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife), [5](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)e [6](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) | [![Esempio di BoxView](views-images/BoxView.png "Esempio di BoxView")](views-images/BoxView-Large.png#lightbox "Esempio di BoxView")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="expander"></a>Expander

|     |     |
| --- | --- |
| `Expander`fornisce un contenitore espandibile per ospitare qualsiasi contenuto ed è costituito da un'intestazione e da un contenuto. Impostare la `Header` proprietà su un oggetto [`View`](xref:Xamarin.Forms.View) che verrà visualizzato come intestazione e la `Content` proprietà su un oggetto [`View`](xref:Xamarin.Forms.View) che verrà visualizzato quando l'intestazione viene espansa con un tocco.<br /><br />[Guida](~/xamarin-forms/user-interface/expander.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos) di | [![Esempio di espansore](views-images/Expander.png "Esempio di espansore")](views-images/Expander-Large.png#lightbox "Esempio di espansore")<br /> [Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ExpanderDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ExpanderDemoPage.xaml) |
|     |     |

### <a name="label"></a>Etichetta

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label)Visualizza le stringhe di testo a riga singola o i blocchi di testo a più righe, con formattazione costante o variabile. Impostare la [`Text`](xref:Xamarin.Forms.Label.Text) proprietà su una stringa per la formattazione costante oppure impostare la [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) proprietà su un [`FormattedString`](xref:Xamarin.Forms.FormattedString) oggetto per la formattazione delle variabili.<br /><br />[Documentazione API](xref:Xamarin.Forms.Label)  /  [Guida](~/xamarin-forms/user-interface/text/label.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) di | [![Esempio di etichetta](views-images/Label.png "Esempio di etichetta")](views-images/Label-Large.png#lightbox "Esempio di etichetta")<br /> [Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>Immagine

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image)Visualizza una bitmap. Le bitmap possono essere scaricate sul Web, incorporate come risorse nel progetto comune o nei progetti di piattaforma oppure create usando un `Stream` oggetto .NET.<br /><br />[Documentazione API](xref:Xamarin.Forms.Image)  /  [Guida](~/xamarin-forms/user-interface/images.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages) di | [![Esempio di immagine](views-images/Image.png "Esempio di immagine")](views-images/Image-Large.png#lightbox "Esempio di immagine")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="map"></a>Mappa

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map)Visualizza una mappa. Oggetto ** Xamarin.Forms . Maps** è necessario installare il pacchetto NuGet. Android e piattaforma UWP (Universal Windows Platform) richiedono una chiave di autorizzazione map.<br /><br />[Documentazione API](xref:Xamarin.Forms.Maps.Map)  /  [Guida](~/xamarin-forms/user-interface/map/index.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps/) di | [![Esempio di mappa](views-images/Map.png "Esempio di mappa")](views-images/Map-Large.png#lightbox "Esempio di mappa")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

### <a name="mediaelement"></a>MediaElement

|     |     |
| --- | --- |
| [`MediaElement`](xref:Xamarin.Forms.MediaElement)Riproduce video o audio. Il supporto può essere riprodotto da un URL o da un file locale, a seconda che la [`Source`](xref:Xamarin.Forms.MediaElement.Source) proprietà sia impostata su [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource) o [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) .<br /><br />[Documentazione API](xref:Xamarin.Forms.MediaElement)  /  [Guida](~/xamarin-forms/user-interface/mediaelement.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos) di | [![Esempio di MediaElement](views-images/MediaElement.png "Esempio di MediaElement")](views-images/MediaElement-Large.png#lightbox "Esempio di MediaElement")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MediaElementDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MediaElementDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView)Visualizza la grafica OpenGL nei progetti iOS e Android. Il piattaforma UWP (Universal Windows Platform) non è supportato. Per i progetti iOS e Android è necessario un riferimento all'assembly **OpenTK-1,0** o **OpenTK** versione 1.0.0.0. `OpenGLView`è più facile da usare in un progetto condiviso. Se usato in una libreria di .NET Standard, sarà necessario anche un servizio di dipendenza (come illustrato nel codice di esempio).<br /><br />Si tratta dell'unica funzionalità grafica incorporata in Xamarin.Forms , ma un' Xamarin.Forms applicazione può anche eseguire il rendering della grafica usando [`SkiaSharp`](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) o [`UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md) .<br /><br />[Documentazione API](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![Esempio di OpenGLView](views-images/OpenGLView.png "Esempio di OpenGLView")](views-images/OpenGLView-Large.png#lightbox "Esempio di OpenGLView")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView)Visualizza le pagine Web o il contenuto HTML, a seconda che la [`Source`](xref:Xamarin.Forms.WebView.Source) proprietà sia impostata su un [`UriWebViewSource`](xref:Xamarin.Forms.UrlWebViewSource) [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource) oggetto o.<br /><br />[Documentazione API](xref:Xamarin.Forms.WebView)  /  [Guida](~/xamarin-forms/user-interface/webview.md)  /  di [Esempio 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview) e [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) | [![Esempio di WebView](views-images/WebView.png "Esempio di WebView")](views-images/WebView-Large.png#lightbox "Esempio di WebView")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>Visualizzazioni che avviano i comandi

### <a name="button"></a>Button

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button)è un oggetto rettangolare che Visualizza il testo e genera un [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento quando viene premuto.<br /><br />[Documentazione API](xref:Xamarin.Forms.Button)  /  [Guida](~/xamarin-forms/user-interface/button.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos/) di | [![Esempio di pulsante](views-images/Button.png "Esempio di pulsante")](views-images/Button-Large.png#lightbox "Esempio di pulsante")<br /> [Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton`è un oggetto rettangolare che visualizza un'immagine e che genera un `Clicked` evento quando viene premuto.<br /><br /> [Guida](~/xamarin-forms/user-interface/imagebutton.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) di | [![Esempio di ImageButton](views-images/ImageButton.png "Esempio di ImageButton")](views-images/ImageButton-Large.png#lightbox "Esempio di ImageButton")<br /> [Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs) |
|     |     |

### <a name="radiobutton"></a>RadioButton

|     |     |
| --- | --- |
| `RadioButton`consente la selezione di un'opzione da un set e genera un `CheckedChanged` evento quando viene eseguita la selezione.<br /><br />[Guida](~/xamarin-forms/user-interface/radiobutton.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/) di | [![Esempio di RadioButton](views-images/RadioButton.png "Esempio di RadioButton")](views-images/RadioButton-Large.png#lightbox "Esempio di RadioButton")<br /> [Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RadioButtonDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RadioButtonDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RadioButtonDemoPage.xaml.cs) |
|     |     |

### <a name="refreshview"></a>RefreshView

|     |     |
| --- | --- |
| `RefreshView`è un controllo contenitore che fornisce funzionalità pull-to-refresh per il contenuto scorrevole. Il valore `ICommand` definito dalla `Command` proprietà viene eseguito quando viene attivato un aggiornamento e la `IsRefreshing` proprietà indica lo stato corrente del controllo.<br /><br /> [Guida](~/xamarin-forms/user-interface/refreshview.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) di | [![Esempio di RefreshView](views-images/RefreshView.png "Esempio di RefreshView")](views-images/RefreshView-Large.png#lightbox "Esempio di RefreshView")<br /> [Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RefreshViewDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar)Visualizza un'area in cui l'utente deve digitare una stringa di testo e un pulsante (o un tasto della tastiera) che segnala all'applicazione di eseguire una ricerca. La [`Text`](xref:Xamarin.Forms.InputView.Text) proprietà fornisce l'accesso al testo e l' [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) evento indica che è stato premuto il pulsante.<br /><br />[Documentazione API](xref:Xamarin.Forms.SearchBar)  /  [Guida](~/xamarin-forms/user-interface/searchbar.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/) di | [![Esempio di SearchBar](views-images/SearchBar.png "Esempio di SearchBar")](views-images/SearchBar-Large.png#lightbox "Esempio di SearchBar")<br /> [Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

### <a name="swipeview"></a>SwipeView

|     |     |
| --- | --- |
| `SwipeView`è un controllo contenitore che esegue il wrapping di un elemento di contenuto e fornisce voci di menu di scelta rapida che vengono rivelate da un gesto di scorrimento. Ogni voce di menu è rappresentata da un oggetto `SwipeItem` , che dispone di una `Command` proprietà che esegue un oggetto `ICommand` quando viene toccato l'elemento.<br /><br /> [Guida](~/xamarin-forms/user-interface/swipeview.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) di | [![Esempio di SwipeView](views-images/SwipeView.png "Esempio di SwipeView")](views-images/SwipeView-Large.png#lightbox "Esempio di SwipeView")<br /> [Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwipeViewDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Visualizzazioni per impostare i valori

### <a name="checkbox"></a>CheckBox

|     |     |
| --- | --- |
| `CheckBox`consente all'utente di selezionare un valore booleano usando un tipo di pulsante che può essere selezionato o vuoto. La `IsChecked` proprietà è lo stato di `CheckBox` e l' `CheckedChanged` evento viene generato quando lo stato cambia.<br /><br />Documentazione dell'API [Guide](~/xamarin-forms/user-interface/checkbox.md)/  /  [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos) di guida | [![Esempio di casella di controllo](views-images/CheckBox.png "Esempio di casella di controllo")](views-images/CheckBox-Large.png#lightbox "Esempio di casella di controllo")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CheckBoxPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CheckBoxPage.xaml) |
|     |     |

### <a name="slider"></a>Slider

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider)consente all'utente di selezionare un `double` valore da un intervallo continuo specificato con le [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) proprietà e.<br /><br />[Documentazione API](xref:Xamarin.Forms.Slider)  /  [Guida](~/xamarin-forms/user-interface/slider.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) di | [![Esempio di dispositivo di scorrimento](views-images/Slider.png "Esempio di dispositivo di scorrimento")](views-images/Slider-Large.png#lightbox "Esempio di dispositivo di scorrimento")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>Gestore di istruzioni

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper)consente all'utente di selezionare un `double` valore da un intervallo di valori incrementali specificato con le [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) proprietà, e [`Increment`](xref:Xamarin.Forms.Stepper.Increment) .<br /><br />[Documentazione API](xref:Xamarin.Forms.Stepper)   /  [Guida](~/xamarin-forms/user-interface/stepper.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) di | [![Esempio di stepper](views-images/Stepper.png "Esempio di stepper")](views-images/Stepper-Large.png#lightbox "Esempio di stepper")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>Opzione

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch)assume la forma di un'opzione di attivazione/disattivazione per consentire all'utente di selezionare un valore booleano. La [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) proprietà è lo stato dell'opzione e l' [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) evento viene generato quando lo stato cambia.<br /><br />[Documentazione API](xref:Xamarin.Forms.Switch)  /  [Guida](~/xamarin-forms/user-interface/switch.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/) di | [![Esempio di switch](views-images/Switch.png "Esempio di switch")](views-images/Switch-Large.png#lightbox "Esempio di switch")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker)consente all'utente di selezionare una data con il selettore di data della piattaforma. Impostare un intervallo di date consentite con [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) le [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) proprietà e. La [`Date`](xref:Xamarin.Forms.DatePicker.Date) proprietà è la data selezionata e l' [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) evento viene generato quando la proprietà viene modificata.<br /><br />[Documentazione API](xref:Xamarin.Forms.DatePicker)  /  [Guida](~/xamarin-forms/user-interface/datepicker.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) di | [![Esempio di DatePicker](views-images/DatePicker.png "Esempio di DatePicker")](views-images/DatePicker-Large.png#lightbox "Esempio di DatePicker")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker)consente all'utente di selezionare un'ora con la selezione ora piattaforma. La [`Time`](xref:Xamarin.Forms.TimePicker.Time) proprietà è l'ora selezionata. Un'applicazione può monitorare le modifiche nella `Time` Proprietà installando un gestore per l' [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento.<br /><br />[Documentazione API](xref:Xamarin.Forms.TimePicker)  /  [Guida](~/xamarin-forms/user-interface/timepicker.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) di | [![Esempio di TimePicker](views-images/TimePicker.png "Esempio di TimePicker")](views-images/TimePicker-Large.png#lightbox "Esempio di TimePicker")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>Visualizzazioni per modificare il testo

Queste due classi derivano dalla [`InputView`](xref:Xamarin.Forms.InputView) classe, che definisce la [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) Proprietà.

### <a name="entry"></a>Voce

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry)consente all'utente di immettere e modificare una singola riga di testo. Il testo è disponibile come [`Text`](xref:Xamarin.Forms.InputView.Text) proprietà e gli [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) [`Completed`](xref:Xamarin.Forms.Entry.Completed) eventi e vengono generati quando il testo viene modificato o l'utente segnala il completamento toccando il tasto INVIO.<br /><br />Usare un [`Editor`](#editor) per l'immissione e la modifica di più righe di testo.<br /><br />[Documentazione API](xref:Xamarin.Forms.Entry)  /  [Guida](~/xamarin-forms/user-interface/text/entry.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) di | [![Esempio di voce](views-images/Entry.png "Esempio di voce")](views-images/Entry-Large.png#lightbox "Esempio di voce")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

### <a name="editor"></a>Editor

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor)consente all'utente di immettere e modificare più righe di testo. Il testo è disponibile come [`Text`](xref:Xamarin.Forms.InputView.Text) proprietà e gli [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) [`Completed`](xref:Xamarin.Forms.Editor.Completed) eventi e vengono generati quando il testo viene modificato o l'utente segnala il completamento.<br /><br />Utilizzare una [`Entry`](#entry) visualizzazione per l'immissione e la modifica di una singola riga di testo.<br /><br />[Documentazione API](xref:Xamarin.Forms.Editor)  /  [Guida](~/xamarin-forms/user-interface/text/editor.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) di | [![Esempio di voce](views-images/Editor.png "Esempio di editor")](views-images/Editor-Large.png#lightbox "Esempio di editor")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>Visualizzazioni per indicare l'attività

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)Usa un'animazione per indicare che l'applicazione è impegnata in un'attività di lunga durata senza fornire alcuna indicazione dello stato di avanzamento. La [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) proprietà controlla l'animazione.<br /><br />Se lo stato dell'attività è noto, usare [`ProgressBar`](#progressbar) invece.<br /><br />[Documentazione API](xref:Xamarin.Forms.ActivityIndicator)  /  [Guida](~/xamarin-forms/user-interface/activityindicator.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/) di | [![Esempio di ActivityIndicator](views-images/ActivityIndicator.png "Esempio di ActivityIndicator")](views-images/ActivityIndicator-Large.png#lightbox "Esempio di ActivityIndicator")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)Usa un'animazione per mostrare che l'applicazione sta avanzando attraverso un'attività di lunga durata. Impostare la [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) proprietà su valori compresi tra 0 e 1 per indicare lo stato di avanzamento.<br /><br />Se lo stato dell'attività non è noto, utilizzare [`ActivityIndicator`](#activityindicator) invece.<br /><br />[Documentazione API](xref:Xamarin.Forms.ProgressBar)  /  [Guida](~/xamarin-forms/user-interface/progressbar.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/) di | [![Esempio di ProgressBar](views-images/ProgressBar.png "Esempio di ProgressBar")](views-images/ProgressBar-Large.png#lightbox "Esempio di ProgressBar")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Visualizzazioni che visualizzano le raccolte

### <a name="carouselview"></a>CarouselView

|     |     |
| --- | --- |
| [`CarouselView`](xref:Xamarin.Forms.CarouselView)consente di visualizzare un elenco scorrevole di elementi di dati. Impostare la `ItemsSource` proprietà su una raccolta di oggetti e impostare la `ItemTemplate` proprietà su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto che descrive la modalità di formattazione degli elementi. L' `CurrentItemChanged` evento segnala che l'elemento attualmente visualizzato è stato modificato, che è disponibile come `CurrentItem` Proprietà.<br /><br />[Guida](~/xamarin-forms/user-interface/carouselview/index.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/) di | [![Esempio di CarouselView](views-images/CarouselView.png "Esempio di CarouselView")](views-images/CarouselView-Large.png#lightbox "Esempio di CarouselView")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselViewDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselViewDemoPage.xaml) |
|     |     |

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| [`CollectionView`](xref:Xamarin.Forms.CollectionView)consente di visualizzare un elenco scorrevole di elementi di dati selezionabili, utilizzando specifiche del layout diverse. Mira a offrire un'alternativa più flessibile ed efficiente a [`ListView`](xref:Xamarin.Forms.ListView) . Impostare la `ItemsSource` proprietà su una raccolta di oggetti e impostare la `ItemTemplate` proprietà su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto che descrive la modalità di formattazione degli elementi. L' `SelectionChanged` evento segnala che è stata effettuata una selezione, disponibile come `SelectedItem` Proprietà.<br /><br />[Guida](~/xamarin-forms/user-interface/collectionview/index.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/) di | [![Esempio di CollectionView](views-images/CollectionView.png "Esempio di CollectionView")](views-images/CollectionView-Large.png#lightbox "Esempio di CollectionView")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) |
|     |     |

### <a name="indicatorview"></a>IndicatorView

|     |     |
| --- | --- |
| `IndicatorView`Visualizza gli indicatori che rappresentano il numero di elementi in un oggetto `CarouselView` . Impostare la `CarouselView.IndicatorView` proprietà sull' `IndicatorView` oggetto per visualizzare gli indicatori per l'oggetto `CarouselView` . <br /><br />[Guida](~/xamarin-forms/user-interface/indicatorview.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/) di | [![Esempio di IndicatorView](views-images/IndicatorView.png "Esempio di IndicatorView")](views-images/IndicatorView-Large.png#lightbox "Esempio di IndicatorView")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/IndicatorViewDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/IndicatorViewDemoPage.xaml) |
|     |     |

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView)deriva da [`ItemsView`](xref:Xamarin.Forms.ItemsView`1) e visualizza un elenco scorrevole di elementi di dati selezionabili. Impostare la [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) proprietà su una raccolta di oggetti e impostare la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) proprietà su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto che descrive la modalità di formattazione degli elementi. L' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento segnala che è stata effettuata una selezione, disponibile come [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) Proprietà.<br /><br />[Documentazione API](xref:Xamarin.Forms.ListView)  /  [Guida](~/xamarin-forms/user-interface/listview/index.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview/) di | [![Esempio di ListView](views-images/ListView.png "Esempio di ListView")](views-images/ListView-Large.png#lightbox "Esempio di ListView")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

### <a name="picker"></a>Selezione

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker)Visualizza un elemento selezionato da un elenco di stringhe di testo e consente di selezionare tale elemento quando la visualizzazione viene toccata. Impostare la [`Items`](xref:Xamarin.Forms.Picker.Items) proprietà su un elenco di stringhe o la [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) proprietà su una raccolta di oggetti. L' [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento viene generato quando viene selezionato un elemento.<br /><br />`Picker`Consente di visualizzare l'elenco degli elementi solo quando viene selezionato. Usare [`ListView`](#listview) o [`TableView`](#tableview) per un elenco scorrevole che rimane nella pagina.<br /><br />[Documentazione API](xref:Xamarin.Forms.Picker)  /  [Guida](~/xamarin-forms/user-interface/picker/index.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo) di | [![Esempio di selezione](views-images/Picker.png "Esempio di selezione")](views-images/Picker-Large.png#lightbox "Esempio di selezione")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView)Visualizza un elenco di righe di tipo [`Cell`](xref:Xamarin.Forms.Cell) con intestazioni e sottointestazioni facoltative. Impostare la [`Root`](xref:Xamarin.Forms.TableView.Root) proprietà su un oggetto di tipo [`TableRoot`](xref:Xamarin.Forms.TableRoot) e aggiungere [`TableSection`](xref:Xamarin.Forms.TableSection) oggetti a tale oggetto `TableRoot` . Ognuno `TableSection` è una raccolta di `Cell` oggetti.<br /><br />[Documentazione API](xref:Xamarin.Forms.TableView)  /  [Guida](~/xamarin-forms/user-interface/tableview.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview) di | [![Esempio di TableView](views-images/TableView.png "Esempio di TableView")](views-images/TableView-Large.png#lightbox "Esempio di TableView")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewFormDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewFormDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.FormsEsempio FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsCampioni](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentazione API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
