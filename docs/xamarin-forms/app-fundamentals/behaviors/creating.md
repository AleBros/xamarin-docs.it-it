---
title: Creare comportamenti di Xamarin.Forms
description: I comportamenti di Xamarin.Forms vengono creati attraverso la derivazione della classe Behavior o Behavior<T>. Questo articolo spiega come creare e usare i comportamenti di Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 37403b9e33ccd54baac4fd36afbe50504e0380f0
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528963"
---
# <a name="create-xamarinforms-behaviors"></a>Creare comportamenti di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)

_I comportamenti di Xamarin.Forms vengono creati attraverso la derivazione della classe Behavior o Behavior&lt;T&gt;. Questo articolo spiega come creare e usare i comportamenti di Xamarin.Forms._

## <a name="overview"></a>Panoramica

Il processo per la creazione di un comportamento di Xamarin.Forms è come segue:

1. Creare una classe che erediti dalla classe [`Behavior`](xref:Xamarin.Forms.Behavior) o dalla classe [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), dove `T` è il tipo di controllo al quale deve essere applicato il comportamento.
1. Eseguire l'override del metodo [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) per eseguire le configurazioni richieste.
1. Eseguire l'override del metodo [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) per eseguire le operazioni di pulizia richieste.
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

Il metodo [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) viene attivato immediatamente non appena il comportamento è associato a un controllo. Questo metodo riceve un riferimento al controllo a cui è associato e può essere utilizzato per registrare i gestori eventi o per eseguire altre impostazioni necessarie per supportare la funzionalità del comportamento. Ad esempio, eseguire una sottoscrizione a un evento in un controllo. La funzionalità del comportamento verrebbe quindi implementata nel gestore eventi per l'evento.

Il metodo [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) viene attivato quando il comportamento viene rimosso dal controllo. Questo metodo riceve un riferimento al controllo a cui è associato e serve per eseguire le operazioni di pulizia necessarie. Ad esempio, annullare la sottoscrizione da un evento in un controllo per evitare perdite di memoria.

Il comportamento può quindi essere utilizzato associandolo alla raccolta [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) del controllo appropriato.

## <a name="creating-a-xamarinforms-behavior"></a>Creazione di un comportamento in Xamarin.Forms

L'applicazione di esempio illustra un `NumericValidationBehavior`, che evidenzia in rosso il valore immesso dall'utente in un controllo [`Entry`](xref:Xamarin.Forms.Entry), se non ha valore `double`. Il comportamento è illustrato nell'esempio di codice riportato di seguito:

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

`NumericValidationBehavior` deriva dalla classe [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), dove `T` è un controllo [`Entry`](xref:Xamarin.Forms.Entry). Il metodo [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) registra un gestore eventi per l'evento [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged), con il metodo [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) che annulla la registrazione dell'evento `TextChanged`per evitare perdite di memoria. La funzionalità di base del comportamento è fornita dal metodo `OnEntryTextChanged`, che analizza il valore immesso dall'utente nel controllo `Entry` e imposta la proprietà [`TextColor`](xref:Xamarin.Forms.Entry.TextColor) su rosso se il valore non è `double`.

> [!NOTE]
> Xamarin.Forms non imposta il `BindingContext` di un comportamento, in quanto i comportamenti possono essere condivisi e applicati a più controlli tramite gli stili.

## <a name="consuming-a-xamarinforms-behavior"></a>Utilizzo di un comportamento di Xamarin.Forms

Ogni controllo di Xamarin.Forms dispone di una raccolta [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) a cui è possibile aggiungere uno o più comportamenti, come illustrato nell'esempio di codice XAML seguente:

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

Il codice C# equivalente per il controllo [`Entry`](xref:Xamarin.Forms.Entry) è visualizzato nell'esempio seguente:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

In fase di esecuzione, il comportamento risponderà all'interazione con il controllo in base all'implementazione del comportamento. Gli screenshot seguenti illustrano il comportamento che risponde a input non valido:

[![](creating-images/screenshots-sml.png "Applicazione di esempio con comportamento di Xamarin.Forms")](creating-images/screenshots.png#lightbox "Applicazione di esempio con comportamento di Xamarin.Forms")

> [!NOTE]
> I comportamenti vengono scritti per un tipo di controllo specifico, o per una superclasse che è possibile applicare a molti controlli, e devono essere aggiunti solo a controlli compatibili. Il tentativo di associare un comportamento a un controllo incompatibile comporta la generazione di un'eccezione.

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>Utilizzo di un comportamento di Xamarin.Forms con uno stile

I comportamenti possono essere utilizzati anche da uno stile esplicito o implicito. Tuttavia, non è possibile creare uno stile che imposti la proprietà [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) di un controllo perché la proprietà è di sola lettura. La soluzione consiste nell'aggiungere una proprietà associata alla classe di comportamento che controlli l'aggiunta del comportamento o la sua rimozione. La procedura da utilizzare è la seguente:

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

[`Style`](xref:Xamarin.Forms.Style) può essere applicato a un controllo [`Entry`](xref:Xamarin.Forms.Entry) impostando la relativa proprietà [`Style`](xref:Xamarin.Forms.NavigableElement.Style) sull'istanza di `Style` utilizzando l'estensione di markup `StaticResource`, come illustrato nell'esempio di codice seguente:

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Per altre informazioni sugli stili, vedere [Stili](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Sebbene sia possibile aggiungere proprietà associabili a un comportamento impostato o sottoposto a query in XAML, se si creano comportamenti con stato, questi non devono essere condivisi tra i controlli tramite uno `Style` in un oggetto `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Rimozione di un comportamento da un controllo

Il metodo [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) viene attivato quando un comportamento viene rimosso da un controllo e serve per eseguire le operazioni di pulizia necessarie, ad esempio annullare la sottoscrizione a un evento per impedire perdite di memoria. Tuttavia, i comportamenti non sono rimossi in modo implicito dai controlli, a meno che la raccolta [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) del controllo non sia modificata tramite un metodo `Remove` o `Clear`. L'esempio di codice seguente illustra come rimuovere un comportamento specifico da una raccolta `Behaviors` del controllo:

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

In alternativa, la raccolta [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) del controllo può essere cancellata, come illustrato nell'esempio di codice seguente:

```csharp
entry.Behaviors.Clear();
```

Si noti inoltre che i comportamenti non vengono implicitamente rimossi dai controlli quando si estraggono le pagine dallo stack di navigazione. Devono pertanto essere rimossi esplicitamente prima che le pagine diventino esterne all'ambito.

## <a name="summary"></a>Riepilogo

Questo articolo ha spiegato come creare e usare i comportamenti di Xamarin.Forms. I comportamenti di Xamarin.Forms vengono creati attraverso la derivazione dalla classe [`Behavior`](xref:Xamarin.Forms.Behavior) o [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1).


## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.Forms Behavior (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior) (Comportamento di Xamarin.Forms)
- [Xamarin.Forms Behavior applied with a Style (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehaviorstyle) (Comportamento di Xamarin.Forms applicato con uno stile)
- [Classe Behavior](xref:Xamarin.Forms.Behavior)
- [Behavior\<T>](xref:Xamarin.Forms.Behavior`1)
