---
title: Convertitori dei valori di binding di Xamarin.Forms
description: Questo articolo illustra come eseguire il cast o convertire i valori all'interno di un data binding di Xamarin.Forms tramite l'implementazione di un convertitore di valori (noto anche come convertitore di data binding o convertitore di valori di binding).
ms.prod: xamarin
ms.assetid: 02B1BBE6-D804-490D-BDD4-8ACED8B70C92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 28892692133020de1fa5a6eb007bb3f9bcf2612b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997484"
---
# <a name="xamarinforms-binding-value-converters"></a>Convertitori dei valori di binding di Xamarin.Forms

In genere i data binding trasferiscono i dati da una proprietà di origine a una proprietà di destinazione e in alcuni casi dalla proprietà di destinazione alla proprietà di origine. Si tratta di un semplice trasferimento quando la proprietà di origine e quella di destinazione sono dello stesso tipo o quando un tipo può essere convertito nell'altro tipo tramite una conversione implicita. Se non è questo il caso, è necessaria una conversione del tipo.

Nell'articolo [**Formattazione delle stringhe**](string-formatting.md) è stato illustrato come usare la proprietà `StringFormat` di un data binding per convertire un tipo qualsiasi in una stringa. Per altri tipi di conversioni, è necessario scrivere codice specializzato in una classe che implementa l'interfaccia [`IValueConverter`](xref:Xamarin.Forms.IValueConverter). La piattaforma UWP (Universal Windows Platform) contiene una classe simile denominata [`IValueConverter`](/uwp/api/Windows.UI.Xaml.Data.IValueConverter/) nello spazio dei nomi `Windows.UI.Xaml.Data`. In questo caso `IValueConverter` si trova nello spazio dei nomi `Xamarin.Forms`. Le classi che implementano `IValueConverter` vengono chiamate *convertitori di valori*, ma vengono anche definite *convertitori di data binding* oppure *convertitori di valori di binding*.

## <a name="the-ivalueconverter-interface"></a>Interfaccia di IValueConverter

Si supponga di voler definire un data binding in cui la proprietà di origine è di tipo `int` mentre la proprietà di destinazione è di tipo `bool`. Questo data binding deve generare un valore `false` quando l'origine del numero intero è uguale a 0; in caso contrario, deve generare `true`.  

A tal proposito, è necessaria una classe che implementa l'interfaccia `IValueConverter`:

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

Impostare un'istanza di questa classe per la proprietà [`Converter`](xref:Xamarin.Forms.Binding.Converter) della classe `Binding` o per la proprietà [`Converter`](xref:Xamarin.Forms.Xaml.BindingExtension.Converter) dell'estensione di markup `Binding`. Questa classe diventa parte dal data binding.

Il metodo `Convert` viene chiamato quando i dati vengono trasferiti dall'origine alla destinazione in binding `OneWay` o `TwoWay`. Il parametro `value` rappresenta l'oggetto o il valore ricevuto dall'origine del data binding. Il metodo deve restituire un valore del tipo della destinazione del data binding. Il metodo illustrato di seguito esegue il cast del parametro `value` per un valore `int` e lo confronta con 0 per un valore restituito `bool`.

Il metodo `ConvertBack` viene chiamato quando i dati vengono trasferiti dalla destinazione all'origine in binding `TwoWay` o `OneWayToSource`. Il metodo `ConvertBack` esegue la conversione inversa. Suppone che il parametro `value` sia un tipo `bool` della destinazione e lo converte in valore restituito `int` per l'origine.

Se il data binding include anche un'impostazione `StringFormat`, il convertitore di valori viene richiamato prima che il risultato sia formattato come stringa.

La pagina **Enable Buttons** (Abilita pulsanti) nell'esempio [**Data Binding Demos**](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) (Demo del data binding) illustra come usare questo convertitore di valori in un data binding. Viene creata un'istanza di `IntToBoolConverter` nel dizionario risorse della pagina, a cui si fa riferimento con un'estensione di markup `StaticResource` per impostare la proprietà `Converter` nei due data binding. È prassi molto comune condividere i convertitori i dati tra più data binding nella pagina:

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

Se un convertitore di valori viene usato in più pagine dell'applicazione, è possibile crearne un'istanza nel dizionario risorse all'interno del file **App.xaml**.

La pagina **Enable Buttons** (Abilita pulsanti) illustra un'esigenza comune che si verifica quando `Button` esegue un'operazione sulla base del teso digitato dall'utente nella visualizzazione `Entry`. Se in `Entry` non viene digitato nulla, `Button` sarà disabilitato. Ogni oggetto `Button` contiene un data binding nella relativa proprietà `IsEnabled`. L'origine del data binding è la proprietà `Length` della proprietà `Text` dell'oggetto `Entry` corrispondente. Se la proprietà `Length` non è 0, il convertitore di valori restituisce `true` e `Button` viene abilitato:

[![Enable Buttons](converters-images/enablebuttons-small.png "Enable Buttons")](converters-images/enablebuttons-large.png#lightbox "Enable Buttons")

Si noti che la proprietà `Text` in ogni oggetto `Entry` viene inizializzata per una stringa vuota. La proprietà `Text` è `null` per impostazione predefinita. In questo caso, il data binding non funzionerà.

Alcuni convertitori di valori vengono scritti specificatamente per determinate applicazioni, mentre altri hanno un uso più generico. Se si sa che un convertitore di valori non viene usato nei binding `OneWay`, il metodo `ConvertBack` potrà restituire soltanto `null`.

Il metodo `Convert` illustrato in precedenza presuppone in modo implicito che l'argomento `value` sia di tipo `int` e il valore restituito debba essere di tipo `bool`. Analogamente, il metodo `ConvertBack` presuppone che l'argomento `value` è di tipo `bool` e il valore restituito è `int`. Se non è questo il caso, verrà generata un'eccezione in fase di esecuzione.

È possibile scrivere convertitori di valori in modo che siano più generici e accettino diversi tipi di dati. I metodi `Convert` e `ConvertBack` possono usare gli operatori `as` e `is` con il parametro `value` oppure possono chiamare `GetType` per tale parametro al fine di determinarne il tipo e procedere nel modo appropriato. Il tipo previsto di ogni valore restituito del metodo viene specificato dal parametro `targetType`. In alcuni casi, i convertitori di valori vengono usati con i data binding di tipi diversi di destinazione. Il convertitore di valori può usare l'argomento `targetType` per convertire nel tipo corretto.

Se la conversione eseguita varia a seconda delle impostazioni cultura, usare il parametro `culture`. L'argomento `parameter` per `Convert` e `ConvertBack` viene illustrato più avanti in questo articolo.

## <a name="binding-converter-properties"></a>Proprietà del convertitore di data binding

Le classi del convertitore di valori possono avere proprietà e parametri generici. Questo convertire di valori specifico converte un valore `bool` dell'origine in un oggetto di tipo `T` per la destinazione:

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

La pagina **Switch Indicators** (Cambia indicatori) illustra come può essere usato per visualizzare il valore di una visualizzazione `Switch`. Generalmente si crea un'istanza dei convertitori di valori come risorse in un dizionario risorse. Questa pagina illustra un'alternativa: viene creata un'istanza di ogni convertitore di valori tra tag proprietà-elemento `Binding.Converter`. `x:TypeArguments` indica l'argomento generico. `TrueObject` e `FalseObject` sono entrambi impostati su oggetti di quel tipo:

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

Nell'ultima delle tre coppie `Switch` e `Label` l'argomento generico è impostato su `Style`, mentre gli oggetti `Style` interi vengono specificati per i valori di `TrueObject` e `FalseObject`. Questi eseguono l'override dello stile implicito per `Label` impostato nel dizionario risorse. Le proprietà in tale stile vengono quindi assegnate in modo esplicito all'oggetto `Label`. L'attivazione/disattivazione di `Switch` determina la modifica dell'oggetto `Label` corrispondente:

[![Switch Indicators](converters-images/switchindicators-small.png "Switch Indicators")](converters-images/switchindicators-large.png#lightbox "Switch Indicators")

È anche possibile usare [`Triggers`](~/xamarin-forms/app-fundamentals/triggers.md) per implementare modifiche simili nell'interfaccia utente basata su altre visualizzazioni.

## <a name="binding-converter-parameters"></a>Parametri del convertitore di data binding

La classe `Binding` definisce una proprietà [`ConverterParameter`](xref:Xamarin.Forms.Binding.ConverterParameter). Anche l'estensione di markup `Binding` definisce una proprietà [`ConverterParameter`](xref:Xamarin.Forms.Xaml.BindingExtension.ConverterParameter). Se si imposta questa proprietà, il valore viene passato ai metodi `Convert` e `ConvertBack` come argomento `parameter`. Sebbene l'istanza del convertitore di valori sia condivisa tra diversi data binding, la proprietà `ConverterParameter` può essere diversa in modo da eseguire conversioni leggermente diverse.

L'uso di `ConverterParameter` viene illustrato con un programma di selezione a colori. In questo caso `RgbColorViewModel` contiene tre proprietà di tipo `double` denominate `Red`, `Green`, e `Blue` che vengono usate per costruire un valore `Color`:

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

Le proprietà `Red`, `Green` e `Blue` sono comprese in un intervallo tra 0 e 1. Potrebbe tuttavia essere preferibile visualizzare i componenti come valori esadecimali a due cifre.

Per visualizzare queste informazioni come valori esadecimali in XAML, moltiplicare i valori per 255, convertirli in un numero intero e formattarli usando una specifica di "X2" nella proprietà `StringFormat`. Le prime due attività, vale a dire la moltiplicazione per 255 e la conversione in un numero intero possono essere gestite dal convertitore di valori. Perché il convertitore di valori sia il più possibile generico, è possibile specificare il fattore di moltiplicazione con la proprietà `ConverterParameter`. In questo modo i metodi `Convert` e `ConvertBack` vengono immessi come argomento `parameter`:

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

`Convert` converte da `double` in `int` moltiplicando per il valore `parameter`. `ConvertBack` divide l'argomento `value` del numero interno per `parameter` e restituisce un risultato `double`. Nel programma illustrato di seguito il convertitore di valori viene usato soltanto unitamente alla formattazione della stringa. `ConvertBack` non viene pertanto usato.

Il tipo dell'argomento `parameter` sarà probabilmente diverso a seconda del fatto che il data binding sia definito nel codice o in XAML. Se la proprietà `ConverterParameter` di `Binding` è impostata nel codice, è probabile che sia impostato un valore numerico:

```csharp
binding.ConverterParameter = 255;
```

La proprietà `ConverterParameter` è di tipo `Object`, pertanto il compilatore C# interpreta il valore letterale 255 come numero intero e imposta la proprietà su tale valore.

In XAML invece `ConverterParameter` è probabile che venga impostato nel modo seguente:

```xaml
<Label Text="{Binding Red,
                      Converter={StaticResource doubleToInt},
                      ConverterParameter=255,
                      StringFormat='Red = {0:X2}'}" />
```

255 appare come numero, ma dal momento che `ConverterParameter` è di tipo `Object`, il parser XAML considera 255 come stringa.

Per questo motivo, il convertitore di valori illustrato in precedenza include un metodo `GetParameter` distinto che gestisce i casi di `parameter` quando è di tipo `double`, `int` o `string`.  

La pagina **RGB Color Selector** (Selettore colori RGB) crea un'istanza `DoubleToIntConverter` nel rispettivo dizionario risorse seguendo la definizione di due stili impliciti:

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

I valori delle proprietà `Red` e `Green` vengono visualizzati con un'estensione di markup `Binding`. La proprietà `Blue`, tuttavia, crea un'istanza della classe `Binding` per illustrare come impostare un valore `double` esplicito per la proprietà `ConverterParameter`.

Il risultato è il seguente:

[![RGB Color Selector](converters-images/rgbcolorselector-small.png "RGB Color Selector")](converters-images/rgbcolorselector-large.png#lightbox "RGB Color Selector")


## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demos (sample)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) (Demo di esempio del data binding)
- [Capitolo sul data binding nella documentazione di Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
