---
title: " Xamarin.Forms map" Description: "il controllo map Visualizza una mappa e richiede Xamarin.Forms . Esegue il mapping del pacchetto NuGet ".
ms. prod: Novell MS. AssetID: B669B5EE-D24C-4C69-93E1-2CA5CC9108B5 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/29/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-map"></a>Xamarin.FormsMappa

## <a name="initialization-and-configuration"></a>[Inizializzazione e configurazione](setup.md)

Oggetto [ Xamarin.Forms . Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) il pacchetto NuGet è necessario per usare la funzionalità Maps in un'applicazione. Inoltre, l'accesso alla posizione dell'utente richiede l'autorizzazione per la posizione per l'applicazione.

## <a name="map-control"></a>[controllo mappa](map.md)

Il [`Map`](xref:Xamarin.Forms.Maps.Map) controllo è una visualizzazione multipiattaforma per la visualizzazione e l'annotazione delle mappe. Usa il controllo mappa nativo per ogni piattaforma, offrendo un'esperienza di mapping rapida e familiare per gli utenti.

## <a name="position-and-distance"></a>[Posizione e distanza](position-distance.md)

Lo [`Position`](xref:Xamarin.Forms.Maps.Position) struct viene in genere usato per il posizionamento di una mappa e dei relativi pin e dello [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct che può essere utilizzato facoltativamente durante il posizionamento di una mappa.

## <a name="pins"></a>[Pins](pins.md)

Il [`Map`](xref:Xamarin.Forms.Maps.Map) controllo consente di contrassegnare le posizioni con [`Pin`](xref:Xamarin.Forms.Maps.Pin) gli oggetti. Un `Pin` è un marcatore di mappa che apre una finestra informazioni quando viene toccato.

## <a name="polygons-polylines-and-circles"></a>[Poligoni, polilinee e cerchi](polygons.md)

`Polygon``Polyline` `Circle` gli elementi, e consentono di evidenziare aree specifiche su una mappa. Un oggetto `Polygon` è una forma completamente racchiusa che può presentare un tratto e un colore di riempimento. Un oggetto `Polyline` è una riga che non racchiude completamente un'area. Un oggetto `Circle` evidenzia un'area circolare della mappa.

## <a name="geocoding"></a>[Geocodifica](geocoder.md)

La [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) classe esegue la conversione tra gli indirizzi stringa e le coordinate di latitudine e Longitudine archiviate negli [`Position`](xref:Xamarin.Forms.Maps.Position) oggetti.

## <a name="launch-the-native-map-app"></a>[Avviare l'app per le mappe native](native-map-app.md)

L'app mappa nativa in ogni piattaforma può essere avviata da un' Xamarin.Forms applicazione dalla Xamarin.Essentials `Launcher` classe.
