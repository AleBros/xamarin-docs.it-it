---
title: Convertitori di valori di associazione di xamarin. Forms
description: Questo articolo illustra come eseguire il cast o convertire i valori all'interno di un data binding di xamarin. Forms mediante l'implementazione di un convertitore di valori (che è noto anche come convertitore di associazioni o un convertitore di valori di associazione).
ms.prod: xamarin
ms.assetid: 02B1BBE6-D804-490D-BDD4-8ACED8B70C92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 28892692133020de1fa5a6eb007bb3f9bcf2612b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997484"
---
# <a name="xamarinforms-binding-value-converters"></a>Convertitori di valori di associazione di xamarin. Forms

In genere le associazioni dati trasferire i dati da una proprietà di origine a una proprietà di destinazione e in alcuni casi dalla proprietà di destinazione alla proprietà di origine. Questo trasferimento è semplice quando le proprietà di origine e destinazione sono dello stesso tipo o quando un tipo può essere convertito in altro tipo tramite una conversione implicita. Quando non è questo il caso, una conversione di tipi deve aver luogo.

Nel [ **formattazione della stringa** ](string-formatting.md) articolo, si è visto come utilizzare il `StringFormat` proprietà di un data binding per convertire qualsiasi tipo in una stringa. Per altri tipi di conversioni, è necessario scrivere codice specializzato in una classe che implementa il [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) interfaccia. (La piattaforma universale di Windows contiene una classe simile denominata [ `IValueConverter` ](/uwp/api/Windows.UI.Xaml.Data.IValueConverter/) nel `Windows.UI.Xaml.Data` dello spazio dei nomi, ma ciò `IValueConverter` nel `Xamarin.Forms` dello spazio dei nomi.) Le classi che implementano `IValueConverter` vengono chiamati *convertitori di tipi di valore*, ma essi vengono anche noto anche come *convertitori di tipi di associazione* oppure *convertitori di valori di associazione*.

## <a name="the-ivalueconverter-interface"></a>L'interfaccia IValueConverter

Si supponga che si vuole definire un'associazione di dati in cui la proprietà di origine è di tipo `int` ma la proprietà di destinazione è un `bool`. Si desidera che questa associazione di dati per produrre un `false` valore quando l'origine di integer è uguale a 0, e `true` in caso contrario.  

È possibile farlo con una classe che implementa il `IValueConverter` interfaccia:

```csharp
public class IntToBoolConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value != 0;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? 1 : 0;
    }
}
```

Si imposta un'istanza di questa classe per il [ `Converter` ](xref:Xamarin.Forms.Binding.Converter) proprietà della `Binding` classe o al [ `Converter` ](xref:Xamarin.Forms.Xaml.BindingExtension.Converter) proprietà del `Binding` estensione di markup. Questa classe diventa parte dell'associazione dati.

Il `Convert` viene chiamato quando si sposta i dati dall'origine alla destinazione in `OneWay` o `TwoWay` associazioni. Il `value` parametro è l'oggetto o valore dell'origine di associazione dati. Il metodo deve restituire un valore del tipo della destinazione di associazione dati. Il metodo illustrato di seguito i cast di `value` parametro per un `int` e quindi lo confronta con 0 per un `bool` valore restituito.

Il `ConvertBack` metodo viene chiamato quando i dati vengono spostati dalla destinazione all'origine in `TwoWay` o `OneWayToSource` associazioni. `ConvertBack` esegue la conversione inversa: si presuppone che il `value` parametro è un `bool` dalla destinazione e lo converte in un `int` restituiscono valore per l'origine.

Se l'associazione di dati include anche un `StringFormat` impostazione, il convertitore di valori viene richiamato prima che il risultato viene formattato come stringa.

Il **abilitare i pulsanti** nella pagina il [ **Data Binding Demo** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) esempio viene illustrato come usare il convertitore di valore in un data binding. Il `IntToBoolConverter` viene creata un'istanza nel dizionario risorse della pagina. Viene quindi fatto riferimento con un `StaticResource` estensione di markup per impostare il `Converter` proprietà in due data binding. È molto comune per condividere i convertitori di tipi di dati tra più associazioni di dati nella pagina:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.EnableButtonsPage"
             Title="Enable Buttons">
    <ContentPage.Resources>
        <ResourceDictionary>
            <local:IntToBoolConverter x:Key="intToBool" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <Entry x:Name="entry1"
               Text=""
               Placeholder="enter search term"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Search"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry1},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />

        <Entry x:Name="entry2"
               Text=""
               Placeholder="enter destination"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Submit"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry2},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />
    </StackLayout>
</ContentPage>
```

Se viene usato un convertitore di valori in più pagine dell'applicazione, è possibile crearne un'istanza del dizionario risorse il **app** file.

Il **abilitare i pulsanti** illustra come una comune necessario quando un `Button` esegue un'operazione basata su testo che l'utente digita in un `Entry` vista. Se non è stato digitato nella `Entry`, il `Button` deve essere disabilitata. Ciascuna `Button` contiene un'associazione di dati nel relativo `IsEnabled` proprietà. L'origine di associazione dati è il `Length` proprietà del `Text` proprietà dell'oggetto corrispondente `Entry`. Se tale `Length` proprietà non è 0, restituisce il convertitore `true` e il `Button` è abilitato:

[![Abilitare i pulsanti](converters-images/enablebuttons-small.png "abilitare i pulsanti")](converters-images/enablebuttons-large.png#lightbox "abilitare i pulsanti")

Si noti che il `Text` in ognuna proprietà `Entry` viene inizializzato in una stringa vuota. Il `Text` è di proprietà `null` per impostazione predefinita e il data binding non funzionerà in questo caso.

Alcuni convertitori di valori vengono scritti in modo specifico per applicazioni specifiche, mentre altri sono generalizzati. Se si sa che un convertitore di valori verrà utilizzato solo nella `OneWay` associazioni, il `ConvertBack` metodo può restituire semplicemente `null`.

Il `Convert` metodo illustrato in precedenza in modo implicito si presuppone che il `value` argomento è di tipo `int` e il valore restituito deve essere di tipo `bool`. Analogamente, il `ConvertBack` metodo presuppone che il `value` argomento è di tipo `bool` e il valore restituito è `int`. Se non è questo il caso, si verificherà un'eccezione di runtime.

È possibile scrivere i convertitori di valori per essere più generalizzato e possono accettare diversi tipi di dati. Il `Convert` e `ConvertBack` metodi possono utilizzare il `as` o `is` operatori con la `value` parametro, oppure chiamare `GetType` per quel parametro per determinare il tipo e quindi eseguire un valore appropriato. Il tipo previsto del valore restituito del metodo ogni è dato dal `targetType` parametro. In alcuni casi, convertitori di valori vengono usati con le associazioni di dati dei tipi di destinazione diverso. il convertitore di valori è possibile usare il `targetType` argomento per eseguire una conversione per il tipo corretto.

Se la conversione viene eseguita è diversa per diverse impostazioni cultura, usare il `culture` parametro per questo scopo. Il `parameter` argomento per `Convert` e `ConvertBack` è descritto più avanti in questo articolo.

## <a name="binding-converter-properties"></a>Proprietà di associazione del convertitore

Le classi del convertitore di valori possono avere proprietà e i parametri generici. Converte questo convertitore di valori specifico un `bool` dell'origine da un oggetto di tipo `T` per la destinazione:

```csharp
public class BoolToObjectConverter<T> : IValueConverter
{
    public T TrueObject { set; get; }

    public T FalseObject { set; get; }

    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? TrueObject : FalseObject;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return ((T)value).Equals(TrueObject);
    }
}
```

Il **commutatore indicatori** pagina viene illustrato come può essere usato per visualizzare il valore di un `Switch` vista. Anche se è comune per creare un'istanza di convertitori di valori come risorse in un dizionario risorse, questa pagina viene illustrata un'alternativa: viene creata un'istanza di ogni convertitore di valori tra `Binding.Converter` i tag di elemento di proprietà. Il `x:TypeArguments` indica l'argomento generico, e `TrueObject` e `FalseObject` sono impostati entrambi su oggetti di quel tipo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SwitchIndicatorsPage"
             Title="Switch Indicators">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>

            <Style TargetType="Switch">
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Subscribe?" />
            <Switch x:Name="switch1" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch1}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Of course!"
                                                         FalseObject="No way!" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Allow popups?" />
            <Switch x:Name="switch2" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Yes"
                                                         FalseObject="No" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
                <Label.TextColor>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Color"
                                                         TrueObject="Green"
                                                         FalseObject="Red" />
                        </Binding.Converter>
                    </Binding>
                </Label.TextColor>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Learn more?" />
            <Switch x:Name="switch3" />
            <Label FontSize="18"
                   VerticalOptions="Center">
                <Label.Style>
                    <Binding Source="{x:Reference switch3}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Style">
                                <local:BoolToObjectConverter.TrueObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Indubitably!" />
                                        <Setter Property="FontAttributes" Value="Italic, Bold" />
                                        <Setter Property="TextColor" Value="Green" />
                                    </Style>                                    
                                </local:BoolToObjectConverter.TrueObject>

                                <local:BoolToObjectConverter.FalseObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Maybe later" />
                                        <Setter Property="FontAttributes" Value="None" />
                                        <Setter Property="TextColor" Value="Red" />
                                    </Style>
                                </local:BoolToObjectConverter.FalseObject>
                            </local:BoolToObjectConverter>
                        </Binding.Converter>
                    </Binding>
                </Label.Style>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Nell'ultimo dei tre `Switch` e `Label` coppie, l'argomento generico è impostata su `Style`e intera `Style` vengono forniti per i valori di oggetti `TrueObject` e `FalseObject`. Questi override lo stile implicito per `Label` impostato nel dizionario risorse, pertanto, le proprietà dello stile vengono assegnate in modo esplicito il `Label`. Attivare e disattivare la `Switch` fa sì che la corrispondente `Label` in modo da riflettere la modifica:

[![Passare gli indicatori](converters-images/switchindicators-small.png "Switch indicatori")](converters-images/switchindicators-large.png#lightbox "passare gli indicatori")

È anche possibile usare [ `Triggers` ](~/xamarin-forms/app-fundamentals/triggers.md) implementare modifiche simili nell'interfaccia utente basata su altre viste.

## <a name="binding-converter-parameters"></a>Parametri di associazione del convertitore

Il `Binding` classe definisce un [ `ConverterParameter` ](xref:Xamarin.Forms.Binding.ConverterParameter) proprietà e il `Binding` estensione di markup definisce anche un [ `ConverterParameter` ](xref:Xamarin.Forms.Xaml.BindingExtension.ConverterParameter) proprietà. Se questa proprietà è impostata, quindi il valore viene passato per il `Convert` e `ConvertBack` metodi come il `parameter` argomento. Anche se l'istanza del convertitore di valori è condivisa tra le diverse associazioni dati, il `ConverterParameter` possono essere diversi per eseguire conversioni in qualche modo diverse.

L'uso di `ConverterParameter` è illustrata con un programma di selezione colore. In questo caso, il `RgbColorViewModel` presenta tre proprietà di tipo `double` denominate `Red`, `Green`, e `Blue` che viene usato per costruire un `Color` valore:

```csharp
public class RgbColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Red
    {
        set
        {
            if (color.R != value)
            {
                Color = new Color(value, color.G, color.B);
            }
        }
        get
        {
            return color.R;
        }
    }

    public double Green
    {
        set
        {
            if (color.G != value)
            {
                Color = new Color(color.R, value, color.B);
            }
        }
        get
        {
            return color.G;
        }
    }

    public double Blue
    {
        set
        {
            if (color.B != value)
            {
                Color = new Color(color.R, color.G, value);
            }
        }
        get
        {
            return color.B;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Red"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Green"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Blue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));

                Name = NamedColor.GetNearestColorName(color);
            }
        }
        get
        {
            return color;
        }
    }

    public string Name
    {
        private set
        {
            if (name != value)
            {
                name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Name"));
            }
        }
        get
        {
            return name;
        }
    }
}
```

Il `Red`, `Green`, e `Blue` intervallo proprietà compreso tra 0 e 1. Tuttavia, si potrebbe preferire che i componenti visualizzati come valori esadecimali a due cifre.

Per visualizzare queste informazioni come valori esadecimali in XAML, deve essere moltiplicati 255, convertiti in un intero e quindi formattati con una specifica di "X2" nel `StringFormat` proprietà. Le prime due attività (moltiplicando 255 e conversione in un numero intero) possono essere gestite dal convertitore di valori. Affinché il convertitore di valori come generalizzata possibile, il fattore di moltiplicazione può essere specificato con il `ConverterParameter` proprietà, il che significa che passa il `Convert` e `ConvertBack` metodi come il `parameter` argomento:

```csharp
public class DoubleToIntConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)Math.Round((double)value * GetParameter(parameter));
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value / GetParameter(parameter);
    }

    double GetParameter(object parameter)
    {
        if (parameter is double)
            return (double)parameter;

        else if (parameter is int)
            return (int)parameter;

        else if (parameter is string)
            return double.Parse((string)parameter);

        return 1;
    }
}
```

Il `Convert` converte da un `double` a `int` durante la moltiplicazione per il `parameter` value; la `ConvertBack` divide il valore integer `value` argomento in base alla `parameter` e restituisce un `double` risultato. (Il programma illustrato di seguito, il convertitore di valori viene utilizzato soltanto per la formattazione delle stringhe, pertanto `ConvertBack` non viene utilizzato.)

Il tipo del `parameter` argomento è probabilmente diverso a seconda del fatto che il data binding è definito nel codice o XAML. Se il `ConverterParameter` proprietà di `Binding` è impostato nel codice, è probabile che essere impostata su un valore numerico:

```csharp
binding.ConverterParameter = 255;
```

Il `ConverterParameter` proprietà è di tipo `Object`, in modo che il compilatore C# interpreta il valore letterale 255 come integer e imposta la proprietà su tale valore.

In XAML, tuttavia, il `ConverterParameter` è probabile che l'impostazione simile al seguente:

```xaml
<Label Text="{Binding Red,
                      Converter={StaticResource doubleToInt},
                      ConverterParameter=255,
                      StringFormat='Red = {0:X2}'}" />
```

I 255 appare come un numero, ma poiché `ConverterParameter` JE typu `Object`, il parser XAML considera i 255 sotto forma di stringa.

Per questo motivo, il convertitore di valori illustrato in precedenza include un oggetto separato `GetParameter` metodo che gestisce i casi per `parameter` di tipo `double`, `int`, o `string`.  

Il **selettore di colore RGB** crea un'istanza di pagina `DoubleToIntConverter` nel proprio dizionario risorse dopo la definizione di stili impliciti due:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.RgbColorSelectorPage"
             Title="RGB Color Selector">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <local:DoubleToIntConverter x:Key="doubleToInt" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:RgbColorViewModel Color="Gray" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Red}" />
            <Label Text="{Binding Red,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Red = {0:X2}'}" />

            <Slider Value="{Binding Green}" />
            <Label Text="{Binding Green,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Green = {0:X2}'}" />

            <Slider Value="{Binding Blue}" />
            <Label>
                <Label.Text>
                    <Binding Path="Blue"
                             StringFormat="Blue = {0:X2}"
                             Converter="{StaticResource doubleToInt}">
                        <Binding.ConverterParameter>
                            <x:Double>255</x:Double>
                        </Binding.ConverterParameter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

I valori del `Red` e `Green` verranno visualizzate le proprietà con un `Binding` estensione di markup. Il `Blue` proprietà, tuttavia, crea un'istanza di `Binding` classe per illustrare la modalità esplicita `double` valore può essere impostato su `ConverterParameter` proprietà.

Ecco il risultato:

[![Selettore di colore RGB](converters-images/rgbcolorselector-small.png "selettore di colore RGB")](converters-images/rgbcolorselector-large.png#lightbox "selettore di colore RGB")


## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capitolo di associazione di dati dal libro di xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
