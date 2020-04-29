---
title: Mappa di Novell. Forms
description: Il controllo mappa Visualizza una mappa e richiede il pacchetto NuGet Novell. Forms. maps.
ms.prod: xamarin
ms.assetid: B669B5EE-D24C-4C69-93E1-2CA5CC9108B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: ffd8f7cc31707d09bb3442c180a867d31afcef0f
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517500"
---
# <a name="xamarinforms-map"></a>Mappa di Novell. Forms

## <a name="initialization-and-configuration"></a>[Inizializzazione e configurazione](setup.md)

Il pacchetto NuGet [Novell. Forms. Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) è necessario per usare la funzionalità Maps in un'applicazione. Inoltre, l'accesso alla posizione dell'utente richiede l'autorizzazione per la posizione per l'applicazione.

## <a name="map-control"></a>[controllo mappa](map.md)

Il [`Map`](xref:Xamarin.Forms.Maps.Map) controllo è una visualizzazione multipiattaforma per la visualizzazione e l'annotazione delle mappe. Usa il controllo mappa nativo per ogni piattaforma, offrendo un'esperienza di mapping rapida e familiare per gli utenti.

## <a name="position-and-distance"></a>[Posizione e distanza](position-distance.md)

Lo [`Position`](xref:Xamarin.Forms.Maps.Position) struct viene in genere usato per il posizionamento di una mappa e dei relativi [`Distance`](xref:Xamarin.Forms.Maps.Distance) pin e dello struct che può essere utilizzato facoltativamente durante il posizionamento di una mappa.

## <a name="pins"></a>[Segnaposto](pins.md)

Il [`Map`](xref:Xamarin.Forms.Maps.Map) controllo consente di contrassegnare le posizioni [`Pin`](xref:Xamarin.Forms.Maps.Pin) con gli oggetti. Un `Pin` è un marcatore di mappa che apre una finestra informazioni quando viene toccato.

## <a name="polygons-polylines-and-circles"></a>[Poligoni, polilinee e cerchi](polygons.md)

`Polygon`gli `Polyline`elementi, `Circle` e consentono di evidenziare aree specifiche su una mappa. Un `Polygon` oggetto è una forma completamente racchiusa che può presentare un tratto e un colore di riempimento. Un `Polyline` oggetto è una riga che non racchiude completamente un'area. Un `Circle` oggetto evidenzia un'area circolare della mappa.

## <a name="geocoding"></a>[Geocodifica](geocoder.md)

La [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) classe esegue la conversione tra gli indirizzi stringa e le coordinate di latitudine e [`Position`](xref:Xamarin.Forms.Maps.Position) Longitudine archiviate negli oggetti.

## <a name="launch-the-native-map-app"></a>[Avviare l'app per le mappe native](native-map-app.md)

L'app mappa nativa in ogni piattaforma può essere avviata da un'applicazione Novell. Forms dalla classe Novell `Launcher` . Essentials.
