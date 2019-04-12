---
title: EffectBehavior riusabili
description: I comportamenti sono un approccio utile per l'aggiunta di un effetto a un controllo e la rimozione del codice boilerplate di gestione degli effetti dai file code-behind. Questo articolo illustra la creazione e l'uso di un comportamento di Xamarin.Forms per l'aggiunta di un effetto a un controllo.
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 8c831dfa45c5028e4f1422467bf3be9210452335
ms.sourcegitcommit: be51b459a0a148ae3adca31d7599f53f7b2c3a68
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59019373"
---
# <a name="reusable-effectbehavior"></a>EffectBehavior riusabili

[![D[Scarica esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)

_I comportamenti sono un approccio utile per l'aggiunta di un effetto a un controllo e la rimozione del codice boilerplate di gestione degli effetti dai file code-behind. Questo articolo illustra la creazione e l'uso di un comportamento di Xamarin.Forms per l'aggiunta di un effetto a un controllo._

## <a name="overview"></a>Panoramica

La classe `EffectBehavior` è un comportamento personalizzato Xamarin.Forms riutilizzabile che consente di aggiungere un'istanza di [`Effect`](xref:Xamarin.Forms.Effect) a un controllo quando il comportamento viene associato al controllo e rimuove l'istanza `Effect` quando il comportamento viene scollegato dal controllo.

Per usare il comportamento, è necessario impostare le proprietà del comportamento seguenti:

- **Group** - Valore dell'attributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) per la classe dell'effetto.
- **Name** - Valore dell'attributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) per la classe dell'effetto.

Per altre informazioni sugli effetti, vedere [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md).

> [!NOTE]
> `EffectBehavior` è una classe personalizzata disponibile nell'[esempio di comportamento dell'effetto](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/) e non fa parte di Xamarin.Forms.

## <a name="creating-the-behavior"></a>Creazione del comportamento

La classe `EffectBehavior` deriva dalla classe [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), dove `T` è un elemento [`View`](xref:Xamarin.Forms.View). Questo significa che la classe `EffectBehavior` può essere associata a qualsiasi controllo di Xamarin.Forms.

### <a name="implementing-bindable-properties"></a>Implementazione di proprietà associabili

La classe `EffectBehavior` definisce due istanze di [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), che vengono usate per aggiungere un [`Effect`](xref:Xamarin.Forms.Effect) a un controllo quando il comportamento è associato al controllo. Queste proprietà sono visualizzate nell'esempio di codice seguente:

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

Quando si utilizza `EffectBehavior`, la proprietà `Group` deve essere impostata sul valore dell'attributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) per l'effetto. Inoltre, la proprietà `Name` deve essere impostata sul valore dell'attributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) per la classe dell'effetto.

### <a name="implementing-the-overrides"></a>Implementazione degli override

La classe `EffectBehavior` esegue l'override dei metodi [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) e [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) della classe [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), come illustrato nell'esempio di codice seguente:

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

Il metodo [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) esegue la configurazione chiamando il metodo `AddEffect`, passando il controllo associato come parametro. Il metodo [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) esegue la pulizia chiamando il metodo `RemoveEffect`, passando il controllo associato come parametro.

### <a name="implementing-the-behavior-functionality"></a>Implementazione della funzionalità del comportamento

Lo scopo del comportamento è aggiungere l'[`Effect`](xref:Xamarin.Forms.Effect) definito nelle proprietà `Group` e `Name` a un controllo quando il comportamento viene associato al controllo e rimuovere `Effect` quando è il comportamento viene scollegato dal controllo. La funzionalità del comportamento principale è illustrata nell'esempio di codice seguente:

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

Il metodo `AddEffect` viene eseguito in risposta all'associazione di `EffectBehavior` a un controllo e riceve il controllo associato come parametro. Il metodo aggiunge quindi l'effetto recuperato alla raccolta [`Effects`](xref:Xamarin.Forms.Element.Effects) del controllo. Il metodo `RemoveEffect` viene eseguito in risposta allo scollegamento di `EffectBehavior` da un controllo e riceve il controllo associato come parametro. Il metodo rimuove quindi l'effetto dalla raccolta [`Effects`](xref:Xamarin.Forms.Element.Effects) del controllo.

Il metodo `GetEffect` usa il metodo [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) per recuperare l'[`Effect`](xref:Xamarin.Forms.Effect). L'effetto viene individuato tramite una concatenazione dei valori delle proprietà `Group` e `Name`. Se una piattaforma non fornisce l'effetto, il metodo `Effect.Resolve` restituirà un valore non `null`.

## <a name="consuming-the-behavior"></a>Utilizzo del comportamento

La classe `EffectBehavior` può essere associata alla raccolta [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) di un controllo, come illustrato nell'esempio di codice XAML seguente:

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

Le proprietà `Group` e `Name` del comportamento vengono impostate sui valori degli attributi [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) e [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) della classe dell'effetto in ogni progetto specifico della piattaforma.

In fase di esecuzione, quando il comportamento è associato al controllo [`Label`](xref:Xamarin.Forms.Label), `Xamarin.LabelShadowEffect` verrà aggiunto alla raccolta [`Effects`](xref:Xamarin.Forms.Element.Effects) del controllo. Il risultato è l'aggiunta di un'ombreggiatura al testo visualizzato dal controllo `Label`, come illustrato negli screenshot seguenti:

![](effect-behavior-images/screenshots.png "Applicazione di esempio con EffectsBehavior")

Il vantaggio dell'uso di questo comportamento per aggiungere e rimuovere effetti per i controlli è che il codice boilerplate di gestione degli effetti può essere rimosso dai file code-behind.

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato l'uso di un comportamento per aggiungere un effetto a un controllo. La classe `EffectBehavior` è un comportamento personalizzato Xamarin.Forms riutilizzabile che consente di aggiungere un'istanza di [`Effect`](xref:Xamarin.Forms.Effect) a un controllo quando il comportamento viene associato al controllo e rimuove l'istanza `Effect` quando il comportamento viene scollegato dal controllo.


## <a name="related-links"></a>Collegamenti correlati

- [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Esempio di comportamento di effetto](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Behavior&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
