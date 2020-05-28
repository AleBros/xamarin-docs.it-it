---
title: Xamarin.FormsRenderer veloci
description: In questo articolo vengono introdotti i renderer veloci, che riducono i costi di inflazione e di rendering di un Xamarin.Forms controllo in Android, rendendo flat la gerarchia dei controlli nativi risultante.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3f25f4c2da5b2a426673b49045b5d2d05b0c6ac4
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139074"
---
# <a name="xamarinforms-fast-renderers"></a>Xamarin.FormsRenderer veloci

Tradizionalmente, la maggior parte dei renderer di controllo originali in Android è costituita da due visualizzazioni:

- Controllo nativo, ad esempio `Button` o `TextView` .
- Contenitore `ViewGroup` che gestisce alcune attività di layout, gestione dei movimenti e altre attività.

Tuttavia, questo approccio presenta una implicazione delle prestazioni in quanto vengono create due visualizzazioni per ogni controllo logico, il che comporta una struttura ad albero visuale più complessa che richiede una maggiore quantità di memoria e una maggiore elaborazione per il rendering sullo schermo.

I renderer veloci riducono l'inflazione e i costi di rendering di un Xamarin.Forms controllo in un'unica visualizzazione. Pertanto, anziché creare due visualizzazioni e aggiungerle alla struttura di visualizzazione, ne viene creata solo una. Ciò consente di migliorare le prestazioni creando un minor numero di oggetti, che a sua volta indica una struttura di visualizzazione meno complessa e un minor utilizzo di memoria (che comporta un minor numero di Garbage Collection pause).

I renderer veloci sono disponibili per i seguenti controlli in Xamarin.Forms in Android:

- [`Button`](xref:Xamarin.Forms.Button)
- [`Image`](xref:Xamarin.Forms.Image)
- [`Label`](xref:Xamarin.Forms.Label)
- [`Frame`](xref:Xamarin.Forms.Frame)

Dal punto di vista funzionale, questi renderer veloci non sono diversi per i renderer legacy. Da Xamarin.Forms 4,0 in poi, per impostazione predefinita tutte le applicazioni destinate a useranno `FormsAppCompatActivity` questi renderer veloci. I renderer per tutti i nuovi controlli, tra cui [`ImageButton`](xref:Xamarin.Forms.ImageButton) e [`CollectionView`](xref:Xamarin.Forms.CollectionView) , usano l'approccio renderer rapido.

I miglioramenti delle prestazioni quando si utilizzano renderer veloci variano per ogni applicazione, a seconda della complessità del layout. Ad esempio, i miglioramenti delle prestazioni di X2 sono possibili quando si scorre un oggetto [`ListView`](xref:Xamarin.Forms.ListView) che contiene migliaia di righe di dati, in cui le celle di ogni riga sono costituite da controlli che utilizzano renderer veloci, il che comporta lo scorrimento invisibile.

> [!NOTE]
> È possibile creare renderer personalizzati per renderer veloci usando lo stesso approccio usato per i renderer legacy. Per altre informazioni, vedere [Custom Renderers](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) (Renderer personalizzati).

## <a name="backwards-compatibility"></a>Compatibilità con le versioni precedenti

I renderer veloci possono essere sostituiti con gli approcci seguenti:

1. Per abilitare i renderer legacy, aggiungere la seguente riga di codice alla `MainActivity` classe prima di chiamare `Forms.Init` :

    ```csharp
    Forms.SetFlags("UseLegacyRenderers");
    ```

1. Uso dei renderer personalizzati destinati ai renderer legacy. Qualsiasi renderer personalizzato esistente continuerà a funzionare con i renderer legacy.
1. Specificando un diverso `View.Visual` , ad esempio `Material` , che usa renderer diversi. Per altre informazioni sull'oggetti visivi Material, vedere [ Xamarin.Forms Material Visual](~/xamarin-forms/user-interface/visual/material-visual.md).

## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
