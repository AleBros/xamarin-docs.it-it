---
title: ListView e ciclo di vita delle attività
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: b2328759b3158920bc8683ec14c2aebefd7a04ae
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61187073"
---
# <a name="listview-and-the-activity-lifecycle"></a>ListView e ciclo di vita delle attività

Le attività passano attraverso alcuni stati durante l'esecuzione dell'applicazione, ad esempio avvio, in esecuzione, in pausa e arresto. Per altre informazioni e linee guida specifiche sulla gestione delle transizioni di stato, vedere la [esercitazione del ciclo di vita dell'attività](~/android/app-fundamentals/activity-lifecycle/index.md).
È importante comprendere il ciclo di vita di attività e sul posto di `ListView` codice nelle posizioni corrette.

Tutti gli esempi in questo documento eseguire 'le attività di installazione' dell'attività `OnCreate` (metodo) e (se necessario) eseguire 'eliminazione' in `OnDestroy`. Gli esempi usano in genere piccoli set di dati che non vengono modificati, pertanto non è necessario caricare nuovamente i dati più frequentemente.

Tuttavia, se i dati sono di cambiare frequentemente o utilizza una grande quantità di memoria potrebbe essere opportuno usare i metodi del ciclo di vita diverso per compilare e aggiornare il `ListView`. Ad esempio, se i dati sottostanti cambiano continuamente (o potrebbero risentire degli aggiornamenti su altre attività) quindi la creazione di adapter nella `OnStart` o `OnResume` assicura i dati più recenti vengano visualizzati ogni volta che viene visualizzata l'attività.

Se l'Adapter utilizza risorse come memoria o di un cursore gestito, ricordare di rilascio delle risorse nel metodo complemento in cui sono state create (ad es. gli oggetti creati nelle `OnStart` può essere eliminato nel `OnStop`).


## <a name="configuration-changes"></a>Modifiche di configurazione

È importante ricordare che la configurazione viene modificato &ndash; particolarmente schermata visibilità rotazione e la tastiera &ndash; può causare l'attività corrente a essere distrutto e ricreato (a meno che non si specifica in caso contrario, utilizzando il `ConfigurationChanges` attributo). Ciò significa che in condizioni normali, rotazione di un dispositivo fa sì che un `ListView` e `Adapter` potrà essere creata nuovamente e (a meno che non sia stato scritto codice `OnPause` e `OnResume`) la selezione di riga e di posizione di scorrimento stati andranno persi.

L'attributo seguente impedirebbe un'attività viene eliminata e ricreata in seguito a modifiche di configurazione:

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

L'attività deve quindi eseguire l'override `OnConfigurationChanged` per rispondere a tali modifiche in modo appropriato. Per altre informazioni su come gestire le modifiche di configurazione, vedere la documentazione.

