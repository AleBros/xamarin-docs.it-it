---
title: Mappe
description: "iOS sono inclusi il MapKit framework, che consente di aggiungere facilmente mappato a un'applicazione. Utilizzando il Kit di mappa, le applicazioni iOS possono aggiungere mappe interattive che supportano funzionalità quali la panoramica e zoom, che mostrano il percorso di utente e grafica dei livelli in una mappa. In questo articolo affronta molte delle funzionalità del Kit di mappa, che illustra come sfruttare i vantaggi di essi per creare funzionalità geografica in un'applicazione"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 1665168344a1e1da4f5b07909a8119d77ef2583a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="maps"></a>Mappe

_iOS sono inclusi il MapKit framework, che consente di aggiungere facilmente mappato a un'applicazione. Utilizzando il Kit di mappa, le applicazioni iOS possono aggiungere mappe interattive che supportano funzionalità quali la panoramica e zoom, che mostrano il percorso di utente e grafica dei livelli in una mappa. In questo articolo affronta molte delle funzionalità del Kit di mappa, che illustra come sfruttare i vantaggi di essi per creare funzionalità geografica in un'applicazione_

Le mappe sono una funzionalità comune in tutti i sistemi operativi moderni dispositivi mobili. iOS offre il supporto di mapping in modo nativo tramite il framework del Kit di mappa. Kit di mappa, le applicazioni possono aggiungere facilmente mappe dettagliate e interattive. Queste mappe possono essere personalizzate in diversi modi, ad esempio l'aggiunta di annotazioni per contrassegnare i percorsi su una mappa e la sovrapposizione di elementi grafici di impatto su forme arbitrarie. Kit di mappa ha anche il supporto incorporato per mostrare la posizione corrente di un dispositivo.

## <a name="adding-a-map"></a>Aggiunta di una mappa

Aggiunta di una mappa a un'applicazione aggiungendo un `MKMapView` istanza per la gerarchia della visualizzazione, come illustrato di seguito:

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

 `MKMapView` è un `UIView` sottoclasse che visualizza una mappa. Aggiungendo semplicemente la mappa utilizzando il codice precedente produce una mappa interattiva:

 ![](images/00-map.png "Una mappa di esempio")

## <a name="map-style"></a>Stile di mappa

 `MKMapView` supporta 3 diversi stili di mappe. Per applicare uno stile di mappa, è sufficiente impostare la `MapType` proprietà su un valore di `MKMapType` enumerazione:
 ```
map.MapType = MKMapType.Standard; //road map
map.MapType = MKMapType.Satellite;
map.MapType = MKMapType.Hybrid;
 ```
  Nella schermata seguente mostra gli stili di mappa diversi disponibili:

 ![](images/01-mapstyles.png "Questa schermata mostra tematiche disponibili")

## <a name="panning-and-zooming"></a>Panoramica e zoom

 `MKMapView` include il supporto per le funzionalità di interattività mappa, ad esempio:

-  Lo zoom tramite un movimento zoom indietro
-  La panoramica tramite un movimento zoom


Queste funzionalità possono essere abilitate o disabilitate impostando semplicemente il `ZoomEnabled` e `ScrollEnabled` le proprietà del `MKMapView` istanza, in cui il valore predefinito è true per entrambi. Ad esempio, per visualizzare una mappa statica, è sufficiente impostare le proprietà appropriate su false:

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>Percorso utente

Oltre a interazione dell'utente, `MKMapView` offre inoltre supporto incorporato per visualizzare il percorso del dispositivo. Esegue questa operazione utilizzando il *percorso Core* framework. Prima di poter accedere al percorso dell'utente, è necessario richiedere all'utente. A tale scopo, creare un'istanza di `CLLocationManager` e chiamare `RequestWhenInUseAuthorization`.

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

Si noti che nelle versioni di iOS prima 8.0, il tentativo di chiamare `RequestWhenInUseAuthorization` si verificherà un errore. Assicurarsi di controllare la versione di iOS prima di effettuare tale chiamata se si prevede di supportare le versioni precedenti alla 8.

Anche l'accesso al percorso dell'utente richiede modifiche alle **Info. plist**. È necessario impostare le chiavi seguenti relative ai dati del percorso:

- **NSLocationWhenInUseUsageDescription**: per l'accesso al percorso dell'utente quando interagisce con l'app.
- **NSLocationAlwaysUsageDescription**: per l'accesso dell'app al percorso dell'utente in background.

È possibile aggiungere tali chiavi aprendo **Info. plist** e selezionando *origine* nella parte inferiore dell'editor.

Dopo avere aggiornato **Info. plist** e chiede all'utente l'autorizzazione per accedere ai loro posizione, è possibile visualizzare la posizione dell'utente sulla mappa impostando il `ShowsUserLocation` proprietà su true:

```csharp
map.ShowsUserLocation = true;
```

 ![](images/02-location-alert.png "L'avviso di accesso Consenti percorso")
 
## <a name="annotations"></a>Annotazioni

 `MKMapView` supporta anche la visualizzazione di immagini, nota come annotazioni, su una mappa. Può trattarsi di immagini personalizzate o PIN definita dal sistema di colori diversi. Ad esempio, nella schermata seguente viene illustrata una mappa con un entrambi un pin e un'immagine personalizzata:

 ![](images/03-annotations.png "Questa schermata viene visualizzata una mappa con un entrambi un pin e un'immagine personalizzata")

### <a name="adding-an-annotation"></a>Aggiunta di un'annotazione

Un'annotazione stessa dispone di due parti:

-  Il `MKAnnotation` oggetto, che include i dati del modello relativamente all'annotazione, ad esempio il titolo e il percorso dell'annotazione.
-  Il `MKAnnotationView` , che contiene l'immagine da visualizzare e, facoltativamente, una didascalia che viene visualizzata quando l'utente tocca l'annotazione.


Kit di mappa utilizza il modello di delega di iOS per aggiungere annotazioni a una mappa, in cui il `Delegate` proprietà del `MKMapView` è impostata su un'istanza di un `MKMapViewDelegate`. Si tratta di implementazione del delegato che è responsabile della restituzione di `MKAnnotationView` per un'annotazione.

Per aggiungere un'annotazione, innanzitutto l'annotazione viene aggiunta chiamando `AddAnnotations` sul `MKMapView` istanza:

```csharp
// add an annotation
map.AddAnnotations (new MKPointAnnotation (){
    Title="MyAnnotation",
    Coordinate = new CLLocationCoordinate2D (42.364260, -71.120824)
});
```

Quando il percorso dell'annotazione è visibile sulla mappa, la `MKMapView` chiamerà il delegato `GetViewForAnnotation` metodo per ottenere il `MKAnnotationView` da visualizzare.

Ad esempio, il codice seguente restituisce fornita dal sistema `MKPinAnnotationView`:

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

Per risparmiare memoria, `MKMapView` consente annotazione Vedi le per inserire in un pool per il riutilizzo, simile al modo in cui vengono riutilizzate le celle di tabella. Ottenere una visualizzazione di annotazione del pool viene eseguita con una chiamata a `DequeueReusableAnnotation`:

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>Con callout

Come accennato in precedenza, un'annotazione può essere visualizzato facoltativamente callout. Per visualizzare semplicemente callout impostare `CanShowCallout` su true nel `MKAnnotationView`. Ciò comporta il titolo dell'annotazione visualizzato quando si tocca l'annotazione, come illustrato:

 ![](images/04-callout.png "Viene visualizzato il titolo di annotazioni")

### <a name="customizing-the-callout"></a>Personalizzazione del Callout

La didascalia può essere personalizzata anche per mostrare visualizzazioni a sinistra e destra degli accessori come mostrato di seguito:

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

Questo codice comporta la chiamata seguente:

 ![](images/05-callout-accessories.png "Un esempio callout")

Per gestire l'utente toccando l'accessorio destra, implementare semplicemente il `CalloutAccessoryControlTapped` metodo il `MKMapViewDelegate`:

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>Sovrapposizioni

Un altro modo per grafica livello su una mappa utilizza sovrapposizioni. Sovrapposizioni supportano disegno del contenuto con interfaccia grafica che consente una scalabilità con la mappa come si esegue lo zoom. iOS fornisce supporto per diversi tipi di sovrapposizioni, tra cui:

-  Poligoni - comunemente usati per evidenziare un'area su una mappa.
-  Polilinea - spesso visualizzata quando una route.
-  Cerchi - usati per evidenziare un'area circolare di una mappa.


Inoltre, è possono creare sovrimpressioni personalizzate per mostrare geometrie arbitrarie con codice di disegno granulare e personalizzate. Ad esempio, radar weather sarebbe un buon candidato per una sovrapposizione personalizzato.

#### <a name="adding-an-overlay"></a>Aggiunta di una sovrapposizione

Analogamente alle annotazioni, aggiunta di una sovrapposizione prevede suddiviso in 2 parti:

-  Creazione di un oggetto modello per la sovrimpressione e aggiungendolo al `MKMapView` .
-  Creazione di una visualizzazione per la sovrimpressione nel `MKMapViewDelegate` .


Il modello per la sovrapposizione può essere qualsiasi `MKShape` sottoclasse. Include xamarin `MKShape` sottoclassi per poligoni, polilinea e cerchi, tramite il `MKPolygon`, `MKPolyline` e `MKCircle` classi rispettivamente.

Ad esempio, il codice seguente viene utilizzato per aggiungere un `MKCircle`:

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

La visualizzazione di una sovrapposizione corrisponde un `MKOverlayView` istanza restituito dal `GetViewForOverlay` nel `MKMapViewDelegate`. Ogni `MKShape` dispone di un corrispondente `MKOverlayView` in grado di visualizzare la forma specificata. Per `MKPolygon` è `MKPolygonView`. Analogamente, `MKPolyline` corrisponde a `MKPolylineView`e per `MKCircle` è `MKCircleView`.

Ad esempio, il codice seguente restituisce un `MKCircleView` per un `MKCircle`:

```csharp
public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
{
    var circleOverlay = overlay as MKCircle;
    var circleView = new MKCircleView (circleOverlay);
    circleView.FillColor = UIColor.Blue;
    return circleView;
}
```

Come illustrato in questo viene visualizzato un cerchio nella mappa:

 ![](images/06-circle-overlay.png "Un cerchio visualizzato sulla mappa")

## <a name="local-search"></a>Ricerca locale

iOS include una ricerca locale API in Kit mappa, che consente di cercare asincrone per i punti di interesse in una determinata area geografica.

Per eseguire una ricerca locale, un'applicazione deve eseguire la procedura seguente:

1.  Creare `MKLocalSearchRequest` oggetto.
1.  Creare un `MKLocalSearch` dall'oggetto di `MKLocalSearchRequest` .
1.  Chiamare il `Start` metodo il `MKLocalSearch` oggetto.
1.  Recuperare il `MKLocalSearchResponse` oggetto in un callback.


La stessa interfaccia API di ricerca locale non fornisce alcuna interfaccia utente. Non richiede anche una mappa da utilizzare. Tuttavia, per un utilizzo pratico di ricerca locale, un'applicazione deve fornire un modo per specificare una query di ricerca e visualizzare i risultati. Inoltre, poiché i risultati conterrà i dati sulla località, può essere spesso utile per visualizzarli in una mappa.

### <a name="adding-a-local-search-ui"></a>Aggiunta di una ricerca locale dell'interfaccia utente

È un modo per accettare l'input di ricerca con un `UISearchBar`, che è fornito da un `UISearchController` e visualizzerà i risultati in una tabella.

Il codice seguente aggiunge il `UISearchController` (che ha una proprietà barra di ricerca) nei `ViewDidLoad` metodo `MapViewController`:

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

```csharp
Note that you are responsible for incorporating the search bar object into the user interface. In this example, we assigned it to the TitleView of the navigation bar, but if you do not use a navigation controller in your application you will have to find another place to display it.

In this code snippet, we created another custom view controller – `searchResultsController` –  that displays the search results and then we used this object to create our search controller object. We also created a new search updater, which becomes active when the user interacts with the search bar. It receives notifications about searches with each keystroke and is responsible for updating the UI.
We will take a look at how to implement both the `searchResultsController` and the `searchResultsUpdater` later in this guide.

This results in a search bar displayed over the map as shown below:

 ![](images/07-searchbar.png "A search bar displayed over the map")
 


### Displaying the Search Results

To display search results, we need to create a custom View Controller; normally a `UITableViewController`. As shown above, the `searchResultsController` is passed to the constructor of the `searchController` when it is being created.
The following code is an example of how to create this custom View Controller:

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

### <a name="updating-the-search-results"></a>Aggiornamento dei risultati di ricerca

Il `SearchResultsUpdater` agisce come mediatore tra il `searchController`della barra di ricerca e i risultati della ricerca. 

In questo esempio è necessario creare innanzitutto il metodo di ricerca nel `SearchResultsViewController`. A tale scopo è necessario creare un `MKLocalSearch` dell'oggetto da utilizzare per eseguire una ricerca per un `MKLocalSearchRequest`, i risultati vengono recuperati in un callback passato al `Start` metodo il `MKLocalSearch` oggetto. I risultati vengono quindi restituiti un `MKLocalSearchResponse` contenente una matrice di oggetti `MKMapItem` oggetti:

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

Quindi, nel nostro `MapViewController` creeremo un'implementazione personalizzata di `UISearchResultsUpdating`, assegnato al `SearchResultsUpdater` proprietà del nostro `searchController` nel [aggiunta di un'interfaccia di ricerca locale](#Adding_a_Local_Search_UI) sezione:

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

L'implementazione precedente aggiunge un'annotazione alla mappa quando è selezionato un elemento dai risultati, come illustrato di seguito:

 ![](images/08-search-results.png "Un'annotazione aggiunta alla mappa quando è selezionato un elemento dai risultati")
 
 > [!IMPORTANT]
> **Nota** `UISearchController` sia stato implementato in iOS 8. Se si desidera supportare i dispositivi precedenti a questo, sarà necessario utilizzare `UISearchDisplayController`.



## <a name="summary"></a>Riepilogo

In questo articolo esaminato il *mappa* *Kit* framework per iOS. Innanzitutto, esaminato come la `MKMapView` classe consente interattive mappe da includere in un'applicazione. Quindi illustrato come personalizzare ulteriormente le mappe mediante le annotazioni e sovrapposizioni. Infine, esaminato le funzionalità di ricerca locale che sono stati aggiunti al Kit di mappa con iOS 6.1, in cui viene illustrato come utilizzare eseguire query di base per i punti di interesse e li aggiunge a una mappa.

## <a name="related-links"></a>Collegamenti correlati

- [SearchController](https://developer.xamarin.com/recipes/ios/content_controls/search-controller/)
- [MapDemo (esempio)](https://developer.xamarin.com/samples/monotouch/MapDemo)
