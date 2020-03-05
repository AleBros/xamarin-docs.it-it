---
title: Proprietà associabili Novell. Forms
description: Questo articolo viene fornita un'introduzione alle proprietà associabile e viene illustrato come creare e il loro uso.
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: 56583aa0df676ae55e1b283f1a8e151b69a13d28
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292011"
---
# <a name="xamarinforms-bindable-properties"></a>Proprietà associabili Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

Le proprietà associabili estendono le funzionalità della proprietà CLR eseguendo il backup di una proprietà con un tipo di [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , anziché eseguire il backup di una proprietà con un campo. Lo scopo di proprietà associabili consiste nel fornire un sistema di proprietà che supporta l'associazione dati, stili, modelli e i valori impostati tramite relazioni padre-figlio. Inoltre, le proprietà associabili possono fornire valori predefiniti, convalida dei valori delle proprietà e i callback che consentono di monitorare le modifiche alle proprietà.

Le proprietà devono essere implementate come proprietà associabile per supportare uno o più delle seguenti funzionalità:

- Fungere da proprietà di *destinazione* valida per data binding.
- Impostazione della proprietà tramite uno [stile](~/xamarin-forms/user-interface/styles/index.md).
- Fornire un valore di proprietà predefinite che è diverso da quello predefinito per il tipo della proprietà.
- La convalida del valore della proprietà.
- Monitoraggio delle modifiche di proprietà.

Esempi di proprietà associabili Novell. Forms includono [`Label.Text`](xref:Xamarin.Forms.Label.Text), [`Button.BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius)e [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation). Ogni proprietà associabile ha una proprietà `public static readonly` corrispondente di tipo [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) esposta nella stessa classe e che corrisponde all'identificatore della proprietà associabile. Ad esempio, l'identificatore di proprietà associabile corrispondente per la proprietà `Label.Text` è [`Label.TextProperty`](xref:Xamarin.Forms.Label.TextProperty).

## <a name="create-a-bindable-property"></a>Creare una proprietà associabile

Il processo per la creazione di una proprietà associabile è come segue:

1. Creare un'istanza di [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) con uno degli overload del metodo [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create*) .
1. Definire le funzioni di accesso alle proprietà per l'istanza di [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) .

Tutte le istanze di [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) devono essere create nel thread UI. Ciò significa che solo il codice viene eseguito sul thread dell'interfaccia utente può ottenere o impostare il valore di una proprietà associabile. Tuttavia, è possibile accedere alle istanze di `BindableProperty` da altri thread eseguendo il marshalling al thread dell'interfaccia utente con il metodo [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) .

### <a name="create-a-property"></a>Creare una proprietà

Per creare un'istanza di `BindableProperty`, la classe che lo contiene deve derivare dalla classe [`BindableObject`](xref:Xamarin.Forms.BindableObject) . Tuttavia, la classe `BindableObject` è elevata nella gerarchia di classi, quindi la maggior parte delle classi utilizzate per le funzionalità dell'interfaccia utente supporta le proprietà associabili.

È possibile creare una proprietà associabile dichiarando una proprietà `public static readonly` di tipo [`BindableProperty`](xref:Xamarin.Forms.BindableProperty). È necessario impostare la proprietà associabile sul valore restituito di uno degli overload del metodo [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . La dichiarazione deve trovarsi all'interno del corpo di [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe derivata, ma all'esterno delle definizioni dei membri.

Quando si crea una [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), è necessario specificare almeno un identificatore insieme ai parametri seguenti:

- Nome del [`BindableProperty`](xref:Xamarin.Forms.BindableProperty).
- Tipo della proprietà.
- Il tipo dell'oggetto.
- Valore predefinito per la proprietà. Ciò garantisce che la proprietà restituisce sempre un valore predefinito specifico quando è impostato e può essere diverso da quello predefinito per il tipo della proprietà. Il valore predefinito verrà ripristinato quando il metodo [`ClearValue`](xref:Xamarin.Forms.BindableObject.ClearValue(Xamarin.Forms.BindableProperty)) viene chiamato sulla proprietà associabile.

Il codice seguente viene illustrato un esempio di una proprietà associabile, con un identificatore e i valori per i quattro parametri obbligatori:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

Verrà creata un'istanza di [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) denominata `EventName`, di tipo `string`. La proprietà appartiene alla classe `EventToCommandBehavior` e il valore predefinito è `null`. La convenzione di denominazione per le proprietà associabili è che l'identificatore di proprietà associabile deve corrispondere al nome della proprietà specificato nel metodo `Create`, con la proprietà "Property" aggiunta. Nell'esempio precedente, quindi, l'identificatore di proprietà associabile è `EventNameProperty`.

Facoltativamente, quando si crea un'istanza di [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , è possibile specificare i parametri seguenti:

- Modalità di associazione. Ciò consente di specificare la direzione in cui verranno propagato modifiche dei valori di proprietà. Nella modalità di associazione predefinita le modifiche vengono propagate dall' *origine* alla *destinazione*.
- Un delegato di convalida che verrà richiamato quando il valore della proprietà è impostato. Per altre informazioni, vedere [callback di convalida](#validation-callbacks).
- Delegato che verrà richiamato quando è stato modificato il valore della proprietà modificata una proprietà. Per altre informazioni, vedere [rilevare le modifiche alle proprietà](#detect-property-changes).
- Una proprietà modifica delegato che verrà richiamato quando il valore della proprietà verrà modificato. Questo delegato ha la stessa firma del delegato di proprietà modificata.
- Un delegato di valore soggetti a coercizione che verrà richiamato quando è stato modificato il valore della proprietà. Per altre informazioni, vedere [coercizione di valori callback](#coerce-value-callbacks).
- `Func` utilizzato per inizializzare un valore di proprietà predefinito. Per ulteriori informazioni, vedere la pagina relativa alla [creazione di un valore predefinito con una funzione Func](#create-a-default-value-with-a-func).

### <a name="create-accessors"></a>Crea funzioni di accesso

Le funzioni di accesso di proprietà sono necessarie per usare la sintassi di proprietà per accedere a una proprietà associabile. La funzione di accesso `Get` deve restituire il valore contenuto nella proprietà associabile corrispondente. Questa operazione può essere eseguita chiamando il metodo [`GetValue`](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) , passando l'identificatore di proprietà associabile sul quale ottenere il valore e quindi eseguendo il cast del risultato al tipo richiesto. La funzione di accesso `Set` deve impostare il valore della proprietà associabile corrispondente. Questa operazione può essere eseguita chiamando il metodo [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) , passando l'identificatore di proprietà associabile su cui impostare il valore e il valore da impostare.

Nell'esempio di codice riportato di seguito vengono illustrate le funzioni di accesso per la `EventName` proprietà associabile:

```csharp
public string EventName
{
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

## <a name="consume-a-bindable-property"></a>Utilizzare una proprietà associabile

Dopo aver creata una proprietà associabile, può essere utilizzato dal codice o XAML. In XAML, ciò si ottiene con la dichiarazione di uno spazio dei nomi con un prefisso con la dichiarazione dello spazio dei nomi che indica il nome dello spazio dei nomi CLR e, facoltativamente, un nome di assembly. Per altre informazioni, vedere [spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md).

L'esempio di codice seguente illustra uno spazio dei nomi XAML per un tipo personalizzato che contiene una proprietà associabile, che viene definita nello stesso assembly del codice dell'applicazione che fa riferimento al tipo personalizzato:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

La dichiarazione dello spazio dei nomi viene usata quando si imposta la proprietà associabile `EventName`, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ListView ...>
  <ListView.Behaviors>
    <local:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

Il codice C# equivalente è visualizzato nell'esempio seguente:

```csharp
var listView = new ListView ();
listView.Behaviors.Add (new EventToCommandBehavior
{
  EventName = "ItemSelected",
  ...
});
```

## <a name="advanced-scenarios"></a>Scenari avanzati

Quando si crea un'istanza di [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , sono disponibili diversi parametri facoltativi che possono essere impostati per abilitare scenari di proprietà associabili avanzati. Questa sezione vengono illustrati questi scenari.

### <a name="detect-property-changes"></a>Rilevare le modifiche alle proprietà

Un `static` metodo di callback modificato dalla proprietà può essere registrato con una proprietà associabile specificando il parametro `propertyChanged` per il metodo [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . Verrà richiamato il metodo di callback specificato quando cambia il valore della proprietà associabile.

Nell'esempio di codice seguente viene illustrato il modo in cui la proprietà associabile `EventName` registra il metodo `OnEventNameChanged` come metodo di callback modificato da una proprietà:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create (
    "EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
...

static void OnEventNameChanged (BindableObject bindable, object oldValue, object newValue)
{
  // Property changed implementation goes here
}
```

Nel metodo di callback modificato da proprietà, il parametro [`BindableObject`](xref:Xamarin.Forms.BindableObject) viene usato per indicare quale istanza della classe proprietaria ha segnalato una modifica e i valori dei due parametri `object` rappresentano i valori vecchi e nuovi della proprietà associabile.

### <a name="validation-callbacks"></a>Callback di convalida

Un metodo di callback di convalida `static` può essere registrato con una proprietà associabile specificando il parametro `validateValue` per il metodo [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . Verrà richiamato il metodo di callback specificato quando è impostato il valore della proprietà associabile.

Nell'esempio di codice seguente viene illustrato il modo in cui la proprietà associabile `Angle` registra il metodo `IsValidValue` come metodo di callback di convalida:

```csharp
public static readonly BindableProperty AngleProperty =
  BindableProperty.Create ("Angle", typeof(double), typeof(HomePage), 0.0, validateValue: IsValidValue);
...

static bool IsValidValue (BindableObject view, object value)
{
  double result;
  bool isDouble = double.TryParse (value.ToString (), out result);
  return (result >= 0 && result <= 360);
}
```

I callback di convalida vengono forniti con un valore e devono restituire `true` se il valore è valido per la proprietà, in caso contrario `false`. Se un callback di convalida restituisce `false`, verrà generata un'eccezione che deve essere gestita dallo sviluppatore. Un tipico utilizzo di un metodo di callback di convalida è vincolare i valori di integer o Double quando è impostata la proprietà associabile. Ad esempio, il metodo `IsValidValue` verifica che il valore della proprietà sia un `double` compreso tra 0 e 360.

### <a name="coerce-value-callbacks"></a>Assegnare i callback di valore

Un metodo di callback del valore di `static` coercizione può essere registrato con una proprietà associabile specificando il parametro `coerceValue` per il metodo [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . Verrà richiamato il metodo di callback specificato quando cambia il valore della proprietà associabile.

> [!IMPORTANT]
> Il tipo di `BindableObject` dispone di un metodo `CoerceValue` che può essere chiamato per forzare una rivalutazione del valore del relativo argomento `BindableProperty` richiamando il callback del valore di coercizione.

Valore di callback vengono usati per forzare una rivalutazione di una proprietà associabile quando cambia il valore della proprietà vengono assegnati forzatamente. Ad esempio, un callback di soggetti a coercizione del valore è utilizzabile per assicurarsi che il valore di una proprietà associabile non sia maggiore del valore di un'altra proprietà associabile.

Nell'esempio di codice seguente viene illustrato il modo in cui la proprietà associabile `Angle` registra il metodo `CoerceAngle` come metodo di callback del valore di coercizione:

```csharp
public static readonly BindableProperty AngleProperty = BindableProperty.Create (
  "Angle", typeof(double), typeof(HomePage), 0.0, coerceValue: CoerceAngle);
public static readonly BindableProperty MaximumAngleProperty = BindableProperty.Create (
  "MaximumAngle", typeof(double), typeof(HomePage), 360.0, propertyChanged: ForceCoerceValue);
...

static object CoerceAngle (BindableObject bindable, object value)
{
  var homePage = bindable as HomePage;
  double input = (double)value;

  if (input > homePage.MaximumAngle)
  {
    input = homePage.MaximumAngle;
  }
  return input;
}

static void ForceCoerceValue(BindableObject bindable, object oldValue, object newValue)
{
  bindable.CoerceValue(AngleProperty);
}
```

Il metodo `CoerceAngle` controlla il valore della proprietà `MaximumAngle` e, se il valore della proprietà `Angle` è maggiore di esso, assegna il valore al valore della proprietà `MaximumAngle`. Inoltre, quando la proprietà `MaximumAngle` modifica il callback del valore di coercizione viene richiamato sulla proprietà `Angle` chiamando il metodo `CoerceValue`.

### <a name="create-a-default-value-with-a-func"></a>Crea un valore predefinito con un Func

È possibile usare un `Func` per inizializzare il valore predefinito di una proprietà associabile, come illustrato nell'esempio di codice seguente:

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

Il parametro `defaultValueCreator` è impostato su un `Func` che richiama il metodo [`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) per restituire un `double` che rappresenta le dimensioni denominate per il tipo di carattere utilizzato in un [`Label`](xref:Xamarin.Forms.Label) sulla piattaforma nativa.

## <a name="related-links"></a>Collegamenti correlati

- [Spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md)
- [Comportamento da evento a comando (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Callback di convalida (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-validationcallback)
- [Callback di valori con coercizione (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-coercevaluecallback)
- [API BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [API BindableObject](xref:Xamarin.Forms.BindableObject)
