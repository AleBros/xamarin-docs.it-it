---
title: EffectBehavior riutilizzabili
description: I comportamenti sono un approccio utile per l'aggiunta di un effetto a un controllo, la rimozione di effetto di stampa o editoriale codice dal file code-behind per la gestione. In questo articolo viene illustrato l'utilizzo di un comportamento di xamarin. Forms per aggiungere un effetto a un controllo.
ms.topic: article
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 698e2baef26a985fa40c2943cd25aefae55381f9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="reusable-effectbehavior"></a>EffectBehavior riutilizzabili

_I comportamenti sono un approccio utile per l'aggiunta di un effetto a un controllo, la rimozione di effetto di stampa o editoriale codice dal file code-behind per la gestione. In questo articolo viene illustrato l'utilizzo di un comportamento di xamarin. Forms per aggiungere un effetto a un controllo._

## <a name="overview"></a>Panoramica

Il `EffectBehavior` classe è un comportamento personalizzato xamarin. Forms riutilizzabile che aggiunge un [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) istanza a un controllo quando il comportamento è associato al controllo e rimuove il `Effect` quando il comportamento di istanza scollegare dal controllo.

Per utilizzare il comportamento, è necessario impostare le proprietà di comportamento seguente:

- **Gruppo** : il valore di [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) attributo per la classe effetto.
- **Nome** : il valore di [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) attributo per la classe effetto.

Per ulteriori informazioni sugli effetti, vedere [effetti](~/xamarin-forms/app-fundamentals/effects/index.md).

## <a name="creating-the-behavior"></a>La creazione del comportamento

Il `EffectBehavior` deriva dalla classe di [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) (classe), in cui `T` è un [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/). Ciò significa che la `EffectBehavior` classe può essere associata a qualsiasi controllo di xamarin. Forms.

### <a name="implementing-bindable-properties"></a>Implementazione di proprietà associabili

Il `EffectBehavior` classe definisce due [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) istanze, vengono utilizzate per aggiungere un [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) a un controllo quando il comportamento è associato al controllo. Queste proprietà vengono visualizzate nell'esempio di codice seguente:

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

Quando il `EffectBehavior` viene utilizzata la `Group` proprietà deve essere impostata sul valore del [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) attributo per l'effetto. Inoltre, il `Name` proprietà deve essere impostata sul valore del [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) attributo per la classe effettiva.

### <a name="implementing-the-overrides"></a>Le sostituzioni di implementazione

Il `EffectBehavior` classe esegue l'override di [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) e [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) metodi del [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) classe, come illustrato nel codice seguente esempio:

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

Il [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) metodo esegue il programma di installazione mediante la chiamata di `AddEffect` (metodo), passando il controllo associato come parametro. Il [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) metodo esegue la pulitura chiamando il `RemoveEffect` (metodo), passando il controllo associato come parametro.

### <a name="implementing-the-behavior-functionality"></a>L'implementazione della funzionalità di comportamento

Lo scopo del comportamento consiste nell'aggiungere il [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) definito nel `Group` e `Name` proprietà a un controllo quando il comportamento è associato al controllo e rimuovere il `Effect` quando il comportamento è scollegare dal controllo. Le funzionalità di comportamento di base sono illustrata nell'esempio di codice seguente:

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

Il `AddEffect` metodo viene eseguito in risposta al `EffectBehavior` viene collegato a un controllo che riceve il controllo associato come parametro. Il metodo aggiunge quindi l'effetto recuperato per il controllo [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) insieme. Il `RemoveEffect` metodo viene eseguito in risposta al `EffectBehavior` viene scollegato da un controllo che riceve il controllo associato come parametro. Il metodo quindi rimuove l'effetto del controllo [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) insieme.

Il `GetEffect` metodo utilizza il [ `Effect.Resolve` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.Resolve/p/System.String/) metodo per recuperare il [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/). L'effetto è disponibile tramite una concatenazione del `Group` e `Name` i valori delle proprietà. Se l'effetto, non ne fornisce una piattaforma di `Effect.Resolve` metodo verrà restituito un non -`null` valore.

## <a name="consuming-the-behavior"></a>Il comportamento di utilizzo

Il `EffectBehavior` classe può essere associata al [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) raccolta di un controllo, come illustrato nell'esempio di codice XAML seguente:

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

Il `Group` e `Name` sono impostate sui valori di proprietà del comportamento di [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) e [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) gli attributi della classe effetto in ogni specifico della piattaforma progetto.

In fase di esecuzione, quando il comportamento è collegato al [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) (controllo), il `Xamarin.LabelShadowEffect` verrà aggiunto al controllo [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) insieme. Di conseguenza, un'ombreggiatura da aggiungere al testo visualizzato per il `Label` controllare, come illustrato nelle schermate seguenti:

![](effect-behavior-images/screenshots.png "Applicazione di esempio con EffectsBehavior")

Il vantaggio dell'utilizzo di questo comportamento per aggiungere e rimuovere gli effetti da controlli è che il codice di effetto Gestione stampa o editoriale può essere rimosso dal file code-behind.

## <a name="summary"></a>Riepilogo

Questo articolo sono state illustrate utilizzando un comportamento per aggiungere un effetto a un controllo. Il `EffectBehavior` classe è un comportamento personalizzato xamarin. Forms riutilizzabile che aggiunge un [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) istanza a un controllo quando il comportamento è associato al controllo e rimuove il `Effect` quando il comportamento di istanza scollegare dal controllo.


## <a name="related-links"></a>Collegamenti correlati

- [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Comportamento di effetto (esempio)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)
- [Comportamento](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [Comportamento<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
