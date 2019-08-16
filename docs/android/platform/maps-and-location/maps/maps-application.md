---
title: Avvio dell'applicazione Maps
description: Come avviare l'applicazione Maps predefinita dall'app Novell. Android.
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: b436ea4f5d8c70f0ca641bea72d1230fc07c5086
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523789"
---
# <a name="launching-the-maps-application"></a>Avvio dell'applicazione Maps

Il modo più semplice per lavorare con le mappe in Novell. Android consiste nell'utilizzare l'applicazione Maps predefinita illustrata di seguito:

[![Screenshot di esempio dell'app Google Maps predefinita](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

Quando si usa l'applicazione Maps, la mappa non sarà inclusa nell'applicazione. All'applicazione viene invece avviata l'applicazione Maps e viene caricata esternamente la mappa. La sezione successiva esamina come usare Novell. Android per avviare mappe come quella precedente.


## <a name="creating-the-intent"></a>Creazione della finalità

L'uso dell'applicazione Maps è semplice come creare una finalità con un URI appropriato, impostare l'azione su ActionView e chiamare il metodo StartActivity. Il codice seguente, ad esempio, avvia l'applicazione Maps centrata su una latitudine e una longitudine specificate:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

Questo codice è tutto ciò che serve per avviare la mappa mostrata nello screenshot precedente. Oltre a specificare la latitudine e la longitudine, lo schema URI per Maps supporta diverse altre opzioni.


## <a name="geo-uri-scheme"></a>Schema URI geografico

Il codice precedente usava lo schema geografico per creare un URI. Questo schema URI supporta diversi formati, come indicato di seguito:

- `geo:latitude,longitude`&ndash; Apre l'applicazione Maps centrata su Lat/Lon. 

- `geo:latitude,longitude?z=zoom`&ndash; Apre l'applicazione Maps centrata su un Lat/Lon e lo zoom al livello specificato. Il livello di zoom può variare da 1 a 23: 1 Visualizza l'intera terra e 23 è il livello di zoom più vicino.

- `geo:0,0?q=my+street+address`&ndash; Apre l'applicazione Maps al percorso di un indirizzo. 

- `geo:0,0?q=business+near+city`&ndash; Apre l'applicazione Maps e Visualizza i risultati della ricerca con annotazioni. 


Le versioni dell'URI che accettano una query (ovvero l'indirizzo via o i termini di ricerca) usano il servizio geocodificatore di Google per recuperare il percorso che viene quindi visualizzato sulla mappa. Ad esempio, l'URI `geo:0,0?q=coop+Cambridge` restituisce la mappa mostrata di seguito:

[![Schermata di esempio che Mostra Google Maps con un termine di ricerca](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)



Per ulteriori informazioni sugli schemi URI Geo, vedere [la pagina relativa alla visualizzazione di un percorso su una mappa](https://developer.android.com/guide/components/intents-common.html#Maps).


## <a name="street-view"></a>Visualizzazione via

Oltre allo schema geografico, Android supporta anche il caricamento di viste via da uno scopo. Di seguito è riportato un esempio di applicazione di visualizzazione via, avviata da Novell. Android:

[![Schermata di esempio di una visualizzazione via](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

Per avviare una visualizzazione via, è sufficiente utilizzare `google.streetview` lo schema URI, come illustrato nel codice seguente:

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

Lo schema URI Google. Streetview usato in precedenza assume il formato seguente:

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

Come si può notare, sono supportati diversi parametri, come indicato di seguito:

- `lat`&ndash; Latitudine della posizione da visualizzare nella visualizzazione via.

- `lng`&ndash; Longitudine della posizione da visualizzare nella visualizzazione via.

- `pitch`&ndash; Angolo del panorama di visualizzazione via, misurato dal centro in gradi in cui 90 gradi sono dritti e-90 gradi sono dritti.

- `yaw`&ndash; Centro di visualizzazione del panorama di visualizzazione via, misurato in senso orario in gradi dal Nord.

- `zoom`&ndash; Moltiplicatore zoom per la visualizzazione via, dove 1,0 = zoom normale, 2,0 = zoom avanti, 3,0 = 4 volte zoom e così via.

- `mz`&ndash; Livello di zoom della mappa che verrà usato quando si passa all'applicazione Maps dalla visualizzazione via.


L'uso dell'applicazione Maps predefinita o della visualizzazione via è un modo semplice per aggiungere rapidamente il supporto per il mapping. Tuttavia, l'API Maps di Android offre un controllo più preciso sull'esperienza di mapping.
