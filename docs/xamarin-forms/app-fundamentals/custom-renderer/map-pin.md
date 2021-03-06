---
title: Personalizzazione di un segnaposto per la mappa
description: Questo articolo illustra come creare un renderer personalizzato per il controllo Mappa che visualizza una mappa nativa con un segnaposto personalizzato e una visualizzazione personalizzata dei dati del segnaposto in ogni piattaforma.
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/06/2019
ms.openlocfilehash: 513ba16f0cb74e330cc3b681e0880b685f0c226c
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82532772"
---
# <a name="customizing-a-map-pin"></a>Personalizzazione di un segnaposto per la mappa

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-pin)

_Questo articolo illustra come creare un renderer personalizzato per il controllo mappa che visualizza una mappa nativa con un segnaposto personalizzato e una visualizzazione personalizzata dei dati del segnaposto in ogni piattaforma._

A ogni vista di Xamarin.Forms è associato un renderer per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un [`Map`](xref:Xamarin.Forms.Maps.Map) oggetto viene sottoposto a rendering da un'applicazione Novell. Forms in iOS, viene creata un'istanza della `MapRenderer` classe, che `MKMapView` a sua volta crea un'istanza di un controllo nativo. Nella piattaforma Android la classe `MapRenderer` crea un'istanza di un controllo `MapView` nativo. Nella piattaforma UWP (Universal Windows Platform) la classe `MapRenderer` crea un'istanza di un controllo `MapControl` nativo. Per altre informazioni sulle classi del renderer e dei controlli nativi di cui Xamarin.Forms controlla il mapping, vedere [Classi di base e controlli nativi del renderer](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra [`Map`](xref:Xamarin.Forms.Maps.Map) e i controlli nativi corrispondenti che lo implementano:

![](map-pin-images/map-classes.png "Relationship Between the Map Control and the Implementing Native Controls")

Il processo di rendering può essere utilizzato per implementare personalizzazioni specifiche della piattaforma creando un renderer personalizzato per un [`Map`](xref:Xamarin.Forms.Maps.Map) in ogni piattaforma. Il processo per eseguire questa operazione è il seguente:

1. [Creare](#Creating_the_Custom_Map) una mappa personalizzata Xamarin.Forms.
1. [Utilizzare](#Consuming_the_Custom_Map) la mappa personalizzata da Xamarin.Forms.
1. [Creare](#Creating_the_Custom_Renderer_on_each_Platform) il renderer personalizzato per la mappa in ogni piattaforma.

Verrà descritto ogni elemento per implementare un renderer `CustomMap` che visualizza una mappa nativa con un segnaposto personalizzato e una visualizzazione personalizzata dei dati del segnaposto in ogni piattaforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps)deve essere inizializzata e configurata prima dell'utilizzo. Per ulteriori informazioni, vedere [`Maps Control`](~/xamarin-forms/user-interface/map/index.md).

<a name="Creating_the_Custom_Map" />

## <a name="creating-the-custom-map"></a>Creazione della mappa personalizzata

È possibile creare un controllo mappa personalizzato creando una sottoclasse della [`Map`](xref:Xamarin.Forms.Maps.Map) classe, come illustrato nell'esempio di codice seguente:

```csharp
public class CustomMap : Map
{
    public List<CustomPin> CustomPins { get; set; }
}
```

Il controllo `CustomMap` viene creato nel progetto di libreria .NET Standard e definisce l'API per la mappa personalizzata. La mappa personalizzata espone la proprietà `CustomPins` che rappresenta la raccolta di oggetti `CustomPin` di cui il controllo mappa nativa eseguirà il rendering in ogni piattaforma. La classe `CustomPin` è illustrata nell'esempio di codice seguente:

```csharp
public class CustomPin : Pin
{
    public string Name { get; set; }
    public string Url { get; set; }
}
```

Questa classe definisce un `CustomPin` oggetto come che eredita le proprietà della [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe e aggiunge `Name` le proprietà `Url` e.

<a name="Consuming_the_Custom_Map" />

## <a name="consuming-the-custom-map"></a>Utilizzo della mappa personalizzata

Per fare riferimento al controllo `CustomMap` in XAML nel progetto di libreria .NET Standard, è possibile dichiarare uno spazio dei nomi per il percorso e usare il prefisso dello spazio dei nomi nel controllo mappa personalizzato. Nell'esempio di codice riportato di seguito viene illustrato come il controllo `CustomMap` può essere usato da una pagina XAML:

```xaml
<ContentPage ...
                   xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer">
    <local:CustomMap x:Name="customMap"
                   MapType="Street" />
</ContentPage>

```

Il prefisso dello spazio dei nomi `local` può avere qualsiasi nome. I valori `clr-namespace` e `assembly` devono tuttavia corrispondere ai dettagli della mappa personalizzata. Dopo aver dichiarato lo spazio dei nomi, il prefisso viene usato per fare riferimento alla mappa personalizzata.

Nell'esempio di codice riportato di seguito viene illustrato come il controllo `CustomMap` può essere usato da una pagina C#:

```csharp
public class MapPageCS : ContentPage
{
    public MapPageCS()
    {
        CustomMap customMap = new CustomMap
        {
            MapType = MapType.Street
        };
        // ...
        Content = customMap;
    }
}
```

L'istanza `CustomMap` verrà usata per visualizzare la mappa nativa in ogni piattaforma. La [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) proprietà imposta lo stile di visualizzazione dell'oggetto [`Map`](xref:Xamarin.Forms.Maps.Map), con i valori possibili definiti nell' [`MapType`](xref:Xamarin.Forms.Maps.MapType) enumerazione.

La posizione della mappa e i segnaposto contenuti nella mappa vengono inizializzati come illustrato nell'esempio di codice seguente:

```csharp
public MapPage()
{
    // ...
    CustomPin pin = new CustomPin
    {
        Type = PinType.Place,
        Position = new Position(37.79752, -122.40183),
        Label = "Xamarin San Francisco Office",
        Address = "394 Pacific Ave, San Francisco CA",
        Name = "Xamarin",
        Url = "http://xamarin.com/about/"
    };
    customMap.CustomPins = new List<CustomPin> { pin };
    customMap.Pins.Add(pin);
    customMap.MoveToRegion(MapSpan.FromCenterAndRadius(new Position(37.79752, -122.40183), Distance.FromMiles(1.0)));
}
```

Questa inizializzazione aggiunge un PIN personalizzato e posiziona la visualizzazione della mappa con [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) il metodo, che modifica la posizione e il livello di zoom della mappa creando [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) un oggetto [`Position`](xref:Xamarin.Forms.Maps.Position) da un [`Distance`](xref:Xamarin.Forms.Maps.Distance)oggetto e un oggetto.

Un renderer personalizzato può essere ora aggiunto a ogni progetto di applicazione per personalizzare i controlli mappa nativa.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creazione del renderer personalizzato in ogni piattaforma

Il processo di creazione della classe di renderer personalizzato è il seguente:

1. Creare una sottoclasse della classe `MapRenderer` che esegue il rendering della mappa personalizzata.
1. Eseguire l'override del metodo `OnElementChanged` che esegue il rendering della mappa personalizzata e scrivere la logica per personalizzarlo. Questo metodo viene chiamato quando viene creata la mappa personalizzata Xamarin.Forms corrispondente.
1. Aggiungere un attributo `ExportRenderer` alla classe di renderer personalizzato per specificare che verrà usata per eseguire il rendering della mappa Xamarin.Forms personalizzata. L'attributo viene usato per registrare il renderer personalizzato con Xamarin.Forms.

> [!NOTE]
> La specifica di un renderer personalizzato nel progetto di ogni piattaforma è facoltativa. Se un renderer personalizzato non è registrato, verrà usato il renderer predefinito per la classe di base del controllo.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazioni tra di essi:

![](map-pin-images/solution-structure.png "CustomMap Custom Renderer Project Responsibilities")

Il rendering del controllo `CustomMap` viene eseguito dalle classi di renderer specifiche della piattaforma che derivano dalla classe `MapRenderer` per la piattaforma. Di conseguenza il rendering di ogni controllo `CustomMap` viene eseguito con controlli specifici della piattaforma, come illustrato negli screenshot seguenti:

![](map-pin-images/screenshots.png "CustomMap on each Platform")

La classe `MapRenderer` espone il metodo `OnElementChanged` che viene chiamato quando si crea la mappa personalizzata Xamarin.Forms per eseguire il rendering del controllo nativo corrispondente. Questo metodo accetta un parametro `ElementChangedEventArgs` che contiene le proprietà `OldElement` e `NewElement`. Queste proprietà rappresentano rispettivamente l'elemento Xamarin.Forms a cui il renderer *era* collegato e l'elemento Xamarin.Forms a cui il renderer *è* collegato. Nell'applicazione di esempio la proprietà `OldElement` sarà `null` e la proprietà `NewElement` conterrà un riferimento all'istanza di `CustomMap`.

La personalizzazione del controllo nativo deve essere eseguita in una versione sostituita del metodo `OnElementChanged` in ogni classe di renderer specifica della piattaforma. Un riferimento tipizzato al controllo nativo usato nella piattaforma è accessibile attraverso la proprietà `Control`. È inoltre possibile ottenere un riferimento al controllo Xamarin.Forms di cui viene eseguito il rendering usando la proprietà `Element`.

Prestare attenzione quando si sottoscrivono i gestori degli eventi nel metodo `OnElementChanged`, come illustrato nell'esempio di codice seguente:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.View> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null)
  {
      // Unsubscribe from event handlers
  }

  if (e.NewElement != null)
  {
      // Configure the native control and subscribe to event handlers
  }
}
```

Configurare il controllo nativo e sottoscrivere i gestori degli eventi solo quando il renderer personalizzato è allegato a un nuovo elemento di Xamarin.Forms. Analogamente, per i gestori degli eventi sottoscritti l'iscrizione deve essere annullata solo in caso di modifica dell'elemento allegato al renderer. L'adozione di questo approccio consente di creare un renderer personalizzato che non subisce perdite di memoria.

Ogni classe del renderer personalizzato è decorata con un attributo `ExportRenderer` che registra il renderer in Xamarin.Forms. L'attributo accetta due parametri: il nome del tipo di controllo personalizzato Xamarin.Forms di cui viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il prefisso `assembly` dell'attributo specifica che l'attributo viene applicato all'intero assembly.

Le sezioni seguenti illustrano l'implementazione della classe di renderer personalizzato specifica di ogni piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creazione del renderer personalizzato in iOS

Gli screenshot seguenti mostrano la mappa prima e dopo la personalizzazione:

![](map-pin-images/map-layout-ios.png "Map Control Before and After Customization")

In iOS il segnaposto viene chiamato *annotazione* e può essere un'immagine personalizzata o un segnaposto di vari colori definito dal sistema. Le annotazioni possono visualizzare un *callout*, che viene visualizzato in risposta alla selezione dell'annotazione da parte dell'utente. Il callout visualizza le proprietà `Label` e `Address` dell'istanza `Pin` con le visualizzazioni accessorio sinistro e destro facoltative. Nello screenshot precedente la visualizzazione accessorio sinistro è costituita dall'immagine di una chiave inglese mentre la visualizzazione accessorio destro è costituita dal pulsante *Informazioni*.

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma iOS:

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

            if (e.OldElement != null)
            {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null)
                {
                    nativeMap.RemoveAnnotations(nativeMap.Annotations);
                    nativeMap.GetViewForAnnotation = null;
                    nativeMap.CalloutAccessoryControlTapped -= OnCalloutAccessoryControlTapped;
                    nativeMap.DidSelectAnnotationView -= OnDidSelectAnnotationView;
                    nativeMap.DidDeselectAnnotationView -= OnDidDeselectAnnotationView;
                }
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                customPins = formsMap.CustomPins;

                nativeMap.GetViewForAnnotation = GetViewForAnnotation;
                nativeMap.CalloutAccessoryControlTapped += OnCalloutAccessoryControlTapped;
                nativeMap.DidSelectAnnotationView += OnDidSelectAnnotationView;
                nativeMap.DidDeselectAnnotationView += OnDidDeselectAnnotationView;
            }
        }
        // ...
    }
}
```

Il `OnElementChanged` metodo esegue la configurazione seguente dell' [`MKMapView`](xref:MapKit.MKMapView) istanza, purché il renderer personalizzato sia collegato a un nuovo elemento Novell. Forms:

- La [`GetViewForAnnotation`](xref:MapKit.MKMapView.GetViewForAnnotation*) proprietà è impostata sul `GetViewForAnnotation` metodo. Questo metodo viene chiamato quando la [posizione dell'annotazione diventa visibile nella mappa](#Displaying_the_Annotation) e viene usato per personalizzare l'annotazione prima della visualizzazione.
- I gestori per gli eventi `CalloutAccessoryControlTapped`, `DidSelectAnnotationView` e `DidDeselectAnnotationView` sono registrati. Questi eventi vengono attivati quando l'utente [tocca l'accessorio destro nel callout](#Tapping_on_the_Right_Callout_Accessory_View) e quando l'utente [seleziona](#Selecting_the_Annotation) e [deseleziona](#Deselecting_the_Annotation) l'annotazione. Gli eventi sono annullati solo quando viene modificato l'elemento a cui il renderer è associato.

<a name="Displaying_the_Annotation" />

#### <a name="displaying-the-annotation"></a>Visualizzazione dell'annotazione

Il metodo `GetViewForAnnotation` viene chiamato quando la posizione dell'annotazione diventa visibile nella mappa e viene usato per personalizzare l'annotazione prima della visualizzazione. Un'annotazione è costituita da due parti:

- `MkAnnotation`: include il titolo, il sottotitolo e la posizione dell'annotazione.
- `MkAnnotationView`: contiene l'immagine per rappresentare l'annotazione e, facoltativamente, un callout visualizzato quando l'utente tocca l'annotazione.

Il metodo `GetViewForAnnotation` accetta `IMKAnnotation` contenente i dati dell'annotazione e restituisce `MKAnnotationView` per la visualizzazione nella mappa, come illustrato nell'esempio di codice seguente:

```csharp
protected override MKAnnotationView GetViewForAnnotation(MKMapView mapView, IMKAnnotation annotation)
{
    MKAnnotationView annotationView = null;

    if (annotation is MKUserLocation)
        return null;

    var customPin = GetCustomPin(annotation as MKPointAnnotation);
    if (customPin == null)
    {
        throw new Exception("Custom pin not found");
    }

    annotationView = mapView.DequeueReusableAnnotation(customPin.Name);
    if (annotationView == null)
    {
        annotationView = new CustomMKAnnotationView(annotation, customPin.Name);
        annotationView.Image = UIImage.FromFile("pin.png");
        annotationView.CalloutOffset = new CGPoint(0, 0);
        annotationView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile("monkey.png"));
        annotationView.RightCalloutAccessoryView = UIButton.FromType(UIButtonType.DetailDisclosure);
        ((CustomMKAnnotationView)annotationView).Name = customPin.Name;
        ((CustomMKAnnotationView)annotationView).Url = customPin.Url;
    }
    annotationView.CanShowCallout = true;

    return annotationView;
}
```

Questo metodo assicura che l'annotazione venga visualizzata come immagine personalizzata anziché come segnaposto definito dal sistema e che quando viene toccata l'annotazione venga visualizzato un callout che contiene contenuti aggiuntivi a sinistra e a destra del titolo dell'annotazione e dell'indirizzo. Questo risultato viene ottenuto come segue:

1. Il metodo `GetCustomPin` viene chiamato per restituire i dati del segnaposto personalizzato per l'annotazione.
1. Per conservare la memoria, la visualizzazione dell'annotazione viene contenuta in un pool per [`DequeueReusableAnnotation`](xref:MapKit.MKMapView.DequeueReusableAnnotation*)riutilizzarla con la chiamata a.
1. La classe `CustomMKAnnotationView` estende la classe `MKAnnotationView` con le proprietà `Name` e `Url` che corrispondono alle stesse proprietà nell'istanza `CustomPin`. Viene creata una nuova istanza di `CustomMKAnnotationView`, a condizione che l'annotazione sia `null`:
    - La proprietà `CustomMKAnnotationView.Image` viene impostata sull'immagine che rappresenterà l'annotazione nella mappa.
    - La proprietà `CustomMKAnnotationView.CalloutOffset` è impostata su `CGPoint` che specifica che il callout verrà centrato sopra l'annotazione.
    - La proprietà `CustomMKAnnotationView.LeftCalloutAccessoryView` è impostata su un'immagine di una chiave inglese che verrà visualizzata a sinistra del titolo dell'annotazione e dell'indirizzo.
    - La proprietà `CustomMKAnnotationView.RightCalloutAccessoryView` è impostata su un pulsante *Informazioni* che verrà visualizzato a destra del titolo dell'annotazione e dell'indirizzo.
    - La proprietà `CustomMKAnnotationView.Name` è impostata sulla proprietà `CustomPin.Name` restituita dal metodo `GetCustomPin`. Ciò consente l'identificazione dell'annotazione in modo che sia possibile [personalizzare ulteriormente il relativo callout](#Selecting_the_Annotation), se necessario.
    - La proprietà `CustomMKAnnotationView.Url` è impostata sulla proprietà `CustomPin.Url` restituita dal metodo `GetCustomPin`. L'URL verrà aperto quando l'utente [tocca il pulsante visualizzato nella visualizzazione accessorio callout destra](#Tapping_on_the_Right_Callout_Accessory_View).
1. La [`MKAnnotationView.CanShowCallout`](xref:MapKit.MKAnnotationView.CanShowCallout*) proprietà è impostata su `true` in modo che il callout venga visualizzato quando viene toccata l'annotazione.
1. L'annotazione viene restituita per la visualizzazione nella mappa.

<a name="Selecting_the_Annotation" />

#### <a name="selecting-the-annotation"></a>Selezione dell'annotazione

Quando l'utente tocca l'annotazione, viene attivato l'evento `DidSelectAnnotationView` che a sua volta esegue il metodo `OnDidSelectAnnotationView`:

```csharp
void OnDidSelectAnnotationView(object sender, MKAnnotationViewEventArgs e)
{
    CustomMKAnnotationView customView = e.View as CustomMKAnnotationView;
    customPinView = new UIView();

    if (customView.Name.Equals("Xamarin"))
    {
        customPinView.Frame = new CGRect(0, 0, 200, 84);
        var image = new UIImageView(new CGRect(0, 0, 200, 84));
        image.Image = UIImage.FromFile("xamarin.png");
        customPinView.AddSubview(image);
        customPinView.Center = new CGPoint(0, -(e.View.Frame.Height + 75));
        e.View.AddSubview(customPinView);
    }
}
```

Questo metodo estende il callout esistente (contenente le visualizzazioni accessorio destra e sinistra) aggiungendo un'istanza `UIView` che contiene un'immagine del logo Xamarin, a condizione che la proprietà `Name` dell'annotazione selezionata sia impostata su `Xamarin`. Ciò rende possibili scenari in cui vengono visualizzati callout diversi per le diverse annotazioni. L'istanza `UIView` verrà visualizzata al centro sopra il callout esistente.

<a name="Tapping_on_the_Right_Callout_Accessory_View" />

#### <a name="tapping-on-the-right-callout-accessory-view"></a>Tocco sulla visualizzazione accessorio callout destra

Quando l'utente tocca il pulsante *Informazioni* nella visualizzazione accessorio callout destra, viene attivato l'evento `CalloutAccessoryControlTapped` che a sua volta esegue il metodo `OnCalloutAccessoryControlTapped`:

```csharp
void OnCalloutAccessoryControlTapped(object sender, MKMapViewAccessoryTappedEventArgs e)
{
    CustomMKAnnotationView customView = e.View as CustomMKAnnotationView;
    if (!string.IsNullOrWhiteSpace(customView.Url))
    {
        UIApplication.SharedApplication.OpenUrl(new Foundation.NSUrl(customView.Url));
    }
}
```

Questo metodo apre un Web browser e passa all'indirizzo memorizzato nella proprietà `CustomMKAnnotationView.Url`. Si noti che l'indirizzo è stato definito durante la creazione della raccolta `CustomPin` nel progetto della libreria .NET Standard.

<a name="Deselecting_the_Annotation" />

#### <a name="deselecting-the-annotation"></a>Deselezione dell'annotazione

Quando viene visualizzata l'annotazione e l'utente tocca la mappa, viene attivato l'evento `DidDeselectAnnotationView` che a sua volta esegue il metodo`OnDidDeselectAnnotationView`:

```csharp
void OnDidDeselectAnnotationView(object sender, MKAnnotationViewEventArgs e)
{
    if (!e.View.Selected)
    {
        customPinView.RemoveFromSuperview();
        customPinView.Dispose();
        customPinView = null;
    }
}
```

Questo metodo garantisce che quando il callout esistente non è selezionato venga interrotta anche la visualizzazione della parte estesa del callout (immagine del logo Xamarin) e vengano liberate le relative risorse.

Per altre informazioni sulla personalizzazione di un'istanza `MKMapView`, vedere [Mappe iOS](~/ios/user-interface/controls/ios-maps/index.md).

### <a name="creating-the-custom-renderer-on-android"></a>Creazione del renderer personalizzato in Android

Gli screenshot seguenti mostrano la mappa prima e dopo la personalizzazione:

![](map-pin-images/map-layout-android.png "Map Control Before and After Customization")

In Android il segnaposto viene chiamato *indicatore* e può essere un'immagine personalizzata o un indicatore di vari colori definito dal sistema. Gli indicatori possono visualizzare una *finestra informazioni* visualizzata quando l'utente tocca l'indicatore. La finestra informazioni visualizza le proprietà `Label` e `Address` dell'istanza `Pin` e può essere personalizzata per includere altri contenuti. Tuttavia, è possibile visualizzare una sola finestra informazioni alla volta.

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma Android:

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

Se il renderer personalizzato è associato a un nuovo elemento Novell. Forms, il `OnElementChanged` metodo recupera l'elenco dei pin personalizzati dal controllo. Quando l'istanza `GoogleMap` è disponibile, viene richiamato l'override `OnMapReady`. Questo metodo registra un gestore per l'evento `InfoWindowClick` che viene attivato quando [viene fatto clic sulla finestra informazioni](#Clicking_on_the_Info_Window) e viene annullato solo quando viene modificato l'elemento a cui è associato il renderer. L'override `OnMapReady` chiama anche il metodo `SetInfoWindowAdapter` per specificare che l'istanza della classe `CustomMapRenderer` offrirà i metodi per personalizzare la finestra informazioni.

La classe `CustomMapRenderer` implementa l'interfaccia `GoogleMap.IInfoWindowAdapter` per [personalizzare la finestra informazioni](#Customizing_the_Info_Window). Questa interfaccia consente di specificare l'implementazione dei metodi seguenti:

- `public Android.Views.View GetInfoWindow(Marker marker)`: questo metodo viene chiamato per restituire una finestra informazioni personalizzata per un indicatore. Se restituisce `null`, verrà usato il rendering della finestra predefinito. Se restituisce `View`, `View` verrà inserito all'interno della finestra informazioni.
- `public Android.Views.View GetInfoContents(Marker marker)`: questo metodo viene chiamato per restituire `View` con il contenuto della finestra informazioni e viene chiamato solo se il metodo `GetInfoWindow` restituisce `null`. Se restituisce `null`, verrà usato il rendering predefinito del contenuto della finestra informazioni.

Nell'applicazione di esempio poiché viene personalizzato soltanto il contenuto della finestra informazioni, il metodo `GetInfoWindow` restituisce `null` per abilitare questa opzione.

#### <a name="customizing-the-marker"></a>Personalizzazione dell'indicatore

È possibile personalizzare l'icona che rappresenta l'indicatore chiamando il metodo `MarkerOptions.SetIcon`. L'operazione può essere eseguita eseguendo l'override del metodo `CreateMarker` che viene richiamato per ogni `Pin` aggiunto alla mappa:

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

Questo metodo crea una nuova istanza `MarkerOption` per ogni istanza `Pin`. Dopo aver impostato la posizione, l'etichetta e l'indirizzo dell'indicatore, l'icona viene impostata con il metodo `SetIcon`. Questo metodo accetta un oggetto `BitmapDescriptor` contenente i dati necessari per eseguire il rendering dell'icona, con la classe `BitmapDescriptorFactory` che specifica i metodo dell'helper per semplificare la creazione di `BitmapDescriptor`. Per altre informazioni sull'uso della classe `BitmapDescriptorFactory` per personalizzare un indicatore, vedere [Customizing a Marker](~/android/platform/maps-and-location/maps/maps-api.md) (Personalizzazione di un indicatore).

> [!NOTE]
> Se necessario, il metodo `GetMarkerForPin` può essere richiamato nel renderer della mappa per recuperare `Marker` da `Pin`.

<a name="Customizing_the_Info_Window" />

#### <a name="customizing-the-info-window"></a>Personalizzazione della finestra informazioni

Quando un utente tocca l'indicatore, viene eseguito il metodo `GetInfoContents`, a condizione che il metodo `GetInfoWindow` restituisca `null`. L'esempio di codice seguente illustra il metodo `GetInfoContents`:

```csharp
public Android.Views.View GetInfoContents(Marker marker)
{
    var inflater = Android.App.Application.Context.GetSystemService(Context.LayoutInflaterService) as Android.Views.LayoutInflater;
    if (inflater != null)
    {
        Android.Views.View view;

        var customPin = GetCustomPin(marker);
        if (customPin == null)
        {
            throw new Exception("Custom pin not found");
        }

        if (customPin.Name.Equals("Xamarin"))
        {
            view = inflater.Inflate(Resource.Layout.XamarinMapInfoWindow, null);
        }
        else
        {
            view = inflater.Inflate(Resource.Layout.MapInfoWindow, null);
        }

        var infoTitle = view.FindViewById<TextView>(Resource.Id.InfoWindowTitle);
        var infoSubtitle = view.FindViewById<TextView>(Resource.Id.InfoWindowSubtitle);

        if (infoTitle != null)
        {
            infoTitle.Text = marker.Title;
        }
        if (infoSubtitle != null)
        {
            infoSubtitle.Text = marker.Snippet;
        }

        return view;
    }
    return null;
}
```

Questo metodo restituisce `View` con il contenuto della finestra informazioni. Questo risultato viene ottenuto come segue:

- Viene recuperata un'istanza `LayoutInflater` che viene usata per creare l'istanza di un file XML di layout nel valore `View` corrispondente.
- Il metodo `GetCustomPin` viene chiamato per restituire i dati del segnaposto personalizzato per la finestra informazioni.
- Il layout `XamarinMapInfoWindow` viene espanso se la proprietà `CustomPin.Name` è uguale a `Xamarin`. In caso contrario, viene espanso il layout `MapInfoWindow`. Ciò rende possibili scenari in cui possono essere visualizzati layout diversi della finestra informazioni per i diversi indicatori.
- Le risorse `InfoWindowTitle` e `InfoWindowSubtitle` vengono recuperate dal layout espanso e le relative proprietà `Text` vengono impostate sui dati corrispondenti dell'istanza `Marker`, a condizione che le risorse non siano `null`.
- L'istanza `View` viene restituita per la visualizzazione nella mappa.

> [!NOTE]
> Una finestra informazioni non è una `View` attiva. Al contrario, Android convertirà la `View` in una bitmap statica e la visualizzerà come immagine. Ciò significa che sebbene possa rispondere a un evento clic, la finestra informazioni non può rispondere agli eventi tocco, mentre i singoli controlli della finestra informazioni non possono rispondere ai relativi eventi clic.

<a name="Clicking_on_the_Info_Window" />

#### <a name="clicking-on-the-info-window"></a>Clic nella finestra informazioni

Quando l'utente fa clic nella finestra informazioni, viene attivato l'evento `InfoWindowClick` che a sua volta esegue il metodo `OnInfoWindowClick`:

```csharp
void OnInfoWindowClick(object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    var customPin = GetCustomPin(e.Marker);
    if (customPin == null)
    {
        throw new Exception("Custom pin not found");
    }

    if (!string.IsNullOrWhiteSpace(customPin.Url))
    {
        var url = Android.Net.Uri.Parse(customPin.Url);
        var intent = new Intent(Intent.ActionView, url);
        intent.AddFlags(ActivityFlags.NewTask);
        Android.App.Application.Context.StartActivity(intent);
    }
}
```

Questo metodo apre un Web browser e passa all'indirizzo memorizzato nella proprietà `Url` dell'istanza `CustomPin` recuperata per `Marker`. Si noti che l'indirizzo è stato definito durante la creazione della raccolta `CustomPin` nel progetto della libreria .NET Standard.

Per altre informazioni sulla personalizzazione di un'istanza `MapView`, vedere [API Maps](~/android/platform/maps-and-location/maps/maps-api.md).

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Creare il renderer personalizzato sulla piattaforma UWP (Universal Windows Platform)

Gli screenshot seguenti mostrano la mappa prima e dopo la personalizzazione:

![](map-pin-images/map-layout-uwp.png "Map Control Before and After Customization")

Nella piattaforma UWP il segnaposto viene chiamato *icona della mappa* e può essere un'immagine personalizzata o immagine predefinita definita dal sistema. Un'icona della mappa può visualizzare `UserControl`, visualizzato quando l'utente tocca l'icona della mappa. `UserControl` può visualizzare qualsiasi contenuto, incluse le proprietà `Label` e `Address` dell'istanza `Pin`.

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma UWP:

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

Il metodo `OnElementChanged` esegue le operazioni seguenti, a condizione che il renderer personalizzato sia associato a un nuovo elemento di Xamarin.Forms:

- Cancella la raccolta `MapControl.Children` per rimuovere tutti gli elementi dell'interfaccia utente esistenti dalla mappa prima di registrare un gestore dell'evento per l'evento `MapElementClick`. Questo evento viene attivato quando l'utente tocca o fa clic su `MapElement` in `MapControl` e viene annullato solo quando viene modificato l'elemento a cui è associato il renderer.
- Ogni segnaposto della raccolta `customPins` viene visualizzato nella posizione geografica corretta nella mappa come indicato di seguito:
  - Il percorso del segnaposto viene creato come istanza `Geopoint`.
  - Viene creata un'istanza `MapIcon` per rappresentare il segnaposto.
  - L'immagine usata per rappresentare `MapIcon` viene specificata impostando la proprietà `MapIcon.Image`. Tuttavia, l'immagine dell'icona della mappa non viene visualizzata sempre poiché può essere nascosta da altri elementi nella mappa. Pertanto, la proprietà `CollisionBehaviorDesired` dell'icona della mappa è impostata su `MapElementCollisionBehavior.RemainVisible` per garantire che rimanga visibile.
  - La posizione di `MapIcon` viene specificata impostando la proprietà `MapIcon.Location`.
  - La proprietà `MapIcon.NormalizedAnchorPoint` è impostata su una posizione approssimativa del puntatore nell'immagine. Se questa proprietà mantiene il valore predefinito (0,0), che rappresenta l'angolo superiore sinistro dell'immagine, le modifiche al livello di zoom della mappa potrebbero fare in modo che l'immagine punti a una posizione diversa.
  - L'istanza `MapIcon` viene aggiunta alla raccolta `MapControl.MapElements`. Il risultato è la visualizzazione dell'icona della mappa in `MapControl`.

> [!NOTE]
> Quando viene usata la stessa immagine per più icone della mappa, per ottenere le migliori prestazioni è necessario dichiarare l'istanza `RandomAccessStreamReference` a livello di pagina o applicazione.

#### <a name="displaying-the-usercontrol"></a>Visualizzazione di UserControl

Quando un utente tocca l'icona della mappa, viene eseguito il metodo `OnMapElementClick`. L'esempio di codice seguente illustra il metodo:

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

            if (customPin.Name.Equals("Xamarin"))
            {
                if (mapOverlay == null)
                {
                    mapOverlay = new XamarinMapOverlay(customPin);
                }

                var snPosition = new BasicGeoposition { Latitude = customPin.Position.Latitude, Longitude = customPin.Position.Longitude };
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

Questo metodo crea un'istanza `UserControl` che visualizza informazioni sul segnaposto. Questo risultato viene ottenuto come segue:

- Viene recuperata l'istanza `MapIcon`.
- Il metodo `GetCustomPin` viene chiamato per restituire i dati del segnaposto personalizzato da visualizzare.
- L'istanza `XamarinMapOverlay` viene creata per visualizzare i dati del segnaposto personalizzato. Questa classe è un controllo utente.
- La posizione geografica in cui visualizzare l'istanza `XamarinMapOverlay` in `MapControl` viene creata come istanza `Geopoint`.
- L'istanza `XamarinMapOverlay` viene aggiunta alla raccolta `MapControl.Children`. Questa raccolta contiene gli elementi dell'interfaccia utente XAML che verranno visualizzati nella mappa.
- La posizione geografica dell'istanza `XamarinMapOverlay` nella mappa viene impostata tramite la chiamata al metodo `SetLocation`.
- La posizione relativa nell'istanza `XamarinMapOverlay`, che corrisponde alla posizione specificata, viene impostata tramite la chiamata al metodo `SetNormalizedAnchorPoint`. In questo modo si garantisce che nonostante le modifiche apportate al livello di zoom della mappa, l'istanza `XamarinMapOverlay` venga sempre visualizzata nella posizione corretta.

In alternativa, se le informazioni sul segnaposto sono già visualizzate nella mappa, un tocco nella mappa rimuove l'istanza `XamarinMapOverlay` dalla raccolta `MapControl.Children`.

#### <a name="tapping-on-the-information-button"></a>Tocco del pulsante Informazioni

Quando l'utente tocca il pulsante *Informazioni* nel controllo utente `XamarinMapOverlay`, viene attivato l'evento `Tapped` che a sua volta esegue il metodo `OnInfoButtonTapped`:

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

Questo metodo apre un Web browser e passa all'indirizzo memorizzato nella proprietà `Url` dell'istanza `CustomPin`. Si noti che l'indirizzo è stato definito durante la creazione della raccolta `CustomPin` nel progetto della libreria .NET Standard.

Per altre informazioni sulla personalizzazione di un'istanza `MapControl`, vedere [Panoramica su mappe e posizione](https://msdn.microsoft.com/library/windows/apps/mt219699.aspx) in MSDN.

## <a name="related-links"></a>Collegamenti correlati

- [Controllo Maps](~/xamarin-forms/user-interface/map/index.md)
- [Mappe iOS](~/ios/user-interface/controls/ios-maps/index.md)
- [API Maps](~/android/platform/maps-and-location/maps/maps-api.md)
- [Segnaposto personalizzato (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-pin)
