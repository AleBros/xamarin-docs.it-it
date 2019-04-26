---
title: L'avvio dell'applicazione di mappe
description: Come avviare l'applicazione mappe predefinita da all'interno dell'app xamarin. Android.
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: fa32783617fce99514560677184f17be904cd42d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61186379"
---
# <a name="launching-the-maps-application"></a>L'avvio dell'applicazione di mappe

Il modo più semplice per lavorare con le mappe in xamarin. Android è sfruttare l'applicazione mappe predefinite illustrato di seguito:

[![Screenshot di esempio di app di Google Maps predefinita](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

Quando si usa l'applicazione di maps, la mappa non farà parte dell'applicazione. Al contrario, l'applicazione verrà avviare l'applicazione esegue il mapping e caricare la mappa esternamente. Nella sezione successiva prende in esame come usare xamarin. Android per avviare le mappe come quello riportato sopra.


## <a name="creating-the-intent"></a>Lo scopo di creazione

Lavorare con le mappe dell'applicazione è semplice come la creazione di un Intent con un URI appropriato e impostando l'azione su ActionView chiamando il metodo StartActivity. Ad esempio, il codice seguente avvia l'applicazione mappe centrata a una latitudine e longitudine:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

Questo codice è tutto ciò che serve per avviare la mappa illustrata nello screenshot precedente. Oltre a specificare latitudine e longitudine, lo schema URI per le mappe supporta diverse altre opzioni.


## <a name="geo-uri-scheme"></a>Schema URI geografica

Il codice precedente usato lo schema geografico per creare un URI. Questo schema URI supporta diversi formati, come indicato di seguito:

-   `geo:latitude,longitude` &ndash; Apre l'applicazione mappe centrato nella forma di/lon lat. 

-   `geo:latitude,longitude?z=zoom` &ndash; Consente di aprire le mappe applicazione centrati nella forma di/lon lat e aver fatto zoom avanti al livello specificato. Il livello di zoom può variare da 1 a 23: 1 mostra la terra intera e 23 è il livello di zoom più vicino.

-   `geo:0,0?q=my+street+address` &ndash; Apre l'applicazione esegue il mapping al percorso di un indirizzo. 

-   `geo:0,0?q=business+near+city` &ndash; Apre l'applicazione esegue il mapping e visualizza i risultati della ricerca con annotazioni. 


Le versioni dell'URI che accetta una query (vale a dire la stradali indirizzo o ricerca termini) usano servizio geocoder di Google per recuperare la posizione che viene quindi visualizzata sulla mappa. Ad esempio, l'URI `geo:0,0?q=coop+Cambridge` la mappa in illustrato di seguito:

[![Screenshot di esempio che mostra di Google Maps con un termine di ricerca](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)



Per altre informazioni su schemi URI geografica, vedere [Mostra una posizione in una mappa](https://developer.android.com/guide/components/intents-common.html#Maps).


## <a name="street-view"></a>Visualizzazione stradale

Oltre a dello schema geografica, Android supporta anche il caricamento di viste civico da un Intent. Di seguito è riportato un esempio dell'applicazione visualizzazione street avviata da xamarin. Android:

[![Screenshot di esempio di una vista strada](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

Per avviare una visualizzazione street, usare semplicemente il `google.streetview` schema URI, come illustrato nel codice seguente:

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

Come si può vedere, esistono diversi parametri supportati, come indicato di seguito:

-   `lat` &ndash; Latitudine della posizione da visualizzare nella visualizzazione strada.

-   `lng` &ndash; Longitudine della posizione da visualizzare nella visualizzazione strada.

-   `pitch` &ndash; Angolo del panorama di visualizzazione street, misurata dal centro in gradi in cui viene direttamente verso il basso di 90 gradi e -90 gradi è verso l'alto.

-   `yaw` &ndash; Di-visualizzazione al centro del panorama di visualizzazione street, misurato in senso orario espresso in gradi dal centro-settentrionali.

-   `zoom` &ndash; Eseguire lo zoom avanti moltiplicatore per il panorama visualizzazione street, dove 1.0 = normale fattore di zoom, 2.0 = 2 ingrandita x, 3.0 = 4 ingrandita x, e così via.

-   `mz` &ndash; Il livello di zoom della mappa che verrà usato quando si passa all'applicazione mappe dalla visualizzazione strada.


Utilizzo con l'elemento predefinito esegue il mapping dell'applicazione o la visualizzazione della via è un modo semplice per aggiungere rapidamente il supporto di mapping. Tuttavia, API di mappe di Android offre controllo più preciso sull'esperienza di mapping.
