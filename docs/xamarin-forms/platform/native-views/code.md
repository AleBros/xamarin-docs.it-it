---
title: Viste native in c#
description: Viste native di iOS, Android e UWP possono fare riferimento direttamente dalle pagine di xamarin. Forms create in c#. In questo articolo viene illustrato come aggiungere viste native a un layout di xamarin. Forms creato in c# e come eseguire l'override di layout di visualizzazioni personalizzate per correggere i misurazione dell'utilizzo delle API.
ms.prod: xamarin
ms.assetid: 230F937C-F914-4B21-8EA1-1A2A9E644769
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 7979e43cdde60b1b0248118e591c096295847acb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="native-views-in-c"></a>Viste native in c#

_Viste native di iOS, Android e UWP possono fare riferimento direttamente dalle pagine di xamarin. Forms create in c#. In questo articolo viene illustrato come aggiungere viste native a un layout di xamarin. Forms creato in c# e come eseguire l'override di layout di visualizzazioni personalizzate per correggere i misurazione dell'utilizzo delle API._

## <a name="overview"></a>Panoramica

Qualsiasi controllo di xamarin. Forms che consente `Content` per impostare, o che dispone di un `Children` insieme, è possibile aggiungere viste specifiche della piattaforma. Ad esempio, un iOS `UILabel` possono essere aggiunti direttamente al [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) proprietà, o il [ `StackLayout.Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/) insieme. Si noti tuttavia che questa funzionalità richiede l'utilizzo di `#if` definisce nelle soluzioni di progetto condiviso xamarin. Forms e non è disponibile da soluzioni xamarin. Forms portabile classe libreria (PCL).

Le schermate seguenti illustrano specifico della piattaforma viste è stato aggiunto a un xamarin. Forms [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/):

[![](code-images/screenshots-sml.png "Che contiene visualizzazioni specifiche della piattaforma StackLayout")](code-images/screenshots.png#lightbox "StackLayout contenenti le viste specifiche della piattaforma")

La possibilità di aggiungere viste specifiche della piattaforma in un layout di xamarin. Forms è abilitata per i due metodi di estensione in ogni piattaforma:

- `Add` : aggiunge una visualizzazione specifica della piattaforma di [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/) raccolta di un layout.
- `ToView` – accetta una visualizzazione specifica della piattaforma e ne esegue il wrapping come un xamarin. Forms [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) che può essere impostata come la `Content` proprietà di un controllo.

L'utilizzo di questi metodi in un progetto condiviso xamarin. Forms richiede l'importazione dello spazio dei nomi xamarin. Forms specifico della piattaforma appropriata:

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Windows Runtime** – Xamarin.Forms.Platform.WinRT
- **La piattaforma Windows universale (UWP)** – Xamarin.Forms.Platform.UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>Aggiunta di visualizzazioni specifiche della piattaforma in ogni piattaforma

Nelle sezioni seguenti illustrano come aggiungere viste specifiche della piattaforma a un layout di xamarin. Forms in ciascuna piattaforma.

### <a name="ios"></a>iOS

Esempio di codice seguente viene illustrato come aggiungere un `UILabel` per un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) e [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```csharp
var uiLabel = new UILabel {
  MinimumFontSize = 14f,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = originalText,
};
stackLayout.Children.Add (uiLabel);
contentView.Content = uiLabel.ToView();
```

Nell'esempio si presuppone che il `stackLayout` e `contentView` istanze sono state create in precedenza in XAML o c#.

### <a name="android"></a>Android

Esempio di codice seguente viene illustrato come aggiungere un `TextView` per un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) e [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

Nell'esempio si presuppone che il `stackLayout` e `contentView` istanze sono state create in precedenza in XAML o c#.

### <a name="windows-runtime-and-universal-windows-platform"></a>Windows Runtime e la piattaforma Windows universale

Esempio di codice seguente viene illustrato come aggiungere un `TextBlock` per un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) e [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```csharp
var textBlock = new TextBlock
{
    Text = originalText,
    FontSize = 14,
    FontFamily = new FontFamily("HelveticaNeue"),
    TextWrapping = TextWrapping.Wrap
};
stackLayout.Children.Add(textBlock);
contentView.Content = textBlock.ToView();
```

Nell'esempio si presuppone che il `stackLayout` e `contentView` istanze sono state create in precedenza in XAML o c#.

## <a name="overriding-platform-measurements-for-custom-views"></a>Si esegue l'override di misure di piattaforma per le visualizzazioni personalizzate

Visualizzazioni personalizzate in ciascuna piattaforma spesso solo correttamente implementano misura per lo scenario di layout per i quali sono stati progettati. Ad esempio, una visualizzazione personalizzata può sono stata progettata per occupare solo metà della larghezza disponibile del dispositivo. Tuttavia, dopo aver condiviso con altri utenti, la visualizzazione personalizzata potrebbe essere necessaria occupare la larghezza disponibile completa del dispositivo. Pertanto, può essere necessario eseguire l'override di un'implementazione di misurazione di visualizzazioni personalizzate quando vengono riutilizzati in un layout di xamarin. Forms. Per questo motivo, il `Add` e `ToView` metodi di estensione forniscono sostituzioni che consentono di specificare i delegati misura che è possono eseguire l'override del layout della visualizzazione personalizzata quando viene aggiunto a un layout di xamarin. Forms.

Nelle sezioni seguenti illustrano come eseguire l'override di layout di visualizzazioni personalizzate, per correggere i misurazione dell'utilizzo delle API.

### <a name="ios"></a>iOS

Nell'esempio di codice riportato di seguito viene illustrato il `CustomControl` (classe), che eredita da `UILabel`:

```csharp
public class CustomControl : UILabel
{
  public override string Text {
    get { return base.Text; }
    set { base.Text = value.ToUpper (); }
  }

  public override CGSize SizeThatFits (CGSize size)
  {
    return new CGSize (size.Width, 150);
  }
}
```

Un'istanza di questa vista viene aggiunta a un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), come illustrato nell'esempio di codice seguente:

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

Tuttavia, poiché il `CustomControl.SizeThatFits` override restituisce sempre un'altezza pari a 150, verrà visualizzata la vista con spazio vuoto sopra e sotto, come illustrato nella schermata seguente:

![](code-images/ios-bad-measurement.png "iOS CustomControl con implementazione SizeThatFits errata")

Una soluzione al problema consiste nel fornire un `GetDesiredSizeDelegate` implementazione, come illustrato nell'esempio di codice seguente:

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, double width, double height)
{
  var uiView = renderer.Control;

  if (uiView == null) {
    return null;
  }

  var constraint = new CGSize (width, height);

  // Let the CustomControl determine its size (which will be wrong)
  var badRect = uiView.SizeThatFits (constraint);

  // Use the width and substitute the height
  return new SizeRequest (new Size (badRect.Width, 70));
}
```

Questo metodo utilizza la larghezza fornita dal `CustomControl.SizeThatFits` (metodo), ma sostituisce l'altezza di 150 per un'altezza pari a 70. Quando il `CustomControl` istanza viene aggiunto al [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), `FixSize` metodo può essere specificato come il `GetDesiredSizeDelegate` correggere la misura non valida fornita dal `CustomControl` classe:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Questo comporta la visualizzazione personalizzata viene visualizzata correttamente, senza spazio vuoto sopra e sotto, come illustrato nella schermata seguente:

![](code-images/ios-good-measurement.png "iOS CustomControl con GetDesiredSize Override")

### <a name="android"></a>Android

Nell'esempio di codice riportato di seguito viene illustrato il `CustomControl` (classe), che eredita da `TextView`:

```csharp
public class CustomControl : TextView
{
  public CustomControl (Context context) : base (context)
  {
  }

  protected override void OnMeasure (int widthMeasureSpec, int heightMeasureSpec)
  {
    int width = MeasureSpec.GetSize (widthMeasureSpec);

    // Force the width to half of what's been requested.
    // This is deliberately wrong to demonstrate providing an override to fix it with.
    int widthSpec = MeasureSpec.MakeMeasureSpec (width / 2, MeasureSpec.GetMode (widthMeasureSpec));

    base.OnMeasure (widthSpec, heightMeasureSpec);
  }
}
```

Un'istanza di questa vista viene aggiunta a un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), come illustrato nell'esempio di codice seguente:

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

Tuttavia, poiché il `CustomControl.OnMeasure` override restituisce sempre la metà della larghezza della richiesta, la visualizzazione verrà visualizzata che occupano solo metà della larghezza disponibile del dispositivo, come illustrato nella schermata seguente:

![](code-images/android-bad-measurement.png "Android CustomControl con implementazione non valida OnMeasure")

Una soluzione al problema consiste nel fornire un `GetDesiredSizeDelegate` implementazione, come illustrato nell'esempio di codice seguente:

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, int widthConstraint, int heightConstraint)
{
  var nativeView = renderer.Control;

  if ((widthConstraint == 0 && heightConstraint == 0) || nativeView == null) {
    return null;
  }

  int width = Android.Views.View.MeasureSpec.GetSize (widthConstraint);
  int widthSpec = Android.Views.View.MeasureSpec.MakeMeasureSpec (
    width * 2, Android.Views.View.MeasureSpec.GetMode (widthConstraint));
  nativeView.Measure (widthSpec, heightConstraint);
  return new SizeRequest (new Size (nativeView.MeasuredWidth, nativeView.MeasuredHeight));
}
```

Questo metodo utilizza la larghezza fornita dal `CustomControl.OnMeasure` (metodo), ma il risultato viene moltiplicato per due. Quando il `CustomControl` istanza viene aggiunto al [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), `FixSize` metodo può essere specificato come il `GetDesiredSizeDelegate` correggere la misura non valida fornita dal `CustomControl` classe:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

In questo modo la visualizzazione personalizzata che viene visualizzato correttamente, che occupano la larghezza del dispositivo, come illustrato nella schermata seguente:

![](code-images/android-good-measurement.png "Android CustomControl con il delegato GetDesiredSize personalizzato")

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Nell'esempio di codice riportato di seguito viene illustrato il `CustomControl` (classe), che eredita da `Panel`:

```csharp
public class CustomControl : Panel
{
  public static readonly DependencyProperty TextProperty =
    DependencyProperty.Register(
      "Text", typeof(string), typeof(CustomControl), new PropertyMetadata(default(string), OnTextPropertyChanged));

  public string Text
  {
    get { return (string)GetValue(TextProperty); }
    set { SetValue(TextProperty, value.ToUpper()); }
  }

  readonly TextBlock textBlock;

  public CustomControl()
  {
    textBlock = new TextBlock
    {
      MinHeight = 0,
      MaxHeight = double.PositiveInfinity,
      MinWidth = 0,
      MaxWidth = double.PositiveInfinity,
      FontSize = 14,
      TextWrapping = TextWrapping.Wrap,
      VerticalAlignment = VerticalAlignment.Center
    };

    Children.Add(textBlock);
  }

  static void OnTextPropertyChanged(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs args)
  {
    ((CustomControl)dependencyObject).textBlock.Text = (string)args.NewValue;
  }

  protected override Size ArrangeOverride(Size finalSize)
  {
      // This is deliberately wrong to demonstrate providing an override to fix it with.
      textBlock.Arrange(new Rect(0, 0, finalSize.Width/2, finalSize.Height));
      return finalSize;
  }

  protected override Size MeasureOverride(Size availableSize)
  {
      textBlock.Measure(availableSize);
      return new Size(textBlock.DesiredSize.Width, textBlock.DesiredSize.Height);
  }
}
```

Un'istanza di questa vista viene aggiunta a un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), come illustrato nell'esempio di codice seguente:

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

Tuttavia, poiché il `CustomControl.ArrangeOverride` override restituisce sempre la metà della larghezza della richiesta, la vista verrà troncata a metà della larghezza disponibile del dispositivo, come illustrato nella schermata seguente:

![](code-images/winrt-bad-measurement.png "CustomControl UWP con implementazione ArrangeOverride non valido")

Una soluzione al problema consiste nel fornire un `ArrangeOverrideDelegate` implementazione, quando si aggiunge la visualizzazione di [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), come illustrato nell'esempio di codice seguente:

```csharp
stackLayout.Children.Add(fixedControl, arrangeOverrideDelegate: (renderer, finalSize) =>
{
    if (finalSize.Width <= 0 || double.IsInfinity(finalSize.Width))
    {
        return null;
    }
    var frameworkElement = renderer.Control;
    frameworkElement.Arrange(new Rect(0, 0, finalSize.Width * 2, finalSize.Height));
    return finalSize;
});
```

Questo metodo utilizza la larghezza fornita dal `CustomControl.ArrangeOverride` (metodo), ma il risultato viene moltiplicato per due. In questo modo la visualizzazione personalizzata che viene visualizzato correttamente, che occupano la larghezza del dispositivo, come illustrato nella schermata seguente:

![](code-images/winrt-good-measurement.png "CustomControl UWP con il delegato ArrangeOverride")

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come aggiungere viste native a un layout di xamarin. Forms creato in c# e come eseguire l'override di layout di visualizzazioni personalizzate per correggere i misurazione dell'utilizzo delle API.


## <a name="related-links"></a>Collegamenti correlati

- [NativeEmbedding (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeEmbedding/)
- [Form nativi](~/xamarin-forms/platform/native-forms.md)
