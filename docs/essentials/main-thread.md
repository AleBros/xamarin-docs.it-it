---
title: 'Xamarin.Essentials: MainThread'
description: La classe MainThread consente alle applicazioni di eseguire il codice sul thread principale di esecuzione.
ms.assetid: CD6D51E7-D933-4FE7-A7F7-392EF27812E1
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
ms.openlocfilehash: 9109e7bff4cfe60479e711240d290d77b60a9af6
ms.sourcegitcommit: 9a46ee759ec4a738da348e8f8904d0f482ef0f25
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060113"
---
# <a name="xamarinessentials-mainthread"></a>Xamarin.Essentials: MainThread

La classe **MainThread** consente alle applicazioni di eseguire il codice sul thread principale di esecuzione e di determinare se un particolare blocco di codice è attualmente in esecuzione sul thread principale.

## <a name="background"></a>Background

La maggior parte dei sistemi operativi, tra cui iOS, Android e la piattaforma UWP, usa un modello a thread singolo per il codice che coinvolge l'interfaccia utente. Questo modello è necessario per serializzare correttamente gli eventi dell'interfaccia utente, inclusi l'input tocco e le sequenze di tasti. Questo thread viene spesso definito _thread principale_ o _thread dell'interfaccia utente_ o _thread UI_. Lo svantaggio di questo modello è che tutto il codice che accede a elementi dell'interfaccia utente deve essere eseguito sul thread principale dell'applicazione. 

Le applicazioni devono talvolta usare eventi che chiamano il gestore dell'evento su un thread secondario di esecuzione. (Tutte le classi Xamarin.Essentials [`Accelerometer`](accelerometer.md), [`Compass`](compass.md), [`Gyroscope`](gyroscope.md), [`Magnetometer`](magnetometer.md) e [`OrientationSensor`](orientation-sensor.md) potrebbero restituire informazioni su un thread secondario in caso d'uso con velocità maggiori.) Se il gestore eventi deve accedere a elementi dell'interfaccia utente, deve eseguire tale codice sul thread principale. La classe **MainThread** consente all'applicazione di eseguire questo codice sul thread principale.

## <a name="get-started"></a>Attività iniziali

[!include[](~/essentials/includes/get-started.md)]

## <a name="running-code-on-the-main-thread"></a>Esecuzione del codice sul thread principale

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per eseguire codice sul thread principale, chiamare il metodo statico `MainThread.BeginInvokeOnMainThread`. L'argomento è un oggetto [`Action`](xref:System.Action), che è semplicemente un metodo con nessun argomento e nessun valore restituito:

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

Sarà quindi possibile eseguire questo metodo sul thread principale facendovi riferimento nel metodo `BeginInvokeOnMainThread`:

```csharp
MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin.Forms include un metodo denominato [`Device.BeginInvokeOnMainThread(Action)`](https://docs.microsoft.com/dotnet/api/xamarin.forms.device.begininvokeonmainthread)
> che esegue la stessa operazione di `MainThread.BeginInvokeOnMainThread(Action)`. Sebbene sia possibile usare uno dei due metodi in un'app Xamarin.Forms, valutare se il codice chiamante ha o meno altre esigenze per una dipendenza da Xamarin.Forms. In caso negativo, `MainThread.BeginInvokeOnMainThread(Action)` è probabilmente un'opzione migliore.

## <a name="determining-if-code-is-running-on-the-main-thread"></a>Determinare se il codice è in esecuzione sul thread principale

La classe `MainThread` consente anche a un'applicazione di determinare se un particolare blocco di codice è in esecuzione sul thread principale. La proprietà `IsMainThread` restituisce `true` se il codice che chiama la proprietà è in esecuzione sul thread principale. Un programma può usare questa proprietà per eseguire codice diverso per il thread principale o un thread secondario:

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

Ci si potrebbe chiedere se è necessario controllare se il codice è in esecuzione su un thread secondario prima di chiamare `BeginInvokeOnMainThread`, ad esempio, come segue:

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

Si potrebbe credere che questo controllo possa migliorare le prestazioni se il blocco di codice è già in esecuzione sul thread principale.

_Tuttavia, questo controllo non è necessario._ Le implementazioni della piattaforma di `BeginInvokeOnMainThread` stesse controllano se la chiamata viene eseguita sul thread principale. L'impatto sulle prestazioni è molto limitato se si chiama `BeginInvokeOnMainThread` quando non è effettivamente necessario.

## <a name="additional-methods"></a>Altri metodi

La classe `MainThread` include i metodi `static` aggiuntivi seguenti che possono essere usati per interagire con gli elementi dell'interfaccia utente dai thread di background:

| Metodo | Argomenti | Restituisce | Scopo |
|---|---|---|---|
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | Richiama un oggetto `Func<T>` sul thread principale e ne attende il completamento. |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | Richiama un oggetto `Action` sul thread principale e ne attende il completamento. |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | Richiama un oggetto `Func<Task<T>>` sul thread principale e ne attende il completamento. |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | Richiama un oggetto `Func<Task>` sul thread principale e ne attende il completamento. |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | Restituisce l'oggetto `SynchronizationContext` per il thread principale. |

## <a name="api"></a>API

- [Codice sorgente di MainThread](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/MainThread)
- [Documentazione dell'API MainThread](xref:Xamarin.Essentials.MainThread)
