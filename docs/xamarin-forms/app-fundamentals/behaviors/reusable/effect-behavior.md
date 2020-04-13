---
title: EffectBehavior riusabili
description: I comportamenti sono un approccio utile per l'aggiunta di un effetto a un controllo e la rimozione del codice boilerplate di gestione degli effetti dai file code-behind. Questo articolo illustra la creazione e l'uso di un comportamento di Xamarin.Forms per l'aggiunta di un effetto a un controllo.
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: ca03dce3bd39664a07b7bf56d22d7c2e000e931f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771990"
---
# <a name="reusable-effectbehavior"></a>EffectBehavior riusabili

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)

_I comportamenti sono un approccio utile per aggiungere un effetto a un controllo, rimuovendo il codice di gestione dell'effetto boiler-plate dai file code-behind. In questo articolo viene illustrata la creazione e l'utilizzo di un comportamento Xamarin.Forms per aggiungere un effetto a un controllo._

## <a name="overview"></a>Panoramica

La `EffectBehavior` classe è un comportamento personalizzato Xamarin.Forms riutilizzabile che aggiunge un'istanza [`Effect`](xref:Xamarin.Forms.Effect) a un `Effect` controllo quando il comportamento è associato al controllo e rimuove l'istanza quando il comportamento viene disconnesso dal controllo.

Per usare il comportamento, è necessario impostare le proprietà del comportamento seguenti:

- **Group** - Valore dell'attributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) per la classe dell'effetto.
- **Name** - Valore dell'attributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) per la classe dell'effetto.

Per altre informazioni sugli effetti, vedere [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md).

> [!NOTE]
> `EffectBehavior` è una classe personalizzata disponibile nell'[esempio di comportamento dell'effetto](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior) e non fa parte di Xamarin.Forms.

## <a name="creating-the-behavior"></a>Creazione del comportamento

La `EffectBehavior` classe deriva [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) dalla classe `T` , [`View`](xref:Xamarin.Forms.View)dove è un oggetto . Questo significa che la classe `EffectBehavior` può essere associata a qualsiasi controllo di Xamarin.Forms.

### <a name="implementing-bindable-properties"></a>Implementazione di proprietà associabili

La `EffectBehavior` classe definisce due [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) istanze, [`Effect`](xref:Xamarin.Forms.Effect) che vengono utilizzate per aggiungere un oggetto a un controllo quando il comportamento è associato al controllo. Queste proprietà sono visualizzate nell'esempio di codice seguente:

```csharp
public class EffectBehavior : Behavior<View>
{
  public static readonly BindableProperty GroupProperty =
    BindableProperty.Create ("Group", typeof(string), typeof(EffectBehavior), null);
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(EffectBehavior), null);

  public string Group {
    get { return (string)GetValue (GroupProperty); }
    set { SetValue (GroupProperty, value); }
  }

  public string Name {
    get { return(string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }
  ...
}
```

Quando `EffectBehavior` l'oggetto viene `Group` utilizzato, la proprietà deve [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) essere impostata sul valore dell'attributo per l'effetto. Inoltre, la `Name` proprietà deve essere impostata [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) sul valore dell'attributo per la classe effect.

### <a name="implementing-the-overrides"></a>Implementazione degli override

La `EffectBehavior` classe esegue [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) l'override [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) dei metodi e della classe , come illustrato nell'esempio di codice seguente:

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  protected override void OnAttachedTo (BindableObject bindable)
  {
    base.OnAttachedTo (bindable);
    AddEffect (bindable as View);
  }

  protected override void OnDetachingFrom (BindableObject bindable)
  {
    RemoveEffect (bindable as View);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

Il [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) metodo esegue l'installazione chiamando il `AddEffect` metodo , passando il controllo associato come parametro. Il [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) metodo esegue la `RemoveEffect` pulizia chiamando il metodo , passando il controllo associato come parametro.

### <a name="implementing-the-behavior-functionality"></a>Implementazione della funzionalità del comportamento

Lo scopo del comportamento consiste [`Effect`](xref:Xamarin.Forms.Effect) nell'aggiungere le proprietà definite in `Group` e `Name` a un controllo `Effect` quando il comportamento è associato al controllo e rimuovere il quando il comportamento viene scollegato dal controllo. La funzionalità del comportamento principale è illustrata nell'esempio di codice seguente:

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  void AddEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Add (GetEffect ());
    }
  }

  void RemoveEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Remove (GetEffect ());
    }
  }

  Effect GetEffect ()
  {
    if (!string.IsNullOrWhiteSpace (Group) && !string.IsNullOrWhiteSpace (Name)) {
      return Effect.Resolve (string.Format ("{0}.{1}", Group, Name));
    }
    return null;
  }
}
```

Il metodo `AddEffect` viene eseguito in risposta all'associazione di `EffectBehavior` a un controllo e riceve il controllo associato come parametro. Il metodo aggiunge quindi l'effetto [`Effects`](xref:Xamarin.Forms.Element.Effects) recuperato alla raccolta del controllo. Il metodo `RemoveEffect` viene eseguito in risposta allo scollegamento di `EffectBehavior` da un controllo e riceve il controllo associato come parametro. Il metodo rimuove quindi l'effetto [`Effects`](xref:Xamarin.Forms.Element.Effects) dalla raccolta del controllo.

Il `GetEffect` metodo [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) utilizza il [`Effect`](xref:Xamarin.Forms.Effect)metodo per recuperare l'oggetto . L'effetto viene individuato tramite una concatenazione dei valori delle proprietà `Group` e `Name`. Se una piattaforma non fornisce l'effetto, il metodo `Effect.Resolve` restituirà un valore non `null`.

## <a name="consuming-the-behavior"></a>Utilizzo del comportamento

La `EffectBehavior` classe può essere [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) associata alla raccolta di un controllo, come illustrato nell'esempio di codice XAML seguente:The class can be attached to the collection of a control, as demonstrated in the following XAML code example:

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Behaviors>
    <local:EffectBehavior Group="Xamarin" Name="LabelShadowEffect" />
  </Label.Behaviors>
</Label>
```

Il codice C# equivalente è visualizzato nell'esempio seguente:

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};
label.Behaviors.Add (new EffectBehavior {
  Group = "Xamarin",
  Name = "LabelShadowEffect"
});
```

Le `Group` `Name` proprietà e del comportamento vengono impostate sui valori degli attributi [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) e [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) per la classe effect in ogni progetto specifico della piattaforma.

In fase di esecuzione, quando [`Label`](xref:Xamarin.Forms.Label) il comportamento `Xamarin.LabelShadowEffect` è associato al controllo, l'oggetto verrà aggiunto alla raccolta del [`Effects`](xref:Xamarin.Forms.Element.Effects) controllo. Il risultato è l'aggiunta di un'ombreggiatura al testo visualizzato dal controllo `Label`, come illustrato negli screenshot seguenti:

![](effect-behavior-images/screenshots.png "Sample Application with EffectsBehavior")

Il vantaggio dell'uso di questo comportamento per aggiungere e rimuovere effetti per i controlli è che il codice boilerplate di gestione degli effetti può essere rimosso dai file code-behind.

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato l'uso di un comportamento per aggiungere un effetto a un controllo. La `EffectBehavior` classe è un comportamento personalizzato Xamarin.Forms riutilizzabile che aggiunge un'istanza [`Effect`](xref:Xamarin.Forms.Effect) a un `Effect` controllo quando il comportamento è associato al controllo e rimuove l'istanza quando il comportamento viene disconnesso dal controllo.

## <a name="related-links"></a>Collegamenti correlati

- [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Esempio di comportamento di effetto](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
