---
title: Comportamenti di xamarin. Forms
description: I comportamenti di xamarin. Forms vengono creati mediante la derivazione dal comportamento o un comportamento<T> classe. In questo articolo viene illustrato come creare e utilizzare i comportamenti di xamarin. Forms.
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 3a86e7713620eff90db995941eb35df7bc393a76
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848291"
---
# <a name="xamarinforms-behaviors"></a>Comportamenti di xamarin. Forms

_I comportamenti di xamarin. Forms vengono creati mediante la derivazione dal comportamento o un comportamento<T> classe. In questo articolo viene illustrato come creare e utilizzare i comportamenti di xamarin. Forms._

## <a name="overview"></a>Panoramica

Il processo per la creazione di un comportamento di xamarin. Forms è come segue:

1. Creare una classe che eredita il [ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/) o [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) (classe), in cui `T` è il tipo del controllo a cui applicare il comportamento.
1. Eseguire l'override di [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) metodo per eseguire alcuna configurazione.
1. Eseguire l'override di [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) metodo per eseguire la pulizia necessaria.
1. Implementare le funzionalità di base del comportamento.

Ciò comporta la struttura illustrata nell'esempio di codice seguente:

```csharp
public class CustomBehavior : Behavior<View>
{
    protected override void OnAttachedTo (View bindable)
    {
        base.OnAttachedTo (bindable);
        // Perform setup
    }

    protected override void OnDetachingFrom (View bindable)
    {
        base.OnDetachingFrom (bindable);
        // Perform clean up
    }

    // Behavior implementation
}
```

Il [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) metodo generato immediatamente dopo il comportamento è associato a un controllo. Questo metodo riceve un riferimento al controllo a cui è collegato e consente di registrare i gestori eventi o eseguire altre impostazioni necessarie per supportare la funzionalità di comportamento. Ad esempio, è possibile sottoscrivere un evento su un controllo. La funzionalità di comportamento potrebbe quindi essere implementata nel gestore eventi per l'evento.

Il [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) metodo viene generato quando il comportamento viene rimosso dal controllo. Questo metodo riceve un riferimento al controllo a cui è collegato e viene utilizzato per eseguire la pulizia necessaria. Ad esempio, è possibile annullare la sottoscrizione da un evento su un controllo per evitare perdite di memoria.

Il comportamento può essere quindi usato da associare al [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) raccolta di controllo appropriato.

## <a name="creating-a-xamarinforms-behavior"></a>Creazione di un comportamento di xamarin. Forms

L'applicazione di esempio viene illustrato un `NumericValidationBehavior`, evidenziando il valore immesso dall'utente in un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) in rosso, il controllo se non è un `double`. Il comportamento è illustrato nell'esempio di codice seguente:

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    protected override void OnAttachedTo(Entry entry)
    {
        entry.TextChanged += OnEntryTextChanged;
        base.OnAttachedTo(entry);
    }

    protected override void OnDetachingFrom(Entry entry)
    {
        entry.TextChanged -= OnEntryTextChanged;
        base.OnDetachingFrom(entry);
    }

    void OnEntryTextChanged(object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

Il `NumericValidationBehavior` deriva il [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) (classe), in cui `T` è un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/). Il [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) metodo registra un gestore eventi per il [ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) evento, con la [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) metodo annullare la registrazione di `TextChanged`le perdite di eventi per evitare che la memoria. Le funzionalità di base del comportamento viene fornita per il `OnEntryTextChanged` metodo, che analizza il valore immesso dall'utente nel `Entry`e imposta il [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.TextColor/) proprietà rosso se il valore non è un `double`.

> [!NOTE]
> Xamarin. Forms non viene impostato il `BindingContext` di un comportamento, in quanto i comportamenti possono essere condivisi e applicati a più controlli tramite gli stili.

## <a name="consuming-a-xamarinforms-behavior"></a>Utilizzo di un comportamento di xamarin. Forms

Ogni controllo in xamarin. Forms è un [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) insieme, a cui è possibile aggiungere uno o più comportamenti, come illustrato nell'esempio di codice XAML seguente:

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

L'equivalente [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) in c# è illustrato nell'esempio di codice seguente:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

In fase di esecuzione il comportamento risponderà all'interazione con il controllo, in base all'implementazione di comportamento. Le schermate seguenti illustrano il comportamento di blocco di input non valido:

[![](creating-images/screenshots-sml.png "Applicazione con il comportamento di xamarin. Forms di esempio")](creating-images/screenshots.png#lightbox "applicazione con il comportamento di xamarin. Forms di esempio")

> [!NOTE]
> I comportamenti sono destinati a un tipo di controllo specifico (o una superclasse che è possibile applicare a molti controlli) e devono essere aggiunti solo a un controllo compatibile. Il tentativo di associare un comportamento a un controllo incompatibile comporterà viene generata un'eccezione.

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>Utilizzo di un comportamento di xamarin. Forms con uno stile

I comportamenti possono essere utilizzati anche da un tipo esplicita o implicita. Tuttavia, la creazione di uno stile che imposta il [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) proprietà di un controllo non è possibile perché la proprietà è di sola lettura. La soluzione consiste nell'aggiungere una proprietà associata alla classe di comportamento che controlla aggiungendo e rimuovendo il comportamento. Il processo è come segue:

1. Aggiungere una proprietà associata alla classe di comportamento che verrà utilizzata per controllare l'aggiunta o rimozione del comportamento per il controllo a cui verrà il comportamento associato. Verificare che la proprietà associata registra un `propertyChanged` delegato che verrà eseguita quando cambia il valore della proprietà.
1. Creare un `static` getter e setter della proprietà associata.
1. Implementare la logica nel `propertyChanged` delegato da aggiungere e rimuovere il comportamento.

Esempio di codice seguente viene illustrata una proprietà associata che controlla l'aggiunta e rimozione di `NumericValidationBehavior`:

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached ("AttachBehavior", typeof(bool), typeof(NumericValidationBehavior), false, propertyChanged: OnAttachBehaviorChanged);

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
            entry.Behaviors.Add (new NumericValidationBehavior ());
        } else {
            var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
            if (toRemove != null) {
                entry.Behaviors.Remove (toRemove);
            }
        }
    }
    ...
}
```

Il `NumericValidationBehavior` classe contiene una proprietà associata denominata `AttachBehavior` con un `static` getter e setter, che controlla l'aggiunta o rimozione del comportamento del controllo a cui verrà collegato. Questo collegato proprietà registri il `OnAttachBehaviorChanged` metodo che verrà eseguita quando cambia il valore della proprietà. Questo metodo aggiunge o rimuove il comportamento per il controllo, in base al valore del `AttachBehavior` proprietà associata.

Nell'esempio di codice riportato di seguito viene un *esplicita* di stile per il `NumericValidationBehavior` che utilizza il `AttachBehavior` allegati, proprietà e che possono essere applicati alle [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controlli:

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

Il [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) può essere applicato a un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllo impostando il relativo [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà per il `Style` istanza utilizzando il `StaticResource` estensione di markup, come illustrato nell'esempio di codice seguente:

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Per ulteriori informazioni sugli stili, vedere [stili](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Sebbene sia possibile aggiungere le proprietà associabili a un comportamento che è impostata o eseguire una query in XAML, se si creano i comportamenti che è stato non devono essere condivise tra i controlli in un `Style` in un `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Rimozione di un comportamento da un controllo

Il [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) metodo viene generato quando un comportamento viene rimosso da un controllo e viene utilizzato per eseguire la pulizia necessaria, ad esempio l'annullamento della sottoscrizione da un evento per evitare una perdita di memoria. Tuttavia, i comportamenti non sono rimossi in modo implicito dai controlli, a meno che il controllo [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) raccolta viene modificata da un `Remove` o `Clear` metodo. L'esempio di codice seguente viene illustrato come rimuovere un comportamento specifico da un controllo `Behaviors` raccolta:

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

In alternativa, del controllo [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) raccolta può essere cancellata, come illustrato nell'esempio di codice seguente:

```csharp
entry.Behaviors.Clear();
```

Si noti inoltre che i comportamenti non sono rimossi in modo implicito da controlli quando le pagine vengono estratti dallo stack di navigazione. Invece, devono essere esplicitamente rimossi prima di passare dall'ambito di pagine.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come creare e utilizzare i comportamenti di xamarin. Forms. I comportamenti di xamarin. Forms vengono creati mediante la derivazione da di [ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/) o [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) classe.


## <a name="related-links"></a>Collegamenti correlati

- [Comportamento di xamarin. Forms (esempio)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehavior/)
- [Comportamento di xamarin. Forms applicato con uno stile (esempio)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehaviorstyle/)
- [Comportamento](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [Comportamento<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
