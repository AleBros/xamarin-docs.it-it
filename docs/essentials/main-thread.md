---
title: 'Xamarin.Essentials: MainThread'
description: La classe MainThread consente alle applicazioni di eseguire il codice nel thread principale di esecuzione.
ms.assetid: CD6D51E7-D933-4FE7-A7F7-392EF27812E1
author: charlespetzold
ms.author: chape
ms.date: 06/26/2018
ms.openlocfilehash: e07d36d3e9a5492e6e170b62dbacb36be44dbfa9
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831425"
---
# <a name="xamarinessentials-mainthread"></a>Xamarin.Essentials: MainThread

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **MainThread** classe consente di eseguire codice nel thread principale dell'esecuzione di applicazioni e per determinare se un particolare blocco di codice è attualmente in esecuzione sul thread principale.

## <a name="background"></a>Sfondo

La maggior parte dei sistemi operativi, tra cui iOS, Android e la piattaforma Windows universale, usare un modello a thread singolo per il codice che coinvolgono l'interfaccia utente. Questo modello è necessario serializzare gli eventi di interfaccia utente, inclusi le sequenze di tasti e input tocco correttamente. Questo thread viene spesso definito il _thread principale_ o il _thread dell'interfaccia utente_ o nella _thread dell'interfaccia utente_. Lo svantaggio di questo modello è che tutto il codice che accede a elementi dell'interfaccia utente deve eseguire sul thread principale dell'applicazione. 

Le applicazioni, talvolta è necessario usare gli eventi che richiamano il gestore dell'evento in un thread secondario dell'esecuzione. (Le classi Xamarin.Essentials [ `Accelerometer` ](accelerometer.md), [ `Compass` ](compass.md), [ `Gyroscope` ](gyroscope.md), [ `Magnetometer` ](magnetometer.md), e [ `OrientationSensor` ](orientation-sensor.md) tutti potrebbero restituire informazioni su un thread secondario se usato con maggiore velocità.) Se il gestore eventi deve accedere a elementi dell'interfaccia utente, è necessario eseguire tale codice sul thread principale. Il **MainThread** classe consente l'esecuzione di questo codice nel thread principale dell'applicazione.

## <a name="running-code-on-the-main-thread"></a>Codice in esecuzione sul Thread principale

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per eseguire codice nel thread principale, chiamare il metodo statico `MainThread.BeginInvokeOnMainThread` (metodo). L'argomento è un [ `Action` ](xref:System.Action) oggetto, che è semplicemente un metodo con nessun argomento e restituisce alcun valore:

```csharp
MainThread.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

È anche possibile definire un metodo separato per il codice che deve essere eseguito sul thread principale:

```csharp
void MyMainThreadCode()
{
    // Code to run on the main thread
}
```

Sarà quindi possibile eseguire questo metodo sul thread principale per farvi riferimento nel `BeginInvokeOnMainThread` metodo:

```csharp
MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin. Forms ha un metodo denominato [ `Device.BeginInvokeOnMainThread(Action)` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.device.begininvokeonmainthread) che esegue la stessa operazione come `MainThread.BeginInvokeOnMainThread(Action)`. Sebbene sia possibile usare uno dei due metodi in un'app xamarin. Forms, si consideri il codice chiamante ha o meno qualsiasi altra esigenza per una dipendenza su xamarin. Forms. In caso contrario, `MainThread.BeginInvokeOnMainThread(Action)` è probabilmente un'opzione migliore.

## <a name="determining-if-code-is-running-on-the-main-thread"></a>Determinare se codice è in esecuzione il Thread principale

Il `MainThread` classe consente inoltre di determinare se un particolare blocco di codice è in esecuzione sul thread principale. Il `IsMainThread` restituisce proprietà `true` se il codice che chiama la proprietà è in esecuzione sul thread principale. Un programma può usare questa proprietà per eseguire codice diverso per il thread principale o un thread secondario:

```csharp
if (MainThread.IsMainThread)
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
if (MainThread.IsMainThread)
{
    MyMainThreadCode();
}
else
{
    MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
}
```

Si potrebbe sospettare che questo controllo potrebbe migliorare le prestazioni se il blocco di codice è già in esecuzione sul thread principale.

_Tuttavia, questo controllo non è necessario._ Le implementazioni della piattaforma di `BeginInvokeOnMainThread` stessi controllare se viene effettuata la chiamata sul thread principale. È molto piccolo riduzione delle prestazioni se si chiama `BeginInvokeOnMainThread` quando non è effettivamente necessario.

## <a name="api"></a>API

- [Codice sorgente MainThread](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/MainThread)
- [Documentazione dell'API MainThread](xref:Xamarin.Essentials.MainThread)
