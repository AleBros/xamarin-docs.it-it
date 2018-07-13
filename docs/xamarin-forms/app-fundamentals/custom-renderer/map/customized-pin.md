---
title: Personalizzazione di un Pin di mappa
description: Questo articolo illustra come creare un renderer personalizzato per il controllo mappa, che consente di visualizzare una mappa con un pin personalizzato e una visualizzazione personalizzata dei dati pin nativa in ciascuna piattaforma.
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 4fee67f08e86c40709aa226c40c0f7721dc26800
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998315"
---
# <a name="customizing-a-map-pin"></a>Personalizzazione di un Pin di mappa

_Questo articolo illustra come creare un renderer personalizzato per il controllo mappa, che consente di visualizzare una mappa con un pin personalizzato e una visualizzazione personalizzata dei dati pin nativa in ciascuna piattaforma._

Tutte le visualizzazioni di xamarin. Forms sono un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un [ `Map` ](xref:Xamarin.Forms.Maps.Map) viene eseguito il rendering da un'applicazione xamarin. Forms in iOS, il `MapRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza di nativo `MKMapView` controllo. La piattaforma Android, il `MapRenderer` un'istanza nativa `MapView` controllo. In Universal Windows Platform (UWP), il `MapRenderer` un'istanza nativa `MapControl`. Per altre informazioni sulle classi di controllo nativo che eseguono il mapping per i controlli di xamarin. Forms e renderer, vedere [Renderer di classi di Base e controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra il [ `Map` ](xref:Xamarin.Forms.Maps.Map) e i corrispondenti controlli nativi che l'implementano:

![](customized-pin-images/map-classes.png "Relazione tra il controllo mappa e i controlli nativi di implementazione")

Il processo di rendering può essere usato per implementare le personalizzazioni specifiche della piattaforma tramite la creazione di un renderer personalizzato per un [ `Map` ](xref:Xamarin.Forms.Maps.Map) in ogni piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Custom_Map) una mappa personalizzata di xamarin. Forms.
1. [Utilizzare](#Consuming_the_Custom_Map) la mappa personalizzata da xamarin. Forms.
1. [Creare](#Creating_the_Custom_Renderer_on_each_Platform) il renderer personalizzato per la mappa su ogni piattaforma.

Ogni elemento a questo punto discuteremo a sua volta, per implementare un `CustomMap` renderer che consente di visualizzare una mappa con un pin personalizzato e una visualizzazione personalizzata dei dati pin nativa in ogni piattaforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) deve essere inizializzato e configurato prima dell'uso. Per altre informazioni, vedere [`Maps Control`](~/xamarin-forms/user-interface/map.md).

<a name="Creating_the_Custom_Map" />

## <a name="creating-the-custom-map"></a>Creazione della mappa personalizzata

È possibile creare un controllo personalizzato di mappa creando sottoclassi il [ `Map` ](xref:Xamarin.Forms.Maps.Map) classe, come illustrato nell'esempio di codice seguente:

```csharp
public class CustomMap : Map
{
  public List<CustomPin> CustomPins { get; set; }
}
```

Il `CustomMap` controllo viene creato nel progetto della libreria .NET Standard e definisce l'API per la mappa personalizzata. La mappa personalizzata espone il `CustomPins` che rappresenta la raccolta di proprietà `CustomPin` gli oggetti che verranno eseguito il rendering per il controllo mappa nativi in ciascuna piattaforma. Il `CustomPin` classe è illustrata nell'esempio di codice seguente:

```csharp
public class CustomPin : Pin
{
  public string Url { get; set; }
}
```

Questa classe definisce un `CustomPin` come classe che eredita le proprietà del [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) classe e l'aggiunta di un `Url` proprietà.

<a name="Consuming_the_Custom_Map" />

## <a name="consuming-the-custom-map"></a>Utilizza la mappa personalizzata

Il `CustomMap` controllo è reperibile in XAML nel progetto della libreria .NET Standard dichiara uno spazio dei nomi per la relativa posizione e usando il prefisso dello spazio dei nomi del controllo mappa personalizzata. Nell'esempio di codice riportato di seguito viene illustrato come il `CustomMap` controllo può essere usato da una pagina XAML:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer">
    <ContentPage.Content>
        <local:CustomMap x:Name="myMap" MapType="Street"
          WidthRequest="{x:Static local:App.ScreenWidth}"
          HeightRequest="{x:Static local:App.ScreenHeight}" />
    </ContentPage.Content>
</ContentPage>
```

Il `local` prefisso dello spazio dei nomi può essere denominata alcuna operazione. Tuttavia, il `clr-namespace` e `assembly` valori devono corrispondere i dettagli della mappa personalizzata. Una volta lo spazio dei nomi è dichiarato, il prefisso viene utilizzato per fare riferimento la mappa personalizzata.

Nell'esempio di codice riportato di seguito viene illustrato come il `CustomMap` controllo può essere usato da una pagina di c#:

```csharp
public class MapPageCS : ContentPage
{
  public MapPageCS ()
  {
    var customMap = new CustomMap {
      MapType = MapType.Street,
      WidthRequest = App.ScreenWidth,
      HeightRequest = App.ScreenHeight
    };
    ...

    Content = customMap;
  }
}
```

Il `CustomMap` istanza verrà usata per visualizzare la mappa nativa in ciascuna piattaforma. Dispone [ `MapType` ](xref:Xamarin.Forms.Maps.Map.MapType) proprietà imposta lo stile di visualizzazione del [ `Map` ](xref:Xamarin.Forms.Maps.Map), con i valori possibili viene definiti nel [ `MapType` ](xref:Xamarin.Forms.Maps.MapType) enumerazione. Per iOS e Android, la larghezza e altezza della mappa viene impostata tramite le proprietà del `App` classe inizializzate nei progetti specifici della piattaforma.

La posizione della mappa e il PIN contiene, vengono inizializzate come illustrato nell'esempio di codice seguente:

```csharp
public MapPage ()
{
  ...
  var pin = new CustomPin {
    Type = PinType.Place,
    Position = new Position (37.79752, -122.40183),
    Label = "Xamarin San Francisco Office",
    Address = "394 Pacific Ave, San Francisco CA",
    Id = "Xamarin",
    Url = "http://xamarin.com/about/"
  };

  customMap.CustomPins = new List<CustomPin> { pin };
  customMap.Pins.Add (pin);
  customMap.MoveToRegion (MapSpan.FromCenterAndRadius (
    new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
}
```

Questa inizializzazione viene aggiunta una puntina personalizzata e posiziona la visualizzazione della mappa con il [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) metodo, che modifica la posizione e livello di zoom della mappa mediante la creazione di un [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) da una [ `Position` ](xref:Xamarin.Forms.Maps.Position) e una [ `Distance` ](xref:Xamarin.Forms.Maps.Distance).

Un renderer personalizzato può ora essere aggiunti a ogni progetto di applicazione per personalizzare i controlli nativi della mappa.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creare il Renderer personalizzato a seconda della piattaforma

Il processo per la creazione della classe renderer personalizzati è come segue:

1. Creare una sottoclasse del `MapRenderer` classe che esegue il rendering della mappa personalizzata.
1. Eseguire l'override di `OnElementChanged` metodo che esegue il rendering di mappa personalizzata e scrivere la logica per personalizzarlo. Questo metodo viene chiamato quando viene creata la mappa personalizzata a xamarin. Forms corrispondente.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzato per specificare che verrà usata per eseguire il rendering della mappa personalizzata di xamarin. Forms. Questo attributo viene utilizzato per registrare il renderer personalizzato con xamarin. Forms.

> [!NOTE]
> È facoltativo per fornire un renderer personalizzato in ogni progetto della piattaforma. Se non è registrato un renderer personalizzato, quindi verrà utilizzato il renderer predefinito per la classe di base del controllo.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con le relazioni tra di essi:

![](customized-pin-images/solution-structure.png "Responsabilità di progetto Renderer personalizzati CustomMap")

Il `CustomMap` rendering del controllo dalle classi renderer specifica della piattaforma, che ne derivano il `MapRenderer` classe per ogni piattaforma. Di conseguenza ogni `CustomMap` controllo viene eseguito il rendering con controlli specifici della piattaforma, come illustrato negli screenshot seguenti:

![](customized-pin-images/screenshots.png "CustomMap in ogni piattaforma")

Il `MapRenderer` classe espone il `OnElementChanged` metodo, che viene chiamato quando viene creata la mappa personalizzata di xamarin. Forms per eseguire il rendering del controllo nativo corrispondente. Questo metodo accetta un `ElementChangedEventArgs` parametro che contiene `OldElement` e `NewElement` proprietà. Queste proprietà rappresentano l'elemento di xamarin. Forms che il renderer *è stata* collegata, l'elemento di xamarin. Forms e che il renderer *è* collegati rispettivamente. Nell'applicazione di esempio il `OldElement` proprietà sarà `null` e il `NewElement` proprietà conterrà un riferimento al `CustomMap` istanza.

Una versione override del `OnElementChanged` metodo, in ogni classe renderer specifica della piattaforma, è possibile eseguire la personalizzazione del controllo nativo. Un riferimento tipizzato al controllo nativo utilizzato nella piattaforma sono accessibili tramite il `Control` proprietà. Inoltre, un riferimento al controllo di xamarin. Forms che viene eseguito il rendering può essere ottenuto tramite il `Element` proprietà.

Prestare attenzione quando si sottoscrive i gestori di eventi nel `OnElementChanged` metodo, come illustrato nell'esempio di codice seguente:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers
  }

  if (e.NewElement != null) {
    // Configure the native control and subscribe to event handlers
  }
}
```

Deve essere configurato il controllo nativo e sottoscrivere i gestori degli eventi solo quando il renderer personalizzato è associato a un nuovo elemento di xamarin. Forms. Analogamente, i gestori degli eventi sottoscritti deve essere annullate solo quando viene modificato l'elemento a cui è associato il renderer. Adottando questo approccio consente di creare un renderer personalizzato che non subisce perdite di memoria.

Ogni classe renderer personalizzato è decorata con un `ExportRenderer` attributo che registra il renderer con xamarin. Forms. L'attributo accetta due parametri: il nome del tipo del controllo personalizzato xamarin. Forms viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il `assembly` prefisso per l'attributo specifica che l'attributo viene applicato all'intero assembly.

Le sezioni seguenti illustrano l'implementazione di ogni classe renderer personalizzato specifico della piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creare il Renderer personalizzati in iOS

Le schermate seguenti illustrano la mappa, prima e dopo la personalizzazione:

![](customized-pin-images/map-layout-ios.png "Controllo mappa di prima e dopo la personalizzazione")

In iOS il pin viene chiamato un *annotazione*, e può essere un'immagine personalizzata o un pin di vari colori definiti dal sistema. Le annotazioni possono essere visualizzato facoltativamente un *callout*, che viene visualizzata in risposta all'utente la selezione dell'annotazione. Consente di visualizzare nel callout il `Label` e `Address` delle proprietà del `Pin` istanza, facoltativo a sinistra e a destra degli accessori visualizzazioni. Nella schermata precedente, la visualizzazione degli accessori a sinistra è l'immagine di un meccanico, dove la vista a destra degli accessori è il *informazioni* pulsante.

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma iOS:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        UIView customPinView;
        List<CustomPin> customPins;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveAnnotations(nativeMap.Annotations);
                    nativeMap.GetViewForAnnotation = null;
                    nativeMap.CalloutAccessoryControlTapped -= OnCalloutAccessoryControlTapped;
                    nativeMap.DidSelectAnnotationView -= OnDidSelectAnnotationView;
                    nativeMap.DidDeselectAnnotationView -= OnDidDeselectAnnotationView;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                customPins = formsMap.CustomPins;

                nativeMap.GetViewForAnnotation = GetViewForAnnotation;
                nativeMap.CalloutAccessoryControlTapped += OnCalloutAccessoryControlTapped;
                nativeMap.DidSelectAnnotationView += OnDidSelectAnnotationView;
                nativeMap.DidDeselectAnnotationView += OnDidDeselectAnnotationView;
            }
        }
        ...
    }
}
```

Il `OnElementChanged` metodo esegue la configurazione seguente del [ `MKMapView` ](https://developer.xamarin.com/api/type/MapKit.MKMapView/) istanza, a condizione che il renderer personalizzato è allegato a un nuovo elemento di xamarin. Forms:

- Il [ `GetViewForAnnotation` ](https://developer.xamarin.com/api/property/MapKit.MKMapView.GetViewForAnnotation/) è impostata sul `GetViewForAnnotation` (metodo). Questo metodo viene chiamato quando la [diventa visibile nella mappa del percorso dell'annotazione](#Displaying_the_Annotation)e viene usato per personalizzare il prima dell'annotazione da visualizzare.
- I gestori eventi per il `CalloutAccessoryControlTapped`, `DidSelectAnnotationView`, e `DidDeselectAnnotationView` gli eventi vengono registrati. Questi eventi vengono attivati quando l'utente [tocca l'accessorio a destra del callout](#Tapping_on_the_Right_Callout_Accessory_View)e quando l'utente [seleziona](#Selecting_the_Annotation) e [deseleziona](#Deselecting_the_Annotation) l'annotazione, rispettivamente. Gli eventi sono annullati solo quando l'elemento del renderer è associato alle modifiche.

<a name="Displaying_the_Annotation" />

#### <a name="displaying-the-annotation"></a>Con l'annotazione

Il `GetViewForAnnotation` metodo viene chiamato quando la posizione dell'annotazione diventa visibile sulla mappa e viene usata per personalizzare il prima dell'annotazione da visualizzare. Un'annotazione è costituito da due parti:

- `MkAnnotation` : include il titolo, sottotitolo e posizione dell'annotazione.
- `MkAnnotationView` : contiene l'immagine per rappresentare l'annotazione e, facoltativamente, un callout in cui viene visualizzato quando l'utente tocca l'annotazione.

Il `GetViewForAnnotation` metodo accetta un `IMKAnnotation` che contiene i dati dell'annotazione e restituisce un `MKAnnotationView` da visualizzare sulla mappa e come illustrato nell'esempio di codice seguente:

```csharp
MKAnnotationView GetViewForAnnotation(MKMapView mapView, IMKAnnotation annotation)
{
    MKAnnotationView annotationView = null;

    if (annotation is MKUserLocation)
        return null;

    var customPin = GetCustomPin(annotation as MKPointAnnotation);
    if (customPin == null) {
        throw new Exception("Custom pin not found");
    }

    annotationView = mapView.DequeueReusableAnnotation(customPin.Id.ToString());
    if (annotationView == null) {
        annotationView = new CustomMKAnnotationView(annotation, customPin.Id.ToString());
        annotationView.Image = UIImage.FromFile("pin.png");
        annotationView.CalloutOffset = new CGPoint(0, 0);
        annotationView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile("monkey.png"));
        annotationView.RightCalloutAccessoryView = UIButton.FromType(UIButtonType.DetailDisclosure);
        ((CustomMKAnnotationView)annotationView).Id = customPin.Id.ToString();
        ((CustomMKAnnotationView)annotationView).Url = customPin.Url;
    }
    annotationView.CanShowCallout = true;

    return annotationView;
}
```

Questo metodo assicura che l'annotazione verrà visualizzata come un'immagine personalizzata, anziché come definito dal sistema con pin e che quando si tocca l'annotazione callout verrà visualizzato che include contenuto aggiuntivo a sinistra e a destra del titolo di annotazione e dell'indirizzo . Questa operazione viene eseguita come indicato di seguito:

1. Il `GetCustomPin` viene chiamato per restituire i dati personalizzati pin per l'annotazione.
1. Per risparmiare memoria, la visualizzazione dell'annotazione è in pool per il riutilizzo fra la chiamata a [ `DequeueReusableAnnotation` ](https://developer.xamarin.com/api/member/MapKit.MKMapView.DequeueReusableAnnotation/(System.String)/).
1. Il `CustomMKAnnotationView` classe estende la `MKAnnotationView` classe con `Id` e `Url` le proprietà che corrispondono alle proprietà identiche nel `CustomPin` istanza. Una nuova istanza di `CustomMKAnnotationView` è stato creato, purché l'annotazione è `null`:
  - Il `CustomMKAnnotationView.Image` viene impostata per l'immagine che rappresenta l'annotazione sulla mappa.
  - Il `CustomMKAnnotationView.CalloutOffset` è impostata su un `CGPoint` che specifica che il callout verrà centrato sopra l'annotazione.
  - Il `CustomMKAnnotationView.LeftCalloutAccessoryView` è impostata su un'immagine di un meccanico che verrà visualizzato a sinistra del titolo di annotazione e dell'indirizzo.
  - Il `CustomMKAnnotationView.RightCalloutAccessoryView` è impostata su un *informazioni* pulsante che verrà visualizzato a destra del titolo di annotazione e dell'indirizzo.
  - Il `CustomMKAnnotationView.Id` è impostata sul `CustomPin.Id` restituito dalla proprietà di `GetCustomPin` (metodo). In questo modo l'annotazione che deve essere identificato in modo che includa [callout è possibile personalizzare ulteriormente](#Selecting_the_Annotation), se necessario.
  - Il `CustomMKAnnotationView.Url` è impostata sul `CustomPin.Url` restituito dalla proprietà di `GetCustomPin` (metodo). L'URL verrà aperto quando l'utente [tocca il pulsante visualizzato nella visualizzazione degli accessori callout destro](#Tapping_on_the_Right_Callout_Accessory_View).
1. Il [ `MKAnnotationView.CanShowCallout` ](https://developer.xamarin.com/api/property/MapKit.MKAnnotationView.CanShowCallout/) è impostata su `true` in modo che i callout viene visualizzato quando si tocca l'annotazione.
1. L'annotazione viene restituita da visualizzare sulla mappa.

<a name="Selecting_the_Annotation" />

#### <a name="selecting-the-annotation"></a>Selezionando l'annotazione

Quando l'utente tocca sull'annotazione, la `DidSelectAnnotationView` viene generato l'evento, che a sua volta esegue il `OnDidSelectAnnotationView` metodo:

```csharp
void OnDidSelectAnnotationView (object sender, MKAnnotationViewEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  customPinView = new UIView ();

  if (customView.Id == "Xamarin") {
    customPinView.Frame = new CGRect (0, 0, 200, 84);
    var image = new UIImageView (new CGRect (0, 0, 200, 84));
    image.Image = UIImage.FromFile ("xamarin.png");
    customPinView.AddSubview (image);
    customPinView.Center = new CGPoint (0, -(e.View.Frame.Height + 75));
    e.View.AddSubview (customPinView);
  }
}
```

Questo metodo estende il callout esistente (che contiene le viste a sinistra e a destra degli accessori) mediante l'aggiunta di un `UIView` istanza che contiene un'immagine del logo Xamarin, a condizione che l'annotazione selezionata a esso relativi `Id` proprietà è impostata su `Xamarin`. In questo modo per gli scenari in cui possono essere visualizzati i callout diversi per diverse annotazioni. Il `UIView` istanza verrà visualizzata centrato sopra il callout esistente.

<a name="Tapping_on_the_Right_Callout_Accessory_View" />

#### <a name="tapping-on-the-right-callout-accessory-view"></a>Se si tocca la visualizzazione corretta accessorio Callout

Quando l'utente tocca il *informazioni* pulsante nella visualizzazione degli accessori callout a destra, il `CalloutAccessoryControlTapped` viene generato l'evento, che a sua volta esegue il `OnCalloutAccessoryControlTapped` metodo:

```csharp
void OnCalloutAccessoryControlTapped (object sender, MKMapViewAccessoryTappedEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  if (!string.IsNullOrWhiteSpace (customView.Url)) {
    UIApplication.SharedApplication.OpenUrl (new Foundation.NSUrl (customView.Url));
  }
}
```

Questo metodo consente di aprire un web browser e Naviga all'indirizzo archiviato in indirizzo il `CustomMKAnnotationView.Url` proprietà. Si noti che l'indirizzo è stato definito quando si crea il `CustomPin` insieme nel progetto della libreria .NET Standard.

<a name="Deselecting_the_Annotation" />

#### <a name="deselecting-the-annotation"></a>Se si deseleziona l'annotazione

Quando viene visualizzata l'annotazione e l'utente tocca sulla mappa, il `DidDeselectAnnotationView` viene generato l'evento, che a sua volta esegue il `OnDidDeselectAnnotationView` metodo:

```csharp
void OnDidDeselectAnnotationView (object sender, MKAnnotationViewEventArgs e)
{
  if (!e.View.Selected) {
    customPinView.RemoveFromSuperview ();
    customPinView.Dispose ();
    customPinView = null;
  }
}
```

Questo metodo assicura che quando il callout esistente non è selezionato, la parte estesa della linea di callout (l'immagine del logo Xamarin) verrà interrotto anche visualizzati e le relative risorse verranno rilasciate.

Per altre informazioni sulla personalizzazione di un `MKMapView` dell'istanza, vedere [mappe di iOS](~/ios/user-interface/controls/ios-maps/index.md).

### <a name="creating-the-custom-renderer-on-android"></a>Creare il Renderer personalizzati in Android

Le schermate seguenti illustrano la mappa, prima e dopo la personalizzazione:

![](customized-pin-images/map-layout-android.png "Controllo mappa di prima e dopo la personalizzazione")

In Android il pin viene chiamato un *marcatore*, e può essere un'immagine personalizzata o un marcatore di vari colori definiti dal sistema. Marcatori possono visualizzare un' *finestra informazioni*, che viene visualizzata in risposta all'utente toccando sul marcatore. Consente di visualizzare la finestra delle informazioni il `Label` e `Address` delle proprietà del `Pin` istanza e possono essere personalizzati per includere altri contenuti. Tuttavia, solo una finestra di informazioni può essere visualizzata in una sola volta.

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma Android:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.Droid
{
    public class CustomMapRenderer : MapRenderer, GoogleMap.IInfoWindowAdapter
    {
        List<CustomPin> customPins;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                NativeMap.InfoWindowClick -= OnInfoWindowClick;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                customPins = formsMap.CustomPins;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(GoogleMap map)
        {
            base.OnMapReady(map);

            NativeMap.InfoWindowClick += OnInfoWindowClick;
            NativeMap.SetInfoWindowAdapter(this);
        }
        ...
    }
}
```

Condizione che il renderer personalizzato è allegato a un nuovo elemento di xamarin. Forms, il `OnElementChanged` chiamate al metodo il `MapView.GetMapAsync` metodo, che ottiene l'oggetto sottostante `GoogleMap` che è associato alla visualizzazione. Una volta il `GoogleMap` istanza è disponibile, il `OnMapReady` sostituzione verrà richiamata. Questo metodo registra un gestore eventi per il `InfoWindowClick` evento, che viene attivato quando il [finestra info fa](#Clicking_on_the_Info_Window)e viene annullato la sottoscrizione da solo quando l'elemento del renderer è associato alle modifiche. Il `OnMapReady` eseguire l'override anche le chiamate di `SetInfoWindowAdapter` metodo per specificare che il `CustomMapRenderer` istanza della classe fornirà i metodi per personalizzare la finestra delle informazioni.

Il `CustomMapRenderer` classe implementa le `GoogleMap.IInfoWindowAdapter` interfaccia [personalizzazione della finestra informazioni](#Customizing_the_Info_Window). Questa interfaccia consente di specificare che devono essere implementati i metodi seguenti:

- `public Android.Views.View GetInfoWindow(Marker marker)` : Questo metodo viene chiamato per restituire una finestra di informazioni personalizzate per un marcatore. Se il valore restituito `null`, verrà usato il rendering di finestra predefinito. Se viene restituito un `View`, che quindi `View` devono essere posizionati all'interno della cornice della finestra informazioni.
- `public Android.Views.View GetInfoContents(Marker marker)` -Questo metodo viene chiamato per restituire un `View` che contiene il contenuto della finestra di informazioni e verrà chiamato solo se il `GetInfoWindow` restituzione del metodo `null`. Se il valore restituito `null`, verrà usato il rendering predefinito del contenuto della finestra di informazioni.

Nell'applicazione di esempio, solo il contenuto della finestra informazioni personalizzato, quindi la `GetInfoWindow` restituzione del metodo `null` per abilitare questa opzione.

#### <a name="customizing-the-marker"></a>Personalizzazione di marcatore

L'icona utilizzata per rappresentare un marcatore può essere personalizzato tramite la chiamata di `MarkerOptions.SetIcon` (metodo). Ciò può essere effettuata eseguendo l'override di `CreateMarker` metodo, che viene richiamato per ogni `Pin` che viene aggiunto alla mappa:

```csharp
protected override MarkerOptions CreateMarker(Pin pin)
{
    var marker = new MarkerOptions();
    marker.SetPosition(new LatLng(pin.Position.Latitude, pin.Position.Longitude));
    marker.SetTitle(pin.Label);
    marker.SetSnippet(pin.Address);
    marker.SetIcon(BitmapDescriptorFactory.FromResource(Resource.Drawable.pin));
    return marker;
}
```

Questo metodo crea un nuovo `MarkerOption` per ogni istanza `Pin` istanza. Dopo aver impostato la posizione, label e indirizzo del marcatore, la relativa icona viene impostato con la `SetIcon` (metodo). Questo metodo accetta un `BitmapDescriptor` oggetto che contiene i dati necessari per eseguire il rendering, l'icona con il `BitmapDescriptorFactory` classe che fornisce metodi helper per semplificare la creazione del `BitmapDescriptor`.

Per altre informazioni sull'uso di `BitmapDescriptorFactory` classe per personalizzare un marcatore, vedere [personalizzazione di un marcatore](~/android/platform/maps-and-location/maps/maps-api.md).

<a name="Customizing_the_Info_Window" />

#### <a name="customizing-the-info-window"></a>Personalizzazione della finestra di informazioni

Quando un utente tocca il marcatore, il `GetInfoContents` metodo viene eseguito, a condizione che il `GetInfoWindow` restituzione del metodo `null`. Nell'esempio di codice riportato di seguito viene illustrato il `GetInfoContents` metodo:

```csharp
public Android.Views.View GetInfoContents (Marker marker)
{
  var inflater = Android.App.Application.Context.GetSystemService (Context.LayoutInflaterService) as Android.Views.LayoutInflater;
  if (inflater != null) {
    Android.Views.View view;

    var customPin = GetCustomPin (marker);
    if (customPin == null) {
      throw new Exception ("Custom pin not found");
    }

    if (customPin.Id.ToString() == "Xamarin") {
      view = inflater.Inflate (Resource.Layout.XamarinMapInfoWindow, null);
    } else {
      view = inflater.Inflate (Resource.Layout.MapInfoWindow, null);
    }

    var infoTitle = view.FindViewById<TextView> (Resource.Id.InfoWindowTitle);
    var infoSubtitle = view.FindViewById<TextView> (Resource.Id.InfoWindowSubtitle);

    if (infoTitle != null) {
      infoTitle.Text = marker.Title;
    }
    if (infoSubtitle != null) {
      infoSubtitle.Text = marker.Snippet;
    }

    return view;
  }
  return null;
}
```

Questo metodo restituisce un `View` che include il contenuto della finestra di informazioni. Questa operazione viene eseguita come indicato di seguito:

- Oggetto `LayoutInflater` istanza viene recuperata. Viene utilizzato per creare un'istanza di un file XML di layout nel relativo valore corrispondente `View`.
- Il `GetCustomPin` viene chiamato per restituire i dati personalizzati pin per la finestra di informazioni.
- Il `XamarinMapInfoWindow` layout viene sottoposto a inflating se il `CustomPin.Id` è uguale alla proprietà `Xamarin`. In caso contrario, il `MapInfoWindow` layout viene ingrandito. In questo modo per gli scenari in cui possono essere visualizzati i layout delle finestre informazioni diverse per diversi simboli marcatori.
- Il `InfoWindowTitle` e `InfoWindowSubtitle` le risorse sono recuperate dal layout maggiori del necessario e i relativi `Text` proprietà vengono impostate per i dati corrispondenti dal `Marker` dell'istanza, purché le risorse non siano `null`.
- Il `View` viene restituita l'istanza da visualizzare sulla mappa.

> [!NOTE]
> Una finestra di informazioni non è attivo `View`. Al contrario, verrà convertito Android il `View` su uno statico bitmap e di visualizzarla come immagine. Ciò significa che una finestra di informazioni può rispondere a un evento click, non può rispondere a eventi di tocco o movimenti e i singoli controlli nella finestra di informazioni non possono rispondere alla propria eventi click.

<a name="Clicking_on_the_Info_Window" />

#### <a name="clicking-on-the-info-window"></a>Fare clic sulla finestra di informazioni

Quando l'utente fa clic nella finestra di informazioni, il `InfoWindowClick` viene generato l'evento, che a sua volta esegue il `OnInfoWindowClick` metodo:

```csharp
void OnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
  var customPin = GetCustomPin (e.Marker);
  if (customPin == null) {
    throw new Exception ("Custom pin not found");
  }

  if (!string.IsNullOrWhiteSpace (customPin.Url)) {
    var url = Android.Net.Uri.Parse (customPin.Url);
    var intent = new Intent (Intent.ActionView, url);
    intent.AddFlags (ActivityFlags.NewTask);
    Android.App.Application.Context.StartActivity (intent);
  }
}
```

Questo metodo consente di aprire un web browser e Naviga all'indirizzo archiviato in indirizzo il `Url` proprietà dell'oggetto recuperato `CustomPin` dell'istanza di `Marker`. Si noti che l'indirizzo è stato definito quando si crea il `CustomPin` insieme nel progetto della libreria .NET Standard.

Per altre informazioni sulla personalizzazione di un `MapView` dell'istanza, vedere [API Maps](~/android/platform/maps-and-location/maps/maps-api.md).

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Creare il Renderer personalizzato sulla piattaforma Windows universale

Le schermate seguenti illustrano la mappa, prima e dopo la personalizzazione:

![](customized-pin-images/map-layout-uwp.png "Controllo mappa di prima e dopo la personalizzazione")

Nella piattaforma UWP il pin viene chiamato un *icona della mappa*, e può essere un'immagine personalizzata o immagine predefinita definito dal sistema. Un'icona di mappa è possibile mostrare un `UserControl`, che viene visualizzata in risposta all'utente toccando l'icona della mappa. Il `UserControl` può visualizzare qualsiasi contenuto, tra cui la `Label` e `Address` delle proprietà del `Pin` istanza.

Esempio di codice seguente illustra il renderer personalizzato UWP:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        MapControl nativeMap;
        List<CustomPin> customPins;
        XamarinMapOverlay mapOverlay;
        bool xamarinOverlayShown = false;

        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                nativeMap.MapElementClick -= OnMapElementClick;
                nativeMap.Children.Clear();
                mapOverlay = null;
                nativeMap = null;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                nativeMap = Control as MapControl;
                customPins = formsMap.CustomPins;

                nativeMap.Children.Clear();
                nativeMap.MapElementClick += OnMapElementClick;

                foreach (var pin in customPins)
                {
                    var snPosition = new BasicGeoposition { Latitude = pin.Pin.Position.Latitude, Longitude = pin.Pin.Position.Longitude };
                    var snPoint = new Geopoint(snPosition);

                    var mapIcon = new MapIcon();
                    mapIcon.Image = RandomAccessStreamReference.CreateFromUri(new Uri("ms-appx:///pin.png"));
                    mapIcon.CollisionBehaviorDesired = MapElementCollisionBehavior.RemainVisible;
                    mapIcon.Location = snPoint;
                    mapIcon.NormalizedAnchorPoint = new Windows.Foundation.Point(0.5, 1.0);

                    nativeMap.MapElements.Add(mapIcon);                    
                }
            }
        }
        ...
    }
}
```

Il `OnElementChanged` metodo esegue le operazioni seguenti, condizione che il renderer personalizzato è allegato a un nuovo elemento di xamarin. Forms:

- Cancella il `MapControl.Children` insieme per rimuovere tutti gli elementi dell'interfaccia utente esistenti dalla mappa, prima di registrare un gestore eventi per il `MapElementClick` evento. Questo evento viene generato quando l'utente tocca o fa clic su un `MapElement` nella `MapControl`e viene annullato la sottoscrizione da solo quando l'elemento del renderer è associato alle modifiche.
- Ogni pin nel `customPins` raccolta viene visualizzata in corrispondenza della posizione geografica corretta sulla mappa, come indicato di seguito:
  - Il percorso per il pin viene creato come un `Geopoint` istanza.
  - Oggetto `MapIcon` istanza viene creata per rappresentare il pin.
  - L'immagine usata per rappresentare il `MapIcon` viene specificato impostando il `MapIcon.Image` proprietà. Tuttavia, la mappa dell'immagine dell'icona è sempre necessariamente essere visualizzati, come può essere nascosto da altri elementi sulla mappa. Pertanto, la mappa dell'icona `CollisionBehaviorDesired` è impostata su `MapElementCollisionBehavior.RemainVisible`, per garantire che rimane visibile.
  - Il percorso dei `MapIcon` viene specificato impostando il `MapIcon.Location` proprietà.
  - Il `MapIcon.NormalizedAnchorPoint` è impostata su località approssimativa del puntatore sull'immagine. Se questa proprietà mantiene il valore predefinito di (0,0), che rappresenta l'angolo superiore sinistro dell'immagine, le modifiche del livello di zoom della mappa possono comportare l'immagine che punta a un percorso diverso.
  - Il `MapIcon` istanza viene aggiunta al `MapControl.MapElements` raccolta. Il risultato è l'icona della mappa visualizzato sul `MapControl`.

> [!NOTE]
> Quando si usa la stessa immagine per più le icone di mappa, la `RandomAccessStreamReference` istanza deve essere dichiarata a livello di pagina o un'applicazione per ottenere prestazioni ottimali.

#### <a name="displaying-the-usercontrol"></a>Visualizzazione di UserControl

Quando un utente tocca l'icona della mappa, la `OnMapElementClick` metodo viene eseguito. Esempio di codice seguente viene illustrato questo metodo:

```csharp
private void OnMapElementClick(MapControl sender, MapElementClickEventArgs args)
{
    var mapIcon = args.MapElements.FirstOrDefault(x => x is MapIcon) as MapIcon;
    if (mapIcon != null)
    {
        if (!xamarinOverlayShown)
        {
            var customPin = GetCustomPin(mapIcon.Location.Position);
            if (customPin == null)
            {
                throw new Exception("Custom pin not found");
            }

            if (customPin.Id.ToString() == "Xamarin")
            {
                if (mapOverlay == null)
                {
                    mapOverlay = new XamarinMapOverlay(customPin);
                }

                var snPosition = new BasicGeoposition { Latitude = customPin.Pin.Position.Latitude, Longitude = customPin.Pin.Position.Longitude };
                var snPoint = new Geopoint(snPosition);

                nativeMap.Children.Add(mapOverlay);
                MapControl.SetLocation(mapOverlay, snPoint);
                MapControl.SetNormalizedAnchorPoint(mapOverlay, new Windows.Foundation.Point(0.5, 1.0));
                xamarinOverlayShown = true;
            }
        }
        else
        {
            nativeMap.Children.Remove(mapOverlay);
            xamarinOverlayShown = false;
        }
    }
}
```

Questo metodo crea un `UserControl` istanza che visualizza informazioni sui pin. Questa operazione viene eseguita come indicato di seguito:

- Il `MapIcon` istanza viene recuperata.
- Il `GetCustomPin` viene chiamato per restituire i dati di pin personalizzato che verranno visualizzati.
- Oggetto `XamarinMapOverlay` istanza viene creata per visualizzare i dati personalizzati pin. Questa classe è un controllo utente.
- La posizione geografica in cui visualizzare il `XamarinMapOverlay` all'istanza sul `MapControl` viene creato come un `Geopoint` istanza.
- Il `XamarinMapOverlay` istanza viene aggiunta al `MapControl.Children` raccolta. Questa raccolta contiene elementi dell'interfaccia utente XAML che verranno visualizzati sulla mappa.
- La posizione geografica del `XamarinMapOverlay` istanza sulla mappa viene impostata chiamando il `SetLocation` (metodo).
- La posizione relativa nella `XamarinMapOverlay` istanza, che corrisponde al percorso specificato, viene impostato chiamando il `SetNormalizedAnchorPoint` (metodo). In questo modo si garantisce che le modifiche apportate nel livello di zoom del risultato della mappa nel `XamarinMapOverlay` istanza viene sempre visualizzato nella posizione corretta.

In alternativa, se le informazioni sui pin sono già visualizzate sulla mappa, se si tocca la mappa Rimuove le `XamarinMapOverlay` dell'istanza dal `MapControl.Children` raccolta.

#### <a name="tapping-on-the-information-button"></a>Se si tocca il pulsante informazioni

Quando l'utente tocca il *informazioni* pulsante nel `XamarinMapOverlay` controllo utente, il `Tapped` viene generato l'evento, che a sua volta esegue il `OnInfoButtonTapped` metodo:

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

Questo metodo consente di aprire un web browser e Naviga all'indirizzo archiviato in indirizzo il `Url` proprietà del `CustomPin` istanza. Si noti che l'indirizzo è stato definito quando si crea il `CustomPin` insieme nel progetto della libreria .NET Standard.

Per altre informazioni sulla personalizzazione di un `MapControl` dell'istanza, vedere [mappe e posizione Panoramica](https://msdn.microsoft.com/library/windows/apps/mt219699.aspx) su MSDN.

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come creare un renderer personalizzato per il `Map` (controllo), consentendo agli sviluppatori di eseguire l'override del rendering nativo predefinito con le personalizzazioni specifiche della piattaforma. Verifica fornisce un'astrazione di multi-piattaforma per la visualizzazione delle mappe che utilizzano la mappa nativa le API in ogni piattaforma per fornire una mappa veloce e familiare esperienza per gli utenti.


## <a name="related-links"></a>Collegamenti correlati

- [Controllo Maps](~/xamarin-forms/user-interface/map.md)
- [Mappe di iOS](~/ios/user-interface/controls/ios-maps/index.md)
- [API Maps](~/android/platform/maps-and-location/maps/maps-api.md)
- [Pin personalizzato (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/pin/)
