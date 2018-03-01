---
title: EventToCommandBehavior riutilizzabili
description: Comportamenti possono essere utilizzati per associare i comandi con i controlli che non sono stati progettati per interagire con i comandi. In questo articolo viene illustrato l'utilizzo di un comportamento di xamarin. Forms per richiamare un comando quando viene generato un evento.
ms.topic: article
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 2727d83e55e305af1372ece35bdf22abfc653fe7
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="reusable-eventtocommandbehavior"></a>EventToCommandBehavior riutilizzabili

_Comportamenti possono essere utilizzati per associare i comandi con i controlli che non sono stati progettati per interagire con i comandi. In questo articolo viene illustrato l'utilizzo di un comportamento di xamarin. Forms per richiamare un comando quando viene generato un evento._

## <a name="overview"></a>Panoramica

Il `EventToCommandBehavior` classe è un comportamento personalizzato xamarin. Forms riutilizzabile che esegue un comando in risposta a *qualsiasi* generazione dell'evento. Per impostazione predefinita, gli argomenti dell'evento per l'evento verrà passato al comando e può essere facoltativamente convertito da un [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) implementazione.

Per utilizzare il comportamento, è necessario impostare le proprietà di comportamento seguente:

- **EventName** : il nome dell'evento il comportamento è in ascolto.
- **Comando** : **ICommand** da eseguire. Il comportamento prevede di trovare il `ICommand` istanza sul [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) del controllo associato, che può essere ereditato da un elemento padre.

Impostare anche le proprietà di comportamento facoltativi seguenti:

- **CommandParameter** : un `object` che verrà passato al comando.
- **Convertitore** : un [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) implementazione che verrà modificato il formato dei dati di argomento dell'evento come viene passato tra *origine* e *destinazione*dal motore di associazione.

## <a name="creating-the-behavior"></a>La creazione del comportamento

Il `EventToCommandBehavior` deriva dalla classe di `BehaviorBase<T>` (classe), che a sua volta deriva dal [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) classe. Lo scopo del `BehaviorBase<T>` classe è fornire una classe di base per tutti i comportamenti di xamarin. Forms che richiedono il [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) del comportamento da impostare per il controllo associato. Ciò garantisce che il comportamento è possibile associare a ed eseguire il `ICommand` specificato per il `Command` proprietà quando viene utilizzato il comportamento.

Il `BehaviorBase<T>` classe fornisce un sottoponibile a override [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) metodo che imposta il [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) il comportamento e un sottoponibile a override [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)metodo che pulisce la `BindingContext`. Inoltre, la classe archivia un riferimento per il controllo associato nel `AssociatedObject` proprietà.

### <a name="implementing-bindable-properties"></a>Implementazione di proprietà associabili

Il `EventToCommandBehavior` classe definisce quattro [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) istanze, che un utente di eseguire definito comando quando viene generato un evento. Queste proprietà vengono visualizzate nell'esempio di codice seguente:

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

Quando il `EventToCommandBehavior` classe è utilizzata, il `Command` della proprietà deve essere associato ai dati di un `ICommand` deve essere eseguito in risposta alla generazione dell'evento definito nel `EventName` proprietà. Il comportamento si aspetta di trovare il `ICommand` sul [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) del controllo associato.

Per impostazione predefinita, gli argomenti dell'evento per l'evento verranno passati al comando. Questi dati possono essere convertiti, facoltativamente, che viene passato tra *origine* e *destinazione* dal motore di associazione, specificando un [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) come implementazione di `Converter` valore della proprietà. In alternativa, un parametro può essere passato al comando specificando il `CommandParameter` valore della proprietà.

### <a name="implementing-the-overrides"></a>Le sostituzioni di implementazione

Il `EventToCommandBehavior` classe esegue l'override di [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) e [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) metodi del `BehaviorBase<T>` classe, come illustrato nell'esempio di codice seguente:

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

Il [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) metodo esegue il programma di installazione mediante la chiamata di `RegisterEvent` , passando il valore del `EventName` proprietà come parametro. Il [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) metodo esegue la pulitura chiamando il `DeregisterEvent` , passando il valore del `EventName` proprietà come parametro.

### <a name="implementing-the-behavior-functionality"></a>L'implementazione della funzionalità di comportamento

Lo scopo del comportamento consiste nell'eseguire il comando definito dal `Command` nella risposta per la generazione dell'evento definita dalla proprietà di `EventName` proprietà. Le funzionalità di comportamento di base sono illustrata nell'esempio di codice seguente:

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

Il `RegisterEvent` metodo viene eseguito in risposta al `EventToCommandBehavior` viene collegato a un controllo che riceve il valore del `EventName` proprietà come parametro. Il metodo tenta quindi di individuare l'evento definito nel `EventName` , proprietà del controllo associato. Condizione che l'evento può essere disponibile, il `OnEvent` metodo viene registrato per il metodo del gestore per l'evento.

Il `OnEvent` metodo viene eseguito in risposta alla generazione dell'evento definito nel `EventName` proprietà. Purché il `Command` proprietà fa riferimento a un oggetto valido `ICommand`, il metodo tenta di recuperare un parametro da passare per il `ICommand` come indicato di seguito:

- Se il `CommandParameter` proprietà definisce un parametro, questo viene recuperato.
- In caso contrario, se il `Converter` proprietà definisce un [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) implementazione, il convertitore viene eseguita e converte i dati di argomento dell'evento come viene passato tra *origine* e *destinazione* dal motore di associazione.
- In caso contrario, gli argomenti dell'evento si presuppone che sia il parametro.

I dati associati `ICommand` viene quindi eseguita, passando il parametro di comando, a condizione che il [ `CanExecute` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Command.CanExecute/p/System.Object/) restituisce `true`.

Sebbene non illustrato di seguito, il `EventToCommandBehavior` include anche un `DeregisterEvent` metodo che viene eseguita tramite il [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) (metodo). Il `DeregisterEvent` consente di individuare e annullare la registrazione dell'evento definito nel `EventName` proprietà, eseguire la pulizia di eventuali potenziali problemi di memoria.

## <a name="consuming-the-behavior"></a>Il comportamento di utilizzo

Il `EventToCommandBehavior` classe può essere associata al [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) raccolta di un controllo, come illustrato nell'esempio di codice XAML seguente:

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

Il `Command` proprietà del comportamento è associato a dati di `OutputAgeCommand` proprietà ViewModel associato, mentre il `Converter` è impostata sul `SelectedItemConverter` istanza, che restituisce il [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/)del [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) dal [ `SelectedItemChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SelectedItemChangedEventArgs/).

In fase di esecuzione, il comportamento risponderà all'interazione con il controllo. Quando è selezionato un elemento nel [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), [ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) viene generato l'evento, che verrà eseguito il `OutputAgeCommand` nel ViewModel. Aggiorna a sua volta il ViewModel `SelectedItemText` proprietà che la [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) associa, come illustrato nelle schermate seguenti:

[ ![](event-to-command-behavior-images/screenshots-sml.png "Esempio di applicazione con EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png "applicazione con EventToCommandBehavior di esempio")

Il vantaggio dell'utilizzo di questo comportamento per eseguire un comando quando viene generato un evento, è che i comandi possono essere associati ai controlli che non sono stati progettati per interagire con i comandi. Inoltre, per rimuovere il codice di gestione degli eventi, stampa o editoriale dal file code-behind.

## <a name="summary"></a>Riepilogo

Questo articolo sono state illustrate utilizzando un comportamento di xamarin. Forms per richiamare un comando quando viene generato un evento. Comportamenti possono essere utilizzati per associare i comandi con i controlli che non sono stati progettati per interagire con i comandi.


## <a name="related-links"></a>Collegamenti correlati

- [Comportamento di EventToCommand (esempio)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [Comportamento](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [Comportamento<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
