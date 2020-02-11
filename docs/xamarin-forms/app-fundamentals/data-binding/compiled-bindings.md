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
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697151"
---
# <a name="xamarinforms-compiled-bindings"></a>Binding compilati in Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_I binding compilati vengono risolti più rapidamente rispetto ai binding classici, migliorando pertanto le prestazioni di data binding nelle applicazioni Xamarin.Forms._

I data binding presentano due problemi principali:

1. Non è prevista la convalida in fase di compilazione delle espressioni di binding. I binding vengono invece risolti in fase di runtime. Di conseguenza tutti i binding non validi non vengono rilevati fino alla fase di runtime, quando l'applicazione non si comporta come previsto o vengono visualizzati messaggi di errore.
1. Non sono efficienti a livelli di costi. I binding vengono risolti in fase di runtime mediante l'ispezione oggetti di carattere generale (reflection) e il carico di lavoro necessario varia a seconda della piattaforma.

I binding compilati migliorano le prestazioni di data binding nelle applicazioni Xamarin.Forms risolvendo le espressioni di binding in fase di compilazione anziché in fase di runtime. Questa convalida delle espressioni di binding in fase di compilazione consente anche agli sviluppatori una risoluzione dei problemi più agevole, perché i binding non validi vengono segnalati come errori di compilazione.

Il processo per l'uso dei binding compilati è il seguente:

1. Abilitare la compilazione XAML. Per altre informazioni sulla compilazione XAML, vedere [Compilazione XAML](~/xamarin-forms/xaml/xamlc.md).
1. Impostare un attributo `x:DataType` in un [`VisualElement`](xref:Xamarin.Forms.VisualElement) sul tipo dell'oggetto al quale `VisualElement` e gli elementi figlio eseguiranno il binding.

> [!NOTE]
> È consigliabile impostare l'attributo `x:DataType` sullo stesso livello della gerarchia di visualizzazione sul quale è impostato [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext). Tuttavia, questo attributo può essere ridefinito in qualsiasi posizione in una gerarchia di visualizzazione.

Per usare le associazioni compilate, l'attributo `x:DataType` deve essere impostato su un valore letterale stringa o un tipo che usa l'estensione di markup `x:Type`. In fase di compilazione XAML, le espressioni di binding non valide vengono segnalate come errori di compilazione. Tuttavia il compilatore XAML segnala un errore di compilazione solo per la prima espressione di binding non valida rilevata. Tutte le espressioni di binding valido definite in `VisualElement` o negli elementi figlio verranno compilate, indipendentemente dal fatto che [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) sia impostato in XAML o nel codice. La compilazione di un'espressione di binding genera codice compilato che ottiene un valore da una proprietà in *source*e lo imposta sulla proprietà in *target* specificata nel markup. Inoltre a seconda dell'espressione di binding il codice generato potrebbe rilevare modifiche nel valore della proprietà *source* e aggiornare la proprietà *target*, nonché eseguire il push delle modifiche dalla proprietà *target* alla proprietà *source*.

> [!IMPORTANT]
> I binding compilati sono attualmente disabilitati per le espressioni di binding che definiscono la proprietà [`Source`](xref:Xamarin.Forms.Binding.Source). Il motivo è che la proprietà `Source` viene sempre impostata usando l'estensione di markup `x:Reference`, che non può essere risolta in fase di compilazione.

## <a name="use-compiled-bindings"></a>Usare binding compilati

La pagina di **selezione colori compilata** illustra l'uso di associazioni compilate tra le visualizzazioni Xamarin.Forms e le proprietà ViewModel:

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

L'elemento radice [`StackLayout`](xref:Xamarin.Forms.StackLayout) crea un'istanza di `HslColorViewModel` e inizializza la proprietà `Color` all'interno dei tag proprietà dell'elemento per la proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext). Questa `StackLayout` radice definisce anche l'attributo `x:DataType` come tipo ViewModel, a indicare che verranno compilate tutte le espressioni di associazione nella gerarchia di visualizzazione `StackLayout` radice. Questa operazione può essere verificata modificando una delle espressioni di associazione da associare a una proprietà ViewModel inesistente, causando un errore di compilazione. Questo esempio imposta l'attributo `x:DataType` su un valore letterale stringa, ma può anche essere impostato su un tipo con l'estensione di markup `x:Type`. Per ulteriori informazioni sull'estensione di markup `x:Type`, vedere [X:Type Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#type).

> [!IMPORTANT]
> L'attributo `x:DataType` può essere ridefinito in qualsiasi posizione di una gerarchia di visualizzazione.

Gli elementi [`BoxView`](xref:Xamarin.Forms.BoxView) e [`Label`](xref:Xamarin.Forms.Label) e le viste [`Slider`](xref:Xamarin.Forms.Slider) ereditano il contesto di binding da [`StackLayout`](xref:Xamarin.Forms.StackLayout). Queste viste sono tutte destinazioni di binding che fanno riferimento alle proprietà di origine nell'elemento ViewModel. Per la proprietà [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) e la proprietà [`Label.Text`](xref:Xamarin.Forms.Label.Text) , le associazioni dati sono `OneWay`: le proprietà della vista vengono impostate dalle proprietà nell'elemento ViewModel. Tuttavia la proprietà [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) usa un binding `TwoWay`. In questo modo è possibile impostare ogni `Slider` dal ViewModel e anche per impostare ViewModel da ogni `Slider`.

Quando l'applicazione viene eseguita per la prima volta, gli elementi [`BoxView`](xref:Xamarin.Forms.BoxView), [`Label`](xref:Xamarin.Forms.Label) e [`Slider`](xref:Xamarin.Forms.Slider) vengono tutti impostati dal ViewModel in base alla proprietà `Color` iniziale impostata quando è stata creata un'istanza di ViewModel. Questo è illustrato negli screenshot seguenti:

[![Selettore colori compilato](compiled-bindings-images/compiledcolorselector-small.png "Selettore colori compilato")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "Selettore colori compilato")

Se si azionano i dispositivi di scorrimento, gli elementi [`BoxView`](xref:Xamarin.Forms.BoxView) e [`Label`](xref:Xamarin.Forms.Label) vengono aggiornati di conseguenza.

Per altre informazioni su questo selettore colori, vedere [ViewModels and Property-Change Notifications](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications) (ViewModel e notifiche di modifica delle proprietà).

## <a name="use-compiled-bindings-in-a-datatemplate"></a>Usare binding compilati in un oggetto DataTemplate

I binding in un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) sono interpretati nel contesto dell'oggetto basato su modelli. Pertanto quando si usano binding compilati in un `DataTemplate`, `DataTemplate` deve dichiarare il tipo del relativo oggetto dati usando l'attributo `x:DataType`.

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

La proprietà [`ListView.ItemsSource`](xref:Xamarin.Forms.ListView) è impostata sulla proprietà `NamedColor.All` statica. La classe `NamedColor` usa la reflection .NET per enumerare tutti i campi pubblici statici nella struttura [`Color`](xref:Xamarin.Forms.Color) e archiviarli con i relativi nomi in una raccolta accessibile dalla proprietà `All` statica. Di conseguenza l'elemento `ListView` viene riempito con tutte le istanze `NamedColor`. Per ogni elemento in `ListView` il contesto di binding per l'elemento è impostato su un oggetto `NamedColor`. Gli elementi [`BoxView`](xref:Xamarin.Forms.BoxView) e [`Label`](xref:Xamarin.Forms.Label) in [`ViewCell`](xref:Xamarin.Forms.ViewCell) sono associati a proprietà `NamedColor`.

Si noti che [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definisce l'attributo `x:DataType` come tipo `NamedColor`, a indicare che tutte le espressioni di binding nella gerarchia di visualizzazione `DataTemplate` verranno compilate. È possibile verificare questa impostazione modificando le espressioni di binding per impostare il binding a una proprietà `NamedColor` inesistente, che produrrà un errore di compilazione.  Questo esempio imposta l'attributo `x:DataType` su un valore letterale stringa, ma può anche essere impostato su un tipo con l'estensione di markup `x:Type`. Per ulteriori informazioni sull'estensione di markup `x:Type`, vedere [X:Type Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#type).

Quando l'applicazione viene eseguita per la prima volta, [`ListView`](xref:Xamarin.Forms.ListView) viene popolata con istanze `NamedColor`. Quando viene selezionato un elemento in `ListView`, la proprietà [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) viene impostata sul colore dell'elemento selezionato in `ListView`:

[![Elenco colori compilato](compiled-bindings-images/compiledcolorlist-small.png "Elenco colori compilato]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Compiled Color List")

Se si selezionano altri elementi in [`ListView`](xref:Xamarin.Forms.BoxView) viene aggiornato il colore di [`BoxView`](xref:Xamarin.Forms.BoxView).

## <a name="combine-compiled-bindings-with-classic-bindings"></a>Combinare binding compilati con associazioni classiche

Le espressioni di binding vengono compilate solo per la gerarchia di visualizzazione in cui è definito l'attributo `x:DataType`. Al contrario, tutte le viste in una gerarchia in cui l'attributo `x:DataType` non è definito usa i binding classici. È pertanto possibile combinare binding compilati e classici in una pagina. Ad esempio nella sezione precedente le viste in [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) usano i binding compilati, mentre l'elemento [`BoxView`](xref:Xamarin.Forms.BoxView) impostato sul colore selezionato in [`ListView`](xref:Xamarin.Forms.ListView) non usa tali binding.

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

L'elemento radice [`StackLayout`](xref:Xamarin.Forms.StackLayout) definisce l'attributo `x:DataType` come tipo `HslColorViewModel`, a indicare che tutte le espressioni di binding nella gerarchia di visualizzazione radice `StackLayout` verranno compilate. Tuttavia l'elemento `StackLayout` interno ridefinisce l'attributo `x:DataType` come `null` con l'espressione di markup `x:Null`. Di conseguenza le espressioni di binding nell'elemento `StackLayout` interno usano i binding classici. Solo [`BoxView`](xref:Xamarin.Forms.BoxView) nell'ambito della gerarchia di visualizzazione radice `StackLayout` usa i binding compilati.

Per altre informazioni sull'espressione di markup `x:Null`, vedere [Estensione di markup x:Null](~/xamarin-forms/xaml/markup-extensions/consuming.md#null).

## <a name="performance"></a>Prestazioni

I binding compilati migliorano le prestazioni di data binding e il vantaggio in termini di prestazioni può variare. Gli unit test rivelano che:

- Un binding compilato che usa la notifica di modifica delle proprietà (ovvero un binding `OneWay`, `OneWayToSource` o `TwoWay`) viene risolto circa 8 volte più rapidamente rispetto a un binding classico.
- Un binding compilato che non usa la notifica di modifica delle proprietà (ovvero un binding `OneTime`) viene risolto circa 20 volte più rapidamente rispetto a un binding classico.
- L'impostazione di [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) per un binding compilato che usa la notifica di modifica delle proprietà (ovvero un binding `OneWay`, `OneWayToSource` o `TwoWay`) è circa 5 volte più rapida rispetto all'impostazione di `BindingContext` per un binding classico.
- L'impostazione di [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) per un binding compilato che non usa la notifica di modifica delle proprietà (ovvero un binding `OneTime`) è circa 7 volte più rapida rispetto all'impostazione di `BindingContext` per un binding classico.

Queste variazioni di prestazioni possono risultare più importanti nei dispositivi mobili a seconda della piattaforma usata, della versione del sistema operativo e del dispositivo in cui viene eseguita l'applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demo di data binding)
