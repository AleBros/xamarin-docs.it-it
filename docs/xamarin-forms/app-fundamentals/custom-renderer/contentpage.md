---
title: Personalizzazione di un elemento ContentPage
description: Un elemento ContentPage è un elemento visivo che consente di visualizzare una singola visualizzazione e occupa la maggior parte della schermata. Questo articolo illustra come creare un renderer personalizzato per la pagina di ContentPage, consentendo agli sviluppatori di eseguire l'override del rendering nativo predefinito con le personalizzazioni specifiche della piattaforma.
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 2369b249681b926476cf3938c51c99745eba9098
ms.sourcegitcommit: 8888cb7d75f4469f2a1195b9a426a2e1fbf46bd8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "38995742"
---
# <a name="customizing-a-contentpage"></a>Personalizzazione di un elemento ContentPage

_Un elemento ContentPage è un elemento visivo che consente di visualizzare una singola visualizzazione e occupa la maggior parte della schermata. Questo articolo illustra come creare un renderer personalizzato per la pagina di ContentPage, consentendo agli sviluppatori di eseguire l'override del rendering nativo predefinito con le personalizzazioni specifiche della piattaforma._

Ogni controllo di xamarin. Forms dispone di un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) viene eseguito il rendering da un'applicazione xamarin. Forms, in iOS il `PageRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza di nativo `UIViewController` controllo. La piattaforma Android, il `PageRenderer` crea un'istanza di classe un `ViewGroup` controllo. In Universal Windows Platform (UWP), il `PageRenderer` crea un'istanza di classe un `FrameworkElement` controllo. Per altre informazioni sulle classi di controllo nativo che eseguono il mapping per i controlli di xamarin. Forms e renderer, vedere [Renderer di classi di Base e controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra il [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) e i corrispondenti controlli nativi che l'implementano:

![](contentpage-images/contentpage-classes.png "Relazione tra ContentPage classe e implementare i controlli nativi")

Il processo di rendering possibile avvantaggiarsi di implementare le personalizzazioni specifiche della piattaforma tramite la creazione di un renderer personalizzato per un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) in ogni piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Xamarin.Forms_Page) una pagina di xamarin. Forms.
1. [Utilizzare](#Consuming_the_Xamarin.Forms_Page) la pagina da xamarin. Forms.
1. [Creare](#Creating_the_Page_Renderer_on_each_Platform) il renderer personalizzato per la pagina in ogni piattaforma.

Ogni elemento a questo punto discuteremo a sua volta, per implementare un `CameraPage` che fornisce una feed fotocamera in tempo reale e la possibilità di acquisire una foto.

<a name="Creating_the_Xamarin.Forms_Page" />

## <a name="creating-the-xamarinforms-page"></a>Creazione della pagina di xamarin. Forms

Un'invariati [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) possono essere aggiunti al progetto xamarin. Forms condiviso, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

Analogamente, il file code-behind per il [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) deve inoltre rimanere invariato, come illustrato nell'esempio di codice seguente:

```csharp
public partial class CameraPage : ContentPage
{
    public CameraPage ()
    {
        // A custom renderer is used to display the camera UI
        InitializeComponent ();
    }
}
```

Esempio di codice seguente viene illustrato come la pagina può essere creata in c#:

```csharp
public class CameraPageCS : ContentPage
{
    public CameraPageCS ()
    {
    }
}
```

Un'istanza di `CameraPage` verrà usato per visualizzare il feed in ogni piattaforma di fotocamera in tempo reale. Personalizzazione del controllo verrà eseguita nel renderer personalizzati, pertanto non è necessario alcuna implementazione aggiuntiva di `CameraPage` classe.

<a name="Consuming_the_Xamarin.Forms_Page" />

## <a name="consuming-the-xamarinforms-page"></a>Utilizzo della pagina di xamarin. Forms

Vuoto `CameraPage` deve essere visualizzata dall'applicazione xamarin. Forms. Ciò si verifica quando un pulsante di `MainPage` toccando istanza, che a sua volta esegue il `OnTakePhotoButtonClicked` metodo, come illustrato nell'esempio di codice seguente:

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

Questo codice passa semplicemente al `CameraPage`, su quale renderer personalizzati personalizzerà l'aspetto della pagina in ogni piattaforma.

<a name="Creating_the_Page_Renderer_on_each_Platform" />

## <a name="creating-the-page-renderer-on-each-platform"></a>Creare il Renderer di pagina in ogni piattaforma

Il processo per la creazione della classe renderer personalizzati è come segue:

1. Creare una sottoclasse del `PageRenderer` classe.
1. Eseguire l'override di `OnElementChanged` metodo che esegue il rendering la logica di pagina e di scrittura nativa per personalizzare la pagina. Il `OnElementChanged` metodo viene chiamato quando viene creato il controllo di xamarin. Forms corrispondente.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer di pagina per specificare che verrà usata per il rendering della pagina di xamarin. Forms. Questo attributo viene utilizzato per registrare il renderer personalizzato con xamarin. Forms.

> [!NOTE]
> È facoltativo per fornire un renderer di pagina in ogni progetto della piattaforma. Se non è registrato un renderer di pagina, quindi verrà utilizzato il renderer predefinito per la pagina.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con la relazione tra di essi:

![](contentpage-images/solution-structure.png "Responsabilità di progetto Renderer personalizzati CameraPage")

Il `CameraPage` viene eseguito il rendering di un'istanza da specifiche della piattaforma `CameraPageRenderer` classi che derivano dal `PageRenderer` classe per la piattaforma. Di conseguenza ogni `CameraPage` istanza viene eseguito il rendering con un feed fotocamera in tempo reale, come illustrato negli screenshot seguenti:

![](contentpage-images/screenshots.png "CameraPage in ogni piattaforma")

Il `PageRenderer` classe espone il `OnElementChanged` metodo, che viene chiamato quando viene creata la pagina di xamarin. Forms per eseguire il rendering del controllo nativo corrispondente. Questo metodo accetta un `ElementChangedEventArgs` parametro che contiene `OldElement` e `NewElement` proprietà. Queste proprietà rappresentano l'elemento di xamarin. Forms che il renderer *è stata* collegata, l'elemento di xamarin. Forms e che il renderer *è* collegati rispettivamente. Nell'applicazione di esempio il `OldElement` proprietà sarà `null` e il `NewElement` proprietà conterrà un riferimento al `CameraPage` istanza.

Una versione override del `OnElementChanged` nel metodo il `CameraPageRenderer` classe è la posizione in cui eseguire la personalizzazione della pagina nativa. Un riferimento all'istanza di pagina di xamarin. Forms che viene eseguito il rendering può essere ottenuto tramite il `Element` proprietà.

Ogni classe renderer personalizzato è decorata con un `ExportRenderer` attributo che registra il renderer con xamarin. Forms. L'attributo accetta due parametri: il nome del tipo della pagina di xamarin. Forms viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il `assembly` prefisso per l'attributo specifica che l'attributo viene applicato all'intero assembly.

Le sezioni seguenti illustrano l'implementazione del `CameraPageRenderer` renderer personalizzato per ogni piattaforma.

### <a name="creating-the-page-renderer-on-ios"></a>Creare il Renderer di pagina in iOS

Esempio di codice seguente illustra il renderer di pagina per la piattaforma iOS:

```csharp
[assembly:ExportRenderer (typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPageRenderer : PageRenderer
    {
        ...

        protected override void OnElementChanged (VisualElementChangedEventArgs e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null || Element == null) {
                return;
            }

            try {
                SetupUserInterface ();
                SetupEventHandlers ();
                SetupLiveCameraStream ();
                AuthorizeCameraUse ();
            } catch (Exception ex) {
                System.Diagnostics.Debug.WriteLine (@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

La chiamata per la classe di base `OnElementChanged` metodo crea un'istanza di un iOS `UIViewController` controllo. Condizione che il renderer non è già collegato a un elemento di xamarin. Forms esistente e condizione che esiste un'istanza di pagina che viene eseguito il rendering dal renderer personalizzati, viene visualizzato solo nel flusso in tempo reale della fotocamera.

La pagina viene quindi personalizzata da una serie di metodi che usano il `AVCapture` le API per fornire il flusso in tempo reale da fotocamera e la possibilità di acquisire una foto.

### <a name="creating-the-page-renderer-on-android"></a>Creare il Renderer di pagina in Android

Esempio di codice seguente illustra il renderer di pagina per la piattaforma Android:

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPageRenderer : PageRenderer, TextureView.ISurfaceTextureListener
    {
        ...
        public CameraPageRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                SetupUserInterface();
                SetupEventHandlers();
                AddView(view);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

La chiamata per la classe di base `OnElementChanged` metodo crea un'istanza di Android `ViewGroup` controllo, ovvero un gruppo di viste. Condizione che il renderer non è già collegato a un elemento di xamarin. Forms esistente e condizione che esiste un'istanza di pagina che viene eseguito il rendering dal renderer personalizzati, viene visualizzato solo nel flusso in tempo reale della fotocamera.

La pagina viene quindi personalizzata richiamando una serie di metodi che usano il `Camera` API per fornire il flusso in tempo reale da fotocamera e la possibilità di acquisire una foto, in precedenza il `AddView` metodo viene richiamato per aggiungere la fotocamera in tempo reale di flusso dell'interfaccia utente per il `ViewGroup`. Si noti che in Android è anche necessario eseguire l'override di `OnLayout` metodo per eseguire operazioni di misure e il layout nella visualizzazione. Per altre informazioni, vedere la [esempio renderer ContentPage](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/).

### <a name="creating-the-page-renderer-on-uwp"></a>Creare il Renderer di pagina nella piattaforma UWP

Esempio di codice seguente illustra il renderer di pagina per la piattaforma UWP:

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPageRenderer : PageRenderer
    {
        ...
        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                ...
                SetupUserInterface();
                SetupBasedOnStateAsync();

                this.Children.Add(page);
            }
            ...
        }

        protected override Size ArrangeOverride(Size finalSize)
        {
            page.Arrange(new Windows.Foundation.Rect(0, 0, finalSize.Width, finalSize.Height));
            return finalSize;
        }
        ...
    }
}

```

La chiamata per la classe di base `OnElementChanged` crea un'istanza di metodo un `FrameworkElement` controllo, in cui viene eseguito il rendering della pagina. Condizione che il renderer non è già collegato a un elemento di xamarin. Forms esistente e condizione che esiste un'istanza di pagina che viene eseguito il rendering dal renderer personalizzati, viene visualizzato solo nel flusso in tempo reale della fotocamera. La pagina viene quindi personalizzata richiamando una serie di metodi che usano il `MediaCapture` API per fornire il flusso in tempo reale da fotocamera e la possibilità di acquisire una foto, prima di aggiunta la pagina personalizzata di `Children` raccolta per la visualizzazione.

Quando si implementa un renderer personalizzato che deriva da `PageRenderer` sulla piattaforma UWP, il `ArrangeOverride` metodo deve essere implementato anche per disporre i controlli di pagina, perché il renderer di base non sa cosa farne. In caso contrario, restituisce una pagina vuota. Pertanto, in questo esempio il `ArrangeOverride` chiamate al metodo il `Arrange` metodo sul `Page` istanza.

> [!NOTE]
> È importante arrestare ed eliminare gli oggetti che forniscono l'accesso alla fotocamera in un'applicazione UWP. In caso contrario, può interferire con altre applicazioni che tentano di accedere la fotocamera del dispositivo. Per altre informazioni, vedere [visualizzare l'anteprima della fotocamera](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access).

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare un renderer personalizzato per il [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) pagina, consentendo agli sviluppatori di eseguire l'override del rendering nativo predefinito con le personalizzazioni specifiche della piattaforma. Oggetto `ContentPage` è un elemento visivo che consente di visualizzare una singola visualizzazione e occupa la maggior parte della schermata.


## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererContentPage (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/)
