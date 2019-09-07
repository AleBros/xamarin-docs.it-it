---
title: Implementazione di una vista
description: Questo articolo spiega come creare un renderer personalizzato per un controllo personalizzato di Xamarin.Forms usato per visualizzare un flusso video di anteprima dalla fotocamera del dispositivo.
ms.prod: xamarin
ms.assetid: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: c93feb9527892b7b4c60c9d213361d19d3bc4b93
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771724"
---
# <a name="implementing-a-view"></a>Implementazione di una vista

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-view)

_I controlli dell'interfaccia utente personalizzata di Xamarin.Forms devono derivare dalla classe View, che viene usata per posizionare layout e controlli sullo schermo. Questo articolo spiega come creare un renderer personalizzato per un controllo personalizzato di Xamarin.Forms usato per visualizzare un flusso video di anteprima dalla fotocamera del dispositivo._

A ogni vista di Xamarin.Forms è associato un renderer per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un'applicazione Xamarin.Forms esegue il rendering di un oggetto [`View`](xref:Xamarin.Forms.View) in iOS, viene creata un'istanza della classe `ViewRenderer`, che a sua volta crea un'istanza di un controllo `UIView` nativo. Nella piattaforma Android la classe `ViewRenderer` crea un'istanza di un controllo `View` nativo. Nella piattaforma UWP (Universal Windows Platform) la classe `ViewRenderer` crea un'istanza di un controllo `FrameworkElement` nativo. Per altre informazioni sulle classi del renderer e dei controlli nativi di cui Xamarin.Forms controlla il mapping, vedere [Classi di base e controlli nativi del renderer](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra la classe [`View`](xref:Xamarin.Forms.View) e i controlli nativi corrispondenti che la implementano:

![](view-images/view-classes.png "Relazione tra la classe View e le classi native che la implementano")

È possibile usare il processo di rendering per implementare personalizzazioni specifiche della piattaforma creando un renderer personalizzato per un elemento [`View`](xref:Xamarin.Forms.View) in ogni piattaforma. Il processo per eseguire questa operazione è il seguente:

1. [Creare](#Creating_the_Custom_Control) un controllo personalizzato Xamarin.Forms.
1. [Utilizzare](#Consuming_the_Custom_Control) il controllo personalizzato da Xamarin.Forms.
1. [Creare](#Creating_the_Custom_Renderer_on_each_Platform) il renderer personalizzato per il controllo in ogni piattaforma.

Ogni elemento verrà trattato separatamente, per implementare un renderer `CameraPreview` che consente di visualizzare un'anteprima del flusso video dalla fotocamera del dispositivo. Toccare il flusso video per arrestarlo e avviarlo.

<a name="Creating_the_Custom_Control" />

## <a name="creating-the-custom-control"></a>Creazione del controllo personalizzato

Per creare un controllo personalizzato, è possibile sottoclassare la classe [`View`](xref:Xamarin.Forms.View) come illustrato nell'esempio di codice seguente:

```csharp
public class CameraPreview : View
{
  public static readonly BindableProperty CameraProperty = BindableProperty.Create (
    propertyName: "Camera",
    returnType: typeof(CameraOptions),
    declaringType: typeof(CameraPreview),
    defaultValue: CameraOptions.Rear);

  public CameraOptions Camera {
    get { return (CameraOptions)GetValue (CameraProperty); }
    set { SetValue (CameraProperty, value); }
  }
}
```

Il controllo personalizzato `CameraPreview` viene creato nel progetto di libreria .NET Standard e definisce l'API per il controllo. Il controllo personalizzato espone una proprietà `Camera` che viene usata per stabilire se il flusso video deve essere visualizzato dalla fotocamera anteriore o posteriore del dispositivo. Se non viene specificato un valore per la proprietà `Camera` quando viene creato il controllo, per impostazione predefinita viene specificata la fotocamera posteriore.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Uso del controllo personalizzato

Per fare riferimento al controllo personalizzato `CameraPreview` in XAML nel progetto di libreria .NET Standard, è possibile dichiarare uno spazio dei nomi per il percorso e usare il prefisso dello spazio dei nomi dell'elemento del controllo personalizzato. Nell'esempio di codice riportato di seguito viene illustrato come il controllo personalizzato `CameraPreview` può essere usato da una pagina XAML:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             ...>
    <ContentPage.Content>
        <StackLayout>
            <Label Text="Camera Preview:" />
            <local:CameraPreview Camera="Rear"
              HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Il prefisso dello spazio dei nomi `local` può avere qualsiasi nome. I valori `clr-namespace` e `assembly` devono tuttavia corrispondere ai dettagli del controllo personalizzato. Dopo aver dichiarato lo spazio dei nomi, il prefisso viene usato per fare riferimento al controllo personalizzato.

Nell'esempio di codice riportato di seguito viene illustrato come il controllo personalizzato `CameraPreview` può essere usato da una pagina C#:

```csharp
public class MainPageCS : ContentPage
{
  public MainPageCS ()
  {
    ...
    Content = new StackLayout {
      Children = {
        new Label { Text = "Camera Preview:" },
        new CameraPreview {
          Camera = CameraOptions.Rear,
          HorizontalOptions = LayoutOptions.FillAndExpand,
          VerticalOptions = LayoutOptions.FillAndExpand
        }
      }
    };
  }
}
```

Un'istanza del controllo personalizzato `CameraPreview` verrà usata per visualizzare il flusso video di anteprima dalla fotocamera del dispositivo. Oltre a specificare, se necessario, un valore per la proprietà `Camera`, la personalizzazione del controllo verrà eseguita nel renderer personalizzato.

Un renderer personalizzato può ora essere aggiunto a ogni progetto di applicazione per creare controlli di anteprima della fotocamera specifici della piattaforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creazione del renderer personalizzato in ogni piattaforma

Di seguito è illustrato il processo di creazione della classe di renderer personalizzato:

1. Creare una sottoclasse della classe `ViewRenderer<T1,T2>` che esegue il rendering del controllo personalizzato. Il primo argomento tipo deve essere il controllo personalizzato per cui si usa il renderer, in questo caso `CameraPreview`. Il secondo argomento tipo deve essere il controllo nativo che implementerà il controllo personalizzato.
1. Eseguire l'override del metodo `OnElementChanged` che esegue il rendering del controllo personalizzato e scrivere la logica per personalizzarlo. Il metodo viene chiamato quando viene creato il controllo Xamarin.Forms corrispondente.
1. Aggiungere un attributo `ExportRenderer` alla classe di renderer personalizzato per specificare che verrà usata per eseguire il rendering del controllo Xamarin.Forms personalizzato. L'attributo viene usato per registrare il renderer personalizzato con Xamarin.Forms.

> [!NOTE]
> Per la maggior parte degli elementi di Xamarin.Forms, l'indicazione di un renderer personalizzato in ogni progetto della piattaforma è facoltativa. Se un renderer personalizzato non è registrato, verrà usato il renderer predefinito per la classe di base del controllo. I renderer personalizzati sono tuttavia necessari in ogni progetto della piattaforma quando si esegue il rendering di un elemento [View](xref:Xamarin.Forms.View).

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazioni tra di essi:

![](view-images/solution-structure.png "Responsabilità del progetto di renderer personalizzato CameraPreview")

Il rendering del controllo personalizzato `CameraPreview` viene eseguito dalle classi di renderer specifiche della piattaforma che derivano tutte dalla classe `ViewRenderer` per ogni piattaforma. Di conseguenza il rendering di ogni controllo `CameraPreview` viene eseguito con controlli specifici della piattaforma, come illustrato negli screenshot seguenti:

![](view-images/screenshots.png "CameraPreview in ogni piattaforma")

La classe `ViewRenderer` espone il metodo `OnElementChanged` che viene chiamato quando si crea il controllo personalizzato Xamarin.Forms per eseguire il rendering del controllo nativo corrispondente. Questo metodo accetta un parametro `ElementChangedEventArgs` che contiene le proprietà `OldElement` e `NewElement`. Queste proprietà rappresentano rispettivamente l'elemento di Xamarin.Forms a cui il renderer *era* associato e l'elemento di Xamarin.Forms a cui il renderer *è* associato. Nell'applicazione di esempio la proprietà `OldElement` sarà `null` e la proprietà `NewElement` conterrà un riferimento all'istanza di `CameraPreview`.

La creazione dell'istanza e la personalizzazione del controllo nativo devono essere eseguite in una versione sostituita del metodo `OnElementChanged` in ogni classe di renderer specifica della piattaforma. Il metodo `SetNativeControl` deve essere usato per creare un'istanza del controllo nativo e assegna anche il riferimento al controllo alla proprietà `Control`. È inoltre possibile ottenere un riferimento al controllo Xamarin.Forms di cui viene eseguito il rendering usando la proprietà `Element`.

In alcune circostanze il metodo `OnElementChanged` può essere chiamato più volte. Di conseguenza, per evitare perdite di memoria, prestare attenzione quando si crea un'istanza di un nuovo controllo nativo. L'approccio da usare quando si crea un'istanza di un nuovo controllo nativo in un renderer personalizzato è illustrato nell'esempio di codice seguente:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    if (Control == null) {
      // Instantiate the native control and assign it to the Control property with
      // the SetNativeControl method
    }
    // Configure the control and subscribe to event handlers
  }
}
```

Per un nuovo controllo nativo l'istanza deve essere creata solo una volta, quando la proprietà `Control` è `null`. È inoltre necessario configurare il controllo e sottoscrivere i gestori degli eventi solo quando il renderer personalizzato è allegato a un nuovo elemento di Xamarin.Forms. Analogamente, per i gestori degli eventi sottoscritti l'iscrizione deve essere annullata solo in caso di modifica dell'elemento a cui è allegato il renderer. L'adozione di questo approccio consente di creare un renderer personalizzato con prestazioni elevate che non subisce perdite di memoria.

> [!IMPORTANT]
> Il metodo `SetNativeControl` deve essere chiamato solo se `e.NewElement` è diverso da `null`.

Ogni classe del renderer personalizzato è decorata con un attributo `ExportRenderer` che registra il renderer in Xamarin.Forms. L'attributo accetta due parametri: il nome del tipo di controllo personalizzato Xamarin.Forms di cui viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il prefisso `assembly` dell'attributo specifica che l'attributo viene applicato all'intero assembly.

Le sezioni seguenti illustrano l'implementazione della classe di renderer personalizzato specifica di ogni piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creazione del renderer personalizzato in iOS

L'esempio di codice seguente illustra il renderer di personalizzato per la piattaforma iOS:

```csharp
[assembly: ExportRenderer (typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, UICameraPreview>
    {
        UICameraPreview uiCameraPreview;

        protected override void OnElementChanged (ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                // Unsubscribe
                uiCameraPreview.Tapped -= OnCameraPreviewTapped;
            }
            if (e.NewElement != null) {
                if (Control == null) {
                  uiCameraPreview = new UICameraPreview (e.NewElement.Camera);
                  SetNativeControl (uiCameraPreview);
                }
                // Subscribe
                uiCameraPreview.Tapped += OnCameraPreviewTapped;
            }
        }

        void OnCameraPreviewTapped (object sender, EventArgs e)
        {
            if (uiCameraPreview.IsPreviewing) {
                uiCameraPreview.CaptureSession.StopRunning ();
                uiCameraPreview.IsPreviewing = false;
            } else {
                uiCameraPreview.CaptureSession.StartRunning ();
                uiCameraPreview.IsPreviewing = true;
            }
        }
        ...
    }
}
```

Se la proprietà `Control` è `null`, viene chiamato il metodo `SetNativeControl` per creare un'istanza di un nuovo controllo `UICameraPreview` e assegnare un riferimento al controllo alla proprietà `Control`. Il controllo `UICameraPreview` è un controllo personalizzato specifico della piattaforma che usa le API `AVCapture` per definire il flusso di anteprima della fotocamera. Espone un evento `Tapped` gestito dal metodo `OnCameraPreviewTapped` per arrestare e avviare l'anteprima video quando viene toccata. L'evento `Tapped` è sottoscritto quando il renderer personalizzato è associato a un nuovo elemento di Xamarin.Forms e la sottoscrizione viene annullata solo se l'elemento a cui è associato il renderer cambia.

### <a name="creating-the-custom-renderer-on-android"></a>Creazione del renderer personalizzato in Android

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma Android:

```csharp
[assembly: ExportRenderer(typeof(CustomRenderer.CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPreviewRenderer : ViewRenderer<CustomRenderer.CameraPreview, CustomRenderer.Droid.CameraPreview>
    {
        CameraPreview cameraPreview;

        public CameraPreviewRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<CustomRenderer.CameraPreview> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
                cameraPreview.Click -= OnCameraPreviewClicked;
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                  cameraPreview = new CameraPreview(Context);
                  SetNativeControl(cameraPreview);
                }
                Control.Preview = Camera.Open((int)e.NewElement.Camera);

                // Subscribe
                cameraPreview.Click += OnCameraPreviewClicked;
            }
        }

        void OnCameraPreviewClicked(object sender, EventArgs e)
        {
            if (cameraPreview.IsPreviewing)
            {
                cameraPreview.Preview.StopPreview();
                cameraPreview.IsPreviewing = false;
            }
            else
            {
                cameraPreview.Preview.StartPreview();
                cameraPreview.IsPreviewing = true;
            }
        }
        ...
    }
}
```

Se la proprietà `Control` è `null`, viene chiamato il metodo `SetNativeControl` per creare un'istanza di un nuovo controllo `CameraPreview` e assegnare un riferimento al controllo alla proprietà `Control`. Il controllo `CameraPreview` è un controllo personalizzato specifico della piattaforma che usa l'API `Camera` per definire il flusso di anteprima della fotocamera. Il controllo `CameraPreview` viene quindi configurato a condizione che il renderer personalizzato sia associato a un nuovo elemento di Xamarin.Forms. Questa configurazione prevede la creazione di un nuovo oggetto `Camera` nativo per accedere a una determinata fotocamera hardware e la registrazione di un gestore eventi per elaborare l'evento `Click`. A sua volta il gestore avvia e arresta l'anteprima video quando viene toccata. L'iscrizione dell'evento `Click` viene annullata se si modifica l'elemento di Xamarin.Forms a cui è associato il renderer.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creazione del renderer personalizzato in UWP

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma UWP:

```csharp
[assembly: ExportRenderer(typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, Windows.UI.Xaml.Controls.CaptureElement>
    {
        ...
        CaptureElement _captureElement;
        bool _isPreviewing;

        protected override void OnElementChanged(ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
                Tapped -= OnCameraPreviewTapped;
                ...
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                  ...
                  _captureElement = new CaptureElement();
                  _captureElement.Stretch = Stretch.UniformToFill;

                  SetupCamera();
                  SetNativeControl(_captureElement);
                }
                // Subscribe
                Tapped += OnCameraPreviewTapped;
            }
        }

        async void OnCameraPreviewTapped(object sender, TappedRoutedEventArgs e)
        {
            if (_isPreviewing)
            {
                await StopPreviewAsync();
            }
            else
            {
                await StartPreviewAsync();
            }
        }
        ...
    }
}
```

A condizione che la proprietà `Control` sia `null`, viene creata un'istanza di un nuovo `CaptureElement` e viene chiamato il metodo `SetupCamera`, che usa l'API `MediaCapture` per specificare il flusso di anteprima della fotocamera. Viene quindi chiamato il metodo `SetNativeControl` per assegnare un riferimento all'istanza di `CaptureElement` alla proprietà `Control`. Il controllo `CaptureElement` espone un evento `Tapped` gestito dal metodo `OnCameraPreviewTapped` per arrestare e avviare l'anteprima video quando viene toccata. L'evento `Tapped` è sottoscritto quando il renderer personalizzato è associato a un nuovo elemento di Xamarin.Forms e la sottoscrizione viene annullata solo se l'elemento a cui è associato il renderer cambia.

> [!NOTE]
> In un'applicazione UWP è importante arrestare ed eliminare gli oggetti che consentono l'accesso alla fotocamera. Questi possono infatti interferire con altre applicazioni che tentano di accedere alla fotocamera del dispositivo. Per altre informazioni, vedere [Display the camera preview](/windows/uwp/audio-video-camera/simple-camera-preview-access/) (Visualizzare l'anteprima della fotocamera).

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare un renderer personalizzato per un controllo personalizzato di Xamarin.Forms usato per visualizzare un flusso video di anteprima dalla fotocamera del dispositivo. I controlli dell'interfaccia utente personalizzata di Xamarin.Forms devono derivare dalla classe [`View`](xref:Xamarin.Forms.View), che viene usata per posizionare layout e controlli sullo schermo.

## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-view)
