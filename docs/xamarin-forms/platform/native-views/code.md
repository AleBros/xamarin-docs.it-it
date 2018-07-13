---
title: Visualizzazioni native in c#
description: Visualizzazioni native da iOS, Android e UWP possono farvi direttamente dalle pagine di xamarin. Forms create utilizzando c#. Questo articolo illustra come aggiungere le visualizzazioni native in un layout di xamarin. Forms creato utilizzando c# e come eseguire l'override di layout di visualizzazioni personalizzate per correggere le unità di misura all'utilizzo dell'API.
ms.prod: xamarin
ms.assetid: 230F937C-F914-4B21-8EA1-1A2A9E644769
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: ad633f49c1c448529fa4c2b50483ec233c1ee841
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996194"
---
# <a name="native-views-in-c"></a>Visualizzazioni native in c#

_Visualizzazioni native da iOS, Android e UWP possono farvi direttamente dalle pagine di xamarin. Forms create utilizzando c#. Questo articolo illustra come aggiungere le visualizzazioni native in un layout di xamarin. Forms creato utilizzando c# e come eseguire l'override di layout di visualizzazioni personalizzate per correggere le unità di misura all'utilizzo dell'API._

## <a name="overview"></a>Panoramica

Qualsiasi controllo di xamarin. Forms che consente `Content` per impostare, o che dispone di un `Children` raccolta, è possibile aggiungere le visualizzazioni specifiche della piattaforma. Ad esempio, iOS `UILabel` possono essere aggiunti direttamente al [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) proprietà, oppure al [ `StackLayout.Children` ](xref:Xamarin.Forms.Layout`1.Children) raccolta. Si noti tuttavia che questa funzionalità richiede l'uso di `#if` definisce nelle soluzioni xamarin. Forms progetto condiviso e non è disponibile da soluzioni di libreria xamarin. Forms .NET Standard.

Gli screenshot seguenti illustrano le viste specifiche della piattaforma è stato aggiunto a un xamarin. Forms [ `StackLayout` ](xref:Xamarin.Forms.StackLayout):

[![](code-images/screenshots-sml.png "StackLayout che contiene le viste specifiche della piattaforma")](code-images/screenshots.png#lightbox "StackLayout che contiene le viste specifiche della piattaforma")

La possibilità di aggiungere le visualizzazioni specifiche della piattaforma a un layout di xamarin. Forms è abilitata per due metodi di estensione in ogni piattaforma:

- `Add` : aggiunge una visualizzazione specifica della piattaforma per la [ `Children` ](xref:Xamarin.Forms.Layout`1.Children) del layout di una raccolta.
- `ToView` – accetta una visualizzazione specifica della piattaforma e ne esegue il wrapping come un xamarin. Forms [ `View` ](xref:Xamarin.Forms.View) che possono essere impostati come il `Content` proprietà di un controllo.

Uso di questi metodi in un progetto condiviso di xamarin. Forms richiede l'importazione di spazio dei nomi di xamarin. Forms appropriato del tipo specifico della piattaforma:

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Universal Windows Platform (UWP)** – Xamarin.Forms.Platform.UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>Aggiunta di visualizzazioni specifiche della piattaforma in ogni piattaforma

Le sezioni seguenti illustrano come aggiungere viste specifiche della piattaforma a un layout di xamarin. Forms in ogni piattaforma.

### <a name="ios"></a>iOS

Esempio di codice seguente viene illustrato come aggiungere un `UILabel` a un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) e un [ `ContentView` ](xref:Xamarin.Forms.ContentView):

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

L'esempio presuppone che il `stackLayout` e `contentView` istanze precedentemente create in XAML o c#.

### <a name="android"></a>Android

Esempio di codice seguente viene illustrato come aggiungere un `TextView` a un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) e un [ `ContentView` ](xref:Xamarin.Forms.ContentView):

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

L'esempio presuppone che il `stackLayout` e `contentView` istanze precedentemente create in XAML o c#.

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Esempio di codice seguente viene illustrato come aggiungere un `TextBlock` a un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) e un [ `ContentView` ](xref:Xamarin.Forms.ContentView):

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

L'esempio presuppone che il `stackLayout` e `contentView` istanze precedentemente create in XAML o c#.

## <a name="overriding-platform-measurements-for-custom-views"></a>Esegue l'override delle misurazioni di piattaforma per le visualizzazioni personalizzate

Visualizzazioni personalizzate in ogni piattaforma implementano spesso solo correttamente l'unità di misura per lo scenario di layout per il quale sono stati progettati. Ad esempio, una visualizzazione personalizzata può sono stata progettata in modo che occupi solo la metà della larghezza disponibile del dispositivo. Tuttavia, dopo aver condiviso con altri utenti, la visualizzazione personalizzata potrebbe essere necessaria a occupare larghezza completa disponibile del dispositivo. Pertanto, può essere necessario eseguire l'override di un'implementazione di misurazione di visualizzazioni personalizzate quando vengono riutilizzate in un layout di xamarin. Forms. Per questo motivo, il `Add` e `ToView` i metodi di estensione offrono override che consentono di delegati di misurazione specificare quale possono eseguire l'override di layout di visualizzazione personalizzato quando viene aggiunto a un layout di xamarin. Forms.

Le sezioni seguenti illustrano come eseguire l'override di layout di visualizzazioni personalizzate, per correggere le unità di misura all'utilizzo dell'API.

### <a name="ios"></a>iOS

Nell'esempio di codice riportato di seguito viene illustrato il `CustomControl` classe che eredita da `UILabel`:

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

Un'istanza di questa vista viene aggiunta a un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), come illustrato nell'esempio di codice seguente:

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

Tuttavia, poiché il `CustomControl.SizeThatFits` override restituisce sempre un'altezza pari a 150, verrà visualizzata la visualizzazione con uno spazio vuoto di sopra e sotto di essa, come illustrato nello screenshot seguente:

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

Questo metodo Usa la larghezza fornita dal `CustomControl.SizeThatFits` (metodo), ma sostituisce l'altezza pari a 150 per un'altezza pari a 70. Quando la `CustomControl` istanza viene aggiunta al [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), il `FixSize` metodo può essere specificato come la `GetDesiredSizeDelegate` per correggere la misurazione non valida fornita dal `CustomControl` classe:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Ciò comporta la visualizzazione personalizzata viene visualizzata correttamente, senza uno spazio vuoto di sopra e sotto di essa, come illustrato nello screenshot seguente:

![](code-images/ios-good-measurement.png "iOS CustomControl con GetDesiredSize Override")

### <a name="android"></a>Android

Nell'esempio di codice riportato di seguito viene illustrato il `CustomControl` classe che eredita da `TextView`:

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

Un'istanza di questa vista viene aggiunta a un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), come illustrato nell'esempio di codice seguente:

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

Tuttavia, poiché il `CustomControl.OnMeasure` override restituisce sempre la metà della larghezza della richiesta, la vista verrà visualizzata che occupa solo metà della larghezza disponibile del dispositivo, come illustrato nello screenshot seguente:

![](code-images/android-bad-measurement.png "Android CustomControl con implementazione OnMeasure non valida")

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

Questo metodo Usa la larghezza fornita dal `CustomControl.OnMeasure` (metodo), ma lo moltiplica per due. Quando la `CustomControl` istanza viene aggiunta al [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), il `FixSize` metodo può essere specificato come la `GetDesiredSizeDelegate` per correggere la misurazione non valida fornita dal `CustomControl` classe:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

In questo modo la visualizzazione personalizzata che viene visualizzata correttamente, che occupano la larghezza del dispositivo, come illustrato nello screenshot seguente:

![](code-images/android-good-measurement.png "CustomControl Android con il delegato GetDesiredSize personalizzato")

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Nell'esempio di codice riportato di seguito viene illustrato il `CustomControl` classe che eredita da `Panel`:

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

Un'istanza di questa vista viene aggiunta a un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), come illustrato nell'esempio di codice seguente:

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

Tuttavia, poiché il `CustomControl.ArrangeOverride` override restituisce sempre la metà della larghezza della richiesta, la visualizzazione verrà ritagliata in base a metà larghezza disponibile del dispositivo, come illustrato nello screenshot seguente:

![](code-images/winrt-bad-measurement.png "CustomControl UWP con l'implementazione ArrangeOverride non valida")

Una soluzione al problema consiste nel fornire un' `ArrangeOverrideDelegate` implementazione, quando si aggiunge la visualizzazione per il [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), come illustrato nell'esempio di codice seguente:

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

Questo metodo Usa la larghezza fornita dal `CustomControl.ArrangeOverride` (metodo), ma lo moltiplica per due. In questo modo la visualizzazione personalizzata che viene visualizzata correttamente, che occupano la larghezza del dispositivo, come illustrato nello screenshot seguente:

![](code-images/winrt-good-measurement.png "CustomControl UWP con il delegato ArrangeOverride")

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come aggiungere le visualizzazioni native in un layout di xamarin. Forms creato utilizzando c# e come eseguire l'override di layout di visualizzazioni personalizzate per correggere le unità di misura all'utilizzo dell'API.


## <a name="related-links"></a>Collegamenti correlati

- [NativeEmbedding (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeEmbedding/)
- [Form nativi](~/xamarin-forms/platform/native-forms.md)
