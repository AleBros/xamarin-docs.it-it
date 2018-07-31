---
title: "Xamarin.Essentials: Stato Saver dell'energia"
description: La classe Power consente un programma ottenere lo stato di risparmio energetico per determinare se il dispositivo funziona in modalità basso consumo.
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: charlespetzold
ms.author: chape
ms.date: 06/27/2018
ms.openlocfilehash: 760a305280269734034a817182a8c2a07894ca2b
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353490"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials: Stato Saver dell'energia

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **Power** classe fornisce informazioni sullo stato di risparmio energetico del dispositivo, che indica se il dispositivo è in esecuzione in modalità basso consumo. Le applicazioni è consigliabile evitare l'elaborazione in background se lo stato di risparmio energetico del dispositivo è on.

## <a name="background"></a>Sfondo

I dispositivi che eseguono batterie possono essere memorizzati in una modalità di risparmio energetico basso consumo. In alcuni casi i dispositivi vengono scambiati in questa modalità automaticamente, ad esempio, quando la batteria scende sotto il 20% della capacità. Il sistema operativo risponde alla modalità di risparmio energetico, riducendo le attività che tendono a ridurre la batteria. Le applicazioni possono aiutare, evitando l'elaborazione in background o altre attività ad elevata potenza quando è in modalità di risparmio energetico.

Per i dispositivi Android, il **Power** classe restituisce informazioni significative solo per la versione di Android 5.0 (Lollipop) e versioni successive.

## <a name="using-the-power-class"></a>Utilizzo della classe di risparmio energia

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Ottenere lo stato corrente di risparmio energetico del dispositivo usando il metodo statico `Power.EnergySaverStatus` proprietà:

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

Questa proprietà restituisce un membro del `EnergySaverStatus` enumerazione, vale a dire `On`, `Off`, o `Unknown`. Se la proprietà restituisce `On`, l'applicazione deve evitare l'elaborazione in background o altre attività che potrebbe utilizzare una grande quantità di energia.

L'applicazione deve inoltre installare un gestore eventi. Il **Power** classe espone un evento che viene attivato quando viene modificato lo stato di risparmio energetico:

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Power.EnergySaverStatusChanged += OnEnergySaverStatusChanged;
    }

    private void OnEnergySaverStatusChanged(EnergySaverStatusChangedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

Se lo stato di risparmio energetico diventa `On`, l'applicazione deve interrompere l'elaborazione in background. Se lo stato diventa `Unknown` o `Off`, l'applicazione possa riprendere l'elaborazione in background.

## <a name="api"></a>API

- [Codice sorgente di risparmio energia](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [Documentazione dell'API Power](xref:Xamarin.Essentials.Power)
