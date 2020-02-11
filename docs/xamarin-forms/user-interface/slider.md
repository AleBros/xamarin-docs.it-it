---
title: Dispositivo di scorrimento di Xamarin.Forms
description: Il dispositivo di scorrimento di Xamarin.Forms è una barra orizzontale che può essere modificata dall'utente per selezionare un valore double da un intervallo continuo. Questo articolo illustra come usare la classe dispositivo di scorrimento per selezionare un valore da un intervallo di valori continui.
ms.prod: xamarin
ms.assetid: 36B1C645-26E0-4874-B6B6-BDBF77662878
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2019
ms.openlocfilehash: ddb25a1f01f91b627fc0c370f7f21e2a797b72cb
ms.sourcegitcommit: 191f1f3b13a14e2afadcb95126c5f653722f126f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545582"
---
# <a name="xamarinforms-slider"></a>Dispositivo di scorrimento di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)

_Usare un dispositivo di scorrimento per selezionare da un intervallo di valori continui._

Xamarin.Forms [ `Slider` ](xref:Xamarin.Forms.Slider) è una barra orizzontale che può essere modificata dall'utente per selezionare un `double` valore dall'intervallo continuo.

Il `Slider` definisce tre proprietà di tipo `double`:

- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) è il valore minimo dell'intervallo, con un valore predefinito pari a 0.
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) è il valore massimo dell'intervallo, con un valore predefinito 1.
- [`Value`](xref:Xamarin.Forms.Slider.Value) valore del dispositivo di scorrimento che può essere compreso tra `Minimum` e `Maximum` e ha un valore predefinito pari a 0.

Tutte le tre proprietà sono supportate da `BindableProperty` oggetti. Il `Value` proprietà dispone di una modalità di associazione predefinita del `BindingMode.TwoWay`, che significa che è adatto come origine del binding in un'applicazione che usa le [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) architettura.

> [!WARNING]
> Internamente, il `Slider` garantisce che `Minimum` è minore di `Maximum`. Se `Minimum` oppure `Maximum` vengono sempre impostati in modo che `Minimum` è non minore di `Maximum`, viene generata un'eccezione. Vedere le [ **precauzioni** ](#precautions) sezione di seguito per altre informazioni sull'impostazione il `Minimum` e `Maximum` proprietà.

Il `Slider` assegna le `Value` proprietà in modo che sia tra `Minimum` e `Maximum`, inclusivo. Se il `Minimum` proprietà è impostata su un valore maggiore del `Value` proprietà, il `Slider` imposta la `Value` proprietà `Minimum`. Analogamente, se `Maximum` è impostata su un valore minore di `Value`, quindi `Slider` imposta la `Value` proprietà `Maximum`.

`Slider` definisce un [ `ValueChanged` ](xref:Xamarin.Forms.Slider.ValueChanged) evento generato quando il `Value` modifiche, tramite la modifica dell'utente del `Slider` o quando il programma imposta la `Value` proprietà direttamente. Oggetto `ValueChanged` evento viene generato quando il `Value` proprietà viene convertita come descritto nel paragrafo precedente.

Il [ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs) oggetti che accompagna il `ValueChanged` evento ha due proprietà, entrambi di tipo `double`: [ `OldValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) e [ `NewValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue). Al momento viene generato l'evento, il valore di `NewValue` equivale al `Value` proprietà del `Slider` oggetto.

`Slider` definisce inoltre gli eventi `DragStarted` e `DragCompleted`, che vengono generati all'inizio e alla fine dell'azione di trascinamento. A differenza dell'evento [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) , gli eventi `DragStarted` e `DragCompleted` vengono generati solo tramite la manipolazione degli utenti del `Slider`. Quando viene generato l'evento `DragStarted`, viene eseguita la `DragStartedCommand`di tipo `ICommand`. Analogamente, quando viene generato l'evento `DragCompleted`, viene eseguita la `DragCompletedCommand`di tipo `ICommand`.

> [!WARNING]
> Non usare opzioni di layout orizzontale non vincolato della `Center`, `Start`, o `End` con `Slider`. In Android e UWP, il `Slider` viene compressa per un indicatore di lunghezza zero e in iOS, la barra è molto breve. Mantenere il valore predefinito `HorizontalOptions` impostazione di `Fill`e non usare una larghezza pari `Auto` quando si inserisce `Slider` in un `Grid` layout.

Il `Slider` definisce inoltre diverse proprietà che interessano l'aspetto del controllo:

- [`MinimumTrackColor`](xref:Xamarin.Forms.Slider.MinimumTrackColorProperty) è la barra di colore a sinistra del cursore.
- [`MaximumTrackColor`](xref:Xamarin.Forms.Slider.MaximumTrackColorProperty) è la barra dei colori a destra del cursore.
- [`ThumbColor`](xref:Xamarin.Forms.Slider.ThumbColorProperty) rappresenta il colore del cursore.
- [`ThumbImageSource`](xref:Xamarin.Forms.Slider.ThumbImageSourceProperty) è l'immagine da utilizzare per il controllo thumb, typu [ `ImageSource` ](xref:Xamarin.Forms.ImageSource).

> [!NOTE]
> Il `ThumbColor` e `ThumbImageSource` proprietà si escludono a vicenda. Se entrambe le proprietà sono impostate, il `ThumbImageSource` proprietà avrà la precedenza.

## <a name="basic-slider-code-and-markup"></a>Markup e codice di dispositivo di scorrimento di base

Il [ **SliderDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) esempio inizia con tre pagine che sono funzionalmente identici, ma vengono implementate in modi diversi. La prima pagina Usa solo il codice c#, il secondo Usa XAML con un gestore eventi nel codice e il terzo è in grado di evitare il gestore dell'evento usando l'associazione dati nel file XAML.

### <a name="creating-a-slider-in-code"></a>Creazione di un dispositivo di scorrimento nel codice

Il **dispositivo di scorrimento di base del codice** pagina il [ **SliderDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) illustra Mostra come creare un `Slider` e due `Label` gli oggetti nel codice:

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

Il `Slider` viene inizializzata per avere una `Maximum` proprietà 360. Il `ValueChanged` gestore del `Slider` Usa il `Value` proprietà del `slider` oggetto per impostare il `Rotation` proprietà del primo `Label` e Usa il `String.Format` metodo con il `NewValue` proprietà del argomenti dell'evento per impostare il `Text` proprietà del secondo `Label`. Questi due approcci per ottenere il valore corrente del `Slider` sono intercambiabili.

Ecco il programma in esecuzione su dispositivi iOS e Android:

[![Codice Slider di base](slider-images/BasicSliderCode.png "Codice Slider di base")](slider-images/BasicSliderCode-Large.png#lightbox)

Il secondo `Label` Visualizza il testo "(non inizializzato)" finché non la `Slider` manipolato, in modo che il primo `ValueChanged` evento generato. Si noti che il numero di posizioni decimali a cui vengono visualizzati diverso per ogni piattaforma. Queste differenze riguardano le implementazioni della piattaforma del `Slider` e sono descritti più avanti in questo articolo nella sezione [differenze di implementazione della piattaforma](#implementations).

### <a name="creating-a-slider-in-xaml"></a>Creazione di un dispositivo di scorrimento in XAML

Il **base XAML di dispositivo di scorrimento** pagina è funzionalmente identico **dispositivo di scorrimento di base del codice** ma implementato principalmente in XAML:

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

È anche possibile che il gestore eventi ottenere il `Slider` che sta generando l'evento attraverso il `sender` argomento. Il `Value` proprietà contiene il valore corrente:

```csharp
double value = ((Slider)sender).Value;
```

Se il `Slider` oggetto assegnato un nome nel file XAML con un `x:Name` attributo (ad esempio, "slider"), quindi il gestore dell'evento è stato possibile fare riferimento a quell'oggetto direttamente:

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>Il dispositivo di scorrimento di associazione dati

Il **associazioni di base dispositivo di scorrimento** pagina viene illustrato come scrivere un programma quasi equivalente che consente di eliminare il `Value` gestore eventi mediante [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md):

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

Il `Rotation` proprietà del primo `Label` è associato ai `Value` proprietà del `Slider`, come è la `Text` proprietà del secondo `Label` con un `StringFormat` specifica. Il **associazioni di base Slider** pagina funzioni un po' diverso da due pagine precedenti: quando la pagina viene visualizzata prima di tutto, il secondo `Label` consente di visualizzare la stringa di testo con il valore. Si tratta di un vantaggio dell'uso del data binding. Per visualizzare il testo senza l'associazione di dati, è necessario inizializzare in modo specifico la `Text` proprietà del `Label` o si simula un'attivazione del `ValueChanged` evento chiamando il gestore dell'evento dal costruttore della classe.

<a name="precautions" />

## <a name="precautions"></a>Precauzioni relative alla

Il valore della `Minimum` proprietà deve essere sempre inferiore al valore della `Maximum` proprietà. Il codice seguente frammento di codice le cause di `Slider` per generare un'eccezione:

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

Il compilatore c# genera il codice che consente di impostare le due proprietà seguenti nella sequenza, e quando le `Minimum` è impostata su 10, è superiore a quello predefinito `Maximum` valore pari a 1. È possibile evitare l'eccezione in questo caso, impostando il `Maximum` proprietà prima:

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

L'impostazione `Maximum` su 20 non costituisce un problema perché è superiore all'impostazione predefinita `Minimum` valore pari a 0. Quando `Minimum` è impostato, il valore è minore di `Maximum` pari a 20.

Lo stesso problema esistente in XAML. Impostare le proprietà in un ordine che assicura che `Maximum` è sempre maggiore `Minimum`:

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

È possibile impostare il `Minimum` e `Maximum` i valori per i numeri negativi, ma solo in un ordine in cui `Minimum` è sempre minore di `Maximum`:

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

Il `Value` proprietà è sempre maggiore di o uguale al `Minimum` valore e minore o uguale a `Maximum`. Se `Value` è impostata su un valore di fuori di tale intervallo, verrà assegnato forzatamente il valore per cui si trovano all'interno dell'intervallo, ma viene generata alcuna eccezione. Ad esempio, questo codice verrà *non* generano un'eccezione:

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

Al contrario, il `Value` proprietà viene assegnata al `Maximum` valore pari a 1.

Ecco un frammento di codice riportato sopra:

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

Quando `Minimum` è impostato su 10, quindi `Value` viene anche impostato su 10.

Se un `ValueChanged` gestore dell'evento è stato associato al momento che il `Value` proprietà viene assegnata a un elemento diverso dal valore predefinito pari a 0, quindi un `ValueChanged` evento. Ecco un frammento di XAML:

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

Quando `Minimum` è impostato su 10, `Value` è impostata su 10 e il `ValueChanged` evento. Questa situazione può verificarsi prima che il resto della pagina è stato costruito, e il gestore potrebbe tentare di fare riferimento agli altri elementi nella pagina che non sono ancora state create. È possibile aggiungere codice per il `ValueChanged` gestore che verifica la presenza di `null` i valori di altri elementi nella pagina. In alternativa, è possibile impostare il `ValueChanged` gestore dell'evento dopo la `Slider` valori sono stati inizializzati.

<a name="implementations" />

## <a name="platform-implementation-differences"></a>Differenze di implementazione della piattaforma

Gli screenshot illustrati in precedenza Visualizza il valore della `Slider` con un diverso numero di cifre decimali. Questo è correlato al modo in cui il `Slider` implementato per le piattaforme Android e UWP.

### <a name="the-android-implementation"></a>L'implementazione di Android

L'implementazione di Android `Slider` si basa su Android [ `SeekBar` ](xref:Android.Widget.SeekBar) e imposta sempre la [ `Max` ](xref:Android.Widget.ProgressBar.Max) proprietà a 1000. Ciò significa che il `Slider` in Android ha solo 1.001 valori discreti. Se si imposta il `Slider` per avere una `Minimum` pari a 0 e un `Maximum` pari a 5000, quindi come le `Slider` manipolato, il `Value` proprietà presenta valori pari a 0, 5, 10, 15 e così via.

### <a name="the-uwp-implementation"></a>L'implementazione di UWP

L'implementazione di UWP del `Slider` si basa su UWP [ `Slider` ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.slider) controllo. Il `StepFrequency` proprietà dell'App UWP `Slider` è impostato per la differenza tra le `Maximum` e `Minimum` proprietà diviso per 10, ma non maggiore di 1.

Ad esempio, per l'intervallo predefinito di 0 e 1, il `StepFrequency` è impostata su 0,1. Come le `Slider` manipolato, il `Value` proprietà è limitata a 0, 0.1, 0.2, 0.3, 0,4, 0,5, 0.6, 0,7, 0.8, 0.9 e 1.0. (Questo è evidente nell'ultima pagina dell'esempio [**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) ). Quando la differenza tra le proprietà `Maximum` e `Minimum` è maggiore o uguale a 10, `StepFrequency` è impostato su 1 e la proprietà `Value` ha valori integrali.

### <a name="the-stepslider-solution"></a>La soluzione StepSlider

Un `StepSlider` più versatile viene illustrato nel [capitolo 27. Renderer personalizzati](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf) del libro creazione di *app per dispositivi mobili con Xamarin.Forms*. Il `StepSlider` è simile a `Slider` ma si aggiungerà un `Steps` proprietà per specificare il numero di valori tra `Minimum` e `Maximum`.

## <a name="sliders-for-color-selection"></a>Dispositivi di scorrimento per la selezione dei colori

Finale due pagine nel [ **SliderDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) esempio entrambi usano tre `Slider` istanze per la selezione del colore. La prima pagina gestisce tutte le interazioni nel file code-behind, mentre la seconda pagina illustra come usare il data binding con un elemento ViewModel.

### <a name="handling-sliders-in-the-code-behind-file"></a>La gestione di dispositivi di scorrimento nel file code-behind

Il **cursori di colore RGB** crea un'istanza di pagina un `BoxView` per visualizzare un colore, tre `Slider` istanze per selezionare i componenti rossi, verdi e blu del colore e tre `Label` elementi per la visualizzazione di tali colore valori:

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

Oggetto `Style` offre tutte e tre `Slider` elementi di un intervallo da 0 a 255. Il `Slider` elementi condividono lo stesso `ValueChanged` gestore, implementata nel file code-behind:

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

I primi set di sezione la `Text` proprietà di uno del `Label` istanze per una breve stringa di testo che indica il valore della `Slider` in formato esadecimale. Quindi, tutte e tre `Slider` istanze sono accessibili per creare un `Color` valore dai componenti RGB:

[![Dispositivi di scorrimento colori RGB](slider-images/RgbColorSliders.png "Dispositivi di scorrimento colori RGB")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>Il dispositivo di scorrimento di associazione a un elemento ViewModel

Il **dispositivi di scorrimento colore HSL** pagina viene illustrato come utilizzare un ViewModel per eseguire i calcoli usati per creare un `Color` valore da valori di tonalità, saturazione e luminosità. Come tutti i ViewModel, la `HSLColorViewModel` classe implementa il `INotifyPropertyChanged` interfaccia e viene attivato un `PropertyChanged` evento ogni volta che una proprietà cambia:

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

ViewModel e la `INotifyPropertyChanged` interfaccia vengono descritti nell'articolo [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Il **HslColorSlidersPage.xaml** file creare un'istanza di `HslColorViewModel` e lo imposta su della pagina `BindingContext` proprietà. In questo modo tutti gli elementi nel file XAML per associare le proprietà nel ViewModel:

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

Come le `Slider` vengono modificati gli elementi di `BoxView` e `Label` gli elementi vengono aggiornati da ViewModel:

[![Dispositivi di scorrimento colore HSL](slider-images/HslColorSliders.png "Dispositivi di scorrimento colore HSL")](slider-images/HslColorSliders-Large.png#lightbox)

Il `StringFormat` del componente di `Binding` estensione di markup viene impostata per un formato di "F2" per visualizzare due posizioni decimali. (La formattazione delle stringhe nei data binding è illustrata nell'articolo [formattazione delle stringhe](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md)). Tuttavia, la versione UWP del programma è limitata ai valori 0, 0,1, 0,2,... 0,9 e 1,0. Si tratta di un risultato diretto dell'implementazione di App UWP `Slider` come descritto in precedenza nella sezione [differenze di implementazione della piattaforma](#implementations).

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di demo di Slider](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)
- [Dispositivo di scorrimento API](xref:Xamarin.Forms.Slider)
