---
title: Xamarin. Forms compilate le associazioni
description: Questo articolo illustra come usare le associazioni compilate per migliorare le prestazioni di associazione dati nelle applicazioni xamarin. Forms.
ms.prod: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2018
ms.openlocfilehash: 0b350082c834076a1d69427644259087d64bf26a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111608"
---
# <a name="xamarinforms-compiled-bindings"></a>Xamarin. Forms compilate le associazioni

_Le associazioni compilate vengono risolti più rapidamente rispetto a binding classico, migliorando pertanto le prestazioni di associazione dati nelle applicazioni xamarin. Forms._

Le associazioni dati sono due problemi principali:

1. Non è prevista alcuna convalida in fase di compilazione di espressioni di associazione. Al contrario, le associazioni vengono risolte in fase di esecuzione. Di conseguenza, tutti i binding non validi non vengono rivelati fino alla fase di esecuzione quando l'applicazione non si comporta come previsto o i messaggi di errore vengono visualizzati.
1. Non sono economicamente efficiente. Le associazioni vengono risolti in fase di esecuzione tramite verifica degli oggetti per utilizzo generico (reflection) e l'overhead di questa operazione varia a seconda della piattaforma.

Le associazioni compilate migliorare le prestazioni di associazione dati nelle applicazioni xamarin. Forms risolvendo le espressioni di associazione in fase di compilazione anziché di runtime. Inoltre, la convalida in fase di compilazione di espressioni di associazione consente agli sviluppatori una migliore risoluzione ottimale dei problemi perché associazioni non valide vengono segnalate come errori di compilazione.

Il processo per l'uso di associazioni compilate consiste nel:

1. Abilitare la compilazione XAML. Per altre informazioni sulla compilazione XAML, vedere [compilazione XAML](~/xamarin-forms/xaml/xamlc.md).
1. Impostare un' `x:DataType` dell'attributo su una [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) per il tipo dell'oggetto che il `VisualElement` e i relativi elementi figlio verrà associato a. Si noti che questo attributo può essere definito nuovamente in qualsiasi posizione in una gerarchia di visualizzazione.

> [!NOTE]
> Si consiglia di impostare il `x:DataType` attributo allo stesso livello nella gerarchia di visualizzazione come i [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) è impostata.

In fase di compilazione XAML, tutte le espressioni di associazione non valida. verranno segnalate come errori di compilazione. Tuttavia, il compilatore XAML solo segnalerà un errore di compilazione per la prima espressione di associazione non valida rilevata. Tutte le espressioni di binding valido che sono definite nel `VisualElement` o i relativi elementi figlio verranno compilate, indipendentemente dal fatto che il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) è impostato in XAML o codice. La compilazione di un'espressione di associazione che genera l'errore codice compilato che verrà visualizzato un valore da una proprietà nel *origine*e impostata sulla proprietà per il *destinazione* specificato nel markup. Inoltre, in base all'espressione di associazione, il codice generato potrebbe osservare le modifiche al valore del *origine* proprietà e l'aggiornamento di *destinazione* proprietà e può eseguire il push delle modifiche dal *destinazione* verso la *origine*.

> [!IMPORTANT]
> Le associazioni compilate sono attualmente disabilitate per le espressioni di associazione che definiscono i [ `Source` ](xref:Xamarin.Forms.Binding.Source) proprietà. Infatti il `Source` viene sempre impostata usando i `x:Reference` estensione di markup, che non può essere risolto in fase di compilazione.

## <a name="using-compiled-bindings"></a>Uso di associazioni compilate

Il **compilato selettore colori** pagina viene illustrato come utilizzare le associazioni compilate tra le visualizzazioni di xamarin. Forms e proprietà ViewModel:

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

La radice [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) crea un'istanza di `HslColorViewModel` e inizializza il `Color` proprietà all'interno dei tag di elemento di proprietà per il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) proprietà. Questa radice `StackLayout` definisce anche il `x:DataType` dell'attributo come tipo di elemento ViewModel, che indica che tutte le espressioni di associazione nella radice `StackLayout` gerarchia di visualizzazione verrà compilata. Ciò può essere verificato modificando le espressioni di associazione per l'associazione a una proprietà ViewModel inesistenti, che verrà generato un errore di compilazione.

> [!IMPORTANT]
> Il `x:DataType` attributo può essere ridefinito in qualsiasi punto in una gerarchia di visualizzazione.

Il [ `BoxView` ](xref:Xamarin.Forms.BoxView), [ `Label` ](xref:Xamarin.Forms.Label) elementi, e [ `Slider` ](xref:Xamarin.Forms.Slider) viste ereditano il contesto di associazione dal [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Queste viste sono tutte le destinazioni di associazione che fanno riferimento a proprietà di origine nel ViewModel. Per il [ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color) proprietà e il [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) sono di proprietà, le associazioni dati `OneWay` : le proprietà nella vista vengono impostate dalle proprietà nel ViewModel. Tuttavia, il [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) utilizzata dalla proprietà un `TwoWay` associazione. In questo modo ognuna `Slider` da impostare da ViewModel, nonché per l'elemento ViewModel da impostare da ogni `Slider`.

Quando l'applicazione viene eseguita prima di tutto, il [ `BoxView` ](xref:Xamarin.Forms.BoxView), [ `Label` ](xref:Xamarin.Forms.Label) elementi, e [ `Slider` ](xref:Xamarin.Forms.Slider) elementi sono tutti impostati dal ViewModel basata iniziale `Color` proprietà impostata quando è stata creata un'istanza l'elemento ViewModel. Come illustrato negli screenshot seguenti:

[![Compilato selettore colori](compiled-bindings-images/compiledcolorselector-small.png "compilate selettore colori")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "compilato selettore colori")

Come vengono modificati i dispositivi di scorrimento, il [ `BoxView` ](xref:Xamarin.Forms.BoxView) e [ `Label` ](xref:Xamarin.Forms.Label) gli elementi vengono aggiornati di conseguenza.

Per altre informazioni su questo selettore di colore, vedere [ViewModel e le notifiche di modifica delle proprietà](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications).

## <a name="using-compiled-bindings-in-a-datatemplate"></a>Uso di associazioni compilate in un DataTemplate

Le associazioni in un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) vengono interpretati nel contesto dell'oggetto basato su modelli. Pertanto, quando tramite compilata associazioni in un `DataTemplate`, il `DataTemplate` è necessario dichiarare il tipo del relativo oggetto dati usando il `x:DataType` attributo.

Il **elenco di colori compilato** pagina viene illustrato come utilizzare le associazioni compilate in un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

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

Il [ `ListView.ItemsSource` ](xref:Xamarin.Forms.ListView) è impostata su statico `NamedColor.All` proprietà. Il `NamedColor` classe utilizza la reflection .NET per enumerare tutti i campi pubblici statici nel [ `Color` ](xref:Xamarin.Forms.Color) struttura e per l'archiviazione con i relativi nomi in una raccolta che è accessibile da statico `All` proprietà. Pertanto, il `ListView` viene riempita con tutte le `NamedColor` istanze. Per ogni elemento di `ListView`, il contesto di associazione per l'elemento è impostato su un `NamedColor` oggetto. Il [ `BoxView` ](xref:Xamarin.Forms.BoxView) e [ `Label` ](xref:Xamarin.Forms.Label) elementi il [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) sono associati a `NamedColor` proprietà.

Si noti che il [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) definisce il `x:DataType` attributo sia il `NamedColor` tipo, che indica che qualsiasi nelle espressioni di associazione la `DataTemplate` gerarchia di visualizzazione verrà compilata. Ciò può essere verificato modificando le espressioni di associazione per l'associazione a un inesistenti `NamedColor` proprietà, che verrà generato un errore di compilazione.

Quando l'applicazione viene eseguita prima di tutto, il [ `ListView` ](xref:Xamarin.Forms.ListView) viene popolata con `NamedColor` istanze. Quando un elemento di `ListView` è selezionata, il [ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color) viene impostata sul colore dell'elemento selezionato nel `ListView`:

[![Compilato l'elenco dei colori](compiled-bindings-images/compiledcolorlist-small.png "compilato l'elenco dei colori]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Compiled Color List")

Selezionando gli altri elementi di [ `ListView` ](xref:Xamarin.Forms.BoxView) aggiorna il colore del [ `BoxView` ](xref:Xamarin.Forms.BoxView).

## <a name="combining-compiled-bindings-with-classic-bindings"></a>Binding compilato in combinazione con le associazioni classiche

Le espressioni di associazione vengono compilate solo per la gerarchia di visualizzazione che di `x:DataType` attributo viene definito per. Al contrario, tutte le viste in una gerarchia in cui il `x:DataType` attributo non è definito userà associazioni classiche. È pertanto possibile combinare associazioni compilate e classiche in una pagina. Ad esempio, nella precedente sezione viste all'interno di [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) usare le associazioni compilate, mentre il [ `BoxView` ](xref:Xamarin.Forms.BoxView) che è impostato sul colore selezionato nel [ `ListView` ](xref:Xamarin.Forms.ListView) non esiste.

Un'attenta strutturazione dei `x:DataType` attributi possono pertanto generare una pagina utilizzando le associazioni compilate e la distribuzione classiche. In alternativa, il `x:DataType` attributo può essere ridefinito in qualsiasi punto in una gerarchia di visualizzazione per `null` usando il `x:Null` estensione di markup. Questa operazione indica che tutte le espressioni di associazione all'interno della gerarchia di visualizzazione utilizzano associazioni classiche. Il *associazioni misto* è illustrato questo approccio:

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

La radice [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) imposta la `x:DataType` attributo sia il `HslColorViewModel` tipo, che indica che qualsiasi espressione di associazione nella radice `StackLayout` gerarchia di visualizzazione verrà compilata. Tuttavia, interna `StackLayout` ridefinisce il `x:DataType` dell'attributo `null` con il `x:Null` espressione di markup. Di conseguenza, le espressioni di associazione all'interno di interna `StackLayout` usare le associazioni di classiche. Solo le [ `BoxView` ](xref:Xamarin.Forms.BoxView), nell'ambito della radice `StackLayout` visualizzare la gerarchia, le associazioni compilate viene utilizzato.

Per altre informazioni sul `x:Null` espressione di markup, vedere [estensione di Markup X:null](~/xamarin-forms/xaml/markup-extensions/consuming.md#null).

## <a name="performance"></a>Prestazioni

Le associazioni compilate di migliorare le prestazioni, con il vantaggio di prestazioni diversi di associazione dati. Gli unit test rivela che:

- Un binding compilato che utilizza la notifica di modifica delle proprietà (ad esempio un `OneWay`, `OneWayToSource`, o `TwoWay` binding) viene risolto circa 8 volte più veloci rispetto a un'associazione classica.
- Un binding compilato che non utilizza la notifica di modifica delle proprietà (vale a dire un `OneTime` binding) viene risolto circa 20 volte più veloci rispetto a un'associazione classica.
- Impostando il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) su un binding compilato che Usa proprietà notifica di modifica (vale a dire una `OneWay`, `OneWayToSource`, o `TwoWay` associazione) è circa 5 volte più veloce rispetto all'impostazione di `BindingContext`su un'associazione classica.
- Impostazione la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) su un binding compilato che non utilizza proprietà notifica di modifica (vale a dire un `OneTime` associazione) è di circa 7 volte più veloce rispetto a impostazione il `BindingContext` su un'associazione classica.

Queste differenze di prestazioni possono essere ingrandite sui dispositivi mobili, dipendenti la piattaforma in uso, la versione del sistema operativo in uso e il dispositivo in cui viene eseguita l'applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
