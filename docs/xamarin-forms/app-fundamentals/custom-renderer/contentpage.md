---
title: Personalizzazione di un elemento ContentPage
description: ContentPage è un elemento visivo con visualizzazione singola che occupa la maggior parte dello schermo. Questo articolo illustra come creare un renderer personalizzato per la pagina ContentPage che consente agli sviluppatori di eseguire l'override del rendering nativo predefinito usando una personalizzazione specifica della piattaforma.
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ca9a541c3d152d1b84ed682881c395f2199b9eaf
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84574379"
---
# <a name="customizing-a-contentpage"></a>Personalizzazione di un elemento ContentPage

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-contentpage)

_Un ContentPage è un elemento visivo che visualizza una singola visualizzazione e occupa la maggior parte dello schermo. Questo articolo illustra come creare un renderer personalizzato per la pagina ContentPage, consentendo agli sviluppatori di eseguire l'override del rendering nativo predefinito con una personalizzazione specifica della piattaforma._

Ogni Xamarin.Forms controllo dispone di un renderer associato per ogni piattaforma che crea un'istanza di un controllo nativo. Quando un oggetto viene sottoposto a [`ContentPage`](xref:Xamarin.Forms.ContentPage) rendering da un' Xamarin.Forms applicazione, in iOS `PageRenderer` viene creata un'istanza della classe, che a sua volta crea un'istanza di un `UIViewController` controllo nativo. Nella piattaforma Android la classe `PageRenderer` crea un'istanza di un controllo `ViewGroup`. Nella piattaforma UWP (Universal Windows Platform) la classe `PageRenderer` crea un'istanza di un controllo `FrameworkElement`. Per ulteriori informazioni sul renderer e sulle classi di controlli native che Xamarin.Forms controllano la mappa a, vedere [classi base renderer e controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra [`ContentPage`](xref:Xamarin.Forms.ContentPage) e i controlli nativi corrispondenti che lo implementano:

![](contentpage-images/contentpage-classes.png "Relationship Between ContentPage Class and Implementing Native Controls")

Per implementare personalizzazioni specifiche della piattaforma, è possibile sfruttare il processo di rendering creando un renderer personalizzato per un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) in ogni piattaforma. Il processo per eseguire questa operazione è il seguente:

1. [Creare](#creating-the-xamarinforms-page) una Xamarin.Forms pagina.
1. [Utilizzare](#consuming-the-xamarinforms-page) la pagina da Xamarin.Forms .
1. [Creare](#creating-the-page-renderer-on-each-platform) il renderer personalizzato per la pagina in ogni piattaforma.

Verrà ora discusso un elemento alla volta per implementare un oggetto `CameraPage` che rende disponibile un feed di fotocamera live e la possibilità di scattare una foto.

## <a name="creating-the-xamarinforms-page"></a>Creazione della Xamarin.Forms pagina

Un oggetto non modificato [`ContentPage`](xref:Xamarin.Forms.ContentPage) può essere aggiunto al progetto condiviso Xamarin.Forms , come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

Analogamente, il file code-behind per l'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) deve rimanere inalterato, come illustrato nell'esempio di codice seguente:

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

L'esempio di codice seguente illustra come creare la pagina in C#:

```csharp
public class CameraPageCS : ContentPage
{
    public CameraPageCS ()
    {
    }
}
```

Per visualizzare il feed di fotocamera live in ogni piattaforma verrà usata un'istanza di `CameraPage`. La personalizzazione del controllo avviene nel renderer personalizzato. Non è pertanto necessaria alcuna implementazione aggiuntiva nella classe `CameraPage`.

## <a name="consuming-the-xamarinforms-page"></a>Utilizzo della Xamarin.Forms pagina

Il vuoto `CameraPage` deve essere visualizzato dall' Xamarin.Forms applicazione. Ciò accade quando si tocca un pulsante nell'istanza di `MainPage` che a sua volta esegue il metodo `OnTakePhotoButtonClicked`, come illustrato nell'esempio di codice seguente:

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

Questo codice passa semplicemente all'oggetto `CameraPage` nel quale i renderer personalizzati personalizzeranno l'aspetto della pagina in ogni piattaforma.

## <a name="creating-the-page-renderer-on-each-platform"></a>Creazione del renderer di pagina in ogni piattaforma

Il processo di creazione della classe di renderer personalizzato è il seguente:

1. Creare una sottoclasse della classe `PageRenderer`.
1. Eseguire l'override del metodo `OnElementChanged` che esegue il rendering della pagina nativa e scrivere la logica per personalizzare la pagina. Il `OnElementChanged` metodo viene chiamato quando Xamarin.Forms viene creato il controllo corrispondente.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer della pagina per specificare che verrà usato per il rendering della Xamarin.Forms pagina. Questo attributo viene usato per registrare il renderer personalizzato con Xamarin.Forms .

> [!NOTE]
> La specifica di un renderer di pagina nel progetto di ogni piattaforma è facoltativa. Se non si registra un renderer personalizzato, verrà usato il renderer predefinito per la pagina.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alla relazione tra di esse:

![](contentpage-images/solution-structure.png "CameraPage Custom Renderer Project Responsibilities")

Il rendering dell'istanza di `CameraPage` viene eseguito dalle classi `CameraPageRenderer` specifiche della piattaforma che derivano tutte dalla classe `PageRenderer` per la piattaforma. Ciò determina il rendering di ogni istanza di `CameraPage` con un feed di fotocamera live, come illustrato negli screenshot seguenti:

![](contentpage-images/screenshots.png "CameraPage on each Platform")

La `PageRenderer` classe espone il `OnElementChanged` metodo, che viene chiamato quando Xamarin.Forms viene creata la pagina per eseguire il rendering del controllo nativo corrispondente. Questo metodo accetta un parametro `ElementChangedEventArgs` che contiene le proprietà `OldElement` e `NewElement`. Queste proprietà rappresentano l' Xamarin.Forms elemento a cui è *stato* collegato il renderer e l' Xamarin.Forms elemento a cui *è* collegato rispettivamente il renderer. Nell'applicazione di esempio la proprietà `OldElement` sarà `null` e la proprietà `NewElement` conterrà un riferimento all'istanza di `CameraPage`.

La personalizzazione della pagina nativa deve essere eseguita in una versione sostituita del metodo `OnElementChanged` nella classe `CameraPageRenderer`. Un riferimento all' Xamarin.Forms istanza di pagina di cui viene eseguito il rendering può essere ottenuto tramite la `Element` Proprietà.

Ogni classe renderer personalizzata è decorata con un `ExportRenderer` attributo che registra il renderer con Xamarin.Forms . L'attributo accetta due parametri: il nome del tipo della Xamarin.Forms pagina di cui viene eseguito il rendering e il nome del tipo del renderer personalizzato. Il prefisso `assembly` dell'attributo specifica che l'attributo viene applicato all'intero assembly.

Le sezioni seguenti illustrano l'implementazione del renderer personalizzato `CameraPageRenderer` per ogni piattaforma.

### <a name="creating-the-page-renderer-on-ios"></a>Creazione del renderer di pagina in iOS

L'esempio di codice seguente illustra il renderer di pagina per la piattaforma iOS:

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

La chiamata al metodo `OnElementChanged` della classe di base crea un'istanza di un controllo `UIViewController` iOS. Viene eseguito il rendering del flusso della fotocamera in tempo reale purché il renderer non sia già collegato a un Xamarin.Forms elemento esistente e purché esista un'istanza di pagina di cui è in corso il rendering dal renderer personalizzato.

La pagina viene quindi personalizzata da una serie di metodi che usano le API `AVCapture` per consentire lo streaming live dalla fotocamera e la possibilità di scattare una foto.

### <a name="creating-the-page-renderer-on-android"></a>Creazione del renderer di pagina in Android

L'esempio di codice seguente illustra il renderer di pagina per la piattaforma Android:

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

La chiamata al metodo `OnElementChanged` della classe di base crea un'istanza di un controllo `ViewGroup` Android, ovvero un gruppo di visualizzazioni. Viene eseguito il rendering del flusso della fotocamera in tempo reale purché il renderer non sia già collegato a un Xamarin.Forms elemento esistente e purché esista un'istanza di pagina di cui è in corso il rendering dal renderer personalizzato.

La pagina viene quindi personalizzata richiamando una serie di metodi che usano l'API `Camera` per consentire lo streaming live dalla fotocamera e la possibilità di scattare una foto prima che venga richiamato il metodo `AddView` per aggiungere l'interfaccia utente dello streaming live della fotocamera al controllo `ViewGroup`. Tenere presente che in Android è anche necessario eseguire l'override del metodo `OnLayout` per eseguire operazioni di misura e layout nella visualizzazione. Per altre informazioni, vedere l'[esempio di renderer ContentPage](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-contentpage).

### <a name="creating-the-page-renderer-on-uwp"></a>Creazione del renderer di pagina in UWP

L'esempio di codice seguente illustra il renderer di pagina per la piattaforma UWP:

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

La chiamata al metodo `OnElementChanged` della classe di base crea un'istanza di un controllo `FrameworkElement` in cui viene eseguito il rendering della pagina. Viene eseguito il rendering del flusso della fotocamera in tempo reale purché il renderer non sia già collegato a un Xamarin.Forms elemento esistente e purché esista un'istanza di pagina di cui è in corso il rendering dal renderer personalizzato. La pagina viene quindi personalizzata richiamando una serie di metodi che usano l'API `MediaCapture` per consentire lo streaming live dalla fotocamera e la possibilità di scattare una foto prima che la pagina personalizzata sia aggiunta alla raccolta `Children` per la visualizzazione.

Quando si implementa un renderer personalizzato che deriva da `PageRenderer` in UWP, è consigliabile implementare anche il metodo `ArrangeOverride` per disporre i controlli della pagina perché il renderer di base non è in grado di eseguire questa operazione. In caso contrario, verrà restituita una pagina vuota. In questo esempio il metodo `ArrangeOverride` chiama pertanto il metodo `Arrange` sull'istanza `Page`.

> [!NOTE]
> In un'applicazione UWP è importante arrestare ed eliminare gli oggetti che consentono l'accesso alla fotocamera. Questi possono infatti interferire con altre applicazioni che tentano di accedere alla fotocamera del dispositivo. Per altre informazioni, vedere [Display the camera preview](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access) (Visualizzare l'anteprima della fotocamera).

## <a name="summary"></a>Summary

Questo articolo ha illustrato come creare un renderer personalizzato per la [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagina, consentendo agli sviluppatori di eseguire l'override del rendering nativo predefinito con una personalizzazione specifica della piattaforma. `ContentPage` è un elemento visivo con visualizzazione singola che occupa la maggior parte dello schermo.

## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererContentPage (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-contentpage)
