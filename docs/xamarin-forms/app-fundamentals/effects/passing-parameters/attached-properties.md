---
title: Passaggio di parametri effetto come proprietà associate
description: Le proprietà associate sono utilizzabile per definire i parametri di effetto che rispondono a modifiche alle proprietà runtime. Questo articolo illustra l'uso di proprietà per passare i parametri per un effetto e la modifica di un parametro in fase di esecuzione associate.
ms.prod: xamarin
ms.assetid: DFCDCB9F-17DD-4117-BD53-B4FB206BB387
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 9483e424a74a88ce3f0eb49624bb5315551f2062
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996451"
---
# <a name="passing-effect-parameters-as-attached-properties"></a>Passaggio di parametri effetto come proprietà associate

_Le proprietà associate sono utilizzabile per definire i parametri di effetto che rispondono a modifiche alle proprietà runtime. Questo articolo illustra l'uso di proprietà per passare i parametri per un effetto e la modifica di un parametro in fase di esecuzione associate._

Il processo di creazione dei parametri di effetto che rispondono a modifiche alle proprietà runtime è come segue:

1. Creare un `static` classe che contiene una proprietà associata per ogni parametro deve essere passato l'effetto.
1. Aggiungere una proprietà aggiuntiva associata alla classe che verrà usata per controllare l'aggiunta o la rimozione dell'effetto al controllo che verrà associata alla classe. Verificare che questo collegato proprietà registri un `propertyChanged` delegato che verrà eseguito quando viene modificato il valore della proprietà.
1. Creare `static` getter e setter per ogni proprietà associata.
1. Implementare la logica nel `propertyChanged` delegato da aggiungere e rimuovere l'effetto.
1. Implementare una classe annidata all'interno di `static` classe, denominata in base all'effetto che crea una sottoclasse di `RoutingEffect` classe. Per il costruttore, chiamare il costruttore di classe di base, passando una concatenazione del nome del gruppo di risoluzione e l'ID univoco che è stata specificata in ogni classe effetto specifico della piattaforma.

I parametri possono quindi essere passati all'effetto aggiungendo le proprietà associate e i valori delle proprietà per il controllo appropriato. Inoltre, i parametri possono essere modificati in fase di esecuzione specificando un nuovo valore della proprietà associata.

> [!NOTE]
> Una proprietà associata è un tipo speciale di proprietà associabile, definita in una classe ma collegato ad altri oggetti e che sia riconoscibile in XAML come attributi che contengono una classe e un nome di proprietà separati da un punto. Per altre informazioni, vedere [collegati proprietà](~/xamarin-forms/xaml/attached-properties.md).

L'applicazione di esempio viene illustrato un `ShadowEffect` che aggiunge un'ombreggiatura per il testo visualizzato da un [ `Label` ](xref:Xamarin.Forms.Label) controllo. Inoltre, il colore dell'ombreggiatura può essere modificato in fase di esecuzione. Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con le relazioni tra di essi:

![](attached-properties-images/shadow-effect.png "Responsabilità di progetto effetto ombreggiatura")

Oggetto [ `Label` ](xref:Xamarin.Forms.Label) control per il `HomePage` personalizzato dal `LabelShadowEffect` in ogni progetto specifico della piattaforma. I parametri vengono passati a ciascun `LabelShadowEffect` tramite le proprietà associate nel `ShadowEffect` classe. Ciascuna `LabelShadowEffect` deriva dalla classe di `PlatformEffect` classe per ogni piattaforma. Il risultato è un'ombreggiatura da aggiungere al testo visualizzato per il `Label` controllare, come illustrato negli screenshot seguenti:

![](attached-properties-images/screenshots.png "Effetto di ombreggiatura per ogni piattaforma")

## <a name="creating-effect-parameters"></a>Creazione di parametri di effetto

Oggetto `static` classe deve essere creata per rappresentare i parametri di effetto, come illustrato nell'esempio di codice seguente:

```csharp
public static class ShadowEffect
{
  public static readonly BindableProperty HasShadowProperty =
    BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false, propertyChanged: OnHasShadowChanged);
  public static readonly BindableProperty ColorProperty =
    BindableProperty.CreateAttached ("Color", typeof(Color), typeof(ShadowEffect), Color.Default);
  public static readonly BindableProperty RadiusProperty =
    BindableProperty.CreateAttached ("Radius", typeof(double), typeof(ShadowEffect), 1.0);
  public static readonly BindableProperty DistanceXProperty =
    BindableProperty.CreateAttached ("DistanceX", typeof(double), typeof(ShadowEffect), 0.0);
  public static readonly BindableProperty DistanceYProperty =
    BindableProperty.CreateAttached ("DistanceY", typeof(double), typeof(ShadowEffect), 0.0);

  public static bool GetHasShadow (BindableObject view)
  {
    return (bool)view.GetValue (HasShadowProperty);
  }

  public static void SetHasShadow (BindableObject view, bool value)
  {
    view.SetValue (HasShadowProperty, value);
  }
  ...

  static void OnHasShadowChanged (BindableObject bindable, object oldValue, object newValue)
  {
    var view = bindable as View;
    if (view == null) {
      return;
    }

    bool hasShadow = (bool)newValue;
    if (hasShadow) {
      view.Effects.Add (new LabelShadowEffect ());
    } else {
      var toRemove = view.Effects.FirstOrDefault (e => e is LabelShadowEffect);
      if (toRemove != null) {
        view.Effects.Remove (toRemove);
      }
    }
  }

  class LabelShadowEffect : RoutingEffect
  {
    public LabelShadowEffect () : base ("MyCompany.LabelShadowEffect")
    {
    }
  }
}
```

Il `ShadowEffect` contiene cinque proprietà associate, con `static` getter e setter per ogni proprietà associata. Quattro di tali proprietà rappresentano i parametri da passare per ogni specifico della piattaforma `LabelShadowEffect`. Il `ShadowEffect` classe definisce anche una `HasShadow` proprietà che viene usato per controllare l'aggiunta o la rimozione dell'effetto di controllo associata che il `ShadowEffect` classe è associata a. Ciò collegato proprietà registri il `OnHasShadowChanged` metodo che verrà eseguito quando viene modificato il valore della proprietà. Questo metodo aggiunge o rimuove l'effetto in base al valore della `HasShadow` proprietà associata.

Annidato `LabelShadowEffect` classe, che crea una sottoclasse di [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) classe supporta l'effettiva aggiunta e rimozione. Il `RoutingEffect` classe rappresenta un effetto indipendente dalla piattaforma che esegue il wrapping di un effetto interno che è in genere specifica della piattaforma. Ciò semplifica il processo di rimozione effetto, poiché non è possibile accedere in fase di compilazione per le informazioni sul tipo per un effetto specifico della piattaforma. Il `LabelShadowEffect` costruttore chiama il costruttore di classe di base, passando un parametro costituito da una concatenazione del nome del gruppo di risoluzione e l'ID univoco che è stata specificata in ogni classe effetto specifico della piattaforma. In questo modo l'effettiva aggiunta e rimozione nel `OnHasShadowChanged` (metodo), come indicato di seguito:

- **Effetti di addizione** – una nuova istanza della `LabelShadowEffect` viene aggiunto al controllo [ `Effects` ](xref:Xamarin.Forms.Element.Effects) raccolta. Questo strumento sostituisce usando il [ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String)) metodo per aggiungere l'effetto.
- **Effetto la rimozione** : la prima istanza del `LabelShadowEffect` del controllo [ `Effects` ](xref:Xamarin.Forms.Element.Effects) raccolta viene recuperata e rimosso.

## <a name="consuming-the-effect"></a>L'effetto di utilizzo

Ogni specifica della piattaforma `LabelShadowEffect` possono essere usati aggiungendo le proprietà associate a un [ `Label` ](xref:Xamarin.Forms.Label) controllare, come illustrato nell'esempio di codice XAML seguente:

```xaml
<Label Text="Label Shadow Effect" ...
       local:ShadowEffect.HasShadow="true" local:ShadowEffect.Radius="5"
       local:ShadowEffect.DistanceX="5" local:ShadowEffect.DistanceY="5">
  <local:ShadowEffect.Color>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="Black" />
        <On Platform="Android" Value="White" />
        <On Platform="UWP" Value="Red" />
    </OnPlatform>
  </local:ShadowEffect.Color>
</Label>
```

L'equivalente [ `Label` ](xref:Xamarin.Forms.Label) nel linguaggio c# viene illustrato nell'esempio di codice seguente:

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};

Color color = Color.Default;
switch (Device.RuntimePlatform)
{
    case Device.iOS:
        color = Color.Black;
        break;
    case Device.Android:
        color = Color.White;
        break;
    case Device.UWP:
        color = Color.Red;
        break;
}

ShadowEffect.SetHasShadow (label, true);
ShadowEffect.SetRadius (label, 5);
ShadowEffect.SetDistanceX (label, 5);
ShadowEffect.SetDistanceY (label, 5);
ShadowEffect.SetColor (label, color));
```

Impostando il `ShadowEffect.HasShadow` proprietà associata `true` esegue il `ShadowEffect.OnHasShadowChanged` metodo che aggiunge o rimuove la `LabelShadowEffect` per il [ `Label` ](xref:Xamarin.Forms.Label) controllo. In entrambi gli esempi di codice, il `ShadowEffect.Color` proprietà associata fornisce i valori di colore specifici della piattaforma. Per altre informazioni, vedere [classe Device](~/xamarin-forms/platform/device.md).

Inoltre, un [ `Button` ](xref:Xamarin.Forms.Button) consente il colore dell'ombreggiatura essere modificato in fase di esecuzione. Quando la `Button` viene selezionata, le seguenti modifiche al codice colore dell'ombreggiatura impostando il `ShadowEffect.Color` proprietà associata:

```csharp
ShadowEffect.SetColor (label, Color.Teal);
```

### <a name="consuming-the-effect-with-a-style"></a>Utilizzo l'effetto con uno stile

Gli effetti che possono essere usati aggiungendo le proprietà associate a un controllo possono essere utilizzati anche da uno stile. L'esempio di codice XAML seguente mostra un' *esplicite* stile per l'effetto di ombreggiatura, che può essere applicato a [ `Label` ](xref:Xamarin.Forms.Label) controlli:

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="True" />
    <Setter Property="local:ShadowEffect.Radius" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceX" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceY" Value="5" />
  </Style.Setters>
</Style>
```

Il [ `Style` ](xref:Xamarin.Forms.Style) può essere applicato a un [ `Label` ](xref:Xamarin.Forms.Label) impostando relativo [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) proprietà per il `Style` istanza utilizzando il `StaticResource`estensione di markup, come illustrato nell'esempio di codice seguente:

```xaml
<Label Text="Label Shadow Effect" ... Style="{StaticResource ShadowEffectStyle}" />
```

Per altre informazioni sugli stili, vedere [stili](~/xamarin-forms/user-interface/styles/index.md).

## <a name="creating-the-effect-on-each-platform"></a>Creare l'effetto in ogni piattaforma

Le sezioni seguenti illustrano l'implementazione specifica della piattaforma del `LabelShadowEffect` classe.

### <a name="ios-project"></a>Progetto iOS

Nell'esempio di codice riportato di seguito viene illustrato il `LabelShadowEffect` implementazione per il progetto iOS:

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                Control.Layer.ShadowOpacity = 1.0f;
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateRadius ()
        {
            Control.Layer.CornerRadius = (nfloat)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            Control.Layer.ShadowColor = ShadowEffect.GetColor (Element).ToCGColor ();
        }

        void UpdateOffset ()
        {
            Control.Layer.ShadowOffset = new CGSize (
                (double)ShadowEffect.GetDistanceX (Element),
                (double)ShadowEffect.GetDistanceY (Element));
        }
    }
```

Il `OnAttached` metodo chiama i metodi che recuperano i valori della proprietà associata utilizzando il `ShadowEffect` getter e quali impostare `Control.Layer` le proprietà per i valori delle proprietà per creare l'ombreggiatura. Questa funzionalità viene eseguito il wrapping un `try` / `catch` blocco nel caso in cui il controllo cui è collegata l'effetto non dispone di `Control.Layer` proprietà. Nessuna implementazione avviene tramite il `OnDetached` metodo perché non è necessaria alcuna operazione di pulizia.

#### <a name="responding-to-property-changes"></a>Rispondere alle modifiche delle proprietà

Se una qualsiasi del `ShadowEffect` collegato modifica i valori della proprietà in fase di esecuzione, l'effetto dovrà rispondere visualizzando le modifiche. Una versione override del `OnElementPropertyChanged` metodo nella classe effetto specifico della piattaforma, è possibile rispondere alle modifiche delle proprietà associabile, come illustrato nell'esempio di codice seguente:

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

Il `OnElementPropertyChanged` metodo aggiorna il raggio, al colore o offset dell'ombreggiatura, a condizione che l'oggetto appropriato `ShadowEffect` valore della proprietà associata è stato modificato. Un controllo per la proprietà viene modificata deve essere sempre eseguito, come la sostituzione può essere chiamata più volte.

### <a name="android-project"></a>Progetto Android

Nell'esempio di codice riportato di seguito viene illustrato il `LabelShadowEffect` implementazione per il progetto Android:

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        Android.Widget.TextView control;
        Android.Graphics.Color color;
        float radius, distanceX, distanceY;

        protected override void OnAttached ()
        {
            try {
                control = Control as Android.Widget.TextView;
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                UpdateControl ();
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateControl ()
        {
            if (control != null) {
                control.SetShadowLayer (radius, distanceX, distanceY, color);
            }
        }

        void UpdateRadius ()
        {
            radius = (float)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            color = ShadowEffect.GetColor (Element).ToAndroid ();
        }

        void UpdateOffset ()
        {
            distanceX = (float)ShadowEffect.GetDistanceX (Element);
            distanceY = (float)ShadowEffect.GetDistanceY (Element);
        }
    }
```

Il `OnAttached` metodo chiama i metodi che recuperano i valori della proprietà associata utilizzando il `ShadowEffect` getter e chiama un metodo che chiama il [ `TextView.SetShadowLayer` ](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/) metodo per creare un'ombreggiatura usando i valori delle proprietà. Questa funzionalità viene eseguito il wrapping un `try` / `catch` blocco nel caso in cui il controllo cui è collegata l'effetto non dispone di `Control.Layer` proprietà. Nessuna implementazione avviene tramite il `OnDetached` metodo perché non è necessaria alcuna operazione di pulizia.

#### <a name="responding-to-property-changes"></a>Rispondere alle modifiche delle proprietà

Se una qualsiasi del `ShadowEffect` collegato modifica i valori della proprietà in fase di esecuzione, l'effetto dovrà rispondere visualizzando le modifiche. Una versione override del `OnElementPropertyChanged` metodo nella classe effetto specifico della piattaforma, è possibile rispondere alle modifiche delle proprietà associabile, come illustrato nell'esempio di codice seguente:

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
      UpdateControl ();
    }
  }
  ...
}
```

Il `OnElementPropertyChanged` metodo aggiorna il raggio, al colore o offset dell'ombreggiatura, a condizione che l'oggetto appropriato `ShadowEffect` valore della proprietà associata è stato modificato. Un controllo per la proprietà viene modificata deve essere sempre eseguito, come la sostituzione può essere chiamata più volte.

### <a name="universal-windows-platform-project"></a>Progetto della piattaforma Windows universale

Nell'esempio di codice riportato di seguito viene illustrato il `LabelShadowEffect` implementazione per il progetto Universal Windows Platform (UWP):

```csharp
[assembly: ResolutionGroupName ("MyCompany")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        Label shadowLabel;
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;

                    shadowLabel = new Label ();
                    shadowLabel.Text = textBlock.Text;
                    shadowLabel.FontAttributes = FontAttributes.Bold;
                    shadowLabel.HorizontalOptions = LayoutOptions.Center;
                    shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;

                    UpdateColor ();
                    UpdateOffset ();

                    ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                    shadowAdded = true;
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateColor ()
        {
            shadowLabel.TextColor = ShadowEffect.GetColor (Element);
        }

        void UpdateOffset ()
        {
            shadowLabel.TranslationX = ShadowEffect.GetDistanceX (Element);
            shadowLabel.TranslationY = ShadowEffect.GetDistanceY (Element);
        }
    }
}
```

La piattaforma Windows universale non fornisce un effetto di ombreggiatura e pertanto `LabelShadowEffect` implementazione su entrambe le piattaforme simula uno tramite l'aggiunta di un offset secondo [ `Label` ](xref:Xamarin.Forms.Label) dietro quelle primarie `Label`. Il `OnAttached` crea il nuovo metodo `Label` e impostando alcune proprietà layout sul `Label`. Quindi chiama i metodi che recuperano i valori della proprietà associata utilizzando il `ShadowEffect` getter e crea l'ombreggiatura impostando la [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor), [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)e [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) delle proprietà per controllare il colore e il percorso del `Label`. Il `shadowLabel` viene quindi inserito offset dietro quelle primarie `Label`. Questa funzionalità viene eseguito il wrapping un `try` / `catch` blocco nel caso in cui il controllo cui è collegata l'effetto non dispone di `Control.Layer` proprietà. Nessuna implementazione avviene tramite il `OnDetached` metodo perché non è necessaria alcuna operazione di pulizia.

#### <a name="responding-to-property-changes"></a>Rispondere alle modifiche delle proprietà

Se una qualsiasi del `ShadowEffect` collegato modifica i valori della proprietà in fase di esecuzione, l'effetto dovrà rispondere visualizzando le modifiche. Una versione override del `OnElementPropertyChanged` metodo nella classe effetto specifico della piattaforma, è possibile rispondere alle modifiche delle proprietà associabile, come illustrato nell'esempio di codice seguente:

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
                      args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

Il `OnElementPropertyChanged` metodo aggiorna il colore o offset dell'ombreggiatura, a condizione che l'oggetto appropriato `ShadowEffect` valore della proprietà associata è stato modificato. Un controllo per la proprietà viene modificata deve essere sempre eseguito, come la sostituzione può essere chiamata più volte.

## <a name="summary"></a>Riepilogo

Questo articolo sono state fornite tramite proprietà per passare i parametri per un effetto e la modifica di un parametro in fase di esecuzione associate. Le proprietà associate sono utilizzabile per definire i parametri di effetto che rispondono a modifiche alle proprietà runtime.


## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effetto](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Effetto di ombreggiatura (esempio)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffectruntimechange/)
