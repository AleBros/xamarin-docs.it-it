---
title: Renderer veloci
description: Questo articolo descrive i renderer veloci, la riduzione di costi per il rendering di un controllo di xamarin. Forms in Android e inflazione da rendere flat la gerarchia di controllo nativo risultante.
ms.topic: article
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 956fa919ef9aa994fea92a9a64ca1325819f3ffc
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="fast-renderers"></a>Renderer veloci

_Questo articolo descrive i renderer veloci, la riduzione di costi per il rendering di un controllo di xamarin. Forms in Android e inflazione da rendere flat la gerarchia di controllo nativo risultante._

In genere, la maggior parte dei renderer di controllo originale in Android sono costituita da due visualizzazioni:

- Controllo nativo, ad esempio un `Button` o `TextView`.
- Un contenitore `ViewGroup` che gestisce alcuni delle operazioni di layout, la gestione di movimento e altre attività.

Tuttavia, questo approccio presenta un'implicazione di prestazioni in quanto vengono create due visualizzazioni per ogni controllo logico, che comporta un più complesso struttura ad albero visuale che richiede più memoria e un'ulteriore elaborazione per eseguire il rendering sullo schermo.

Renderer veloce ridurre gli inflazione e i costi per il rendering di un controllo di xamarin. Forms in una singola visualizzazione. Di conseguenza, invece di creare due visualizzazioni e aggiungerli alla struttura di visualizzazione, viene creata solo una. Ciò migliora le prestazioni creando gli oggetti di un numero inferiore, che significa che a sua volta un struttura ad albero visualizzazione meno complesso, e un minore utilizzo della memoria (che anche i risultati in un numero inferiore di pause di garbage collection).

Renderer rapido sono disponibili per i controlli seguenti in xamarin. Forms 2.4 in Android:

- [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)
- [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)
- [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)

A livello funzionale, tali renderer veloce non sono diversi per il renderer originale. Tuttavia, sono attualmente sperimentale e può essere utilizzati solo aggiungendo la seguente riga di codice per il `MainActivity` classe prima di chiamare `Forms.Init`:

```csharp
Forms.SetFlags("FastRenderers_Experimental");
```

> [!NOTE]
> Renderer veloci sono applicabili solo alle app compat Android back-end, pertanto questa impostazione viene ignorata per le attività di pre-app compat.

Miglioramenti delle prestazioni possono variare per ogni applicazione, a seconda della complessità del layout. Ad esempio, sono possibili miglioramenti delle prestazioni di x2 durante lo scorrimento un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) che contiene migliaia di righe di dati, le celle in ogni riga in cui vengono apportate di controlli che utilizzano i renderer veloci, dando luogo a visibilmente scorrimento più uniforme.


## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
