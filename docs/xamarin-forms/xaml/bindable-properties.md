---
title: Proprietà associabili
description: Questo articolo viene fornita un'introduzione alle proprietà associabile e viene illustrato come creare e il loro uso.
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
ms.openlocfilehash: 50efd6b37d70fa835436c28c73b3d4f9fc6c7c83
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758100"
---
# <a name="bindable-properties"></a>Proprietà associabili

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

_In xamarin. Forms, la funzionalità delle proprietà di common language runtime (CLR) viene estesa dalla proprietà associabili. Una proprietà associabile è un tipo speciale di proprietà, in cui il valore della proprietà viene rilevato dal sistema di proprietà di xamarin. Forms. Questo articolo viene fornita un'introduzione alle proprietà associabile e viene illustrato come creare e il loro uso._

## <a name="overview"></a>Panoramica

Proprietà associabili estendere la funzionalità delle proprietà CLR eseguendo il backup di una proprietà con un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) tipo, invece di eseguire il backup di una proprietà con un campo. Lo scopo di proprietà associabili consiste nel fornire un sistema di proprietà che supporta l'associazione dati, stili, modelli e i valori impostati tramite relazioni padre-figlio. Inoltre, le proprietà associabili possono fornire valori predefiniti, convalida dei valori delle proprietà e i callback che consentono di monitorare le modifiche alle proprietà.

Le proprietà devono essere implementate come proprietà associabile per supportare uno o più delle seguenti funzionalità:

- Funge da un oggetto valido *destinazione* proprietà per il data binding.
- Impostazione della proprietà tramite un [stile](~/xamarin-forms/user-interface/styles/index.md).
- Fornire un valore di proprietà predefinite che è diverso da quello predefinito per il tipo della proprietà.
- La convalida del valore della proprietà.
- Monitoraggio delle modifiche di proprietà.

Esempi di xamarin. Forms proprietà associabili [ `Label.Text` ](xref:Xamarin.Forms.Label.Text), [ `Button.BorderRadius` ](xref:Xamarin.Forms.Button.BorderRadius), e [ `StackLayout.Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation). Ogni proprietà associabili ha un corrispondente `public static readonly` vlastnosti typu [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) esposto nella stessa classe e che è l'identificatore della proprietà associabile. Ad esempio, l'identificatore della proprietà associabile corrispondente per il `Label.Text` proprietà viene [ `Label.TextProperty` ](xref:Xamarin.Forms.Label.TextProperty).

<a name="consuming-bindable-property" />

## <a name="creating-and-consuming-a-bindable-property"></a>Creazione e l'utilizzo di una proprietà associabile

Il processo per la creazione di una proprietà associabile è come segue:

1. Creare un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) istanza con uno del [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create*) overload del metodo.
1. Definire le funzioni di accesso di proprietà per il [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) istanza.

Si noti che tutti i [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) istanze devono essere create nel thread UI. Ciò significa che solo il codice viene eseguito sul thread dell'interfaccia utente può ottenere o impostare il valore di una proprietà associabile. Tuttavia `BindableProperty` istanze sono accessibili da altri thread effettuando il marshalling al thread UI con la [ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) (metodo).

### <a name="creating-a-property"></a>Creazione di una proprietà

Per creare un `BindableProperty` istanza, la classe contenitore deve derivare dal [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) classe. Tuttavia, il `BindableObject` classe è elevata nella gerarchia delle classi, in modo che la maggior parte delle classi usato per la proprietà associabile supporto funzionalità dell'interfaccia utente.

Può creare una proprietà associabile dichiarando una `public static readonly` vlastnosti typu [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty). Le proprietà associabili deve essere impostata sul valore restituito di una delle [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) overload del metodo. La dichiarazione deve essere all'interno del corpo della [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) derivato (classe), ma di fuori di qualsiasi definizione del membro.

Come minimo, un identificatore deve essere specificato durante la creazione di un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty), insieme ai parametri seguenti:

- Il nome del [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty).
- Tipo della proprietà.
- Il tipo dell'oggetto.
- Il valore predefinito per la proprietà. Ciò garantisce che la proprietà restituisce sempre un valore predefinito specifico quando è impostato e può essere diverso da quello predefinito per il tipo della proprietà. Il valore predefinito sarà ripristinato quando la [ `ClearValue` ](xref:Xamarin.Forms.BindableObject.ClearValue(Xamarin.Forms.BindableProperty)) viene chiamato sulla proprietà associabili.

Il codice seguente viene illustrato un esempio di una proprietà associabile, con un identificatore e i valori per i quattro parametri obbligatori:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

Ciò consente di creare un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) istanza denominata `EventName`, di tipo `string`. La proprietà è di proprietà di `EventToCommandBehavior` classi e ha un valore predefinito di `null`. La convenzione di denominazione per le proprietà associabili è che l'identificatore di proprietà associabili deve corrispondere al nome di proprietà specificato nella `Create` con "Property" aggiunto a tale metodo. Pertanto, nell'esempio precedente, è l'identificatore della proprietà associabile `EventNameProperty`.

Facoltativamente, durante la creazione di un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) dell'istanza, di seguito è possibile specificare parametri:

- La modalità di associazione. Ciò consente di specificare la direzione in cui verranno propagato modifiche dei valori di proprietà. Nella modalità di associazione predefinita, le modifiche si propaghino dal *origine* per il *destinazione*.
- Un delegato di convalida che verrà richiamato quando il valore della proprietà è impostato. Per altre informazioni, vedere [i callback di convalida](#validation).
- Delegato che verrà richiamato quando è stato modificato il valore della proprietà modificata una proprietà. Per altre informazioni, vedere [rilevamento di modifiche alle proprietà](#propertychanges).
- Una proprietà modifica delegato che verrà richiamato quando il valore della proprietà verrà modificato. Questo delegato ha la stessa firma del delegato di proprietà modificata.
- Un delegato di valore soggetti a coercizione che verrà richiamato quando è stato modificato il valore della proprietà. Per altre informazioni, vedere [i callback di valori soggetti a coercizione](#coerce).
- Oggetto `Func` che viene usato per inizializzare un valore di proprietà predefinito. Per altre informazioni, vedere [creazione di un valore predefinito con un Func](#defaultfunc).

### <a name="creating-accessors"></a>Creazione di funzioni di accesso

Le funzioni di accesso di proprietà sono necessarie per usare la sintassi di proprietà per accedere a una proprietà associabile. Il `Get` della funzione di accesso deve restituire il valore contenuto nella proprietà associabili corrispondente. Ciò può essere ottenuto chiamando il [ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) (metodo), passando l'identificatore di proprietà associabili su cui ottenere il valore e quindi esegue il cast del risultato per il tipo richiesto. Il `Set` della funzione di accesso deve impostare il valore della proprietà associabile corrispondente. Ciò può essere ottenuto chiamando il [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) metodo, passando l'identificatore della proprietà associabile per cui impostare il valore e il valore da impostare.

Esempio di codice seguente mostra le funzioni di accesso per il `EventName` proprietà associabili:

```csharp
public string EventName {
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

### <a name="consuming-a-bindable-property"></a>Utilizzo di una proprietà associabile

Dopo aver creata una proprietà associabile, può essere utilizzato dal codice o XAML. In XAML, ciò si ottiene con la dichiarazione di uno spazio dei nomi con un prefisso con la dichiarazione dello spazio dei nomi che indica il nome dello spazio dei nomi CLR e, facoltativamente, un nome di assembly. Per altre informazioni, vedere [spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md).

L'esempio di codice seguente illustra uno spazio dei nomi XAML per un tipo personalizzato che contiene una proprietà associabile, che viene definita nello stesso assembly del codice dell'applicazione che fa riferimento al tipo personalizzato:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

La dichiarazione dello spazio dei nomi viene usata quando si impostano le `EventName` proprietà associabile, come illustrato nell'esempio di codice XAML seguente:

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
listView.Behaviors.Add (new EventToCommandBehavior {
  EventName = "ItemSelected",
  ...
});
```

<a name="advanced" />

## <a name="advanced-scenarios"></a>Scenari avanzati

Quando si crea una [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) dell'istanza, esistono una serie di parametri facoltativi che è possibile impostare per abilitare scenari avanzati di proprietà associabili. Questa sezione vengono illustrati questi scenari.

<a name="propertychanges" />

### <a name="detecting-property-changes"></a>Rilevamento delle modifiche di proprietà

Oggetto `static` metodo di callback di modifica della proprietà può essere registrata con una proprietà associabile, specificando le `propertyChanged` parametro per il [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) (metodo). Verrà richiamato il metodo di callback specificato quando cambia il valore della proprietà associabile.

Nell'esempio di codice riportato di seguito viene illustrato come la `EventName` registri di proprietà associabili il `OnEventNameChanged` metodo come metodo di callback di modifica delle proprietà:

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

Nel metodo di callback di modifica delle proprietà, il [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) parametro viene usato per indicare quale istanza della classe di appartenenza ha segnalato una modifica e i valori dei due `object` parametri rappresentano i valori vecchi e nuovi di la proprietà associabile.

<a name="validation" />

### <a name="validation-callbacks"></a>Callback di convalida

Oggetto `static` metodo di callback di convalida può essere registrata con una proprietà associabile, specificando le `validateValue` parametro per il [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) (metodo). Verrà richiamato il metodo di callback specificato quando è impostato il valore della proprietà associabile.

Nell'esempio di codice riportato di seguito viene illustrato come la `Angle` registri di proprietà associabili il `IsValidValue` metodo come metodo di callback di convalida:

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

I callback di convalida vengono forniti con un valore e deve restituire `true` se il valore è valido per la proprietà, in caso contrario `false`. Verrà generata un'eccezione se un callback di convalida restituisce `false`, che deve essere gestita dallo sviluppatore. Un tipico utilizzo di un metodo di callback di convalida è vincolare i valori di integer o Double quando è impostata la proprietà associabile. Ad esempio, il `IsValidValue` metodo controlla che il valore della proprietà è un `double` all'interno dell'intervallo da 0 a 360.

<a name="coerce" />

### <a name="coerce-value-callbacks"></a>I callback di valori soggetti a coercizione

Oggetto `static` valori soggetti a coercizione metodo di callback può essere registrata con una proprietà associabile, specificando le `coerceValue` parametro per il [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) (metodo). Verrà richiamato il metodo di callback specificato quando cambia il valore della proprietà associabile.

Valore di callback vengono usati per forzare una rivalutazione di una proprietà associabile quando cambia il valore della proprietà vengono assegnati forzatamente. Ad esempio, un callback di soggetti a coercizione del valore è utilizzabile per assicurarsi che il valore di una proprietà associabile non sia maggiore del valore di un'altra proprietà associabile.

Nell'esempio di codice riportato di seguito viene illustrato come la `Angle` registra la proprietà associabile il `CoerceAngle` metodo come metodo di callback soggetti a coercizione valore:

```csharp
public static readonly BindableProperty AngleProperty = BindableProperty.Create (
  "Angle", typeof(double), typeof(HomePage), 0.0, coerceValue: CoerceAngle);
public static readonly BindableProperty MaximumAngleProperty = BindableProperty.Create (
  "MaximumAngle", typeof(double), typeof(HomePage), 360.0);
...

static object CoerceAngle (BindableObject bindable, object value)
{
  var homePage = bindable as HomePage;
  double input = (double)value;

  if (input > homePage.MaximumAngle) {
    input = homePage.MaximumAngle;
  }

  return input;
}
```

Il `CoerceAngle` metodo controlla il valore della `MaximumAngle` proprietà e, se il `Angle` è maggiore rispetto al valore della proprietà, assegna il valore per il `MaximumAngle` valore della proprietà.

<a name="defaultfunc" />

### <a name="creating-a-default-value-with-a-func"></a>Creazione di un valore predefinito con una funzione

Oggetto `Func` può essere utilizzato per inizializzare il valore predefinito di una proprietà associabile, come illustrato nell'esempio di codice seguente:

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

Il `defaultValueCreator` parametro è impostato su un `Func` che richiama la [ `Device.GetNamedSize` ](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) metodo restituisca un `double` che rappresenta la dimensione denominata per il tipo di carattere utilizzato in un [ `Label` ](xref:Xamarin.Forms.Label) sulla piattaforma nativa.

## <a name="summary"></a>Riepilogo

In questo articolo ha presentato proprietà associabili ed è stato illustrato come creare e il loro uso. Una proprietà associabile è un tipo speciale di proprietà, in cui il valore della proprietà viene rilevato dal sistema di proprietà di xamarin. Forms.

## <a name="related-links"></a>Collegamenti correlati

- [Spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md)
- [Evento al comportamento del comando (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Callback di convalida (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-validationcallback)
- [Forzare il Callback di valore (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-coercevaluecallback)
- [BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [BindableObject](xref:Xamarin.Forms.BindableObject)
