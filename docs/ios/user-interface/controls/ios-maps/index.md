---
title: Mappe in xamarin. IOS
description: Questo documento descrive il framework MapKit iOS e come viene usato con xamarin. IOS. Illustra come aggiungere una mappa, stile, fare una panoramica e zoom, rivolgersi al percorso utente, aggiungere annotazioni, lavorare con i callout e sovrapposizioni ed eseguire la ricerca locale.
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: c61b5a8bd99afda5e8fbeea44e3362574fa7feea
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61226732"
---
# <a name="maps-in-xamarinios"></a>Mappe in xamarin. IOS

Le mappe sono una funzionalità comune in tutti i sistemi operativi per dispositivi mobili moderni. iOS offre il supporto di mapping in modo nativo tramite il framework Map Kit. Con il Framework Map Kit, le applicazioni possono aggiungere con facilità mappe avanzate e interattive. Queste mappe possono essere personalizzate in svariati modi, ad esempio l'aggiunta di annotazioni per contrassegnare le posizioni in una mappa e la sovrapposizione di elementi grafici di impatto su forme arbitrarie. Il Framework Map Kit include anche supporto predefinito per che mostra la posizione corrente di un dispositivo.

## <a name="adding-a-map"></a>Aggiunta di una mappa

Aggiunta di una mappa a un'applicazione aggiungendo un `MKMapView` dell'istanza per la gerarchia di visualizzazione, come illustrato di seguito:

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

 `MKMapView` è un `UIView` sottoclasse che consente di visualizzare una mappa. Aggiungendo semplicemente la mappa utilizzando il codice precedente produce una mappa interattiva:

 ![](images/00-map.png "Una mappa di esempio")

## <a name="map-style"></a>Stile di visualizzazione mappa

 `MKMapView` supporta 3 diversi stili di mappe. Per applicare uno stile di mappa, è sufficiente impostare il `MapType` un valore dalla proprietà di `MKMapType` enumerazione:
 ```
map.MapType = MKMapType.Standard; //road map
map.MapType = MKMapType.Satellite;
map.MapType = MKMapType.Hybrid;
 ```
  Lo screenshot seguente mostra gli stili mappa diversi disponibili:

 ![](images/01-mapstyles.png "Questo screenshot Mostra il mappa diversi stili disponibili")

## <a name="panning-and-zooming"></a>Panoramica e lo zoom

 `MKMapView` include il supporto per le funzionalità di interattività mappa, ad esempio:

-  Lo zoom tramite un movimento zoom indietro
-  La panoramica tramite un movimento di zoom


Queste funzionalità possono essere abilitate o disabilitate, semplicemente impostando il `ZoomEnabled` e `ScrollEnabled` delle proprietà del `MKMapView` istanza, in cui il valore predefinito è true per entrambi. Ad esempio, per visualizzare una mappa statica, è sufficiente impostare le proprietà appropriate su false:

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>Posizione dell'utente

Oltre all'interazione dell'utente, `MKMapView` offre inoltre supporto incorporato per visualizzare il percorso del dispositivo. Ciò avviene usando il *posizione Core* framework. Prima di poter accedere alla posizione dell'utente, è necessario richiedere all'utente. A questo scopo, creare un'istanza di `CLLocationManager` e chiamare `RequestWhenInUseAuthorization`.

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

Si noti che nelle versioni di iOS precedente 8.0, provare a chiamare `RequestWhenInUseAuthorization` comporterà un errore. Assicurarsi di controllare la versione di iOS prima di effettuare tale chiamata se si prevede di supportare le versioni precedenti alla 8.

Anche l'accesso al percorso dell'utente richiede modifiche a **Info. plist**. È necessario impostare le chiavi seguenti relative ai dati del percorso:

- **NSLocationWhenInUseUsageDescription**: per l'accesso al percorso dell'utente quando interagisce con l'app.
- **NSLocationAlwaysUsageDescription**: per l'accesso dell'app al percorso dell'utente in background.

È possibile aggiungere tali chiavi aprendo **Info. plist** e selezionando *origine* nella parte inferiore dell'editor.

Dopo avere aggiornato **Info. plist** e viene richiesto all'utente l'autorizzazione accedere ai loro posizione, è possibile visualizzare la posizione dell'utente sulla mappa, impostando il `ShowsUserLocation` proprietà su true:

```csharp
map.ShowsUserLocation = true;
```

 ![](images/02-location-alert.png "L'avviso di accesso Consenti percorso")
 
## <a name="annotations"></a>Annotazioni

 `MKMapView` supporta anche la visualizzazione di immagini, nota come annotazioni, su una mappa. Può trattarsi di immagini personalizzate o definito dal sistema con i pin di colori diversi. Ad esempio, lo screenshot seguente mostra una mappa con un entrambi un pin e un'immagine personalizzata:

 ![](images/03-annotations.png "Questo screenshot viene illustrata una mappa con un entrambi un pin e un'immagine personalizzata")

### <a name="adding-an-annotation"></a>Aggiunta di un'annotazione

Un'annotazione stesso è costituito da due parti:

-  Il `MKAnnotation` oggetto, che include i dati del modello relativamente all'annotazione, ad esempio il titolo e il percorso dell'annotazione.
-  Il `MKAnnotationView` , che contiene l'immagine da visualizzare e facoltativamente un callout in cui viene visualizzato quando l'utente tocca l'annotazione.


Il Framework Map Kit Usa il modello di delega di iOS per aggiungere annotazioni a una mappa, in cui il `Delegate` proprietà del `MKMapView` è impostata su un'istanza di un `MKMapViewDelegate`. Si tratta di implementazione del delegato che è responsabile della restituzione di `MKAnnotationView` per un'annotazione.

Per aggiungere un'annotazione, prima di tutto l'annotazione viene aggiunto chiamando `AddAnnotations` nella `MKMapView` istanza:

```csharp
// add an annotation
map.AddAnnotations (new MKPointAnnotation (){
    Title="MyAnnotation",
    Coordinate = new CLLocationCoordinate2D (42.364260, -71.120824)
});
```

Quando la posizione dell'annotazione è visibile sulla mappa, il `MKMapView` chiamerà il delegato `GetViewForAnnotation` metodo per ottenere il `MKAnnotationView` da visualizzare.

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

Per risparmiare memoria, `MKMapView` consente di inserire in un pool per il riutilizzo, simile al modo in cui vengono riutilizzate le celle della tabella vista di annotazione. Come ottenere una visualizzazione di annotazione dal pool viene eseguita con una chiamata a `DequeueReusableAnnotation`:

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>Che mostra i callout

Come accennato in precedenza, un'annotazione può essere visualizzato facoltativamente un callout. Per visualizzare semplicemente un callout impostata `CanShowCallout` su true nella `MKAnnotationView`. Ciò comporta il titolo dell'annotazione viene visualizzato quando si tocca l'annotazione, come illustrato:

 ![](images/04-callout.png "Il titolo di annotazioni da visualizzare")

### <a name="customizing-the-callout"></a>Personalizzazione del Callout

Il callout può essere personalizzato anche per mostrare visualizzazioni a sinistra e a destra degli accessori come mostrato di seguito:

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

Questo codice genera il callout seguente:

 ![](images/05-callout-accessories.png "Un esempio callout")

Per gestire l'utente se si tocca l'accessorio a destra, è sufficiente implementare il `CalloutAccessoryControlTapped` metodo di `MKMapViewDelegate`:

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>Sovrimpressioni

Un altro modo per la grafica di livello su una mappa utilizza le sovrimpressioni. Le sovrimpressioni supportano contenuto grafico di tipo disegno che viene ridimensionato con la mappa quando si esegue lo zoom. iOS fornisce supporto per diversi tipi di sovrapposizioni, tra cui:

-  Poligoni - comunemente usati per evidenziare alcune aree su una mappa.
-  Polilinee - spesso visualizzato quando viene visualizzata una route.
-  Cerchi - usati per evidenziare un'area circolare di una mappa.


Inoltre, le sovrimpressioni personalizzate è possibile creare in modo da visualizzare geometrie arbitrarie con codice di disegno granulare e personalizzati. Ad esempio, radar meteo sarebbe un buon candidato per un overlay di personalizzati.

#### <a name="adding-an-overlay"></a>Aggiunta di una sovrimpressione

Come per le annotazioni, aggiunta di una sovrimpressione prevede 2 parti:

-  Creazione di un oggetto modello per il controllo overlay e aggiungendolo al `MKMapView` .
-  Creazione di una vista per la sovrimpressione nel `MKMapViewDelegate` .


Il modello per la sovrimpressione può essere qualsiasi `MKShape` sottoclasse. Xamarin. IOS include `MKShape` sottoclassi per poligoni, polilinee e cerchi, tramite il `MKPolygon`, `MKPolyline` e `MKCircle` rispettivamente le classi.

Ad esempio, il codice seguente consente di aggiungere un `MKCircle`:

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

La visualizzazione per un controllo overlay è un' `MKOverlayView` istanza restituito dal `GetViewForOverlay` nel `MKMapViewDelegate`. Ciascuna `MKShape` ha un corrispondente `MKOverlayView` che conosce la modalità di visualizzazione della forma. Per la `MKPolygon` esiste `MKPolygonView`. Analogamente, `MKPolyline` corrisponde al `MKPolylineView`e per `MKCircle` esiste `MKCircleView`.

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

Come illustrato in questo viene visualizzato un cerchio sulla mappa:

 ![](images/06-circle-overlay.png "Un cerchio visualizzato sulla mappa")

## <a name="local-search"></a>Ricerca locale

iOS include una ricerca locale API con il Framework Map Kit, che consente di cercare asincrone per i punti di interesse in un'area geografica specificata.

Per eseguire una ricerca locale, un'applicazione deve seguire questi passaggi:

1.  Creare `MKLocalSearchRequest` oggetto.
1.  Creare un `MKLocalSearch` dall'oggetto di `MKLocalSearchRequest` .
1.  Chiamare il `Start` metodo di `MKLocalSearch` oggetto.
1.  Recuperare il `MKLocalSearchResponse` oggetto in un callback.


L'API stessa ricerca locale non fornisce alcuna interfaccia utente. Non richiedono nemmeno che una mappa da utilizzare. Tuttavia, affinché l'uso pratico di ricerca locale, un'applicazione deve fornire un modo per specificare una query di ricerca e visualizzazione dei risultati. Inoltre, poiché i risultati conterranno dati sulla località, sarà spesso senso per visualizzarli in una mappa.

<a name="Adding_a_Local_Search_UI"/>

### <a name="adding-a-local-search-ui"></a>Aggiunta di una ricerca locale dell'interfaccia utente

È un modo per accettare l'input di ricerca con un `UISearchBar`, che fornito da un `UISearchController` e visualizzerà i risultati in una tabella.

Il codice seguente aggiunge il `UISearchController` (che dispone di una proprietà barra di ricerca) nella `ViewDidLoad` metodo `MapViewController`:

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

Si noti che è responsabili includendo l'oggetto barra di ricerca nell'interfaccia utente. In questo esempio viene assegnato a TitleView della barra di spostamento, ma se non si utilizza un controller di spostamento all'interno dell'applicazione è necessario trovare un altro percorso per visualizzarlo.

In questo frammento di codice sono stati creati un altro controller di visualizzazione personalizzata: `searchResultsController` : che visualizza i risultati della ricerca e quindi viene usato questo oggetto per creare l'oggetto controller di ricerca. Viene inoltre creata una nuova ricerca dello strumento di aggiornamento, che si attiva quando l'utente interagisce con la barra di ricerca. Riceve le notifiche relative ricerche con ogni sequenza di tasti ed è responsabile dell'aggiornamento dell'interfaccia utente.
Verrà usato uno sguardo a come implementare entrambe le `searchResultsController` e il `searchResultsUpdater` più avanti in questa Guida.

Ciò comporta una barra di ricerca visualizzata sulla mappa, come illustrato di seguito:

 ![](images/07-searchbar.png "Una barra di ricerca visualizzata sulla mappa")
 


### <a name="displaying-the-search-results"></a>Visualizzare i risultati della ricerca

Per visualizzare i risultati della ricerca, è necessario creare un Controller di visualizzazione personalizzato. in genere un `UITableViewController`. Come illustrato in precedenza, il `searchResultsController` viene passato al costruttore del `searchController` quando vengono create.
Il codice riportato di seguito è riportato un esempio di come creare questo Controller di visualizzazione personalizzata:

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

In questo esempio è necessario creare innanzitutto il metodo di ricerca nel `SearchResultsViewController`. A tale scopo è necessario creare un `MKLocalSearch` dell'oggetto e usarlo per eseguire una ricerca per un `MKLocalSearchRequest`, i risultati vengono recuperati in un callback passato al `Start` metodo il `MKLocalSearch` oggetto. I risultati vengono quindi restituiti un' `MKLocalSearchResponse` contenente una matrice di oggetti `MKMapItem` oggetti:

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

Quindi, nel nostro `MapViewController` si creerà un'implementazione personalizzata di `UISearchResultsUpdating`, che viene assegnato al `SearchResultsUpdater` proprietà di nostro `searchController` nel [aggiunta un'interfaccia utente di ricerca locale](#Adding_a_Local_Search_UI) sezione:

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
> `UISearchController` è stato implementato in iOS 8. Se si desidera supportare i dispositivi precedenti a questo, allora sarà necessario usare `UISearchDisplayController`.



## <a name="summary"></a>Riepilogo

Questo articolo esaminato il *mappa* *Kit* framework per iOS. Prima di tutto esaminato come la `MKMapView` classe consente mappe interattive da includere in un'applicazione. Quindi è stato illustrato come personalizzare ulteriormente mappe mediante le annotazioni e sovrimpressioni. Infine, ha esaminato le funzionalità di ricerca locale che sono stati aggiunti per il Framework Map Kit con iOS 6.1, che illustra come usare l'esecuzione di query basato su posizione per i punti di interesse e aggiungerle a una mappa.

## <a name="related-links"></a>Collegamenti correlati

- [SearchController](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
- [MapDemo (esempio)](https://developer.xamarin.com/samples/monotouch/MapDemo)
