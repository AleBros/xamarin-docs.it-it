---
title: 'Xamarin.Essentials: Stato di risparmio energia'
description: La classe Power consente a un programma di ottenere lo stato di risparmio energia per determinare se il dispositivo funziona in modalità a basso consumo.
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: jamesmontemagno
ms.author: jamont
ms.date: 06/27/2018
ms.openlocfilehash: 96b4aef3a8df571392d43836d46b03b025c80888
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675380"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials: Stato di risparmio energia

![NuGet in versione non definitiva](~/media/shared/pre-release.png)

La classe **Power** offre informazioni sullo stato di risparmio energia del dispositivo, che indica se il dispositivo è in esecuzione in modalità a basso consumo. Le applicazioni dovrebbero evitare l'elaborazione in background se lo stato di risparmio energia del dispositivo è attivo.

## <a name="background"></a>Sfondo

I dispositivi eseguiti a batteria possono essere messi in modalità di risparmio energia a basso consumo. In alcuni casi, questa modalità viene attivata automaticamente per i dispositivi, ad esempio quando la carica della batteria scende sotto il 20%. Il sistema operativo risponde alla modalità di risparmio energia riducendo le attività che tendono a scaricare la batteria. Le applicazioni possono contribuire evitando l'elaborazione in background o altre attività con un consumo elevato di energia, quando è attiva la modalità di risparmio energia.

Per i dispositivi Android, la classe **Power** restituisce informazioni significative solo per Android versione 5.0 (Lollipop) e successive.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-the-power-class"></a>Uso della classe Power

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Ottenere lo stato corrente di risparmio energia del dispositivo usando la proprietà statica `Power.EnergySaverStatus`:

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

Questa proprietà restituisce un membro dell'enumerazione `EnergySaverStatus`, ovvero `On`, `Off` o `Unknown`. Se la proprietà restituisce `On`, l'applicazione deve evitare l'elaborazione in background o altre attività che potrebbero usare molta energia.

L'applicazione deve anche installare un gestore eventi. La classe **Power** espone un evento che viene attivato quando cambia lo stato di risparmio energia:

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

Se lo stato di risparmio energia diventa `On`, l'applicazione deve interrompere l'elaborazione in background. Se lo stato diventa `Unknown` o `Off`, l'applicazione può riprendere l'elaborazione in background.

## <a name="api"></a>API

- [Codice sorgente di Power](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [Documentazione dell'API Power](xref:Xamarin.Essentials.Power)
