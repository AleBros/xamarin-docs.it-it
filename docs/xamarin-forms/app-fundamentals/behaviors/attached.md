---
title: Comportamenti associati
description: Comportamenti associati sono le classi statiche con uno o più proprietà associate. Questo articolo illustra come creare e utilizzare i comportamenti associati.
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 2c9bd9ad4e7572b9eae6f0073da8a2c8f1e7c9fc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995346"
---
# <a name="attached-behaviors"></a>Comportamenti associati

_Comportamenti associati sono le classi statiche con uno o più proprietà associate. Questo articolo illustra come creare e utilizzare i comportamenti associati._

## <a name="overview"></a>Panoramica

Una proprietà associata è un tipo speciale di proprietà associabili. Sono definiti in una classe ma collegati ad altri oggetti, e sono riconoscibili in XAML come attributi che contengono una classe e un nome di proprietà separati da un punto.

Una proprietà associata è possibile definire un `propertyChanged` delegato che verrà eseguita quando cambia il valore della proprietà, ad esempio quando la proprietà è impostata su un controllo. Quando il `propertyChanged` delegato viene eseguita, è passato un riferimento ai parametri che contengono i valori vecchi e nuovi per la proprietà e il controllo su cui è associato. Questo delegato può essere utilizzato per aggiungere nuove funzionalità al controllo che ha la proprietà è associata a modificando il riferimento che viene passato, come indicato di seguito:

1. Il `propertyChanged` delegato esegue il cast il riferimento di controllo, come è stato ricevuto un [ `BindableObject` ](xref:Xamarin.Forms.BindableObject)al tipo di controllo che il comportamento è progettato per migliorare.
1. Il `propertyChanged` delegato modifica le proprietà del controllo, chiama i metodi del controllo o registra i gestori di eventi per gli eventi esposti dal controllo, per implementare le funzionalità di comportamento di base.

Un problema con comportamenti associati è che sono definiti un `static` (classe), con `static` proprietà e metodi. Questo rende difficile creare comportamenti associati che dispongono dello stato. Inoltre, i comportamenti di xamarin. Forms sono sostituite comportamenti associati come approccio preferito per la costruzione di comportamento. Per altre informazioni sui comportamenti di xamarin. Forms, vedere [comportamenti di xamarin. Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md) e [comportamenti riutilizzabili](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).

## <a name="creating-an-attached-behavior"></a>Creazione di un comportamento dell'allegato

L'applicazione di esempio viene illustrato un `NumericValidationBehavior`, che evidenzia il valore immesso dall'utente in un [ `Entry` ](xref:Xamarin.Forms.Entry) in rosso, il controllo se non è un `double`. Il comportamento è illustrato nell'esempio di codice seguente:

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

Il `NumericValidationBehavior` classe contiene una proprietà associata denominata `AttachBehavior` con un `static` getter e setter, che controlla l'aggiunta o la rimozione del comportamento per il controllo a cui verrà collegato. Ciò collegato proprietà registri il `OnAttachBehaviorChanged` metodo che verrà eseguito quando viene modificato il valore della proprietà. Questo metodo registra o deprovisioning registra un gestore eventi per il [ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged) evento, in base al valore della `AttachBehavior` proprietà associata. Le funzionalità di base del comportamento avviene tramite il `OnEntryTextChanged` metodo, che analizza il valore immesso nel [ `Entry` ](xref:Xamarin.Forms.Entry) dall'utente e i set del `TextColor` proprietà in rosso se il valore non è un `double`.

## <a name="consuming-an-attached-behavior"></a>Utilizzo di un comportamento dell'allegato

Il `NumericValidationBehavior` classe può essere usata aggiungendo il `AttachBehavior` proprietà associata un' [ `Entry` ](xref:Xamarin.Forms.Entry) controllare, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

L'equivalente [ `Entry` ](xref:Xamarin.Forms.Entry) nel linguaggio c# viene illustrato nell'esempio di codice seguente:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

In fase di esecuzione, il comportamento risponderà all'interazione con il controllo, in base all'implementazione del comportamento. Gli screenshot seguenti dimostrano il comportamento associato rispondono a input non valido:

[![](attached-images/screenshots-sml.png "Esempio di applicazione con comportamento collegato")](attached-images/screenshots.png#lightbox "applicazione con comportamento collegato di esempio")

> [!NOTE]
> Comportamenti associati vengono scritti per un tipo di controllo specifici (o una superclasse che è possibile applicare a molti controlli) e devono essere aggiunte solo a un controllo compatibile. Tentativo di associare un comportamento a un controllo incompatibile determineranno un comportamento sconosciuto e dipende dall'implementazione comportamento.

### <a name="removing-an-attached-behavior-from-a-control"></a>Rimozione di un comportamento dell'allegato da un controllo

Il `NumericValidationBehavior` classe può essere rimosso da un controllo impostando la `AttachBehavior` proprietà associata `false`, come illustrato nell'esempio di codice XAML seguente:

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

L'equivalente [ `Entry` ](xref:Xamarin.Forms.Entry) nel linguaggio c# viene illustrato nell'esempio di codice seguente:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

In fase di esecuzione, il `OnAttachBehaviorChanged` metodo sarà eseguita quando il valore della `AttachBehavior` associata è impostata su `false`. Il `OnAttachBehaviorChanged` metodo verrà quindi annullare la registrazione del gestore eventi per il [ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged) evento, assicurando che il comportamento non viene eseguito quando l'utente interagisce con il controllo.

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come creare e utilizzare i comportamenti associati. Comportamenti associati sono `static` classi con uno o più proprietà associate.


## <a name="related-links"></a>Collegamenti correlati

- [Comportamenti associati (esempio)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/attachednumericvalidationbehavior/)
