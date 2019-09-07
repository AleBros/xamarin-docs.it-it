---
title: Personalizzazione di un elemento ContentPage
description: ContentPage è un elemento visivo con visualizzazione singola che occupa la maggior parte dello schermo. Questo articolo illustra come creare un renderer personalizzato per la pagina ContentPage che consente agli sviluppatori di eseguire l'override del rendering nativo predefinito usando una personalizzazione specifica della piattaforma.
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 64367ded8dcd173f7c9e57cfc234aa66712aefd4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772017"
---
# <a name="customizing-a-contentpage"></a>Personalizzazione di un elemento ContentPage

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-contentpage)

_ContentPage è un elemento visivo con visualizzazione singola che occupa la maggior parte dello schermo. Questo articolo illustra come creare un renderer personalizzato per la pagina ContentPage che consente agli sviluppatori di eseguire l'override del rendering nativo predefinito usando una personalizzazione specifica della piattaforma._

A ogni controllo di Xamarin.Forms è associato un renderer per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un'applicazione Xamarin.Forms esegue il rendering di un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage), in iOS viene creata un'istanza della classe `PageRenderer`, che a sua volta crea un'istanza di un controllo `UIViewController` nativo. Nella piattaforma Android la classe `PageRenderer` crea un'istanza di un controllo `ViewGroup`. Nella piattaforma UWP (Universal Windows Platform) la classe `PageRenderer` crea un'istanza di un controllo `FrameworkElement`. Per altre informazioni sulle classi del renderer e dei controlli nativi a cui vengono mappati i controlli Xamarin.Forms, vedere [Classi di base e controlli nativi del renderer](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra la classe [`ContentPage`](xref:Xamarin.Forms.ContentPage) e i controlli nativi corrispondenti che la implementano:

![](contentpage-images/contentpage-classes.png "Relazione tra la classe ContentPage e i controlli nativi che la implementano")

È possibile sfruttare il processo di rendering per implementare personalizzazioni specifiche della piattaforma creando un renderer personalizzato per una classe [`ContentPage`](xref:Xamarin.Forms.ContentPage) in ogni piattaforma. Il processo per eseguire questa operazione è il seguente:

1. [Creare](#Creating_the_Xamarin.Forms_Page) una pagina Xamarin.Forms.
1. [Utilizzare](#Consuming_the_Xamarin.Forms_Page) la pagina da Xamarin.Forms.
1. [Creare](#Creating_the_Page_Renderer_on_each_Platform) il renderer personalizzato per la pagina in ogni piattaforma.

Verrà ora discusso un elemento alla volta per implementare un oggetto `CameraPage` che rende disponibile un feed di fotocamera live e la possibilità di scattare una foto.

<a name="Creating_the_Xamarin.Forms_Page" />

## <a name="creating-the-xamarinforms-page"></a>Creazione della pagina Xamarin.Forms

Al progetto Xamarin.Forms condiviso è possibile aggiungere un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) non modificato, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

In modo analogo, anche il file code-behind per l'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) deve rimanere invariato, come illustrato nell'esempio di codice seguente:

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

<a name="Consuming_the_Xamarin.Forms_Page" />

## <a name="consuming-the-xamarinforms-page"></a>Utilizzo della pagina Xamarin.Forms

L'applicazione Xamarin.Forms deve visualizzare l'oggetto `CameraPage` vuoto. Ciò accade quando si tocca un pulsante nell'istanza di `MainPage` che a sua volta esegue il metodo `OnTakePhotoButtonClicked`, come illustrato nell'esempio di codice seguente:

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

Questo codice passa semplicemente all'oggetto `CameraPage` nel quale i renderer personalizzati personalizzeranno l'aspetto della pagina in ogni piattaforma.

<a name="Creating_the_Page_Renderer_on_each_Platform" />

## <a name="creating-the-page-renderer-on-each-platform"></a>Creazione del renderer di pagina in ogni piattaforma

Di seguito è illustrato il processo di creazione della classe di renderer personalizzato:

1. Creare una sottoclasse della classe `PageRenderer`.
1. Eseguire l'override del metodo `OnElementChanged` che esegue il rendering della pagina nativa e scrivere la logica per personalizzare la pagina. Il metodo `OnElementChanged` viene chiamato quando viene creato il controllo Xamarin.Forms corrispondente.
1. Aggiungere un attributo `ExportRenderer` alla classe di renderer della pagina per specificare che verrà usato per eseguire il rendering della pagina Xamarin.Forms. L'attributo viene usato per registrare il renderer personalizzato in Xamarin.Forms.

> [!NOTE]
> La specifica di un renderer di pagina nel progetto di ogni piattaforma è facoltativa. Se non si registra un renderer personalizzato, verrà usato il renderer predefinito per la pagina.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alla relazione tra di esse:

![](contentpage-images/solution-structure.png "Responsabilità del progetto di renderer personalizzato CameraPage")

Il rendering dell'istanza di `CameraPage` viene eseguito dalle classi `CameraPageRenderer` specifiche della piattaforma che derivano tutte dalla classe `PageRenderer` per la piattaforma. Ciò determina il rendering di ogni istanza di `CameraPage` con un feed di fotocamera live, come illustrato negli screenshot seguenti:

![](contentpage-images/screenshots.png "CameraPage in ogni piattaforma")

La classe `PageRenderer` espone il metodo `OnElementChanged` che viene chiamato quando si crea la pagina Xamarin.Forms per eseguire il rendering del controllo nativo corrispondente. Questo metodo accetta un parametro `ElementChangedEventArgs` che contiene le proprietà `OldElement` e `NewElement`. Queste proprietà rappresentano rispettivamente l'elemento Xamarin.Forms a cui il renderer *era* collegato e l'elemento Xamarin.Forms a cui il renderer *è* collegato. Nell'applicazione di esempio la proprietà `OldElement` sarà `null` e la proprietà `NewElement` conterrà un riferimento all'istanza di `CameraPage`.

La personalizzazione della pagina nativa deve essere eseguita in una versione sostituita del metodo `OnElementChanged` nella classe `CameraPageRenderer`. È possibile ottenere un riferimento all'istanza della pagina Xamarin.Forms di cui viene eseguito il rendering tramite la proprietà `Element`.

Ogni classe del renderer personalizzato è decorata con un attributo `ExportRenderer` che registra il renderer in Xamarin.Forms. L'attributo accetta due parametri: il nome del tipo di pagina Xamarin.Forms di cui viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il prefisso `assembly` dell'attributo specifica che l'attributo viene applicato all'intero assembly.

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

La chiamata al metodo `OnElementChanged` della classe di base crea un'istanza di un controllo `UIViewController` iOS. Il rendering dello streaming live della fotocamera viene eseguito solo a condizione che il renderer non sia già collegato a un elemento Xamarin.Forms esistente e che sia presente un'istanza della pagina di cui il renderer personalizzato stia eseguendo il rendering.

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

La chiamata al metodo `OnElementChanged` della classe di base crea un'istanza di un controllo `ViewGroup` Android, ovvero un gruppo di visualizzazioni. Il rendering dello streaming live della fotocamera viene eseguito solo a condizione che il renderer non sia già collegato a un elemento Xamarin.Forms esistente e che sia presente un'istanza della pagina di cui il renderer personalizzato stia eseguendo il rendering.

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

La chiamata al metodo `OnElementChanged` della classe di base crea un'istanza di un controllo `FrameworkElement` in cui viene eseguito il rendering della pagina. Il rendering dello streaming live della fotocamera viene eseguito solo a condizione che il renderer non sia già collegato a un elemento Xamarin.Forms esistente e che sia presente un'istanza della pagina di cui il renderer personalizzato stia eseguendo il rendering. La pagina viene quindi personalizzata richiamando una serie di metodi che usano l'API `MediaCapture` per consentire lo streaming live dalla fotocamera e la possibilità di scattare una foto prima che la pagina personalizzata sia aggiunta alla raccolta `Children` per la visualizzazione.

Quando si implementa un renderer personalizzato che deriva da `PageRenderer` in UWP, è consigliabile implementare anche il metodo `ArrangeOverride` per disporre i controlli della pagina perché il renderer di base non è in grado di eseguire questa operazione. In caso contrario, verrà restituita una pagina vuota. In questo esempio il metodo `ArrangeOverride` chiama pertanto il metodo `Arrange` sull'istanza `Page`.

> [!NOTE]
> In un'applicazione UWP è importante arrestare ed eliminare gli oggetti che consentono l'accesso alla fotocamera. Questi possono infatti interferire con altre applicazioni che tentano di accedere alla fotocamera del dispositivo. Per altre informazioni, vedere [Display the camera preview](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access) (Visualizzare l'anteprima della fotocamera).

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare un renderer personalizzato per la pagina [`ContentPage`](xref:Xamarin.Forms.ContentPage) che consente agli sviluppatori di eseguire l'override del rendering nativo predefinito usando una personalizzazione specifica della piattaforma. `ContentPage` è un elemento visivo con visualizzazione singola che occupa la maggior parte dello schermo.

## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererContentPage (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-contentpage)
