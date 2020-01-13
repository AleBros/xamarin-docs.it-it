---
title: Mappa di Xamarin.Forms
description: Il controllo mappa Visualizza una mappa e richiede il pacchetto NuGet Xamarin.Forms. maps.
ms.prod: xamarin
ms.assetid: B669B5EE-D24C-4C69-93E1-2CA5CC9108B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: 013e126b76de08442327707cd0502f207826dad8
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2019
ms.locfileid: "73425589"
---
# <a name="xamarinforms-map"></a>Mappa di Xamarin.Forms

## <a name="initialization-and-configurationsetupmd"></a>[Inizializzazione e configurazione](setup.md)

Il pacchetto NuGet [Xamarin.Forms. Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) è necessario per usare la funzionalità Maps in un'applicazione. Inoltre, l'accesso alla posizione dell'utente richiede l'autorizzazione per la posizione per l'applicazione.

## <a name="map-controlmapmd"></a>[controllo mappa](map.md)

Il controllo [`Map`](xref:Xamarin.Forms.Maps.Map) è una visualizzazione multipiattaforma per la visualizzazione e l'annotazione delle mappe. Usa il controllo mappa nativo per ogni piattaforma, offrendo un'esperienza di mapping rapida e familiare per gli utenti.

## <a name="position-and-distanceposition-distancemd"></a>[Posizione e distanza](position-distance.md)

Il [`Position`](xref:Xamarin.Forms.Maps.Position) struct viene in genere usato per il posizionamento di una mappa e dei relativi pin e dello struct di [`Distance`](xref:Xamarin.Forms.Maps.Distance) che può essere usato facoltativamente durante il posizionamento di una mappa.

## <a name="pinspinsmd"></a>[Puntine](pins.md)

Il controllo [`Map`](xref:Xamarin.Forms.Maps.Map) consente di contrassegnare le posizioni con [`Pin`](xref:Xamarin.Forms.Maps.Pin) oggetti. Un `Pin` è un marcatore di mappa che apre una finestra informazioni quando viene toccato.

## <a name="polygons-and-polylinespolygonsmd"></a>[Poligoni e polilinee](polygons.md)

gli elementi `Polygon` e `Polyline` consentono di evidenziare aree specifiche su una mappa. Un `Polygon` è una forma completamente racchiusa che può avere un tratto e un colore di riempimento. Una `Polyline` è una riga che non racchiude completamente un'area.

## <a name="geocodinggeocodermd"></a>[Geocodifica](geocoder.md)

La classe [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) esegue la conversione tra gli indirizzi stringa e le coordinate di latitudine e Longitudine archiviate in oggetti [`Position`](xref:Xamarin.Forms.Maps.Position) .

## <a name="launch-the-native-map-appnative-map-appmd"></a>[Avviare l'app mappa nativa](native-map-app.md)

L'app map nativa in ogni piattaforma può essere avviata da un'applicazione Xamarin.Forms dalla classe Xamarin.Essentials `Launcher`.
