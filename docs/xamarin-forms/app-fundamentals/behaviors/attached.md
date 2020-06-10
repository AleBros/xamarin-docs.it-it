---
title: "comportamenti collegati" Descrizione: "i comportamenti collegati sono classi statiche con una o più proprietà associate. In questo articolo viene illustrato come creare e utilizzare comportamenti collegati ".
ms. prod: Novell MS. AssetID: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 04/06/2016 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="attached-behaviors"></a>Comportamenti associati

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-attachednumericvalidationbehavior)

_I comportamenti collegati sono classi statiche con una o più proprietà associate. Questo articolo illustra come creare e utilizzare comportamenti collegati._

## <a name="overview"></a>Panoramica

Le proprietà associate sono un tipo speciale di proprietà associabili. Sono definite in una classe ma associate ad altri oggetti. Sono riconoscibili in XAML perché si presentano come attributi che contengono una classe e un nome di proprietà separati da un punto.

Una proprietà associata può definire un delegato `propertyChanged` che verrà eseguito quando cambia il valore della proprietà, ad esempio quando la proprietà è impostata su un controllo. Quando viene eseguito, il delegato `propertyChanged` passa un riferimento al controllo a cui è associato e ai parametri che contengono i valori vecchi e nuovi della proprietà. Questo delegato può essere utilizzato per aggiungere nuove funzionalità al controllo a cui la proprietà è associata modificando il riferimento con cui viene passato, come indicato di seguito:

1. Il `propertyChanged` delegato esegue il cast del riferimento di controllo, che viene ricevuto come [`BindableObject`](xref:Xamarin.Forms.BindableObject) , al tipo di controllo che il comportamento è progettato per migliorare.
1. Il delegato `propertyChanged` modifica le proprietà del controllo, chiama i metodi del controllo o registra i gestori di eventi per gli eventi esposti dal controllo allo scopo di implementare le funzionalità del comportamento di base.

Un problema che può sorgere con comportamenti associati è che sono definiti in una classe `static` con proprietà e metodi di tipo `static`. Ciò rende difficile creare comportamenti associati che presentano uno stato. Inoltre, Xamarin.Forms i comportamenti hanno sostituito i comportamenti associati come approccio preferito alla costruzione del comportamento. Per ulteriori informazioni sui Xamarin.Forms comportamenti, vedere comportamenti [ Xamarin.Forms ](~/xamarin-forms/app-fundamentals/behaviors/creating.md) e [comportamenti riutilizzabili](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).

## <a name="creating-an-attached-behavior"></a>Creazione di un comportamento associato

Nell'applicazione di esempio viene illustrato un oggetto `NumericValidationBehavior` , che evidenzia il valore immesso dall'utente in un [`Entry`](xref:Xamarin.Forms.Entry) controllo in rosso, se non è un oggetto `double` . Il comportamento è illustrato nell'esempio di codice riportato di seguito:

```csharp
public static class NumericValidationBehavior
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached (
            "AttachBehavior",
            typeof(bool),
            typeof(NumericValidationBehavior),
            false,
            propertyChanged:OnAttachBehaviorChanged);

    public static bool GetAttachBehavior (BindableObject view)
    {
        return (bool)view.GetValue (AttachBehaviorProperty);
    }

    public static void SetAttachBehavior (BindableObject view, bool value)
    {
        view.SetValue (AttachBehaviorProperty, value);
    }

    static void OnAttachBehaviorChanged (BindableObject view, object oldValue, object newValue)
    {
        var entry = view as Entry;
        if (entry == null) {
            return;
        }

        bool attachBehavior = (bool)newValue;
        if (attachBehavior) {
            entry.TextChanged += OnEntryTextChanged;
        } else {
            entry.TextChanged -= OnEntryTextChanged;
        }
    }

    static void OnEntryTextChanged (object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

La classe `NumericValidationBehavior` contiene una proprietà associata denominata `AttachBehavior` con un getter e un setter `static`, che controlla l'aggiunta o la rimozione del comportamento del controllo a cui verrà associata. Tale proprietà associata registra il metodo `OnAttachBehaviorChanged` che verrà eseguito quando cambia il valore della proprietà. Questo metodo registra o Annulla la registrazione di un gestore eventi per l' [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) evento, in base al valore della `AttachBehavior` proprietà associata. La funzionalità di base del comportamento viene fornita dal `OnEntryTextChanged` metodo, che analizza il valore immesso nell'oggetto [`Entry`](xref:Xamarin.Forms.Entry) dall'utente e imposta la `TextColor` proprietà su rosso se il valore non è un `double` .

## <a name="consuming-an-attached-behavior"></a>Uso di un comportamento associato

La `NumericValidationBehavior` classe può essere utilizzata aggiungendo la `AttachBehavior` proprietà associata a un [`Entry`](xref:Xamarin.Forms.Entry) controllo, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

L'equivalente [`Entry`](xref:Xamarin.Forms.Entry) in C# è illustrato nell'esempio di codice seguente:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

In fase di esecuzione, il comportamento risponderà all'interazione con il controllo in base all'implementazione del comportamento. Gli screenshot seguenti illustrano il comportamento associato che risponde a input non valido:

[![](attached-images/screenshots-sml.png "Sample Application with Attached Behavior")](attached-images/screenshots.png#lightbox "Sample Application with Attached Behavior")

> [!NOTE]
> I comportamenti associati vengono scritti per un tipo di controllo specifico, o per una superclasse che è possibile applicare a molti controlli, e devono essere aggiunti solo a controlli compatibili. Il tentativo di associare un comportamento a un controllo incompatibile genererà un comportamento imprevisto dettato dall'implementazione del comportamento.

### <a name="removing-an-attached-behavior-from-a-control"></a>Rimozione di un comportamento associato da un controllo

La classe `NumericValidationBehavior` può essere rimossa da un controllo impostando la proprietà associata `AttachBehavior` su `false`, come illustrato nell'esempio di codice XAML seguente:

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

L'equivalente [`Entry`](xref:Xamarin.Forms.Entry) in C# è illustrato nell'esempio di codice seguente:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

In fase di esecuzione, il metodo `OnAttachBehaviorChanged` sarà eseguito quando il valore della proprietà associata `AttachBehavior` è impostato su `false`. Il `OnAttachBehaviorChanged` metodo annulla la registrazione del gestore eventi per l' [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) evento, assicurando che il comportamento non venga eseguito quando l'utente interagisce con il controllo.

## <a name="summary"></a>Summary

Questo articolo ha illustrato come creare e usare i comportamenti associati. I comportamenti associati sono classi `static` con una o più proprietà associate.

## <a name="related-links"></a>Collegamenti correlati

- [Comportamenti associati (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-attachednumericvalidationbehavior)
