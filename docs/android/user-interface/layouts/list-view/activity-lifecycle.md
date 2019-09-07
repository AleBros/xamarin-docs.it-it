---
title: Novell. Android ListView e ciclo di vita delle attività
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 1c093d3d67ace3b0f9186fca8226d4ef631d9af0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762313"
---
# <a name="xamarinandroid-listview-and-the-activity-lifecycle"></a>Novell. Android ListView e ciclo di vita delle attività

Le attività passano attraverso determinati stati durante l'esecuzione dell'applicazione, ad esempio l'avvio, l'esecuzione, la sospensione e l'arresto. Per altre informazioni e linee guida specifiche sulla gestione delle transizioni di stato, vedere l' [esercitazione ciclo](~/android/app-fundamentals/activity-lifecycle/index.md)di vita delle attività.
È importante comprendere il ciclo di vita dell'attività e inserire `ListView` il codice nei percorsi corretti.

Tutti gli esempi in questo documento eseguono ' attività di configurazione ' nel `OnCreate` metodo dell'attività e (quando necessario) eseguono ' teardown ' in. `OnDestroy` Gli esempi usano in genere set di dati di piccole dimensioni che non cambiano, quindi il ricaricamento dei dati più spesso non è necessario.

Tuttavia, se i dati cambiano di frequente o usano una grande quantità di memoria, potrebbe essere opportuno usare metodi del `ListView`ciclo di vita diversi per popolare e aggiornare. Se, ad esempio, i dati sottostanti cambiano continuamente (o potrebbero essere interessati da aggiornamenti in altre attività), la creazione dell' `OnStart` adapter `OnResume` in o garantirà la visualizzazione dei dati più recenti ogni volta che viene visualizzata l'attività.

Se l'adapter usa risorse come la memoria o un cursore gestito, ricordarsi di rilasciare tali risorse nel metodo complementare per la posizione in cui sono state create istanze, ad esempio gli oggetti creati `OnStart` in possono essere eliminati in `OnStop`).

## <a name="configuration-changes"></a>Modifiche di configurazione

È importante ricordare che le modifiche &ndash; alla configurazione, in particolare la rotazione dello schermo e la visibilità &ndash; della tastiera, possono causare la distruzione e la ricreazione dell'attività corrente, a `ConfigurationChanges` meno che non si specifichi diversamente usando il attributo). Ciò significa che, in condizioni normali, la rotazione di un dispositivo `ListView` comporterà la ricreazione di un oggetto e `Adapter` la ricreazione e, `OnPause` a `OnResume`meno che non sia stato scritto codice in e, la posizione di scorrimento e gli Stati di selezione delle righe andranno persi.

L'attributo seguente impedisce che un'attività venga distrutta e ricreata in seguito a modifiche della configurazione:

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

L'attività deve quindi eseguire `OnConfigurationChanged` l'override di per rispondere alle modifiche in modo appropriato. Per ulteriori informazioni su come gestire le modifiche di configurazione, vedere la documentazione di.
