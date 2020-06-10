---
title: "viste native in C#" Description: "è possibile fare riferimento direttamente alle visualizzazioni native da iOS, Android e UWP dalle Xamarin.Forms pagine create con c#. Questo articolo illustra come aggiungere viste native a un Xamarin.Forms layout creato con C# e come eseguire l'override del layout delle visualizzazioni personalizzate per correggere l'utilizzo dell'API di misurazione ".
ms. prod: Novell MS. AssetID: 230F937C-F914-4B21-8EA1-1A2A9E644769 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 04/27/2016 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="native-views-in-c"></a>Viste native in C\#

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)

_È possibile fare riferimento direttamente alle visualizzazioni native di iOS, Android e UWP dalle Xamarin.Forms pagine create con C#. Questo articolo illustra come aggiungere viste native a un Xamarin.Forms layout creato con C# e come eseguire l'override del layout delle visualizzazioni personalizzate per correggere l'utilizzo dell'API di misurazione._

## <a name="overview"></a>Panoramica

Qualsiasi Xamarin.Forms controllo che consente `Content` di impostare o che dispone di una `Children` raccolta può aggiungere visualizzazioni specifiche della piattaforma. Ad esempio, un iOS `UILabel` può essere aggiunto direttamente alla [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) proprietà o alla [`StackLayout.Children`](xref:Xamarin.Forms.Layout`1.Children) raccolta. Si noti tuttavia che questa funzionalità richiede l'uso di `#if` definizioni nelle Xamarin.Forms soluzioni di progetto condivise e non è disponibile dalle Xamarin.Forms soluzioni di libreria .NET standard.

Gli screenshot seguenti illustrano le visualizzazioni specifiche della piattaforma aggiunte a un Xamarin.Forms [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

[![](code-images/screenshots-sml.png "StackLayout Containing Platform-Specific Views")](code-images/screenshots.png#lightbox "StackLayout Containing Platform-Specific Views")

La possibilità di aggiungere visualizzazioni specifiche della piattaforma a un Xamarin.Forms layout è abilitata da due metodi di estensione in ogni piattaforma:

- `Add`: aggiunge una visualizzazione specifica della piattaforma alla [`Children`](xref:Xamarin.Forms.Layout`1.Children) raccolta di un layout.
- `ToView`: accetta una visualizzazione specifica della piattaforma e la esegue il wrapping come oggetto Xamarin.Forms [`View`](xref:Xamarin.Forms.View) che può essere impostato come `Content` proprietà di un controllo.

L'uso di questi metodi in un Xamarin.Forms progetto condiviso richiede l'importazione dello Xamarin.Forms spazio dei nomi specifico della piattaforma appropriato:

- **iOS** : Xamarin.Forms . Platform. iOS
- **Android** : Xamarin.Forms . Platform. Android
- **Piattaforma UWP (Universal Windows Platform) (UWP)** - Xamarin.Forms . Platform. UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>Aggiunta di visualizzazioni specifiche della piattaforma in ogni piattaforma

Le sezioni seguenti illustrano come aggiungere viste specifiche della piattaforma a un Xamarin.Forms layout in ogni piattaforma.

### <a name="ios"></a>iOS

Nell'esempio di codice seguente viene illustrato come aggiungere un oggetto `UILabel` a un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) e un oggetto [`ContentView`](xref:Xamarin.Forms.ContentView) :

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

Nell'esempio si presuppone che `stackLayout` le `contentView` istanze e siano state create in precedenza in XAML o C#.

### <a name="android"></a>Android

Nell'esempio di codice seguente viene illustrato come aggiungere un oggetto `TextView` a un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) e un oggetto [`ContentView`](xref:Xamarin.Forms.ContentView) :

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

Nell'esempio si presuppone che `stackLayout` le `contentView` istanze e siano state create in precedenza in XAML o C#.

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Nell'esempio di codice seguente viene illustrato come aggiungere un oggetto `TextBlock` a un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) e un oggetto [`ContentView`](xref:Xamarin.Forms.ContentView) :

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

Nell'esempio si presuppone che `stackLayout` le `contentView` istanze e siano state create in precedenza in XAML o C#.

## <a name="overriding-platform-measurements-for-custom-views"></a>Override delle misurazioni della piattaforma per le visualizzazioni personalizzate

Le visualizzazioni personalizzate in ogni piattaforma spesso implementano correttamente solo la misurazione dello scenario di layout per cui sono state progettate. Ad esempio, una visualizzazione personalizzata potrebbe essere stata progettata per occupare solo metà della larghezza disponibile del dispositivo. Tuttavia, dopo la condivisione con altri utenti, potrebbe essere necessario che la visualizzazione personalizzata occupi la larghezza completa disponibile del dispositivo. Pertanto, può essere necessario eseguire l'override di un'implementazione di misurazione delle visualizzazioni personalizzate quando viene riutilizzato in un Xamarin.Forms layout. Per questo motivo, i `Add` `ToView` metodi di estensione e forniscono sostituzioni che consentono di specificare delegati di misurazione, che possono eseguire l'override del layout della visualizzazione personalizzata quando viene aggiunto a un Xamarin.Forms layout.

Le sezioni seguenti illustrano come eseguire l'override del layout delle visualizzazioni personalizzate per correggere l'utilizzo dell'API di misurazione.

### <a name="ios"></a>iOS

Nell'esempio di codice seguente viene illustrata la `CustomControl` classe, che eredita da `UILabel` :

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

Un'istanza di questa vista viene aggiunta a un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) , come illustrato nell'esempio di codice seguente:

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

Tuttavia, poiché la `CustomControl.SizeThatFits` sostituzione restituisce sempre un'altezza di 150, la vista verrà visualizzata con uno spazio vuoto al di sopra e al di sotto di esso, come illustrato nello screenshot seguente:

![](code-images/ios-bad-measurement.png "iOS CustomControl with Bad SizeThatFits Implementation")

Una soluzione a questo problema consiste nel fornire un' `GetDesiredSizeDelegate` implementazione di, come illustrato nell'esempio di codice seguente:

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

Questo metodo usa la larghezza fornita dal `CustomControl.SizeThatFits` metodo, ma sostituisce l'altezza di 150 per un'altezza di 70. Quando l' `CustomControl` istanza di viene aggiunta a [`StackLayout`](xref:Xamarin.Forms.StackLayout) , il `FixSize` metodo può essere specificato come `GetDesiredSizeDelegate` per correggere la misurazione non valida fornita dalla `CustomControl` classe:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Ciò comporta la visualizzazione corretta della visualizzazione personalizzata, senza spazio vuoto al di sopra e al di sotto di esso, come illustrato nello screenshot seguente:

![](code-images/ios-good-measurement.png "iOS CustomControl with GetDesiredSize Override")

### <a name="android"></a>Android

Nell'esempio di codice seguente viene illustrata la `CustomControl` classe, che eredita da `TextView` :

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

Un'istanza di questa vista viene aggiunta a un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) , come illustrato nell'esempio di codice seguente:

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

Tuttavia, poiché l' `CustomControl.OnMeasure` override restituisce sempre metà della larghezza richiesta, la vista verrà visualizzata occupando solo metà della larghezza disponibile del dispositivo, come illustrato nello screenshot seguente:

![](code-images/android-bad-measurement.png "Android CustomControl with Bad OnMeasure Implementation")

Una soluzione a questo problema consiste nel fornire un' `GetDesiredSizeDelegate` implementazione di, come illustrato nell'esempio di codice seguente:

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

Questo metodo usa la larghezza fornita dal `CustomControl.OnMeasure` metodo, ma lo moltiplica per due. Quando l' `CustomControl` istanza di viene aggiunta a [`StackLayout`](xref:Xamarin.Forms.StackLayout) , il `FixSize` metodo può essere specificato come `GetDesiredSizeDelegate` per correggere la misurazione non valida fornita dalla `CustomControl` classe:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Ciò comporta la visualizzazione corretta della visualizzazione personalizzata, che occupa la larghezza del dispositivo, come illustrato nello screenshot seguente:

![](code-images/android-good-measurement.png "Android CustomControl with Custom GetDesiredSize Delegate")

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Nell'esempio di codice seguente viene illustrata la `CustomControl` classe, che eredita da `Panel` :

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

Un'istanza di questa vista viene aggiunta a un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) , come illustrato nell'esempio di codice seguente:

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

Tuttavia, poiché l' `CustomControl.ArrangeOverride` override restituisce sempre metà della larghezza richiesta, la vista verrà ritagliata in metà della larghezza disponibile del dispositivo, come illustrato nello screenshot seguente:

![](code-images/winrt-bad-measurement.png "UWP CustomControl with Bad ArrangeOverride Implementation")

Una soluzione a questo problema consiste nel fornire un' `ArrangeOverrideDelegate` implementazione di quando si aggiunge la visualizzazione a [`StackLayout`](xref:Xamarin.Forms.StackLayout) , come illustrato nell'esempio di codice seguente:

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

Questo metodo usa la larghezza fornita dal `CustomControl.ArrangeOverride` metodo, ma lo moltiplica per due. Ciò comporta la visualizzazione corretta della visualizzazione personalizzata, che occupa la larghezza del dispositivo, come illustrato nello screenshot seguente:

![](code-images/winrt-good-measurement.png "UWP CustomControl with ArrangeOverride Delegate")

## <a name="summary"></a>Summary

Questo articolo ha illustrato come aggiungere viste native a un Xamarin.Forms layout creato con C# e come eseguire l'override del layout delle visualizzazioni personalizzate per correggere l'utilizzo dell'API di misurazione.

## <a name="related-links"></a>Collegamenti correlati

- [NativeEmbedding (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)
- [Form nativi](~/xamarin-forms/platform/native-forms.md)
