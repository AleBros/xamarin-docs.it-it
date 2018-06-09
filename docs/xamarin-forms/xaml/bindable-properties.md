---
title: Proprietà associabili
description: In questo articolo viene fornita un'introduzione alle proprietà associabile e viene illustrato come creare e utilizzarli.
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/02/2016
ms.openlocfilehash: 5e39e8eb3d7ffb3ed33ea2a585d8d367302e9baa
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245976"
---
# <a name="bindable-properties"></a>Proprietà associabili

_In xamarin. Forms, viene estesa la funzionalità delle proprietà di common language runtime (CLR) dalle proprietà associabile. Una proprietà associabile è un tipo speciale di proprietà, in cui il valore della proprietà viene rilevato dal sistema di proprietà di xamarin. Forms. In questo articolo viene fornita un'introduzione alle proprietà associabile e viene illustrato come creare e utilizzarli._

## <a name="overview"></a>Panoramica

Proprietà associabili estendono le funzionalità delle proprietà CLR eseguendo il backup di una proprietà con un [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) tipo, anziché eseguire il backup di una proprietà con un campo. Lo scopo di proprietà associabili consiste nel fornire un sistema di proprietà che supporta l'associazione dati, stili, modelli e i valori impostati tramite relazioni padre-figlio. Inoltre, proprietà associabili possono fornire i valori predefiniti, la convalida dei valori di proprietà e i callback che consentono di monitorare le modifiche alle proprietà.

Proprietà devono essere implementate come proprietà associabile per supportare uno o più delle seguenti funzionalità:

- Funge da valido *destinazione* proprietà per l'associazione dati.
- L'impostazione della proprietà tramite un [stile](~/xamarin-forms/user-interface/styles/index.md).
- Fornire un valore di proprietà predefinito che è diverso da quello predefinito per il tipo della proprietà.
- Convalida del valore della proprietà.
- Monitoraggio delle modifiche di proprietà.

Esempi di proprietà associabili xamarin. Forms [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/), [ `Button.BorderRadius` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderRadius/), e [ `StackLayout.Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Orientation/). Ciascuna proprietà associabile con un corrispondente `public static readonly` proprietà di tipo [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) esposto nella stessa classe e che è l'identificatore della proprietà associabile. Ad esempio, l'identificatore di proprietà associabile corrispondente per il `Label.Text` proprietà [ `Label.TextProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Label.TextProperty/).

<a name="consuming-bindable-property" />

## <a name="creating-and-consuming-a-bindable-property"></a>Creazione e utilizzo di una proprietà associabile

Il processo per la creazione di una proprietà associabile è come segue:

1. Creare un [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) istanza con uno del [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) overload del metodo.
1. Funzioni di accesso di proprietà per definire il [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) istanza.

Si noti che tutti i [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) sul thread dell'interfaccia utente, è necessario creare istanze. Ciò significa che solo il codice che viene eseguito nel thread UI per ottenere o impostare il valore di una proprietà associabile. Tuttavia, `BindableProperty` istanze è possibile accedere da altri thread effettuando il marshalling al thread dell'interfaccia utente con il [ `Device.BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/) metodo.

### <a name="creating-a-property"></a>Creazione di una proprietà

Per creare un `BindableProperty` istanza, tale classe deve derivare dal [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) classe. Tuttavia, la `BindableObject` classe è elevata nella gerarchia delle classi, pertanto la maggior parte delle classi utilizzate per la proprietà associabile supporto funzionalità dell'interfaccia utente.

Una proprietà associabile può essere creata dichiarando un `public static readonly` proprietà di tipo [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/). La proprietà associabile deve essere impostata sul valore restituito di uno del [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) overload del metodo. La dichiarazione deve essere all'interno del corpo di [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) derivata, ma di fuori di qualsiasi definizione del membro.

Come minimo, è necessario specificare un identificatore quando si crea un [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/), con i parametri seguenti:

- Il nome del [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/).
- Tipo della proprietà.
- Il tipo di oggetto proprietario.
- Il valore predefinito per la proprietà. Ciò garantisce che la proprietà restituisce sempre un valore predefinito specifico quando è impostato e può essere diverso da quello predefinito per il tipo della proprietà. Il valore predefinito sarà ripristinato quando la [ `ClearValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.ClearValue/p/Xamarin.Forms.BindableProperty/) metodo viene chiamato sulla proprietà associabile.

Il codice seguente viene illustrato un esempio di una proprietà associabile, con un identificatore e i valori per i quattro parametri obbligatori:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

Crea un [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) istanza denominata `EventName`, di tipo `string`. La proprietà è di proprietà di `EventToCommandBehavior` classe e ha un valore predefinito di `null`. La convenzione di denominazione per le proprietà associabili è che l'identificatore della proprietà associabile deve corrispondere al nome di proprietà specificato nella `Create` con "Property" aggiunto a tale metodo. Nell'esempio precedente, pertanto, l'identificatore della proprietà associabile è `EventNameProperty`.

Facoltativamente, quando si crea un [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) istanza, le operazioni seguenti è possibile specificare parametri:

- La modalità di associazione. Viene utilizzato per specificare la direzione in cui verranno propagate le modifiche di valore della proprietà. Nella modalità di associazione predefinita, le modifiche verranno propagate dal *origine* per il *destinazione*.
- Un delegato di convalida che verrà richiamato quando il valore della proprietà è impostato. Per ulteriori informazioni, vedere [i callback di convalida](#validation).
- Delegato che verrà richiamato quando viene modificato il valore della proprietà modificata una proprietà. Per ulteriori informazioni, vedere [il rilevamento delle modifiche alle proprietà](#propertychanges).
- Una proprietà modifica delegato che verrà richiamato quando il valore della proprietà verrà modificato. Questo delegato è la stessa firma del delegato di proprietà modificata.
- Un delegato di valore soggetto a coercizione che verrà richiamato quando viene modificato il valore della proprietà. Per ulteriori informazioni, vedere [assegnare valore callback](#coerce).
- Oggetto `Func` che viene utilizzato per inizializzare un valore di proprietà predefinito. Per ulteriori informazioni, vedere [creazione di un valore predefinito con un oggetto Func](#defaultfunc).

### <a name="creating-accessors"></a>Creazione di funzioni di accesso

Funzioni di accesso a proprietà sono necessarie per utilizzare la sintassi di proprietà per accedere a una proprietà associabile. Il `Get` della funzione di accesso deve restituire il valore contenuto nella proprietà associabile corrispondente. Questo può essere ottenuto chiamando il [ `GetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.GetValue/p/Xamarin.Forms.BindableProperty/) (metodo), passando l'identificatore della proprietà associabili su cui ottenere il valore e quindi il cast del risultato nel tipo richiesto. Il `Set` della funzione di accesso deve impostare il valore della proprietà associabile corrispondente. Questo può essere ottenuto chiamando il [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) , passando nell'identificatore della proprietà associabili su cui impostare il valore e il valore da impostare.

Esempio di codice seguente mostra le funzioni di accesso per il `EventName` proprietà associabile:

```csharp
public string EventName {
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

### <a name="consuming-a-bindable-property"></a>Utilizzo di una proprietà associabile

Dopo aver creata una proprietà associabile, possono essere utilizzato da XAML o nel codice. In XAML, infatti, la dichiarazione di uno spazio dei nomi con un prefisso, con la dichiarazione dello spazio dei nomi che indica il nome dello spazio dei nomi CLR e, facoltativamente, un nome di assembly. Per ulteriori informazioni, vedere [spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md).

L'esempio di codice seguente illustra uno spazio dei nomi XAML per un tipo personalizzato che contiene una proprietà associabile, che viene definita nello stesso assembly del codice dell'applicazione che fa riferimento al tipo personalizzato:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

La dichiarazione dello spazio dei nomi viene utilizzata durante l'impostazione di `EventName` proprietà associabile, come illustrato nell'esempio di codice XAML seguente:

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

Quando si crea un [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) dell'istanza, esistono numerosi parametri facoltativi che è possibile impostare per abilitare scenari avanzati proprietà associabile. Questa sezione vengono illustrati questi scenari.

<a name="propertychanges" />

### <a name="detecting-property-changes"></a>Il rilevamento delle modifiche di proprietà

A `static` metodo di callback di modifica della proprietà può essere registrato con una proprietà associabile, specificando il `propertyChanged` parametro per il [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) metodo. Verrà richiamato il metodo di callback specificato quando cambia il valore della proprietà associabile.

Nell'esempio di codice riportato di seguito viene illustrato come la `EventName` proprietà associabile registri il `OnEventNameChanged` metodo come metodo di callback di modifica della proprietà:

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

Nel metodo di callback di modifica della proprietà, il [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) parametro viene utilizzato per indicare quale istanza della classe di appartenenza ha segnalato una modifica e i valori dei due `object` parametri rappresentano i valori vecchi e nuovi di la proprietà associabile.

<a name="validation" />

### <a name="validation-callbacks"></a>Callback di convalida

A `static` il metodo di callback di convalida può essere registrato con una proprietà associabile, specificando il `validateValue` parametro per il [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) metodo. Viene richiamato il metodo di callback specificato quando è impostato il valore della proprietà associabile.

Nell'esempio di codice riportato di seguito viene illustrato come la `Angle` proprietà associabile registri il `IsValidValue` metodo come metodo di callback di convalida:

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

I callback di convalida vengono forniti con un valore e deve restituire `true` se il valore è valido per la proprietà, in caso contrario `false`. Verrà generata un'eccezione se un callback di convalida restituisce `false`, che deve essere gestita dallo sviluppatore. Un tipico utilizzo di un metodo di callback di convalida è vincolare i valori di integer o Double quando è impostata la proprietà associabile. Ad esempio, il `IsValidValue` metodo verifica che il valore della proprietà sia una `double` all'interno dell'intervallo da 0 a 360.

<a name="coerce" />

### <a name="coerce-value-callbacks"></a>Assegnare i callback di valori

A `static` assegnare valore metodo di callback può essere registrata con una proprietà associabile, specificando il `coerceValue` parametro per il [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) metodo. Verrà richiamato il metodo di callback specificato quando cambia il valore della proprietà associabile.

Assegnare i callback vengono utilizzati per forzare una rivalutazione di una proprietà associabile quando cambia il valore della proprietà di valore. Ad esempio, un callback di valore soggetto a coercizione può essere usato per garantire che il valore di una proprietà associabile non è maggiore del valore di un'altra proprietà associabile.

Nell'esempio di codice riportato di seguito viene illustrato come la `Angle` proprietà associabile registri il `CoerceAngle` metodo come metodo di callback del valore soggetto a coercizione:

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

Il `CoerceAngle` metodo controlla il valore del `MaximumAngle` , proprietà e, se il `Angle` è maggiore rispetto al valore della proprietà, assegna il valore per il `MaximumAngle` valore della proprietà.

<a name="defaultfunc" />

### <a name="creating-a-default-value-with-a-func"></a>Creazione di un valore predefinito con un oggetto Func

Oggetto `Func` può essere utilizzato per inizializzare il valore predefinito di una proprietà associabile, come illustrato nell'esempio di codice seguente:

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

Il `defaultValueCreator` parametro è impostato su un `Func` che richiama la [ `Device.GetNamedSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/System.Type/) per restituire un `double` che rappresenta la dimensione denominata per il tipo di carattere utilizzato in un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) sulla piattaforma nativa.

## <a name="summary"></a>Riepilogo

In questo articolo fornita un'introduzione alle proprietà associabili e illustrato come creare e utilizzarli. Una proprietà associabile è un tipo speciale di proprietà, in cui il valore della proprietà viene rilevato dal sistema di proprietà di xamarin. Forms.


## <a name="related-links"></a>Collegamenti correlati

- [Spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md)
- [Evento di comportamento del comando (esempio)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [Callback di convalida (esempio)](https://developer.xamarin.com/samples/xamarin-forms/xaml/validationcallback/)
- [Valore di Callback (esempio) vengono assegnati forzatamente](https://developer.xamarin.com/samples/xamarin-forms/xaml/coercevaluecallback/)
- [BindableProperty](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)
- [BindableObject](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)
