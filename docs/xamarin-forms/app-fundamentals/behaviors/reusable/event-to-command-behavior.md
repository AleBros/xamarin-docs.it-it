---
title: "EventToCommandBehavior riutilizzabile" Descrizione: "i comportamenti possono essere usati per associare i comandi ai controlli che non sono stati progettati per interagire con i comandi. Questo articolo illustra la creazione e l'utilizzo Xamarin.Forms di un comportamento per richiamare un comando quando viene generato un evento.
ms. prod: Novell MS. AssetID: EC7F6556-9776-40B8-9424-A8094482A2F3 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 11/09/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="reusable-eventtocommandbehavior"></a>EventToCommandBehavior riutilizzabile

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

_I comportamenti possono essere usati per associare i comandi ai controlli che non sono stati progettati per interagire con i comandi. Questo articolo illustra la creazione e l'utilizzo Xamarin.Forms di un comportamento per richiamare un comando quando viene generato un evento._

## <a name="overview"></a>Panoramica

La `EventToCommandBehavior` classe è un comportamento personalizzato riutilizzabile Xamarin.Forms che esegue un comando in risposta a *qualsiasi* attivazione di eventi. Per impostazione predefinita, gli argomenti dell'evento per l'evento vengono passati al comando e possono essere facoltativamente convertiti da un' [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) implementazione di.

Per usare il comportamento, è necessario impostare le proprietà del comportamento seguenti:

- **EventName** : nome dell'evento di cui il comportamento è in ascolto.
- **Command**: `ICommand` da eseguire. Il comportamento prevede di trovare l' `ICommand` istanza sull'oggetto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del controllo collegato, che può essere ereditato da un elemento padre.

È anche possibile impostare le proprietà facoltative del comportamento seguenti:

- **CommandParameter**: `object` che verrà passato al comando.
- **Converter**: implementazione di [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) che cambia il formato dei dati dell'argomento dell'evento quando vengono passati tra *origine* e *destinazione* dal motore di binding.

> [!NOTE]
> `EventToCommandBehavior`È una classe personalizzata che può essere inclusa nell'esempio di [comportamento EventToCommand](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)e non fa parte di Xamarin.Forms .

## <a name="creating-the-behavior"></a>Creazione del comportamento

La `EventToCommandBehavior` classe deriva dalla classe `BehaviorBase<T>` , che a sua volta deriva dalla [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe. Lo scopo della `BehaviorBase<T>` classe è fornire una classe di base per qualsiasi Xamarin.Forms comportamento che richiede l'impostazione del [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) comportamento sul controllo associato. Ciò garantisce che il comportamento possa essere associato al metodo `ICommand` specificato dalla proprietà `Command` ed eseguirlo quando il comportamento viene utilizzato.

La `BehaviorBase<T>` classe fornisce un oggetto sottoponibile a override [ `OnAttachedTo` ] (xrif: Xamarin.Forms . Comportamento `1.OnAttachedTo(Xamarin.Forms.BindableObject)) method that sets the [` BindingContext `](xref:Xamarin.Forms.BindableObject.BindingContext) of the behavior and an overridable [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . BindableObject)) metodo che pulisce l'oggetto `BindingContext` . La classe archivia anche un riferimento al controllo associato nella proprietà `AssociatedObject`.

### <a name="implementing-bindable-properties"></a>Implementazione di proprietà associabili

La `EventToCommandBehavior` classe definisce quattro [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) istanze che eseguono un comando definito dall'utente quando viene generato un evento. Queste proprietà sono visualizzate nell'esempio di codice seguente:

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

Quando la classe `EventToCommandBehavior` viene utilizzata, la proprietà `Command` deve essere associata a dati di un `ICommand` da eseguire in risposta alla generazione dell'evento definito nella proprietà `EventName`. Il comportamento prevede di trovare l'oggetto `ICommand` sull'oggetto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del controllo associato.

Per impostazione predefinita, al comando vengono passati gli argomenti dell'evento. Questi dati possono essere convertiti,se si vuole, quando vengono passati tra *origine* e *destinazione* dal motore di binding, specificando un'implementazione di [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) come valore della proprietà `Converter`. In alternativa, è possibile passare un parametro al comando specificando il valore della proprietà `CommandParameter`.

### <a name="implementing-the-overrides"></a>Implementazione degli override

La `EventToCommandBehavior` classe esegue l'override di [ `OnAttachedTo` ] (xrif: Xamarin.Forms . Comportamento `1.OnAttachedTo(Xamarin.Forms.BindableObject)) and [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . BindableObject)). metodi della `BehaviorBase<T>` classe, come illustrato nell'esempio di codice seguente:

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

[ `OnAttachedTo` ] (Xrif: Xamarin.Forms . Comportamento `1.OnAttachedTo(Xamarin.Forms.BindableObject)) method performs setup by calling the ` RegisterEvent ` method, passing in the value of the ` EventName ` property as a parameter. The [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . BindableObject)) il metodo esegue la pulizia chiamando il `DeregisterEvent` metodo, passando il valore della `EventName` proprietà come parametro.

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
- In caso contrario, se la `Converter` proprietà definisce un' [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) implementazione, il convertitore viene eseguito e converte i dati dell'argomento dell'evento mentre viene passato tra l' *origine* e la *destinazione* dal motore di associazione.
- In caso contrario, si presuppone che il parametro sia costituito dagli argomenti dell'evento.

`ICommand`Viene quindi eseguito il data binding, passando il parametro al comando, purché il [`CanExecute`](xref:Xamarin.Forms.Command.CanExecute(System.Object)) metodo restituisca `true` .

Sebbene non sia illustrato qui, `EventToCommandBehavior` include anche un `DeregisterEvent` metodo che viene eseguito da [ `OnDetachingFrom` ] (xrif: Xamarin.Forms . Comportamento `1.OnDetachingFrom(Xamarin.Forms.BindableObject)) method. The ` DeregisterEvent ` method is used to locate and deregister the event defined in the ` EventName ', per pulire eventuali perdite di memoria potenziali.

## <a name="consuming-the-behavior"></a>Utilizzo del comportamento

La `EventToCommandBehavior` classe può essere collegata alla [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) raccolta di un controllo, come illustrato nell'esempio di codice XAML seguente:

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

La `Command` proprietà del comportamento è associata ai dati `OutputAgeCommand` della proprietà dell'elemento ViewModel associato, mentre la `Converter` proprietà è impostata sull'istanza di `SelectedItemConverter` , che restituisce l'oggetto [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) dell'oggetto [`ListView`](xref:Xamarin.Forms.ListView) da [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) .

In fase di runtime, il comportamento risponderà all'interazione con il controllo. Quando si seleziona un elemento in [`ListView`](xref:Xamarin.Forms.ListView) , l' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento viene attivato, che eseguirà nell'elemento `OutputAgeCommand` ViewModel. A sua volta, aggiorna la `SelectedItemText` proprietà ViewModel a cui viene [`Label`](xref:Xamarin.Forms.Label) associato, come illustrato nelle schermate seguenti:

[![](event-to-command-behavior-images/screenshots-sml.png "Sample Application with EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "Sample Application with EventToCommandBehavior")

Il vantaggio dell'uso di questo comportamento per eseguire un comando quando viene generato un evento è che i comandi possono essere associati a controlli non progettati per interagire con i comandi. In questo modo, poi, il codice di gestione degli eventi boilerplate viene rimosso dai file code-behind.

## <a name="summary"></a>Summary

Questo articolo ha illustrato l'uso Xamarin.Forms di un comportamento per richiamare un comando quando viene generato un evento. I comportamenti possono essere usati per associare comandi a controlli che non sono stati progettati per interagire con i comandi.

## <a name="related-links"></a>Collegamenti correlati

- [EventToCommand Behavior (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior) (Comportamento EventToCommand (esempio))
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento &lt; T&gt;](xref:Xamarin.Forms.Behavior`1)
