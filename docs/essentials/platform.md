---
title: Piattaforma Xamarin.Essentials
description: La classe Platform consente alle applicazioni di eseguire il codice nel thread principale di esecuzione.
ms.assetid: CD6D51E7-D933-4FE7-A7F7-392EF27812E0
author: charlespetzold
ms.author: chape
ms.date: 06/03/2018
ms.openlocfilehash: 82e248ee702e104dff98b342aec72179273fc34f
ms.sourcegitcommit: d9ecac62bcf743aff52408fbbd09c5509921a000
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "36329948"
---
# <a name="xamarinessentials-platform"></a>Piattaforma Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **piattaforma** classe consente alle applicazioni di eseguire il codice nel thread principale di esecuzione e per determinare se un determinato blocco di codice è attualmente in esecuzione nel thread principale.

## <a name="background"></a>Sfondo

La maggior parte dei sistemi operativi, tra cui iOS, Android e la piattaforma Windows universale, usare un modello a thread singolo per il codice che interessa l'interfaccia utente. Questo modello è necessario serializzare gli eventi di interfaccia utente, inclusi le sequenze di tasti e input tocco correttamente. Questo thread è spesso definito il _thread principale_ o il _thread dell'interfaccia utente_ o la _thread dell'interfaccia utente_. Lo svantaggio di questo modello è che tutto il codice che accede a elementi dell'interfaccia utente è necessario eseguire sul thread principale dell'applicazione. 

Le applicazioni è talvolta necessario utilizzare gli eventi che chiama il gestore dell'evento su un thread secondario dell'esecuzione. (Le classi Xamarin.Essentials [ `Accelerometer` ](accelerometer.md), [ `Compass` ](compass.md), [ `Gyroscope` ](gyroscope.md), [ `Magnetometer` ](magnetometer.md), e [ `OrientationSensor` ](orientation-sensor.md) tutti potrebbe restituire informazioni su un thread secondario se utilizzata con velocità più elevate.) Se il gestore dell'evento deve accedere agli elementi dell'interfaccia utente, è necessario eseguire il codice nel thread principale. Il **piattaforma** classe consente l'esecuzione di questo codice nel thread principale dell'applicazione.

## <a name="running-code-on-the-main-thread"></a>Esecuzione del codice nel Thread principale

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per eseguire il codice nel thread principale, chiamare il metodo statico `Platform.BeginInvokeOnMainThread` metodo. L'argomento è un [ `Action` ](xref:System.Action) oggetto, che è semplicemente un metodo con senza argomenti e nessun valore restituito:

```csharp
Platform.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

Se è necessario chiamare questo metodo in un'applicazione di xamarin. Forms all'interno di una classe che deriva da `Element` (che include qualsiasi classe che deriva da `View` oppure `Page`), il `Platform` nome classe deve essere completo con il nome dello spazio dei nomi:

```csharp
Xamarin.Essentials.Platform.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

È anche possibile definire un metodo separato per il codice che deve essere eseguito nel thread principale:

```csharp
void MyMainThreadCode()
{
    // Code to run on the main thread
}
```

È quindi possibile eseguire questo metodo sul thread principale facendo clic su esso nel `BeginInvokeOnMainThread` metodo:

```csharp
Xamarin.Essentials.Platform.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin. Forms è un metodo denominato [ `Device.BeginInvokeOnMainThread(Action)` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.device.begininvokeonmainthread) che consente di eseguire la stessa operazione come `Xamarin.Essentials.Platform.BeginInvokeOnMainThread(Action)`. Sebbene sia possibile utilizzare dei metodi in un'app xamarin. Forms, si consideri il codice chiamante dispone o meno tutte le altre esigenze di una dipendenza su xamarin. Forms. In caso contrario, `Xamarin.Essentials.Platform.BeginInvokeOnMainThread(Action)` è probabilmente un'opzione migliore.

## <a name="determining-if-code-is-running-on-the-main-thread"></a>Determinare se codice è in esecuzione il Thread principale

Il `Platform` classe consente inoltre di un'applicazione determinare se un determinato blocco di codice è in esecuzione nel thread principale. Il `IsMainThread` restituisce proprietà `true` se il codice che chiama la proprietà è in esecuzione nel thread principale. Un programma può utilizzare questa proprietà per eseguire codice diverso per il thread principale o un thread secondario:

```csharp
if (Xamarin.Essentials.Platform.IsMainThread)
{
    // Code to run if this is the main thread
}
else
{
    // Code to run if this is a secondary thread
}
```

Ci si potrebbe chiedere se è necessario controllare se codice è in esecuzione su un thread secondario prima di chiamare `BeginInvokeOnMainThread`, ad esempio, come segue:

```csharp
if (Xamarin.Essentials.Platform.IsMainThread)
{
    MyMainThreadCode();
}
else
{
    Xamarin.Essentials.Platform.BeginInvokeOnMainThread(MyMainThreadCode);
}
```

Si potrebbe sospettare che questo controllo potrebbe migliorare le prestazioni se il blocco di codice è già in esecuzione nel thread principale.

_Tuttavia, questo controllo non è necessario._ Le implementazioni di piattaforma di `BeginInvokeOnMainThread` autonomamente controllare se viene effettuata la chiamata sul thread principale. È molto spazio riduzione delle prestazioni se si chiama `BeginInvokeOnMainThread` quando non è effettivamente necessario.

## <a name="api"></a>API

- [Codice sorgente della piattaforma](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Platform)
- [Documentazione API della piattaforma](xref:Xamarin.Essentials.Platform)
