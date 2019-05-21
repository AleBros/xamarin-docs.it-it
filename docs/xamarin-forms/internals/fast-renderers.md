---
title: Xamarin.Forms Fast Renderers
description: Questo articolo presenta renderer veloci, la riduzione di inflazione e i costi per il rendering di un controllo di xamarin. Forms in Android appiattendo la gerarchia dei controllo nativi risultante.
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 861d9e3f898dcd61015d9aca27ae66c3fe72d1a9
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970714"
---
# <a name="xamarinforms-fast-renderers"></a>Xamarin.Forms Fast Renderers

In genere, la maggior parte dei renderer controllo originale in Android sono costituita da due visualizzazioni:

- Controllo nativo, ad esempio un `Button` o `TextView`.
- Un contenitore `ViewGroup` che gestisce alcuni delle operazioni di layout, la gestione di movimenti e altre attività.

Tuttavia, questo approccio presenta un'implicazione di prestazioni in quanto le due visualizzazioni vengono create per ogni controllo logico, che comporta una struttura visiva più complessa che richiede più memoria e un'ulteriore elaborazione per eseguire il rendering sullo schermo.

I renderer veloci riducono l'ingrandimento e i costi per il rendering di un controllo di xamarin. Forms in una singola visualizzazione. Di conseguenza, invece di creare due visualizzazioni e ad aggiungerle alla struttura della visualizzazione, viene creata solo una. Ciò migliora le prestazioni creando un minor numero di oggetti, che significa che a sua volta una struttura ad albero Visualizza meno complesso, e minore consumo di memoria (che comporta un minor numero pause di garbage collection).

Renderer veloci sono disponibili per i controlli seguenti in xamarin. Forms in Android:

- [`Button`](xref:Xamarin.Forms.Button)
- [`Image`](xref:Xamarin.Forms.Image)
- [`Label`](xref:Xamarin.Forms.Label)
- [`Frame`](xref:Xamarin.Forms.Frame)

Tali renderer veloci sono a livello funzionale, non è diverso per il renderer legacy. Da xamarin. Forms 4.0 e versioni successive, tutte le applicazioni destinate a `FormsAppCompatActivity` useranno questi renderer veloci per impostazione predefinita. I renderer per tutti i nuovi controlli, tra cui [ `ImageButton` ](xref:Xamarin.Forms.ImageButton) e [ `CollectionView` ](xref:Xamarin.Forms.CollectionView), usare l'approccio renderer veloci.

Miglioramenti delle prestazioni quando si Usa renderer veloci variano per ogni applicazione, a seconda della complessità del layout. Ad esempio, sono possibili miglioramenti delle prestazioni di x2 durante lo scorrimento di un [ `ListView` ](xref:Xamarin.Forms.ListView) che contengono migliaia di righe di dati, in cui le celle in ogni riga sono costituite da controlli che usano renderer veloci, in modo da visibilmente lo scorrimento più uniforme.

> [!NOTE]
> Renderer personalizzati possono essere create per renderer veloci usando lo stesso approccio usato per il renderer legacy. Per altre informazioni, vedere [Custom Renderers](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) (Renderer personalizzati).

## <a name="backwards-compatibility"></a>Compatibilità con le versioni precedenti

Renderer veloci possono eseguire l'override con gli approcci seguenti:

1. Abilitare i renderer legacy aggiungendo la riga seguente di codice per il `MainActivity` classe prima di chiamare `Forms.Init`:

    ```csharp
    Forms.SetFlags("UseLegacyRenderers");
    ```

1. Usa renderer personalizzati che il renderer legacy come destinazione. Qualsiasi renderer personalizzati esistenti continueranno a funzionare con i renderer legacy.
1. Specificare un diverso `View.Visual`, ad esempio `Material`, che Usa renderer diversi. Per altre informazioni su Visual materiale, vedere [xamarin. Forms materiale Visual](~/xamarin-forms/user-interface/visual/material-visual.md).

## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
