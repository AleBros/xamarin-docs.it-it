---
title: Con dispositivo di scorrimento
description: Usare un dispositivo di scorrimento per selezionare da un intervallo di valori continui.
ms.topic: article
ms.prod: xamarin
ms.assetid: 36B1C645-26E0-4874-B6B6-BDBF77662878
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/16/2018
ms.openlocfilehash: ce5d8c46282d3831edcf58af63b66d1f6292f75b
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2018
---
# <a name="using-slider"></a>Con dispositivo di scorrimento

_Usare un dispositivo di scorrimento per selezionare da un intervallo di valori continui._

Il xamarin. Forms [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) è una barra orizzontale che può essere modificata dall'utente per selezionare un `double` valore da un intervallo continuo. 

Il `Slider` definisce tre proprietà di tipo `double`:

- [`Minimum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Minimum/) è il valore minimo dell'intervallo, con valore predefinito è 0.
- [`Maximum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Maximum/) è il valore massimo dell'intervallo, con valore predefinito è 1.
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Value/) è il valore del dispositivo di scorrimento, che può essere compreso tra `Minimum` e `Maximum` e ha un valore predefinito pari a 0.

Le tre proprietà sono supportate da `BindableProperty` oggetti. Il `Value` proprietà dispone di una modalità di associazione predefinito di `BindingMode.TwoWay`, che significa che è adatto come un'origine di associazione in un'applicazione che utilizza il [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) architettura. 

> [!WARNING]
> Internamente, il `Slider` garantisce che `Minimum` è inferiore a `Maximum`. Se `Minimum` oppure `Maximum` sono sempre impostate in modo che `Minimum` è non minore di `Maximum`, viene generata un'eccezione. Vedere la [ **precauzioni** ](#precautions) sezione riportata di seguito per ulteriori informazioni sull'impostazione di `Minimum` e `Maximum` proprietà.

Il `Slider` forza la `Value` proprietà in modo che sia compreso tra `Minimum` e `Maximum`, estremi inclusi. Se il `Minimum` proprietà è impostata su un valore maggiore di `Value` proprietà, il `Slider` imposta il `Value` proprietà da `Minimum`. Analogamente, se `Maximum` è impostata su un valore minore `Value`, quindi `Slider` imposta la `Value` proprietà `Maximum`. 

`Slider` definisce una [ `ValueChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Slider.ValueChanged/) evento generato quando il `Value` modifiche, tramite la modifica dell'utente del `Slider` o quando il programma imposta la `Value` proprietà direttamente. Un `ValueChanged` evento viene generato anche quando il `Value` proprietà viene convertita come descritto nel paragrafo precedente. 

Il [ `ValueChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ValueChangedEventArgs/) oggetto che accompagna il `ValueChanged` evento ha due proprietà, entrambi di tipo `double`: [ `OldValue` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ValueChangedEventArgs.OldValue/) e [ `NewValue` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ValueChangedEventArgs.NewValue/). Al momento viene generato l'evento, il valore di `NewValue` equivale il `Value` proprietà del `Slider` oggetto.

> [!WARNING]
> Non utilizzare opzioni di layout orizzontale non vincolato della `Center`, `Start`, o `End` con `Slider`. In Android e la piattaforma UWP, il `Slider` viene compressa a una barra di lunghezza zero e nelle versioni iOS, la barra è molto breve. Mantenere il valore predefinito `HorizontalOptions` impostazione di `Fill`e non usano una larghezza pari `Auto` quando si inserisce `Slider` in un `Grid` layout.

## <a name="basic-slider-code-and-markup"></a>Markup e codice di base dispositivo di scorrimento

Il [ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) esempio inizia con tre pagine che sono funzionalmente identico, ma vengono implementate in modi diversi. La prima pagina utilizza solo il codice c#, la seconda Usa XAML con un gestore eventi nel codice e il terzo è in grado di evitare il gestore dell'evento usando l'associazione dati nel file XAML.

### <a name="creating-a-slider-in-code"></a>Creazione di un dispositivo di scorrimento nel codice

Il **base codice dispositivo di scorrimento** nella pagina di [ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) illustra Mostra come creare un `Slider` e due `Label` gli oggetti nel codice:

```csharp
public class BasicSliderCodePage : ContentPage
{
    public BasicSliderCodePage()
    {
        Label rotationLabel = new Label
        {
            Text = "ROTATING TEXT",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Label displayLabel = new Label
        {
            Text = "(uninitialized)",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Slider slider = new Slider
        {
            Maximum = 360
        };
        slider.ValueChanged += (sender, args) =>
        {
            rotationLabel.Rotation = slider.Value;
            displayLabel.Text = String.Format("The Slider value is {0}", args.NewValue);
        };

        Title = "Basic Slider Code";
        Padding = new Thickness(10, 0);
        Content = new StackLayout
        {
            Children =
            {
                rotationLabel,
                slider,
                displayLabel
            }
        };
    }
}
```

Il `Slider` viene inizializzata per avere un `Maximum` proprietà 360. Il `ValueChanged` gestore del `Slider` Usa il `Value` proprietà del `slider` oggetto da impostare il `Rotation` proprietà del primo `Label` e utilizza il `String.Format` metodo con il `NewValue` proprietà del argomenti dell'evento per impostare il `Text` proprietà del secondo `Label`. Questi due approcci per ottenere il valore corrente del `Slider` sono intercambiabili. 

Ecco il programma in esecuzione su iOS, Android e Windows della piattaforma UWP (Universal) i dispositivi:

[![Codice di base dispositivo di scorrimento](slider-images/BasicSliderCode.png "codice base dispositivo di scorrimento")](slider-images/BasicSliderCode-Large.png#lightbox)

Il secondo `Label` viene visualizzato il testo "(non inizializzato)" finché il `Slider` viene modificato, quali casi il primo `ValueChanged` evento di attivazione. Si noti che il numero di posizioni decimali a cui vengono visualizzati diverso per tre piattaforme. Queste differenze riguardano le implementazioni di piattaforma del `Slider` e sono descritti più avanti in questo articolo nella sezione [le differenze di piattaforma implementazione](#implementations).

### <a name="creating-a-slider-in-xaml"></a>Creazione di un dispositivo di scorrimento in XAML

Il **XAML di base dispositivo di scorrimento** pagina è funzionalmente identico **il codice di dispositivo di scorrimento base** ma implementato principalmente in XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderXamlPage"
             Title="Basic Slider XAML"
             Padding="10, 0">
    <StackLayout>
        <Label x:Name="rotatingLabel" 
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider Maximum="360"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Il file code-behind contiene il gestore per il `ValueChanged` evento:

```csharp
public partial class BasicSliderXamlPage : ContentPage
{
    public BasicSliderXamlPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double value = args.NewValue;
        rotatingLabel.Rotation = value;
        displayLabel.Text = String.Format("The Slider value is {0}", value);
    }
}
```

È anche possibile che il gestore dell'evento ottenere il `Slider` che sta generando l'evento attraverso il `sender` argomento. Il `Value` proprietà contiene il valore corrente:

```csharp
double value = ((Slider)sender).Value;
```

Se il `Slider` oggetto assegnato un nome nel file XAML con un `x:Name` attributo (ad esempio, "slider"), quindi il gestore dell'evento può fare riferimento a quell'oggetto direttamente:

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>Il dispositivo di scorrimento di associazione dati

Il **base le associazioni dispositivo di scorrimento** pagina viene illustrato come scrivere un programma quasi equivalente che elimina il `Value` gestore dell'evento tramite [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderBindingsPage"
             Title="Basic Slider Bindings"
             Padding="10, 0">
    <StackLayout>
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference slider}, 
                                  Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360" />

        <Label x:Name="displayLabel"
               Text="{Binding Source={x:Reference slider}, 
                              Path=Value, 
                              StringFormat='The Slider value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Il `Rotation` proprietà del primo `Label` è associato al `Value` proprietà del `Slider`è il `Text` proprietà del secondo `Label` con un `StringFormat` specifica. Il **base le associazioni dispositivo di scorrimento** pagina funzioni leggermente in modo diverso da due pagine precedenti: quando viene visualizzata la pagina, il secondo `Label` consente di visualizzare la stringa di testo con il valore. Si tratta di un vantaggio dell'utilizzo del data binding. Per visualizzare testo senza l'associazione dati, è necessario inizializzare in modo specifico il `Text` proprietà del `Label` o si simula l'attivazione del `ValueChanged` evento chiamando il gestore dell'evento dal costruttore della classe.

<a name="precautions" />

## <a name="precautions"></a>Precauzioni

Il valore di `Minimum` proprietà deve sempre essere minore del valore del `Maximum` proprietà. Vengono illustrate le cause di frammento di codice seguente il `Slider` per generare un'eccezione:

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

Il compilatore c# genera codice che consente di impostare queste due proprietà in sequenza, e quando le `Minimum` è impostata su 10, è superiore a quello predefinito `Maximum` pari a 1. È possibile evitare l'eccezione in questo caso, impostare il `Maximum` proprietà primo:

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

L'impostazione `Maximum` 20 non costituisce un problema perché è superiore a quello predefinito `Minimum` impostazione pari a 0. Quando si `Minimum` è impostata, il valore è inferiore a quello di `Maximum` valore pari a 20.

Lo stesso problema esistente in XAML. Impostare le proprietà in un ordine che assicura che `Maximum` è sempre maggiore `Minimum`:

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

È possibile impostare il `Minimum` e `Maximum` i valori per i numeri negativi, ma solo in un ordine in cui `Minimum` è sempre minore `Maximum`:

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

Il `Value` proprietà è sempre maggiore o uguale al `Minimum` valore e minore o uguale a `Maximum`. Se `Value` è impostata su un valore incluso in tale intervallo, verrà assegnato forzatamente il valore per l'intervallo è compreso, ma viene generata alcuna eccezione. Ad esempio, questo codice verrà *non* generare un'eccezione:

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

Al contrario, il `Value` proprietà viene assegnata al `Maximum` pari a 1.

Di seguito è riportato un frammento di codice illustrato in precedenza: 

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

Quando si `Minimum` è impostato su 10, quindi `Value` anche viene impostato su 10. 

Se un `ValueChanged` gestore dell'evento è stato collegato al momento che il `Value` proprietà viene assegnata un nome diverso dal valore predefinito pari a 0, quindi un `ValueChanged` viene generato l'evento. Di seguito è riportato un frammento di XAML: 

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

Quando si `Minimum` è impostato su 10, `Value` è impostata su 10 e il `ValueChanged` viene generato l'evento. Questa situazione può verificarsi prima che una volta creato il resto della pagina e il gestore potrebbe provare a fare riferimento agli altri elementi nella pagina che non sono ancora state create. È possibile aggiungere il codice per il `ValueChanged` gestore che verifica la presenza di `null` valori degli altri elementi nella pagina. Oppure, è possibile impostare il `ValueChanged` gestore dell'evento dopo il `Slider` valori siano stati inizializzati.

<a name="implementations" />

## <a name="platform-implementation-differences"></a>Differenze di implementazione della piattaforma

La schermata illustrata in precedenza Visualizza il valore della `Slider` con un diverso numero di posizioni decimali. Questa informazione è correlata come il `Slider` implementato per le piattaforme Android e UWP.

### <a name="the-android-implementation"></a>L'implementazione di Android 

L'implementazione di Android `Slider` basa le Android [ `SeekBar` ](https://developer.xamarin.com/api/type/Android.Widget.SeekBar/) e imposta sempre la [ `Max` ](https://developer.xamarin.com/api/property/Android.Widget.ProgressBar.Max/) proprietà a 1000. Ciò significa che il `Slider` in Android presenta solo 1.001 valori discreti. Se si imposta la `Slider` disporre di un `Minimum` pari a 0 e una `Maximum` pari a 5000, quindi come il `Slider` viene modificato, il `Value` proprietà presenta valori pari a 0, 5, 10, 15 e così via. 

### <a name="the-uwp-implementation"></a>L'implementazione di piattaforma UWP

L'implementazione di piattaforma UWP di `Slider` basa alla piattaforma UWP [ `Slider` ](/uwp/api/windows.ui.xaml.controls.slider) controllo. Il `StepFrequency` proprietà alla piattaforma UWP `Slider` è impostato per la differenza tra il `Maximum` e `Minimum` proprietà diviso per 10, ma non maggiore di 1. 

Ad esempio, per l'intervallo predefinito di 0 e 1, il `StepFrequency` è impostata su 0,1. Come le `Slider` viene modificato, il `Value` proprietà è limitata a 0, 0,1, 0,2, 0,3, 0,4, 0,5, 0,6, 0,7, 0,8, 0,9 e 1.0. (Ciò è evidente nell'ultima pagina di [ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) esempio.) Quando la differenza tra i `Maximum` e `Minimum` delle proprietà è 10 o versione successiva, quindi `StepFrequency` è impostato su 1 e il `Value` proprietà presenta valori integrali. 

### <a name="the-stepslider-solution"></a>La soluzione StepSlider

Più versatile `StepSlider` verrà discusso [capitolo 27. Renderer personalizzato](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf) del libro *creazione di App per dispositivi mobili con xamarin. Forms*. Il `StepSlider` è simile a `Slider` ma aggiunge un `Steps` proprietà per specificare il numero di valori tra `Minimum` e `Maximum`.

## <a name="sliders-for-color-selection"></a>Dispositivi di scorrimento per la selezione dei colori

Gli ultimi due pagine di [ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) esempio utilizzano entrambi tre `Slider` istanze per la selezione dei colori. La prima pagina gestisce tutte le interazioni nel file code-behind, mentre la seconda pagina viene illustrato come utilizzare l'associazione dati con un ViewModel. 

### <a name="handling-sliders-in-the-code-behind-file"></a>La gestione di dispositivi di scorrimento nel file code-behind 

Il **cursori di colore RGB** crea un'istanza di pagina un `BoxView` per visualizzare un colore, tre `Slider` istanze per selezionare i componenti rossi, verde e blu del colore e tre `Label` elementi per la visualizzazione di tali colore valori:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.RgbColorSlidersPage"
             Title="RGB Color Sliders">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="Maximum" Value="255" />
            </Style>
            
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView x:Name="boxView"
                 Color="Black"
                 VerticalOptions="FillAndExpand" />

        <Slider x:Name="redSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="redLabel" />

        <Slider x:Name="greenSlider" 
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="greenLabel" />

        <Slider x:Name="blueSlider" 
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="blueLabel" />
    </StackLayout>
</ContentPage>
```

Un `Style` offre tutte e tre `Slider` elementi un intervallo da 0 a 255. Il `Slider` elementi condividono lo stesso `ValueChanged` gestore, che viene implementata nel file code-behind:

```csharp
public partial class RgbColorSlidersPage : ContentPage
{
    public RgbColorSlidersPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (sender == redSlider)
        {
            redLabel.Text = String.Format("Red = {0:X2}", (int)args.NewValue);
        }
        else if (sender == greenSlider)
        {
            greenLabel.Text = String.Format("Green = {0:X2}", (int)args.NewValue);
        }
        else if (sender == blueSlider)
        {
            blueLabel.Text = String.Format("Blue = {0:X2}", (int)args.NewValue);
        }

        boxView.Color = Color.FromRgb((int)redSlider.Value,
                                      (int)greenSlider.Value,
                                      (int)blueSlider.Value);
    }
}
```

Il primo set di sezione di `Text` proprietà di uno del `Label` istanze per una breve stringa di testo che indica il valore del `Slider` in formato esadecimale. Quindi, tutti e tre `Slider` le istanze sono accessibili per creare un `Color` valore dai componenti RGB:

[![I cursori di colore RGB](slider-images/RgbColorSliders.png "cursori di colore RGB")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>Il dispositivo di scorrimento di associazione a un elemento ViewModel

Il **cursori colore HSL** pagina viene illustrato come utilizzare un ViewModel per eseguire i calcoli utilizzati per creare un `Color` valore da valori di tonalità, saturazione e luminosità. Come ViewModel tutti, il `HSLColorViewModel` implementa la `INotifyPropertyChanged` interfaccia e genera un `PropertyChanged` evento ogni volta che una delle proprietà cambia:

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Hue
    {
        set
        {
            if (color.Hue != value)
            {
                Color = Color.FromHsla(value, color.Saturation, color.Luminosity);
            }
        }
        get 
        {
            return color.Hue;
        }
    }

    public double Saturation
    {
        set
        {
            if (color.Saturation != value)
            {
                Color = Color.FromHsla(color.Hue, value, color.Luminosity);
            }
        }
        get
        {
            return color.Saturation;
        }
    }

    public double Luminosity
    {
        set
        {
            if (color.Luminosity != value)
            {
                Color = Color.FromHsla(color.Hue, color.Saturation, value);
            }
        }
        get
        {
            return color.Luminosity;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Hue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Saturation"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Luminosity"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));
            }
        }
        get
        {
            return color;
        }
    }
}
```

ViewModel e il `INotifyPropertyChanged` interfaccia vengono descritti nell'articolo [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Il **HslColorSlidersPage.xaml** file crea un'istanza di `HslColorViewModel` e lo imposta per la pagina `BindingContext` proprietà. In questo modo tutti gli elementi nel file XAML da associare alla proprietà di ViewModel:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SliderDemos"
             x:Class="SliderDemos.HslColorSlidersPage"
             Title="HSL Color Sliders">

    <ContentPage.BindingContext>
        <local:HslColorViewModel Color="Chocolate" />
    </ContentPage.BindingContext>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</ContentPage> 
```

Come le `Slider` vengono modificati gli elementi di `BoxView` e `Label` elementi sono aggiornati dal ViewModel:

[![I cursori di colore HSL](slider-images/HslColorSliders.png "cursori colore HSL")](slider-images/HslColorSliders-Large.png#lightbox)

Il `StringFormat` componente del `Binding` estensione di markup è impostato per un formato di "F2" per visualizzare due posizioni decimali. (Stringa di formattazione i data binding è descritto nell'articolo [la formattazione della stringa](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md).) Tuttavia, la versione della piattaforma UWP del programma è limitata ai valori di 0, 0,1, 0,2,... 0,9 e 1.0. Si tratta di un risultato diretto dell'implementazione di alla piattaforma UWP `Slider` come descritto in precedenza nella sezione [le differenze di piattaforma implementazione](#implementations).

## <a name="related-links"></a>Collegamenti correlati

- [Esempio Demo di Slider](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)
- [Cursore API](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)
