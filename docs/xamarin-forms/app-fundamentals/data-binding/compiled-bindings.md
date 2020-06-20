---
title: Xamarin.FormsBinding compilati
description: Questo articolo illustra come usare le associazioni compilate per migliorare le prestazioni di data binding nelle Xamarin.Forms applicazioni.
ms.prod: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 094691796fed9653f2a2e468ccb1c33d1a408a49
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84571558"
---
# <a name="xamarinforms-compiled-bindings"></a>Xamarin.FormsBinding compilati

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_Le associazioni compilate vengono risolte più rapidamente rispetto alle associazioni classiche, migliorando pertanto le prestazioni data binding nelle Xamarin.Forms applicazioni._

I data binding presentano due problemi principali:

1. Non è prevista la convalida in fase di compilazione delle espressioni di binding. I binding vengono invece risolti in fase di runtime. Di conseguenza tutti i binding non validi non vengono rilevati fino alla fase di runtime, quando l'applicazione non si comporta come previsto o vengono visualizzati messaggi di errore.
1. Non sono efficienti a livelli di costi. I binding vengono risolti in fase di runtime mediante l'ispezione oggetti di carattere generale (reflection) e il carico di lavoro necessario varia a seconda della piattaforma.

Le associazioni compilate migliorano data binding le prestazioni nelle Xamarin.Forms applicazioni risolvendo le espressioni di associazione in fase di compilazione anziché in fase di esecuzione. Questa convalida delle espressioni di binding in fase di compilazione consente anche agli sviluppatori una risoluzione dei problemi più agevole, perché i binding non validi vengono segnalati come errori di compilazione.

Il processo per l'uso dei binding compilati è il seguente:

1. Abilitare la compilazione XAML. Per altre informazioni sulla compilazione XAML, vedere [Compilazione XAML](~/xamarin-forms/xaml/xamlc.md).
1. Impostare un `x:DataType` attributo su una proprietà sul [`VisualElement`](xref:Xamarin.Forms.VisualElement) tipo di oggetto `VisualElement` a cui si associa l'oggetto e i relativi elementi figlio.

> [!NOTE]
> È consigliabile impostare l' `x:DataType` attributo allo stesso livello nella gerarchia di visualizzazione in cui [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) è impostato. Tuttavia, questo attributo può essere ridefinito in qualsiasi posizione in una gerarchia di visualizzazione.

Per usare le associazioni compilate, l' `x:DataType` attributo deve essere impostato su un valore letterale stringa o un tipo che usa l' `x:Type` estensione di markup. In fase di compilazione XAML, le espressioni di binding non valide vengono segnalate come errori di compilazione. Tuttavia il compilatore XAML segnala un errore di compilazione solo per la prima espressione di binding non valida rilevata. Tutte le espressioni di associazione valide definite in `VisualElement` o nei relativi elementi figlio verranno compilate, indipendentemente dal fatto che [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) sia impostato in XAML o nel codice. La compilazione di un'espressione di binding genera codice compilato che ottiene un valore da una proprietà in *source*e lo imposta sulla proprietà in *target* specificata nel markup. Inoltre a seconda dell'espressione di binding il codice generato potrebbe rilevare modifiche nel valore della proprietà *source* e aggiornare la proprietà *target*, nonché eseguire il push delle modifiche dalla proprietà *target* alla proprietà *source*.

> [!IMPORTANT]
> Le associazioni compilate sono attualmente disabilitate per tutte le espressioni di associazione che definiscono la [`Source`](xref:Xamarin.Forms.Binding.Source) Proprietà. Il motivo è che la proprietà `Source` viene sempre impostata usando l'estensione di markup `x:Reference`, che non può essere risolta in fase di compilazione.

## <a name="use-compiled-bindings"></a>Usare binding compilati

La pagina del **selettore colori compilati** illustra l'uso di associazioni compilate tra Xamarin.Forms viste e proprietà ViewModel:

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

La radice [`StackLayout`](xref:Xamarin.Forms.StackLayout) Crea un'istanza di `HslColorViewModel` e inizializza la `Color` proprietà nei tag dell'elemento Property per la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) Proprietà. Questa radice `StackLayout` definisce anche l' `x:DataType` attributo come tipo ViewModel, a indicare che verranno compilate tutte le espressioni di associazione nella `StackLayout` gerarchia della visualizzazione radice. Questa operazione può essere verificata modificando una delle espressioni di associazione da associare a una proprietà ViewModel inesistente, causando un errore di compilazione. Questo esempio imposta l' `x:DataType` attributo su un valore letterale stringa, ma può anche essere impostato su un tipo con l' `x:Type` estensione di markup. Per ulteriori informazioni sull' `x:Type` estensione di markup, vedere [X:Type Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#xtype-markup-extension).

> [!IMPORTANT]
> L'attributo `x:DataType` può essere ridefinito in qualsiasi posizione di una gerarchia di visualizzazione.

Gli [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) elementi, e le [`Slider`](xref:Xamarin.Forms.Slider) viste ereditano il contesto di associazione da [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Queste viste sono tutte destinazioni di binding che fanno riferimento alle proprietà di origine nell'elemento ViewModel. Per la [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) proprietà e la [`Label.Text`](xref:Xamarin.Forms.Label.Text) proprietà, le associazioni dati sono `OneWay` : le proprietà della vista vengono impostate dalle proprietà nell'elemento ViewModel. Tuttavia, la [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) proprietà utilizza un' `TwoWay` associazione. In questo `Slider` modo è possibile impostare ogni elemento da ViewModel e anche per l'elemento ViewModel da impostare `Slider` .

Quando l'applicazione viene eseguita per la prima volta, gli elementi, [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) e [`Slider`](xref:Xamarin.Forms.Slider) sono tutti impostati dal ViewModel in base al `Color` set di proprietà iniziale quando è stata creata un'istanza di ViewModel. Ciò viene illustrato negli screenshot seguenti:

[![Selettore colori compilato](compiled-bindings-images/compiledcolorselector-small.png "Selettore colori compilato")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "Selettore colori compilato")

Quando vengono modificati i dispositivi di scorrimento, gli [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) elementi e vengono aggiornati di conseguenza.

Per altre informazioni su questo selettore colori, vedere [ViewModels and Property-Change Notifications](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications) (ViewModel e notifiche di modifica delle proprietà).

## <a name="use-compiled-bindings-in-a-datatemplate"></a>Usare binding compilati in un oggetto DataTemplate

Le associazioni in un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) vengono interpretate nel contesto dell'oggetto basato su modelli. Pertanto quando si usano binding compilati in un `DataTemplate`, `DataTemplate` deve dichiarare il tipo del relativo oggetto dati usando l'attributo `x:DataType`.

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

La [`ListView.ItemsSource`](xref:Xamarin.Forms.ListView) proprietà viene impostata sulla proprietà statica `NamedColor.All` . La `NamedColor` classe usa la reflection .NET per enumerare tutti i campi pubblici statici nella [`Color`](xref:Xamarin.Forms.Color) struttura e per archiviarli con i relativi nomi in una raccolta accessibile dalla `All` proprietà statica. Di conseguenza l'elemento `ListView` viene riempito con tutte le istanze `NamedColor`. Per ogni elemento in `ListView` il contesto di binding per l'elemento è impostato su un oggetto `NamedColor`. Gli [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) elementi e in [`ViewCell`](xref:Xamarin.Forms.ViewCell) sono associati alle `NamedColor` Proprietà.

Si noti che [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definisce l' `x:DataType` attributo come `NamedColor` tipo, a indicare che tutte le espressioni di associazione nella `DataTemplate` gerarchia di visualizzazione verranno compilate. È possibile verificare questa impostazione modificando le espressioni di binding per impostare il binding a una proprietà `NamedColor` inesistente, che produrrà un errore di compilazione.  Questo esempio imposta l' `x:DataType` attributo su un valore letterale stringa, ma può anche essere impostato su un tipo con l' `x:Type` estensione di markup. Per ulteriori informazioni sull' `x:Type` estensione di markup, vedere [X:Type Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#xtype-markup-extension).

Quando l'applicazione viene eseguita per la prima volta, [`ListView`](xref:Xamarin.Forms.ListView) viene popolata con le `NamedColor` istanze di. Quando si seleziona un elemento in `ListView` , la [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) proprietà viene impostata sul colore dell'elemento selezionato in `ListView` :

[![Elenco colori compilato](compiled-bindings-images/compiledcolorlist-small.png "Elenco colori compilato]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Elenco colori compilato")

La selezione di altri elementi in [`ListView`](xref:Xamarin.Forms.BoxView) Aggiorna il colore di [`BoxView`](xref:Xamarin.Forms.BoxView) .

## <a name="combine-compiled-bindings-with-classic-bindings"></a>Combinare binding compilati con associazioni classiche

Le espressioni di binding vengono compilate solo per la gerarchia di visualizzazione in cui è definito l'attributo `x:DataType`. Al contrario, tutte le viste in una gerarchia in cui l'attributo `x:DataType` non è definito usa i binding classici. È pertanto possibile combinare binding compilati e classici in una pagina. Nella sezione precedente, ad esempio, le visualizzazioni all'interno di [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) utilizzano le associazioni compilate, mentre l'oggetto [`BoxView`](xref:Xamarin.Forms.BoxView) impostato sul colore selezionato in [`ListView`](xref:Xamarin.Forms.ListView) non lo è.

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

La radice [`StackLayout`](xref:Xamarin.Forms.StackLayout) imposta l' `x:DataType` attributo come `HslColorViewModel` tipo, a indicare che qualsiasi espressione di associazione nella gerarchia della `StackLayout` visualizzazione radice verrà compilata. Tuttavia l'elemento `StackLayout` interno ridefinisce l'attributo `x:DataType` come `null` con l'espressione di markup `x:Null`. Di conseguenza le espressioni di binding nell'elemento `StackLayout` interno usano i binding classici. Solo all' [`BoxView`](xref:Xamarin.Forms.BoxView) interno della gerarchia di `StackLayout` visualizzazione radice utilizza associazioni compilate.

Per altre informazioni sull'espressione di markup `x:Null`, vedere [Estensione di markup x:Null](~/xamarin-forms/xaml/markup-extensions/consuming.md#xnull-markup-extension).

## <a name="performance"></a>Prestazioni

I binding compilati migliorano le prestazioni di data binding e il vantaggio in termini di prestazioni può variare. Gli unit test rivelano che:

- Un binding compilato che usa la notifica di modifica delle proprietà (ovvero un binding `OneWay`, `OneWayToSource` o `TwoWay`) viene risolto circa 8 volte più rapidamente rispetto a un binding classico.
- Un binding compilato che non usa la notifica di modifica delle proprietà (ovvero un binding `OneTime`) viene risolto circa 20 volte più rapidamente rispetto a un binding classico.
- L'impostazione di [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) su un'associazione compilata che utilizza una notifica di modifica della proprietà (ad esempio `OneWay` , un' `OneWayToSource` associazione, o `TwoWay` ) è di circa 5 volte più veloce rispetto all'impostazione di `BindingContext` su un'associazione classica.
- L'impostazione di [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) su un'associazione compilata che non usa la notifica delle modifiche delle proprietà (ad esempio un' `OneTime` associazione) è circa 7 volte più veloce rispetto all'impostazione di `BindingContext` su un'associazione classica.

Queste variazioni di prestazioni possono risultare più importanti nei dispositivi mobili a seconda della piattaforma usata, della versione del sistema operativo e del dispositivo in cui viene eseguita l'applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demo di data binding)
