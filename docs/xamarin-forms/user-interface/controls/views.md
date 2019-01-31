---
title: Visualizzazioni di xamarin. Forms
description: Le visualizzazioni di xamarin. Forms sono i blocchi predefiniti di interfacce utente per dispositivi mobili multipiattaforma. Questo articolo elenca le viste che sono inclusi in xamarin. Forms.
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 13/11/2018
ms.openlocfilehash: f5e3c5dbadeeb3cc1c019707ce7aa106e4946e36
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292012"
---
# <a name="xamarinforms-views"></a>Visualizzazioni di xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/FormsGallery/)

_Le visualizzazioni di xamarin. Forms sono i blocchi predefiniti di interfacce utente per dispositivi mobili multipiattaforma._

Le visualizzazioni sono oggetti dell'interfaccia utente, ad esempio i dispositivi di scorrimento che sono comunemente noti come, pulsanti ed etichette *controlli* oppure *widget* in altri ambienti di programmazione grafiche. Le visualizzazioni supportate da xamarin. Forms derivano da tutte le [ `View` ](xref:Xamarin.Forms.View) classe. Possono essere suddivisi in diverse categorie:

## <a name="views-for-presentation"></a>Viste per la presentazione

### <a name="label"></a>Label

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label) Visualizza stringhe di testo a riga singola o multilinea blocchi di testo con formattazione costante o variabile. Impostare il [ `Text` ](xref:Xamarin.Forms.Label.Text) proprietà in una stringa di costanti di formattazione o set il [ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText) proprietà da un [ `FormattedString` ](xref:Xamarin.Forms.FormattedString) oggetto per la variabile la formattazione.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Label) / [Guida](~/xamarin-forms/user-interface/text/label.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/) | [![Assegnare un'etichetta di esempio](views-images/Label.png "etichettare riportato")](views-images/Label-Large.png#lightbox "assegnare un'etichetta di esempio")<br /> [Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>Image

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image) Consente di visualizzare una bitmap. Le bitmap possono essere scaricate tramite il Web, incorporati come risorse nel progetto comune o i progetti di piattaforma o create utilizzando .NET `Stream` oggetto.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Image) / [Guida](~/xamarin-forms/user-interface/images.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/) | [![Esempio di immagine](views-images/Image.png "esempio di immagine")](views-images/Image-Large.png#lightbox "immagine di esempio")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView) viene visualizzato un rettangolo pieno colorato in base il [ `Color` ](xref:Xamarin.Forms.BoxView.Color) proprietà. `BoxView` dispone di una richiesta di dimensioni predefinito di 40 x 40. Per gli altri formati, assegnare il [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) e [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) proprietà.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.BoxView) / [Guida](~/xamarin-forms/user-interface/boxview.md) / [campionare 1](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView), [2](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration), [3](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ColorListBox), [4 ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife), [5](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock), e [6](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock) | [![Esempio BoxView](views-images/BoxView.png "esempio BoxView")](views-images/BoxView-Large.png#lightbox "BoxView esempio")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView) Visualizza contenuto, HTML o pagine Web a seconda che il [ `Source` ](xref:Xamarin.Forms.WebView.Source) è impostata su un [ `UriWebViewSource` ](xref:Xamarin.Forms.UrlWebViewSource) o un [ `HtmlWebViewSource` ](xref:Xamarin.Forms.HtmlWebViewSource) oggetto.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.WebView) / [Guida](~/xamarin-forms/user-interface/webview.md) / [campionare 1](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/) e [2](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView) | [![Esempio di WebView](views-images/WebView.png "esempio WebView")](views-images/WebView-Large.png#lightbox "esempio WebView")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView) Visualizza la grafica OpenGL in progetti iOS e Android. Non vi è alcun supporto per la piattaforma Windows universale. I progetti iOS e Android richiedono un riferimento per il **OpenTK-1.0** assembly o il **OpenTK** assembly della versione 1.0.0.0. `OpenGLView` è più facile da usare in un progetto condiviso. Se utilizzata in una libreria .NET Standard, quindi una dipendenza servizio sarà anche necessario (come illustrato nell'esempio di codice).<br /><br />Questa è la funzionalità di grafica solo che è incorporata in xamarin. Forms, ma un'applicazione xamarin. Forms possa inoltre eseguire il rendering della grafica utilizzando [ `CocosSharp` ](~/xamarin-forms/user-interface/graphics/cocossharp.md), [ `SkiaSharp` ](~/xamarin-forms/user-interface/graphics/skiasharp/index.md), o [ `UrhoSharp` ](~/xamarin-forms/user-interface/graphics/urhosharp.md).<br /><br />[Documentazione delle API](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![Esempio OpenGLView](views-images/OpenGLView.png "esempio OpenGLView")](views-images/OpenGLView-Large.png#lightbox "OpenGLView esempio")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="map"></a>Mappa

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map) Consente di visualizzare una mappa. Il **verifica** è necessario installare il pacchetto Nuget. Android e Universal Windows Platform richiedono una chiave di autorizzazione della mappa.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Maps.Map) / [Guida](~/xamarin-forms/user-interface/map.md) / [esempio](https://developer.xamarin.com/samples/WorkingWithMaps/) | [![Eseguire il mapping di esempio](views-images/Map.png "eseguire il mapping di esempio")](views-images/Map-Large.png#lightbox "eseguire il mapping di esempio")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>Viste che consentono di avviare i comandi

### <a name="button"></a>Button

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button) è un oggetto rettangolare che visualizza il testo, e che viene attivato un [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) evento quando è stato premuto.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Button) / [Guida](~/xamarin-forms/user-interface/button.md) / [esempio](https://developer.xamarin.com/samples/UserInterface/ButtonDemos/) | [![Pulsante riportato](views-images/Button.png "pulsante riportato")](views-images/Button-Large.png#lightbox "sul pulsante di esempio")<br /> [Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton` è rettangolare di oggetto a cui viene visualizzata un'immagine e che viene attivato un `Clicked` evento quando è stato premuto.<br /><br /> [Guida](~/xamarin-forms/user-interface/imagebutton.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/) | [![Esempio ImageButton](views-images/ImageButton.png "esempio ImageButton")](views-images/ImageButton-Large.png#lightbox "ImageButton esempio")<br /> [Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar) Visualizza un'area all'utente di digitare una stringa di testo e un pulsante (o un tasto della tastiera) che segnala l'applicazione per eseguire una ricerca. Il [ `Text` ](xref:Xamarin.Forms.SearchBar.Text) proprietà consente di accedere al testo e il [ `SearchButtonPressed` ](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) evento indica che è stato premuto il pulsante.<br /><br />[Documentazione delle API](xref:Xamarin.Forms.SearchBar) | [![Esempio SearchBar](views-images/SearchBar.png "esempio SearchBar")](views-images/SearchBar-Large.png#lightbox "SearchBar esempio")<br /> [Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Visualizzazioni per l'impostazione di valori

### <a name="slider"></a>Slider

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider) consente all'utente di selezionare una `double` valore da un intervallo continuo specificato con il [ `Minimum` ](xref:Xamarin.Forms.Slider.Minimum) e [ `Maximum` ](xref:Xamarin.Forms.Slider.Maximum) proprietà.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Slider) / [Guida](~/xamarin-forms/user-interface/slider.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) | [![Esempio di dispositivo di scorrimento](views-images/Slider.png "esempio di dispositivo di scorrimento")](views-images/Slider-Large.png#lightbox "esempio di dispositivo di scorrimento")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>Gestore di istruzioni

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper) consente all'utente di selezionare una `double` valore da un intervallo di valori incrementali specificato con il [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum), [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum), e [ `Increment` ](xref:Xamarin.Forms.Stepper.Increment) delle proprietà.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Stepper)  / [Guida](~/xamarin-forms/user-interface/stepper.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/StepperDemos) | [![Esempio di gestore di istruzioni](views-images/Stepper.png "esempio di gestore di istruzioni")](views-images/Stepper-Large.png#lightbox "esempio di gestore di istruzioni")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>Opzione

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch) assume la forma di un'opzione di attivazione/disattivazione per consentire all'utente di selezionare un valore booleano. Il [ `IsToggled` ](xref:Xamarin.Forms.Switch.IsToggled) proprietà è lo stato dell'interruttore e il [ `Toggled` ](xref:Xamarin.Forms.Switch.Toggled) evento viene generato quando lo stato viene modificato.<br /><br />[Documentazione delle API](xref:Xamarin.Forms.Switch) | [![Passare riportato](views-images/Switch.png "Switch esempio")](views-images/Switch-Large.png#lightbox "passare esempio")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker) consente all'utente di selezionare una data con la selezione di data platform. Impostare un intervallo di date consentite con il [ `MinimumDate` ](xref:Xamarin.Forms.DatePicker.MinimumDate) e [ `MaximumDate` ](xref:Xamarin.Forms.DatePicker.MaximumDate) proprietà. Il [ `Date` ](xref:Xamarin.Forms.DatePicker.Date) proprietà è la data selezionata e il [ `DateSelected` ](xref:Xamarin.Forms.DatePicker.DateSelected) evento viene generato quando tale proprietà viene modificata.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.DatePicker) / [Guida](~/xamarin-forms/user-interface/datepicker.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker) | [![Esempio DatePicker](views-images/DatePicker.png "esempio DatePicker")](views-images/DatePicker-Large.png#lightbox "esempio DatePicker")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker) consente all'utente di selezionare un'ora con la selezione di tempo della piattaforma. Il [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) proprietà è l'ora selezionata. Un'applicazione può monitorare le modifiche nel `Time` proprietà installando un gestore per il [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.TimePicker) / [Guida](~/xamarin-forms/user-interface/timepicker.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TimePicker) | [![Esempio di controllo TimePicker](views-images/TimePicker.png "esempio TimePicker")](views-images/TimePicker-Large.png#lightbox "TimePicker esempio")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>Viste per la modifica del testo

Queste due classi derivano dal [ `InputView` ](xref:Xamarin.Forms.InputView) (classe), che definisce il [ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard) proprietà.

<a name="entry" />

### <a name="entry"></a>Voce

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry) consente all'utente di immettere e modificare una singola riga di testo. Il testo è disponibile come le [ `Text` ](xref:Xamarin.Forms.Entry.Text) proprietà e il [ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged) e [ `Completed` ](xref:Xamarin.Forms.Entry.Completed) gli eventi vengono attivati quando le modifiche di testo o dall'utente segnala il completamento toccando il tasto INVIO.<br /><br />Usa un' [ `Editor` ](#editor) per immettere e modificare più righe di testo.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Entry) / [Guida](~/xamarin-forms/user-interface/text/entry.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text) | [![Esempio di voce](views-images/Entry.png "esempio di voce")](views-images/Entry-Large.png#lightbox "esempio di voce")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

<a name="editor" />

### <a name="editor"></a>Editor

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor) consente all'utente di immettere e modificare più righe di testo. Il testo è disponibile come le [ `Text` ](xref:Xamarin.Forms.Editor.Text) proprietà e il [ `TextChanged` ](xref:Xamarin.Forms.Editor.TextChanged) e [ `Completed` ](xref:Xamarin.Forms.Editor.Completed) gli eventi vengono attivati quando le modifiche di testo o dall'utente segnala il completamento.<br /><br />Usa un' [ `Entry` ](#entry) visualizzazione per l'immissione e modifica di una singola riga di testo.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Editor) / [Guida](~/xamarin-forms/user-interface/text/editor.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text) | [![Esempio di voce](views-images/Editor.png "esempio di Editor")](views-images/Editor-Large.png#lightbox "Editor di esempio")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>Viste per indicare l'attività

<a name="activityindicator" />

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) utilizza un'animazione per mostrare che l'applicazione è impegnata in un'attività di lunga durata senza fornire alcuna indicazione dello stato di avanzamento. Il [ `IsRunning` ](xref:Xamarin.Forms.ActivityIndicator.IsRunning) proprietà controlla l'animazione.<br /><br />Se è noto che lo stato di avanzamento dell'attività, usare una [ `ProgressBar` ](#progressbar) invece.<br /><br />[Documentazione delle API](xref:Xamarin.Forms.ActivityIndicator) | [![Esempio ActivityIndicator](views-images/ActivityIndicator.png "esempio ActivityIndicator")](views-images/ActivityIndicator-Large.png#lightbox "ActivityIndicator esempio")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

<a name="progressbar" />

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) utilizza un'animazione per mostrare che l'applicazione sta avanzando tramite un'attività di lunga durata. Impostare il [ `Progress` ](xref:Xamarin.Forms.ProgressBar.Progress) proprietà su valori compresi tra 0 e 1 per indicare lo stato di avanzamento.<br /><br />Se lo stato di avanzamento dell'attività non è noto, usare un [ `ActivityIndicator` ](#activityindicator) invece.<br /><br />[Documentazione delle API](xref:Xamarin.Forms.ProgressBar) | [![Esempio di ProgressBar](views-images/ProgressBar.png "esempio ProgressBar")](views-images/ProgressBar-Large.png#lightbox "esempio ProgressBar")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Viste che consentono di visualizzare le raccolte

### <a name="picker"></a>Selezione

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker) Visualizza un elemento selezionato da un elenco di stringhe di testo e consente di selezionare questo elemento quando viene toccata la visualizzazione. Impostare il [ `Items` ](xref:Xamarin.Forms.Picker.Items) proprietà per un elenco di stringhe, o il [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) proprietà a una raccolta di oggetti. Il [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento viene generato quando si seleziona un elemento.<br /><br />Il `Picker` consente di visualizzare l'elenco di elementi solo quando è selezionata. Usare un [ `ListView` ](#listView) oppure [ `TableView` ](#tableView) per un elenco scorrevole che rimane nella pagina.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Picker) / [Guida](~/xamarin-forms/user-interface/picker/index.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/) | [![Esempio di selettore](views-images/Picker.png "esempio di selettore")](views-images/Picker-Large.png#lightbox "esempio selezione")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

<a name="listView" />

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView) deriva da [ `ItemsView[Cell]` ](xref:Xamarin.Forms.ItemsView`1) e visualizza un elenco scorrevole di elementi di dati selezionabile. Impostare il [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) proprietà a una raccolta di oggetti e set il [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) proprietà su un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) oggetto che descrive come gli elementi sono da formattare. Il [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) evento segnala che è stata eseguita alcuna selezione, che è disponibile come le [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) proprietà.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.ListView) / [Guida](~/xamarin-forms/user-interface/listview/index.md) / [esempio](https://developer.xamarin.com/samples/WorkingWithListview) | [![Esempio di ListView](views-images/ListView.png "esempio di ListView")](views-images/ListView-Large.png#lightbox "esempio di ListView")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

<a name="tableView" />

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView) Visualizza un elenco di righe di tipo [ `Cell` ](xref:Xamarin.Forms.Cell) con i sottotitoli e intestazioni facoltative. Impostare il [ `Root` ](xref:Xamarin.Forms.TableView.Root) proprietà a un oggetto di tipo [ `TableRoot` ](xref:Xamarin.Forms.TableRoot)e aggiungere [ `TableSection` ](xref:Xamarin.Forms.TableSection) oggetti da cui `TableRoot`. Ciascuna `TableSection` è una raccolta di `Cell` oggetti.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.TableView) / [Guida](~/xamarin-forms/user-interface/tableview.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView) | [![Esempio TableView](views-images/TableView.png "esempio TableView")](views-images/TableView-Large.png#lightbox "TableView esempio")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di xamarin. Forms FormsGallery](https://developer.xamarin.com/samples/FormsGallery/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentazione per le API di Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
