---
title: Renderer veloci di xamarin. Forms
description: Questo articolo presenta renderer veloci, la riduzione di inflazione e i costi per il rendering di un controllo di xamarin. Forms in Android appiattendo la gerarchia dei controllo nativi risultante.
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: a0054c79bf0f356bcfcc48389a0a28f68f73a48b
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "38997120"
---
# <a name="xamarinforms-fast-renderers"></a>Renderer veloci di xamarin. Forms

_Questo articolo presenta renderer veloci, la riduzione di inflazione e i costi per il rendering di un controllo di xamarin. Forms in Android appiattendo la gerarchia dei controllo nativi risultante._

In genere, la maggior parte dei renderer controllo originale in Android sono costituita da due visualizzazioni:

- Controllo nativo, ad esempio un `Button` o `TextView`.
- Un contenitore `ViewGroup` che gestisce alcuni delle operazioni di layout, la gestione di movimenti e altre attività.

Tuttavia, questo approccio presenta un'implicazione di prestazioni in quanto le due visualizzazioni vengono create per ogni controllo logico, che comporta una struttura visiva più complessa che richiede più memoria e un'ulteriore elaborazione per eseguire il rendering sullo schermo.

I renderer veloci riducono l'ingrandimento e i costi per il rendering di un controllo di xamarin. Forms in una singola visualizzazione. Di conseguenza, invece di creare due visualizzazioni e ad aggiungerle alla struttura della visualizzazione, viene creata solo una. Ciò migliora le prestazioni creando un minor numero di oggetti, che significa che a sua volta una struttura ad albero Visualizza meno complesso, e minore consumo di memoria (che comporta un minor numero pause di garbage collection).

Renderer veloci sono disponibili per i controlli seguenti in 2.4 di xamarin. Forms in Android:

- [`Button`](xref:Xamarin.Forms.Button)
- [`Image`](xref:Xamarin.Forms.Image)
- [`Label`](xref:Xamarin.Forms.Label)
- [`Frame`](xref:Xamarin.Forms.Frame)

Tali renderer veloci sono a livello funzionale, non è diverso per il renderer originale. Tuttavia, sono attualmente in fase sperimentale e può essere usati solo aggiungendo la riga seguente di codice per il `MainActivity` classe prima di chiamare `Forms.Init`:

```csharp
Forms.SetFlags("FastRenderers_Experimental");
```

> [!NOTE]
> Renderer veloci sono applicabili solo alle app compat Android back-end, in modo che questa impostazione viene ignorata per le attività di pre-app compat.

Miglioramenti delle prestazioni variano per ogni applicazione, a seconda della complessità del layout. Ad esempio, sono possibili miglioramenti delle prestazioni di x2 durante lo scorrimento di un [ `ListView` ](xref:Xamarin.Forms.ListView) che contengono migliaia di righe di dati, in cui le celle in ogni riga sono costituite da controlli che usano renderer veloci, in modo da visibilmente lo scorrimento più uniforme.


## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
