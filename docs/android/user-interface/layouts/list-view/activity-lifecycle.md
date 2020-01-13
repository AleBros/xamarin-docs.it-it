---
title: Xamarin.Android ListView e ciclo di vita delle attività
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: a4ebda89ad3bdcb663dc9d7cd513e45760163c34
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028943"
---
# <a name="xamarinandroid-listview-and-the-activity-lifecycle"></a>Xamarin.Android ListView e ciclo di vita delle attività

Le attività passano attraverso determinati stati durante l'esecuzione dell'applicazione, ad esempio l'avvio, l'esecuzione, la sospensione e l'arresto. Per altre informazioni e linee guida specifiche sulla gestione delle transizioni di stato, vedere l' [esercitazione ciclo](~/android/app-fundamentals/activity-lifecycle/index.md)di vita delle attività.
È importante comprendere il ciclo di vita delle attività e inserire il codice `ListView` nei percorsi corretti.

Tutti gli esempi in questo documento eseguono ' attività di configurazione ' nel metodo di `OnCreate` dell'attività e, quando necessario, eseguono ' teardown ' in `OnDestroy`. Gli esempi usano in genere set di dati di piccole dimensioni che non cambiano, quindi il ricaricamento dei dati più spesso non è necessario.

Tuttavia, se i dati vengono modificati di frequente o utilizzano una grande quantità di memoria, potrebbe essere opportuno utilizzare metodi del ciclo di vita diversi per popolare e aggiornare i `ListView`. Se, ad esempio, i dati sottostanti cambiano continuamente (o potrebbero essere interessati da aggiornamenti in altre attività), la creazione dell'adapter in `OnStart` o `OnResume` garantirà la visualizzazione dei dati più recenti ogni volta che viene visualizzata l'attività.

Se l'adapter usa risorse come la memoria o un cursore gestito, ricordarsi di rilasciare tali risorse nel metodo complementare per la posizione in cui sono state create istanze, ad esempio gli oggetti creati in `OnStart` possono essere eliminati in `OnStop`).

## <a name="configuration-changes"></a>Modifiche di configurazione

È importante ricordare che le modifiche alla configurazione &ndash; in particolare la rotazione dello schermo e la visibilità della tastiera &ndash; possono causare la distruzione e la ricreazione dell'attività corrente, a meno che non si specifichi diversamente usando l'attributo `ConfigurationChanges`). Ciò significa che, in condizioni normali, la rotazione di un dispositivo provocherà la ricreazione di un `ListView` e `Adapter` e, a meno che non sia stato scritto codice in `OnPause` e `OnResume`, la posizione di scorrimento e gli Stati di selezione delle righe andranno persi.

L'attributo seguente impedisce che un'attività venga distrutta e ricreata in seguito a modifiche della configurazione:

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

L'attività deve quindi eseguire l'override di `OnConfigurationChanged` per rispondere in modo appropriato a tali modifiche. Per ulteriori informazioni su come gestire le modifiche di configurazione, vedere la documentazione di.
