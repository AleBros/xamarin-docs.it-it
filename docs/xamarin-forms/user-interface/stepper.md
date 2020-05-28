---
title: Xamarin.FormsStepper
description: Il Xamarin.Forms stepper consente a un utente di selezionare un valore numerico da un intervallo di valori. È costituito da due pulsanti contrassegnati con segno meno e segno più. Se si modificano i due pulsanti, il valore selezionato viene modificato in modo incrementale.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4f071530fb17de44d8ede786ca1b42f5e11f4f7c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84130546"
---
# <a name="xamarinforms-stepper"></a>Xamarin.FormsStepper

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)

_Utilizzare un stepper per la selezione di un valore numerico da un intervallo di valori._

Xamarin.Forms [`Stepper`](xref:Xamarin.Forms.Stepper) È costituito da due pulsanti etichettati con segno meno e segno più. Questi pulsanti possono essere modificati dall'utente per selezionare in modo incrementale un `double` valore da un intervallo di valori.

[`Stepper`](xref:Xamarin.Forms.Stepper)Definisce quattro proprietà di tipo `double` :

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment)valore di cui si desidera modificare il valore selezionato e il valore predefinito è 1.
- [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum)è il valore minimo dell'intervallo e il valore predefinito è 0.
- [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum)è il valore massimo dell'intervallo e il valore predefinito è 100.
- [`Value`](xref:Xamarin.Forms.Stepper.Value)è il valore del stepper, che può variare tra `Minimum` e `Maximum` e il valore predefinito è 0.

Tutte queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti. La [`Value`](xref:Xamarin.Forms.Stepper.Value) proprietà dispone di una modalità di associazione predefinita [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , che significa che è adatta come origine di associazione in un'applicazione che utilizza l'architettura [MVC (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

> [!WARNING]
> Internamente, [`Stepper`](xref:Xamarin.Forms.Stepper) assicura che [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) è minore di [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) . Se `Minimum` o `Maximum` sono mai impostati in modo che `Minimum` non sia minore di `Maximum` , viene generata un'eccezione. Per ulteriori informazioni sull'impostazione delle `Minimum` `Maximum` proprietà e, vedere la sezione [precauzioni](#precautions) .

Tramite [`Stepper`](xref:Xamarin.Forms.Stepper) viene forzata la [`Value`](xref:Xamarin.Forms.Stepper.Value) Proprietà in modo che sia compresa tra [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) e [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) , inclusi. Se la `Minimum` proprietà è impostata su un valore maggiore della `Value` proprietà, `Stepper` imposta la `Value` proprietà su `Minimum` . Analogamente, se `Maximum` è impostato su un valore minore di `Value` , `Stepper` imposta la `Value` proprietà su `Maximum` .

[`Stepper`](xref:Xamarin.Forms.Stepper)definisce un [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) evento che viene generato quando [`Value`](xref:Xamarin.Forms.Stepper.Value) cambia, tramite la manipolazione dell'utente di `Stepper` o quando l'applicazione imposta direttamente la `Value` Proprietà. `ValueChanged`Viene inoltre generato un evento quando la `Value` proprietà viene assegnata come descritto nel paragrafo precedente.

L' [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) oggetto che accompagna l' [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) evento è costituito da due proprietà, entrambe di tipo `double` : [`OldValue`](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) e [`NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue) . Nel momento in cui viene generato l'evento, il valore di `NewValue` è uguale a quello della [`Value`](xref:Xamarin.Forms.Stepper.Value) proprietà dell' [`Stepper`](xref:Xamarin.Forms.Stepper) oggetto.

## <a name="basic-stepper-code-and-markup"></a>Codice stepper di base e markup

L'esempio [**StepperDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) contiene tre pagine che sono funzionalmente identiche, ma sono implementate in modi diversi. La prima pagina usa solo codice C#, la seconda usa XAML con un gestore eventi nel codice e la terza è in grado di evitare il gestore eventi usando data binding nel file XAML.

### <a name="creating-a-stepper-in-code"></a>Creazione di un Stepper nel codice

La **tabella codici di base di stepper** nell'esempio [**StepperDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) illustra come creare un oggetto [`Stepper`](xref:Xamarin.Forms.Stepper) e due [`Label`](xref:Xamarin.Forms.Label) oggetti nel codice:

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

L'oggetto [`Stepper`](xref:Xamarin.Forms.Stepper) viene inizializzato in modo da avere una [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) proprietà di 360 e una [`Increment`](xref:Xamarin.Forms.Stepper.Increment) proprietà di 30. Se si modifica l'oggetto `Stepper` , il valore selezionato viene modificato [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) in modo incrementale in `Maximum` base al valore della `Increment` Proprietà. Il [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) gestore di `Stepper` Usa la [`Value`](xref:Xamarin.Forms.Stepper.Value) proprietà dell' `stepper` oggetto per impostare la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) proprietà del primo [`Label`](xref:Xamarin.Forms.Label) e usa il `string.Format` metodo con la `NewValue` proprietà degli argomenti dell'evento per impostare la [`Text`](xref:Xamarin.Forms.Label.Text) proprietà del secondo `Label` . Questi due approcci per ottenere il valore corrente di `Stepper` sono intercambiabili.

Gli screenshot seguenti mostrano la **tabella codici di base di stepper** :

[![Codice stepper di base](stepper-images/basic-stepper-code.png "Codice stepper di base")](stepper-images/basic-stepper-code-large.png#lightbox)

Il secondo [`Label`](xref:Xamarin.Forms.Label) Visualizza il testo "(non inizializzato)" fino a quando l'oggetto non [`Stepper`](xref:Xamarin.Forms.Stepper) viene modificato, causando la generazione del primo [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) evento.

### <a name="creating-a-stepper-in-xaml"></a>Creazione di uno stepper in XAML

La pagina **XAML di base di stepper** è funzionalmente identica al **codice stepper di base** , ma implementata principalmente in XAML:

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

Il file code-behind contiene il gestore per l' [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) evento:

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

È anche possibile che il gestore eventi ottenga l'oggetto [`Stepper`](xref:Xamarin.Forms.Stepper) che genera l'evento tramite l' `sender` argomento. La [`Value`](xref:Xamarin.Forms.Stepper.Value) proprietà contiene il valore corrente:

```csharp
double value = ((Stepper)sender).Value;
```

Se all' [`Stepper`](xref:Xamarin.Forms.Stepper) oggetto è stato assegnato un nome nel file XAML con un `x:Name` attributo (ad esempio, "stepper"), il gestore eventi può fare riferimento direttamente all'oggetto:

```csharp
double value = stepper.Value;
```

### <a name="data-binding-the-stepper"></a>Associazione dati del gestore di stato

Nella pagina **Binding stepper di base** viene illustrato come scrivere un'applicazione quasi equivalente che elimini il [`Value`](xref:Xamarin.Forms.Stepper.Value) gestore eventi utilizzando il [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md):

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

La [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) proprietà del primo oggetto [`Label`](xref:Xamarin.Forms.Label) è associata alla [`Value`](xref:Xamarin.Forms.Stepper.Value) proprietà dell'oggetto [`Stepper`](xref:Xamarin.Forms.Stepper) , così come la [`Text`](xref:Xamarin.Forms.Label.Text) proprietà del secondo oggetto `Label` con una `StringFormat` specifica. La pagina **associazioni stepper di base** funziona in modo leggermente diverso dalle due pagine precedenti: quando viene visualizzata la pagina per la prima volta, il secondo `Label` Visualizza la stringa di testo con il valore. Si tratta di un vantaggio dell'utilizzo di data binding. Per visualizzare il testo senza data binding, è necessario inizializzare in modo specifico la `Text` proprietà di `Label` o simulare un'attivazione dell' [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) evento chiamando il gestore eventi dal costruttore della classe.

## <a name="precautions"></a>Precauzioni

Il valore della [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) proprietà deve essere sempre minore del valore della [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) Proprietà. Il frammento di codice seguente causa la [`Stepper`](xref:Xamarin.Forms.Stepper) generazione di un'eccezione da parte di.

```csharp
// Throws an exception!
Stepper stepper = new Stepper
{
    Minimum = 180,
    Maximum = 360
};
```

Il compilatore C# genera codice che imposta queste due proprietà in sequenza e quando la [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) proprietà è impostata su 180, è maggiore del [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) valore predefinito 100. È possibile evitare l'eccezione in questo caso impostando `Maximum` prima la proprietà:

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

[`Maximum`](xref:Xamarin.Forms.Stepper.Maximum)L'impostazione di su 360 non costituisce un problema perché è maggiore del [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) valore predefinito 0. Quando `Minimum` è impostato, il valore è minore del `Maximum` valore di 360.

Lo stesso problema esiste in XAML. Impostare le proprietà in un ordine che garantisce che [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) sia sempre maggiore di `Minimum` :

```xaml
<Stepper Maximum="360"
         Minimum="180" ... />
```

È possibile impostare i [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) valori e su numeri negativi, ma solo in un ordine in cui `Minimum` è sempre minore di `Maximum` :

```xaml
<Stepper Minimum="-360"
         Maximum="-180" ... />
```

La [`Value`](xref:Xamarin.Forms.Stepper.Value) proprietà è sempre maggiore o uguale al [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) valore e minore o uguale a [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) . Se `Value` è impostato su un valore non compreso in tale intervallo, il valore verrà forzato a trovarsi all'interno dell'intervallo, ma non viene generata alcuna eccezione. Questo codice, ad esempio, *non* genererà un'eccezione:

```csharp
Stepper stepper = new Stepper
{
    Value = 180
};
```

Al contrario, la [`Value`](xref:Xamarin.Forms.Stepper.Value) proprietà viene assegnata al [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) valore di 100.

Ecco un frammento di codice illustrato sopra:

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

Quando [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) è impostato su 180, [`Value`](xref:Xamarin.Forms.Stepper.Value) viene anche impostato su 180.

Se un [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) gestore eventi è stato associato nel momento in cui la [`Value`](xref:Xamarin.Forms.Stepper.Value) proprietà è stata assegnata a un valore diverso da quello predefinito 0, `ValueChanged` viene generato un evento. Ecco un frammento di codice XAML:

```xaml
<Stepper ValueChanged="OnStepperValueChanged"
         Maximum="360"
         Minimum="180" />
```

Quando [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) è impostato su 180, [`Value`](xref:Xamarin.Forms.Stepper.Value) viene impostato anche su 180 e [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) viene generato l'evento. Questo problema può verificarsi prima che il resto della pagina sia stato costruito e il gestore potrebbe tentare di fare riferimento ad altri elementi della pagina che non sono stati ancora creati. È possibile aggiungere codice al gestore per verificare la presenza di `ValueChanged` `null` valori di altri elementi nella pagina. In alternativa, è possibile impostare il `ValueChanged` gestore eventi dopo che i [`Stepper`](xref:Xamarin.Forms.Stepper) valori sono stati inizializzati.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di demo stepper](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)
- [API stepper](xref:Xamarin.Forms.Stepper)
