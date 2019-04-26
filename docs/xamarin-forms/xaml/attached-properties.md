---
title: Proprietà associate
description: Questo articolo fornisce un'introduzione alle proprietà associate e viene illustrato come creare e il loro uso.
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
ms.openlocfilehash: 130f4e3efdd7b3792f6458ad28fc85654e6e38c8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60857432"
---
# <a name="attached-properties"></a>Proprietà associate

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)

_Una proprietà associata è un tipo speciale di proprietà associabile, definito in una classe ma collegato ad altri oggetti e riconoscibile in XAML come attributo che contiene una classe e un nome di proprietà separati da un punto. Questo articolo fornisce un'introduzione alle proprietà associate e viene illustrato come creare e il loro uso._

## <a name="overview"></a>Panoramica

Collegati abilitare proprietà un oggetto per assegnare un valore per una proprietà che non definisce una propria classe. Ad esempio, possono utilizzare gli elementi figlio collegati proprietà in modo da informare relativo elemento padre del modo in cui sono presentati nell'interfaccia utente. Il [ `Grid` ](xref:Xamarin.Forms.Grid) controllo consente lo righe e colonne di un elemento figlio di essere specificato impostando il `Grid.Row` e `Grid.Column` proprietà associate. `Grid.Row` e `Grid.Column` sono le proprietà associate, perché vengono impostate su elementi che sono elementi figlio di un `Grid`, anziché sul `Grid` stesso.

Proprietà associabili deve essere implementata come proprietà associate negli scenari seguenti:

- La definizione di classe quando è necessario avere un meccanismo di impostazione proprietà disponibile, per le classi diverso da.
- Quando la classe rappresenta un servizio che deve essere facilmente integrato con altre classi.

Per altre informazioni sulle proprietà associabile, vedere [proprietà associabili](~/xamarin-forms/xaml/bindable-properties.md).

## <a name="creating-and-consuming-an-attached-property"></a>Creazione e l'utilizzo di una proprietà associata

Il processo per la creazione di una proprietà associata è come segue:

1. Creare un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) istanza con uno del [ `CreateAttached` ](xref:Xamarin.Forms.BindableProperty.CreateAttached*) overload del metodo.
1. Fornire `static` `Get` *PropertyName* e `Set` *PropertyName* metodi come funzioni di accesso per la proprietà associata.

### <a name="creating-a-property"></a>Creazione di una proprietà

Quando si crea una proprietà associata per l'uso in altri tipi, la classe in cui viene creata la proprietà non deve derivare da [ `BindableObject` ](xref:Xamarin.Forms.BindableObject). Tuttavia, il *destinazione* proprietà per le funzioni di accesso deve essere o derivare da, [ `BindableObject` ](xref:Xamarin.Forms.BindableObject).

Una proprietà associata può essere creata con la dichiarazione di una `public static readonly` vlastnosti typu [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty). Le proprietà associabili deve essere impostata sul valore restituito di una delle [ `BindableProperty.CreateAttached` ](xref:Xamarin.Forms.BindableProperty.CreateAttached(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) overload del metodo. La dichiarazione deve essere all'interno del corpo della classe di appartenenza, ma di fuori di qualsiasi definizione del membro.

Il codice seguente viene illustrato un esempio di una proprietà associata:

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

Verrà creata una proprietà associata denominata `HasShadow`, di tipo `bool`. La proprietà è di proprietà di `ShadowEffect` classi e ha un valore predefinito di `false`. La convenzione di denominazione per le proprietà associate è che l'identificatore della proprietà associata deve corrispondere al nome di proprietà specificato nella `CreateAttached` con "Property" aggiunto a tale metodo. Nell'esempio precedente, pertanto, l'identificatore della proprietà associata è `HasShadowProperty`.

Per altre informazioni sulla creazione di proprietà associabile, inclusi i parametri che possono essere specificati durante la creazione, vedere [creazione e l'utilizzo di una proprietà associabile](~/xamarin-forms/xaml/bindable-properties.md#consuming-bindable-property).

### <a name="creating-accessors"></a>Creazione di funzioni di accesso

Statica `Get` *PropertyName* e `Set` *PropertyName* metodi sono necessari le funzioni di accesso per la proprietà associata, in caso contrario, sarà possibile usare il sistema di proprietà di proprietà associata. Il `Get` *NomeProprietà* della funzione di accesso deve essere conforme per la firma seguente:

```csharp
public static valueType GetPropertyName(BindableObject target)
```

Il `Get` *PropertyName* della funzione di accesso deve restituire il valore contenuto nel corrispondente `BindableProperty` campo per la proprietà associata. Ciò può essere ottenuto chiamando il [ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) (metodo), passando l'identificatore di proprietà associabili su cui ottenere il valore e quindi esegue il cast il valore risultante nel tipo richiesto.

Il `Set` *NomeProprietà* della funzione di accesso deve essere conforme per la firma seguente:

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

Il `Set` *PropertyName* della funzione di accesso deve impostare il valore dell'oggetto corrispondente `BindableProperty` campo per la proprietà associata. Ciò può essere ottenuto chiamando il [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) metodo, passando l'identificatore della proprietà associabile per cui impostare il valore e il valore da impostare.

Per entrambe le funzioni di accesso, il *destinazione* oggetto deve essere di o derivi da esso [ `BindableObject` ](xref:Xamarin.Forms.BindableObject).

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

Dopo aver creata una proprietà associata, può essere utilizzato dal codice o XAML. In XAML, ciò si ottiene con la dichiarazione di uno spazio dei nomi con un prefisso con la dichiarazione dello spazio dei nomi che indica il nome dello spazio dei nomi Common Language Runtime (CLR) e, facoltativamente, un nome di assembly. Per altre informazioni, vedere [spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md).

L'esempio di codice seguente illustra uno spazio dei nomi XAML per un tipo personalizzato che contiene una proprietà associata, che viene definita nello stesso assembly del codice dell'applicazione che fa riferimento al tipo personalizzato:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

La dichiarazione dello spazio dei nomi viene quindi utilizzata durante l'impostazione della proprietà associata in un controllo specifico, come illustrato nell'esempio di codice XAML seguente:

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

Il codice C# equivalente è visualizzato nell'esempio seguente:

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consuming-an-attached-property-with-a-style"></a>Utilizzo di una proprietà associata con uno stile

Le proprietà associate possono anche essere aggiunto a un controllo da uno stile. L'esempio di codice XAML seguente mostra un' *esplicite* stile di visualizzazione che utilizza il `HasShadow` proprietà collegata, che può essere applicato a [ `Label` ](xref:Xamarin.Forms.Label) controlli:

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

Il [ `Style` ](xref:Xamarin.Forms.Style) può essere applicato a un [ `Label` ](xref:Xamarin.Forms.Label) impostando relativo [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) proprietà per il `Style` istanza utilizzando il `StaticResource`estensione di markup, come illustrato nell'esempio di codice seguente:

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

Per altre informazioni sugli stili, vedere [stili](~/xamarin-forms/user-interface/styles/index.md).

## <a name="advanced-scenarios"></a>Scenari avanzati

Durante la creazione di una proprietà associata, esistono una serie di parametri facoltativi che è possibile impostare per abilitare scenari avanzati proprietà associata. Ciò include il rilevamento delle modifiche di proprietà, convalida i valori delle proprietà e la coercizione di valori di proprietà. Per altre informazioni, vedere [scenari avanzati di](~/xamarin-forms/xaml/bindable-properties.md#advanced).

## <a name="summary"></a>Riepilogo

Questo articolo ha presentato le proprietà associate ed è stato illustrato come creare e il loro uso. Una proprietà associata è un tipo speciale di proprietà associabile, definita in una classe ma collegato ad altri oggetti e che sia riconoscibile in XAML come attributi che contengono una classe e un nome di proprietà separati da un punto.


## <a name="related-links"></a>Collegamenti correlati

- [Proprietà associabili](~/xamarin-forms/xaml/bindable-properties.md)
- [Spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md)
- [Effetto di ombreggiatura (esempio)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
- [BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [BindableObject](xref:Xamarin.Forms.BindableObject)
