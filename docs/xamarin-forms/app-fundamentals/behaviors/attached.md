---
title: Comportamenti collegati
description: I comportamenti collegati sono le classi statiche con uno o più proprietà associate. In questo articolo viene illustrato come creare e utilizzare i comportamenti associati.
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 32573ac3ed0dfecf8ddf1c731613c9a5f88fb1e7
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34845993"
---
# <a name="attached-behaviors"></a>Comportamenti collegati

_I comportamenti collegati sono le classi statiche con uno o più proprietà associate. In questo articolo viene illustrato come creare e utilizzare i comportamenti associati._

## <a name="overview"></a>Panoramica

Una proprietà associata è un tipo speciale di proprietà associabile. Sono definiti in una classe ma collegati ad altri oggetti, e sono riconoscibili in XAML come attributi che contengono una classe e un nome di proprietà separati da un punto.

Una proprietà associata è possibile definire un `propertyChanged` delegato che verrà eseguita quando cambia il valore della proprietà, ad esempio quando la proprietà è impostata su un controllo. Quando il `propertyChanged` delegato viene eseguito, è passato un riferimento ai parametri che contengono i valori vecchi e nuovi per la proprietà e il controllo in cui è associato. Questo delegato può essere utilizzato per aggiungere nuove funzionalità per il controllo a cui è associata la proprietà modificando il riferimento che viene passato, come indicato di seguito:

1. Il `propertyChanged` delegato esegue il cast di riferimento di controllo, ovvero come è stato ricevuto un [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/), per il tipo di controllo che il comportamento è progettato per migliorare.
1. Il `propertyChanged` delegato modifica le proprietà del controllo, chiama i metodi di controllo o registra i gestori eventi per gli eventi esposti dal controllo, per implementare le funzionalità di comportamento di base.

Un problema con i comportamenti collegati è che sono definiti un `static` (classe), con `static` proprietà e metodi. Questo rende difficile creare comportamenti collegati che dispongono di stato. Inoltre, i comportamenti di xamarin. Forms sono sostituite comportamenti collegati come l'approccio consigliato per la costruzione di comportamento. Per ulteriori informazioni sui comportamenti di xamarin. Forms, vedere [xamarin. Forms comportamenti](~/xamarin-forms/app-fundamentals/behaviors/creating.md) e [riutilizzabile comportamenti](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).

## <a name="creating-an-attached-behavior"></a>Creazione di un comportamento dell'allegato

L'applicazione di esempio viene illustrato un `NumericValidationBehavior`, evidenziando il valore immesso dall'utente in un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) in rosso, il controllo se non è un `double`. Il comportamento è illustrato nell'esempio di codice seguente:

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

Il `NumericValidationBehavior` classe contiene una proprietà associata denominata `AttachBehavior` con un `static` getter e setter, che controlla l'aggiunta o rimozione del comportamento del controllo a cui verrà collegato. Questo collegato proprietà registri il `OnAttachBehaviorChanged` metodo che verrà eseguita quando cambia il valore della proprietà. Questo metodo registra o Registra rimuovere un gestore eventi per il [ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) evento, in base al valore del `AttachBehavior` proprietà associata. Le funzionalità di base del comportamento viene fornita dal `OnEntryTextChanged` metodo, che analizza il valore immesso nel [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) dall'utente e imposta il `TextColor` proprietà rosso se il valore non è un `double`.

## <a name="consuming-an-attached-behavior"></a>Utilizzo di un comportamento dell'allegato

Il `NumericValidationBehavior` classe può essere utilizzata con l'aggiunta di `AttachBehavior` associata a un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllare, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

L'equivalente [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) in c# è illustrato nell'esempio di codice seguente:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

In fase di esecuzione, il comportamento risponderà all'interazione con il controllo, in base all'implementazione di comportamento. Le seguenti catture illustrano il comportamento associato risponde all'input non valido:

[![](attached-images/screenshots-sml.png "Esempio di applicazione con comportamento collegato")](attached-images/screenshots.png#lightbox "applicazione con il comportamento associato di esempio")

> [!NOTE]
> I comportamenti collegati vengono scritti per un tipo di controllo specifico (o una superclasse che è possibile applicare a molti controlli) e devono essere aggiunti solo a un controllo compatibile. Il tentativo di associare un comportamento a un controllo incompatibile determineranno un comportamento sconosciuto e dipende dall'implementazione di comportamento.

### <a name="removing-an-attached-behavior-from-a-control"></a>Rimozione di un comportamento collegato da un controllo

Il `NumericValidationBehavior` classe può essere rimosso da un controllo impostando il `AttachBehavior` proprietà associata `false`, come illustrato nell'esempio di codice XAML seguente:

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

L'equivalente [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) in c# è illustrato nell'esempio di codice seguente:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

In fase di esecuzione, il `OnAttachBehaviorChanged` metodo verrà eseguito quando il valore della `AttachBehavior` proprietà associata è impostata su `false`. Il `OnAttachBehaviorChanged` metodo verrà quindi annullare la registrazione del gestore eventi per il [ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) evento, assicurando che il comportamento non viene eseguito quando l'utente interagisce con il controllo.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come creare e utilizzare i comportamenti associati. I comportamenti collegati sono `static` classi con uno o più proprietà associate.


## <a name="related-links"></a>Collegamenti correlati

- [Comportamenti collegati (esempio)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/attachednumericvalidationbehavior/)
