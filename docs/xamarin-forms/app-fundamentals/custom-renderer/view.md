---
title: "implementazione di una vista" Descrizione: "questo articolo spiega come creare un renderer personalizzato per un Xamarin.Forms controllo personalizzato usato per visualizzare un flusso video di anteprima dalla fotocamera del dispositivo".
ms. prod: Novell MS. AssetID: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 05/10/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---
# <a name="implementing-a-view"></a>Implementazione di una vista

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-view)

_I controlli dell'interfaccia utente personalizzati di Novell. Forms devono derivare dalla classe View, che viene usata per posizionare i layout e i controlli sullo schermo. Questo articolo illustra come creare un renderer personalizzato per un Xamarin.Forms controllo personalizzato usato per visualizzare un flusso video di anteprima dalla fotocamera del dispositivo._

Ogni Xamarin.Forms visualizzazione dispone di un renderer associato per ogni piattaforma che crea un'istanza di un controllo nativo. Quando un oggetto viene sottoposto a [`View`](xref:Xamarin.Forms.View) rendering da un' Xamarin.Forms applicazione in iOS, `ViewRenderer` viene creata un'istanza della classe, che a sua volta crea un'istanza di un `UIView` controllo nativo. Nella piattaforma Android la classe `ViewRenderer` crea un'istanza di un controllo `View` nativo. Nella piattaforma UWP (Universal Windows Platform) la classe `ViewRenderer` crea un'istanza di un controllo `FrameworkElement` nativo. Per ulteriori informazioni sul renderer e sulle classi di controlli native che Xamarin.Forms controllano la mappa a, vedere [classi base renderer e controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra [`View`](xref:Xamarin.Forms.View) e i controlli nativi corrispondenti che lo implementano:

![](view-images/view-classes.png "Relationship Between the View Class and its Implementing Native Classes")

Il processo di rendering può essere utilizzato per implementare personalizzazioni specifiche della piattaforma creando un renderer personalizzato per un [`View`](xref:Xamarin.Forms.View) in ogni piattaforma. Il processo per eseguire questa operazione è il seguente:

1. [Creare](#creating-the-custom-control) un Xamarin.Forms controllo personalizzato.
1. [Utilizzare](#consuming-the-custom-control) il controllo personalizzato da Xamarin.Forms .
1. [Creare](#creating-the-custom-renderer-on-each-platform) il renderer personalizzato per il controllo in ogni piattaforma.

Ogni elemento verrà trattato separatamente, per implementare un renderer `CameraPreview` che consente di visualizzare un'anteprima del flusso video dalla fotocamera del dispositivo. Toccare il flusso video per arrestarlo e avviarlo.

## <a name="creating-the-custom-control"></a>Creazione del controllo personalizzato

È possibile creare un controllo personalizzato creando una sottoclasse della [`View`](xref:Xamarin.Forms.View) classe, come illustrato nell'esempio di codice seguente:

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

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creazione del renderer personalizzato in ogni piattaforma

Il processo di creazione della classe di renderer personalizzato è il seguente:

1. Creare una sottoclasse della classe `ViewRenderer<T1,T2>` che esegue il rendering del controllo personalizzato. Il primo argomento tipo deve essere il controllo personalizzato per cui si usa il renderer, in questo caso `CameraPreview`. Il secondo argomento tipo deve essere il controllo nativo che implementerà il controllo personalizzato.
1. Eseguire l'override del metodo `OnElementChanged` che esegue il rendering del controllo personalizzato e scrivere la logica per personalizzarlo. Questo metodo viene chiamato quando Xamarin.Forms viene creato il controllo corrispondente.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzata per specificare che verrà usato per eseguire il rendering del Xamarin.Forms controllo personalizzato. Questo attributo viene usato per registrare il renderer personalizzato con Xamarin.Forms .

> [!NOTE]
> Per la maggior parte Xamarin.Forms degli elementi, è facoltativo fornire un renderer personalizzato in ogni progetto di piattaforma. Se un renderer personalizzato non è registrato, verrà usato il renderer predefinito per la classe di base del controllo. I renderer personalizzati sono tuttavia necessari in ogni progetto della piattaforma quando si esegue il rendering di un elemento [View](xref:Xamarin.Forms.View).

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazioni tra di essi:

![](view-images/solution-structure.png "CameraPreview Custom Renderer Project Responsibilities")

Il rendering del controllo personalizzato `CameraPreview` viene eseguito dalle classi di renderer specifiche della piattaforma che derivano tutte dalla classe `ViewRenderer` per ogni piattaforma. Di conseguenza il rendering di ogni controllo `CameraPreview` viene eseguito con controlli specifici della piattaforma, come illustrato negli screenshot seguenti:

![](view-images/screenshots.png "CameraPreview on each Platform")

La `ViewRenderer` classe espone il `OnElementChanged` metodo, che viene chiamato quando Xamarin.Forms viene creato il controllo personalizzato per eseguire il rendering del controllo nativo corrispondente. Questo metodo accetta un parametro `ElementChangedEventArgs` che contiene le proprietà `OldElement` e `NewElement`. Queste proprietà rappresentano l' Xamarin.Forms elemento a cui è *stato* collegato il renderer e l' Xamarin.Forms elemento a cui *è* collegato rispettivamente il renderer. Nell'applicazione di esempio la proprietà `OldElement` sarà `null` e la proprietà `NewElement` conterrà un riferimento all'istanza di `CameraPreview`.

La creazione dell'istanza e la personalizzazione del controllo nativo devono essere eseguite in una versione sostituita del metodo `OnElementChanged` in ogni classe di renderer specifica della piattaforma. Il metodo `SetNativeControl` deve essere usato per creare un'istanza del controllo nativo e assegna anche il riferimento al controllo alla proprietà `Control`. Inoltre, è possibile ottenere un riferimento al Xamarin.Forms controllo di cui è in corso il rendering tramite la `Element` Proprietà.

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

Per un nuovo controllo nativo l'istanza deve essere creata solo una volta, quando la proprietà `Control` è `null`. Inoltre, il controllo deve essere creato, configurato e i gestori di eventi hanno sottoscritto quando il renderer personalizzato è associato a un nuovo Xamarin.Forms elemento. Analogamente, per i gestori degli eventi sottoscritti l'iscrizione deve essere annullata solo in caso di modifica dell'elemento a cui è allegato il renderer. L'adozione di questo approccio consente di creare un renderer personalizzato con prestazioni elevate che non subisce perdite di memoria.

> [!IMPORTANT]
> Il metodo `SetNativeControl` deve essere chiamato solo se `e.NewElement` è diverso da `null`.

Ogni classe renderer personalizzata è decorata con un `ExportRenderer` attributo che registra il renderer con Xamarin.Forms . L'attributo accetta due parametri: il nome del tipo del Xamarin.Forms controllo personalizzato di cui viene eseguito il rendering e il nome del tipo del renderer personalizzato. Il prefisso `assembly` dell'attributo specifica che l'attributo viene applicato all'intero assembly.

Le sezioni seguenti illustrano l'implementazione della classe di renderer personalizzato specifica di ogni piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creazione del renderer personalizzato in iOS

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma iOS:

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

Se la proprietà `Control` è `null`, viene chiamato il metodo `SetNativeControl` per creare un'istanza di un nuovo controllo `UICameraPreview` e assegnare un riferimento al controllo alla proprietà `Control`. Il controllo `UICameraPreview` è un controllo personalizzato specifico della piattaforma che usa le API `AVCapture` per definire il flusso di anteprima della fotocamera. Espone un evento `Tapped` gestito dal metodo `OnCameraPreviewTapped` per arrestare e avviare l'anteprima video quando viene toccata. L' `Tapped` evento viene sottoscritto quando il renderer personalizzato è associato a un nuovo elemento ed è stata annullata la Xamarin.Forms sottoscrizione solo quando l'elemento a cui è associato il renderer viene modificato.

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

Se la proprietà `Control` è `null`, viene chiamato il metodo `SetNativeControl` per creare un'istanza di un nuovo controllo `CameraPreview` e assegnare un riferimento al controllo alla proprietà `Control`. Il controllo `CameraPreview` è un controllo personalizzato specifico della piattaforma che usa l'API `Camera` per definire il flusso di anteprima della fotocamera. Il `CameraPreview` controllo viene quindi configurato, purché il renderer personalizzato sia collegato a un nuovo Xamarin.Forms elemento. Questa configurazione prevede la creazione di un nuovo oggetto `Camera` nativo per accedere a una determinata fotocamera hardware e la registrazione di un gestore eventi per elaborare l'evento `Click`. A sua volta il gestore avvia e arresta l'anteprima video quando viene toccata. Viene annullata la `Click` sottoscrizione dell'evento se l' Xamarin.Forms elemento a cui è associato il renderer viene modificato.

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

A condizione che la proprietà `Control` sia `null`, viene creata un'istanza di un nuovo `CaptureElement` e viene chiamato il metodo `SetupCamera`, che usa l'API `MediaCapture` per specificare il flusso di anteprima della fotocamera. Viene quindi chiamato il metodo `SetNativeControl` per assegnare un riferimento all'istanza di `CaptureElement` alla proprietà `Control`. Il controllo `CaptureElement` espone un evento `Tapped` gestito dal metodo `OnCameraPreviewTapped` per arrestare e avviare l'anteprima video quando viene toccata. L' `Tapped` evento viene sottoscritto quando il renderer personalizzato è associato a un nuovo elemento ed è stata annullata la Xamarin.Forms sottoscrizione solo quando l'elemento a cui è associato il renderer viene modificato.

> [!NOTE]
> In un'applicazione UWP è importante arrestare ed eliminare gli oggetti che consentono l'accesso alla fotocamera. Questi possono infatti interferire con altre applicazioni che tentano di accedere alla fotocamera del dispositivo. Per altre informazioni, vedere [Display the camera preview](/windows/uwp/audio-video-camera/simple-camera-preview-access/) (Visualizzare l'anteprima della fotocamera).

## <a name="summary"></a>Summary

Questo articolo ha illustrato come creare un renderer personalizzato per un Xamarin.Forms controllo personalizzato usato per visualizzare un flusso video di anteprima dalla fotocamera del dispositivo. Xamarin.Formsi controlli dell'interfaccia utente personalizzati devono derivare dalla [`View`](xref:Xamarin.Forms.View) classe, utilizzata per posizionare i layout e i controlli sullo schermo.

## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-view)
