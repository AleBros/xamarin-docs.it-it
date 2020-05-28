---
title: Xamarin.FormsProprietà associabili
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 33b3763075b64ea8af615465825313a527d20db2
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138177"
---
# <a name="xamarinforms-bindable-properties"></a>Xamarin.FormsProprietà associabili

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

Le proprietà associabili estendono le funzionalità della proprietà CLR eseguendo il backup di una proprietà con un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) tipo, anziché eseguire il backup di una proprietà con un campo. Lo scopo delle proprietà associabili è fornire un sistema di proprietà che supporti data binding, stili, modelli e valori impostati tramite relazioni padre-figlio. Inoltre, le proprietà associabili possono fornire valori predefiniti, la convalida dei valori delle proprietà e i callback che monitorano le modifiche delle proprietà.

Le proprietà devono essere implementate come proprietà associabili per supportare una o più delle funzionalità seguenti:

- Fungere da proprietà di *destinazione* valida per data binding.
- Impostazione della proprietà tramite uno [stile](~/xamarin-forms/user-interface/styles/index.md).
- Specificando un valore predefinito della proprietà diverso da quello predefinito per il tipo della proprietà.
- Convalida del valore della proprietà.
- Monitoraggio delle modifiche alle proprietà.

Esempi di Xamarin.Forms proprietà associabili sono [`Label.Text`](xref:Xamarin.Forms.Label.Text) , [`Button.BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) e [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) . Ogni proprietà associabile dispone `public static readonly` di un campo corrispondente di tipo [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) esposto nella stessa classe e che corrisponde all'identificatore della proprietà associabile. Ad esempio, l'identificatore di proprietà associabile corrispondente per la `Label.Text` proprietà è [`Label.TextProperty`](xref:Xamarin.Forms.Label.TextProperty) .

## <a name="create-a-bindable-property"></a>Creare una proprietà associabile

Il processo per la creazione di una proprietà associabile è il seguente:

1. Creare un' [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) istanza di con uno degli [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create*) Overload del metodo.
1. Definire le funzioni di accesso alle proprietà per l' [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) istanza di.

Tutte le [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) istanze devono essere create nel thread UI. Ciò significa che solo il codice eseguito nel thread dell'interfaccia utente può ottenere o impostare il valore di una proprietà associabile. Tuttavia, `BindableProperty` è possibile accedere alle istanze da altri thread eseguendo il marshalling al thread dell'interfaccia utente con il [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) metodo.

### <a name="create-a-property"></a>Creare una proprietà

Per creare un' `BindableProperty` istanza di, la classe che lo contiene deve derivare dalla [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe. Tuttavia, la `BindableObject` classe è elevata nella gerarchia di classi, quindi la maggior parte delle classi usate per le funzionalità dell'interfaccia utente supporta le proprietà associabili.

È possibile creare una proprietà associabile dichiarando una `public static readonly` proprietà di tipo [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) . È necessario impostare la proprietà associabile sul valore restituito di uno degli [ `BindableProperty.Create` ] (xrif: Xamarin.Forms . BindableProperty. Create (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . BindableProperty. ValidateValueDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangedDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangingDelegate, Xamarin.Forms . BindableProperty. CoerceValueDelegate, Xamarin.Forms . BindableProperty. CreateDefaultValueDelegate)) overload del metodo. La dichiarazione deve trovarsi all'interno del corpo della [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe derivata, ma all'esterno delle definizioni dei membri.

Quando si crea un oggetto, è necessario specificare almeno un identificatore [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) insieme ai parametri seguenti:

- Nome dell'oggetto [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) .
- Tipo della proprietà.
- Tipo dell'oggetto proprietario.
- Il valore predefinito per la proprietà. Ciò garantisce che la proprietà restituisca sempre un valore predefinito particolare quando viene annullato e può essere diverso dal valore predefinito per il tipo della proprietà. Il valore predefinito verrà ripristinato quando [ `ClearValue` ] (xrif: Xamarin.Forms . BindableObject. ClearValue ( Xamarin.Forms . BindableProperty)) il metodo viene chiamato sulla proprietà associabile.

Il codice seguente illustra un esempio di proprietà associabile, con un identificatore e i valori per i quattro parametri obbligatori:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

Verrà creata un' [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) istanza denominata `EventName` , di tipo `string` . La proprietà è di proprietà della `EventToCommandBehavior` classe e il valore predefinito è `null` . La convenzione di denominazione per le proprietà associabili è che l'identificatore di proprietà associabile deve corrispondere al nome della proprietà specificato nel `Create` metodo, con l'aggiunta di "Property". Nell'esempio precedente, quindi, l'identificatore di proprietà associabile è `EventNameProperty` .

Facoltativamente, quando si crea un' [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) istanza di, è possibile specificare i parametri seguenti:

- La modalità di associazione. Viene usato per specificare la direzione di propagazione delle modifiche del valore della proprietà. Nella modalità di associazione predefinita le modifiche vengono propagate dall' *origine* alla *destinazione*.
- Delegato di convalida che verrà richiamato quando viene impostato il valore della proprietà. Per altre informazioni, vedere [callback di convalida](#validation-callbacks).
- Delegato modificato da una proprietà che verrà richiamato quando viene modificato il valore della proprietà. Per altre informazioni, vedere [rilevare le modifiche alle proprietà](#detect-property-changes).
- Delegato che modifica la proprietà che verrà richiamato quando il valore della proprietà verrà modificato. Questo delegato ha la stessa firma del delegato modificato della proprietà.
- Delegato del valore di coercizione che verrà richiamato quando viene modificato il valore della proprietà. Per altre informazioni, vedere [coercizione di valori callback](#coerce-value-callbacks).
- Oggetto `Func` utilizzato per inizializzare un valore di proprietà predefinito. Per ulteriori informazioni, vedere la pagina relativa alla [creazione di un valore predefinito con una funzione Func](#create-a-default-value-with-a-func).

### <a name="create-accessors"></a>Crea funzioni di accesso

Le funzioni di accesso alle proprietà sono necessarie per usare la sintassi delle proprietà per accedere a una proprietà associabile. La `Get` funzione di accesso deve restituire il valore contenuto nella proprietà associabile corrispondente. Questa operazione può essere eseguita chiamando il [ `GetValue` ] (xrif: Xamarin.Forms . BindableObject. GetValue ( Xamarin.Forms . BindableProperty)), passando l'identificatore di proprietà associabile sul quale ottenere il valore e quindi eseguendo il cast del risultato al tipo richiesto. La `Set` funzione di accesso deve impostare il valore della proprietà associabile corrispondente. Questa operazione può essere eseguita chiamando il [ `SetValue` ] (xrif: Xamarin.Forms . BindableObject. SetValue ( Xamarin.Forms . Metodo BindableProperty, System. Object)), passando l'identificatore di proprietà associabile su cui impostare il valore e il valore da impostare.

Nell'esempio di codice seguente vengono illustrate le funzioni di accesso per la `EventName` proprietà associabile:

```csharp
public string EventName
{
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

## <a name="consume-a-bindable-property"></a>Utilizzare una proprietà associabile

Una volta creata, una proprietà associabile può essere utilizzata da XAML o dal codice. In XAML, questa operazione viene eseguita dichiarando uno spazio dei nomi con un prefisso, con la dichiarazione dello spazio dei nomi che indica il nome dello spazio dei nomi CLR e, facoltativamente, un nome di assembly. Per altre informazioni, vedere [spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md).

Nell'esempio di codice riportato di seguito viene illustrato uno spazio dei nomi XAML per un tipo personalizzato che contiene una proprietà associabile, definita all'interno dello stesso assembly del codice dell'applicazione che fa riferimento al tipo personalizzato:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

La dichiarazione dello spazio dei nomi viene usata quando si imposta la `EventName` proprietà associabile, come illustrato nell'esempio di codice XAML seguente:

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

Quando si crea un' [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) istanza di, sono disponibili diversi parametri facoltativi che possono essere impostati per abilitare gli scenari di proprietà associabili avanzate. In questa sezione vengono esaminati questi scenari.

### <a name="detect-property-changes"></a>Rilevare le modifiche alle proprietà

Un `static` metodo di callback modificato da proprietà può essere registrato con una proprietà associabile specificando il `propertyChanged` parametro per [ `BindableProperty.Create` ] (xrif: Xamarin.Forms . BindableProperty. Create (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . BindableProperty. ValidateValueDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangedDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangingDelegate, Xamarin.Forms . BindableProperty. CoerceValueDelegate, Xamarin.Forms . Metodo BindableProperty. CreateDefaultValueDelegate). Il metodo di callback specificato verrà richiamato quando il valore della proprietà associabile viene modificato.

Nell'esempio di codice seguente viene illustrato il modo `EventName` in cui la proprietà associabile registra il `OnEventNameChanged` metodo come metodo di callback modificato da una proprietà:

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

Nel metodo di callback modificato da proprietà, il [`BindableObject`](xref:Xamarin.Forms.BindableObject) parametro viene utilizzato per indicare quale istanza della classe proprietaria ha segnalato una modifica e i valori dei due `object` parametri rappresentano i valori obsoleti e nuovi della proprietà associabile.

### <a name="validation-callbacks"></a>Callback di convalida

Un `static` metodo di callback di convalida può essere registrato con una proprietà associabile specificando il `validateValue` parametro per [ `BindableProperty.Create` ] (xrif: Xamarin.Forms . BindableProperty. Create (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . BindableProperty. ValidateValueDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangedDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangingDelegate, Xamarin.Forms . BindableProperty. CoerceValueDelegate, Xamarin.Forms . Metodo BindableProperty. CreateDefaultValueDelegate). Il metodo di callback specificato verrà richiamato quando viene impostato il valore della proprietà associabile.

Nell'esempio di codice seguente viene illustrato il modo `Angle` in cui la proprietà associabile registra il `IsValidValue` metodo come metodo di callback di convalida:

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

I callback di convalida vengono forniti con un valore e devono restituire `true` se il valore è valido per la proprietà; in caso contrario, `false` . Se viene restituito un callback di convalida, verrà generata un'eccezione `false` che deve essere gestita dallo sviluppatore. Un metodo di callback di convalida viene usato in genere per vincolare i valori di numeri interi o doppi quando la proprietà associabile è impostata. Il metodo, ad esempio, `IsValidValue` Verifica che il valore della proprietà sia `double` compreso tra 0 e 360.

### <a name="coerce-value-callbacks"></a>Assegnare i callback di valore

Un `static` metodo di callback del valore di coercizione può essere registrato con una proprietà associabile specificando il `coerceValue` parametro per [ `BindableProperty.Create` ] (xrif: Xamarin.Forms . BindableProperty. Create (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . BindableProperty. ValidateValueDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangedDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangingDelegate, Xamarin.Forms . BindableProperty. CoerceValueDelegate, Xamarin.Forms . Metodo BindableProperty. CreateDefaultValueDelegate). Il metodo di callback specificato verrà richiamato quando il valore della proprietà associabile viene modificato.

> [!IMPORTANT]
> Il `BindableObject` tipo dispone di un `CoerceValue` metodo che può essere chiamato per forzare una rivalutazione del valore del relativo `BindableProperty` argomento richiamando il callback del valore di coercizione.

I callback di valori con coercizione vengono usati per forzare una rivalutazione di una proprietà associabile quando cambia il valore della proprietà. È ad esempio possibile utilizzare un callback di valori con coercizione per garantire che il valore di una proprietà associabile non sia maggiore del valore di un'altra proprietà associabile.

Nell'esempio di codice seguente viene illustrato il modo `Angle` in cui la proprietà associabile registra il `CoerceAngle` metodo come metodo di callback del valore di coercizione:

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

Il `CoerceAngle` metodo controlla il valore della `MaximumAngle` proprietà e, se il `Angle` valore della proprietà è maggiore di esso, assegna il valore al valore della `MaximumAngle` Proprietà. Inoltre, quando la `MaximumAngle` proprietà viene modificata, il callback del valore di coercizione viene richiamato sulla `Angle` proprietà chiamando il `CoerceValue` metodo.

### <a name="create-a-default-value-with-a-func"></a>Crea un valore predefinito con un Func

Un oggetto `Func` può essere utilizzato per inizializzare il valore predefinito di una proprietà associabile, come illustrato nell'esempio di codice seguente:

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

Il `defaultValueCreator` parametro è impostato su un oggetto `Func` che richiama [ `Device.GetNamedSize` ] (xrif: Xamarin.Forms . Device. GetNamedSize ( Xamarin.Forms . Metodo NamedSize, System. Type)) per restituire un oggetto `double` che rappresenta le dimensioni denominate per il tipo di carattere utilizzato su un oggetto [`Label`](xref:Xamarin.Forms.Label) nella piattaforma nativa.

## <a name="related-links"></a>Collegamenti correlati

- [Spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md)
- [Comportamento da evento a comando (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Callback di convalida (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-validationcallback)
- [Callback di valori con coercizione (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-coercevaluecallback)
- [API BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [API BindableObject](xref:Xamarin.Forms.BindableObject)
