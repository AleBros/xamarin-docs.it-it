---
title: EventToCommandBehavior riutilizzabile
description: I comportamenti possono essere usati per associare comandi a controlli che non sono stati progettati per interagire con i comandi. Questo articolo illustra la creazione e l'uso di un comportamento di Xamarin.Forms per la chiamata di un comando quando viene generato un evento.
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/09/2018
ms.openlocfilehash: 1c2aea9a5dead1962cfd4bb71d1a1211e8b98ee9
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53056707"
---
# <a name="reusable-eventtocommandbehavior"></a>EventToCommandBehavior riutilizzabile

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)

_I comportamenti possono essere usati per associare comandi a controlli che non sono stati progettati per interagire con i comandi. Questo articolo illustra la creazione e l'uso di un comportamento di Xamarin.Forms per la chiamata di un comando quando viene generato un evento._

## <a name="overview"></a>Panoramica

La classe `EventToCommandBehavior` è un comportamento personalizzato Xamarin.Forms riutilizzabile che esegue un comando in risposta alla generazione di *qualsiasi* evento. Per impostazione predefinita, gli argomenti dell'evento vengono passati al comando e, facoltativamente, possono essere convertiti da un'implementazione di [`IValueConverter`](xref:Xamarin.Forms.IValueConverter).

Per usare il comportamento, è necessario impostare le proprietà del comportamento seguenti:

- **EventName** : nome dell'evento di cui il comportamento è in ascolto.
- **Command**: `ICommand` da eseguire. Il comportamento prevede di trovare l'istanza di `ICommand` nella proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del controllo associato, che può essere ereditato da un elemento padre.

È anche possibile impostare le proprietà facoltative del comportamento seguenti:

- **CommandParameter**: `object` che verrà passato al comando.
- **Converter**: implementazione di [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) che cambia il formato dei dati dell'argomento dell'evento quando vengono passati tra *origine* e *destinazione* dal motore di binding.

> [!NOTE]
> `EventToCommandBehavior` è una classe personalizzata disponibile nell'[esempio di comportamento di EventToCommand](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/) e non fa parte di Xamarin.Forms.

## <a name="creating-the-behavior"></a>Creazione del comportamento

La classe `EventToCommandBehavior` deriva dalla classe `BehaviorBase<T>`, che a sua volta deriva dalla classe [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1). Lo scopo della classe `BehaviorBase<T>` è di offrire una classe di base per tutti i comportamenti di Xamarin.Forms che richiedono che la proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del comportamento sia impostata sul controllo associato. Ciò garantisce che il comportamento possa essere associato al metodo `ICommand` specificato dalla proprietà `Command` ed eseguirlo quando il comportamento viene utilizzato.

La classe `BehaviorBase<T>` mette a disposizione un metodo [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) sottoponibile a override che imposta la proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del comportamento e un metodo [ `OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) sottoponibile a override che esegue la pulizia della proprietà `BindingContext`. La classe archivia anche un riferimento al controllo associato nella proprietà `AssociatedObject`.

### <a name="implementing-bindable-properties"></a>Implementazione di proprietà associabili

La classe `EventToCommandBehavior` definisce quattro istanze di [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), che eseguono un comando definito dall'utente quando viene generato un evento. Queste proprietà sono visualizzate nell'esempio di codice seguente:

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

Quando la classe `EventToCommandBehavior` viene utilizzata, la proprietà `Command` deve essere associata a dati di un `ICommand` da eseguire in risposta alla generazione dell'evento definito nella proprietà `EventName`. Il comportamento prevede di trovare `ICommand` nella proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del controllo associato.

Per impostazione predefinita, al comando vengono passati gli argomenti dell'evento. Questi dati possono essere convertiti,se si vuole, quando vengono passati tra *origine* e *destinazione* dal motore di binding, specificando un'implementazione di [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) come valore della proprietà `Converter`. In alternativa, è possibile passare un parametro al comando specificando il valore della proprietà `CommandParameter`.

### <a name="implementing-the-overrides"></a>Implementazione degli override

La classe `EventToCommandBehavior` esegue l'override dei metodi [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) e [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) della classe `BehaviorBase<T>`, come illustrato nell'esempio di codice seguente:

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

Il metodo [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) esegue la configurazione chiamando il metodo `RegisterEvent`, passando il valore della proprietà `EventName` come parametro. Il metodo [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) esegue la pulizia chiamando il metodo `DeregisterEvent`, passando il valore della proprietà `EventName` come parametro.

### <a name="implementing-the-behavior-functionality"></a>Implementazione della funzionalità del comportamento

Lo scopo del comportamento consiste nell'eseguire il comando definito dalla proprietà `Command` in risposta alla generazione dell'evento definito dalla proprietà `EventName`. La funzionalità del comportamento principale è illustrata nell'esempio di codice seguente:

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

Il metodo `RegisterEvent` viene eseguito in risposta all'associazione di `EventToCommandBehavior` a un controllo e riceve il valore della proprietà `EventName` come parametro. Il metodo tenta quindi di individuare l'evento definito nella proprietà `EventName`, nel controllo associato. A condizione che l'evento possa essere individuato, il metodo `OnEvent` viene registrato come metodo gestore dell'evento.

Il metodo `OnEvent` viene eseguito in risposta alla generazione dell'evento definito nella proprietà `EventName`. A condizione che la proprietà `Command` faccia riferimento a un `ICommand` valido, il metodo tenta di recuperare un parametro da passare a `ICommand` come indicato di seguito:

- Se la proprietà `CommandParameter` definisce un parametro, questo viene recuperato.
- In caso contrario, se la proprietà `Converter` definisce un'implementazione di [`IValueConverter`](xref:Xamarin.Forms.IValueConverter), il convertitore viene eseguito e converte i dati dell'argomento dell'evento quando questo viene passato tra *origine* e*destinazione* dal motore di binding.
- In caso contrario, si presuppone che il parametro sia costituito dagli argomenti dell'evento.

Viene quindi eseguito il comando `ICommand` associato a dati passando il parametro al comando, a condizione che il metodo [`CanExecute`](xref:Xamarin.Forms.Command.CanExecute(System.Object)) restituisca `true`.

Anche se non è illustrato qui, `EventToCommandBehavior` include anche un metodo `DeregisterEvent` che viene eseguito dal metodo [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)). Il metodo `DeregisterEvent` viene usato per individuare l'evento definito nella proprietà `EventName` e annullarne la registrazione, allo scopo di eseguire la pulizia di eventuali perdite di memoria.

## <a name="consuming-the-behavior"></a>Utilizzo del comportamento

La classe `EventToCommandBehavior` può essere associata alla raccolta [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) di un controllo, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ListView ItemsSource="{Binding People}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <TextCell Text="{Binding Name}" />
        </DataTemplate>
    </ListView.ItemTemplate>
    <ListView.Behaviors>
        <local:EventToCommandBehavior EventName="ItemSelected" Command="{Binding OutputAgeCommand}" Converter="{StaticResource SelectedItemConverter}" />
    </ListView.Behaviors>
</ListView>
<Label Text="{Binding SelectedItemText}" />
```

Il codice C# equivalente è visualizzato nell'esempio seguente:

```csharp
var listView = new ListView();
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "People");
listView.ItemTemplate = new DataTemplate(() =>
{
    var textCell = new TextCell();
    textCell.SetBinding(TextCell.TextProperty, "Name");
    return textCell;
});
listView.Behaviors.Add(new EventToCommandBehavior
{
    EventName = "ItemSelected",
    Command = ((HomePageViewModel)BindingContext).OutputAgeCommand,
    Converter = new SelectedItemEventArgsToSelectedItemConverter()
});

var selectedItemLabel = new Label();
selectedItemLabel.SetBinding(Label.TextProperty, "SelectedItemText");
```

La proprietà `Command` del comportamento è associata a dati della proprietà `OutputAgeCommand` dell'elemento ViewModel associato, mentre la proprietà `Converter` è impostata sull'istanza di `SelectedItemConverter`, che restituisce [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) di [`ListView`](xref:Xamarin.Forms.ListView) da [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs).

In fase di runtime, il comportamento risponderà all'interazione con il controllo. Quando viene selezionato un elemento in [`ListView`](xref:Xamarin.Forms.ListView), viene generato l'evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected), che esegue `OutputAgeCommand` nell'elemento ViewModel. A sua volta, questo aggiorna la proprietà `SelectedItemText` del ViewModel a cui l'elemento [`Label`](xref:Xamarin.Forms.Label) è associato, come illustrato negli screenshot seguenti:

[![](event-to-command-behavior-images/screenshots-sml.png "Applicazione di esempio con EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "Applicazione di esempio con EventToCommandBehavior")

Il vantaggio dell'uso di questo comportamento per eseguire un comando quando viene generato un evento è che i comandi possono essere associati a controlli non progettati per interagire con i comandi. In questo modo, poi, il codice di gestione degli eventi boilerplate viene rimosso dai file code-behind.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato l'uso di un comportamento di Xamarin.Forms per la chiamata di un comando quando viene generato un evento. I comportamenti possono essere usati per associare comandi a controlli che non sono stati progettati per interagire con i comandi.

## <a name="related-links"></a>Collegamenti correlati

- [EventToCommand Behavior (sample)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/) (Comportamento EventToCommand (esempio))
- [Behavior](xref:Xamarin.Forms.Behavior)
- [Behavior&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
