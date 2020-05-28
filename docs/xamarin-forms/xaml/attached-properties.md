---
title: ''
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
ms.openlocfilehash: 1f26a4415a75b2b02fd7d6893e366ef81156f077
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138190"
---
# <a name="attached-properties"></a>Proprietà associate

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)


Le proprietà associate consentono a un oggetto di assegnare un valore per una proprietà non definita dalla relativa classe. Ad esempio, gli elementi figlio possono utilizzare le proprietà associate per informare l'elemento padre della modalità di presentazione nell'interfaccia utente. Il [`Grid`](xref:Xamarin.Forms.Grid) controllo consente di specificare la riga e la colonna di un elemento figlio impostando `Grid.Row` le `Grid.Column` proprietà associate e. `Grid.Row`e `Grid.Column` sono proprietà associate perché sono impostate su elementi figlio di un oggetto `Grid` , anziché sullo `Grid` stesso oggetto.

Le proprietà associabili devono essere implementate come proprietà associate negli scenari seguenti:

- Quando è necessario disporre di un meccanismo di impostazione delle proprietà disponibile per le classi diverse dalla classe di definizione.
- Quando la classe rappresenta un servizio che deve essere facilmente integrato con altre classi.

Per ulteriori informazioni sulle proprietà associabili, vedere [proprietà associabili](~/xamarin-forms/xaml/bindable-properties.md).

## <a name="create-an-attached-property"></a>Creare una proprietà associata

Il processo per la creazione di una proprietà associata è il seguente:

1. Creare un' [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) istanza di con uno degli [`CreateAttached`](xref:Xamarin.Forms.BindableProperty.CreateAttached*) Overload del metodo.
1. Fornire `static` `Get` metodi *PropertyName* e `Set` *PropertyName* come funzioni di accesso per la proprietà associata.

### <a name="create-a-property"></a>Creare una proprietà

Quando si crea una proprietà associata per l'uso in altri tipi, la classe in cui viene creata la proprietà non deve derivare da [`BindableObject`](xref:Xamarin.Forms.BindableObject) . Tuttavia, la proprietà di *destinazione* per le funzioni di accesso deve essere o derivare da [`BindableObject`](xref:Xamarin.Forms.BindableObject) .

Una proprietà associata può essere creata dichiarando una `public static readonly` proprietà di tipo [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) . È necessario impostare la proprietà associabile sul valore restituito di uno degli [ `BindableProperty.CreateAttached` ] (xrif: Xamarin.Forms . BindableProperty. CreateAttached (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . BindableProperty. ValidateValueDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangedDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangingDelegate, Xamarin.Forms . BindableProperty. CoerceValueDelegate, Xamarin.Forms . BindableProperty. CreateDefaultValueDelegate)) overload del metodo. La dichiarazione deve trovarsi all'interno del corpo della classe proprietaria, ma all'esterno delle definizioni dei membri.

Il codice seguente illustra un esempio di una proprietà associata:

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

Viene creata una proprietà associata denominata `HasShadow` , di tipo `bool` . La proprietà è di proprietà della `ShadowEffect` classe e il valore predefinito è `false` . La convenzione di denominazione per le proprietà associate è che l'identificatore della proprietà associata deve corrispondere al nome della proprietà specificato nel `CreateAttached` metodo, con l'aggiunta di "Property". Nell'esempio precedente, quindi, l'identificatore della proprietà associata è `HasShadowProperty` .

Per ulteriori informazioni sulla creazione di proprietà associabili, inclusi i parametri che possono essere specificati durante la creazione, vedere [creare una proprietà associabile](~/xamarin-forms/xaml/bindable-properties.md#consume-a-bindable-property).

### <a name="create-accessors"></a>Crea funzioni di accesso

I `Get` metodi statici *PropertyName* e `Set` *PropertyName* sono obbligatori come funzioni di accesso per la proprietà associata. in caso contrario, il sistema di proprietà non sarà in grado di utilizzare la proprietà associata. La `Get` funzione di accesso *PropertyName* deve essere conforme alla firma seguente:

```csharp
public static valueType GetPropertyName(BindableObject target)
```

La `Get` funzione di accesso *PropertyName* deve restituire il valore contenuto nel campo corrispondente `BindableProperty` per la proprietà associata. Questa operazione può essere eseguita chiamando il [ `GetValue` ] (xrif: Xamarin.Forms . BindableObject. GetValue ( Xamarin.Forms . BindableProperty)), passando l'identificatore di proprietà associabile sul quale ottenere il valore e quindi eseguendo il cast del valore risultante al tipo richiesto.

La `Set` funzione di accesso *PropertyName* deve essere conforme alla firma seguente:

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

La `Set` funzione di accesso *PropertyName* deve impostare il valore del `BindableProperty` campo corrispondente per la proprietà associata. Questa operazione può essere eseguita chiamando il [ `SetValue` ] (xrif: Xamarin.Forms . BindableObject. SetValue ( Xamarin.Forms . Metodo BindableProperty, System. Object)), passando l'identificatore di proprietà associabile su cui impostare il valore e il valore da impostare.

Per entrambe le funzioni di accesso, l'oggetto di *destinazione* deve essere di o derivare da [`BindableObject`](xref:Xamarin.Forms.BindableObject) .

Nell'esempio di codice riportato di seguito vengono illustrate le funzioni di accesso per la `HasShadow` proprietà associata:

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

### <a name="consume-an-attached-property"></a>Utilizzare una proprietà associata

Una volta creata, una proprietà associata può essere utilizzata da XAML o dal codice. In XAML, questa operazione viene eseguita dichiarando uno spazio dei nomi con un prefisso, con la dichiarazione dello spazio dei nomi che indica il nome dello spazio dei nomi CLR (Common Language Runtime) e, facoltativamente, un nome di assembly. Per altre informazioni, vedere [spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md).

Nell'esempio di codice riportato di seguito viene illustrato uno spazio dei nomi XAML per un tipo personalizzato che contiene una proprietà associata, definita all'interno dello stesso assembly del codice dell'applicazione che fa riferimento al tipo personalizzato:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

La dichiarazione dello spazio dei nomi viene quindi utilizzata quando si imposta la proprietà associata su un controllo specifico, come illustrato nell'esempio di codice XAML seguente:

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

Il codice C# equivalente è visualizzato nell'esempio seguente:

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consume-an-attached-property-with-a-style"></a>Utilizzare una proprietà associata con uno stile

Le proprietà associate possono inoltre essere aggiunte a un controllo da uno stile. L'esempio di codice XAML seguente mostra uno stile *esplicito* che usa la `HasShadow` proprietà associata, che può essere applicata ai [`Label`](xref:Xamarin.Forms.Label) Controlli:

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

L'oggetto [`Style`](xref:Xamarin.Forms.Style) può essere applicato a un impostando la [`Label`](xref:Xamarin.Forms.Label) relativa [`Style`](xref:Xamarin.Forms.NavigableElement.Style) proprietà sull' `Style` istanza di usando l'estensione di `StaticResource` markup, come illustrato nell'esempio di codice seguente:

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

Per altre informazioni sugli stili, vedere [Stili](~/xamarin-forms/user-interface/styles/index.md).

## <a name="advanced-scenarios"></a>Scenari avanzati

Quando si crea una proprietà associata, sono disponibili diversi parametri facoltativi che possono essere impostati per abilitare gli scenari di proprietà associate avanzate. Sono inclusi il rilevamento delle modifiche alle proprietà, la convalida dei valori delle proprietà e la coercizione dei valori delle proprietà. Per ulteriori informazioni, vedere [scenari avanzati](~/xamarin-forms/xaml/bindable-properties.md#advanced-scenarios).

## <a name="related-links"></a>Collegamenti correlati

- [Proprietà associabili](~/xamarin-forms/xaml/bindable-properties.md)
- [Spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md)
- [Shadow Effect (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect) (Esempio di effetto di ombreggiatura)
- [API BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [API BindableObject](xref:Xamarin.Forms.BindableObject)
