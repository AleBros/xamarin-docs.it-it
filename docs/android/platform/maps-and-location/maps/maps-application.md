---
title: Avvio dell'applicazione di mappe
description: Come avviare l'applicazione di mappe predefinita all'interno dell'app xamarin. Android.
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/25/2018
ms.openlocfilehash: d15b6e544f58f03272c711236b579ca568e09539
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935022"
---
# <a name="launching-the-maps-application"></a>Avvio dell'applicazione di mappe

Il modo più semplice per funzionare con le mappe di xamarin. Android è sfruttare l'applicazione di mappe predefinite illustrato di seguito:

[![Schermata di esempio di app di Google Map integrata](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

Quando si usa l'applicazione di maps, la mappa non farà parte dell'applicazione. Al contrario, l'applicazione verrà avviare l'applicazione di mappe e caricare la mappa esternamente. Nella sezione successiva viene illustrato come utilizzare xamarin. Android per avviare le mappe simile a quello precedente.


## <a name="creating-the-intent"></a>Lo scopo di creazione

Utilizzo con le mappe dell'applicazione è semplice come la creazione di un intento con un URI appropriato, impostare l'azione ActionView e la chiamata al metodo StartActivity. Ad esempio, il codice seguente avvia l'applicazione di mappe centrata in una determinata latitudine e longitudine:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

Questo codice è tutto ciò che serve per avviare il mapping mostrato nella schermata precedente. Oltre a specificare latitudine e longitudine, lo schema URI per le mappe supporta numerose altre opzioni.


## <a name="geo-uri-scheme"></a>Schema URI di replica geografica

Il codice precedente usato lo schema di replica geografica per creare un URI. Questo schema URI supporta diversi formati, come indicato di seguito:

-   `geo:latitude,longitude` &ndash; Apre l'applicazione di mappe centrata in una tabella lat/lon. 

-   `geo:latitude,longitude?z=zoom` &ndash; Consente di aprire le mappe applicazione centrata in una tabella lat/lon e aver fatto zoom avanti al livello specificato. Il livello di zoom è compresi tra 1 e 23: 1 consente di visualizzare la terra intera e 23 è il livello di zoom più vicino.

-   `geo:0,0?q=my+street+address` &ndash; Apre l'applicazione esegue il mapping alla posizione di un indirizzo stradale. 

-   `geo:0,0?q=business+near+city` &ndash; Apre l'applicazione di mappe e visualizza i risultati della ricerca con annotazioni. 


Le versioni dell'URI che accettano una query (vale a dire la sede di lavoro indirizzo o ricerca termini) usare servizio geocoder di Google per recuperare il percorso che viene quindi visualizzato nella mappa. Ad esempio, l'URI `geo:0,0?q=coop+Cambridge` comporta la mappa illustrata di seguito:

[![Schermata di esempio che illustra le mappe di Google con un termine di ricerca](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)



Per ulteriori informazioni sugli schemi URI di replica geografica, vedere [Mostra una posizione in una mappa](http://developer.android.com/guide/components/intents-common.html#Maps).


## <a name="street-view"></a>Visualizzazione sede di lavoro

Oltre allo schema di replica geografica, Android supporta anche il caricamento di viste sede di lavoro da una finalità. Seguito è riportato un esempio dell'applicazione vista street avviata da xamarin:

[![Schermata di esempio di una vista sede di lavoro](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

Per avviare una vista sede di lavoro, è sufficiente usare il `google.streetview` schema URI, come illustrato nel codice seguente:

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

-   `lat` &ndash; La latitudine della posizione in cui essere mostrati nella visualizzazione della sede di lavoro.

-   `lng` &ndash; La longitudine della posizione in cui essere mostrati nella visualizzazione della sede di lavoro.

-   `pitch` &ndash; Angolo di panorama vista street, misurata dal centro in gradi in 90 gradi è retta verso il basso e -90 gradi è verso l'alto.

-   `yaw` &ndash; Centro di visualizzazione del panorama vista street, misurato in senso orario espresso in gradi da settentrionale.

-   `zoom` &ndash; Eseguire lo zoom avanti moltiplicatore per panorama vista via, dove 1.0 = normale zoom, 2.0 = 2 ingrandita x 3.0 = 4 ingrandita x, e così via.

-   `mz` &ndash; Il livello di zoom della mappa che verrà utilizzato quando si passa all'applicazione mappe dalla vista sede di lavoro.


Utilizzo con l'elemento predefinito esegue il mapping dell'applicazione o la vista sede di lavoro è un modo semplice per aggiungere rapidamente il supporto di mapping. API di mappe di Android offre tuttavia un controllo più preciso l'esperienza di mapping.
