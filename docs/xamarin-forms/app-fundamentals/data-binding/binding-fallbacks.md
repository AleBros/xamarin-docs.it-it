---
title: Fallback per i binding di Xamarin.Forms
description: Questo articolo illustra come rendere più affidabili i binding mediante la definizione di valori di fallback, che vengono usati se il binding non riesce.
ms.prod: xamarin
ms.assetid: 637ACD9D-3E5D-4014-86DE-A77D1FEF238A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/16/2018
ms.openlocfilehash: 67fd8070ae36bdc1a90b8a33b25f13369d8d995d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650206"
---
# <a name="xamarinforms-binding-fallbacks"></a>Fallback per i binding di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

In alcuni casi i data binding non riescono perché l'origine del binding non può essere risolta o perché il binding ha esito positivo ma restituisce un valore `null`. Anche se questi scenari possono essere gestiti con convertitori di valori o altro codice aggiuntivo, i data binding possono essere resi più solidi mediante la definizione di valori di fallback da usare se il processo di binding ha esito negativo. Questa operazione può essere eseguita definendo le proprietà [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) e [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) in un'espressione di binding. Dato che queste proprietà risiedono nella classe [`BindingBase`](xref:Xamarin.Forms.BindingBase) possono essere usate con binding, binding compilati e con l'estensione di markup `Binding`.

> [!NOTE]
> L'uso delle proprietà [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) e [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) in un'espressione di binding è facoltativa.

## <a name="defining-a-fallback-value"></a>Definizione di un valore di fallback

La proprietà [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) consente di definire un valore di fallback che verrà usato nel caso in cui non sia possibile risolvere l'*origine* del binding. Uno scenario classico per l'impostazione di questa proprietà è il caso di un binding a proprietà di origine che potrebbero non esistere per tutti gli oggetti in una raccolta associata di tipi eterogenei.

La pagina **MonkeyDetail** illustra l'impostazione della proprietà [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue):

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

Il binding su [`Label`](xref:Xamarin.Forms.Label) definisce un valore [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) che verrà impostato nella destinazione se non è possibile risolvere l'origine del binding. Pertanto il valore definito dalla proprietà `FallbackValue` viene visualizzato se la proprietà `Population` non esiste per l'oggetto associato. Si noti che qui il valore della proprietà `FallbackValue` è delimitato da caratteri virgoletta singola (apostrofo).

Invece di definire i valori della proprietà [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) inline, è consigliabile definirli come risorse in un elemento [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Il vantaggio di questo approccio è che questi valori vengono definiti una sola volta in un'unica posizione e sono più facilmente localizzabili. È quindi possibile recuperare le risorse tramite l'estensione di markup `StaticResource`:

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> Non è possibile impostare la proprietà `FallbackValue` con un'espressione di binding.

Ecco il programma in esecuzione:

![Binding FallbackValue](binding-fallbacks-images/bindingunavailable-detail-cropped.png "Binding FallbackValue")

Quando la proprietà `FallbackValue` non è impostata in un'espressione di binding e il percorso di binding o una parte di esso non viene risolta, nella destinazione viene impostato [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue). Tuttavia, quando la proprietà `FallbackValue` è impostata e il percorso di binding o una parte di esso non viene risolta, nella destinazione viene impostato il valore della proprietà `FallbackValue`. Di conseguenza, nella pagina **MonkeyDetail** l'elemento [`Label`](xref:Xamarin.Forms.Label) visualizza "Population size unknown" (Dimensioni popolazione sconosciute) perché l'oggetto associato non dispone di una proprietà `Population`.

> [!IMPORTANT]
> Un convertitore di valori definito non viene eseguito in un'espressione di binding quando è impostata la proprietà [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue).

## <a name="defining-a-null-replacement-value"></a>Definizione di un valore di sostituzione null

La proprietà [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) consente di definire un valore di sostituzione che viene usato nel caso in cui l'*origine* del binding viene risolta, ma il valore è `null`. Uno scenario classico per l'impostazione di questa proprietà è il caso di un binding a proprietà di origine che potrebbero essere `null` in una raccolta associata.

La pagina **Monkeys** illustra l'impostazione della proprietà [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue):

```xaml
<ListView ItemsSource="{Binding Monkeys}"
          ...>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Image Source="{Binding ImageUrl, TargetNullValue='https://upload.wikimedia.org/wikipedia/commons/2/20/Point_d_interrogation.jpg'}"
                           ... />
                    ...
                    <Label Text="{Binding Location, TargetNullValue='Location unknown'}"
                           ... />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Entrambi i binding su [`Image`](xref:Xamarin.Forms.Image) e [`Label`](xref:Xamarin.Forms.Label) definiscono i valori [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) che verranno applicati se il percorso di binding restituisce `null`. Pertanto i valori definiti dalle proprietà `TargetNullValue` vengono visualizzati per tutti gli oggetti della raccolta in cui le proprietà `ImageUrl` e `Location` non sono definite. Si noti che qui i valori della proprietà `TargetNullValue` sono delimitati da caratteri virgoletta singola (apostrofo).

Invece di definire i valori della proprietà [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) inline, è consigliabile definirli come risorse in un elemento [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Il vantaggio di questo approccio è che questi valori vengono definiti una sola volta in un'unica posizione e sono più facilmente localizzabili. È quindi possibile recuperare le risorse tramite l'estensione di markup `StaticResource`:

```xaml
<Image Source="{Binding ImageUrl, TargetNullValue={StaticResource fallbackImageUrl}}"
       ... />
<Label Text="{Binding Location, TargetNullValue={StaticResource locationUnknown}}"
       ... />
```

> [!NOTE]
> Non è possibile impostare la proprietà `TargetNullValue` con un'espressione di binding.

Ecco il programma in esecuzione:

[![Binding TargetNullValue](binding-fallbacks-images/bindingunavailable-small.png "Binding TargetNullValue")](binding-fallbacks-images/bindingunavailable-large.png#lightbox "Binding TargetNullValue")

Quando la proprietà `TargetNullValue` non è impostata in un'espressione di binding, un valore di origine `null` viene convertito se è definito un convertitore di valori, formattato se è definito un `StringFormat` e quindi il risultato viene impostato nella destinazione. Tuttavia, quando la proprietà `TargetNullValue` è impostata, il valore di origine `null` viene convertito se è definito un convertitore di valori, e se è ancora `null` dopo la conversione, nella destinazione viene impostato il valore della proprietà `TargetNullValue`.

> [!IMPORTANT]
> La formattazione della stringa non viene applicata in un'espressione di binding quando è impostata la proprietà `TargetNullValue`.

## <a name="related-links"></a>Collegamenti correlati

- [Demo sul data binding (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
