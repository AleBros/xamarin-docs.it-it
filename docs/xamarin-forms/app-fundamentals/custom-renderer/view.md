---
title: Implementazione di una vista
description: Xamarin. Forms controlli dell'interfaccia utente personalizzata devono derivare dalla classe di visualizzazione, viene utilizzata per posizionare i controlli nella schermata e layout. In questo articolo viene illustrato come creare un renderer personalizzato per un controllo personalizzato di xamarin. Forms che consente di visualizzare un flusso video di anteprima da fotocamera del dispositivo.
ms.topic: article
ms.prod: xamarin
ms.assetid: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 30ee40272b5f7a6f5863dccf4dcae7431f6f536f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="implementing-a-view"></a>Implementazione di una vista

_Xamarin. Forms controlli dell'interfaccia utente personalizzata devono derivare dalla classe di visualizzazione, viene utilizzata per posizionare i controlli nella schermata e layout. In questo articolo viene illustrato come creare un renderer personalizzato per un controllo personalizzato di xamarin. Forms che consente di visualizzare un flusso video di anteprima da fotocamera del dispositivo._

Tutte le viste di xamarin. Forms sono un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) viene eseguito il rendering da un'applicazione di xamarin. Forms in iOS, il `ViewRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza nativa `UIView` controllo. Nella piattaforma Android, il `ViewRenderer` un'istanza nativa `View` controllo. In Windows Phone e di Windows della piattaforma UWP (Universal), il `ViewRenderer` un'istanza nativa `FrameworkElement` controllo. Per ulteriori informazioni sulle classi di controllo nativo che eseguono il mapping per i controlli di xamarin. Forms e renderer, vedere [Renderer classi di Base e i controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra il [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) e i corrispondenti controlli nativi che l'implementano:

![](view-images/view-classes.png "Relazione tra la classe di visualizzazione e le relative classi Native di implementazione")

Il processo di rendering può essere utilizzato per implementare le personalizzazioni specifiche della piattaforma tramite la creazione di un renderer personalizzato per un [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) in ciascuna piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Custom_Control) un controllo personalizzato di xamarin. Forms.
1. [Utilizzare](#Consuming_the_Custom_Control) il controllo personalizzato da xamarin. Forms.
1. [Creare](#Creating_the_Custom_Renderer_on_each_Platform) il renderer personalizzato per il controllo in ogni piattaforma.

Ogni elemento verrà ora essere descritti in dettaglio, per implementare un `CameraPreview` renderer che consente di visualizzare un flusso video di anteprima da fotocamera del dispositivo. Toccando il flusso video verrà arrestarlo e avviarlo.

<a name="Creating_the_Custom_Control" />

## <a name="creating-the-custom-control"></a>Creazione del controllo personalizzato

È possibile creare un controllo personalizzato creando sottoclassi di [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) classe, come illustrato nell'esempio di codice seguente:

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

Il `CameraPreview` controllo personalizzato viene creato nel progetto di libreria (PCL) di classi portabile e definisce l'API per il controllo. Il controllo personalizzato espone un `Camera` proprietà utilizzata per controllare se il flusso video deve essere visualizzato dalla parte anteriore o posteriore della fotocamera del dispositivo. Se non è specificato un valore per il `Camera` proprietà quando il controllo viene creato, per impostazione predefinita che specifica la fotocamera posteriore.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Utilizzo del controllo personalizzato

Il `CameraPreview` controllo personalizzato può fare riferimento in XAML nel progetto libreria di classi Portabile dichiarare uno spazio dei nomi per la posizione e utilizzando il prefisso dello spazio dei nomi dell'elemento di controllo personalizzato. Nell'esempio di codice riportato di seguito viene illustrato come la `CameraPreview` controllo personalizzato può essere utilizzato da una pagina XAML:

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

Il `local` prefisso dello spazio dei nomi denominato nulla. Tuttavia, il `clr-namespace` e `assembly` i valori devono corrispondere i dettagli del controllo personalizzato. Dopo aver dichiarato lo spazio dei nomi, il prefisso viene utilizzato per fare riferimento al controllo personalizzato.

Nell'esempio di codice riportato di seguito viene illustrato come la `CameraPreview` controllo personalizzato può essere utilizzato da una pagina in c#:

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

Un'istanza di `CameraPreview` controllo personalizzato da utilizzare per visualizzare il flusso video di anteprima da fotocamera del dispositivo. A parte, facoltativamente, specificando un valore per il `Camera` proprietà, la personalizzazione del controllo verrà effettuata nel renderer personalizzato.

Un renderer personalizzato può ora essere aggiunto a ogni progetto di applicazione per creare i controlli di anteprima della fotocamera specifico della piattaforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creazione di Renderer personalizzato a seconda della piattaforma

Il processo di creazione della classe renderer personalizzato è come segue:

1. Creare una sottoclasse di `ViewRenderer<T1,T2>` classe che esegue il rendering del controllo personalizzato. Il primo argomento di tipo deve essere il controllo personalizzato, il renderer è, in questo caso `CameraPreview`. Il secondo argomento di tipo deve essere il controllo nativo che consente di implementare il controllo personalizzato.
1. Eseguire l'override di `OnElementChanged` metodo che esegue il rendering di controllo e scrivere la logica personalizzata per personalizzarlo. Questo metodo viene chiamato quando viene creato il controllo di xamarin. Forms corrispondente.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzato per specificare che verrà usata per il rendering del controllo personalizzato di xamarin. Forms. Questo attributo viene utilizzato per registrare il renderer personalizzato con xamarin. Forms.

> [!NOTE]
> Per la maggior parte degli elementi di xamarin. Forms, è facoltativo per fornire un renderer personalizzato in ogni progetto della piattaforma. Se non è registrato un renderer personalizzato, verrà utilizzato il renderer predefinito per la classe di base del controllo. Un renderer personalizzato è tuttavia necessarie in ogni progetto della piattaforma per il rendering di un [vista](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) elemento.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con le relazioni tra di essi:

![](view-images/solution-structure.png "Responsabilità di progetto personalizzato CameraPreview Renderer")

Il `CameraPreview` viene eseguito il rendering di controlli personalizzati dalle classi renderer specifico della piattaforma, che derivano dalla `ViewRenderer` classe per ogni piattaforma. Di conseguenza, ogni `CameraPreview` controllo personalizzato viene eseguito il rendering con controlli specifici della piattaforma, come illustrato nelle schermate seguenti:

![](view-images/screenshots.png "CameraPreview in ciascuna piattaforma")

Il `ViewRenderer` classe espone il `OnElementChanged` metodo, che viene chiamato quando viene creato il controllo personalizzato di xamarin. Forms per il rendering del controllo native corrispondente. Questo metodo accetta un `ElementChangedEventArgs` parametro contenente `OldElement` e `NewElement` proprietà. Queste proprietà rappresentano l'elemento di xamarin. Forms che il renderer *è stata* collegata, l'elemento di xamarin. Forms e che il renderer *è* associata rispettivamente. Nell'applicazione di esempio, il `OldElement` proprietà sarà `null` e `NewElement` proprietà contiene un riferimento al `CameraPreview` istanza.

Una versione sottoposta a override del `OnElementChanged` (metodo), in ogni classe renderer specifico della piattaforma, è possibile eseguire la creazione di un'istanza nativa controllo e personalizzazione. Il `SetNativeControl` metodo deve essere utilizzato per creare un'istanza del controllo nativo, e questo metodo assegna anche il riferimento al controllo per il `Control` proprietà. Inoltre, un riferimento al controllo di xamarin. Forms che viene eseguito il rendering può essere ottenuto tramite il `Element` proprietà.

In alcuni casi, il `OnElementChanged` metodo può essere chiamato più volte. Pertanto, per evitare perdite di memoria, prestare attenzione quando si crea un nuovo controllo nativo. L'approccio da usare quando si crea un'istanza di un nuovo controllo nativo in un renderer personalizzato è illustrato nell'esempio di codice seguente:

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

Per un nuovo controllo nativo l'istanza deve essere creata solo una volta, quando la proprietà `Control` è `null`. Configurare il controllo e sottoscrivere i gestori degli eventi solo quando il renderer personalizzato è allegato a un nuovo elemento di Xamarin.Forms. Analogamente, gestori di eventi che sono stati sottoscritti solo devono essere annullati da quando l'elemento collegato per il renderer cambia. Questo approccio consente di creare un renderer personalizzato ad alte prestazioni che non subisca delle perdite di memoria.

Ogni classe renderer personalizzato è decorato con un `ExportRenderer` attributo che registra il renderer con xamarin. Forms. L'attributo accetta due parametri: il nome del tipo del controllo personalizzato xamarin. Forms viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il `assembly` prefisso per l'attributo specifica che l'attributo si applica all'intero assembly.

Le sezioni seguenti illustrano l'implementazione di ogni classe renderer personalizzato specifico della piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creazione di Renderer personalizzato in iOS

Esempio di codice seguente viene illustrato il renderer personalizzato per la piattaforma iOS:

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

Purché il `Control` proprietà è `null`, `SetNativeControl` metodo viene chiamato per creare un'istanza di un nuovo `UICameraPreview` controllo e di assegnare un riferimento a questo argomento per il `Control` proprietà. Il `UICameraPreview` controllo è un controllo personalizzato specifico della piattaforma che utilizza il `AVCapture` le API per fornire il flusso di anteprima tra la camera. Espone un `Tapped` evento che viene gestita dal `OnCameraPreviewTapped` metodo per interrompere e avviare l'anteprima video quando verrà scelti. Il `Tapped` evento sottoscritto quando renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms e ha annullato la sottoscrizione da solo quando l'elemento del renderer è associato alle modifiche.

### <a name="creating-the-custom-renderer-on-android"></a>Creazione di Renderer personalizzato in Android

Esempio di codice seguente viene illustrato il renderer personalizzato per la piattaforma Android:

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

Purché il `Control` proprietà è `null`, `SetNativeControl` metodo viene chiamato per creare un'istanza di un nuovo `CameraPreview` controllare e assegnare un riferimento a questo argomento per il `Control` proprietà. Il `CameraPreview` controllo è un controllo personalizzato specifico della piattaforma che utilizza il `Camera` API per fornire il flusso di anteprima tra la camera. Il `CameraPreview` controllo viene quindi configurato, condizione che il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms. Questa configurazione prevede la creazione di un nuovo nativo `Camera` dell'oggetto per accedere a una fotocamera hardware specifico e la registrazione di un gestore eventi per l'elaborazione di `Click` evento. A sua volta questo gestore verrà avviati e arrestati anteprima video quando verrà scelti. Il `Click` evento è annullato la sottoscrizione a, se l'elemento di xamarin. Forms, il renderer è associato alle modifiche.

### <a name="creating-the-custom-renderer-on-windows-phone-and-uwp"></a>Creazione di Renderer personalizzato in Windows Phone e UWP

Esempio di codice seguente viene illustrato il renderer personalizzato per Windows Phone e UWP:

```csharp
[assembly: ExportRenderer (typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.WinPhone81
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, Windows.UI.Xaml.Controls.CaptureElement>
    {
        MediaCapture mediaCapture;
        CaptureElement captureElement;
        CameraOptions cameraOptions;
        Application app;
        bool isPreviewing = false;

        protected override void OnElementChanged (ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged (e);

            if (Control == null) {
                ...
                captureElement = new CaptureElement ();
                captureElement.Stretch = Stretch.UniformToFill;

                InitializeAsync ();
                SetNativeControl (captureElement);
            }
            if (e.OldElement != null) {
                // Unsubscribe
                Tapped -= OnCameraPreviewTapped;
            }
            if (e.NewElement != null) {
                // Subscribe
                Tapped += OnCameraPreviewTapped;
            }
        }

        async void OnCameraPreviewTapped (object sender, TappedRoutedEventArgs e)
        {
            if (isPreviewing) {
                await StopPreviewAsync ();
            } else {
                await StartPreviewAsync ();
            }
        }
        ...
    }
}
```

Purché il `Control` proprietà è `null`, un nuovo `CaptureElement` viene creata un'istanza e `InitializeAsync` metodo viene chiamato, che utilizza il `MediaCapture` API per fornire il flusso di anteprima tra la camera. Il `SetNativeControl` viene quindi chiamato il metodo per assegnare un riferimento al `CaptureElement` istanza per il `Control` proprietà. Il `CaptureElement` controllo espone un `Tapped` evento che viene gestita dal `OnCameraPreviewTapped` metodo per interrompere e avviare l'anteprima video quando verrà scelti. Il `Tapped` evento sottoscritto quando renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms e ha annullato la sottoscrizione da solo quando l'elemento del renderer è associato alle modifiche.

> [!NOTE]
> È importante arrestare ed eliminare oggetti che forniscono l'accesso alla fotocamera in un Windows Phone o un'applicazione UWP. In caso contrario, può interferire con altre applicazioni che tentano di accedere la fotocamera del dispositivo. Per ulteriori informazioni, vedere e [Guida introduttiva: acquisizione video tramite l'API MediaCapture](https://msdn.microsoft.com/library/windows/apps/xaml/dn642092.aspx) per le applicazioni di Windows Runtime, e [visualizzare l'anteprima della fotocamera](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access) per le applicazioni UWP.

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come creare un renderer personalizzato per un controllo personalizzato di xamarin. Forms che consente di visualizzare un flusso video di anteprima da fotocamera del dispositivo. Xamarin. Forms controlli dell'interfaccia utente personalizzata devono derivare dal [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) (classe), che viene utilizzato per posizionare i controlli nella schermata e layout.


## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/view/)
