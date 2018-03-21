---
title: Viste di xamarin. Forms
description: Viste di xamarin. Forms sono i blocchi predefiniti di interfacce utente per dispositivi mobili multipiattaforma.
ms.topic: article
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: ef4de2d544f3bcfb661b29dd90de738ae0442373
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2018
---
# <a name="xamarinforms-views"></a>Viste di xamarin. Forms

_Viste di xamarin. Forms sono i blocchi predefiniti di interfacce utente per dispositivi mobili multipiattaforma._

Le visualizzazioni sono oggetti dell'interfaccia utente, ad esempio etichette, i pulsanti e dispositivi di scorrimento sono comunemente noti come *controlli* o *widget* in altri ambienti di programmazione grafiche. Le viste supportate da xamarin. Forms derivano tutte dal [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) classe. Possono essere suddivisi in categorie diverse:

## <a name="views-for-presentation"></a>Viste di presentazione

### <a name="label"></a>Label

|     |     |
| --- | --- |
| [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) Visualizza le stringhe di testo a riga singola o multilinea blocchi di testo con formattazione costante o variabile. Impostare il [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) proprietà in una stringa di costanti di formattazione o set di [ `FormattedText` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FormattedText/) proprietà per un [ `FormattedString` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FormattedString/) oggetto per la variabile la formattazione.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) / [Guida](~/xamarin-forms/user-interface/text/label.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/) | [![Esempio di etichetta](views-images/Label.png "etichetta esempio")](views-images/Label-Large.png#lightbox "un'etichetta di esempio")<br /> [Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>Image

|     |     |
| --- | --- |
| [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) Consente di visualizzare una bitmap. Bitmap possono essere scaricate dal Web, incorporati come risorse nel progetto common o nei progetti di piattaforma o create utilizzando .NET `Stream` oggetto.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) / [Guida](~/xamarin-forms/user-interface/images.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/) | [![Immagine di esempio](views-images/Image.png "immagine esempio")](views-images/Image-Large.png#lightbox "immagine di esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) Visualizza un rettangolo pieno colorato con il [ `Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) proprietà. `BoxView` dispone di una richiesta di dimensione predefinito di 40 x 40. Per altre dimensioni, assegnare il [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) e [ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) proprietà.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) / [Guida](~/xamarin-forms/user-interface/boxview.md) / [1 di esempio](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView), [2](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration), [3](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ColorListBox), [4 ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife), [5](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock), e [6](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock) | [![Esempio BoxView](views-images/BoxView.png "esempio BoxView")](views-images/BoxView-Large.png#lightbox "esempio BoxView")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) Visualizza contenuto, HTML o pagine Web a seconda che il [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.Source/) è impostata su un [ `UriWebViewSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UrlWebViewSource/) o [ `HtmlWebViewSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.HtmlWebViewSource/) oggetto.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) / [Guida](~/xamarin-forms/user-interface/webview.md) / [1 di esempio](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/) e [2](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView) | [![Esempio di WebView](views-images/WebView.png "esempio WebView")](views-images/WebView-Large.png#lightbox "esempio WebView")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/) Consente di visualizzare grafica OpenGL in progetti iOS e Android. Non è previsto alcun supporto per la piattaforma Windows universale. I progetti iOS e Android richiedono un riferimento di **OpenTK 1.0** assembly o **OpenTK** assembly della versione 1.0.0.0. `OpenGLView` è più facile da utilizzare in un progetto condiviso. Se utilizzata in una libreria .NET Standard o di libreria di classi Portabile, un servizio di dipendenza inoltre sarà necessario (come illustrato nell'esempio di codice).<br /><br />Questa è la funzionalità grafica solo incorporato in xamarin. Forms, ma un'applicazione di xamarin. Forms può anche eseguire il rendering di grafica usando [ `CocosSharp` ](~/xamarin-forms/user-interface/graphics/cocossharp.md), [ `SkiaSharp` ](~/xamarin-forms/user-interface/graphics/skiasharp/index.md), o [ `UrhoSharp` ](~/xamarin-forms/user-interface/graphics/urhosharp.md).<br /><br />[Documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/)<br /><br /> | [![Esempio OpenGLView](views-images/OpenGLView.png "OpenGLView esempio")](views-images/OpenGLView-Large.png#lightbox "OpenGLView esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) con [codice](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="map"></a>Mappa

|     |     |
| --- | --- |
| [`Map`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) Visualizza una mappa. Il **Xamarin.Forms.Maps** è necessario installare il pacchetto Nuget. Android e Universal Windows Platform richiedono una chiave di autorizzazione della mappa.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) / [Guida](~/xamarin-forms/user-interface/map.md) / [esempio](https://developer.xamarin.com/samples/WorkingWithMaps/) | [![Eseguire il mapping di esempio](views-images/Map.png "eseguire il mapping di esempio")](views-images/Map-Large.png#lightbox "eseguire il mapping di esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>Visualizzazioni che consentono di avviare i comandi

### <a name="button"></a>Button

|     |     |
| --- | --- |
| [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) è un oggetto rettangolare che visualizza il testo, e che viene attivato un [ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Button.Clicked/) evento quando viene premuto.<br /><br />[Documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) | [![Esempio di pulsante](views-images/Button.png "pulsante esempio")](views-images/Button-Large.png#lightbox "pulsante esempio")<br /> [Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) con [codice](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) viene visualizzata un'area per l'utente di digitare una stringa di testo e un pulsante (o un tasto della tastiera) che segnala l'applicazione per eseguire una ricerca. Il [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.Text/) proprietà consente di accedere al testo e [ `SearchButtonPressed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.SearchButtonPressed/) evento indica che è stato premuto il pulsante.<br /><br />[Documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) | [![Esempio SearchBar](views-images/SearchBar.png "SearchBar esempio")](views-images/SearchBar-Large.png#lightbox "SearchBar esempio")<br /> [Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) con [codice](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Visualizzazioni per l'impostazione di valori 

### <a name="slider"></a>Slider

|     |     |
| --- | --- |
| [`Slider`](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) consente all'utente di selezionare un `double` valore da un intervallo continuo specificato con il [ `Minimum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Minimum/) e [ `Maximum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Maximum/) proprietà.<br /><br />[Documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) | [![Esempio di dispositivo di scorrimento](views-images/Slider.png "dispositivo di scorrimento esempio")](views-images/Slider-Large.png#lightbox "esempio dispositivo di scorrimento")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>Gestore di istruzioni

|     |     |
| --- | --- |
| [`Stepper`](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) consente all'utente di selezionare un `double` valore da un intervallo di valori incrementali specificato con il [ `Minimum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Minimum/), [ `Maximum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Maximum/), e [ `Increment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Increment/) proprietà.<br /><br />[Documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) | [![Esempio di gestore di istruzioni](views-images/Stepper.png "esempio gestore di istruzioni")](views-images/Stepper-Large.png#lightbox "esempio gestore di istruzioni")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>Opzione 

|     |     |
| --- | --- |
| [`Switch`](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) assume la forma di un'opzione di attivazione/disattivazione per consentire all'utente di selezionare un valore booleano. Il [ `IsToggled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Switch.IsToggled/) proprietà è lo stato del commutatore e [ `Toggled` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Switch.Toggled/) evento viene generato quando cambia lo stato.<br /><br />[Documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) | [![Passare l'esempio](views-images/Switch.png "passare esempio")](views-images/Stepper-Large.png#lightbox "passare esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) consente all'utente di selezionare una data con il pulsante di selezione data piattaforma. Impostare un intervallo di date consentite con la [ `MinimumDate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MinimumDate/) e [ `MaximumDate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MaximumDate/) proprietà. Il [ `Date` ](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Date/) proprietà è la data selezionata e [ `DateSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.DatePicker.DateSelected/) evento viene generato quando cambia la proprietà.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) / [Guida](~/xamarin-forms/user-interface/datepicker.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker) | [![Esempio DatePicker](views-images/DatePicker.png "esempio DatePicker")](views-images/DatePicker-Large.png#lightbox "esempio DatePicker")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) consente all'utente di selezionare un'ora con il pulsante di selezione ora piattaforma. Il [ `Time` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Time/) proprietà è l'ora selezionata. Un'applicazione può monitorare le modifiche di `Time` proprietà installando un gestore per il [ `PropertyChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.BindableObject.PropertyChanged/) evento.<br /><br />[Documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) | [![Esempio TimePicker](views-images/TimePicker.png "TimePicker esempio")](views-images/TimePicker-Large.png#lightbox "TimePicker esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>Viste per la modifica del testo

Queste due classi derivano dal [ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/) (classe), che definisce il [ `Keyboard` ](https://developer.xamarin.com/api/property/Xamarin.Forms.InputView.Keyboard/) proprietà.

<a name="entry" />

### <a name="entry"></a>Voce

|     |     |
| --- | --- |
| [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) consente all'utente di immettere e modificare una singola riga di testo. Il testo è disponibile come il [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) , proprietà e [ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) e [ `Completed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.Completed/) gli eventi vengono attivati quando le modifiche di testo o l'utente segnala il completamento, toccare il tasto INVIO.<br /><br />Utilizzare un [ `Editor` ](#editor) per l'immissione e modifica di più righe di testo.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) / [Guida](~/xamarin-forms/user-interface/text/entry.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text) | [![Esempio di voce](views-images/Entry.png "voce esempio")](views-images/Entry-Large.png#lightbox "voce esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

<a name="editor" />

### <a name="editor"></a>Editor

|     |     |
| --- | --- |
| [`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) consente all'utente di immettere e modificare più righe di testo. Il testo è disponibile come il [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Editor.Text/) , proprietà e [ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Editor.TextChanged/) e [ `Completed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Editor.Completed/) gli eventi vengono attivati quando le modifiche di testo o l'utente segnala il completamento.<br /><br />Utilizzare un [ `Entry` ](#entry) visualizzazione per l'immissione e modifica di una singola riga di testo.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) / [Guida](~/xamarin-forms/user-interface/text/editor.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text) | [![Esempio di voce](views-images/Editor.png "Editor esempio")](views-images/Editor-Large.png#lightbox "esempio di Editor")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>Viste per indicare un'attività

<a name="activityindicator" />

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/) utilizza un'animazione per mostrare che l'applicazione è coinvolto in un'attività di lunga durata senza fornire alcuna indicazione dello stato di avanzamento. Il [ `IsRunning` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ActivityIndicator.IsRunning/) proprietà controlla l'animazione.<br /><br />Se è noto lo stato di avanzamento dell'attività, utilizzare un [ `ProgressBar` ](#progressbar) invece.<br /><br />[Documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/) | [![Esempio ActivityIndicator](views-images/ActivityIndicator.png "ActivityIndicator esempio")](views-images/ActivityIndicator-Large.png#lightbox "ActivityIndicator esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

<a name="progressbar" />

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/) utilizza un'animazione per mostrare che l'applicazione viene eseguita l'operazione tramite un'attività di lunga durata. Impostare il [ `Progress` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ProgressBar.Progress/) proprietà su valori compresi tra 0 e 1 per indicare lo stato di avanzamento.<br /><br />Se lo stato di avanzamento dell'attività non è noto, utilizzare un [ `ActivityIndicator` ](#activityindicator) invece.<br /><br />[Documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/) | [![Esempio ProgressBar](views-images/ProgressBar.png "esempio ProgressBar")](views-images/ProgressBar-Large.png#lightbox "esempio ProgressBar")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) con [codice](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Viste che visualizzano le raccolte

### <a name="picker"></a>Selezione

|     |     |
| --- | --- |
| [`Picker`](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) Visualizza un elemento selezionato da un elenco di stringhe di testo e consente di selezionare l'elemento quando si tocca la vista. Impostare il [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) proprietà per un elenco di stringhe, o [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) proprietà a una raccolta di oggetti. Il [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) evento viene generato quando viene selezionato un elemento.<br /><br />Il `Picker` consente di visualizzare l'elenco degli elementi solo quando è selezionata. Utilizzare un [ `ListView` ](#listView) o [ `TableView` ](#tableView) per un elenco scorrevole che rimane nella pagina.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) / [Guida](~/xamarin-forms/user-interface/picker/index.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/) | [![Esempio di selezione](views-images/Picker.png "selezione esempio")](views-images/Picker-Large.png#lightbox "esempio selezione")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) con [codice](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

<a name="listView" />

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) deriva da [ `ItemsView[Cell]` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) e visualizza un elenco di elementi di dati selezionabile scorrevole. Impostare il [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) proprietà a una raccolta di oggetti e impostare il [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) proprietà per un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) oggetto che descrive come gli elementi sono da formattare. Il [ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) evento segnala che è stata effettuata una selezione, che è disponibile come il [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/) proprietà.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) / [Guida](~/xamarin-forms/user-interface/listview/index.md) / [esempio](https://developer.xamarin.com/samples/WorkingWithListview) | [![Esempio di ListView](views-images/ListView.png "esempio ListView")](views-images/ListView-Large.png#lightbox "esempio di ListView")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

<a name="tableView" />

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) Visualizza un elenco di righe di tipo [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) sottotitoli e intestazioni facoltative. Impostare il [ `Root` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.Root/) proprietà a un oggetto di tipo [ `TableRoot` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableRoot/)e aggiungere [ `TableSection` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSection/) oggetti che `TableRoot`. Ogni `TableSection` è una raccolta di `Cell` oggetti.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) / [Guida](~/xamarin-forms/user-interface/tableview.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView) | [![Esempio TableView](views-images/TableView.png "esempio TableView")](views-images/TableView-Large.png#lightbox "esempio TableView")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Introduction to Xamarin.Forms (Introduzione a Xamarin.Forms)](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Esempio FormsGallery xamarin. Forms](https://developer.xamarin.com/samples/FormsGallery/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentazione di xamarin. Forms API](https://developer.xamarin.com/api/root/Xamarin.Forms/)
