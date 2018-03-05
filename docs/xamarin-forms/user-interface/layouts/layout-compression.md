---
title: Compressione del layout
description: "La compressione di layout rimuove layout specificato dall'albero visuale nel tentativo di migliorare le prestazioni per il rendering della pagina. In questo articolo viene illustrato come abilitare la compressione di layout e i vantaggi che può eseguire."
ms.topic: article
ms.prod: xamarin
ms.assetid: da9e1b26-9d31-4762-94c3-4039f306b7f2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2017
ms.openlocfilehash: 15f198465c544989b347fe534978956741478ed2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="layout-compression"></a>Compressione del layout

_La compressione di layout rimuove layout specificato dall'albero visuale nel tentativo di migliorare le prestazioni per il rendering della pagina. In questo articolo viene illustrato come abilitare la compressione di layout e i vantaggi che può eseguire._

## <a name="overview"></a>Panoramica

Xamarin. Forms esegue il layout usando due serie di chiamate al metodo ricorsivo:

- Layout inizia nella parte superiore dell'albero visuale con una pagina e ne vengono tutti i rami dell'albero visuale per delimitare ciascun elemento visivo in una pagina. Gli elementi padre agli altri elementi sono responsabili di ridimensionamento e posizionamento autonomamente i relativi elementi figlio.
- Invalidamento è il processo mediante il quale una modifica in un elemento in una pagina attiva un nuovo ciclo di layout. Quando le dimensioni corrette o la posizione non è più necessario, gli elementi vengono considerati non validi. Ogni elemento nella struttura visiva che include elementi figlio viene generato un avviso ogni volta che uno dei relativi figli cambia le dimensioni. Pertanto, una modifica delle dimensioni di un elemento di struttura ad albero visuale può causare modifiche che si propagano fino alla struttura.

Per ulteriori informazioni su come xamarin. Forms esegue il layout, vedere [creazione di un Layout personalizzato](~/xamarin-forms/user-interface/layouts/custom.md).

Il risultato del processo di layout è una gerarchia di controlli nativi. Questa gerarchia include tuttavia i renderer di contenitori e i wrapper per i renderer di piattaforma, eccessi nella misurazione ulteriormente la gerarchia della visualizzazione di nidificazione. Se il livello di annidamento, maggiore è la quantità di lavoro che deve eseguire per visualizzare una pagina xamarin. Forms. Per layout complessi, la gerarchia della visualizzazione può essere sia completa e ampie, con più livelli di annidamento.

Ad esempio, si consideri il seguente pulsante dall'applicazione di esempio per l'accesso a Facebook:

![](layout-compression-images/facebook-button.png "Pulsante di Facebook")

Questo pulsante è specificato come un controllo personalizzato con la gerarchia della visualizzazione XAML seguente:

```xaml
<ContentView ...>
    <StackLayout>
        <StackLayout ...>
            <AbsoluteLayout ...>
                <Button ... />    
                <Image ... />
                <Image ... />
                <BoxView ... />
                <Label ... />
                <Button ... />
            </AbsoluteLayout>
        </StackLayout>
        <Label ... />
    </StackLayout>    
</ContentView>
```

La gerarchia della visualizzazione nidificata risultante può essere esaminata con [Xamarin controllo](~/tools/inspector/index.md). In Android, la gerarchia della visualizzazione nidificata contiene 17 visualizzazioni:

![](layout-compression-images/no-compression.png "Gerarchia di visualizzazione per il pulsante di Facebook")

La compressione di layout, che è disponibile per le applicazioni di xamarin. Forms in cui le piattaforme iOS e Android, mira a rendere flat la nidificazione rimuovendo layout specificato dall'albero visuale, che può migliorare le prestazioni di rendering della pagina di visualizzazione. Il miglioramento delle prestazioni che viene recapitato varia a seconda della complessità di una pagina, la versione del sistema operativo in uso e il dispositivo in cui è in esecuzione l'applicazione. Tuttavia, le prestazioni miglioreranno in modo più evidente nei dispositivi meno recenti.

> [!NOTE]
> **Nota**: Sebbene questo articolo illustra i risultati dell'applicazione la compressione di layout in Android, è ugualmente applicabile a iOS.

## <a name="layout-compression"></a>Compressione del layout

In XAML, la compressione di layout può essere abilitata impostando il `CompressedLayout.IsHeadless` proprietà associata `true` in una classe di layout:

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

In alternativa, può essere abilitata in c#, specificando l'istanza di layout come primo argomento per il `CompressedLayout.SetIsHeadless` metodo:

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> Poiché la compressione di layout rimuove la struttura ad albero visuale di un layout, non è adatto per i layout che hanno un aspetto visivo o di ottenere l'input tocco. Di conseguenza, i layout che impostare [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) proprietà (ad esempio [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/), [ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/), [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/), [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/), [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) e [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)) o accettare i movimenti non sono candidati per il layout compressione. Tuttavia, l'abilitazione della compressione di layout in un layout che imposta le proprietà di aspetto visivo o che accetta i movimenti, non comporterà un errore di compilazione o di runtime. Al contrario, verrà applicata la compressione di layout e le proprietà di aspetto e il riconoscimento di movimento, avranno esito negativo.

Per il pulsante di Facebook, la compressione di layout può essere abilitata per le classi di tre layout:

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
    <StackLayout CompressedLayout.IsHeadless="true" ...>
        <AbsoluteLayout CompressedLayout.IsHeadless="true" ...>
            ...
        </AbsoluteLayout>
    </StackLayout>
    ...
</StackLayout>  
```

In Android, in questo modo una gerarchia la vista annidata di 14 viste:

![](layout-compression-images/layout-compression.png "Gerarchia di visualizzazione per il pulsante di Facebook con la compressione di Layout")

Se confrontato con la gerarchia della visualizzazione nidificata originale delle 17 viste, rappresenta una riduzione del numero di viste di 17%. Mentre la riduzione potrebbe essere apparentemente irrilevante, la riduzione di visualizzazione in un'intera pagina può essere più significativa.

### <a name="fast-renderers"></a>Renderer veloci

Renderer veloce ridurre gli inflazione e i costi per il rendering di controlli di xamarin. Forms in Android rendere flat la gerarchia della visualizzazione nativo risultante. Questo ulteriore miglioramento delle prestazioni tramite la creazione di un numero inferiore di oggetti, che a sua volta comporta una struttura ad albero visuale meno complesso e un minore utilizzo della memoria. Per ulteriori informazioni relativi a renderer veloce, vedere [renderer veloce](~/xamarin-forms/internals/fast-renderers.md).

Per il pulsante di Facebook nell'applicazione di esempio, la combinazione di compressione di layout e renderer veloce produce una gerarchia la vista annidata di 8 viste:

![](layout-compression-images/layout-compression-with-fast-renderers.png "Gerarchia di visualizzazione per il pulsante di Facebook con la compressione di Layout e renderer veloce")

Se confrontato con la gerarchia della visualizzazione nidificata originale delle 17 viste, rappresenta una riduzione % 52.

L'applicazione di esempio contiene una pagina estratta da un'applicazione reale. Senza la compressione di layout e renderer veloce, la pagina genera una gerarchia la vista annidata di 130 viste in Android. L'abilitazione di renderer veloce e la compressione di layout in classi di layout appropriato riduce la gerarchia della visualizzazione nidificate alle 70 visualizzazioni, una riduzione del 46%.

## <a name="summary"></a>Riepilogo

La compressione di layout rimuove layout specificato dall'albero visuale nel tentativo di migliorare le prestazioni per il rendering della pagina. Il miglioramento delle prestazioni offerto varia in base alla complessità di una pagina, alla versione del sistema operativo in uso e al dispositivo in cui viene eseguita l'applicazione. Tuttavia, le prestazioni miglioreranno in modo più evidente nei dispositivi meno recenti.


## <a name="related-links"></a>Collegamenti correlati

- [Creazione di un layout personalizzato](~/xamarin-forms/user-interface/layouts/custom.md)
- [Renderer veloci](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutcompression/)
