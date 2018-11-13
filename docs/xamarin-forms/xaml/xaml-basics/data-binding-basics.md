---
title: Parte 4. Nozioni fondamentali sull'associazione dati
description: Le associazioni dati consentono di proprietà di due oggetti da collegare in modo che una modifica in una causa una modifica in altro.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 342288C3-BB4C-4924-B178-72E112D777BA
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: e0ad67db0671996e594f9c5d48b329a5d676fc1d
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563433"
---
# <a name="part-4-data-binding-basics"></a>Parte 4. Nozioni fondamentali sull'associazione dati

_Le associazioni dati consentono di proprietà di due oggetti da collegare in modo che una modifica in una causa una modifica in altro. Questo è uno strumento molto utile e mentre i data binding può essere definito completamente nel codice, XAML fornisce tasti di scelta rapida e convenience. Di conseguenza, una delle estensioni di markup più importanti in xamarin. Forms è associato._

## <a name="data-bindings"></a>Associazioni dati

Le associazioni dati connettono le proprietà di due oggetti, denominati il *origine* e il *destinazione*. Nel codice, sono necessari due passaggi: il `BindingContext` proprietà dell'oggetto di destinazione deve essere impostata per l'oggetto di origine e il `SetBinding` metodo (spesso usato in combinazione con il `Binding` classe) deve essere chiamato sull'oggetto di destinazione per associare una proprietà di tale oggetto da una proprietà dell'oggetto di origine.

La proprietà di destinazione deve essere una proprietà associabile, il che significa che l'oggetto di destinazione deve derivare da `BindableObject`. La documentazione online di xamarin. Forms indica le proprietà che sono proprietà associabili. Una proprietà del `Label` , ad esempio `Text` associata con la proprietà associabile `TextProperty`.

Nel markup, è anche necessario eseguire gli stessi due passaggi richiesti nel codice, ad eccezione del fatto che il `Binding` estensione di markup prende il posto del `SetBinding` chiamare e `Binding` classe.

Tuttavia, quando si definiscono i data binding in XAML, esistono diversi modi per impostare il `BindingContext` dell'oggetto di destinazione. In alcuni casi viene impostato dal file code-behind, in alcuni casi utilizzando un `StaticResource` o `x:Static` estensione di markup e talvolta come contenuto di `BindingContext` tag di elemento di proprietà.

Le associazioni utilizzate più spesso per connettere gli oggetti visivi di un programma con un modello di dati sottostante, in genere in una realizzazione dell'architettura dell'applicazione MVVM (Model-View-ViewModel), come descritto in [parte 5. Da Data Binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), ma sono possibili altri scenari.

## <a name="view-to-view-bindings"></a>Associazioni di visualizzazione-Vista

È possibile definire associazioni dati per collegare le proprietà delle due visualizzazioni nella stessa pagina. In questo caso, si imposta la `BindingContext` dell'oggetto di destinazione utilizzando il `x:Reference` estensione di markup.

Ecco un file XAML che contiene un `Slider` e due `Label` viste, uno dei quali viene ruotato delle `Slider` valore e l'altro che consente di visualizzare il `Slider` valore:

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

Il `Slider` contiene un `x:Name` attributo cui viene fatto riferimento dai due `Label` le viste usando il `x:Reference` estensione di markup.

Il `x:Reference` estensione di binding definisce una proprietà denominata `Name` per impostare il nome dell'elemento di cui viene fatto riferimento, in questo caso `slider`. Tuttavia, il `ReferenceExtension` classe che definisce il `x:Reference` estensione di markup definisce anche un `ContentProperty` attributo per `Name`, il che significa che non è richiesta in modo esplicito. Solo per diverse, il primo `x:Reference` include "Name =" ma non il secondo:

```csharp
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

Il `Binding` estensione di markup stesso può avere diverse proprietà, come i `BindingBase` e `Binding` classe. Il `ContentProperty` per `Binding` viene `Path`, ma il "percorso =" parte dell'estensione di markup può essere omesso se il percorso è il primo elemento nel `Binding` estensione di markup. Nel primo esempio è "percorso =", ma omette il secondo esempio:

```csharp
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Le proprietà possono essere tutti su una riga o suddivisi in più righe:

```csharp
Text="{Binding Value,
               StringFormat='The angle is {0:F0} degrees'}"
```

Eseguire le operazioni pratica.

Si noti che il `StringFormat` proprietà nella seconda `Binding` estensione di markup. In xamarin. Forms, le associazioni non eseguono alcuna conversione implicita del tipo e se è necessario visualizzare un oggetto non di tipo stringa sotto forma di stringa deve fornire un convertitore di tipi o usare `StringFormat`. Dietro le quinte, il metodo statico `String.Format` metodo viene usato per implementare `StringFormat`. Questo metodo è potenzialmente un problema, perché comportano la parentesi graffe, che vengono usate anche per delimitare le estensioni di markup specifiche di formato .NET. In questo modo viene creata un rischio di confondere il parser XAML. Per evitare questo problema, inserire l'intera stringa di formattazione tra virgolette singole:

```csharp
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Ecco il programma in esecuzione:

[![](data-binding-basics-images/sliderbinding.png "Per-visualizzare associazioni")](data-binding-basics-images/sliderbinding-large.png#lightbox "associazioni visualizzazione-Vista ")

## <a name="the-binding-mode"></a>La modalità di associazione

Una singola visualizzazione può avere associazioni dati su alcune delle proprie proprietà. Tuttavia, ogni vista può avere un solo `BindingContext`, in modo che più associazioni di dati per tale vista devono fare riferimento alle proprietà dell'oggetto stesso.

La soluzione a questo e altri problemi prevede la `Mode` proprietà, che è impostata su un membro del `BindingMode` enumerazione:

- `Default`
- `OneWay` ovvero i valori vengono trasferiti dall'origine alla destinazione
- `OneWayToSource` ovvero i valori vengono trasferiti dalla destinazione all'origine
- `TwoWay` ovvero i valori vengono trasferiti in entrambe le direzioni tra origine e destinazione

Il programma seguente viene illustrato un caso frequente il `OneWayToSource` e `TwoWay` modalità di associazione. Quattro `Slider` viste sono destinate al controllo il `Scale`, `Rotate`, `RotateX`, e `RotateY` le proprietà di un `Label`. Inizialmente, può sembrare come se questi quattro proprietà del `Label` deve essere destinazioni di associazione dati, perché ognuno viene impostata un `Slider`. Tuttavia, il `BindingContext` di `Label` può essere solo un oggetto e non esistono quattro diversi dispositivi di scorrimento.

Per questo motivo, tutte le associazioni vengono impostate apparentemente con le versioni precedenti modi: il `BindingContext` della ognuno dei quattro i dispositivi di scorrimento è impostato sul `Label`, e le associazioni vengono impostate per il `Value` le proprietà di scorrimento. Tramite il `OneWayToSource` e `TwoWay` modalità, queste `Value` proprietà possono impostare le proprietà di origine, che sono il `Scale`, `Rotate`, `RotateX`, e `RotateY` le proprietà del `Label`:

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

Le associazioni su tre il `Slider` le visualizzazioni sono `OneWayToSource`, ovvero che la `Slider` valore fa sì che una modifica nella proprietà del relativo `BindingContext`, che è la `Label` denominato `label`. Questi tre `Slider` viste determinano una modifica di `Rotate`, `RotateX`, e `RotateY` le proprietà del `Label`.

Tuttavia, l'associazione per il `Scale` è di proprietà `TwoWay`. Infatti il `Scale` proprietà ha un valore predefinito di 1 e l'uso un `TwoWay` associazione cause il `Slider` valore da impostare in corrispondenza di 1 anziché 0 iniziale. Se quell'associazione erano `OneWayToSource`, il `Scale` viene inizialmente impostata su 0 del `Slider` il valore predefinito. Il `Label` non dovrebbe essere visibile e che potrebbe causare confusione all'utente.

 [![](data-binding-basics-images/slidertransforms.png "Con le versioni precedenti le associazioni")](data-binding-basics-images/slidertransforms-large.png#lightbox "con le versioni precedenti le associazioni")

 > [!NOTE]
 > Il [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe dispone inoltre [ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX) e [ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY) proprietà, ridimensionare il `VisualElement` su x e y rispettivamente.

## <a name="bindings-and-collections"></a>Associazioni e le raccolte

Nulla illustra le potenzialità di XAML e le associazioni dati migliori rispetto a una basata su modelli `ListView`.

`ListView` definisce un `ItemsSource` vlastnosti typu `IEnumerable`, e visualizza gli elementi nella raccolta. Questi elementi possono essere oggetti di qualsiasi tipo. Per impostazione predefinita `ListView` utilizza il `ToString` (metodo) di ogni elemento per visualizzare tale elemento. In alcuni casi questo è solo quello desiderato, ma in molti casi, `ToString` restituisce solo il nome completo della classe dell'oggetto.

Tuttavia, gli elementi nel `ListView` raccolta può essere visualizzata come vuoi tramite l'uso di un *modello*, che implica una classe che deriva da `Cell`. Il modello viene clonato per tutti gli elementi di `ListView`, associazioni di dati che sono state impostate sul modello vengono trasferiti al singolo clone.

Molto spesso, è opportuno creare una cella personalizzata per questi elementi usando il `ViewCell` classe. Questo processo è un po' complessa nel codice, ma in XAML diventa molto semplice.

Il XamlSamples progetto include una classe denominata `NamedColor`. Ogni `NamedColor` oggetto presenta `Name` e `FriendlyName` delle proprietà di tipo `string`e un `Color` vlastnosti typu `Color`. È inoltre `NamedColor` ha 141 ReadOnly campi statici di tipo `Color` corrispondente per i colori definiti in xamarin. Forms `Color` classe. Un costruttore statico crea un' `IEnumerable<NamedColor>` raccolta che contiene `NamedColor` oggetti corrispondenti a questi campi statici e di assegnarlo ai relativi statici pubblici `All` proprietà.

Impostare il metodo statico `NamedColor.All` proprietà per il `ItemsSource` di un `ListView` è facile usando il `x:Static` estensione di markup:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

La visualizzazione risultante stabilisce che gli elementi siano realmente di tipo `XamlSamples.NamedColor`:

[![](data-binding-basics-images/listview1.png "Associazione a una raccolta")](data-binding-basics-images/listview1-large.png#lightbox "associazione a una raccolta")

Non tutte le informazioni, ma il `ListView` è scorrevole e selezionabile.

Per definire un modello per gli elementi, è opportuno suddividere il `ItemTemplate` proprietà jako prvek vlastnosti e impostarla su una `DataTemplate`, che fa riferimento a quindi un `ViewCell`. Per il `View` proprietà del `ViewCell` è possibile definire un layout di uno o più viste per visualizzare ogni elemento. Ecco un esempio semplice:

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

Il `Label` elemento è impostato sul `View` proprietà del `ViewCell`. (Il `ViewCell.View` tag non sono necessarie perché il `View` è la proprietà content di `ViewCell`.) Questo markup consente di visualizzare il `FriendlyName` proprietà della ognuno `NamedColor` oggetto:

[![](data-binding-basics-images/listview2.png "Associazione a una raccolta con un DataTemplate")](data-binding-basics-images/listview2-large.png#lightbox "associazione a una raccolta con un DataTemplate")

Molto meglio. A questo punto è sufficiente consiste arricchisce il modello di elemento con altre informazioni e il colore effettivo. Per supportare questo modello, alcuni valori e gli oggetti sono stati definiti nel dizionario risorse della pagina:

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

Si noti l'uso di `OnPlatform` per definire le dimensioni di un `BoxView` e l'altezza del `ListView` righe. Sebbene i valori per tutte e tre le piattaforme siano gli stessi, il markup può essere facilmente adattato per altri valori ottimizzare la visualizzazione.

## <a name="binding-value-converters"></a>Convertitori di valori di associazione

Il precedente **ListView Demo** file XAML consente di visualizzare i singoli `R`, `G`, e `B` le proprietà di xamarin. Forms `Color` struttura. Queste proprietà sono di tipo `double` e compreso tra 0 e 1. Se si desidera visualizzare i valori esadecimali, è possibile utilizzare semplicemente `StringFormat` con una formattazione specifica "X2". Che funziona solo per i numeri interi e oltre, la `double` i valori devono essere moltiplicati per 255.

Questo problema è stato risolto con un *convertitore*, definita anche come una *convertitore di associazioni*. Si tratta di una classe che implementa il `IValueConverter` interfaccia, che significa che ha due metodi denominati `Convert` e `ConvertBack`. Il `Convert` metodo viene chiamato quando un valore viene trasferito dall'origine alla destinazione; le `ConvertBack` viene chiamato per i trasferimenti dalla destinazione all'origine in `OneWayToSource` o `TwoWay` associazioni:

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

Il `ConvertBack` metodo non giocano un ruolo in questo programma perché le associazioni sono solo uno di questi dall'origine alla destinazione.

Un'associazione fa riferimento a un convertitore di associazioni con il `Converter` proprietà. Un convertitore di associazioni può anche accettare un parametro specificato con il `ConverterParameter` proprietà. Per alcuni versatilità, si tratta di come viene specificato il moltiplicatore. Il convertitore di associazioni controlla il parametro del convertitore per un valore valido `double` valore.

Il convertitore viene creata un'istanza nel dizionario risorse in modo che possa essere condivisa tra più associazioni:

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

Tre data binding di fare riferimento a questa istanza singola. Si noti che il `Binding` estensione di markup è incorporata `StaticResource` estensione di markup:

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

Ecco il risultato:

[![](data-binding-basics-images/listview3.png "Associazione a una raccolta con un DataTemplate e convertitori")](data-binding-basics-images/listview3-large.png#lightbox "associazione a una raccolta con un DataTemplate e convertitori di tipi")

Il `ListView` è piuttosto sofisticato nella gestione delle modifiche che potrebbero verificarsi in modo dinamico nei dati sottostanti, ma solo se è eseguire alcuni passaggi. Se la raccolta di elementi assegnati al `ItemsSource` proprietà del `ListView` modifiche in fase di esecuzione, che è, se è possibile aggiungere elementi a o rimosso dalla raccolta, usare un `ObservableCollection` classe per questi elementi. `ObservableCollection` implementa il `INotifyCollectionChanged` interfaccia, e `ListView` installerà un gestore per il `CollectionChanged` evento.

Se le proprietà degli elementi stessi cambiano in fase di esecuzione, quindi gli elementi della raccolta devono implementare il `INotifyPropertyChanged` interfaccia e dei segnali le modifiche ai valori delle proprietà utilizzando il `PropertyChanged` evento. Questa funzionalità viene illustrata la parte successiva della serie, [parte 5. Da Data Binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Riepilogo

Le associazioni dati forniscono un meccanismo potente per il collegamento tra due oggetti all'interno di una pagina o tra gli oggetti visivi di proprietà e i dati sottostanti. Ma quando l'applicazione diventa operativa con origini dati, un modello di architettura diffuso dell'applicazione inizia a emergere come un utile paradigma. Questo argomento viene trattato [parte 5. Da Data Binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).



## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Parte 1. Introduzione a XAML (esempio)](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintassi XAML essenziale (esempio)](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Estensioni di Markup XAML (esempio)](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 5. Da un Data Binding a MVVM (esempio)](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
