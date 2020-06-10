---
title: "EffectBehavior riutilizzabile" Descrizione: "i comportamenti sono un approccio utile per aggiungere un effetto a un controllo, rimuovendo il codice di gestione degli effetti della piastra calda dai file code-behind. Questo articolo illustra la creazione e l'utilizzo di un Xamarin.Forms comportamento per aggiungere un effetto a un controllo ".
ms. prod: Novell MS. AssetID: A909B24D-960A-4023-AFF6-4B9256C55ADD ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 04/06/2016 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="reusable-effectbehavior"></a>EffectBehavior riusabili

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)

_I comportamenti sono un approccio utile per aggiungere un effetto a un controllo, rimuovendo il codice di gestione degli effetti della piastra calda dai file code-behind. Questo articolo illustra la creazione e l'utilizzo di un Xamarin.Forms comportamento per aggiungere un effetto a un controllo._

## <a name="overview"></a>Panoramica

La `EffectBehavior` classe è un comportamento personalizzato riutilizzabile Xamarin.Forms che aggiunge un' [`Effect`](xref:Xamarin.Forms.Effect) istanza a un controllo quando il comportamento è associato al controllo e rimuove l' `Effect` istanza quando il comportamento viene scollegato dal controllo.

Per usare il comportamento, è necessario impostare le proprietà del comportamento seguenti:

- **Group** - Valore dell'attributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) per la classe dell'effetto.
- **Name** - Valore dell'attributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) per la classe dell'effetto.

Per altre informazioni sugli effetti, vedere [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md).

> [!NOTE]
> `EffectBehavior`È una classe personalizzata che può essere individuata nell' [esempio di comportamento degli effetti](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)e non fa parte di Xamarin.Forms .

## <a name="creating-the-behavior"></a>Creazione del comportamento

La `EffectBehavior` classe deriva dalla [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe, dove `T` è un oggetto [`View`](xref:Xamarin.Forms.View) . Ciò significa che la `EffectBehavior` classe può essere associata a qualsiasi Xamarin.Forms controllo.

### <a name="implementing-bindable-properties"></a>Implementazione di proprietà associabili

La `EffectBehavior` classe definisce due [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) istanze, che vengono usate per aggiungere un oggetto [`Effect`](xref:Xamarin.Forms.Effect) a un controllo quando il comportamento è associato al controllo. Queste proprietà sono visualizzate nell'esempio di codice seguente:

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

Quando `EffectBehavior` viene utilizzato, la `Group` proprietà deve essere impostata sul valore dell' [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) attributo per l'effetto. Inoltre, la `Name` proprietà deve essere impostata sul valore dell' [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) attributo per la classe Effect.

### <a name="implementing-the-overrides"></a>Implementazione degli override

La `EffectBehavior` classe esegue l'override di [ `OnAttachedTo` ] (xrif: Xamarin.Forms . Comportamento `1.OnAttachedTo(Xamarin.Forms.BindableObject)) and [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . BindableObject)). metodi della [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe, come illustrato nell'esempio di codice seguente:

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

[ `OnAttachedTo` ] (Xrif: Xamarin.Forms . Comportamento `1.OnAttachedTo(Xamarin.Forms.BindableObject)) method performs setup by calling the ` AddEffect ` method, passing in the attached control as a parameter. The [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . BindableObject)) il metodo esegue la pulizia chiamando il `RemoveEffect` metodo, passando il controllo associato come parametro.

### <a name="implementing-the-behavior-functionality"></a>Implementazione della funzionalità del comportamento

Lo scopo del comportamento è quello di aggiungere il [`Effect`](xref:Xamarin.Forms.Effect) definito nelle `Group` proprietà e `Name` a un controllo quando il comportamento è associato al controllo e rimuovere `Effect` quando il comportamento viene scollegato dal controllo. La funzionalità del comportamento principale è illustrata nell'esempio di codice seguente:

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

Il metodo `AddEffect` viene eseguito in risposta all'associazione di `EffectBehavior` a un controllo e riceve il controllo associato come parametro. Il metodo aggiunge quindi l'effetto recuperato alla raccolta del controllo [`Effects`](xref:Xamarin.Forms.Element.Effects) . Il metodo `RemoveEffect` viene eseguito in risposta allo scollegamento di `EffectBehavior` da un controllo e riceve il controllo associato come parametro. Il metodo rimuove quindi l'effetto dall'insieme del controllo [`Effects`](xref:Xamarin.Forms.Element.Effects) .

Il `GetEffect` metodo usa il [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) metodo per recuperare l'oggetto [`Effect`](xref:Xamarin.Forms.Effect) . L'effetto viene individuato tramite una concatenazione dei valori delle proprietà `Group` e `Name`. Se una piattaforma non fornisce l'effetto, il metodo `Effect.Resolve` restituirà un valore non `null`.

## <a name="consuming-the-behavior"></a>Utilizzo del comportamento

La `EffectBehavior` classe può essere collegata alla [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) raccolta di un controllo, come illustrato nell'esempio di codice XAML seguente:

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

Le `Group` `Name` proprietà e del comportamento sono impostate sui valori degli [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) attributi e per la classe Effect in ogni progetto specifico della piattaforma.

In fase di esecuzione, quando il comportamento è associato al [`Label`](xref:Xamarin.Forms.Label) controllo, l'oggetto `Xamarin.LabelShadowEffect` verrà aggiunto alla raccolta del controllo [`Effects`](xref:Xamarin.Forms.Element.Effects) . Il risultato è l'aggiunta di un'ombreggiatura al testo visualizzato dal controllo `Label`, come illustrato negli screenshot seguenti:

![](effect-behavior-images/screenshots.png "Sample Application with EffectsBehavior")

Il vantaggio dell'uso di questo comportamento per aggiungere e rimuovere effetti per i controlli è che il codice boilerplate di gestione degli effetti può essere rimosso dai file code-behind.

## <a name="summary"></a>Summary

In questo articolo è stato illustrato l'uso di un comportamento per aggiungere un effetto a un controllo. La `EffectBehavior` classe è un comportamento personalizzato riutilizzabile Xamarin.Forms che aggiunge un' [`Effect`](xref:Xamarin.Forms.Effect) istanza a un controllo quando il comportamento è associato al controllo e rimuove l' `Effect` istanza quando il comportamento viene scollegato dal controllo.

## <a name="related-links"></a>Collegamenti correlati

- [Effetti](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Esempio di comportamento di effetto](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento &lt; T&gt;](xref:Xamarin.Forms.Behavior`1)
