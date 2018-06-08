---
title: Personalizzazione di un Pin di mappa
description: In questo articolo viene illustrato come creare un renderer personalizzato per il controllo mappa, che consente di visualizzare una mappa con un pin personalizzato e una visualizzazione personalizzata dei dati nativa in ciascuna piattaforma.
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 029dbf073f61e3a07ec01da4f877bf997af57d98
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848551"
---
# <a name="customizing-a-map-pin"></a>Personalizzazione di un Pin di mappa

_In questo articolo viene illustrato come creare un renderer personalizzato per il controllo mappa, che consente di visualizzare una mappa con un pin personalizzato e una visualizzazione personalizzata dei dati nativa in ciascuna piattaforma._

Tutte le viste di xamarin. Forms sono un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) viene eseguito il rendering da un'applicazione di xamarin. Forms in iOS, il `MapRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza nativa `MKMapView` controllo. Nella piattaforma Android, il `MapRenderer` un'istanza nativa `MapView` controllo. Nel Windows piattaforma UWP (Universal), il `MapRenderer` un'istanza nativa `MapControl`. Per ulteriori informazioni sulle classi di controllo nativo che eseguono il mapping per i controlli di xamarin. Forms e renderer, vedere [Renderer classi di Base e i controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra il [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) e i corrispondenti controlli nativi che l'implementano:

![](customized-pin-images/map-classes.png "Relazione tra il controllo mappa e i controlli nativi di implementazione")

Il processo di rendering può essere utilizzato per implementare le personalizzazioni specifiche della piattaforma tramite la creazione di un renderer personalizzato per un [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) in ciascuna piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Custom_Map) una mappa personalizzata xamarin. Forms.
1. [Utilizzare](#Consuming_the_Custom_Map) la mappa personalizzata da xamarin. Forms.
1. [Creare](#Creating_the_Custom_Renderer_on_each_Platform) il renderer personalizzato per la mappa in ogni piattaforma.

Ogni elemento verrà ora essere descritti in dettaglio, per implementare un `CustomMap` renderer che consente di visualizzare una mappa con un pin personalizzato e una visualizzazione personalizzata dei dati nativa in ciascuna piattaforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) deve essere inizializzato e configurato prima dell'uso. Per altre informazioni, vedere [`Maps Control`](~/xamarin-forms/user-interface/map.md).

<a name="Creating_the_Custom_Map" />

## <a name="creating-the-custom-map"></a>Creazione della mappa personalizzata

È possibile creare un controllo mappa personalizzata creando sottoclassi di [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) classe, come illustrato nell'esempio di codice seguente:

```csharp
public class CustomMap : Map
{
  public List<CustomPin> CustomPins { get; set; }
}
```

Il `CustomMap` controllo viene creato nel progetto di libreria Standard di .NET e definisce l'API per la mappa personalizzata. La mappa personalizzata espone il `CustomPins` proprietà che rappresenta la raccolta di `CustomPin` gli oggetti che verranno visualizzati dal controllo mappa nativi in ciascuna piattaforma. La `CustomPin` classe è illustrata nell'esempio di codice seguente:

```csharp
public class CustomPin : Pin
{
  public string Url { get; set; }
}
```

Questa classe definisce un `CustomPin` come classe che eredita le proprietà del [ `Pin` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Pin/) classe e l'aggiunta di un `Url` proprietà.

<a name="Consuming_the_Custom_Map" />

## <a name="consuming-the-custom-map"></a>Utilizzo di mappa personalizzata

Il `CustomMap` controllo possibile farvi riferimento in XAML nel progetto di libreria .NET Standard dichiarando uno spazio dei nomi per la posizione e utilizzando il prefisso dello spazio dei nomi del controllo mappa personalizzata. Nell'esempio di codice riportato di seguito viene illustrato come la `CustomMap` controllo può essere utilizzato da una pagina XAML:

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

Il `local` prefisso dello spazio dei nomi denominato nulla. Tuttavia, il `clr-namespace` e `assembly` i valori devono corrispondere i dettagli della mappa personalizzata. Dopo aver dichiarato lo spazio dei nomi, il prefisso viene utilizzato per fare riferimento a mappa personalizzata.

Nell'esempio di codice riportato di seguito viene illustrato come la `CustomMap` controllo può essere utilizzato da una pagina in c#:

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

Il `CustomMap` istanza verrà utilizzata per visualizzare la mappa nativa in ciascuna piattaforma. Ha [ `MapType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.MapType/) proprietà imposta lo stile di visualizzazione di [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/), con i valori possibili viene definiti nel [ `MapType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapType/) enumerazione. Per iOS e Android, la larghezza e l'altezza della mappa viene impostata tramite le proprietà del `App` classe inizializzate nei progetti specifici della piattaforma.

Il percorso della mappa e il PIN contiene, vengono inizializzate come illustrato nell'esempio di codice seguente:

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

Questa inizializzazione aggiunge un pin personalizzato e posiziona la visualizzazione della mappa con la [ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)/) (metodo), che modifica la posizione e il livello di zoom della mappa creando un [ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/) da un [ `Position` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/) e [ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/).

Un renderer personalizzato può ora essere aggiunto a ogni progetto di applicazione per personalizzare i controlli nativi di mappa.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creazione di Renderer personalizzato a seconda della piattaforma

Il processo di creazione della classe renderer personalizzato è come segue:

1. Creare una sottoclasse di `MapRenderer` classe che esegue il rendering della mappa personalizzata.
1. Eseguire l'override di `OnElementChanged` metodo che esegue il rendering di mappa personalizzata e scrivere la logica per personalizzarlo. Questo metodo viene chiamato quando viene creata la mappa personalizzata corrispondente di xamarin. Forms.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzato per specificare che verrà usata per eseguire il rendering della mappa personalizzata xamarin. Forms. Questo attributo viene utilizzato per registrare il renderer personalizzato con xamarin. Forms.

> [!NOTE]
> È facoltativo per fornire un renderer personalizzato in ogni progetto della piattaforma. Se non è registrato un renderer personalizzato, verrà utilizzato il renderer predefinito per la classe di base del controllo.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con le relazioni tra di essi:

![](customized-pin-images/solution-structure.png "Responsabilità di progetto personalizzato CustomMap Renderer")

Il `CustomMap` rendering del controllo da classi renderer specifico della piattaforma, che derivano dalla `MapRenderer` classe per ogni piattaforma. Di conseguenza, ogni `CustomMap` controllo viene eseguito il rendering con controlli specifici della piattaforma, come illustrato nelle schermate seguenti:

![](customized-pin-images/screenshots.png "CustomMap in ciascuna piattaforma")

Il `MapRenderer` classe espone il `OnElementChanged` metodo, che viene chiamato quando viene creata la mappa personalizzata xamarin. Forms per eseguire il rendering del controllo nativo corrispondente. Questo metodo accetta un `ElementChangedEventArgs` parametro contenente `OldElement` e `NewElement` proprietà. Queste proprietà rappresentano l'elemento di xamarin. Forms che il renderer *è stata* collegata, l'elemento di xamarin. Forms e che il renderer *è* associata rispettivamente. Nell'applicazione di esempio di `OldElement` proprietà sarà `null` e `NewElement` proprietà contiene un riferimento al `CustomMap` istanza.

Una versione sottoposta a override del `OnElementChanged` (metodo), in ogni classe renderer specifico della piattaforma, è possibile eseguire la personalizzazione del controllo nativo. Un riferimento tipizzato al controllo nativo utilizzato nella piattaforma sono accessibili tramite il `Control` proprietà. Inoltre, un riferimento al controllo di xamarin. Forms che viene eseguito il rendering può essere ottenuto tramite il `Element` proprietà.

Prestare attenzione quando si sottoscrive i gestori eventi nel `OnElementChanged` (metodo), come illustrato nell'esempio di codice seguente:

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

Il controllo nativo deve essere configurato e gestori eventi sottoscritti solo quando è collegato il renderer personalizzato a un nuovo elemento di xamarin. Forms. Analogamente, gestori di eventi che sono stati sottoscritti devono essere annullati da solo quando l'elemento collegato per il renderer cambia. Questo approccio consente di creare un renderer personalizzato che non subisca delle perdite di memoria.

Ogni classe renderer personalizzato è decorato con un `ExportRenderer` attributo che registra il renderer con xamarin. Forms. L'attributo accetta due parametri: il nome del tipo del controllo personalizzato xamarin. Forms viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il `assembly` prefisso per l'attributo specifica che l'attributo si applica all'intero assembly.

Le sezioni seguenti illustrano l'implementazione di ogni classe renderer personalizzato specifico della piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creazione di Renderer personalizzato in iOS

Le schermate seguenti mostrano la mappa, prima e dopo la personalizzazione:

![](customized-pin-images/map-layout-ios.png "Controllo mappa prima e dopo la personalizzazione")

In iOS il pin viene chiamato un *annotazione*, e può essere un'immagine personalizzata o un pin definita dal sistema di colori diversi. Le annotazioni possono essere resi visibili una *callout*, che viene visualizzato nella risposta per l'utente selezionando l'annotazione. Il callout consente di visualizzare il `Label` e `Address` le proprietà del `Pin` istanza, con facoltativo a sinistra e destra degli accessori viste. Nella schermata precedente, la visualizzazione degli accessori a sinistra è l'immagine di un monkey, con la vista a destra degli accessori il *informazioni* pulsante.

Esempio di codice seguente viene illustrato il renderer personalizzato per la piattaforma iOS:

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

Il `OnElementChanged` metodo esegue la seguente configurazione del [ `MKMapView` ](https://developer.xamarin.com/api/type/MapKit.MKMapView/) istanza, a condizione che il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms:

- Il [ `GetViewForAnnotation` ](https://developer.xamarin.com/api/property/MapKit.MKMapView.GetViewForAnnotation/) è impostata sul `GetViewForAnnotation` metodo. Questo metodo viene chiamato quando il [percorso dell'annotazione diventa visibile sulla mappa](#Displaying_the_Annotation)e viene utilizzato per personalizzare il prima annotazione da visualizzare.
- Gestori eventi per il `CalloutAccessoryControlTapped`, `DidSelectAnnotationView`, e `DidDeselectAnnotationView` gli eventi vengono registrati. Questi eventi vengono attivati quando l'utente [tocca l'accessorio destra nel callout](#Tapping_on_the_Right_Callout_Accessory_View)e quando l'utente [seleziona](#Selecting_the_Annotation) e [deseleziona](#Deselecting_the_Annotation) l'annotazione, rispettivamente. Gli eventi sono annullati da solo quando l'elemento del renderer è associato alle modifiche.

<a name="Displaying_the_Annotation" />

#### <a name="displaying-the-annotation"></a>Visualizzare l'annotazione

Il `GetViewForAnnotation` metodo viene chiamato quando il percorso dell'annotazione diventa visibile sulla mappa e viene utilizzato per personalizzare il prima annotazione da visualizzare. Un'annotazione è costituito da due parti:

- `MkAnnotation` -include title, subtitle e percorso dell'annotazione.
- `MkAnnotationView` : contiene l'immagine per rappresentare l'annotazione e, facoltativamente, una didascalia che viene visualizzata quando l'utente tocca l'annotazione.

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

Questo metodo garantisce che l'annotazione verrà visualizzata come un'immagine personalizzata, anziché come pin definita dal sistema e che quando verrà scelti l'annotazione callout verrà visualizzato che include contenuto aggiuntivo a sinistra e a destra del titolo di annotazione e dell'indirizzo . Questa operazione viene eseguita come indicato di seguito:

1. Il `GetCustomPin` metodo viene chiamato per restituire i dati personalizzati pin per l'annotazione.
1. Per risparmiare memoria, la visualizzazione dell'annotazione è in pool per il riutilizzo con la chiamata a [ `DequeueReusableAnnotation` ](https://developer.xamarin.com/api/member/MapKit.MKMapView.DequeueReusableAnnotation/(System.String)/).
1. Il `CustomMKAnnotationView` classe estende la `MKAnnotationView` classe con `Id` e `Url` proprietà che corrispondono alle proprietà identiche nel `CustomPin` istanza. Una nuova istanza di `CustomMKAnnotationView` è stato creato, purché l'annotazione è `null`:
  - Il `CustomMKAnnotationView.Image` proprietà è impostata per l'immagine che rappresenta l'annotazione sulla mappa.
  - Il `CustomMKAnnotationView.CalloutOffset` è impostata su un `CGPoint` che specifica che il callout verrà centrato sopra l'annotazione.
  - Il `CustomMKAnnotationView.LeftCalloutAccessoryView` è impostata su un'immagine di un monkey che verrà visualizzato a sinistra del titolo di annotazione e dell'indirizzo.
  - Il `CustomMKAnnotationView.RightCalloutAccessoryView` è impostata su un *informazioni* pulsante che verrà visualizzato a destra del titolo di annotazione e dell'indirizzo.
  - Il `CustomMKAnnotationView.Id` è impostata sul `CustomPin.Id` restituito dalla proprietà di `GetCustomPin` metodo. In questo modo l'annotazione di identificare in modo che possa [callout è possibile personalizzare ulteriormente](#Selecting_the_Annotation), se necessario.
  - Il `CustomMKAnnotationView.Url` è impostata sul `CustomPin.Url` restituito dalla proprietà di `GetCustomPin` metodo. L'URL verrà aperto quando l'utente [tocca il pulsante nella visualizzazione degli accessori callout destra](#Tapping_on_the_Right_Callout_Accessory_View).
1. Il [ `MKAnnotationView.CanShowCallout` ](https://developer.xamarin.com/api/property/MapKit.MKAnnotationView.CanShowCallout/) è impostata su `true` in modo che la didascalia viene visualizzata quando si tocca l'annotazione.
1. L'annotazione viene restituita per la visualizzazione della mappa.

<a name="Selecting_the_Annotation" />

#### <a name="selecting-the-annotation"></a>Selezionando l'annotazione

Quando l'utente tocca sull'annotazione, il `DidSelectAnnotationView` viene generato l'evento, che a sua volta esegue il `OnDidSelectAnnotationView` metodo:

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

Questo metodo estende il callout esistente (che contiene le viste a sinistra e destra degli accessori) aggiungendo un `UIView` istanza, che contiene un'immagine del logo Xamarin, a condizione che l'annotazione selezionata relativo `Id` proprietà è impostata su `Xamarin`. In questo modo per gli scenari in cui è possibile visualizzare callout diversi per diverse annotazioni. Il `UIView` istanza verrà visualizzata centrato di sopra del callout esistente.

<a name="Tapping_on_the_Right_Callout_Accessory_View" />

#### <a name="tapping-on-the-right-callout-accessory-view"></a>Se si tocca nella vista destra accessori Callout

Quando l'utente tocca sul *informazioni* pulsante nella visualizzazione degli accessori callout destra, il `CalloutAccessoryControlTapped` viene generato l'evento, che a sua volta esegue il `OnCalloutAccessoryControlTapped` metodo:

```csharp
void OnCalloutAccessoryControlTapped (object sender, MKMapViewAccessoryTappedEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  if (!string.IsNullOrWhiteSpace (customView.Url)) {
    UIApplication.SharedApplication.OpenUrl (new Foundation.NSUrl (customView.Url));
  }
}
```

Questo metodo apre un browser web e consente di passare all'indirizzo archiviato nel `CustomMKAnnotationView.Url` proprietà. Si noti che l'indirizzo è stato definito durante la creazione di `CustomPin` insieme nel progetto di libreria .NET Standard.

<a name="Deselecting_the_Annotation" />

#### <a name="deselecting-the-annotation"></a>Se si deseleziona l'annotazione

Quando l'annotazione verrà visualizzata e l'utente tocca sulla mappa, la `DidDeselectAnnotationView` viene generato l'evento, che a sua volta esegue il `OnDidDeselectAnnotationView` metodo:

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

Questo metodo garantisce che i callout esistente non è selezionato, la parte della linea del callout (l'immagine del logo Xamarin) estesa verrà interrotto anche la visualizzazione e verranno rilasciate le risorse.

Per ulteriori informazioni sulla personalizzazione di un `MKMapView` dell'istanza, vedere [iOS mappe](~/ios/user-interface/controls/ios-maps/index.md).

### <a name="creating-the-custom-renderer-on-android"></a>Creazione di Renderer personalizzato in Android

Le schermate seguenti mostrano la mappa, prima e dopo la personalizzazione:

![](customized-pin-images/map-layout-android.png "Controllo mappa prima e dopo la personalizzazione")

In Android viene chiamato il pin un *marcatore*, e può essere un'immagine personalizzata o un marcatore definita dal sistema di colori diversi. Consente di visualizzare gli indicatori un *finestra info*, che viene visualizzato in risposta all'utente se si tocca sul marcatore. Consente di visualizzare la finestra delle informazioni di `Label` e `Address` le proprietà del `Pin` dell'istanza e possono essere personalizzati per includere altri contenuti. Tuttavia, solo una finestra di informazioni può visualizzare contemporaneamente.

Esempio di codice seguente viene illustrato il renderer personalizzato per la piattaforma Android:

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

Condizione che il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms, il `OnElementChanged` chiamate al metodo di `MapView.GetMapAsync` metodo, che ottiene l'oggetto sottostante `GoogleMap` che è correlato alla vista. Una volta il `GoogleMap` istanza è disponibile, il `OnMapReady` sostituzione verrà richiamata. Questo metodo registra un gestore eventi per il `InfoWindowClick` evento, che viene generato quando il [si fa clic su finestra info](#Clicking_on_the_Info_Window)e viene annullata da solo quando l'elemento del renderer è associato alle modifiche. Il `OnMapReady` override anche le chiamate di `SetInfoWindowAdapter` metodo per specificare che il `CustomMapRenderer` istanza della classe fornirà i metodi per personalizzare la finestra di informazioni.

Il `CustomMapRenderer` classe implementa il `GoogleMap.IInfoWindowAdapter` interfaccia [personalizzare la finestra di informazioni](#Customizing_the_Info_Window). Questa interfaccia consente di specificare che devono essere implementati i metodi seguenti:

- `public Android.Views.View GetInfoWindow(Marker marker)` : Questo metodo viene chiamato per restituire una finestra di informazioni personalizzate per un marcatore. Se restituisce `null`, verrà utilizzato il rendering di finestra predefinito. Se viene restituito un `View`, che quindi `View` devono essere posizionati all'interno della cornice della finestra informazioni.
- `public Android.Views.View GetInfoContents(Marker marker)` : Questo metodo viene chiamato per restituire un `View` che contiene il contenuto della finestra info e verrà chiamata solo se il `GetInfoWindow` restituisce `null`. Se restituisce `null`, verrà utilizzato il rendering predefinito le informazioni del contenuto della finestra.

Nell'applicazione di esempio, solo il contenuto della finestra info personalizzato, quindi il `GetInfoWindow` restituisce `null` per abilitare questa opzione.

#### <a name="customizing-the-marker"></a>Il marcatore di personalizzazione

L'icona utilizzata per rappresentare un marcatore può essere personalizzato tramite la chiamata di `MarkerOptions.SetIcon` metodo. Questo può essere effettuato eseguendo l'override di `CreateMarker` metodo, che viene richiamata per ogni `Pin` che viene aggiunto alla mappa:

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

Questo metodo crea un nuovo `MarkerOption` istanza per ogni `Pin` istanza. Dopo aver impostato la posizione, l'etichetta e l'indirizzo del marcatore, è impostata la relativa icona con il `SetIcon` metodo. Questo metodo accetta un `BitmapDescriptor` oggetto contenente i dati necessari per il rendering, l'icona con il `BitmapDescriptorFactory` classe fornisce metodi di supporto per semplificare la creazione del `BitmapDescriptor`.

Per ulteriori informazioni sull'utilizzo di `BitmapDescriptorFactory` classe per personalizzare un marcatore, vedere [personalizzazione di un marcatore](~/android/platform/maps-and-location/maps/maps-api.md).

<a name="Customizing_the_Info_Window" />

#### <a name="customizing-the-info-window"></a>Personalizzazione della finestra di informazioni

Quando un utente tocca sul marcatore del `GetInfoContents` metodo viene eseguito, a condizione che il `GetInfoWindow` restituisce `null`. Nell'esempio di codice riportato di seguito viene illustrato il `GetInfoContents` metodo:

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

Questo metodo restituisce un `View` che include il contenuto della finestra info. Questa operazione viene eseguita come indicato di seguito:

- Oggetto `LayoutInflater` istanza viene recuperata. Viene utilizzato per creare un'istanza di un file XML di layout in corrispondente `View`.
- Il `GetCustomPin` metodo viene chiamato per restituire i dati personalizzati pin per la finestra di informazioni.
- Il `XamarinMapInfoWindow` layout viene ingrandito se il `CustomPin.Id` proprietà è uguale a `Xamarin`. In caso contrario, il `MapInfoWindow` layout viene ingrandito. In questo modo per gli scenari in cui il layout di finestra informazioni diverse può essere visualizzato per i marcatori di diversi.
- Il `InfoWindowTitle` e `InfoWindowSubtitle` risorse vengono recuperate dal layout ingrandito e i relativi `Text` proprietà vengono impostate per i dati corrispondenti dal `Marker` istanza, a condizione che le risorse non sono `null`.
- Il `View` viene restituita l'istanza per la visualizzazione della mappa.

> [!NOTE]
> Una finestra info non è attivo `View`. Al contrario, si convertirà Android di `View` statico bitmap e visualizzare che come un'immagine. Ciò significa che una finestra di informazioni può rispondere a un evento click, non può rispondere a eventi tocco o i movimenti e i singoli controlli nella finestra info non possono rispondere ai propri eventi click.

<a name="Clicking_on_the_Info_Window" />

#### <a name="clicking-on-the-info-window"></a>Fare clic sulla finestra Info

Quando l'utente fa clic sulla finestra info di `InfoWindowClick` viene generato l'evento, che a sua volta esegue il `OnInfoWindowClick` metodo:

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

Questo metodo apre un browser web e consente di passare all'indirizzo archiviato nel `Url` proprietà dell'oggetto recuperato `CustomPin` istanza per il `Marker`. Si noti che l'indirizzo è stato definito durante la creazione di `CustomPin` insieme nel progetto di libreria .NET Standard.

Per ulteriori informazioni sulla personalizzazione di un `MapView` dell'istanza, vedere [API delle mappe](~/android/platform/maps-and-location/maps/maps-api.md).

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Creazione di Renderer personalizzato nella piattaforma Windows universale

Le schermate seguenti mostrano la mappa, prima e dopo la personalizzazione:

![](customized-pin-images/map-layout-uwp.png "Controllo mappa prima e dopo la personalizzazione")

In UWP il pin viene chiamato un *icona mappa*, e può essere un'immagine personalizzata o l'immagine predefinita definita dal sistema. Consente di visualizzare un'icona mappa un `UserControl`, che viene visualizzato in risposta all'utente toccando l'icona della mappa. Il `UserControl` può visualizzare qualsiasi contenuto, inclusi il `Label` e `Address` le proprietà del `Pin` istanza.

Esempio di codice seguente viene illustrato il renderer personalizzato UWP:

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

Il `OnElementChanged` esegue le operazioni seguenti, purché il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms:

- Cancella il `MapControl.Children` insieme per rimuovere tutti gli elementi dell'interfaccia utente esistenti dalla mappa, prima di registrare un gestore eventi per il `MapElementClick` evento. Questo evento viene generato quando l'utente tocca o fa clic su un `MapElement` sul `MapControl`e viene annullata da solo quando l'elemento del renderer è associato alle modifiche.
- Ogni pin nel `customPins` raccolta viene visualizzata nella posizione corretta geografica nella mappa, come indicato di seguito:
  - Il percorso per il pin viene creato come un `Geopoint` istanza.
  - Oggetto `MapIcon` istanza viene creata per rappresentare il pin.
  - L'immagine usata per rappresentare il `MapIcon` viene specificato impostando il `MapIcon.Image` proprietà. Tuttavia, immagine dell'icona mappa non è sempre garantito da visualizzare come può essere nascosto da altri elementi nella mappa. Pertanto, la mappa dell'icona `CollisionBehaviorDesired` è impostata su `MapElementCollisionBehavior.RemainVisible`, per assicurare che rimane visibile.
  - Il percorso del `MapIcon` viene specificato impostando il `MapIcon.Location` proprietà.
  - Il `MapIcon.NormalizedAnchorPoint` proprietà è impostata per la posizione approssimativa dell'indicatore di misura dell'immagine. Se questa proprietà mantiene il valore predefinito (0,0), che rappresenta l'angolo superiore sinistro dell'immagine, l'immagine che punta a una posizione diversa possono comportare modifiche nel livello di zoom della mappa.
  - Il `MapIcon` istanza viene aggiunto per il `MapControl.MapElements` insieme. Di conseguenza, l'icona mappa verrà visualizzata la `MapControl`.

> [!NOTE]
> Quando si utilizza la stessa immagine per le icone di mappa più, il `RandomAccessStreamReference` istanza deve essere dichiarata a livello di pagina o un'applicazione per ottenere prestazioni ottimali.

#### <a name="displaying-the-usercontrol"></a>Visualizzazione di UserControl

Quando un utente tocca sull'icona della mappa, la `OnMapElementClick` metodo viene eseguito. Esempio di codice seguente viene illustrato questo metodo:

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
- Il `GetCustomPin` metodo viene chiamato per restituire i dati di pin personalizzato che verranno visualizzati.
- Oggetto `XamarinMapOverlay` per visualizzare i dati personalizzati pin viene creata l'istanza. Questa classe è un controllo utente.
- La posizione geografica in cui visualizzare il `XamarinMapOverlay` istanza sul `MapControl` viene creata come un `Geopoint` istanza.
- Il `XamarinMapOverlay` istanza viene aggiunto per il `MapControl.Children` insieme. Questa raccolta contiene elementi dell'interfaccia utente XAML che verranno visualizzati nella mappa.
- La posizione geografica del `XamarinMapOverlay` istanza nella mappa viene impostato chiamando la `SetLocation` metodo.
- Il percorso relativo nel `XamarinMapOverlay` istanza, che corrisponde al percorso specificato, viene impostato chiamando la `SetNormalizedAnchorPoint` metodo. In questo modo si garantisce che le modifiche apportate nel livello di zoom del risultato nella mappa di `XamarinMapOverlay` istanza viene sempre visualizzato nella posizione corretta.

In alternativa, se informazioni il pin sono già visualizzate sulla mappa, selezionando la mappa Rimuove il `XamarinMapOverlay` istanza il `MapControl.Children` insieme.

#### <a name="tapping-on-the-information-button"></a>Toccando il pulsante informazioni

Quando l'utente tocca sul *informazioni* pulsante il `XamarinMapOverlay` controllo utente, il `Tapped` viene generato l'evento, che a sua volta esegue il `OnInfoButtonTapped` metodo:

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

Questo metodo apre un browser web e consente di passare all'indirizzo archiviato nel `Url` proprietà del `CustomPin` istanza. Si noti che l'indirizzo è stato definito durante la creazione di `CustomPin` insieme nel progetto di libreria .NET Standard.

Per ulteriori informazioni sulla personalizzazione di un `MapControl` dell'istanza, vedere [mappe e posizione Panoramica](https://msdn.microsoft.com/library/windows/apps/mt219699.aspx) su MSDN.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come creare un renderer personalizzato per il `Map` (controllo), consentendo agli sviluppatori di eseguire l'override per il rendering predefinito nativo con le personalizzazioni specifiche della piattaforma. Xamarin.Forms.Maps fornisce un'astrazione multipiattaforma per la visualizzazione di mappe di utilizzano la mappa nativa le API in ogni piattaforma per fornire una mappa veloce e familiare esperienza per gli utenti.


## <a name="related-links"></a>Collegamenti correlati

- [Controllo di mappe](~/xamarin-forms/user-interface/map.md)
- [Mappe di iOS](~/ios/user-interface/controls/ios-maps/index.md)
- [API Maps](~/android/platform/maps-and-location/maps/maps-api.md)
- [Pin personalizzato (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/pin/)
