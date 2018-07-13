---
title: EffectBehavior riutilizzabili
description: I comportamenti sono un approccio utile per l'aggiunta di un effetto a un controllo, la rimozione effettiva boilerplate codice da file code-behind per la gestione. Questo articolo illustra l'uso di un comportamento di xamarin. Forms per aggiungere un effetto di un controllo.
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 1ce7eda6f556041cbffc3793b00e8e2cba44d3d0
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995781"
---
# <a name="reusable-effectbehavior"></a>EffectBehavior riutilizzabili

_I comportamenti sono un approccio utile per l'aggiunta di un effetto a un controllo, la rimozione effettiva boilerplate codice da file code-behind per la gestione. Questo articolo illustra l'uso di un comportamento di xamarin. Forms per aggiungere un effetto di un controllo._

## <a name="overview"></a>Panoramica

Il `EffectBehavior` classe è un comportamento personalizzato riutilizzabile xamarin. Forms che consente di aggiungere un' [ `Effect` ](xref:Xamarin.Forms.Effect) istanza a un controllo quando il comportamento è associato al controllo e rimuove il `Effect` quando il comportamento di istanza scollegato dal controllo.

Utilizzare il comportamento, è necessario impostare le proprietà del comportamento seguente:

- **Gruppo** – il valore della [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) attributo per la classe di effetto.
- **Nome** – il valore della [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) attributo per la classe di effetto.

Per altre informazioni sugli effetti, vedere [effetti](~/xamarin-forms/app-fundamentals/effects/index.md).

## <a name="creating-the-behavior"></a>La creazione del comportamento

Il `EffectBehavior` deriva dalla classe la [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) (classe), dove `T` è un [ `View` ](xref:Xamarin.Forms.View). Ciò significa che il `EffectBehavior` classe può essere associata ad alcun controllo di xamarin. Forms.

### <a name="implementing-bindable-properties"></a>Implementazione di proprietà associabili

Il `EffectBehavior` classe vengono definiti due [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) istanze, che vengono usate per aggiungere un' [ `Effect` ](xref:Xamarin.Forms.Effect) a un controllo quando il comportamento è associato al controllo. Queste proprietà vengono visualizzate nell'esempio di codice seguente:

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

Quando la `EffectBehavior` viene utilizzato, il `Group` proprietà deve essere impostata sul valore della [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) attributo per l'effetto. Inoltre, il `Name` proprietà deve essere impostata sul valore della [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) attributo per la classe di effetto.

### <a name="implementing-the-overrides"></a>Implementazione di override

Il `EffectBehavior` classe esegue l'override di [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) e [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) metodi del [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) classe, come illustrato nel codice seguente esempio:

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

Il [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) metodo esegue il programma di installazione chiamando il `AddEffect` metodo, passando il controllo associato come parametro. Il [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) metodo esegue la pulizia chiamando il `RemoveEffect` metodo, passando il controllo associato come parametro.

### <a name="implementing-the-behavior-functionality"></a>L'implementazione della funzionalità di comportamento

Lo scopo del comportamento consiste nell'aggiungere il [ `Effect` ](xref:Xamarin.Forms.Effect) definito nel `Group` e `Name` proprietà a un controllo quando il comportamento è associato al controllo e rimuovere il `Effect` quando è il comportamento scollegato dal controllo. La funzionalità di comportamento principale è illustrata nell'esempio di codice seguente:

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

Il `AddEffect` metodo viene eseguito in risposta al `EffectBehavior` viene allegato a un controllo che riceve il controllo associato come parametro. Il metodo aggiunge quindi l'effetto recuperato per il controllo [ `Effects` ](xref:Xamarin.Forms.Element.Effects) raccolta. Il `RemoveEffect` metodo viene eseguito in risposta al `EffectBehavior` viene scollegato da un controllo che riceve il controllo associato come parametro. Il metodo quindi rimuove l'effetto del controllo [ `Effects` ](xref:Xamarin.Forms.Element.Effects) raccolta.

Il `GetEffect` metodo viene utilizzato il [ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String)) metodo per recuperare il [ `Effect` ](xref:Xamarin.Forms.Effect). L'effetto è disponibile tramite una concatenazione del `Group` e `Name` i valori delle proprietà. Se una piattaforma non fornisce l'effetto, la `Effect.Resolve` metodo verrà restituito un non -`null` valore.

## <a name="consuming-the-behavior"></a>Il comportamento di utilizzo

Il `EffectBehavior` classe può essere associata ai [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) raccolta di un controllo, come illustrato nell'esempio di codice XAML seguente:

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

Il `Group` e `Name` delle proprietà del comportamento sono impostate sui valori delle [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) e [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) gli attributi della classe effetto in ogni specifico della piattaforma progetto.

In fase di esecuzione, quando il comportamento è associato ai [ `Label` ](xref:Xamarin.Forms.Label) (controllo), il `Xamarin.LabelShadowEffect` verranno aggiunti al controllo [ `Effects` ](xref:Xamarin.Forms.Element.Effects) raccolta. Il risultato è un'ombreggiatura da aggiungere al testo visualizzato per il `Label` controllare, come illustrato negli screenshot seguenti:

![](effect-behavior-images/screenshots.png "Applicazione di esempio con EffectsBehavior")

Il vantaggio dell'uso di questo comportamento per aggiungere e rimuovere gli effetti dei controlli è che il codice di gestione delle effettive boilerplate può essere rimosso dal file code-behind.

## <a name="summary"></a>Riepilogo

Questo articolo illustrato l'utilizzo di un comportamento per aggiungere un effetto di un controllo. Il `EffectBehavior` classe è un comportamento personalizzato riutilizzabile xamarin. Forms che consente di aggiungere un' [ `Effect` ](xref:Xamarin.Forms.Effect) istanza a un controllo quando il comportamento è associato al controllo e rimuove il `Effect` quando il comportamento di istanza scollegato dal controllo.


## <a name="related-links"></a>Collegamenti correlati

- [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Comportamento di effetto (esempio)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento<T>](xref:Xamarin.Forms.Behavior`1)
