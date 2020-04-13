---
title: EventToCommandBehavior riutilizzabile
description: I comportamenti possono essere usati per associare comandi a controlli che non sono stati progettati per interagire con i comandi. Questo articolo illustra la creazione e l'uso di un comportamento di Xamarin.Forms per la chiamata di un comando quando viene attivato un evento.
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/09/2018
ms.openlocfilehash: 292a6aaaea4fb0f84138e04c88f001c72ddd096d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "68650905"
---
# <a name="reusable-eventtocommandbehavior"></a>EventToCommandBehavior riutilizzabile

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

_I comportamenti possono essere utilizzati per associare comandi a controlli che non sono stati progettati per interagire con i comandi. In questo articolo viene illustrata la creazione e l'utilizzo di un comportamento Xamarin.Forms per richiamare un comando quando viene generato un evento._

## <a name="overview"></a>Panoramica

La classe `EventToCommandBehavior` è un comportamento personalizzato Xamarin.Forms riutilizzabile che esegue un comando in risposta alla generazione di *qualsiasi* evento. Per impostazione predefinita, gli argomenti dell'evento per l'evento verranno [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) passati al comando e possono essere convertiti facoltativamente da un'implementazione.

Per usare il comportamento, è necessario impostare le proprietà del comportamento seguenti:

- **EventName** : nome dell'evento di cui il comportamento è in ascolto.
- **Command**: `ICommand` da eseguire. Il comportamento prevede di `ICommand` trovare [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) l'istanza sul controllo associato, che può essere ereditata da un elemento padre.

È anche possibile impostare le proprietà facoltative del comportamento seguenti:

- **CommandParameter**: `object` che verrà passato al comando.
- **Converter**: implementazione di [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) che cambia il formato dei dati dell'argomento dell'evento quando vengono passati tra *origine* e *destinazione* dal motore di binding.

> [!NOTE]
> `EventToCommandBehavior` è una classe personalizzata disponibile nell'[esempio di comportamento di EventToCommand](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior) e non fa parte di Xamarin.Forms.

## <a name="creating-the-behavior"></a>Creazione del comportamento

La `EventToCommandBehavior` classe deriva `BehaviorBase<T>` dalla classe , che a [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) sua volta deriva dalla classe . Lo scopo `BehaviorBase<T>` della classe è fornire una classe base per tutti i [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) comportamenti Xamarin.Forms che richiedono l'impostazione del comportamento sul controllo associato. Ciò garantisce che il comportamento possa essere associato al metodo `ICommand` specificato dalla proprietà `Command` ed eseguirlo quando il comportamento viene utilizzato.

La `BehaviorBase<T>` classe fornisce [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) un metodo [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) sottoponibile a override [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) che imposta il `BindingContext`comportamento e un metodo sottoponibile a override che pulisce l'oggetto . La classe archivia anche un riferimento al controllo associato nella proprietà `AssociatedObject`.

### <a name="implementing-bindable-properties"></a>Implementazione di proprietà associabili

La `EventToCommandBehavior` classe definisce quattro [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) istanze, che eseguono un comando definito dall'utente quando viene generato un evento. Queste proprietà sono visualizzate nell'esempio di codice seguente:

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

Quando la classe `EventToCommandBehavior` viene utilizzata, la proprietà `Command` deve essere associata a dati di un `ICommand` da eseguire in risposta alla generazione dell'evento definito nella proprietà `EventName`. Il comportamento si aspetta `ICommand` di [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) trovare il on del controllo associato.

Per impostazione predefinita, al comando vengono passati gli argomenti dell'evento. Questi dati possono essere convertiti,se si vuole, quando vengono passati tra *origine* e *destinazione* dal motore di binding, specificando un'implementazione di [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) come valore della proprietà `Converter`. In alternativa, è possibile passare un parametro al comando specificando il valore della proprietà `CommandParameter`.

### <a name="implementing-the-overrides"></a>Implementazione degli override

La `EventToCommandBehavior` classe esegue [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) l'override `BehaviorBase<T>` dei metodi e della classe , come illustrato nell'esempio di codice seguente:

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

Il [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) metodo esegue l'installazione chiamando il `RegisterEvent` metodo `EventName` , passando il valore della proprietà come parametro. Il [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) metodo esegue la `DeregisterEvent` pulizia chiamando il metodo `EventName` , passando il valore della proprietà come parametro.

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
- In caso `Converter` contrario, [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) se la proprietà definisce un'implementazione, il convertitore viene eseguito e converte i dati dell'argomento dell'evento quando vengono passati tra *origine* e *destinazione* dal motore di associazione.
- In caso contrario, si presuppone che il parametro sia costituito dagli argomenti dell'evento.

L'associazione di `ICommand` dati viene quindi eseguita, passando [`CanExecute`](xref:Xamarin.Forms.Command.CanExecute(System.Object)) il `true`parametro al comando, a condizione che il metodo restituisca .

Anche se non `EventToCommandBehavior` illustrato qui, il include anche `DeregisterEvent` [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) un metodo che viene eseguito dal metodo. Il metodo `DeregisterEvent` viene usato per individuare l'evento definito nella proprietà `EventName` e annullarne la registrazione, allo scopo di eseguire la pulizia di eventuali perdite di memoria.

## <a name="consuming-the-behavior"></a>Utilizzo del comportamento

La `EventToCommandBehavior` classe può essere [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) associata alla raccolta di un controllo, come illustrato nell'esempio di codice XAML seguente:The class can be attached to the collection of a control, as demonstrated in the following XAML code example:

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

La `Command` proprietà del comportamento è `OutputAgeCommand` associata a dati alla `Converter` proprietà dell'oggetto `SelectedItemConverter` ViewModel associato, mentre la proprietà è impostata sull'istanza , che restituisce l'oggetto [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) [`ListView`](xref:Xamarin.Forms.ListView) from l. [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs)

In fase di runtime, il comportamento risponderà all'interazione con il controllo. Quando un elemento viene [`ListView`](xref:Xamarin.Forms.ListView)selezionato [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) nella , verrà generato `OutputAgeCommand` l'evento , che eseguirà il nel ViewModel. A sua volta questo `SelectedItemText` aggiorna [`Label`](xref:Xamarin.Forms.Label) il ViewModel proprietà che il si associa a, come illustrato nelle schermate seguenti:In turn this updates the ViewModel property that the binds to, as shown in the following screenshots:

[![](event-to-command-behavior-images/screenshots-sml.png "Sample Application with EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "Sample Application with EventToCommandBehavior")

Il vantaggio dell'uso di questo comportamento per eseguire un comando quando viene generato un evento è che i comandi possono essere associati a controlli non progettati per interagire con i comandi. In questo modo, poi, il codice di gestione degli eventi boilerplate viene rimosso dai file code-behind.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato l'uso di un comportamento di Xamarin.Forms per la chiamata di un comando quando viene generato un evento. I comportamenti possono essere usati per associare comandi a controlli che non sono stati progettati per interagire con i comandi.

## <a name="related-links"></a>Collegamenti correlati

- [EventToCommand Behavior (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior) (Comportamento EventToCommand (esempio))
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
