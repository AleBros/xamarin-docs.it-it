---
title: Crea Xamarin.Forms comportamenti
description: Xamarin.Formsi comportamenti vengono creati tramite la derivazione dal comportamento o dalla classe del comportamento <T> . Questo articolo illustra come creare e utilizzare i Xamarin.Forms comportamenti.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 67db30b5caadce75a41755530db2b245562d0304
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135824"
---
# <a name="create-xamarinforms-behaviors"></a>Crea Xamarin.Forms comportamenti

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)

_I comportamenti di Novell. Forms vengono creati mediante derivazione dalla classe Behavior o Behavior &lt; T &gt; . Questo articolo illustra come creare e utilizzare i Xamarin.Forms comportamenti._

## <a name="overview"></a>Panoramica

Il processo per la creazione di un Xamarin.Forms comportamento è il seguente:

1. Creare una classe che erediti dalla [`Behavior`](xref:Xamarin.Forms.Behavior) classe o [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) , dove `T` è il tipo del controllo a cui deve essere applicato il comportamento.
1. Eseguire l'override di [ `OnAttachedTo` ] (xrif: Xamarin.Forms . Behavior'1. OnAttachedTo ( Xamarin.Forms . BindableObject)) per eseguire tutte le operazioni di configurazione necessarie.
1. Eseguire l'override di [ `OnDetachingFrom` ] (xrif: Xamarin.Forms . Behavior'1. OnDetachingFrom ( Xamarin.Forms . BindableObject)) per eseguire tutte le operazioni di pulizia richieste.
1. Implementare le funzionalità principali del comportamento.

Il risultato è la struttura mostrata nell'esempio di codice seguente:

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

[ `OnAttachedTo` ] (Xrif: Xamarin.Forms . Behavior'1. OnAttachedTo ( Xamarin.Forms . BindableObject)) il metodo viene generato immediatamente dopo che il comportamento è associato a un controllo. Questo metodo riceve un riferimento al controllo a cui è associato e può essere utilizzato per registrare i gestori eventi o per eseguire altre impostazioni necessarie per supportare la funzionalità del comportamento. Ad esempio, eseguire una sottoscrizione a un evento in un controllo. La funzionalità del comportamento verrebbe quindi implementata nel gestore eventi per l'evento.

[ `OnDetachingFrom` ] (Xrif: Xamarin.Forms . Behavior'1. OnDetachingFrom ( Xamarin.Forms . BindableObject)) il metodo viene generato quando il comportamento viene rimosso dal controllo. Questo metodo riceve un riferimento al controllo a cui è associato e serve per eseguire le operazioni di pulizia necessarie. Ad esempio, annullare la sottoscrizione da un evento in un controllo per evitare perdite di memoria.

Il comportamento può quindi essere utilizzato connettendo l'oggetto alla [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) raccolta del controllo appropriato.

## <a name="creating-a-xamarinforms-behavior"></a>Creazione di un Xamarin.Forms comportamento

Nell'applicazione di esempio viene illustrato un oggetto `NumericValidationBehavior` , che evidenzia il valore immesso dall'utente in un [`Entry`](xref:Xamarin.Forms.Entry) controllo in rosso, se non è un oggetto `double` . Il comportamento è illustrato nell'esempio di codice riportato di seguito:

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

L'oggetto `NumericValidationBehavior` deriva dalla [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe, dove `T` è un oggetto [`Entry`](xref:Xamarin.Forms.Entry) . [ `OnAttachedTo` ] (Xrif: Xamarin.Forms . Comportamento `1.OnAttachedTo(Xamarin.Forms.BindableObject)) method registers an event handler for the [` TextChanged `](xref:Xamarin.Forms.InputView.TextChanged) event, with the [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . BindableObject)) il metodo annulla la registrazione dell' `TextChanged` evento per evitare perdite di memoria. La funzionalità di base del comportamento viene fornita dal `OnEntryTextChanged` metodo, che analizza il valore immesso dall'utente in `Entry` e imposta la [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) proprietà su rosso se il valore non è un `double` .

> [!NOTE]
> Xamarin.Formsnon imposta l'oggetto `BindingContext` di un comportamento, perché i comportamenti possono essere condivisi e applicati a più controlli tramite gli stili.

## <a name="consuming-a-xamarinforms-behavior"></a>Utilizzo di un Xamarin.Forms comportamento

Ogni Xamarin.Forms controllo ha una [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) raccolta, a cui è possibile aggiungere uno o più comportamenti, come illustrato nell'esempio di codice XAML seguente:

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

L'equivalente [`Entry`](xref:Xamarin.Forms.Entry) in C# è illustrato nell'esempio di codice seguente:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

In fase di esecuzione, il comportamento risponderà all'interazione con il controllo in base all'implementazione del comportamento. Gli screenshot seguenti illustrano il comportamento che risponde a input non valido:

[![](creating-images/screenshots-sml.png "Sample Application with Xamarin.Forms Behavior")](creating-images/screenshots.png#lightbox "Sample Application with Xamarin.Forms Behavior")

> [!NOTE]
> I comportamenti vengono scritti per un tipo di controllo specifico, o per una superclasse che è possibile applicare a molti controlli, e devono essere aggiunti solo a controlli compatibili. Il tentativo di associare un comportamento a un controllo incompatibile comporta la generazione di un'eccezione.

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>Utilizzo di un Xamarin.Forms comportamento con uno stile

I comportamenti possono essere utilizzati anche da uno stile esplicito o implicito. Tuttavia, la creazione di uno stile che imposta la [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) proprietà di un controllo non è possibile perché la proprietà è di sola lettura. La soluzione consiste nell'aggiungere una proprietà associata alla classe di comportamento che controlli l'aggiunta del comportamento o la sua rimozione. Il processo è il seguente:

1. Aggiungere alla classe di comportamento una proprietà associata che verrà usata per controllare l'aggiunta del comportamento al controllo o la sua rimozione dal controllo a cui il comportamento verrà associato. Assicurarsi che la proprietà associata registri un delegato `propertyChanged` che verrà eseguito quando cambia il valore della proprietà.
1. Creare un getter e un setter `static` per la proprietà associata.
1. Implementare la logica nel delegato `propertyChanged` per aggiungere e rimuovere il comportamento.

L'esempio di codice seguente illustra una proprietà associata che controlla l'aggiunta e la rimozione di `NumericValidationBehavior`:

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

La classe `NumericValidationBehavior` contiene una proprietà associata denominata `AttachBehavior` con un getter e un setter `static`, che controlla l'aggiunta o la rimozione del comportamento del controllo a cui verrà associata. Tale proprietà associata registra il metodo `OnAttachBehaviorChanged` che verrà eseguito quando cambia il valore della proprietà. Questo metodo aggiunge il comportamento al controllo o lo rimuove da esso in base al valore della proprietà `AttachBehavior` associata.

L'esempio di codice riportato di seguito illustra uno stile *esplicito* per il `NumericValidationBehavior` che usa la proprietà associata `AttachBehavior` e che può essere applicato ai controlli [`Entry`](xref:Xamarin.Forms.Entry):

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

[`Style`](xref:Xamarin.Forms.Style)È possibile applicare a un controllo impostando la [`Entry`](xref:Xamarin.Forms.Entry) relativa [`Style`](xref:Xamarin.Forms.NavigableElement.Style) proprietà sull' `Style` istanza usando l' `StaticResource` estensione di markup, come illustrato nell'esempio di codice seguente:

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Per altre informazioni sugli stili, vedere [Stili](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Sebbene sia possibile aggiungere proprietà associabili a un comportamento impostato o sottoposto a query in XAML, se si creano comportamenti con stato, questi non devono essere condivisi tra i controlli tramite uno `Style` in un oggetto `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Rimozione di un comportamento da un controllo

[ `OnDetachingFrom` ] (Xrif: Xamarin.Forms . Comportamenti di comportamento `1.OnDetachingFrom(Xamarin.Forms.BindableObject)) method is fired when a behavior is removed from a control, and is used to perform any required cleanup such as unsubscribing from an event to prevent a memory leak. However, behaviors are not implicitly removed from controls unless the control's [` `](xref:Xamarin.Forms.VisualElement.Behaviors) collection is modified by a ` rimuovere la ` or ` ` method. The following code example demonstrates removing a specific behavior from a control's ` raccolta dei comportamenti cancellati:

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

In alternativa, la raccolta del controllo [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) può essere cancellata, come illustrato nell'esempio di codice seguente:

```csharp
entry.Behaviors.Clear();
```

Si noti inoltre che i comportamenti non vengono implicitamente rimossi dai controlli quando si estraggono le pagine dallo stack di navigazione. Devono pertanto essere rimossi esplicitamente prima che le pagine diventino esterne all'ambito.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare e utilizzare i Xamarin.Forms comportamenti. Xamarin.Formsi comportamenti vengono creati mediante derivazione dalla [`Behavior`](xref:Xamarin.Forms.Behavior) [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe o.

## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.FormsComportamento (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)
- [Xamarin.FormsComportamento applicato con uno stile (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehaviorstyle)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento\<T>](xref:Xamarin.Forms.Behavior`1)
