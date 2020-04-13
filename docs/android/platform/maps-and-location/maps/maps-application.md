---
title: Avvio dell'applicazione Mappe
description: Come avviare l'applicazione Mappe integrata dall'app Xamarin.Android.
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 7b74f564f2b6e9613874a774258a7e999002e61a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027072"
---
# <a name="launching-the-maps-application"></a>Avvio dell'applicazione Mappe

Il modo più semplice per lavorare con le mappe in Xamarin.Android è quello di sfruttare l'applicazione mappe built-in mostrato di seguito:

[![Schermata di esempio dell'app Google Maps integrata](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

Quando si utilizza l'applicazione mappe, la mappa non farà parte dell'applicazione. Al contrario, l'applicazione avvierà l'applicazione di mappe e caricherà la mappa esternamente. La sezione successiva esamina come utilizzare Xamarin.Android per lanciare mappe come quella sopra.

## <a name="creating-the-intent"></a>Creazione dell'intento

Lavorare con l'applicazione mappe è facile come la creazione di un intento con un URI appropriato, l'impostazione dell'azione su ActionView e la chiamata di StartActivity metodo. Ad esempio, il codice seguente avvia le mappe dell'applicazione centrate a una determinata latitudine e longitudine:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

Questo codice è tutto ciò che è necessario per avviare la mappa mostrata nella schermata precedente. Oltre a specificare latitudine e longitudine, lo schema URI per le mappe supporta diverse altre opzioni.

## <a name="geo-uri-scheme"></a>Schema URI geografico

Il codice precedente ha usato lo schema geografico per creare un URI. Questo schema URI supporta diversi formati, come elencato di seguito:This URI scheme supports several formats, as listed below:

- `geo:latitude,longitude`&ndash; Apre l'applicazione mappe centrata su un lat/lon. 

- `geo:latitude,longitude?z=zoom`&ndash; Apre l'applicazione mappe centrata su un lat/lon e ingrandita al livello specificato. Il livello di zoom può variare da 1 a 23: 1 visualizza l'intera Terra e 23 è il livello di zoom più vicino.

- `geo:0,0?q=my+street+address`&ndash; Apre l'applicazione mappe alla posizione di un indirizzo. 

- `geo:0,0?q=business+near+city`&ndash; Apre l'applicazione mappe e visualizza i risultati della ricerca con annotato. 

Le versioni dell'URI che accettano una query (vale a dire l'indirizzo o i termini di ricerca) utilizzano il servizio geocoder di Google per recuperare la posizione che viene quindi visualizzata sulla mappa. Ad esempio, `geo:0,0?q=coop+Cambridge` l'URI genera la mappa illustrata di seguito:For example, the URI results in the map shown below:

[![Schermata di esempio che mostra Google Maps con un termine di ricerca](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)

Per ulteriori informazioni sugli schemi URI geografici, vedere [Mostrare una posizione su una mappa.](https://developer.android.com/guide/components/intents-common.html#Maps)

## <a name="street-view"></a>Vista stradale

Oltre allo schema geografico, Android supporta anche il caricamento delle viste stradali da un intento. Di seguito è riportato un esempio dell'applicazione street view lanciata da Xamarin.Android:

[![Schermata di esempio di una vista stradale](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

Per avviare una visualizzazione `google.streetview` stradale, è sufficiente utilizzare lo schema URI, come illustrato nel codice seguente:To launch a street view, simply use the URI scheme, as demonstrated in the following code:

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

Lo schema URI google.streetview utilizzato sopra prende il seguente formato:

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

Come si può vedere, ci sono diversi parametri supportati, come elencato di seguito:

- `lat`&ndash; Latitudine della posizione da visualizzare nella vista stradale.

- `lng`&ndash; La longitudine della posizione da mostrare nella vista stradale.

- `pitch`&ndash; Angolo di vista stradale panorama, misurato dal centro in gradi dove 90 gradi è dritto verso il basso e -90 gradi è dritto verso l'alto.

- `yaw`&ndash; Centro di vista del panorama vista stradale, misurato in senso orario in gradi da nord.

- `zoom`&ndash; Moltiplicatore di zoom per il panorama della vista stradale, dove 1,0 - zoom normale, 2,0 ingrandito 2x, 3,0 - 4x ingrandito, ecc.

- `mz`&ndash; Livello di zoom della mappa che verrà utilizzato quando si passa all'applicazione mappe dalla vista stradale.

Lavorare con l'applicazione di mappe integrata o la vista stradale è un modo semplice per aggiungere rapidamente il supporto di mappatura. Tuttavia, l'API Mappe di Android offre un controllo più preciso sull'esperienza di mapping.
