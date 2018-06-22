---
title: Applicazione di mappe
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/05/2017
ms.openlocfilehash: b94c65c079b28fe042a42ec04357c11f3516d205
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30765555"
---
# <a name="maps-application"></a>Applicazione di mappe

Il modo più semplice per lavorare con le mappe di xamarin è di sfruttare l'applicazione esegue il mapping predefinito indicato di seguito:

[![Schermata di esempio di app di Google mappe predefinite](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

Quando si utilizza l'applicazione esegue il mapping, la mappa non farà parte dell'applicazione. Al contrario, l'applicazione verrà avviare l'applicazione esegue il mapping e caricare la mappa esternamente. La sezione successiva viene illustrato come utilizzare xamarin per avviare mappe simile a quello precedente.


## <a name="creating-the-intent"></a>Lo scopo di creazione

Utilizzo con le mappe dell'applicazione è semplice come la creazione di un intento con un URI appropriato, imposta l'azione ActionView e la chiamata al metodo StartActivity. Ad esempio, il codice seguente avvia l'applicazione di mappe centrato determinato latitudine e longitudine:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

Questo codice è tutto ciò che serve per avviare il mapping mostrato nella schermata precedente. Oltre a specificare latitudine e longitudine, lo schema URI per le mappe supporta numerose altre opzioni.


## <a name="geo-uri-scheme"></a>Schema URI geografica

Il codice precedente usato la combinazione di area geografica per creare un URI. Questo schema URI supporta diversi formati, come indicato di seguito:

-   `geo:latitude,longitude` &ndash; Apre l'applicazione esegue il mapping un lat/lon centrato. 

-   `geo:latitude,longitude?z=zoom` &ndash; Apre le mappe dell'applicazione un lat/lon centrato e ingrandito al livello specificato. Il livello di zoom può variare da 1 a 23: 1 consente di visualizzare la terra intera e 23 è il livello di zoom più vicino.

-   `geo:0,0?q=my+street+address` &ndash; Apre l'applicazione esegue il mapping al percorso di un indirizzo. 

-   `geo:0,0?q=business+near+city` &ndash; Apre l'applicazione di mappe e visualizza i risultati della ricerca con annotazioni. 


Le versioni dell'URI che accettano una query (vale a dire la via indirizzo o ricerca termini) usare il servizio di geocoder Google per recuperare il percorso che viene quindi visualizzato nella mappa. Ad esempio, l'URI `geo:0,0?q=coop+Cambridge` comporta la mappa illustrata di seguito:

[![Schermata di esempio che illustra il Google Maps con un termine di ricerca](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)



Per ulteriori informazioni su schemi URI geografica, vedere [Mostra un percorso in una mappa](http://developer.android.com/guide/components/intents-common.html#Maps).


## <a name="street-view"></a>Visualizzazione della via

Oltre la combinazione di area geografica, Android inoltre supporta il caricamento street viste da una finalità. Seguito è riportato un esempio dell'applicazione vista street avviata da xamarin:

[![Schermata di esempio di una vista via](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

Per avviare una vista via, utilizzare semplicemente la `google.streetview` schema URI, come illustrato nel codice seguente:

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

Lo schema URI google.streetview precedente assume il formato seguente:

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

Come si può notare, esistono numerosi parametri supportati, come indicato di seguito:

-   `lat` &ndash; La latitudine della posizione da visualizzare nella visualizzazione della via.

-   `lng` &ndash; La longitudine della località da visualizzare nella visualizzazione della via.

-   `pitch` &ndash; Angolo di panorama vista street, misurata dal centro in gradi in 90 gradi è retta verso il basso e -90 gradi è verso l'alto.

-   `yaw` &ndash; Centro di visualizzazione di panorama vista street, misurato in senso orario espresso in gradi da Nord.

-   `zoom` &ndash; Zoom moltiplicatore per panorama vista via, dove 1.0 = normale zoom, 2.0 = 2 ingrandita x, 3.0 = 4 ingrandita x, e così via.

-   `mz` &ndash; Il livello di zoom della mappa che verrà utilizzato quando l'applicazione esegue il mapping dalla visualizzazione della via.


Utilizzo predefinito esegue il mapping dell'applicazione o la vista via è un modo semplice per aggiungere rapidamente il supporto di mapping. Tuttavia, API di mappe di Android offre un maggiore controllo sull'esperienza di mapping.
