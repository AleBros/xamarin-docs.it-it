---
title: Parte 4. Nozioni di base sul data binding
description: Le associazioni dati consentono di collegare le proprietà di due oggetti in modo che una modifica in uno di essi provochi una modifica nell'altra.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 342288C3-BB4C-4924-B178-72E112D777BA
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 08be571d3ba69891a56c08efd556a999e51431c8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139854"
---
# <a name="part-4-data-binding-basics"></a>Parte 4. Nozioni di base sul data binding

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Le associazioni dati consentono di collegare le proprietà di due oggetti in modo che una modifica in uno di essi provochi una modifica nell'altra. Si tratta di uno strumento molto utile e, mentre i data binding possono essere definiti interamente nel codice, XAML fornisce collegamenti e praticità. Di conseguenza, una delle estensioni di markup più importanti in Xamarin.Forms è l'associazione._

## <a name="data-bindings"></a>Associazioni dati

Le associazioni dati connettono le proprietà di due oggetti, denominati *origine* e *destinazione*. Nel codice sono necessari due passaggi: la `BindingContext` proprietà dell'oggetto di destinazione deve essere impostata sull'oggetto di origine e il `SetBinding` Metodo (spesso usato in combinazione con la `Binding` classe) deve essere chiamato sull'oggetto di destinazione per associare una proprietà di tale oggetto a una proprietà dell'oggetto di origine.

La proprietà di destinazione deve essere una proprietà associabile, il che significa che l'oggetto di destinazione deve derivare da `BindableObject` . La Xamarin.Forms documentazione online indica quali proprietà sono proprietà associabili. Una proprietà di `Label` , ad esempio, `Text` è associata alla proprietà associabile `TextProperty` .

Nel markup è inoltre necessario eseguire gli stessi due passaggi necessari nel codice, ad eccezione del fatto che l' `Binding` estensione di markup prende il posto della `SetBinding` chiamata e della `Binding` classe.

Tuttavia, quando si definiscono le associazioni dati in XAML, esistono diversi modi per impostare la classe `BindingContext` dell'oggetto di destinazione. A volte è impostato dal file code-behind, a volte usando un' `StaticResource` `x:Static` estensione di markup o e talvolta come il contenuto dei `BindingContext` tag dell'elemento Property.

Le associazioni vengono usate più spesso per connettere gli oggetti visivi di un programma con un modello di dati sottostante, in genere in una realizzazione dell'architettura dell'applicazione MVVM (Model-View-ViewModel), come illustrato nella [parte 5. Da data binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), ma altri scenari sono possibili.

## <a name="view-to-view-bindings"></a>Associazioni viste-visualizzazione

È possibile definire associazioni dati per collegare le proprietà di due visualizzazioni nella stessa pagina. In questo caso, si imposta l' `BindingContext` oggetto dell'oggetto di destinazione usando l' `x:Reference` estensione di markup.

Ecco un file XAML che contiene una `Slider` e due `Label` Visualizzazioni, una delle quali viene ruotata in base al `Slider` valore e un'altra che Visualizza il `Slider` valore:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderBindingsPage"
             Title="Slider Bindings Page">

    <StackLayout>
        <Label Text="ROTATION"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Slider`Contiene un `x:Name` attributo a cui fanno riferimento le due visualizzazioni che `Label` usano l' `x:Reference` estensione di markup.

L' `x:Reference` estensione di binding definisce una proprietà denominata `Name` per impostare sul nome dell'elemento a cui si fa riferimento, in questo caso `slider` . Tuttavia, la `ReferenceExtension` classe che definisce l' `x:Reference` estensione di markup definisce anche un `ContentProperty` attributo per `Name` , il che significa che non è richiesto in modo esplicito. Solo per la varietà, il primo `x:Reference` include "Name =", ma il secondo non:

```xaml
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

L' `Binding` estensione di markup può avere diverse proprietà, proprio come la `BindingBase` `Binding` classe e. `ContentProperty`Per `Binding` è `Path` , ma la parte "Path =" dell'estensione di markup può essere omessa se il percorso è il primo elemento nell'estensione di `Binding` markup. Il primo esempio ha "Path =", ma il secondo esempio lo omette:

```xaml
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Le proprietà possono essere tutte in una riga o separate in più righe:

```xaml
Text="{Binding Value,
               StringFormat='The angle is {0:F0} degrees'}"
```

Eseguire tutte le operazioni più convenienti.

Si noti la `StringFormat` proprietà nella seconda `Binding` estensione di markup. In Xamarin.Forms le associazioni non eseguono alcuna conversione implicita di tipi e, se è necessario visualizzare un oggetto non stringa come stringa, è necessario fornire un convertitore di tipi o utilizzare `StringFormat` . Dietro le quinte, `String.Format` viene usato il metodo statico per `StringFormat` implementare. Questo è potenzialmente un problema perché le specifiche di formattazione .NET coinvolgono parentesi graffe, che vengono usate anche per delimitare le estensioni di markup. Questo crea il rischio di confondere il parser XAML. Per evitare tale indicizzazione, inserire l'intera stringa di formattazione tra virgolette singole:

```xaml
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Ecco il programma in esecuzione:

[![Associazioni viste-visualizzazione](data-binding-basics-images/sliderbinding.png)](data-binding-basics-images/sliderbinding-large.png#lightbox)

## <a name="the-binding-mode"></a>Modalità di associazione

Una singola vista può avere data binding su diverse proprietà. Tuttavia, ogni vista può disporre di un solo `BindingContext` , pertanto più associazioni dati in tale vista devono fare riferimento a tutte le proprietà dello stesso oggetto.

La soluzione per questo e altri problemi riguarda la `Mode` proprietà, che è impostata su un membro dell' `BindingMode` enumerazione:

- `Default`
- `OneWay`-i valori vengono trasferiti dall'origine alla destinazione
- `OneWayToSource`-i valori vengono trasferiti dalla destinazione all'origine
- `TwoWay`-i valori vengono trasferiti in entrambe le direzioni tra l'origine e la destinazione
- `OneTime`: i dati passano dall'origine alla destinazione, ma solo quando `BindingContext` cambiano

Nel programma seguente viene illustrato un uso comune delle `OneWayToSource` `TwoWay` modalità di associazione e. Quattro `Slider` visualizzazioni sono progettate per controllare le `Scale` `Rotate` proprietà,, `RotateX` e `RotateY` di un oggetto `Label` . Inizialmente, sembra che queste quattro proprietà di `Label` debbano essere destinazioni di data binding perché ciascuna viene impostata da un oggetto `Slider` . Tuttavia, `BindingContext` `Label` può essere un solo oggetto e sono presenti quattro dispositivi di scorrimento diversi.

Per questo motivo, tutte le associazioni sono impostate in modi apparentemente indietro: il `BindingContext` di ognuno dei quattro dispositivi di scorrimento è impostato `Label` su e le associazioni vengono impostate nelle `Value` proprietà dei dispositivi di scorrimento. Utilizzando le `OneWayToSource` modalità e `TwoWay` , queste `Value` proprietà possono impostare le proprietà di origine, ovvero le `Scale` proprietà, `Rotate` , `RotateX` e `RotateY` di `Label` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderTransformsPage"
             Padding="5"
             Title="Slider Transforms Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>

        <!-- Scaled and rotated Label -->
        <Label x:Name="label"
               Text="TEXT"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <!-- Slider and identifying Label for Scale -->
        <Slider x:Name="scaleSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="1" Grid.Column="0"
                Maximum="10"
                Value="{Binding Scale, Mode=TwoWay}" />

        <Label BindingContext="{x:Reference scaleSlider}"
               Text="{Binding Value, StringFormat='Scale = {0:F1}'}"
               Grid.Row="1" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for Rotation -->
        <Slider x:Name="rotationSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="2" Grid.Column="0"
                Maximum="360"
                Value="{Binding Rotation, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationSlider}"
               Text="{Binding Value, StringFormat='Rotation = {0:F0}'}"
               Grid.Row="2" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationX -->
        <Slider x:Name="rotationXSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="3" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationX, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationXSlider}"
               Text="{Binding Value, StringFormat='RotationX = {0:F0}'}"
               Grid.Row="3" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationY -->
        <Slider x:Name="rotationYSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="4" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationY, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationYSlider}"
               Text="{Binding Value, StringFormat='RotationY = {0:F0}'}"
               Grid.Row="4" Grid.Column="1"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

Le associazioni in tre `Slider` visualizzazioni sono `OneWayToSource` , ovvero il `Slider` valore provoca una modifica nella proprietà di `BindingContext` , che è l'oggetto `Label` denominato `label` . Queste tre `Slider` visualizzazioni generano modifiche alle `Rotate` `RotateX` proprietà, e `RotateY` di `Label` .

Tuttavia, l'associazione per la `Scale` proprietà è `TwoWay` . Poiché il valore `Scale` predefinito della proprietà è 1 e l'utilizzo di un' `TwoWay` associazione determina l'impostazione del `Slider` valore iniziale su 1 anziché su 0. Se tale associazione fosse `OneWayToSource` , la `Scale` proprietà verrebbe inizialmente impostata su 0 rispetto al `Slider` valore predefinito. Il `Label` non è visibile e questo potrebbe causare confusione all'utente.

 [![Binding all'indietro](data-binding-basics-images/slidertransforms.png)](data-binding-basics-images/slidertransforms-large.png#lightbox)

 > [!NOTE]
 > La [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe dispone inoltre [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) di [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) proprietà e che scalano `VisualElement` rispettivamente sull'asse x e sull'asse y.

## <a name="bindings-and-collections"></a>Associazioni e raccolte

Nulla illustra la potenza di XAML e le associazioni dati migliori rispetto a un modello `ListView` .

`ListView`definisce una `ItemsSource` proprietà di tipo `IEnumerable` e visualizza gli elementi della raccolta. Questi elementi possono essere oggetti di qualsiasi tipo. Per impostazione predefinita, `ListView` Usa il `ToString` metodo di ogni elemento per visualizzare l'elemento. In alcuni casi questo è solo quello che si desidera, ma in molti casi `ToString` restituisce solo il nome completo della classe dell'oggetto.

Tuttavia, gli elementi della `ListView` raccolta possono essere visualizzati nel modo desiderato tramite l'uso di un *modello*, che implica una classe che deriva da `Cell` . Il modello viene clonato per ogni elemento in `ListView` e i data binding impostati nel modello vengono trasferiti ai singoli cloni.

Molto spesso, è consigliabile creare una cella personalizzata per questi elementi usando la `ViewCell` classe. Questo processo è piuttosto confuso nel codice, ma in XAML diventa molto semplice.

Incluso nel progetto XamlSamples è una classe denominata `NamedColor` . Ogni `NamedColor` oggetto dispone `Name` `FriendlyName` di proprietà e di tipo e `string` una `Color` proprietà di tipo `Color` . Inoltre, `NamedColor` presenta 141 campi statici di sola lettura di tipo `Color` corrispondenti ai colori definiti nella Xamarin.Forms `Color` classe. Un costruttore statico crea una `IEnumerable<NamedColor>` raccolta che contiene `NamedColor` oggetti corrispondenti a questi campi statici e la assegna alla relativa proprietà statica pubblica `All` .

L'impostazione della `NamedColor.All` proprietà statica su `ItemsSource` di un `ListView` è semplice utilizzando l' `x:Static` estensione di markup:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

La visualizzazione risultante stabilisce che gli elementi sono effettivamente di tipo `XamlSamples.NamedColor` :

[![Associazione a una raccolta](data-binding-basics-images/listview1.png)](data-binding-basics-images/listview1-large.png#lightbox)

Non si tratta di molte informazioni, ma `ListView` è scorrevole e selezionabile.

Per definire un modello per gli elementi, è opportuno suddividere la `ItemTemplate` proprietà come elemento Property e impostarla su un oggetto `DataTemplate` , che fa quindi riferimento a `ViewCell` . Alla `View` proprietà di `ViewCell` è possibile definire un layout di una o più visualizzazioni per visualizzare ogni elemento. Ecco un semplice esempio:

```xaml
<ListView ItemsSource="{x:Static local:NamedColor.All}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.View>
                    <Label Text="{Binding FriendlyName}" />
                </ViewCell.View>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

> [!NOTE]
> L'origine del binding per le celle e gli elementi figlio delle celle è la `ListView.ItemsSource` raccolta.

L' `Label` elemento viene impostato sulla `View` proprietà di `ViewCell` . I `ViewCell.View` tag non sono necessari perché la `View` proprietà è la proprietà di contenuto di `ViewCell` . Questo markup Visualizza la `FriendlyName` proprietà di ogni `NamedColor` oggetto:

[![Associazione a una raccolta con un oggetto DataTemplate](data-binding-basics-images/listview2.png)](data-binding-basics-images/listview2-large.png#lightbox)

Molto meglio. A questo punto, è sufficiente ripulire il modello di elemento con altre informazioni e il colore effettivo. Per supportare questo modello, alcuni valori e oggetti sono stati definiti nel dizionario risorse della pagina:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <OnPlatform x:Key="boxSize"
                        x:TypeArguments="x:Double">
                <On Platform="iOS, Android, UWP" Value="50" />
            </OnPlatform>

            <OnPlatform x:Key="rowHeight"
                        x:TypeArguments="x:Int32">
                <On Platform="iOS, Android, UWP" Value="60" />
            </OnPlatform>

            <local:DoubleToIntConverter x:Key="intConverter" />

        </ResourceDictionary>
    </ContentPage.Resources>

    <ListView ItemsSource="{x:Static local:NamedColor.All}"
              RowHeight="{StaticResource rowHeight}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <StackLayout Padding="5, 5, 0, 5"
                                 Orientation="Horizontal"
                                 Spacing="15">

                        <BoxView WidthRequest="{StaticResource boxSize}"
                                 HeightRequest="{StaticResource boxSize}"
                                 Color="{Binding Color}" />

                        <StackLayout Padding="5, 0, 0, 0"
                                     VerticalOptions="Center">

                            <Label Text="{Binding FriendlyName}"
                                   FontAttributes="Bold"
                                   FontSize="Medium" />

                            <StackLayout Orientation="Horizontal"
                                         Spacing="0">
                                <Label Text="{Binding Color.R,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat='R={0:X2}'}" />

                                <Label Text="{Binding Color.G,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', G={0:X2}'}" />

                                <Label Text="{Binding Color.B,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', B={0:X2}'}" />
                            </StackLayout>
                        </StackLayout>
                    </StackLayout>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

Si noti l'uso di `OnPlatform` per definire le dimensioni di un oggetto `BoxView` e l'altezza delle `ListView` righe. Sebbene i valori per tutte le piattaforme siano uguali, il markup può essere facilmente adattato per altri valori per ottimizzare la visualizzazione.

## <a name="binding-value-converters"></a>Convertitori di valori per i binding

Il file XAML **demo ListView** precedente Visualizza le singole `R` `G` proprietà, e `B` della Xamarin.Forms `Color` struttura. Queste proprietà sono di tipo `double` e sono comprese tra 0 e 1. Se si desidera visualizzare i valori esadecimali, non è possibile utilizzare semplicemente `StringFormat` con una specifica di formattazione "X2". Funziona solo per numeri interi e oltre a, i `double` valori devono essere moltiplicati per 255.

Questo piccolo problema è stato risolto con un *convertitore di valori*, detto anche convertitore di *Binding*. Si tratta di una classe che implementa l' `IValueConverter` interfaccia, ovvero presenta due metodi denominati `Convert` e `ConvertBack` . Il `Convert` metodo viene chiamato quando un valore viene trasferito dall'origine alla destinazione; il `ConvertBack` metodo viene chiamato per i trasferimenti dalla destinazione all'origine `OneWayToSource` in `TwoWay` binding o:

```csharp
using System;
using System.Globalization;
using Xamarin.Forms;

namespace XamlSamples
{
    class DoubleToIntConverter : IValueConverter
    {
        public object Convert(object value, Type targetType,
                              object parameter, CultureInfo culture)
        {
            double multiplier;

            if (!Double.TryParse(parameter as string, out multiplier))
                multiplier = 1;

            return (int)Math.Round(multiplier * (double)value);
        }

        public object ConvertBack(object value, Type targetType,
                                  object parameter, CultureInfo culture)
        {
            double divider;

            if (!Double.TryParse(parameter as string, out divider))
                divider = 1;

            return ((double)(int)value) / divider;
        }
    }
}
```

Il `ConvertBack` metodo non svolge un ruolo in questo programma perché le associazioni sono solo unidirezionali dall'origine alla destinazione.

Un'associazione fa riferimento a un convertitore di associazioni con la `Converter` Proprietà. Un convertitore di associazioni può anche accettare un parametro specificato con la `ConverterParameter` Proprietà. Per una certa versatilità, questo è il modo in cui viene specificato il moltiplicatore. Il convertitore di associazioni controlla il parametro del convertitore per un `double` valore valido.

Viene creata un'istanza del convertitore nel dizionario risorse, in modo che possa essere condivisa tra più associazioni:

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

Tre associazioni dati fanno riferimento a questa singola istanza. Si noti che l' `Binding` estensione di markup contiene un'estensione di markup incorporata `StaticResource` :

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

Ecco il risultato:

[![Associazione a una raccolta con DataTemplate e convertitori](data-binding-basics-images/listview3.png)](data-binding-basics-images/listview3-large.png#lightbox)

`ListView`È piuttosto sofisticato per la gestione delle modifiche che possono verificarsi in modo dinamico nei dati sottostanti, ma solo se si esegue una determinata procedura. Se la raccolta di elementi assegnati alla `ItemsSource` proprietà delle `ListView` modifiche durante il runtime, ovvero se è possibile aggiungere o rimuovere elementi dalla raccolta, utilizzare una `ObservableCollection` classe per questi elementi. `ObservableCollection`implementa l' `INotifyCollectionChanged` interfaccia e `ListView` installerà un gestore per l' `CollectionChanged` evento.

Se le proprietà degli elementi cambiano in fase di esecuzione, gli elementi della raccolta devono implementare l' `INotifyPropertyChanged` interfaccia e segnalare le modifiche ai valori delle proprietà usando l' `PropertyChanged` evento. Questa operazione viene illustrata nella parte successiva della serie, la [parte 5. Dal data binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Summary

Le associazioni dati forniscono un meccanismo potente per il collegamento delle proprietà tra due oggetti all'interno di una pagina o tra oggetti visivi e dati sottostanti. Tuttavia, quando l'applicazione inizia a lavorare con le origini dati, un modello di architettura dell'applicazione comune inizia a emergere come un paradigma utile. Questa operazione è prevista nella [parte 5. Da data binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introduzione con XAML (esempio)](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintassi XAML essenziale (esempio)](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Estensioni di markup XAML (esempio)](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 5. Dal data binding a MVVM (esempio)](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
