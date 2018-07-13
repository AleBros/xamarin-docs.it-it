---
title: Comportamenti di xamarin. Forms
description: Vengono creati i comportamenti di xamarin. Forms mediante la derivazione da un comportamento o il comportamento<T> classe. Questo articolo illustra come creare e utilizzare i comportamenti di xamarin. Forms.
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 7e057567ec0bb72e9bcc016d4a9fef3af78a3ea1
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998895"
---
# <a name="xamarinforms-behaviors"></a>Comportamenti di xamarin. Forms

_Vengono creati i comportamenti di xamarin. Forms mediante la derivazione da un comportamento o il comportamento<T> classe. Questo articolo illustra come creare e utilizzare i comportamenti di xamarin. Forms._

## <a name="overview"></a>Panoramica

Il processo per la creazione di un comportamento di xamarin. Forms è come segue:

1. Creare una classe che eredita dal [ `Behavior` ](xref:Xamarin.Forms.Behavior) oppure [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) (classe), in cui `T` è il tipo del controllo a cui applicare il comportamento.
1. Eseguire l'override di [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) metodo per eseguire alcuna configurazione.
1. Eseguire l'override di [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) metodo per eseguire la pulizia necessaria.
1. Implementare le funzionalità principali del comportamento.

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

Il [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) metodo viene generato immediatamente dopo che il comportamento è associato a un controllo. Questo metodo riceve un riferimento al controllo a cui è associato e può essere utilizzato per registrare i gestori eventi o eseguire altre impostazioni necessarie per supportare la funzionalità di comportamento. Ad esempio, può eseguire una sottoscrizione a un evento in un controllo. La funzionalità di comportamento potrebbe quindi essere implementata nel gestore eventi per l'evento.

Il [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) metodo viene generato quando il comportamento viene rimossa dal controllo. Questo metodo riceve un riferimento al controllo a cui è associato e viene usato per eseguire la pulizia necessaria. Ad esempio, è possibile annullare la sottoscrizione da un evento in un controllo per evitare perdite di memoria.

Il comportamento può essere quindi usato tramite il collegamento per il [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) insieme del controllo appropriato.

## <a name="creating-a-xamarinforms-behavior"></a>Creazione di un comportamento di xamarin. Forms

L'applicazione di esempio viene illustrato un `NumericValidationBehavior`, che evidenzia il valore immesso dall'utente in un [ `Entry` ](xref:Xamarin.Forms.Entry) in rosso, il controllo se non è un `double`. Il comportamento è illustrato nell'esempio di codice seguente:

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

Il `NumericValidationBehavior` deriva dal [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) (classe), dove `T` è un [ `Entry` ](xref:Xamarin.Forms.Entry). Il [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) metodo registra un gestore eventi per il [ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged) evento, con la [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) metodo annullando la registrazione di `TextChanged`perdite di eventi per evitare che la memoria. Le funzionalità di base del comportamento avviene tramite il `OnEntryTextChanged` metodo, che analizza il valore immesso dall'utente nel `Entry`e imposta la [ `TextColor` ](xref:Xamarin.Forms.Entry.TextColor) proprietà in rosso se il valore non è un `double`.

> [!NOTE]
> Xamarin. Forms, non viene impostata la `BindingContext` di un comportamento, in quanto i comportamenti possono essere condivisi e applicati a più controlli tramite gli stili.

## <a name="consuming-a-xamarinforms-behavior"></a>Utilizzo di un comportamento di xamarin. Forms

Ogni controllo di xamarin. Forms ha un [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) insieme, a cui è possibile aggiungere uno o più comportamenti, come illustrato nell'esempio di codice XAML seguente:

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

L'equivalente [ `Entry` ](xref:Xamarin.Forms.Entry) nel linguaggio c# viene illustrato nell'esempio di codice seguente:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

In fase di esecuzione il comportamento di rispondere alla richiesta per l'interazione con il controllo, in base all'implementazione del comportamento. Gli screenshot seguenti illustrano il comportamento di risposta agli input non valido:

[![](creating-images/screenshots-sml.png "Applicazione con il comportamento di xamarin. Forms di esempio")](creating-images/screenshots.png#lightbox "applicazione con il comportamento di xamarin. Forms di esempio")

> [!NOTE]
> I comportamenti sono scritti per un tipo di controllo specifici (o una superclasse che è possibile applicare a molti controlli) e devono essere aggiunte solo a un controllo compatibile. Tentativo di associare un comportamento a un controllo incompatibile comporterà un'eccezione generata.

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>Utilizzo di un comportamento di xamarin. Forms con uno stile

I comportamenti possono essere utilizzati anche da uno stile esplicito o implicito. Tuttavia, la creazione di uno stile che imposta la [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) proprietà di un controllo non è possibile perché la proprietà è di sola lettura. La soluzione consiste nell'aggiungere una proprietà associata alla classe di comportamento che controlla l'aggiunta e rimozione il comportamento. Il processo è come segue:

1. Aggiungere una proprietà associata alla classe di comportamento che verrà usata per controllare l'aggiunta o la rimozione del comportamento per il controllo a cui verrà il comportamento associato. Assicurarsi che la proprietà associata registra un `propertyChanged` delegato che verrà eseguito quando viene modificato il valore della proprietà.
1. Creare un `static` getter e setter per la proprietà associata.
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

Il `NumericValidationBehavior` classe contiene una proprietà associata denominata `AttachBehavior` con un `static` getter e setter, che controlla l'aggiunta o la rimozione del comportamento per il controllo a cui verrà collegato. Ciò collegato proprietà registri il `OnAttachBehaviorChanged` metodo che verrà eseguito quando viene modificato il valore della proprietà. Questo metodo aggiunge o rimuove il comportamento per il controllo, in base al valore della `AttachBehavior` proprietà associata.

Nell'esempio di codice riportato di seguito viene illustrato un *esplicita* applicare uno stile per il `NumericValidationBehavior` che usa il `AttachBehavior` allegati, proprietà e che possono essere applicati alle [ `Entry` ](xref:Xamarin.Forms.Entry) controlli:

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

Il [ `Style` ](xref:Xamarin.Forms.Style) può essere applicato a un [ `Entry` ](xref:Xamarin.Forms.Entry) controllo impostando relativo [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) proprietà per il `Style` istanza utilizzando la `StaticResource` estensione di markup, come illustrato nell'esempio di codice seguente:

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Per altre informazioni sugli stili, vedere [stili](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Mentre è possibile aggiungere proprietà associabili a un comportamento che viene impostata o sottoposti a query in XAML, se si creano i comportamenti che hanno stato non devono essere condivise tra i controlli in una `Style` in un `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Rimozione di un comportamento da un controllo

Il [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) metodo viene generato quando un comportamento viene rimosso da un controllo e viene usato per eseguire la pulizia necessaria, ad esempio l'annullamento della sottoscrizione da un evento per impedire una perdita di memoria. Tuttavia, i comportamenti non sono rimossi in modo implicito dai controlli, a meno che il controllo [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) raccolta viene modificata da un `Remove` o `Clear` (metodo). Esempio di codice seguente illustra come rimuovere un comportamento specifico da un controllo `Behaviors` raccolta:

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

In alternativa, del controllo [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) raccolta può essere cancellata, come illustrato nell'esempio di codice seguente:

```csharp
entry.Behaviors.Clear();
```

Si noti inoltre che i comportamenti non vengono implicitamente rimosse dai controlli quando le pagine vengono estratti dallo stack di navigazione. Invece, devono essere esplicitamente rimossi prima della pagine esterno all'ambito.

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come creare e utilizzare i comportamenti di xamarin. Forms. Vengono creati i comportamenti di xamarin. Forms mediante la derivazione dal [ `Behavior` ](xref:Xamarin.Forms.Behavior) oppure [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) classe.


## <a name="related-links"></a>Collegamenti correlati

- [Comportamento di xamarin. Forms (esempio)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehavior/)
- [Comportamento di xamarin. Forms applicato con uno stile (esempio)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehaviorstyle/)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento<T>](xref:Xamarin.Forms.Behavior`1)
