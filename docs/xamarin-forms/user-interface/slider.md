---
title: Xamarin.FormsCursore
description: Il Xamarin.Forms dispositivo di scorrimento è una barra orizzontale che può essere modificata dall'utente per selezionare un valore Double da un intervallo continuo. Questo articolo illustra come usare la classe Slider per selezionare un valore da un intervallo di valori continui.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1cde999e6781f019b6abceee82caf259e1e5a710
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140153"
---
# <a name="xamarinforms-slider"></a>Xamarin.FormsCursore

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)

_Utilizzare un dispositivo di scorrimento per la selezione da un intervallo di valori continui._

Xamarin.Forms [`Slider`](xref:Xamarin.Forms.Slider) È una barra orizzontale che può essere modificata dall'utente per selezionare un `double` valore da un intervallo continuo.

`Slider`Definisce tre proprietà di tipo `double` :

- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum)è il valore minimo dell'intervallo e il valore predefinito è 0.
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum)è il valore massimo dell'intervallo e il valore predefinito è 1.
- [`Value`](xref:Xamarin.Forms.Slider.Value)valore del dispositivo di scorrimento, che può variare tra `Minimum` e `Maximum` e il cui valore predefinito è 0.

Tutte e tre le proprietà sono supportate da `BindableProperty` oggetti. La `Value` proprietà dispone di una modalità di associazione predefinita `BindingMode.TwoWay` , che significa che è adatta come origine di associazione in un'applicazione che utilizza l'architettura [MVC (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

> [!WARNING]
> Internamente, `Slider` assicura che `Minimum` è minore di `Maximum` . Se `Minimum` o `Maximum` sono mai impostati in modo che `Minimum` non sia minore di `Maximum` , viene generata un'eccezione. Per ulteriori informazioni sull'impostazione delle proprietà e, vedere la sezione [**precauzioni**](#precautions) riportata di seguito `Minimum` `Maximum` .

Tramite `Slider` viene forzata la `Value` Proprietà in modo che sia compresa tra `Minimum` e `Maximum` , inclusi. Se la `Minimum` proprietà è impostata su un valore maggiore della `Value` proprietà, `Slider` imposta la `Value` proprietà su `Minimum` . Analogamente, se `Maximum` è impostato su un valore minore di `Value` , `Slider` imposta la `Value` proprietà su `Maximum` .

`Slider`definisce un [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) evento che viene generato quando `Value` cambia, tramite la manipolazione dell'utente di `Slider` o quando il programma imposta direttamente la `Value` Proprietà. `ValueChanged`Viene inoltre generato un evento quando la `Value` proprietà viene assegnata come descritto nel paragrafo precedente.

L' [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) oggetto che accompagna l' `ValueChanged` evento è costituito da due proprietà, entrambe di tipo `double` : [`OldValue`](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) e [`NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue) . Nel momento in cui viene generato l'evento, il valore di `NewValue` è uguale a quello della `Value` proprietà dell' `Slider` oggetto.

`Slider`definisce anche `DragStarted` `DragCompleted` gli eventi e, che vengono generati all'inizio e alla fine dell'azione di trascinamento. A differenza dell' [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) evento, gli `DragStarted` `DragCompleted` eventi e vengono generati solo tramite la manipolazione dell'utente `Slider` . Quando `DragStarted` viene generato l'evento, `DragStartedCommand` viene eseguito l'oggetto di tipo `ICommand` . Analogamente, quando `DragCompleted` viene generato l'evento, `DragCompletedCommand` viene eseguito l'oggetto di tipo `ICommand` .

> [!WARNING]
> Non usare opzioni di layout orizzontale non vincolate di `Center` , `Start` o `End` con `Slider` . Sia per Android che per UWP, il `Slider` comprimere su una barra di lunghezza zero e su iOS, la barra è molto breve. Mantiene l' `HorizontalOptions` impostazione predefinita di `Fill` e non usare una larghezza di `Auto` quando si inserisce `Slider` un `Grid` layout.

`Slider`Definisce inoltre diverse proprietà che influiscono sull'aspetto:

- [`MinimumTrackColor`](xref:Xamarin.Forms.Slider.MinimumTrackColorProperty)colore della barra sul lato sinistro del cursore.
- [`MaximumTrackColor`](xref:Xamarin.Forms.Slider.MaximumTrackColorProperty)colore della barra sul lato destro del cursore.
- [`ThumbColor`](xref:Xamarin.Forms.Slider.ThumbColorProperty)colore del cursore.
- [`ThumbImageSource`](xref:Xamarin.Forms.Slider.ThumbImageSourceProperty)immagine da utilizzare per il controllo Thumb, di tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) .

> [!NOTE]
> Le `ThumbColor` proprietà e si escludono a `ThumbImageSource` vicenda. Se entrambe le proprietà sono impostate, la `ThumbImageSource` proprietà avrà la precedenza.

## <a name="basic-slider-code-and-markup"></a>Codice Slider di base e markup

L'esempio [**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) inizia con tre pagine che sono funzionalmente identiche, ma sono implementate in modi diversi. La prima pagina usa solo codice C#, la seconda usa XAML con un gestore eventi nel codice e la terza è in grado di evitare il gestore eventi usando data binding nel file XAML.

### <a name="creating-a-slider-in-code"></a>Creazione di un dispositivo di scorrimento nel codice

La **tabella codici del dispositivo di scorrimento di base** nell'esempio [**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) Mostra per creare una `Slider` e due `Label` oggetti nel codice:

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

L'oggetto `Slider` viene inizializzato per avere una `Maximum` proprietà di 360. Il `ValueChanged` gestore di `Slider` Usa la `Value` proprietà dell' `slider` oggetto per impostare la `Rotation` proprietà del primo `Label` e usa il `String.Format` metodo con la `NewValue` proprietà degli argomenti dell'evento per impostare la `Text` proprietà del secondo `Label` . Questi due approcci per ottenere il valore corrente di `Slider` sono intercambiabili.

Ecco il programma in esecuzione su dispositivi iOS e Android:

[![Codice Slider di base](slider-images/BasicSliderCode.png "Codice Slider di base")](slider-images/BasicSliderCode-Large.png#lightbox)

Il secondo `Label` Visualizza il testo "(non inizializzato)" fino a quando l'oggetto non `Slider` viene modificato, causando la generazione del primo `ValueChanged` evento. Si noti che il numero di posizioni decimali visualizzate è diverso per ogni piattaforma. Queste differenze sono correlate alle implementazioni della piattaforma di `Slider` e vengono illustrate più avanti in questo articolo nella sezione [differenze di implementazione della piattaforma](#implementations).

### <a name="creating-a-slider-in-xaml"></a>Creazione di un dispositivo di scorrimento in XAML

La pagina **XAML di base del dispositivo di scorrimento** è funzionalmente identica al codice del dispositivo di scorrimento di **base** , ma implementata principalmente in XAML:

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

Il file code-behind contiene il gestore per l' `ValueChanged` evento:

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

È anche possibile che il gestore eventi ottenga l'oggetto `Slider` che genera l'evento tramite l' `sender` argomento. La `Value` proprietà contiene il valore corrente:

```csharp
double value = ((Slider)sender).Value;
```

Se all' `Slider` oggetto è stato assegnato un nome nel file XAML con un `x:Name` attributo (ad esempio, "slider"), il gestore eventi può fare riferimento direttamente all'oggetto:

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>Data binding del dispositivo di scorrimento

La pagina delle **associazioni di base del dispositivo di scorrimento** Mostra come scrivere un programma quasi equivalente che elimina il `Value` gestore eventi usando il [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md):

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

La `Rotation` proprietà del primo oggetto `Label` è associata alla `Value` proprietà dell'oggetto `Slider` , così come la `Text` proprietà del secondo oggetto `Label` con una `StringFormat` specifica. La pagina delle **associazioni di scorrimento di base** funziona in modo leggermente diverso dalle due pagine precedenti: quando viene visualizzata la pagina per la prima volta, il secondo `Label` Visualizza la stringa di testo con il valore. Si tratta di un vantaggio dell'utilizzo di data binding. Per visualizzare il testo senza data binding, è necessario inizializzare in modo specifico la `Text` proprietà di `Label` o simulare un'attivazione dell' `ValueChanged` evento chiamando il gestore eventi dal costruttore della classe.

<a name="precautions" />

## <a name="precautions"></a>Precauzioni

Il valore della `Minimum` proprietà deve essere sempre minore del valore della `Maximum` Proprietà. Il frammento di codice seguente causa la `Slider` generazione di un'eccezione da parte di.

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

Il compilatore C# genera codice che imposta queste due proprietà in sequenza e quando la `Minimum` proprietà è impostata su 10, è maggiore del `Maximum` valore predefinito 1. È possibile evitare l'eccezione in questo caso impostando `Maximum` prima la proprietà:

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

`Maximum`L'impostazione di su 20 non costituisce un problema perché è maggiore del `Minimum` valore predefinito 0. Quando `Minimum` è impostato, il valore è minore del `Maximum` valore di 20.

Lo stesso problema esiste in XAML. Impostare le proprietà in un ordine che garantisce che `Maximum` sia sempre maggiore di `Minimum` :

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

È possibile impostare i `Minimum` `Maximum` valori e su numeri negativi, ma solo in un ordine in cui `Minimum` è sempre minore di `Maximum` :

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

La `Value` proprietà è sempre maggiore o uguale al `Minimum` valore e minore o uguale a `Maximum` . Se `Value` è impostato su un valore non compreso in tale intervallo, il valore verrà forzato a trovarsi all'interno dell'intervallo, ma non viene generata alcuna eccezione. Questo codice, ad esempio, *non* genererà un'eccezione:

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

Al contrario, la `Value` proprietà viene assegnata al `Maximum` valore 1.

Ecco un frammento di codice illustrato sopra:

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

Quando `Minimum` è impostato su 10, `Value` viene impostato anche su 10.

Se un `ValueChanged` gestore eventi è stato associato nel momento in cui la `Value` proprietà è stata assegnata a un valore diverso da quello predefinito 0, `ValueChanged` viene generato un evento. Ecco un frammento di codice XAML:

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

Quando `Minimum` è impostato su 10, `Value` viene impostato anche su 10 e `ValueChanged` viene generato l'evento. Questo problema può verificarsi prima che il resto della pagina sia stato costruito e il gestore potrebbe tentare di fare riferimento ad altri elementi della pagina che non sono stati ancora creati. È possibile aggiungere codice al gestore per verificare la presenza di `ValueChanged` `null` valori di altri elementi nella pagina. In alternativa, è possibile impostare il `ValueChanged` gestore eventi dopo che i `Slider` valori sono stati inizializzati.

<a name="implementations" />

## <a name="platform-implementation-differences"></a>Differenze di implementazione della piattaforma

Gli screenshot mostrati in precedenza visualizzano il valore di `Slider` con un numero diverso di punti decimali. Questo si riferisce al modo in cui `Slider` viene implementato nelle piattaforme Android e UWP.

### <a name="the-android-implementation"></a>Implementazione di Android

L'implementazione di Android di `Slider` si basa su Android [`SeekBar`](xref:Android.Widget.SeekBar) e imposta sempre la [`Max`](xref:Android.Widget.ProgressBar.Max) proprietà su 1000. Ciò significa che `Slider` in Android sono presenti solo 1.001 valori discreti. Se si imposta `Slider` su un valore pari a `Minimum` 0 e a `Maximum` 5000, quando `Slider` viene modificato l'oggetto, la `Value` proprietà avrà valori 0, 5, 10, 15 e così via.

### <a name="the-uwp-implementation"></a>Implementazione di UWP

L'implementazione di UWP di `Slider` è basata sul [`Slider`](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.slider) controllo UWP. La `StepFrequency` proprietà di UWP `Slider` è impostata sulla differenza delle `Maximum` `Minimum` proprietà e divisa per 10, ma non maggiore di 1.

Per l'intervallo predefinito compreso tra 0 e 1, ad esempio, la `StepFrequency` proprietà è impostata su 0,1. Quando l'oggetto `Slider` viene modificato, la `Value` proprietà è limitata a 0, 0,1, 0,2, 0,3, 0,4, 0,5, 0,6, 0,7, 0,8, 0,9 e 1,0. (Questo è evidente nell'ultima pagina dell'esempio [**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) ). Quando la differenza tra le `Maximum` `Minimum` proprietà e è maggiore o uguale a 10, `StepFrequency` viene impostato su 1 e la `Value` proprietà ha valori integrali.

### <a name="the-stepslider-solution"></a>Soluzione StepSlider

Un più versatile `StepSlider` viene illustrato nel [capitolo 27. Renderer personalizzati](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf) del libro creazione di *app per dispositivi mobili Xamarin.Forms con *. `StepSlider`È simile a `Slider` ma aggiunge una `Steps` proprietà per specificare il numero di valori compresi tra `Minimum` e `Maximum` .

## <a name="sliders-for-color-selection"></a>Dispositivi di scorrimento per la selezione colori

Le ultime due pagine nell'esempio [**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) usano entrambe tre `Slider` istanze per la selezione dei colori. Nella prima pagina vengono gestite tutte le interazioni nel file code-behind, mentre nella seconda pagina viene illustrato come utilizzare data binding con un elemento ViewModel.

### <a name="handling-sliders-in-the-code-behind-file"></a>Gestione dei dispositivi di scorrimento nel file code-behind

La pagina dei dispositivi di **scorrimento colori RGB** crea un'istanza `BoxView` di per visualizzare un colore, tre `Slider` istanze per selezionare i componenti rosso, verde e blu del colore e tre `Label` elementi per la visualizzazione dei valori di colore:

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

Un oggetto `Style` assegna `Slider` a tutti e tre gli elementi un intervallo compreso tra 0 e 255. Gli `Slider` elementi condividono lo stesso `ValueChanged` gestore, che viene implementato nel file code-behind:

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

La prima sezione imposta la `Text` proprietà di una delle `Label` istanze su una stringa di testo breve che indica il valore dell'oggetto `Slider` in formato esadecimale. A questo punto, `Slider` è possibile accedere a tutte e tre le istanze per creare un `Color` valore dai componenti RGB:

[![Dispositivi di scorrimento colori RGB](slider-images/RgbColorSliders.png "Dispositivi di scorrimento colori RGB")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>Associazione del dispositivo di scorrimento a un ViewModel

La pagina del **cursore dei colori HSL** Mostra come usare un elemento ViewModel per eseguire i calcoli usati per creare un `Color` valore dai valori di tonalità, saturazione e luminosità. Come tutti i ViewModel, la `HSLColorViewModel` classe implementa l' `INotifyPropertyChanged` interfaccia e genera un `PropertyChanged` evento ogni volta che una delle proprietà cambia:

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

I ViewModel e l' `INotifyPropertyChanged` interfaccia vengono descritti nell'articolo [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Il file **HslColorSlidersPage. XAML** crea un'istanza `HslColorViewModel` di e lo imposta sulla proprietà della pagina `BindingContext` . In questo modo tutti gli elementi nel file XAML possono essere associati alle proprietà nel ViewModel:

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

Quando gli `Slider` elementi vengono modificati, gli `BoxView` elementi e `Label` vengono aggiornati da ViewModel:

[![Dispositivi di scorrimento colore HSL](slider-images/HslColorSliders.png "Dispositivi di scorrimento colore HSL")](slider-images/HslColorSliders-Large.png#lightbox)

Il `StringFormat` componente dell' `Binding` estensione di markup è impostato per un formato "F2" per visualizzare due posizioni decimali. (La formattazione delle stringhe nei data binding è illustrata nell'articolo [formattazione delle stringhe](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md)). Tuttavia, la versione UWP del programma è limitata ai valori 0, 0,1, 0,2,... 0,9 e 1,0. Si tratta di un risultato diretto dell'implementazione di UWP `Slider` , come descritto in precedenza nella sezione [differenze di implementazione della piattaforma](#implementations).

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di demo Slider](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)
- [API Slider](xref:Xamarin.Forms.Slider)
