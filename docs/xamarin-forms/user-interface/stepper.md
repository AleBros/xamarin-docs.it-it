---
title: Gestore di istruzioni di xamarin. Forms
description: Il gestore di istruzioni di xamarin. Forms consente a un utente di selezionare un valore numerico da un intervallo di valori. È costituito da due pulsanti contrassegnati con segni più e meno (-). Modifica i due pulsanti Modifica il valore selezionato in modo incrementale.
ms.prod: xamarin
ms.assetid: 62571B3E-D84B-4F52-9FC7-C105D6733B16
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/17/2018
ms.openlocfilehash: a224d82ed7bb993f51be6cca6ccf09b5331cfac0
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052206"
---
# <a name="xamarinforms-stepper"></a>Gestore di istruzioni di xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/StepperDemos)

_Usare un gestore di istruzioni per la selezione di un valore numerico da un intervallo di valori._

Xamarin. Forms [ `Stepper` ](xref:Xamarin.Forms.Stepper) è costituito da due pulsanti contrassegnati con segni più e meno (-). Questi pulsanti possono essere modificati dall'utente per selezionare in modo incrementale un `double` valore da un intervallo di valori.

Il [ `Stepper` ](xref:Xamarin.Forms.Stepper) definisce quattro proprietà di tipo `double`:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) è la quantità per modificare il valore selezionato, con un valore predefinito 1.
- [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) è il valore minimo dell'intervallo, con un valore predefinito pari a 0.
- [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) è il valore massimo dell'intervallo, con un valore predefinito pari a 100.
- [`Value`](xref:Xamarin.Forms.Stepper.Value) valore del gestore di istruzioni che può essere compreso tra `Minimum` e `Maximum` e ha un valore predefinito pari a 0.

Tutte queste proprietà sono supportate da [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetti. Il [ `Value` ](xref:Xamarin.Forms.Stepper.Value) proprietà ha una modalità di associazione predefinita [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), che significa che è adatto come origine del binding in un'applicazione che utilizza il [ Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) architettura.

> [!WARNING]
> Internamente, il [ `Stepper` ](xref:Xamarin.Forms.Stepper) assicura che [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) è inferiore a [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum). Se `Minimum` oppure `Maximum` vengono sempre impostati in modo che `Minimum` è non minore di `Maximum`, viene generata un'eccezione. Per altre informazioni sull'impostazione di `Minimum` e `Maximum` proprietà, vedere [precauzioni](#precautions) sezione.

Il [ `Stepper` ](xref:Xamarin.Forms.Stepper) forza la [ `Value` ](xref:Xamarin.Forms.Stepper.Value) proprietà in modo che sia tra [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) e [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum), inclusi. Se il `Minimum` proprietà è impostata su un valore maggiore del `Value` proprietà, il `Stepper` imposta la `Value` proprietà `Minimum`. Analogamente, se `Maximum` è impostata su un valore minore di `Value`, quindi `Stepper` imposta la `Value` proprietà `Maximum`.

[`Stepper`](xref:Xamarin.Forms.Stepper) definisce un [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) evento generato quando il [ `Value` ](xref:Xamarin.Forms.Stepper.Value) modifiche, tramite la modifica dell'utente del `Stepper` o quando l'applicazione imposta il `Value` proprietà direttamente. Oggetto `ValueChanged` evento viene generato quando il `Value` proprietà viene convertita come descritto nel paragrafo precedente.

Il [ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs) oggetti che accompagna il [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) evento ha due proprietà, entrambi di tipo `double`: [ `OldValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) e[ `NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue). Al momento viene generato l'evento, il valore di `NewValue` equivale al [ `Value` ](xref:Xamarin.Forms.Stepper.Value) proprietà del [ `Stepper` ](xref:Xamarin.Forms.Stepper) oggetto.

## <a name="basic-stepper-code-and-markup"></a>Markup e codice di gestore di istruzioni di base

Il [ **StepperDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/StepperDemos) esempio contiene tre pagine che sono funzionalmente identici, ma vengono implementate in modi diversi. Usa solo la prima pagina C# code, il secondo Usa XAML con un gestore eventi nel codice e della terza è in grado di evitare il gestore eventi usando un'associazione dati nel file XAML.

### <a name="creating-a-stepper-in-code"></a>Creazione di un gestore di istruzioni nel codice

Il **gestore di istruzioni di base del codice** pagina il [ **StepperDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/StepperDemos) esempio viene illustrato come creare un [ `Stepper` ](xref:Xamarin.Forms.Stepper) e due [ `Label` ](xref:Xamarin.Forms.Label) gli oggetti nel codice:

```csharp
public class BasicStepperCodePage : ContentPage
{
    public BasicStepperCodePage()
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

        Stepper stepper = new Stepper
        {
            Maximum = 360,
            Increment = 30,
            HorizontalOptions = LayoutOptions.Center
        };
        stepper.ValueChanged += (sender, e) =>
        {
            rotationLabel.Rotation = stepper.Value;
            displayLabel.Text = string.Format("The Stepper value is {0}", e.NewValue);
        };

        Title = "Basic Stepper Code";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = { rotationLabel, stepper, displayLabel }
        };
    }
}
```

Il [ `Stepper` ](xref:Xamarin.Forms.Stepper) viene inizializzata per avere una [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) proprietà 360 e un [ `Increment` ](xref:Xamarin.Forms.Stepper.Increment) proprietà pari a 30. La modifica di `Stepper` cambia il valore selezionato in modo incrementale tra [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) a `Maximum` in base al valore del `Increment` proprietà. Il [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) gestore del `Stepper` Usa i [ `Value` ](xref:Xamarin.Forms.Stepper.Value) proprietà del `stepper` oggetto per impostare il [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) proprietà del primo [ `Label` ](xref:Xamarin.Forms.Label) e viene utilizzato il `string.Format` metodo con il `NewValue` proprietà degli argomenti dell'evento per impostare il [ `Text` ](xref:Xamarin.Forms.Label.Text) proprietà del secondo `Label`. Questi due approcci per ottenere il valore corrente del `Stepper` sono intercambiabili.

L'esempio di screenshot seguente mostra le **gestore di istruzioni di base del codice** pagina:

[![Gestore di istruzioni base codice](stepper-images/basic-stepper-code.png "gestore di istruzioni base codice")](stepper-images/basic-stepper-code-large.png#lightbox)

Il secondo [ `Label` ](xref:Xamarin.Forms.Label) viene visualizzato il testo "(non inizializzato)" finché il [ `Stepper` ](xref:Xamarin.Forms.Stepper) manipolato, in modo che il primo [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) evento per essere generato.

### <a name="creating-a-stepper-in-xaml"></a>Creazione di un gestore di istruzioni in XAML

Il **base XAML di gestore di istruzioni** pagina è funzionalmente identico **gestore di istruzioni di base del codice** ma implementato principalmente in XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StepperDemo.BasicStepperXAMLPage"
             Title="Basic Stepper XAML">
    <StackLayout Margin="20">
        <Label x:Name="_rotatingLabel"
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
        <Stepper Maximum="360"
                 Increment="30"
                 HorizontalOptions="Center"
                 ValueChanged="OnStepperValueChanged" />
        <Label x:Name="_displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />        
    </StackLayout>
</ContentPage>
```

Il file code-behind contiene il gestore per il [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) evento:

```csharp
public partial class BasicStepperXAMLPage : ContentPage
{
    public BasicStepperXAMLPage()
    {
        InitializeComponent();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs e)
    {
        double value = e.NewValue;
        _rotatingLabel.Rotation = value;
        _displayLabel.Text = string.Format("The Stepper value is {0}", value);
    }
}
```

È anche possibile che il gestore eventi ottenere il [ `Stepper` ](xref:Xamarin.Forms.Stepper) che sta generando l'evento attraverso il `sender` argomento. Il [ `Value` ](xref:Xamarin.Forms.Stepper.Value) proprietà contiene il valore corrente:

```csharp
double value = ((Stepper)sender).Value;
```

Se il [ `Stepper` ](xref:Xamarin.Forms.Stepper) oggetto assegnato un nome nel file XAML con un `x:Name` attributo (ad esempio, "gestore di istruzioni"), quindi il gestore dell'evento è stato possibile fare riferimento a quell'oggetto direttamente:

```csharp
double value = stepper.Value;
```

### <a name="data-binding-the-stepper"></a>Il gestore di istruzioni di associazione dati

Il **associazioni di gestore di istruzioni di base** pagina viene illustrato come scrivere un'applicazione quasi equivalente che consente di eliminare le [ `Value` ](xref:Xamarin.Forms.Stepper.Value) gestore dell'evento tramite [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StepperDemo.BasicStepperBindingsPage"
             Title="Basic Stepper Bindings">
    <StackLayout Margin="20">
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference _stepper}, Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
        <Stepper x:Name="_stepper"
                 Maximum="360"
                 Increment="30"
                 HorizontalOptions="Center" />
        <Label Text="{Binding Source={x:Reference _stepper}, Path=Value, StringFormat='The Stepper value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Il [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) proprietà del primo [ `Label` ](xref:Xamarin.Forms.Label) è associato ai [ `Value` ](xref:Xamarin.Forms.Stepper.Value) proprietà del [ `Stepper` ](xref:Xamarin.Forms.Stepper), poiché è il [ `Text` ](xref:Xamarin.Forms.Label.Text) proprietà del secondo `Label` con un `StringFormat` specifica. Il **associazioni di gestore di istruzioni di base** pagina funzioni un po' diverso da due pagine precedenti: quando la pagina viene visualizzata prima di tutto, il secondo `Label` consente di visualizzare la stringa di testo con il valore. Si tratta di un vantaggio dell'uso del data binding. Per visualizzare il testo senza l'associazione di dati, è necessario inizializzare in modo specifico la `Text` proprietà del `Label` o si simula un'attivazione del [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) evento chiamando il gestore dell'evento dal costruttore della classe .

## <a name="precautions"></a>Precauzioni relative alla

Il valore della [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) proprietà deve essere sempre inferiore al valore della [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) proprietà. Nell'esempio di codice le cause di frammento di codice le [ `Stepper` ](xref:Xamarin.Forms.Stepper) per generare un'eccezione:

```csharp
// Throws an exception!
Stepper stepper = new Stepper
{
    Minimum = 180,
    Maximum = 360
};
```

Il C# il compilatore genera codice che imposta le due proprietà seguenti nella sequenza, e quando il [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) è impostata su 180, è superiore all'impostazione predefinita [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) pari a 100. È possibile evitare l'eccezione in questo caso, impostando il `Maximum` proprietà prima:

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

L'impostazione [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) a 360 non costituisce un problema perché è superiore all'impostazione predefinita [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) valore pari a 0. Quando `Minimum` è impostato, il valore è minore di `Maximum` valore 360.

Lo stesso problema esistente in XAML. Impostare le proprietà in un ordine che assicura che [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) è sempre maggiore `Minimum`:

```xaml
<Stepper Maximum="360"
         Minimum="180" ... />
```

È possibile impostare il [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) e [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) valori per i numeri negativi, ma solo in un ordine in cui `Minimum` è sempre minore di `Maximum`:

```xaml
<Stepper Minimum="-360"
         Maximum="-180" ... />
```

Il [ `Value` ](xref:Xamarin.Forms.Stepper.Value) proprietà è sempre maggiore di o uguale al [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) valore e minore o uguale a [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum). Se `Value` è impostata su un valore di fuori di tale intervallo, verrà assegnato forzatamente il valore per cui si trovano all'interno dell'intervallo, ma viene generata alcuna eccezione. Ad esempio, questo codice verrà *non* generano un'eccezione:

```csharp
Stepper stepper = new Stepper
{
    Value = 180
};
```

Al contrario, il [ `Value` ](xref:Xamarin.Forms.Stepper.Value) proprietà viene assegnata al [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) pari a 100.

Ecco un frammento di codice riportato sopra:

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

Quando [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) è impostato su 180, quindi [ `Value` ](xref:Xamarin.Forms.Stepper.Value) anche è impostato su 180.

Se un [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) gestore dell'evento è stato associato al momento che il [ `Value` ](xref:Xamarin.Forms.Stepper.Value) proprietà viene assegnata a un elemento diverso dal valore predefinito pari a 0, quindi un `ValueChanged` evento. Ecco un frammento di XAML:

```xaml
<Stepper ValueChanged="OnStepperValueChanged"
         Maximum="360"
         Minimum="180" />
```

Quando [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) è impostato su 180, [ `Value` ](xref:Xamarin.Forms.Stepper.Value) anche è impostato su 180 e il [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) evento. Questa situazione può verificarsi prima che il resto della pagina è stato costruito, e il gestore potrebbe tentare di fare riferimento agli altri elementi nella pagina che non sono ancora state create. È possibile aggiungere codice per il `ValueChanged` gestore che verifica la presenza di `null` i valori di altri elementi nella pagina. In alternativa, è possibile impostare il `ValueChanged` gestore dell'evento dopo il [ `Stepper` ](xref:Xamarin.Forms.Stepper) valori sono stati inizializzati.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di demo di gestore di istruzioni](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/StepperDemos)
- [Gestore di istruzioni API](xref:Xamarin.Forms.Stepper)
