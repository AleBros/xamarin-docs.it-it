---
title: ListView e il ciclo di vita di attività
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 6e15fb8796ae6a616c5eae44059caae3d9478aef
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764222"
---
# <a name="listview-and-the-activity-lifecycle"></a>ListView e il ciclo di vita di attività

Attività attraversano alcuni stati durante l'esecuzione dell'applicazione, ad esempio avvio, in esecuzione, in pausa e in fase di arresto. Per ulteriori informazioni e linee guida specifiche per la gestione delle transizioni di stato, vedere il [esercitazione del ciclo di vita di attività](~/android/app-fundamentals/activity-lifecycle/index.md).
È importante comprendere il ciclo di vita dell'attività e sul posto del `ListView` codice nei percorsi corretti.

Tutti gli esempi in questo documento eseguire 'attività di installazione' dell'attività `OnCreate` (metodo) e (se necessario) eseguire 'eliminazione' `OnDestroy`. Gli esempi viene utilizzata in genere piccoli set di dati che non cambiano, pertanto non è necessario ricaricare i dati più frequentemente.

Tuttavia, se i dati sono spesso la modifica o richiede una notevole quantità di memoria potrebbe essere opportuno utilizzare i metodi del ciclo di vita diverso per popolare e aggiornare il `ListView`. Ad esempio, se i dati sottostanti sono costantemente (o potrebbero essere interessati dagli aggiornamenti su altre attività), quindi creare l'adattatore in `OnStart` o `OnResume` garantisce i dati più recenti viene visualizzati ogni volta che viene visualizzata l'attività.

Se l'Adapter utilizza risorse come memoria o di un cursore gestito, ricordare di rilasciare quelle risorse nel metodo complementare in cui sono state create (ad es. gli oggetti creati nel `OnStart` può essere eliminato nel `OnStop`).


## <a name="configuration-changes"></a>Modifiche di configurazione

È importante ricordare che la configurazione viene modificato &ndash; in particolare per la visibilità di rotazione e tastiera &ndash; può causare l'attività corrente essere eliminato e ricreato (a meno che non si specifica l'utilizzo di `ConfigurationChanges` attributo). Ciò significa che in condizioni normali, la rotazione di un dispositivo causerà un `ListView` e `Adapter` ricreare e (a meno che non è stato scritto codice `OnPause` e `OnResume`) la selezione di riga e posizione di scorrimento stati andranno perse.

Un'attività venga eliminato definitivamente e ricreati in seguito alle modifiche di configurazione impediscono l'attributo seguente:

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

L'attività deve quindi eseguire l'override `OnConfigurationChanged` per rispondere a tali modifiche in modo appropriato. Per ulteriori informazioni su come gestire le modifiche di configurazione, vedere la documentazione.

