---
title: Fallback associazione di xamarin. Forms
description: Questo articolo illustra come rendere più affidabili i binding mediante la definizione di valori di fallback che verranno usati se l'associazione non riesce.
ms.prod: xamarin
ms.assetid: 637ACD9D-3E5D-4014-86DE-A77D1FEF238A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/16/2018
ms.openlocfilehash: 2a4b29df9148ce695f8f3ca5377e5848af1b775a
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171599"
---
# <a name="xamarinforms-binding-fallbacks"></a>Fallback associazione di xamarin. Forms

In alcuni casi le associazioni dati esito negativo, perché l'origine del binding non può essere risolto o perché l'associazione ha esito positivo ma restituisce un `null` valore. Anche se questi scenari possono essere gestiti con convertitori di valori o altro codice aggiuntivo, i data binding è assicurare maggiore affidabilità mediante la definizione di valori di fallback da usare se il processo di associazione ha esito negativo. Questa operazione può essere eseguita definendo le [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) e [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) proprietà in un'espressione di associazione. Perché queste proprietà si trovano nel [ `BindingBase` ](xref:Xamarin.Forms.BindingBase) (classe), possono essere usati con le associazioni, le associazioni compilate e con il `Binding` estensione di markup.

> [!NOTE]
> Usare la [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) e [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) proprietà in un'espressione di associazione è facoltativo.

## <a name="defining-a-fallback-value"></a>Definizione di un valore di fallback

Il [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) proprietà consente a un valore di fallback definire che verrà utilizzato quando l'associazione *origine* non può essere risolto. Uno scenario comune per l'impostazione di questa proprietà è durante l'associazione alle proprietà di origine che non esista su tutti gli oggetti in una raccolta associata di tipi eterogenei.

Il **MonkeyDetail** pagina viene illustrata l'impostazione di [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) proprietà:

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

L'associazione per il [ `Label` ](xref:Xamarin.Forms.Label) definisce una [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) valore che verrà impostata la destinazione se l'origine del binding non può essere risolto. Pertanto, il valore definito dal `FallbackValue` proprietà verrà visualizzata se il `Population` proprietà non esiste per l'oggetto associato. Si noti che qui il `FallbackValue` valore della proprietà è delimitato da caratteri di virgoletta singola (apostrofo).

Invece di definire [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) valori di proprietà inline, si consiglia di definirli come risorse in un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Il vantaggio di questo approccio è che tali valori sono definiti una volta in un'unica posizione e sono più facilmente localizzabili. Le risorse possono quindi essere recuperate tramite il `StaticResource` estensione di markup:

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> Non è possibile impostare il `FallbackValue` proprietà con un'espressione di associazione.

Ecco il programma in esecuzione:

![Associazione FallbackValue](binding-fallbacks-images/bindingunavailable-detail-cropped.png "FallbackValue associazione")

Quando la `FallbackValue` non è impostata in un'espressione di associazione e il percorso di associazione o parte del percorso non viene risolta, [ `BindableProperty.DefaultValue` ](xref:Xamarin.Forms.BindableProperty.DefaultValue) è impostato sulla destinazione. Tuttavia, quando la `FallbackValue` proprietà viene impostata e il percorso di associazione o parte del percorso non viene risolta, il valore della `FallbackValue` proprietà value viene impostata nella destinazione. Pertanto, nelle **MonkeyDetail** pagina il [ `Label` ](xref:Xamarin.Forms.Label) Visualizza "Dimensione della popolazione sconosciuto" perché manca l'oggetto associato un `Population` proprietà.

> [!IMPORTANT]
> Un convertitore di tipi di valore definito non viene eseguita in un'espressione di associazione quando il [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) è impostata.

## <a name="defining-a-null-replacement-value"></a>Definizione di un valore di sostituzione null

Il [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) proprietà consente di definire un valore di sostituzione che verrà utilizzato quando l'associazione *origine* viene risolto, ma il valore è `null`. Uno scenario comune per l'impostazione di questa proprietà è durante l'associazione a proprietà di origine che potrebbero essere `null` in una raccolta associata.

Il **esistono programmatori** pagina viene illustrata l'impostazione di [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) proprietà:

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

Le associazioni sul [ `Image` ](xref:Xamarin.Forms.Image) e [ `Label` ](xref:Xamarin.Forms.Label) definiscono entrambi [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) valori che verranno applicati se il percorso di associazione restituisce `null`. Pertanto, i valori definiti dal `TargetNullValue` verranno visualizzate le proprietà per tutti gli oggetti nella raccolta in cui il `ImageUrl` e `Location` proprietà non sono definite. Si noti che qui il `TargetNullValue` i valori delle proprietà sono delimitati da caratteri di virgoletta singola (apostrofo).

Invece di definire [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) valori di proprietà inline, si consiglia di definirli come risorse in un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Il vantaggio di questo approccio è che tali valori sono definiti una volta in un'unica posizione e sono più facilmente localizzabili. Le risorse possono quindi essere recuperate tramite il `StaticResource` estensione di markup:

```xaml
<Image Source="{Binding ImageUrl, TargetNullValue={StaticResource fallbackImageUrl}}"
       ... />
<Label Text="{Binding Location, TargetNullValue={StaticResource locationUnknown}}"
       ... />
```

> [!NOTE]
> Non è possibile impostare il `TargetNullValue` proprietà con un'espressione di associazione.

Ecco il programma in esecuzione:

[![Associazione TargetNullValue](binding-fallbacks-images/bindingunavailable-small.png "associazione TargetNullValue")](binding-fallbacks-images/bindingunavailable-large.png#lightbox "TargetNullValue associazione")

Quando la `TargetNullValue` non è impostata in un'espressione di associazione, un valore di origine di `null` verranno convertite se viene definito un convertitore di valori, formattati se un `StringFormat` è definito, e il risultato viene quindi impostato sulla destinazione. Tuttavia, quando la `TargetNullValue` è impostata, un valore di origine di `null` verrà convertito se è definito un convertitore di valori, e se è ancora `null` dopo la conversione, il valore della `TargetNullValue` viene impostata nella destinazione.

> [!IMPORTANT]
> Formattazione della stringa non viene applicata in un'espressione di associazione quando il `TargetNullValue` è impostata.

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
