---
title: Xamarin.FormsFallback di binding
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f3aadef1960618731c3749507233933b8bf7630e
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84133470"
---
# <a name="xamarinforms-binding-fallbacks"></a>Xamarin.FormsFallback di binding

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

In alcuni casi i data binding non riescono perché l'origine del binding non può essere risolta o perché il binding ha esito positivo ma restituisce un valore `null`. Anche se questi scenari possono essere gestiti con convertitori di valori o altro codice aggiuntivo, i data binding possono essere resi più solidi mediante la definizione di valori di fallback da usare se il processo di binding ha esito negativo. Questa operazione può essere eseguita definendo le [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) proprietà e in un'espressione di associazione. Poiché queste proprietà si trovano nella [`BindingBase`](xref:Xamarin.Forms.BindingBase) classe, possono essere utilizzate con binding, associazioni compilate e con l' `Binding` estensione di markup.

> [!NOTE]
> L'utilizzo delle [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) proprietà e in un'espressione di associazione è facoltativo.

## <a name="defining-a-fallback-value"></a>Definizione di un valore di fallback

La [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) proprietà consente di definire un valore di fallback che verrà utilizzato quando l' *origine* del binding non può essere risolta. Uno scenario classico per l'impostazione di questa proprietà è il caso di un binding a proprietà di origine che potrebbero non esistere per tutti gli oggetti in una raccolta associata di tipi eterogenei.

La pagina **MonkeyDetail** illustra l'impostazione della proprietà [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue):

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

L'associazione in [`Label`](xref:Xamarin.Forms.Label) definisce un [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) valore che verrà impostato sulla destinazione se l'origine del binding non può essere risolta. Pertanto il valore definito dalla proprietà `FallbackValue` viene visualizzato se la proprietà `Population` non esiste per l'oggetto associato. Si noti che qui il valore della proprietà `FallbackValue` è delimitato da caratteri virgoletta singola (apostrofo).

Anziché definire [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) i valori delle proprietà inline, è consigliabile definirli come risorse in un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Il vantaggio di questo approccio è che questi valori vengono definiti una sola volta in un'unica posizione e sono più facilmente localizzabili. È quindi possibile recuperare le risorse tramite l'estensione di markup `StaticResource`:

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> Non è possibile impostare la proprietà `FallbackValue` con un'espressione di binding.

Ecco il programma in esecuzione:

![Binding FallbackValue](binding-fallbacks-images/bindingunavailable-detail-cropped.png "Binding FallbackValue")

Quando la `FallbackValue` proprietà non è impostata in un'espressione di associazione e il percorso di associazione o parte del percorso non è risolto, [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue) viene impostato sulla destinazione. Tuttavia, quando la proprietà `FallbackValue` è impostata e il percorso di binding o una parte di esso non viene risolta, nella destinazione viene impostato il valore della proprietà `FallbackValue`. Di conseguenza, nella pagina **MonkeyDetail** l'elemento [`Label`](xref:Xamarin.Forms.Label) visualizza "Population size unknown" (Dimensioni popolazione sconosciute) perché l'oggetto associato non dispone di una proprietà `Population`.

> [!IMPORTANT]
> Un convertitore di valori definito non viene eseguito in un'espressione di binding quando la [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) proprietà è impostata.

## <a name="defining-a-null-replacement-value"></a>Definizione di un valore di sostituzione null

La [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) proprietà consente di definire un valore di sostituzione che verrà utilizzato quando l' *origine* del binding viene risolta, ma il valore è `null` . Uno scenario classico per l'impostazione di questa proprietà è il caso di un binding a proprietà di origine che potrebbero essere `null` in una raccolta associata.

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

Le associazioni in [`Image`](xref:Xamarin.Forms.Image) e [`Label`](xref:Xamarin.Forms.Label) definiscono entrambi [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) i valori che verranno applicati se il percorso di associazione restituisce `null` . Pertanto i valori definiti dalle proprietà `TargetNullValue` vengono visualizzati per tutti gli oggetti della raccolta in cui le proprietà `ImageUrl` e `Location` non sono definite. Si noti che qui i valori della proprietà `TargetNullValue` sono delimitati da caratteri virgoletta singola (apostrofo).

Anziché definire [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) i valori delle proprietà inline, è consigliabile definirli come risorse in un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Il vantaggio di questo approccio è che questi valori vengono definiti una sola volta in un'unica posizione e sono più facilmente localizzabili. È quindi possibile recuperare le risorse tramite l'estensione di markup `StaticResource`:

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

- [Data Binding Demos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demo di data binding)
