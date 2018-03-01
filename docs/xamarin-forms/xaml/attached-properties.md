---
title: "Proprietà associate"
description: "Una proprietà associata è un tipo speciale di proprietà associabile, definito in una classe ma collegato ad altri oggetti e riconoscibile in XAML come un attributo che contiene una classe e un nome di proprietà separati da un punto. In questo articolo viene fornita un'introduzione alle proprietà associata e viene illustrato come creare e utilizzarli."
ms.topic: article
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/02/2016
ms.openlocfilehash: 7112812c843ccbcd6c24ea028deae3c09851b03d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="attached-properties"></a>Proprietà associate

_Una proprietà associata è un tipo speciale di proprietà associabile, definito in una classe ma collegato ad altri oggetti e riconoscibile in XAML come un attributo che contiene una classe e un nome di proprietà separati da un punto. In questo articolo viene fornita un'introduzione alle proprietà associata e viene illustrato come creare e utilizzarli._

## <a name="overview"></a>Panoramica

Allegato consentono un oggetto per assegnare un valore per una proprietà che non definisce la relativa classe. Ad esempio, possono utilizzare gli elementi figlio associate per informare dell'elemento padre di come devono essere presentati nell'interfaccia utente. Il [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) controllo consente di riga e della colonna di un elemento figlio di essere specificato impostando il `Grid.Row` e `Grid.Column` le proprietà associate. `Grid.Row` e `Grid.Column` sono le proprietà associate, perché vengono impostate per gli elementi figlio di un `Grid`, anziché sul `Grid` stesso.

Proprietà associabili devono essere implementate come proprietà associate negli scenari seguenti:

- La definizione di classe quando è necessario disporre di un meccanismo di impostazione proprietà disponibile, per le classi diverso.
- Quando la classe rappresenta un servizio che deve essere facilmente integrato con altre classi.

Per ulteriori informazioni sulle proprietà associabile, vedere [proprietà associabili](~/xamarin-forms/xaml/bindable-properties.md).

## <a name="creating-and-consuming-an-attached-property"></a>Creazione e utilizzo di una proprietà associata

Il processo per la creazione di una proprietà associata è come segue:

1. Creare un [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) istanza con uno del [ `CreateAttached` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateAttached/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) overload del metodo.
1. Fornire `static` `Get` *PropertyName* e `Set` *PropertyName* metodi come funzioni di accesso per la proprietà associata.

### <a name="creating-a-property"></a>Creazione di una proprietà

Quando si crea una proprietà associata per l'utilizzo in altri tipi, la classe in cui viene creata la proprietà non è necessario derivare da [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/). Tuttavia, il *destinazione* proprietà per le funzioni di accesso deve essere o derivare da, [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/).

Una proprietà associata può essere creata dichiarando un `public static readonly` proprietà di tipo [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/). La proprietà associabile deve essere impostata sul valore restituito di uno del [ `BindableProperty.CreateAttached` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateAttached/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) overload del metodo. La dichiarazione deve essere all'interno del corpo della classe di appartenenza, ma di fuori di qualsiasi definizione del membro.

Il codice seguente viene illustrato un esempio di una proprietà associata:

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

Verrà creata una proprietà associata denominata `HasShadow`, di tipo `bool`. La proprietà è di proprietà di `ShadowEffect` classe e ha un valore predefinito di `false`. La convenzione di denominazione per le proprietà associate è che l'identificatore della proprietà associata deve corrispondere al nome di proprietà specificato nella `CreateAttached` con "Property" aggiunto a tale metodo. Nell'esempio precedente, pertanto, l'identificatore della proprietà associata è `HasShadowProperty`.

Per ulteriori informazioni sulla creazione di proprietà associabile, inclusi i parametri che possono essere specificati durante la creazione, vedere [creazione e utilizzo di una proprietà associabile](~/xamarin-forms/xaml/bindable-properties.md#consuming-bindable-property).

### <a name="creating-accessors"></a>Creazione di funzioni di accesso

Statico `Get` *PropertyName* e `Set` *PropertyName* metodi sono necessari le funzioni di accesso per la proprietà associata, in caso contrario il sistema di proprietà sarà in grado di utilizzare il proprietà associata. Il `Get` *PropertyName* della funzione di accesso devono essere conformi alla firma seguente:

```csharp
public static valueType GetPropertyName(BindableObject target)
```

Il `Get` *PropertyName* della funzione di accesso deve restituire il valore contenuto nel corrispondente `BindableProperty` campo per la proprietà associata. Questo può essere ottenuto chiamando il [ `GetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.GetValue/p/Xamarin.Forms.BindableProperty/) (metodo), passando l'identificatore della proprietà associabili su cui ottenere il valore e quindi eseguire il cast il valore risultante nel tipo richiesto.

Il `Set` *PropertyName* della funzione di accesso devono essere conformi alla firma seguente:

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

Il `Set` *PropertyName* della funzione di accesso deve impostare il valore dell'oggetto corrispondente `BindableProperty` campo per la proprietà associata. Questo può essere ottenuto chiamando il [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) , passando nell'identificatore della proprietà associabili su cui impostare il valore e il valore da impostare.

Per entrambe le funzioni di accesso, il *destinazione* oggetto deve essere o derivare da, [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/).

Esempio di codice seguente mostra le funzioni di accesso per il `HasShadow` proprietà associata:

```csharp
public static bool GetHasShadow (BindableObject view)
{
  return (bool)view.GetValue (HasShadowProperty);
}

public static void SetHasShadow (BindableObject view, bool value)
{
  view.SetValue (HasShadowProperty, value);
}
```

### <a name="consuming-an-attached-property"></a>Utilizzo di una proprietà associata

Dopo aver creata una proprietà associata, possono essere utilizzato da XAML o nel codice. In XAML, infatti, la dichiarazione di uno spazio dei nomi con un prefisso, con la dichiarazione dello spazio dei nomi che indica il nome dello spazio dei nomi Common Language Runtime (CLR) e, facoltativamente, un nome di assembly. Per ulteriori informazioni, vedere [spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md).

L'esempio di codice seguente illustra uno spazio dei nomi XAML per un tipo personalizzato che contiene una proprietà associata è definita nello stesso assembly del codice dell'applicazione che fa riferimento al tipo personalizzato:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

La dichiarazione dello spazio dei nomi viene quindi utilizzata durante l'impostazione della proprietà associata a un controllo specifico, come illustrato nell'esempio di codice XAML seguente:

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

Il codice C# equivalente è visualizzato nell'esempio seguente:

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consuming-an-attached-property-with-a-style"></a>Utilizzo di una proprietà associata con uno stile

Inoltre, è possibile aggiungere la proprietà associata a un controllo da uno stile. Nell'esempio di codice XAML seguente viene un *esplicita* stile che utilizza il `HasShadow` proprietà collegata, che può essere applicato a [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) controlli:

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

Il [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) può essere applicato a un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) impostando il relativo [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà per il `Style` istanza utilizzando il `StaticResource`estensione di markup, come illustrato nell'esempio di codice seguente:

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

Per ulteriori informazioni sugli stili, vedere [stili](~/xamarin-forms/user-interface/styles/index.md).

## <a name="advanced-scenarios"></a>Scenari avanzati

Quando si crea una proprietà associata, esistono numerosi parametri facoltativi che è possibile impostare per abilitare scenari avanzati proprietà associata. Ciò include il rilevamento delle modifiche di proprietà, i valori delle proprietà di convalida e l'assegnazione di valori di proprietà. Per ulteriori informazioni, vedere [scenari avanzati](~/xamarin-forms/xaml/bindable-properties.md#advanced).

## <a name="summary"></a>Riepilogo

In questo articolo fornita un'introduzione alle proprietà associate e illustrato come creare e utilizzarli. Una proprietà associata è un tipo speciale di proprietà associabile, definito in una classe ma collegato ad altri oggetti e che sia riconoscibile in XAML come attributi che contengono una classe e un nome di proprietà separati da un punto.


## <a name="related-links"></a>Collegamenti correlati

- [Proprietà associabili](~/xamarin-forms/xaml/bindable-properties.md)
- [Spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md)
- [Effetto di ombreggiatura (esempio)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
- [BindableProperty](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)
- [BindableObject](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)
