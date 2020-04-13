---
title: Binding compilati in Xamarin.Forms
description: Questo articolo illustra come usare i binding compilati per migliorare le prestazioni di data binding nelle applicazioni Xamarin.Forms.
ms.prod: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2019
ms.openlocfilehash: 531d9719eb4bf5c23001ebe4260254e13f9989eb
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "72697151"
---
# <a name="xamarinforms-compiled-bindings"></a>Binding compilati in Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_I binding compilati vengono risolti più rapidamente rispetto ai binding classici, migliorando pertanto le prestazioni di data binding nelle applicazioni Xamarin.Forms._

I data binding presentano due problemi principali:

1. Non è prevista la convalida in fase di compilazione delle espressioni di binding. I binding vengono invece risolti in fase di runtime. Di conseguenza tutti i binding non validi non vengono rilevati fino alla fase di runtime, quando l'applicazione non si comporta come previsto o vengono visualizzati messaggi di errore.
1. Non sono efficienti a livelli di costi. I binding vengono risolti in fase di runtime mediante l'ispezione oggetti di carattere generale (reflection) e il carico di lavoro necessario varia a seconda della piattaforma.

I binding compilati migliorano le prestazioni di data binding nelle applicazioni Xamarin.Forms risolvendo le espressioni di binding in fase di compilazione anziché in fase di runtime. Questa convalida delle espressioni di binding in fase di compilazione consente anche agli sviluppatori una risoluzione dei problemi più agevole, perché i binding non validi vengono segnalati come errori di compilazione.

Il processo per l'uso dei binding compilati è il seguente:

1. Abilitare la compilazione XAML. Per altre informazioni sulla compilazione XAML, vedere [Compilazione XAML](~/xamarin-forms/xaml/xamlc.md).
1. Impostare `x:DataType` un [`VisualElement`](xref:Xamarin.Forms.VisualElement) attributo su a sul `VisualElement` tipo dell'oggetto a cui verranno associati gli elementi figlio e .

> [!NOTE]
> È consigliabile impostare `x:DataType` l'attributo allo stesso livello [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) nella gerarchia di visualizzazione dell'oggetto impostato. Tuttavia, questo attributo può essere ridefinito in qualsiasi posizione in una gerarchia di visualizzazione.

Per utilizzare le associazioni `x:DataType` compilate, l'attributo deve essere `x:Type` impostato su un valore letterale stringa o su un tipo utilizzando l'estensione di markup. In fase di compilazione XAML, le espressioni di binding non valide vengono segnalate come errori di compilazione. Tuttavia il compilatore XAML segnala un errore di compilazione solo per la prima espressione di binding non valida rilevata. Tutte le espressioni di associazione valide definite nell'elemento `VisualElement` figlio [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) o in quello relativo verranno compilate, indipendentemente dal fatto che l'oggetto sia impostato in XAML o nel codice. La compilazione di un'espressione di binding genera codice compilato che ottiene un valore da una proprietà in *source*e lo imposta sulla proprietà in *target* specificata nel markup. Inoltre a seconda dell'espressione di binding il codice generato potrebbe rilevare modifiche nel valore della proprietà *source* e aggiornare la proprietà *target*, nonché eseguire il push delle modifiche dalla proprietà *target* alla proprietà *source*.

> [!IMPORTANT]
> Le associazioni compilate sono attualmente disabilitate [`Source`](xref:Xamarin.Forms.Binding.Source) per tutte le espressioni di associazione che definiscono la proprietà. Il motivo è che la proprietà `Source` viene sempre impostata usando l'estensione di markup `x:Reference`, che non può essere risolta in fase di compilazione.

## <a name="use-compiled-bindings"></a>Usare binding compilati

Nella pagina **Selettore colori compilato** viene illustrato l'utilizzo delle associazioni compilate tra le visualizzazioni Xamarin.Forms e le proprietà viewmodel:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorSelectorPage"
             Title="Compiled Color Selector">
    ...
    <StackLayout x:DataType="local:HslColorViewModel">
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>
        <BoxView Color="{Binding Color}"
                 ... />
        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />
            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>    
</ContentPage>
```

La [`StackLayout`](xref:Xamarin.Forms.StackLayout) radice crea `HslColorViewModel` un'istanza `Color` di e inizializza [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) la proprietà all'interno dei tag degli elementi proprietà per la proprietà. Questa `StackLayout` radice definisce `x:DataType` anche l'attributo come tipo viewmodel, `StackLayout` che indica che tutte le espressioni di associazione nella gerarchia della visualizzazione radice verranno compilate. Questo può essere verificato modificando una qualsiasi delle espressioni di associazione da associare a una proprietà viewmodel inesistente, che genererà un errore di compilazione. Mentre in questo `x:DataType` esempio imposta l'attributo su un valore `x:Type` letterale stringa, può anche essere impostato su un tipo con l'estensione di markup. Per ulteriori informazioni `x:Type` sull'estensione di markup, vedere [x:Type Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#type).

> [!IMPORTANT]
> L'attributo `x:DataType` può essere ridefinito in qualsiasi posizione di una gerarchia di visualizzazione.

Gli [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) elementi , [`Slider`](xref:Xamarin.Forms.Slider) e le visualizzazioni [`StackLayout`](xref:Xamarin.Forms.StackLayout)ereditano il contesto di associazione dall'oggetto . Queste visualizzazioni sono tutte destinazioni di associazione che fanno riferimento alle proprietà di origine nel viewmodel. Per [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) la proprietà [`Label.Text`](xref:Xamarin.Forms.Label.Text) e la proprietà, `OneWay` le associazioni dati sono : le proprietà nella visualizzazione vengono impostate dalle proprietà nel viewmodel. Tuttavia, [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) la `TwoWay` proprietà utilizza un'associazione. In questo `Slider` modo ciascunmodello viene impostato dal viewmodel e anche `Slider`per il viewmodel da ogni .

Quando l'applicazione viene [`BoxView`](xref:Xamarin.Forms.BoxView)eseguita per [`Slider`](xref:Xamarin.Forms.Slider) la prima volta, gli elementi `Color` e gli elementi , [`Label`](xref:Xamarin.Forms.Label) vengono tutti impostati dal viewmodel in base alla proprietà iniziale impostata quando è stata creata un'istanza del viewmodel. Ciò viene illustrato negli screenshot seguenti:

[![Selettore colore compilato](compiled-bindings-images/compiledcolorselector-small.png "Selettore colore compilato")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "Selettore colore compilato")

Quando i cursori vengono [`BoxView`](xref:Xamarin.Forms.BoxView) manipolati, gli elementi e [`Label`](xref:Xamarin.Forms.Label) vengono aggiornati di conseguenza.

Per altre informazioni su questo selettore colori, vedere [ViewModels and Property-Change Notifications](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications) (ViewModel e notifiche di modifica delle proprietà).

## <a name="use-compiled-bindings-in-a-datatemplate"></a>Usare le associazioni compilate in un DataTemplateUse compiled bindings in a DataTemplate

Le associazioni [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) in a vengono interpretate nel contesto dell'oggetto basato su modelli. Pertanto quando si usano binding compilati in un `DataTemplate`, `DataTemplate` deve dichiarare il tipo del relativo oggetto dati usando l'attributo `x:DataType`.

La pagina **Compiled Color List** (Elenco colori compilato) visualizza come usare i binding compilati in un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorListPage"
             Title="Compiled Color List">
    <Grid>
        ...
        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  ... >
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="local:NamedColor">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     ... />
                            <Label Text="{Binding FriendlyName}"
                                   ... />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
        <!-- The BoxView doesn't use compiled bindings -->
        <BoxView Color="{Binding Source={x:Reference colorListView}, Path=SelectedItem.Color}"
                 ... />
    </Grid>
</ContentPage>
```

La [`ListView.ItemsSource`](xref:Xamarin.Forms.ListView) proprietà è impostata sulla proprietà statica. `NamedColor.All` La `NamedColor` classe utilizza la reflection .NET per [`Color`](xref:Xamarin.Forms.Color) enumerare tutti i campi pubblici statici nella struttura `All` e per archiviarli con i relativi nomi in una raccolta accessibile dalla proprietà statica. Di conseguenza l'elemento `ListView` viene riempito con tutte le istanze `NamedColor`. Per ogni elemento in `ListView` il contesto di binding per l'elemento è impostato su un oggetto `NamedColor`. Gli [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) elementi e [`ViewCell`](xref:Xamarin.Forms.ViewCell) in `NamedColor` sono associati alle proprietà.

Si noti [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che `x:DataType` l'attributo definisce il `NamedColor` tipo, `DataTemplate` che indica che tutte le espressioni di associazione nella gerarchia di visualizzazione verranno compilate. È possibile verificare questa impostazione modificando le espressioni di binding per impostare il binding a una proprietà `NamedColor` inesistente, che produrrà un errore di compilazione.  Mentre in questo `x:DataType` esempio imposta l'attributo su un valore `x:Type` letterale stringa, può anche essere impostato su un tipo con l'estensione di markup. Per ulteriori informazioni `x:Type` sull'estensione di markup, vedere [x:Type Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#type).

Quando l'applicazione viene [`ListView`](xref:Xamarin.Forms.ListView) eseguita `NamedColor` per la prima volta, l'oggetto viene popolato con istanze. Quando `ListView` viene selezionato un elemento [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) in `ListView`, la proprietà viene impostata sul colore dell'elemento selezionato nel campo :

[![Elenco colori compilati](compiled-bindings-images/compiledcolorlist-small.png "Elenco colori compilati]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Elenco colori compilati")

La selezione di [`ListView`](xref:Xamarin.Forms.BoxView) altri elementi [`BoxView`](xref:Xamarin.Forms.BoxView)nell'aggiornamento viene aggiornato con il colore del file .

## <a name="combine-compiled-bindings-with-classic-bindings"></a>Combinare le associazioni compilate con associazioni classicheCombine compiled bindings with classic bindings

Le espressioni di binding vengono compilate solo per la gerarchia di visualizzazione in cui è definito l'attributo `x:DataType`. Al contrario, tutte le viste in una gerarchia in cui l'attributo `x:DataType` non è definito usa i binding classici. È pertanto possibile combinare binding compilati e classici in una pagina. Ad esempio, nella sezione precedente [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) le visualizzazioni all'interno delle associazioni compilate use, mentre l'oggetto [`BoxView`](xref:Xamarin.Forms.BoxView) che è impostato sul colore selezionato in [`ListView`](xref:Xamarin.Forms.ListView) non lo fa.

Un'attenta strutturazione degli attributi `x:DataType` può dare origine a una pagina che usa binding sia compilati che classici. In alternativa l'attributo `x:DataType` può essere ridefinito in qualsiasi punto di una gerarchia di visualizzazione come `null` usando l'estensione di markup `x:Null`. Questa operazione indica che tutte le espressioni di binding all'interno della gerarchia di visualizzazione useranno i binding classici. La pagina *Mixed Bindings* (Binding misti) dimostra questo approccio:

```xaml
<StackLayout x:DataType="local:HslColorViewModel">
    <StackLayout.BindingContext>
        <local:HslColorViewModel Color="Sienna" />
    </StackLayout.BindingContext>
    <BoxView Color="{Binding Color}"
             VerticalOptions="FillAndExpand" />
    <StackLayout x:DataType="{x:Null}"
                 Margin="10, 0">
        <Label Text="{Binding Name}" />
        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</StackLayout>   
```

La [`StackLayout`](xref:Xamarin.Forms.StackLayout) radice `x:DataType` imposta l'attributo come `HslColorViewModel` tipo, a indicare che qualsiasi espressione di associazione nella gerarchia della visualizzazione radice `StackLayout` verrà compilata. Tuttavia l'elemento `StackLayout` interno ridefinisce l'attributo `x:DataType` come `null` con l'espressione di markup `x:Null`. Di conseguenza le espressioni di binding nell'elemento `StackLayout` interno usano i binding classici. Solo [`BoxView`](xref:Xamarin.Forms.BoxView), all'interno della gerarchia della visualizzazione radice, `StackLayout` utilizza le associazioni compilate.

Per altre informazioni sull'espressione di markup `x:Null`, vedere [Estensione di markup x:Null](~/xamarin-forms/xaml/markup-extensions/consuming.md#null).

## <a name="performance"></a>Prestazioni

I binding compilati migliorano le prestazioni di data binding e il vantaggio in termini di prestazioni può variare. Gli unit test rivelano che:

- Un binding compilato che usa la notifica di modifica delle proprietà (ovvero un binding `OneWay`, `OneWayToSource` o `TwoWay`) viene risolto circa 8 volte più rapidamente rispetto a un binding classico.
- Un binding compilato che non usa la notifica di modifica delle proprietà (ovvero un binding `OneTime`) viene risolto circa 20 volte più rapidamente rispetto a un binding classico.
- L'impostazione [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) di un'associazione compilata che utilizza `OneWay` `OneWayToSource`la `TwoWay` notifica di modifica delle proprietà (ad esempio, un'associazione , o un'associazione) è circa 5 volte più rapida rispetto all'impostazione `BindingContext` di un'associazione classica.
- L'impostazione di [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) un'associazione compilata che non usa `OneTime` la notifica di modifica delle proprietà `BindingContext` (ad esempio un'associazione) è circa 7 volte più veloce rispetto all'impostazione di un'associazione classica.

Queste variazioni di prestazioni possono risultare più importanti nei dispositivi mobili a seconda della piattaforma usata, della versione del sistema operativo e del dispositivo in cui viene eseguita l'applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demo di data binding)
