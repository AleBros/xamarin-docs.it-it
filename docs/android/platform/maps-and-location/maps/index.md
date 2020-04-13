---
title: Come utilizzare Google Maps e la posizione con Xamarin.Android
description: Questo articolo illustra come usare mappe e posizione con Xamarin.Android. Copre tutto, dal sfruttando l'applicazione mappe built-in per l'utilizzo di Google Maps Android API V2 direttamente.
ms.prod: xamarin
ms.assetid: 425E0ED2-5380-6EBE-7059-256B6E9128B8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: d877f415bb96024bb41edc2be9aec108ae248e88
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020037"
---
# <a name="how-to-use-google-maps-and-location-with-xamarinandroid"></a>Come utilizzare Google Maps e la posizione con Xamarin.Android

_Questo articolo illustra come usare mappe e posizione con Xamarin.Android. Copre tutto, dal sfruttando l'applicazione mappe built-in per l'utilizzo di Google Maps Android API V2 direttamente._

## <a name="maps-overview"></a>Cenni preliminari sulle mappe

Le tecnologie di mappatura sono un complemento onnipresente ai dispositivi mobili. I computer desktop e i portatili non tendono ad avere una consapevolezza della posizione integrata. D'altra parte, i dispositivi mobili utilizzano tali applicazioni per individuare i dispositivi e per visualizzare le informazioni sulla posizione mutevoli. Android dispone di una potente tecnologia integrata che visualizza i dati sulla posizione sulle mappe utilizzando l'hardware di posizione che potrebbe essere disponibile sul dispositivo. Questo articolo copre uno spettro di ciò che le applicazioni mappe sotto Xamarin.Android hanno da offrire, tra cui: 

- Utilizzo dell'applicazione di mappe incorporata per aggiungere rapidamente la funzionalità di mapping.
- Utilizzo dell'API Mappe per controllare la visualizzazione di una mappa.
- Utilizzo di una varietà di tecniche per aggiungere sovrapposizioni grafiche.

Negli argomenti di questa sezione viene trattatiuna un'ampia gamma di funzionalità di mapping.
In primo luogo, spiegano come sfruttare l'applicazione mappe integrata di Android e come visualizzare una vista panoramica sulla strada di una posizione. Vengono quindi illustrate le modalità di utilizzo dell'API Mappe per incorporare le funzionalità di mapping direttamente all'interno di un'applicazione, che illustrano sia come controllare la posizione e la visualizzazione di una mappa, sia come aggiungere sovrapposizioni grafiche.

## <a name="related-links"></a>Collegamenti correlati

- [MapsAndLocationDemo_v3 (esempio)MapsAndLocationDemo_v3 (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/mapsandlocationdemo-v3)
- [Ciclo di vita dell'attività](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Ottenere una chiave API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [Elenco delle finalità: richiamare le applicazioni Google su dispositivi Android](https://developer.android.com/guide/appendix/g-app-intents.html)
- [Posizione e mappe](https://developer.android.com/guide/topics/location/index.html)
