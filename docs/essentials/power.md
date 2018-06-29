---
title: "Xamarin.Essentials: Stato Screen Saver dell'energia"
description: La classe Power consente a un programma ottenere lo stato di risparmio energia per determinare se il dispositivo funziona in una modalità basso consumo.
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: charlespetzold
ms.author: chape
ms.date: 06/27/2018
ms.openlocfilehash: 6d8ccb5be69eb1ea7ea63d3f5c373d9284089679
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080531"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials: Stato Screen Saver dell'energia

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **Power** classe fornisce le informazioni sullo stato di risparmio energetico del dispositivo, che indica se il dispositivo è in esecuzione in modalità basso consumo. Le applicazioni è consigliabile evitare l'elaborazione in background se lo stato di risparmio energetico del dispositivo è on.

## <a name="background"></a>Sfondo

I dispositivi che eseguono batterie possono essere inseriti in una modalità basso consumo energetico-screen saver. In alcuni casi i dispositivi vengono scambiati in questa modalità automaticamente, ad esempio, quando la batteria scende di sotto del 20% della capacità. Il sistema operativo risponde alla modalità di risparmio energetico, riducendo le attività che tendono a riducono la batteria. Consentono di applicazioni, evitando l'elaborazione in background o altre attività ad elevata potenza quando è in modalità di risparmio energetico.

Per i dispositivi Android, il **Power** classe restituisce informazioni significative solo per la versione di Android 5.0 (simbolo) e versioni successive.

## <a name="using-the-power-class"></a>Utilizzo della classe di risparmio energia

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Ottenere lo stato di risparmio energetico corrente del dispositivo tramite il metodo statico `Power.EnergySaverStatus` proprietà:

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

Questa proprietà restituisce un membro della `EnergySaverStatus` enumerazione, vale a dire `On`, `Off`, o `Unknown`. Se la proprietà restituisce `On`, l'applicazione è consigliabile evitare l'elaborazione in background o altre attività che potrebbero richiedere una grande quantità di energia elettrica.

L'applicazione è necessario installare anche un gestore eventi. Il **Power** classe espone un evento che viene attivato quando viene modificato lo stato di risparmio energia:

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Power.EnergySaverStatusChanaged += OnEnergySaverStatusChanaged;
    }

    private void OnEnergySaverStatusChanaged(EnergySaverStatusChanagedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

Se lo stato di risparmio energetico diventa `On`, l'applicazione deve arrestare l'elaborazione in background. Se lo stato diventa `Unknown` o `Off`, l'applicazione può riprendere l'elaborazione in background.

## <a name="api"></a>API

- [Codice sorgente di alimentazione](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [Documentazione dell'API Power](xref:Xamarin.Essentials.Power)
