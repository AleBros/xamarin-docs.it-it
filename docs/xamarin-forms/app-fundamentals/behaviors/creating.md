---
title: Creare comportamenti di Xamarin.Forms
description: I comportamenti di Xamarin.Forms vengono creati attraverso la derivazione della classe Behavior o Behavior<T>. Questo articolo spiega come creare e usare i comportamenti di Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 42ad56a7ae34bcef638ed25bea267dcabd21e20c
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "77131086"
---
# <a name="create-xamarinforms-behaviors"></a>Creare comportamenti di Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)

_I comportamenti Xamarin.Forms vengono creati derivando&lt;dalla&gt; classe T Behavior o Behavior. In questo articolo viene illustrato come creare e utilizzare i comportamenti Xamarin.Forms.This article demonstrates how to create and consume Xamarin.Forms behaviors._

## <a name="overview"></a>Panoramica

Il processo per la creazione di un comportamento di Xamarin.Forms è come segue:

1. Creare una classe che [`Behavior`](xref:Xamarin.Forms.Behavior) eredita [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) dalla `T` classe o, dove è il tipo del controllo a cui applicare il comportamento.
1. Eseguire [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) l'override del metodo per eseguire l'impostazione necessaria.
1. Eseguire [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) l'override del metodo per eseguire la pulizia necessaria.
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

Il [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) metodo viene generato immediatamente dopo che il comportamento è associato a un controllo. Questo metodo riceve un riferimento al controllo a cui è associato e può essere utilizzato per registrare i gestori eventi o per eseguire altre impostazioni necessarie per supportare la funzionalità del comportamento. Ad esempio, eseguire una sottoscrizione a un evento in un controllo. La funzionalità del comportamento verrebbe quindi implementata nel gestore eventi per l'evento.

Il [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) metodo viene generato quando il comportamento viene rimosso dal controllo. Questo metodo riceve un riferimento al controllo a cui è associato e serve per eseguire le operazioni di pulizia necessarie. Ad esempio, annullare la sottoscrizione da un evento in un controllo per evitare perdite di memoria.

Il comportamento può quindi essere utilizzato associandolo alla [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) raccolta del controllo appropriato.

## <a name="creating-a-xamarinforms-behavior"></a>Creazione di un comportamento in Xamarin.Forms

L'applicazione di `NumericValidationBehavior`esempio viene illustrato un oggetto [`Entry`](xref:Xamarin.Forms.Entry) , che evidenzia il valore `double`immesso dall'utente in un controllo in rosso, se non è un oggetto . Il comportamento è illustrato nell'esempio di codice riportato di seguito:

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

Deriva `NumericValidationBehavior` dalla [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe , `T` dove [`Entry`](xref:Xamarin.Forms.Entry)è un oggetto . Il [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) metodo registra un gestore eventi per l'evento, [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) con il [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) metodo che annulla la registrazione dell'evento `TextChanged` per evitare perdite di memoria. La funzionalità di base del `OnEntryTextChanged` comportamento viene fornita dal metodo , che `Entry`analizza il [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) valore immesso dall'utente `double`in , e imposta la proprietà su rosso se il valore non è un oggetto .

> [!NOTE]
> Xamarin.Forms non imposta il `BindingContext` di un comportamento, in quanto i comportamenti possono essere condivisi e applicati a più controlli tramite gli stili.

## <a name="consuming-a-xamarinforms-behavior"></a>Utilizzo di un comportamento di Xamarin.Forms

Ogni controllo Xamarin.Forms [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) dispone di un insieme, a cui è possibile aggiungere uno o più comportamenti, come illustrato nell'esempio di codice XAML seguente:Every Xamarin.Forms control has a collection, to which one or more behaviors can be added, as demonstrated in the following XAML code example:

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

Nell'esempio di codice seguente viene illustrato l'equivalente [`Entry`](xref:Xamarin.Forms.Entry) in C:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

In fase di esecuzione, il comportamento risponderà all'interazione con il controllo in base all'implementazione del comportamento. Gli screenshot seguenti illustrano il comportamento che risponde a input non valido:

[![](creating-images/screenshots-sml.png "Sample Application with Xamarin.Forms Behavior")](creating-images/screenshots.png#lightbox "Sample Application with Xamarin.Forms Behavior")

> [!NOTE]
> I comportamenti vengono scritti per un tipo di controllo specifico, o per una superclasse che è possibile applicare a molti controlli, e devono essere aggiunti solo a controlli compatibili. Il tentativo di associare un comportamento a un controllo incompatibile comporta la generazione di un'eccezione.

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>Utilizzo di un comportamento di Xamarin.Forms con uno stile

I comportamenti possono essere utilizzati anche da uno stile esplicito o implicito. Tuttavia, la creazione [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) di uno stile che imposta la proprietà di un controllo non è possibile perché la proprietà è di sola lettura. La soluzione consiste nell'aggiungere una proprietà associata alla classe di comportamento che controlli l'aggiunta del comportamento o la sua rimozione. Il processo è il seguente:

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

L'oggetto può [`Entry`](xref:Xamarin.Forms.Entry) essere applicato [`Style`](xref:Xamarin.Forms.NavigableElement.Style) a un `Style` controllo `StaticResource` impostando la relativa proprietà sull'istanza utilizzando l'estensione di markup, come illustrato nell'esempio di codice seguente:The [`Style`](xref:Xamarin.Forms.Style) can be applied to an control by setting its property to the instance using the markup extension, as demonstrated in the following code example:

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Per altre informazioni sugli stili, vedere [Stili](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Sebbene sia possibile aggiungere proprietà associabili a un comportamento impostato o sottoposto a query in XAML, se si creano comportamenti con stato, questi non devono essere condivisi tra i controlli tramite uno `Style` in un oggetto `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Rimozione di un comportamento da un controllo

Il [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) metodo viene generato quando un comportamento viene rimosso da un controllo e viene utilizzato per eseguire qualsiasi pulizia necessaria, ad esempio l'annullamento della sottoscrizione da un evento per evitare una perdita di memoria. Tuttavia, i comportamenti non vengono rimossi in [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) modo implicito `Remove` `Clear` dai controlli a meno che l'insieme del controllo non venga modificato da un metodo o . L'esempio di codice seguente illustra come rimuovere un comportamento specifico da una raccolta `Behaviors` del controllo:

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

In alternativa, l'insieme del controllo può essere cancellato, come illustrato nell'esempio di codice seguente:Alternatively, the control's [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) collection can be cleared, as demonstrated in the following code example:

```csharp
entry.Behaviors.Clear();
```

Si noti inoltre che i comportamenti non vengono implicitamente rimossi dai controlli quando si estraggono le pagine dallo stack di navigazione. Devono pertanto essere rimossi esplicitamente prima che le pagine diventino esterne all'ambito.

## <a name="summary"></a>Riepilogo

Questo articolo ha spiegato come creare e usare i comportamenti di Xamarin.Forms. I comportamenti Xamarin.Forms vengono creati [`Behavior`](xref:Xamarin.Forms.Behavior) derivando dalla classe o [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) .

## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.Forms Behavior (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior) (Comportamento di Xamarin.Forms)
- [Xamarin.Forms Behavior applied with a Style (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehaviorstyle) (Comportamento di Xamarin.Forms applicato con uno stile)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Behavior\<T>](xref:Xamarin.Forms.Behavior`1)
