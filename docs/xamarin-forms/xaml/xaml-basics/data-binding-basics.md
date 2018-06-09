---
title: Parte 4. Nozioni fondamentali sull'associazione dati
description: Le associazioni dati consentono di proprietà di due oggetti da collegare in modo che una modifica in uno provoca una modifica in altro.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 342288C3-BB4C-4924-B178-72E112D777BA
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: 117ddd033faedda871c33ba10c246739309e2e86
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245950"
---
# <a name="part-4-data-binding-basics"></a>Parte 4. Nozioni fondamentali sull'associazione dati

_Le associazioni dati consentono di proprietà di due oggetti da collegare in modo che una modifica in uno provoca una modifica in altro. Questo è uno strumento molto importante e mentre associazioni di dati possono essere definite interamente nel codice, XAML fornisce tasti di scelta rapida e di praticità. Di conseguenza, una delle estensioni di markup più importanti in xamarin. Forms è associato._

## <a name="data-bindings"></a>Data Binding

Proprietà di due oggetti, denominata connect di Data Binding di *origine* e *destinazione*. Nel codice, sono necessari due passaggi: il `BindingContext` proprietà dell'oggetto di destinazione deve essere impostata all'oggetto di origine e `SetBinding` metodo (spesso usato in combinazione con la `Binding` classe) deve essere chiamato sull'oggetto di destinazione per associare una proprietà di tale oggetto a una proprietà dell'oggetto di origine.

La proprietà di destinazione deve essere una proprietà associabile, il che significa che l'oggetto di destinazione deve derivare da `BindableObject`. La documentazione online di xamarin. Forms indica quali proprietà sono proprietà associabili. Una proprietà di `Label` , ad esempio `Text` è associata a una proprietà associabile `TextProperty`.

Nel codice, è necessario eseguire anche gli stessi due passaggi necessari nel codice, ad eccezione del fatto che il `Binding` estensione di markup prende il posto di `SetBinding` chiamare e `Binding` classe.

Tuttavia, quando si definiscono le associazioni dati in XAML, esistono diversi modi per impostare il `BindingContext` dell'oggetto di destinazione. In alcuni casi è impostato dal file di codice, talvolta utilizzando un `StaticResource` o `x:Static` estensione di markup e talvolta come contenuto di `BindingContext` tag di elemento di proprietà.

Le associazioni usate più spesso per la connessione di oggetti visivi di un programma con un modello di dati sottostante, in genere in una realizzazione dell'architettura dell'applicazione MVVM (Model-View-ViewModel), come descritto in [parte 5. Da associazioni di dati per MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), ma sono possibili altri scenari.

## <a name="view-to-view-bindings"></a>Per visualizzare binding

È possibile definire associazioni di dati per collegare le proprietà delle due visualizzazioni nella stessa pagina. In questo caso, si imposta la `BindingContext` dell'oggetto di destinazione utilizzando il `x:Reference` estensione di markup.

Ecco un file XAML che contiene un `Slider` e due `Label` viste, uno dei quali viene ruotato del `Slider` valore e l'altro che consente di visualizzare il `Slider` valore:

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

Il `Slider` contiene un `x:Name` attributo cui viene fatto riferimento dai due `Label` viste utilizzando la `x:Reference` estensione di markup.

Il `x:Reference` estensione di binding definisce una proprietà denominata `Name` su cui impostare il nome dell'elemento di cui viene fatto riferimento, in questo caso `slider`. Tuttavia, il `ReferenceExtension` classe che definisce il `x:Reference` estensione di markup definisce inoltre un `ContentProperty` attributo per `Name`, il che significa che non sia esplicitamente richiesta. Solo per serie, il primo `x:Reference` include "nome =" ma non il secondo:

```csharp
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

Il `Binding` estensione di markup stesso può avere diverse proprietà, esattamente come il `BindingBase` e `Binding` classe. Il `ContentProperty` per `Binding` è `Path`, ma la "percorso =" parte dell'estensione di markup può essere omesso se il percorso è il primo elemento di `Binding` estensione di markup. Nel primo esempio è "Path =" omettendo nel secondo esempio:

```csharp
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Le proprietà possono essere su una riga o suddivisi in più righe:

```csharp
Text="{Binding Value,
               StringFormat='The angle is {0:F0} degrees'}"
```

Eseguire le operazioni pratico.

Si noti il `StringFormat` proprietà nel secondo `Binding` estensione di markup. Associazioni in xamarin. Forms, non eseguono alcuna conversione implicita del tipo e, se si desidera visualizzare un oggetto non di tipo stringa come stringa, è necessario fornire un convertitore di tipi o utilizzare `StringFormat`. Dietro le quinte, statica `String.Format` metodo viene utilizzato per implementare `StringFormat`. Che potenzialmente costituisce un problema, perché le specifiche di formato .NET prevedono l'uso di parentesi graffe, che vengono inoltre utilizzate per delimitare le estensioni di markup. Crea un rischio di confondere il parser XAML. Per evitare che, inserire l'intera stringa di formattazione tra virgolette singole:

```csharp
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Di seguito è riportato il programma in esecuzione:

[![](data-binding-basics-images/sliderbinding.png "Per visualizzare associazioni")](data-binding-basics-images/sliderbinding-large.png#lightbox "-per visualizzare le associazioni ")

## <a name="the-binding-mode"></a>La modalità di associazione

Una singola visualizzazione può avere associazioni dati in alcune delle proprie proprietà. Tuttavia, ogni vista può avere un solo `BindingContext`, in modo più associazioni di dati per tale vista devono fare riferimento alle proprietà dell'oggetto stesso.

La soluzione a questo e altri problemi implica il `Mode` proprietà è impostata su un membro del `BindingMode` enumerazione:

- `Default`
- `OneWay` : i valori vengono trasferiti dall'origine alla destinazione
- `OneWayToSource` : i valori vengono trasferiti dalla destinazione all'origine
- `TwoWay` : i valori vengono trasferiti in entrambe le direzioni tra origine e destinazione

Il programma seguente viene illustrato un caso frequente di `OneWayToSource` e `TwoWay` modalità di associazione. Quattro `Slider` le visualizzazioni sono designate per controllare il `Scale`, `Rotate`, `RotateX`, e `RotateY` le proprietà di un `Label`. Inizialmente, può sembrare come se queste quattro proprietà del `Label` deve essere destinazioni di associazione di dati, perché ogni viene impostata un `Slider`. Tuttavia, il `BindingContext` di `Label` può essere un solo oggetto e vi sono quattro diversi dispositivi di scorrimento.

Per questo motivo, tutte le associazioni sono impostate apparentemente con le versioni precedenti modi: il `BindingContext` di ciascuno dei quattro i dispositivi di scorrimento è impostato sul `Label`, e le associazioni vengono impostate per il `Value` le proprietà di scorrimento. Tramite il `OneWayToSource` e `TwoWay` modalità, questi `Value` impostate le proprietà di origine, ovvero il `Scale`, `Rotate`, `RotateX`, e `RotateY` le proprietà del `Label`:

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

Le associazioni su tre il `Slider` le visualizzazioni sono `OneWayToSource`, ovvero che la `Slider` valore provoca una modifica nella proprietà del relativo `BindingContext`, che è il `Label` denominato `label`. Questi tre `Slider` viste determinano la modifica del `Rotate`, `RotateX`, e `RotateY` le proprietà del `Label`.

Tuttavia, l'associazione per il `Scale` proprietà `TwoWay`. In questo modo il `Scale` proprietà ha un valore predefinito di 1 e l'uso un `TwoWay` associazione cause il `Slider` iniziale valore da impostare in corrispondenza di 1 anziché 0. Se sono stati che l'associazione `OneWayToSource`, `Scale` sarebbe inizialmente impostata su 0 dal `Slider` il valore predefinito. Il `Label` non dovrebbe essere visibile e che potrebbe causare confusione all'utente.

 [![](data-binding-basics-images/slidertransforms.png "Con le versioni precedenti associazioni")](data-binding-basics-images/slidertransforms-large.png#lightbox "con le versioni precedenti le associazioni")

## <a name="bindings-and-collections"></a>Associazioni e le raccolte

Non illustra le potenzialità di XAML e i data binding migliore una basata su modelli `ListView`.

`ListView` definisce un `ItemsSource` proprietà di tipo `IEnumerable`, e visualizza gli elementi nella raccolta. Questi elementi possono essere oggetti di qualsiasi tipo. Per impostazione predefinita, `ListView` utilizza il `ToString` metodo di ogni elemento per visualizzare l'elemento di. In alcuni casi si tratta solo ciò che si desidera, ma in molti casi, `ToString` restituisce solo il nome completo della classe dell'oggetto.

Tuttavia, gli elementi il `ListView` raccolta può essere visualizzata modo desiderato tramite l'utilizzo di un *modello*, che implica una classe che deriva da `Cell`. Il modello viene duplicato per ogni elemento di `ListView`, le associazioni di dati che sono state impostate nel modello vengono trasferiti al singolo clone.

Molto spesso, è opportuno creare una cella personalizzata per questi elementi utilizzando la `ViewCell` classe. Questo processo è in qualche modo disordinato nel codice, ma in XAML diventa molto semplice.

Il XamlSamples progetto include una classe denominata `NamedColor`. Ogni `NamedColor` oggetto ha `Name` e `FriendlyName` le proprietà di tipo `string`e un `Color` proprietà di tipo `Color`. Inoltre, `NamedColor` ha 141 statici campi di sola lettura di tipo `Color` corrispondente a colori definiti in di xamarin. Forms `Color` classe. Un costruttore statico viene creato un `IEnumerable<NamedColor>` raccolta che contiene `NamedColor` oggetti corrispondenti a questi campi statici e di assegnarlo ai relativi statici pubblici `All` proprietà.

Impostazione statica `NamedColor.All` proprietà per il `ItemsSource` di un `ListView` semplice utilizzando il `x:Static` estensione di markup:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

La visualizzazione risulta stabilisce che gli elementi siano effettivamente di tipo `XamlSamples.NamedColor`:

[![](data-binding-basics-images/listview1.png "Associazione a una raccolta")](data-binding-basics-images/listview1-large.png#lightbox "associazione a una raccolta")

Non è una quantità di informazioni, ma la `ListView` è scorrevole e selezionabile.

Per definire un modello per gli elementi, sarà necessario suddividere il `ItemTemplate` proprietà come un elemento di proprietà e impostarla su un `DataTemplate`, che fa quindi riferimento un `ViewCell`. Per il `View` proprietà del `ViewCell` è possibile definire un layout di uno o più visualizzazioni da visualizzare ogni elemento. Di seguito è riportato un esempio:

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

Il `Label` è impostato sul `View` proprietà del `ViewCell`. (Il `ViewCell.View` tag non sono necessarie perché il `View` proprietà è la proprietà content del `ViewCell`.) Questo codice consente di visualizzare il `FriendlyName` proprietà di ogni `NamedColor` oggetto:

[![](data-binding-basics-images/listview2.png "Associazione a una raccolta con un oggetto DataTemplate")](data-binding-basics-images/listview2-large.png#lightbox "associazione a una raccolta con un elemento DataTemplate")

Molto meglio. Tutto ciò che serve è perfetto il modello di elemento con ulteriori informazioni e al colore effettivo. Per supportare questo modello, alcuni valori e gli oggetti sono stati definiti nel dizionario di risorse della pagina:

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

Si noti l'uso di `OnPlatform` per definire la dimensione di un `BoxView` e l'altezza del `ListView` righe. Anche se i valori per tutte e tre le piattaforme sono uguali, il codice può essere facilmente adattato per gli altri valori ottimizzare la visualizzazione.

## <a name="binding-value-converters"></a>Convertitori di valori di associazione

Precedente **ListView Demo** file XAML vengono visualizzati i singoli `R`, `G`, e `B` le proprietà di xamarin. Forms `Color` struttura. Queste proprietà sono di tipo `double` e compreso tra 0 e 1. Se si desidera visualizzare i valori esadecimali, è possibile utilizzare semplicemente `StringFormat` con una formattazione specifica "X2". Che funziona solo per i numeri interi e oltre, la `double` i valori devono essere moltiplicato per 255.

Questo problema è stato risolto con un *convertitore*, denominati anche un *convertitore associazione*. Si tratta di una classe che implementa il `IValueConverter` interfaccia, vale a dire due metodi denominati `Convert` e `ConvertBack`. Il `Convert` metodo viene chiamato quando un valore viene trasferito dall'origine alla destinazione; il `ConvertBack` metodo viene chiamato per i trasferimenti dalla destinazione all'origine in `OneWayToSource` o `TwoWay` associazioni:

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

Il `ConvertBack` metodo non svolge un ruolo in questo programma perché le associazioni sono solo unidirezionale dall'origine alla destinazione.

Un'associazione fa riferimento a un convertitore di tipi di associazione con il `Converter` proprietà. Un convertitore di tipi di associazione può anche accettare un parametro specificato con il `ConverterParameter` proprietà. Per alcuni versatilità, si tratta di come specificare il moltiplicatore. Il convertitore di tipi di associazione controlla il parametro del convertitore per un oggetto valido `double` valore.

Il convertitore viene creata un'istanza del dizionario risorse in modo che può essere condivise tra più associazioni:

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

Le associazioni tre dati fare riferimento a questa istanza singola. Si noti che il `Binding` estensione di markup è incorporata `StaticResource` estensione di markup:

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

Di seguito è riportato il risultato:

[![](data-binding-basics-images/listview3.png "Associazione a una raccolta con un modello di dati e i convertitori di tipi")](data-binding-basics-images/listview3-large.png#lightbox "associazione a una raccolta con un modello di dati e i convertitori di tipi")

Il `ListView` è piuttosto sofisticato nella gestione delle modifiche che potrebbero verificarsi in modo dinamico nei dati sottostanti, ma solo se è effettuare alcune operazioni. Se la raccolta di elementi assegnati al `ItemsSource` proprietà del `ListView` modifiche in fase di esecuzione, che è, se è possibile aggiungere elementi a o rimosso dalla raccolta, utilizzare un `ObservableCollection` classe per questi elementi. `ObservableCollection` implementa il `INotifyCollectionChanged` interfaccia e `ListView` installerà un gestore per il `CollectionChanged` evento.

Se le proprietà degli elementi stessi modificare durante la fase di esecuzione, quindi gli elementi nella raccolta devono implementare il `INotifyPropertyChanged` interfaccia e segnalare le modifiche ai valori delle proprietà utilizzando il `PropertyChanged` evento. Questa funzionalità viene illustrata la parte successiva di questa serie, [parte 5. Da un'associazione dati a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Riepilogo

Le associazioni di dati forniscono un meccanismo potente per il collegamento tra due oggetti all'interno di una pagina o tra gli oggetti visivi di proprietà e i dati sottostanti. Tuttavia, quando l'applicazione diventa operativa con origini dati, un modello architettonico comune applicazione inizia a emergere come un paradigma utile. Questo aspetto è illustrato [parte 5. Da associazioni di dati per MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).



## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Parte 1. Introduzione di XAML (esempio)](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintassi di XAML essenziali (esempio)](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Estensioni di Markup XAML (esempio)](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 5. Da un Data Binding a MVVM (esempio)](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
