---
title: Compressione del layout
description: Compressione dei layout rimuove i layout specificati dall'albero visuale nel tentativo di migliorare le prestazioni per il rendering della pagina. Questo articolo illustra come abilitare la compressione del layout e i vantaggi che può offrire.
ms.prod: xamarin
ms.assetid: da9e1b26-9d31-4762-94c3-4039f306b7f2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2017
ms.openlocfilehash: 97aa8c321362ebccc954a79f99b7bc69b5a0ad63
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657072"
---
# <a name="layout-compression"></a>Compressione del layout

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)

_Compressione dei layout rimuove i layout specificati dall'albero visuale nel tentativo di migliorare le prestazioni per il rendering della pagina. Questo articolo illustra come abilitare la compressione del layout e i vantaggi che può offrire._

## <a name="overview"></a>Panoramica

Xamarin. Forms esegue il layout usando due serie di chiamate al metodo ricorsivo:

- Layout in cui inizia la parte superiore della struttura visiva con una pagina e procede attraverso tutti i rami dell'albero visuale per includere ogni elemento visivo in una pagina. Gli elementi che sono elementi padre agli altri elementi sono responsabili per il ridimensionamento e posizionamento relativi elementi figlio rispetto a se stessi.
- Invalidamento è il processo mediante il quale una modifica in un elemento in una pagina attiva un nuovo ciclo di layout. Gli elementi vengono considerati non validi quando le dimensioni corrette o la posizione non è più necessario. Ogni elemento nell'albero visuale che contiene elementi figlio venga visualizzato un avviso ogni volta che uno dei relativi elementi figlio cambia le dimensioni. Pertanto, una modifica delle dimensioni di un elemento nell'albero visuale può causare modifiche che si propagano nell'albero.

Per altre informazioni sul modo in cui viene eseguita layout di xamarin. Forms, vedere [creazione di un Layout personalizzato](~/xamarin-forms/user-interface/layouts/custom.md).

Il risultato del processo di layout è una gerarchia di controlli nativi. Tuttavia, questa gerarchia include renderer contenitore aggiuntivo e wrapper per i renderer di piattaforma, ulteriormente ciò fa aumentare erroneamente la gerarchia di visualizzazione di annidamento. Maggiore è la profondità il livello di annidamento, maggiore è la quantità di lavoro che xamarin. Forms deve eseguire per visualizzare una pagina. Per layout complessi, la gerarchia di visualizzazione può essere sia completa e generica, con più livelli di annidamento.

Ad esempio, si consideri il seguente pulsante dall'applicazione di esempio per l'accesso a Facebook:

![](layout-compression-images/facebook-button.png "Pulsante di Facebook")

Questo pulsante viene specificato come un controllo personalizzato con la gerarchia di visualizzazione XAML seguente:

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

La gerarchia di visualizzazione annidati risultanti può essere esaminata con [Xamarin Inspector](~/tools/inspector/index.md). In Android, la gerarchia di visualizzazione nidificata contiene le 17 viste:

![](layout-compression-images/no-compression.png "Gerarchia di visualizzazione per il pulsante di Facebook")

Compressione del layout, che è disponibile per le applicazioni xamarin. Forms nelle piattaforme Android e iOS, è possibile rendere flat la visualizzazione di annidamento rimuovendo i layout specificati dalla struttura visiva, che può migliorare le prestazioni di rendering della pagina. Il miglioramento delle prestazioni che viene recapitato varia a seconda della complessità di una pagina, la versione del sistema operativo in uso e il dispositivo in cui viene eseguita l'applicazione. Tuttavia, le prestazioni miglioreranno in modo più evidente nei dispositivi meno recenti.

> [!NOTE]
> Questo articolo è incentrato sui risultati di applicare la compressione di layout in Android, è ugualmente applicabile ai dispositivi iOS.

## <a name="layout-compression"></a>Compressione del layout

In XAML, la compressione dei layout può essere abilitata impostando il `CompressedLayout.IsHeadless` proprietà associata `true` in una classe di layout:

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

In alternativa, può essere abilitata nel linguaggio c#, specificando l'istanza di layout come il primo argomento di `CompressedLayout.SetIsHeadless` metodo:

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> Poiché la compressione dei layout rimuove un layout dall'albero visuale, non è adatto per i layout che hanno un aspetto visivo, o che ottenere input tocco. Pertanto, i layout che impostata [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) delle proprietà (ad esempio [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor), [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible), [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation), [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale), [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) e [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) o che accetti i movimenti, non sono candidati per il layout compressione. Tuttavia, l'abilitazione della compressione di layout in un layout che imposta le proprietà di aspetto visivo, oppure che accetta i movimenti, non comporterà un errore di compilazione o di runtime. Al contrario, verrà applicata la compressione dei layout e le proprietà di aspetto visivo e il riconoscimento di movimento, avranno esito negativo.

Per il pulsante di Facebook, la compressione dei layout può essere abilitato per le classi di tre layout:

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

In Android, in questo modo una gerarchia la vista annidata di 14 visualizzazioni:

![](layout-compression-images/layout-compression.png "Gerarchia di visualizzazione per il pulsante di Facebook con compressione del Layout")

Rispetto alla gerarchia di vista annidata originale delle 17 viste, rappresenta una riduzione del numero di visualizzazioni di 17 %). Sebbene questa riduzione possa sembrare irrilevante, la riduzione della visualizzazione tramite un'intera pagina può essere più significativo.

### <a name="fast-renderers"></a>Renderer veloci

I renderer veloci riducono l'ingrandimento e i costi per il rendering dei controlli di xamarin. Forms in Android appiattendo la gerarchia di visualizzazione nativi risultante. Questo migliora ulteriormente le prestazioni creando un minor numero di oggetti, che comporta a sua volta in un struttura ad albero visuale meno complesso e minore consumo di memoria. Per altre informazioni sui renderer veloci, vedere [renderer veloci](~/xamarin-forms/internals/fast-renderers.md).

Per il pulsante di Facebook nell'applicazione di esempio, la combinazione di compressione del layout e renderer veloci produce una gerarchia la vista annidata di 8 visualizzazioni:

![](layout-compression-images/layout-compression-with-fast-renderers.png "Gerarchia di visualizzazione per il pulsante di Facebook con compressione del Layout e renderer veloci")

Rispetto alla gerarchia di vista annidata originale delle 17 viste, rappresenta una riduzione del 52%.

L'applicazione di esempio contiene una pagina estratta da un'applicazione reale. Senza compressione del layout e renderer veloci, la pagina genera una gerarchia la vista annidata di 130 visualizzazioni in Android. Abilitazione della compressione del layout nelle classi di layout appropriato e renderer veloci riduce la gerarchia di visualizzazione annidati a 70 visualizzazioni, una riduzione del 46%.

## <a name="summary"></a>Riepilogo

Compressione dei layout rimuove i layout specificati dall'albero visuale nel tentativo di migliorare le prestazioni per il rendering della pagina. Il miglioramento delle prestazioni offerto varia in base alla complessità di una pagina, alla versione del sistema operativo in uso e al dispositivo in cui viene eseguita l'applicazione. Tuttavia, le prestazioni miglioreranno in modo più evidente nei dispositivi meno recenti.


## <a name="related-links"></a>Collegamenti correlati

- [Creazione di un layout personalizzato](~/xamarin-forms/user-interface/layouts/custom.md)
- [Renderer veloci](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)
