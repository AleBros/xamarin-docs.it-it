---
title: Mappe in Xamarin.iOS
description: Questo documento descrive il Framework MapKit iOS e il modo in cui viene usato con Xamarin.iOS. Viene illustrato come aggiungere una mappa, applicarvi uno stile, una panoramica e uno zoom, utilizzare la posizione dell'utente, aggiungere annotazioni, utilizzare callout e sovrapposizioni ed eseguire ricerche locali.
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 3eb50c97521d11944e6d549018e057416b9dc2b2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022037"
---
# <a name="maps-in-xamarinios"></a>Mappe in Xamarin.iOS

Le mappe sono una funzionalità comune in tutti i moderni sistemi operativi mobili. iOS offre supporto per il mapping in modo nativo tramite il Framework del kit di mappe. Con Map Kit, le applicazioni possono aggiungere facilmente mappe interattive e complesse. Queste mappe possono essere personalizzate in diversi modi, ad esempio aggiungendo annotazioni per contrassegnare le posizioni su una mappa e sovrapponendo la grafica di forme arbitrarie. Map Kit include anche il supporto predefinito per visualizzare la posizione corrente di un dispositivo.

## <a name="adding-a-map"></a>Aggiunta di una mappa

L'aggiunta di una mappa a un'applicazione viene eseguita aggiungendo un'istanza di `MKMapView` alla gerarchia di visualizzazione, come illustrato di seguito:

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

`MKMapView` è una sottoclasse `UIView` che visualizza una mappa. La semplice aggiunta della mappa usando il codice precedente produce una mappa interattiva:

![](images/00-map.png "A sample map")

## <a name="map-style"></a>Stile mappa

`MKMapView` supporta 3 stili diversi di Maps. Per applicare uno stile mappa, è sufficiente impostare la proprietà `MapType` su un valore dell'enumerazione `MKMapType`:

```csharp
map.MapType = MKMapType.Standard; //road map
map.MapType = MKMapType.Satellite;
map.MapType = MKMapType.Hybrid;
```

Lo screenshot seguente mostra i diversi stili di mappa disponibili:

![](images/01-mapstyles.png "This screenshot show the different map styles that are available")

## <a name="panning-and-zooming"></a>Panoramica e zoom

`MKMapView` include il supporto per le funzionalità di interattività mappa, ad esempio:

- Zoom tramite un movimento di pizzico
- Panoramica tramite un movimento di Pan

Queste funzionalità possono essere abilitate o disabilitate semplicemente impostando le proprietà `ZoomEnabled` e `ScrollEnabled` dell'istanza `MKMapView`, in cui il valore predefinito è true per entrambi. Per visualizzare, ad esempio, una mappa statica, è sufficiente impostare le proprietà appropriate su false:

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>Percorso utente

Oltre all'interazione dell'utente, `MKMapView` dispone anche del supporto incorporato per la visualizzazione del percorso del dispositivo. Questa operazione viene eseguita usando il Framework del *percorso di base* . Prima di poter accedere alla posizione dell'utente, è necessario richiedere conferma all'utente. A tale scopo, creare un'istanza di `CLLocationManager` e chiamare `RequestWhenInUseAuthorization`.

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

Si noti che nelle versioni di iOS precedenti alla 8,0, il tentativo di chiamare `RequestWhenInUseAuthorization` genererà un errore. Assicurarsi di controllare la versione di iOS prima di effettuare la chiamata se si intende supportare versioni precedenti a 8.

L'accesso alla posizione dell'utente richiede anche modifiche a **info. plist**. È necessario impostare le chiavi seguenti relative ai dati del percorso:

- **NSLocationWhenInUseUsageDescription**: per l'accesso al percorso dell'utente quando interagisce con l'app.
- **NSLocationAlwaysUsageDescription**: per l'accesso dell'app al percorso dell'utente in background.

È possibile aggiungere queste chiavi aprendo **info. plist** e selezionando *source* nella parte inferiore dell'editor.

Dopo aver aggiornato **info. plist** e aver richiesto all'utente l'autorizzazione per accedere alla propria posizione, è possibile visualizzare la posizione dell'utente sulla mappa impostando la proprietà `ShowsUserLocation` su true:

```csharp
map.ShowsUserLocation = true;
```

 ![](images/02-location-alert.png "The allow location access alert")

## <a name="annotations"></a>Annotazioni

 `MKMapView` supporta anche la visualizzazione di immagini, note come annotazioni, su una mappa. Possono essere immagini personalizzate o pin definiti dal sistema di diversi colori. Ad esempio, nella schermata seguente viene illustrata una mappa con un PIN e un'immagine personalizzata:

 ![](images/03-annotations.png "This screenshot shows a map with a both a pin and a custom image")

### <a name="adding-an-annotation"></a>Aggiunta di un'annotazione

Un'annotazione è costituita da due parti:

- Oggetto `MKAnnotation`, che include i dati del modello sull'annotazione, ad esempio il titolo e la posizione dell'annotazione.
- Il `MKAnnotationView`, che contiene l'immagine da visualizzare e, facoltativamente, un callout visualizzato quando l'utente tocca l'annotazione.

Map Kit usa il modello di delega iOS per aggiungere annotazioni a una mappa, in cui la proprietà `Delegate` del `MKMapView` è impostata su un'istanza di un `MKMapViewDelegate`. Si tratta dell'implementazione di questo delegato responsabile della restituzione del `MKAnnotationView` di un'annotazione.

Per aggiungere un'annotazione, prima viene aggiunta l'annotazione chiamando `AddAnnotations` sull'istanza `MKMapView`:

```csharp
// add an annotation
map.AddAnnotations (new MKPointAnnotation (){
    Title="MyAnnotation",
    Coordinate = new CLLocationCoordinate2D (42.364260, -71.120824)
});
```

Quando la posizione dell'annotazione diventa visibile sulla mappa, il `MKMapView` chiamerà il metodo `GetViewForAnnotation` del delegato per ottenere la `MKAnnotationView` da visualizzare.

Il codice seguente, ad esempio, restituisce un `MKPinAnnotationView`fornito dal sistema:

```csharp
string pId = "PinAnnotation";

public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
{
    if (annotation is MKUserLocation)
        return null;

    // create pin annotation view
    MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);

    if (pinView == null)
        pinView = new MKPinAnnotationView (annotation, pId);

    ((MKPinAnnotationView)pinView).PinColor = MKPinAnnotationColor.Red;
    pinView.CanShowCallout = true;

    return pinView;
}
```

### <a name="reusing-annotations"></a>Riutilizzo di annotazioni

Per conservare la memoria, `MKMapView` consente il pool della visualizzazione annotazione per il riutilizzo, in modo analogo al modo in cui vengono riutilizzate le celle della tabella. Il recupero di una visualizzazione annotazione dal pool viene effettuato con una chiamata a `DequeueReusableAnnotation`:

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>Visualizzazione di callout

Come indicato in precedenza, un'annotazione può facoltativamente visualizzare un callout. Per visualizzare un callout è sufficiente impostare `CanShowCallout` su true nel `MKAnnotationView`. Ciò comporta la visualizzazione del titolo dell'annotazione quando l'annotazione viene toccata, come illustrato:

 ![](images/04-callout.png "The annotations title being displayed")

### <a name="customizing-the-callout"></a>Personalizzazione del callout

Il callout può anche essere personalizzato per mostrare le visualizzazioni accessorie a sinistra e a destra, come illustrato di seguito:

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

Questo codice genera il seguente callout:

 ![](images/05-callout-accessories.png "An example callout")

Per gestire l'utente che tocca l'accessorio corretto, è sufficiente implementare il metodo `CalloutAccessoryControlTapped` nel `MKMapViewDelegate`:

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>Le sovrimpressioni

Un altro modo per eseguire il livello di grafica su una mappa consiste nell'usare sovrimpressioni. Le sovrimpressioni supportano contenuto grafico di tipo disegno che viene ridimensionato con la mappa quando si esegue lo zoom. iOS fornisce supporto per diversi tipi di sovrimpressione, tra cui:

- Poligoni: usati comunemente per evidenziare alcune aree su una mappa.
- Polilinee: spesso visualizzate quando si mostra una route.
- Cerchi, usati per evidenziare un'area circolare di una mappa.

Inoltre, è possibile creare sovrapposizioni personalizzate per mostrare geometrie arbitrarie con codice di disegno granulare e personalizzato. Ad esempio, il radar meteorologico è un buon candidato per una sovrapposizione personalizzata.

#### <a name="adding-an-overlay"></a>Aggiunta di una sovrapposizione

Analogamente alle annotazioni, l'aggiunta di una sovrapposizione implica due parti:

- Creazione di un oggetto modello per la sovrimpressione e aggiunta al `MKMapView`.
- Creazione di una vista per la sovrimpressione nella `MKMapViewDelegate`.

Il modello per la sovrimpressione può essere qualsiasi `MKShape` sottoclasse. Xamarin.iOS include `MKShape` sottoclassi per poligoni, polilinee e cerchi, rispettivamente tramite le classi `MKPolygon`, `MKPolyline` e `MKCircle`.

Il codice seguente, ad esempio, viene usato per aggiungere un `MKCircle`:

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

La visualizzazione di una sovrapposizione è un'istanza di `MKOverlayView` restituita dal `GetViewForOverlay` nel `MKMapViewDelegate`. Ogni `MKShape` dispone di un `MKOverlayView` corrispondente che sa come visualizzare la forma specificata. Per `MKPolygon` è `MKPolygonView`. Analogamente, `MKPolyline` corrisponde a `MKPolylineView`e per `MKCircle` è presente `MKCircleView`.

Il codice seguente, ad esempio, restituisce un `MKCircleView` per un `MKCircle`:

```csharp
public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
{
    var circleOverlay = overlay as MKCircle;
    var circleView = new MKCircleView (circleOverlay);
    circleView.FillColor = UIColor.Blue;
    return circleView;
}
```

Viene visualizzato un cerchio sulla mappa come illustrato:

 ![](images/06-circle-overlay.png "A circle displayed on the map")

## <a name="local-search"></a>Ricerca locale

iOS include un'API di ricerca locale con Map Kit, che consente la ricerca asincrona dei punti di interesse in un'area geografica specifica.

Per eseguire una ricerca locale, un'applicazione deve attenersi alla seguente procedura:

1. Creare `MKLocalSearchRequest` oggetto.
1. Creare un oggetto `MKLocalSearch` dalla `MKLocalSearchRequest`.
1. Chiamare il metodo `Start` sull'oggetto `MKLocalSearch`.
1. Recuperare l'oggetto `MKLocalSearchResponse` in un callback.

L'API di ricerca locale non fornisce alcuna interfaccia utente. Non richiede neanche una mappa da usare. Tuttavia, per utilizzare in modo pratico la ricerca locale, un'applicazione deve fornire un modo per specificare una query di ricerca e visualizzare i risultati. Inoltre, poiché i risultati conterranno i dati relativi alla posizione, sarà spesso utile visualizzarli su una mappa.

<a name="Adding_a_Local_Search_UI"/>

### <a name="adding-a-local-search-ui"></a>Aggiunta di un'interfaccia utente di ricerca locale

Un modo per accettare l'input di ricerca è con una `UISearchBar`, fornita da un `UISearchController` e visualizzerà i risultati in una tabella.

Il codice seguente aggiunge il `UISearchController` (che dispone di una proprietà della barra di ricerca) nel metodo `ViewDidLoad` di `MapViewController`:

```csharp
//Creates an instance of a custom View Controller that holds the results
var searchResultsController = new SearchResultsViewController (map);

//Creates a search controller updater
var searchUpdater = new SearchResultsUpdator ();
searchUpdater.UpdateSearchResults += searchResultsController.Search;

//add the search controller
searchController = new UISearchController (searchResultsController) {
                SearchResultsUpdater = searchUpdater
            };

//format the search bar
searchController.SearchBar.SizeToFit ();
searchController.SearchBar.SearchBarStyle = UISearchBarStyle.Minimal;
searchController.SearchBar.Placeholder = "Enter a search query";

//the search bar is contained in the navigation bar, so it should be visible
searchController.HidesNavigationBarDuringPresentation = false;

//Ensure the searchResultsController is presented in the current View Controller
DefinesPresentationContext = true;

//Set the search bar in the navigation bar
NavigationItem.TitleView = searchController.SearchBar;
```

Si noti che l'utente è responsabile dell'incorporamento dell'oggetto della barra di ricerca nell'interfaccia utente. In questo esempio, l'assegnazione è stata assegnata al TitleView della barra di spostamento, ma se non si utilizza un controller di spostamento nell'applicazione, sarà necessario trovare un'altra posizione per visualizzarla.

In questo frammento di codice è stato creato un altro controller di visualizzazione personalizzato, `searchResultsController`, che Visualizza i risultati della ricerca, quindi è stato usato questo oggetto per creare l'oggetto controller di ricerca. È stato anche creato un nuovo aggiornamento di ricerca, che diventa attivo quando l'utente interagisce con la barra di ricerca. Riceve notifiche sulle ricerche con ogni pressione di tasto ed è responsabile dell'aggiornamento dell'interfaccia utente.
Si esaminerà come implementare sia la `searchResultsController` che la `searchResultsUpdater` più avanti in questa guida.

Ciò comporta la visualizzazione di una barra di ricerca sulla mappa, come illustrato di seguito:

 ![](images/07-searchbar.png "A search bar displayed over the map")

### <a name="displaying-the-search-results"></a>Visualizzazione dei risultati della ricerca

Per visualizzare i risultati della ricerca, è necessario creare un controller di visualizzazione personalizzato. in genere un `UITableViewController`. Come illustrato in precedenza, il `searchResultsController` viene passato al costruttore del `searchController` quando viene creato.
Il codice seguente è un esempio di come creare il controller di visualizzazione personalizzato:

```csharp
public class SearchResultsViewController : UITableViewController
{
    static readonly string mapItemCellId = "mapItemCellId";
    MKMapView map;

    public List<MKMapItem> MapItems { get; set; }

    public SearchResultsViewController (MKMapView map)
    {
        this.map = map;

        MapItems = new List<MKMapItem> ();
    }

    public override nint RowsInSection (UITableView tableView, nint section)
    {
        return MapItems.Count;
    }

    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        var cell = tableView.DequeueReusableCell (mapItemCellId);

        if (cell == null)
            cell = new UITableViewCell ();

        cell.TextLabel.Text = MapItems [indexPath.Row].Name;
        return cell;
    }

    public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
    {
        // add item to map
        CLLocationCoordinate2D coord = MapItems [indexPath.Row].Placemark.Location.Coordinate;
        map.AddAnnotations (new MKPointAnnotation () {
            Title = MapItems [indexPath.Row].Name,
            Coordinate = coord
        });

        map.SetCenterCoordinate (coord, true);

        DismissViewController (false, null);
    }

    public void Search (string forSearchString)
    {
        // create search request
        var searchRequest = new MKLocalSearchRequest ();
        searchRequest.NaturalLanguageQuery = forSearchString;
        searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

        // perform search
        var localSearch = new MKLocalSearch (searchRequest);

        localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
            if (response != null && error == null) {
                this.MapItems = response.MapItems.ToList ();
                this.TableView.ReloadData ();
            } else {
                Console.WriteLine ("local search error: {0}", error);
            }
        });

    }
}
```

### <a name="updating-the-search-results"></a>Aggiornamento dei risultati della ricerca

Il `SearchResultsUpdater` funge da Mediator tra la barra di ricerca `searchController`e i risultati della ricerca.

In questo esempio è necessario creare innanzitutto il metodo di ricerca nell'`SearchResultsViewController`. A tale scopo, è necessario creare un oggetto `MKLocalSearch` e usarlo per eseguire una ricerca di un `MKLocalSearchRequest`, i risultati vengono recuperati in un callback passato al metodo `Start` dell'oggetto `MKLocalSearch`. I risultati vengono quindi restituiti in un oggetto `MKLocalSearchResponse` contenente una matrice di oggetti `MKMapItem`:

```csharp
public void Search (string forSearchString)
{
    // create search request
    var searchRequest = new MKLocalSearchRequest ();
    searchRequest.NaturalLanguageQuery = forSearchString;
    searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

    // perform search
    var localSearch = new MKLocalSearch (searchRequest);

    localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
        if (response != null && error == null) {
            this.MapItems = response.MapItems.ToList ();
            this.TableView.ReloadData ();
        } else {
            Console.WriteLine ("local search error: {0}", error);
        }
    });

}
```

Quindi, nella `MapViewController` verrà creata un'implementazione personalizzata di `UISearchResultsUpdating`, assegnata alla proprietà `SearchResultsUpdater` del `searchController` nella sezione [aggiunta di un'interfaccia utente di ricerca locale](#Adding_a_Local_Search_UI) :

```csharp
public class SearchResultsUpdator : UISearchResultsUpdating
{
    public event Action<string> UpdateSearchResults = delegate {};

    public override void UpdateSearchResultsForSearchController (UISearchController searchController)
    {
        this.UpdateSearchResults (searchController.SearchBar.Text);
    }
}
```

L'implementazione precedente aggiunge un'annotazione alla mappa quando viene selezionato un elemento dai risultati, come illustrato di seguito:

 ![](images/08-search-results.png "An annotation added to the map when an item is selected from the results")

> [!IMPORTANT]
> `UISearchController` è stato implementato in iOS 8. Se si vuole supportare i dispositivi prima di questo, sarà necessario usare `UISearchDisplayController`.

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato il Framework del *Kit* di *mappe* per iOS. In primo luogo, è stato esaminato il modo in cui la classe `MKMapView` consente l'inclusione di mappe interattive in un'applicazione. È stato quindi illustrato come personalizzare ulteriormente le mappe utilizzando le annotazioni e le sovrimpressioni. Infine, sono state esaminate le funzionalità di ricerca locali che sono state aggiunte a Map Kit con iOS 6,1, mostrando come utilizzare eseguire query location based per i punti di interesse e aggiungerle a una mappa.

## <a name="related-links"></a>Collegamenti correlati

- [SearchController](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
- [MapDemo (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/mapdemo)
