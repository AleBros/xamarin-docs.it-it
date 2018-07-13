---
title: Implementazione di una vista
description: Questo articolo illustra come creare un renderer personalizzato per un controllo personalizzato di xamarin. Forms che consente di visualizzare un flusso video di anteprima dalla fotocamera del dispositivo.
ms.prod: xamarin
ms.assetid: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 8ee9926eb3b726673711141e7c75a68b607d02d3
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994702"
---
# <a name="implementing-a-view"></a>Implementazione di una vista

_Xamarin. Forms controlli dell'interfaccia utente personalizzata devono derivare dalla classe di visualizzazione, che viene usata per posizionare i layout e controlli sullo schermo. Questo articolo illustra come creare un renderer personalizzato per un controllo personalizzato di xamarin. Forms che consente di visualizzare un flusso video di anteprima dalla fotocamera del dispositivo._

Tutte le visualizzazioni di xamarin. Forms sono un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un [ `View` ](xref:Xamarin.Forms.View) viene eseguito il rendering da un'applicazione xamarin. Forms in iOS, il `ViewRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza di nativo `UIView` controllo. La piattaforma Android, il `ViewRenderer` un'istanza nativa `View` controllo. In Universal Windows Platform (UWP), il `ViewRenderer` un'istanza nativa `FrameworkElement` controllo. Per altre informazioni sulle classi di controllo nativo che eseguono il mapping per i controlli di xamarin. Forms e renderer, vedere [Renderer di classi di Base e controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra il [ `View` ](xref:Xamarin.Forms.View) e i corrispondenti controlli nativi che l'implementano:

![](view-images/view-classes.png "Relazione tra la classe di visualizzazione e relativa implementazione delle classi Native")

Il processo di rendering può essere usato per implementare le personalizzazioni specifiche della piattaforma tramite la creazione di un renderer personalizzato per un [ `View` ](xref:Xamarin.Forms.View) in ogni piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Custom_Control) un controllo personalizzato di xamarin. Forms.
1. [Utilizzare](#Consuming_the_Custom_Control) il controllo personalizzato da xamarin. Forms.
1. [Creare](#Creating_the_Custom_Renderer_on_each_Platform) il renderer personalizzato per il controllo in ogni piattaforma.

Ogni elemento a questo punto discuteremo a sua volta, per implementare un `CameraPreview` renderer che consente di visualizzare un flusso video di anteprima dalla fotocamera del dispositivo. Se si tocca il flusso video verrà arrestarlo e avviarlo.

<a name="Creating_the_Custom_Control" />

## <a name="creating-the-custom-control"></a>Creazione di un controllo personalizzato

È possibile creare un controllo personalizzato creando sottoclassi il [ `View` ](xref:Xamarin.Forms.View) classe, come illustrato nell'esempio di codice seguente:

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

Il `CameraPreview` controllo personalizzato viene creato nel progetto di libreria (PCL) di classi portabile e definisce l'API per il controllo. Il controllo personalizzato espone un `Camera` proprietà utilizzata per controllare se il flusso video deve essere visualizzato dalla parte anteriore o posteriore della fotocamera del dispositivo. Se non viene specificato un valore per il `Camera` proprietà quando viene creato il controllo, per impostazione predefinita che specifica la fotocamera posteriore.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Utilizzo del controllo personalizzato

Il `CameraPreview` controllo personalizzato è reperibile in XAML nel progetto libreria di classi Portabile dichiara uno spazio dei nomi per la relativa posizione e usando il prefisso dello spazio dei nomi dell'elemento di controllo personalizzato. Nell'esempio di codice riportato di seguito viene illustrato come il `CameraPreview` controlli personalizzati possono essere usati da una pagina XAML:

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

Il `local` prefisso dello spazio dei nomi può essere denominata alcuna operazione. Tuttavia, il `clr-namespace` e `assembly` valori devono corrispondere i dettagli del controllo personalizzato. Una volta lo spazio dei nomi è dichiarato, il prefisso viene utilizzato per fare riferimento al controllo personalizzato.

Nell'esempio di codice riportato di seguito viene illustrato come il `CameraPreview` controlli personalizzati possono essere usati da una pagina di c#:

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

Un'istanza di `CameraPreview` controllo personalizzato da utilizzare per visualizzare il flusso video di anteprima dalla fotocamera del dispositivo. A parte, facoltativamente, specificando un valore per il `Camera` proprietà, la personalizzazione del controllo verrà eseguita nel programma di rendering personalizzata.

Un renderer personalizzato può ora essere aggiunti a ogni progetto di applicazione per creare controlli di anteprima della fotocamera specifiche della piattaforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creare il Renderer personalizzato a seconda della piattaforma

Il processo per la creazione della classe renderer personalizzati è come segue:

1. Creare una sottoclasse del `ViewRenderer<T1,T2>` classe che esegue il rendering del controllo personalizzato. Il primo argomento tipo deve essere il controllo personalizzato in questo caso il renderer è, `CameraPreview`. Il secondo argomento tipo deve essere del controllo nativo che implementerà il controllo personalizzato.
1. Eseguire l'override di `OnElementChanged` metodo che esegue il rendering il controllo e scrivere la logica personalizzata per personalizzarla. Questo metodo viene chiamato quando viene creato il controllo di xamarin. Forms corrispondente.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzato per specificare che verrà usata per eseguire il rendering del controllo personalizzato di xamarin. Forms. Questo attributo viene utilizzato per registrare il renderer personalizzato con xamarin. Forms.

> [!NOTE]
> Per la maggior parte degli elementi di xamarin. Forms, è facoltativo per fornire un renderer personalizzato in ogni progetto della piattaforma. Se non è registrato un renderer personalizzato, quindi verrà utilizzato il renderer predefinito per la classe di base del controllo. Renderer personalizzati sono tuttavia necessarie in ogni progetto della piattaforma durante il rendering di un [vista](xref:Xamarin.Forms.View) elemento.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con le relazioni tra di essi:

![](view-images/solution-structure.png "Responsabilità di progetto Renderer personalizzati CameraPreview")

Il `CameraPreview` viene eseguito il rendering di controlli personalizzati dalle classi renderer specifica della piattaforma, che derivano da tutti i `ViewRenderer` classe per ogni piattaforma. Di conseguenza ogni `CameraPreview` controllo personalizzato viene eseguito il rendering con controlli specifici della piattaforma, come illustrato negli screenshot seguenti:

![](view-images/screenshots.png "CameraPreview in ogni piattaforma")

Il `ViewRenderer` classe espone il `OnElementChanged` metodo, che viene chiamato quando viene creato il controllo personalizzato di xamarin. Forms per eseguire il rendering del controllo nativo corrispondente. Questo metodo accetta un `ElementChangedEventArgs` parametro che contiene `OldElement` e `NewElement` proprietà. Queste proprietà rappresentano l'elemento di xamarin. Forms che il renderer *è stata* collegata, l'elemento di xamarin. Forms e che il renderer *è* collegati rispettivamente. Nell'applicazione di esempio, il `OldElement` proprietà sarà `null` e il `NewElement` proprietà conterrà un riferimento al `CameraPreview` istanza.

Una versione override del `OnElementChanged` metodo, in ogni classe renderer specifica della piattaforma, è possibile eseguire la creazione di istanze di controllo nativo e personalizzazione. Il `SetNativeControl` metodo deve essere usato per creare un'istanza del controllo nativo e questo metodo si assegna il riferimento di controllo per il `Control` proprietà. Inoltre, un riferimento al controllo di xamarin. Forms che viene eseguito il rendering può essere ottenuto tramite il `Element` proprietà.

In alcuni casi, il `OnElementChanged` metodo può essere chiamato più volte. Pertanto, per evitare perdite di memoria, è necessario prestare attenzione quando si crea un nuovo controllo nativo. L'approccio da usare quando si crea un'istanza di un nuovo controllo nativo in un renderer personalizzato è illustrato nell'esempio di codice seguente:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (Control == null) {
    // Instantiate the native control and assign it to the Control property with
    // the SetNativeControl method
  }

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the control and subscribe to event handlers
  }
}
```

Per un nuovo controllo nativo l'istanza deve essere creata solo una volta, quando la proprietà `Control` è `null`. Configurare il controllo e sottoscrivere i gestori degli eventi solo quando il renderer personalizzato è allegato a un nuovo elemento di Xamarin.Forms. Analogamente, i gestori degli eventi sottoscritti deve essere annullate solo da quando viene modificato l'elemento a cui è associato il renderer. Adottando questo approccio consente di creare un renderer personalizzato ad alte prestazioni che non subisce perdite di memoria.

Ogni classe renderer personalizzato è decorata con un `ExportRenderer` attributo che registra il renderer con xamarin. Forms. L'attributo accetta due parametri: il nome del tipo del controllo personalizzato xamarin. Forms viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il `assembly` prefisso per l'attributo specifica che l'attributo viene applicato all'intero assembly.

Le sezioni seguenti illustrano l'implementazione di ogni classe renderer personalizzato specifico della piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creare il Renderer personalizzati in iOS

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma iOS:

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

            if (Control == null) {
                uiCameraPreview = new UICameraPreview (e.NewElement.Camera);
                SetNativeControl (uiCameraPreview);
            }
            if (e.OldElement != null) {
                // Unsubscribe
                uiCameraPreview.Tapped -= OnCameraPreviewTapped;
            }
            if (e.NewElement != null) {
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

A condizione che il `Control` proprietà è `null`, il `SetNativeControl` metodo viene chiamato per creare un'istanza di un nuovo `UICameraPreview` controllo e di assegnare un riferimento a esso con il `Control` proprietà. Il `UICameraPreview` controllo è un controllo personalizzato specifico della piattaforma che utilizza il `AVCapture` le API per fornire il flusso di anteprima dalla fotocamera. Espone un `Tapped` evento che viene gestita dal `OnCameraPreviewTapped` metodo per arrestare e avviare l'anteprima video quando lo si tocca. Il `Tapped` evento è sottoscritto quando il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms e annullato la sottoscrizione da solo quando l'elemento del renderer viene collegato a modifiche.

### <a name="creating-the-custom-renderer-on-android"></a>Creare il Renderer personalizzati in Android

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma Android:

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

            if (Control == null)
            {
                cameraPreview = new CameraPreview(Context);
                SetNativeControl(cameraPreview);
            }

            if (e.OldElement != null)
            {
                // Unsubscribe
                cameraPreview.Click -= OnCameraPreviewClicked;
            }
            if (e.NewElement != null)
            {
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

A condizione che il `Control` proprietà è `null`, il `SetNativeControl` metodo viene chiamato per creare un'istanza di un nuovo `CameraPreview` controllano e assegnare un riferimento a esso con il `Control` proprietà. Il `CameraPreview` controllo è un controllo personalizzato specifico della piattaforma che utilizza il `Camera` API per fornire il flusso di anteprima dalla fotocamera. Il `CameraPreview` controllo quindi viene configurato, condizione che il renderer personalizzato è allegato a un nuovo elemento di xamarin. Forms. Questa configurazione prevede la creazione di un nuovo nativo `Camera` dell'oggetto per accedere a una fotocamera hardware specifico e la registrazione di un gestore eventi per elaborare il `Click` evento. A sua volta questo gestore verrà avviati e arrestati dell'anteprima video quando si viene toccato. Il `Click` evento viene annullato la sottoscrizione a, se l'elemento di xamarin. Forms il renderer è associato alle modifiche.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creare il Renderer personalizzato nella piattaforma UWP

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma UWP:

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

            if (Control == null)
            {
                ...
                _captureElement = new CaptureElement();
                _captureElement.Stretch = Stretch.UniformToFill;

                SetupCamera();
                SetNativeControl(_captureElement);
            }
            if (e.OldElement != null)
            {
                // Unsubscribe
                Tapped -= OnCameraPreviewTapped;
                ...
            }
            if (e.NewElement != null)
            {
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

A condizione che il `Control` proprietà è `null`, un nuovo `CaptureElement` viene creata un'istanza e il `SetupCamera` viene chiamato il metodo che usa il `MediaCapture` API per fornire il flusso di anteprima dalla fotocamera. Il `SetNativeControl` viene quindi chiamato il metodo per assegnare un riferimento al `CaptureElement` dell'istanza per il `Control` proprietà. Il `CaptureElement` controllo espone un `Tapped` evento che viene gestita dal `OnCameraPreviewTapped` metodo per arrestare e avviare l'anteprima video quando lo si tocca. Il `Tapped` evento è sottoscritto quando il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms e annullato la sottoscrizione da solo quando l'elemento del renderer viene collegato a modifiche.

> [!NOTE]
> È importante arrestare ed eliminare gli oggetti che forniscono l'accesso alla fotocamera in un'applicazione UWP. In caso contrario, può interferire con altre applicazioni che tentano di accedere la fotocamera del dispositivo. Per altre informazioni, vedere [visualizzare l'anteprima della fotocamera](/windows/uwp/audio-video-camera/simple-camera-preview-access/).

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare un renderer personalizzato per un controllo personalizzato di xamarin. Forms che consente di visualizzare un flusso video di anteprima dalla fotocamera del dispositivo. Xamarin. Forms controlli dell'interfaccia utente personalizzata devono derivare dal [ `View` ](xref:Xamarin.Forms.View) classe, che viene usato per posizionare i layout e controlli sullo schermo.


## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/view/)
