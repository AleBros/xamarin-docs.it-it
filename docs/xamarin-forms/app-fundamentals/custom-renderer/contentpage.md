---
title: Personalizzazione di un ContentPage
description: "Un ContentPage è un elemento visivo che consente di visualizzare una singola visualizzazione e occupa la maggior parte della schermata. In questo articolo viene illustrato come creare un renderer personalizzato per la pagina ContentPage, consentendo agli sviluppatori di eseguire l'override per il rendering predefinito nativo con le personalizzazioni specifiche della piattaforma."
ms.topic: article
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: f1f420641691e700894687fef8ea3bd44fd60ff2
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="customizing-a-contentpage"></a>Personalizzazione di un ContentPage

_Un ContentPage è un elemento visivo che consente di visualizzare una singola visualizzazione e occupa la maggior parte della schermata. In questo articolo viene illustrato come creare un renderer personalizzato per la pagina ContentPage, consentendo agli sviluppatori di eseguire l'override per il rendering predefinito nativo con le personalizzazioni specifiche della piattaforma._

Ogni controllo in xamarin. Forms è un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) viene eseguito il rendering da un'applicazione di xamarin. Forms, in iOS il `PageRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza nativa `UIViewController` controllo. Nella piattaforma Android, il `PageRenderer` crea un'istanza di classe un `ViewGroup` controllo. In Windows Phone e di Windows della piattaforma UWP (Universal), il `PageRenderer` crea un'istanza di classe un `FrameworkElement` controllo. Per ulteriori informazioni sulle classi di controllo nativo che eseguono il mapping per i controlli di xamarin. Forms e renderer, vedere [Renderer classi di Base e i controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra il [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) e i corrispondenti controlli nativi che l'implementano:

![](contentpage-images/contentpage-classes.png "Relazione tra la classe ContentPage e l'implementazione di controlli nativi")

Il processo di rendering può essere sfruttato per implementare le personalizzazioni specifiche della piattaforma tramite la creazione di un renderer personalizzato per un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) in ciascuna piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Xamarin.Forms_Page) una pagina di xamarin. Forms.
1. [Utilizzare](#Consuming_the_Xamarin.Forms_Page) la pagina xamarin. Forms.
1. [Creare](#Creating_the_Page_Renderer_on_each_Platform) il renderer personalizzato per la pagina in ogni piattaforma.

Ogni elemento verrà ora essere descritti in dettaglio, per implementare un `CameraPage` che fornisce una fotocamera in tempo reale del feed e la possibilità di acquisire una foto.

<a name="Creating_the_Xamarin.Forms_Page" />

## <a name="creating-the-xamarinforms-page"></a>Creazione di una pagina xamarin. Forms

Un inalterato [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) può essere aggiunto al progetto xamarin. Forms condiviso, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

Analogamente, il file code-behind per il [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) deve anche rimangono inalterati, come illustrato nell'esempio di codice seguente:

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

Esempio di codice seguente viene illustrato come creare la pagina in c#:

```csharp
public class CameraPageCS : ContentPage
{
    public CameraPageCS ()
    {
    }
}
```

Un'istanza di `CameraPage` verrà utilizzato per visualizzare la fotocamera in tempo reale del feed in ciascuna piattaforma. Personalizzazione del controllo verrà effettuata nel renderer personalizzato, è necessario alcun implementazione aggiuntiva di `CameraPage` classe.

<a name="Consuming_the_Xamarin.Forms_Page" />

## <a name="consuming-the-xamarinforms-page"></a>Utilizzo della pagina di xamarin. Forms

Vuoto `CameraPage` deve essere visualizzato dall'applicazione xamarin. Forms. Ciò si verifica quando un pulsante di `MainPage` istanza verrà scelti, che a sua volta esegue il `OnTakePhotoButtonClicked` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

Questo codice passa semplicemente al `CameraPage`, in cui renderer personalizzato personalizzare l'aspetto della pagina in ogni piattaforma.

<a name="Creating_the_Page_Renderer_on_each_Platform" />

## <a name="creating-the-page-renderer-on-each-platform"></a>Creare il Renderer di pagine in ciascuna piattaforma

Il processo di creazione della classe renderer personalizzato è come segue:

1. Creare una sottoclasse di `PageRenderer` classe.
1. Eseguire l'override di `OnElementChanged` metodo che esegue il rendering della logica di pagina e di scrittura nativa per personalizzare la pagina. Il `OnElementChanged` metodo viene chiamato quando viene creato il controllo di xamarin. Forms corrispondente.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer pagina per specificare che verrà usata per il rendering della pagina di xamarin. Forms. Questo attributo viene utilizzato per registrare il renderer personalizzato con xamarin. Forms.

> [!NOTE]
> **Nota**: è facoltativo per fornire un renderer di pagine in ogni progetto della piattaforma. Se non è registrato un renderer di pagine, verrà utilizzato il programma di rendering predefinito per la pagina.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con la relazione tra di essi:

![](contentpage-images/solution-structure.png "Responsabilità di progetto personalizzato CameraPage Renderer")

Il `CameraPage` istanza esegue il rendering specifico della piattaforma `CameraPageRenderer` classi che derivano dalla `PageRenderer` classe per la piattaforma. Di conseguenza, ogni `CameraPage` istanza viene eseguito il rendering con un feed di fotocamera in tempo reale, come illustrato nelle schermate seguenti:

![](contentpage-images/screenshots.png "CameraPage in ciascuna piattaforma")

Il `PageRenderer` classe espone il `OnElementChanged` metodo, che viene chiamato quando viene creata la pagina di xamarin. Forms per eseguire il rendering del controllo nativo corrispondente. Questo metodo accetta un `ElementChangedEventArgs` parametro contenente `OldElement` e `NewElement` proprietà. Queste proprietà rappresentano l'elemento di xamarin. Forms che il renderer *è stata* collegata, l'elemento di xamarin. Forms e che il renderer *è* associata rispettivamente. Nell'applicazione di esempio di `OldElement` proprietà sarà `null` e `NewElement` proprietà contiene un riferimento al `CameraPage` istanza.

Una versione sottoposta a override del `OnElementChanged` metodo la `CameraPageRenderer` classe è il luogo per eseguire la personalizzazione della pagina nativo. Un riferimento all'istanza di page xamarin. Forms che viene eseguito il rendering può essere ottenuto tramite il `Element` proprietà.

Ogni classe renderer personalizzato è decorato con un `ExportRenderer` attributo che registra il renderer con xamarin. Forms. L'attributo accetta due parametri: il nome del tipo di viene eseguito il rendering della pagina di xamarin. Forms e il nome del tipo di renderer personalizzato. Il `assembly` prefisso per l'attributo specifica che l'attributo si applica all'intero assembly.

Le sezioni seguenti illustrano l'implementazione del `CameraPageRenderer` un renderer personalizzato per ogni piattaforma.

### <a name="creating-the-page-renderer-on-ios"></a>Creare il Renderer di pagine in iOS

Esempio di codice seguente viene illustrato il renderer di pagine per la piattaforma iOS:

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
                System.Diagnostics.Debug.WriteLine (@"          ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

La chiamata per la classe base `OnElementChanged` metodo crea un'istanza di un iOS `UIViewController` controllo. Il flusso in tempo reale della fotocamera solo viene eseguito il rendering condizione che il renderer non è già collegato a un elemento esistente di xamarin. Forms, condizione che esiste un'istanza di pagina che viene eseguito il rendering dal renderer personalizzato.

La pagina viene quindi personalizzata da una serie di metodi che utilizzano il `AVCapture` le API per fornire il flusso live dalla fotocamera e la possibilità di acquisire una foto.

### <a name="creating-the-page-renderer-on-android"></a>Creare il Renderer di pagine in Android

Esempio di codice seguente viene illustrato il renderer di pagine per la piattaforma Android:

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
                System.Diagnostics.Debug.WriteLine(@"           ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

La chiamata per la classe base `OnElementChanged` metodo crea un'istanza di un Android `ViewGroup` controllo, ovvero un gruppo di viste. Il flusso in tempo reale della fotocamera solo viene eseguito il rendering condizione che il renderer non è già collegato a un elemento esistente di xamarin. Forms, condizione che esiste un'istanza di pagina che viene eseguito il rendering dal renderer personalizzato.

La pagina viene quindi personalizzata tramite la chiamata di una serie di metodi che utilizzano il `Camera` API per fornire il flusso in tempo reale dalla fotocamera e la possibilità di acquisire una foto, prima di `AddView` metodo viene richiamato per aggiungere la fotocamera in tempo reale flusso dell'interfaccia utente per il `ViewGroup`.

### <a name="creating-the-page-renderer-on-windows-phone"></a>Creazione di Renderer di pagine in Windows Phone

Esempio di codice seguente viene illustrato il renderer di pagine per la piattaforma Windows Phone:

```csharp
[assembly: ExportRenderer (typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.WinPhone81
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
                ...
                var container = ContainerElement as Canvas;

                SetupUserInterface ();
                SetupEventHandlers ();
                SetupLiveCameraStream ();
                container.Children.Add (page);
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

La chiamata per la classe base `OnElementChanged` metodo crea un'istanza di un Windows Phone `Canvas` controllo, in cui viene eseguito il rendering della pagina. Il flusso in tempo reale della fotocamera solo viene eseguito il rendering condizione che il renderer non è già collegato a un elemento esistente di xamarin. Forms, condizione che esiste un'istanza di pagina che viene eseguito il rendering dal renderer personalizzato.

Nella piattaforma Windows Phone, un riferimento tipizzato a pagina nativo utilizzato nella piattaforma sono accessibili tramite il `ContainerElement` proprietà, con la `Canvas` controllare da riferimento tipizzato al `FrameworkElement`. La pagina viene quindi personalizzata tramite la chiamata di una serie di metodi che utilizzano il `MediaCapture` API per il flusso live dalla fotocamera e la possibilità di acquisire una foto prima pagina personalizzata viene aggiunta per fornire il `Canvas` per la visualizzazione.

Quando si implementa un renderer personalizzato che deriva da `PageRenderer` in Windows Runtime, il `ArrangeOverride` metodo deve essere implementato anche per disporre i controlli della pagina, in quanto il renderer di base non sa cosa farne. In caso contrario, risultati di una pagina vuota. Pertanto, in questo esempio il `ArrangeOverride` chiamate al metodo di `Arrange` metodo sul `Page` istanza.

> [!NOTE]
> **Nota**: è importante arrestare ed eliminare oggetti che forniscono l'accesso alla camera in un'applicazione Windows Phone 8.1 WinRT. In caso contrario, può interferire con altre applicazioni che tentano di accedere la fotocamera del dispositivo. Per ulteriori informazioni, vedere il `CleanUpCaptureResourcesAsync` metodo nel progetto nella soluzione di esempio, Windows Phone e [Guida introduttiva: acquisizione video tramite l'API MediaCapture](https://msdn.microsoft.com/library/windows/apps/xaml/dn642092.aspx).

### <a name="creating-the-page-renderer-on-uwp"></a>Creazione di Renderer di pagine in UWP

Esempio di codice seguente viene illustrato il renderer di pagine per UWP:

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
                SetupCamera();

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

La chiamata per la classe base `OnElementChanged` metodo crea un `FrameworkElement` controllo, in cui viene eseguito il rendering della pagina. Il flusso in tempo reale della fotocamera solo viene eseguito il rendering condizione che il renderer non è già collegato a un elemento esistente di xamarin. Forms, condizione che esiste un'istanza di pagina che viene eseguito il rendering dal renderer personalizzato. La pagina viene quindi personalizzata tramite la chiamata di una serie di metodi che utilizzano il `MediaCapture` API per il flusso live dalla fotocamera e la possibilità di acquisire una foto prima pagina personalizzata viene aggiunta per fornire il `Children` insieme per la visualizzazione.

Quando si implementa un renderer personalizzato che deriva da `PageRenderer` in UWP, il `ArrangeOverride` metodo deve essere implementato anche per disporre i controlli della pagina, in quanto il renderer di base non sa cosa farne. In caso contrario, risultati di una pagina vuota. Pertanto, in questo esempio il `ArrangeOverride` chiamate al metodo di `Arrange` metodo sul `Page` istanza.

> [!NOTE]
> **Nota**: è importante arrestare ed eliminare oggetti che forniscono l'accesso alla camera in un'applicazione UWP. In caso contrario, può interferire con altre applicazioni che tentano di accedere la fotocamera del dispositivo. Per ulteriori informazioni, vedere [visualizzare l'anteprima della fotocamera](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access).

## <a name="summary"></a>Riepilogo

In questo articolo ha illustrato come creare un renderer personalizzato per il [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) pagina, consentendo agli sviluppatori di eseguire l'override per il rendering predefinito nativo con le personalizzazioni specifiche della piattaforma. Oggetto `ContentPage` è un elemento visivo che consente di visualizzare una singola visualizzazione e occupa la maggior parte della schermata.


## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererContentPage (sample)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/)
