---
title: EventToCommandBehavior riutilizzabili
description: I comportamenti sono utilizzabile per associare i comandi con i controlli che non sono stati progettati per interagire con i comandi. Questo articolo illustra l'uso di un comportamento di xamarin. Forms per richiamare un comando quando viene generato un evento.
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 3151179b6ff6d26b74a87ded747310646b304603
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996321"
---
# <a name="reusable-eventtocommandbehavior"></a>EventToCommandBehavior riutilizzabili

_I comportamenti sono utilizzabile per associare i comandi con i controlli che non sono stati progettati per interagire con i comandi. Questo articolo illustra l'uso di un comportamento di xamarin. Forms per richiamare un comando quando viene generato un evento._

## <a name="overview"></a>Panoramica

Il `EventToCommandBehavior` classe è un comportamento personalizzato xamarin. Forms riutilizzabile che esegue un comando in risposta alle *qualsiasi* generazione dell'evento. Per impostazione predefinita, gli argomenti dell'evento per l'evento verrà passato al comando e può essere facoltativamente convertita da un [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) implementazione.

Utilizzare il comportamento, è necessario impostare le proprietà del comportamento seguente:

- **EventName** : il nome dell'evento il comportamento è in ascolto.
- **Comando** : tramite il **ICommand** da eseguire. Il comportamento prevede di trovare il `ICommand` all'istanza sul [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del controllo associato, che può essere ereditato da un elemento padre.

Può anche essere impostare le seguenti proprietà di comportamento facoltativo:

- **CommandParameter** : un `object` che verrà passato al comando.
- **Convertitore** : un' [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) implementazione che verrà modificato il formato dei dati di argomento dell'evento come viene passato tra *origine* e *destinazione*dal motore di binding.

## <a name="creating-the-behavior"></a>La creazione del comportamento

Il `EventToCommandBehavior` deriva dalla classe la `BehaviorBase<T>` (classe), che a sua volta deriva dalle [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) classe. Lo scopo del `BehaviorBase<T>` classe consiste nel fornire una classe di base per tutti i comportamenti di xamarin. Forms che richiedono il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del comportamento da impostare sul controllo associato. Ciò garantisce che il comportamento può associare a ed eseguire la `ICommand` specificato da di `Command` proprietà quando viene utilizzato il comportamento.

Il `BehaviorBase<T>` classe fornisce un sottoponibile a override [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) metodo che imposta la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) di una sottoponibile a override e il comportamento [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))metodo che pulisce la `BindingContext`. Inoltre, la classe archivia un riferimento al controllo associato nel `AssociatedObject` proprietà.

### <a name="implementing-bindable-properties"></a>Implementazione di proprietà associabili

Il `EventToCommandBehavior` classe definisce quattro [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) istanze, che eseguono un utente definito comando quando viene generato un evento. Queste proprietà vengono visualizzate nell'esempio di codice seguente:

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  public static readonly BindableProperty EventNameProperty =
    BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
  public static readonly BindableProperty CommandProperty =
    BindableProperty.Create ("Command", typeof(ICommand), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty CommandParameterProperty =
    BindableProperty.Create ("CommandParameter", typeof(object), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty InputConverterProperty =
    BindableProperty.Create ("Converter", typeof(IValueConverter), typeof(EventToCommandBehavior), null);

  public string EventName { ... }
  public ICommand Command { ... }
  public object CommandParameter { ... }
  public IValueConverter Converter { ...  }
  ...
}
```

Quando la `EventToCommandBehavior` classe viene utilizzata, il `Command` della proprietà deve essere associato ai dati di un `ICommand` da eseguire in risposta alla generazione dell'evento definito nel `EventName` proprietà. Il comportamento aspetteranno di trovare il `ICommand` nella [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del controllo associato.

Per impostazione predefinita, gli argomenti dell'evento per l'evento verranno essere passati al comando. Questi dati possono essere convertiti, facoltativamente, che viene passato tra *origine* e *destinazione* dal motore di associazione, specificando un' [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) come implementazione di `Converter` valore della proprietà. In alternativa, un parametro può essere passato al comando specificando il `CommandParameter` valore della proprietà.

### <a name="implementing-the-overrides"></a>Implementazione di override

Il `EventToCommandBehavior` classe esegue l'override di [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) e [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) metodi del `BehaviorBase<T>` classe, come illustrato nell'esempio di codice seguente:

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  protected override void OnAttachedTo (View bindable)
  {
    base.OnAttachedTo (bindable);
    RegisterEvent (EventName);
  }

  protected override void OnDetachingFrom (View bindable)
  {
    DeregisterEvent (EventName);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

Il [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) metodo esegue il programma di installazione chiamando il `RegisterEvent` metodo, passando il valore del `EventName` proprietà come un parametro. Il [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) metodo esegue la pulizia chiamando il `DeregisterEvent` metodo, passando il valore del `EventName` proprietà come parametro.

### <a name="implementing-the-behavior-functionality"></a>L'implementazione della funzionalità di comportamento

Lo scopo del comportamento consiste nell'eseguire il comando definito dal `Command` proprietà nella risposta per la generazione dell'evento definito dal `EventName` proprietà. La funzionalità di comportamento principale è illustrata nell'esempio di codice seguente:

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  void RegisterEvent (string name)
  {
    if (string.IsNullOrWhiteSpace (name)) {
      return;
    }

    EventInfo eventInfo = AssociatedObject.GetType ().GetRuntimeEvent (name);
    if (eventInfo == null) {
      throw new ArgumentException (string.Format ("EventToCommandBehavior: Can't register the '{0}' event.", EventName));
    }
    MethodInfo methodInfo = typeof(EventToCommandBehavior).GetTypeInfo ().GetDeclaredMethod ("OnEvent");
    eventHandler = methodInfo.CreateDelegate (eventInfo.EventHandlerType, this);
    eventInfo.AddEventHandler (AssociatedObject, eventHandler);
  }

  void OnEvent (object sender, object eventArgs)
  {
    if (Command == null) {
      return;
    }

    object resolvedParameter;
    if (CommandParameter != null) {
      resolvedParameter = CommandParameter;
    } else if (Converter != null) {
      resolvedParameter = Converter.Convert (eventArgs, typeof(object), null, null);
    } else {
      resolvedParameter = eventArgs;
    }        

    if (Command.CanExecute (resolvedParameter)) {
      Command.Execute (resolvedParameter);
    }
  }
  ...
}
```

Il `RegisterEvent` metodo viene eseguito in risposta al `EventToCommandBehavior` viene allegato a un controllo che riceve il valore del `EventName` proprietà come parametro. Il metodo tenta quindi di individuare l'evento definito nel `EventName` proprietà, il controllo associato. Condizione che l'evento può essere individuato, il `OnEvent` metodo viene registrato per essere il metodo del gestore dell'evento.

Il `OnEvent` viene eseguito in risposta alla generazione dell'evento definito nel metodo il `EventName` proprietà. A condizione che il `Command` proprietà fa riferimento a un valore valido `ICommand`, il metodo tenta di recuperare un parametro da passare per il `ICommand` come indicato di seguito:

- Se il `CommandParameter` proprietà definisce un parametro, questo viene recuperato.
- In caso contrario, se il `Converter` proprietà definisce un [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) implementazione, il convertitore non viene eseguita e converte i dati dell'argomento evento come viene passato tra *origine* e*destinazione* dal motore di binding.
- In caso contrario, gli argomenti dell'evento si presuppone che sia il parametro.

I dati associati `ICommand` viene quindi eseguita, passando il parametro del comando, a condizione che il [ `CanExecute` ](xref:Xamarin.Forms.Command.CanExecute(System.Object)) restituzione del metodo `true`.

Sebbene non illustrato in questo caso, il `EventToCommandBehavior` include anche una `DeregisterEvent` metodo eseguito dalle [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) (metodo). Il `DeregisterEvent` metodo viene utilizzato per individuare e annullare la registrazione dell'evento definito nel `EventName` proprietà, eseguire la pulizia di eventuali potenziali problemi di memoria.

## <a name="consuming-the-behavior"></a>Il comportamento di utilizzo

Il `EventToCommandBehavior` classe può essere associata ai [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) raccolta di un controllo, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ListView ItemsSource="{Binding People}">
  <ListView.Behaviors>
    <local:EventToCommandBehavior EventName="ItemSelected" Command="{Binding OutputAgeCommand}"
                                  Converter="{StaticResource SelectedItemConverter}" />
  </ListView.Behaviors>
</ListView>
<Label Text="{Binding SelectedItemText}" />
```

Il codice C# equivalente è visualizzato nell'esempio seguente:

```csharp
var listView = new ListView ();
listView.SetBinding (ItemsView<Cell>.ItemsSourceProperty, "People");
listView.Behaviors.Add (new EventToCommandBehavior {
  EventName = "ItemSelected",
  Command = ((HomePageViewModel)BindingContext).OutputAgeCommand,
  Converter = new SelectedItemEventArgsToSelectedItemConverter ()
});

var selectedItemLabel = new Label ();
selectedItemLabel.SetBinding (Label.TextProperty, "SelectedItemText");
```

Il `Command` proprietà del comportamento è associato a dati di `OutputAgeCommand` proprietà dell'elemento ViewModel associato, mentre il `Converter` è impostata sul `SelectedItemConverter` dell'istanza, che restituisce il [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)del [ `ListView` ](xref:Xamarin.Forms.ListView) dal [ `SelectedItemChangedEventArgs` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs).

In fase di esecuzione, il comportamento risponderà all'interazione con il controllo. Quando viene selezionato un elemento nel [ `ListView` ](xref:Xamarin.Forms.ListView), il [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) , quindi, viene generato l'evento cui verrà eseguita la `OutputAgeCommand` nel ViewModel. A sua volta Aggiorna l'elemento ViewModel `SelectedItemText` proprietà che il [ `Label` ](xref:Xamarin.Forms.Label) viene associato, come illustrato negli screenshot seguenti:

[![](event-to-command-behavior-images/screenshots-sml.png "Esempio di applicazione con EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "con EventToCommandBehavior applicazione di esempio")

Il vantaggio dell'uso di questo comportamento per eseguire un comando quando viene generato un evento, è che i comandi possono essere associati ai controlli che non sono stati progettati per interagire con i comandi. Inoltre, il codice di gestione degli eventi boilerplate verrà rimosso dal file code-behind.

## <a name="summary"></a>Riepilogo

Questo articolo illustrato l'utilizzo di un comportamento di xamarin. Forms per richiamare un comando quando viene generato un evento. I comportamenti sono utilizzabile per associare i comandi con i controlli che non sono stati progettati per interagire con i comandi.


## <a name="related-links"></a>Collegamenti correlati

- [Comportamento EventToCommand (esempio)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento<T>](xref:Xamarin.Forms.Behavior`1)
