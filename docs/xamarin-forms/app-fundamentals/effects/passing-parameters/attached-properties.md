---
title: Passare parametri a un effetto come proprietà associate
description: Le proprietà associate possono essere usate per definire i parametri di effetto che rispondono a modifiche delle proprietà in fase di esecuzione. Questo articolo spiega come usare le proprietà associate per passare i parametri a un effetto e come modificare un parametro in fase di esecuzione.
ms.prod: xamarin
ms.assetid: DFCDCB9F-17DD-4117-BD53-B4FB206BB387
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 2fcfbfa8a1780a9da4e9bfe187dc99a5172e95f0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771489"
---
# <a name="passing-effect-parameters-as-attached-properties"></a>Passare parametri a un effetto come proprietà associate

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffectruntimechange)

_Le proprietà associate possono essere usate per definire i parametri di effetto che rispondono a modifiche delle proprietà in fase di esecuzione. Questo articolo spiega come usare le proprietà associate per passare i parametri a un effetto e come modificare un parametro in fase di esecuzione._

Il processo di creazione dei parametri per un effetto che rispondono a modifiche delle proprietà in fase di esecuzione è il seguente:

1. Creare una classe `static` che contenga una proprietà associata per ogni parametro che deve essere passato all'effetto.
1. Aggiungere alla classe una proprietà associata aggiuntiva che verrà usata per controllare l'aggiunta dell'effetto al controllo, o la sua rimozione dal controllo, a cui la classe verrà associata. Assicurarsi che tale proprietà associata registri un delegato `propertyChanged` che verrà eseguito quando cambia il valore della proprietà.
1. Creare un getter e un setter `static` per ogni proprietà associata.
1. Implementare la logica nel delegato `propertyChanged` per aggiungere e rimuovere l'effetto.
1. Implementare una classe annidata all'interno della classe `static`, denominata in base all'effetto, che crea una sottoclasse della classe `RoutingEffect`. Per il costruttore, chiamare il costruttore della classe di base, passando una concatenazione del nome del gruppo di risoluzione e dell'ID univoco specificato per ogni classe di effetto specifica della piattaforma.

I parametri possono quindi essere passati all'effetto aggiungendo le proprietà associate, e i valori delle proprietà, al controllo appropriato. Inoltre, i parametri possono essere modificati in fase di esecuzione specificando un nuovo valore della proprietà associata.

> [!NOTE]
> Le proprietà associate sono un tipo speciale di proprietà associabile, definite in una classe ma associate ad altri oggetti. Sono riconoscibili in XAML perché si presentano come attributi che contengono una classe e un nome di proprietà separati da un punto. Per altre informazioni, vedere [Proprietà associate](~/xamarin-forms/xaml/attached-properties.md).

L'applicazione di esempio illustra un `ShadowEffect` che aggiunge un'ombreggiatura al testo visualizzato da un controllo [`Label`](xref:Xamarin.Forms.Label). Inoltre, il colore dell'ombreggiatura può essere modificato in fase di esecuzione. Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazione tra di esse:

![](attached-properties-images/shadow-effect.png "Responsabilità dei progetti per l'effetto di ombreggiatura")

Un controllo [`Label`](xref:Xamarin.Forms.Label) per la `HomePage` è personalizzato da `LabelShadowEffect` in ogni progetto specifico della piattaforma. I parametri vengono passati a ogni `LabelShadowEffect` tramite le proprietà associate nella classe `ShadowEffect`. Ogni classe `LabelShadowEffect` deriva dalla classe `PlatformEffect` per ogni piattaforma. Il risultato è l'aggiunta di un'ombreggiatura al testo visualizzato dal controllo `Label`, come illustrato negli screenshot seguenti:

![](attached-properties-images/screenshots.png "Effetto di ombreggiatura per ogni piattaforma")

## <a name="creating-effect-parameters"></a>Creazione di parametri di effetto

È necessario creare una classe `static` per rappresentare i parametri di effetto, come illustrato nell'esempio di codice seguente:

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

L'elemento `ShadowEffect` contiene cinque proprietà associate, con getter e setter `static` per ogni proprietà associata. Quattro di queste proprietà rappresentano i parametri da passare a ogni `LabelShadowEffect` specifico della piattaforma. La classe `ShadowEffect` definisce anche una proprietà associata `HasShadow` che verrà usata per controllare l'aggiunta dell'effetto al controllo, o la sua rimozione dal controllo, a cui la classe `ShadowEffect` verrà associata. Tale proprietà associata registra il metodo `OnHasShadowChanged` che verrà eseguito quando cambia il valore della proprietà. Questo metodo aggiunge o rimuove l'effetto in base al valore della proprietà `HasShadow` associata.

La classe `LabelShadowEffect` annidata, che crea una sottoclasse della classe [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect), supporta l'aggiunta e la rimozione dell'effetto. La classe `RoutingEffect` rappresenta un effetto indipendente dalla piattaforma che esegue il wrapping di un effetto interno, in genere specifico della piattaforma. Ciò semplifica il processo di rimozione dell'effetto, poiché non è previsto l'accesso alle informazioni sul tipo in fase di compilazione per un effetto specifico della piattaforma. Il costruttore `LabelShadowEffect` chiama il costruttore della classe di base, passando un parametro costituito da una concatenazione del nome del gruppo di risoluzione e dell'ID univoco specificato per ogni classe di effetto specifica della piattaforma. Questo abilita l'aggiunta e la rimozione dell'effetto nel metodo `OnHasShadowChanged`, come indicato di seguito:

- **Aggiunta dell'effetto**: una nuova istanza di `LabelShadowEffect` viene aggiunta alla raccolta [`Effects`](xref:Xamarin.Forms.Element.Effects) del controllo. Questa operazione sostituisce l'utilizzo del metodo [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) per aggiungere l'effetto.
- **Rimozione dell'effetto**: la prima istanza di `LabelShadowEffect` della raccolta [`Effects`](xref:Xamarin.Forms.Element.Effects) del controllo viene recuperata e rimossa.

## <a name="consuming-the-effect"></a>Utilizzo dell'effetto

Ogni elemento `LabelShadowEffect` specifico della piattaforma può essere utilizzato aggiungendo le proprietà associate a un controllo [`Label`](xref:Xamarin.Forms.Label), come illustrato nell'esempio di codice XAML seguente:

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

Il codice C# equivalente per il controllo [`Label`](xref:Xamarin.Forms.Label) è visualizzato nell'esempio seguente:

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

L'impostazione della proprietà associata `ShadowEffect.HasShadow`su `true` esegue il metodo `ShadowEffect.OnHasShadowChanged` che aggiunge o rimuove `LabelShadowEffect` dal controllo [`Label`](xref:Xamarin.Forms.Label). In entrambi gli esempi di codice, la proprietà associata `ShadowEffect.Color` fornisce i valori di colore specifici della piattaforma. Per altre informazioni, vedere [Classe Device](~/xamarin-forms/platform/device.md).

Inoltre, con un [`Button`](xref:Xamarin.Forms.Button) il colore dell'ombreggiatura può essere modificato in fase di esecuzione. Quando si fa clic su `Button`, il codice seguente cambia il colore dell'ombreggiatura impostando la proprietà associata `ShadowEffect.Color`:

```csharp
ShadowEffect.SetColor (label, Color.Teal);
```

### <a name="consuming-the-effect-with-a-style"></a>Utilizzo dell'effetto con uno stile

Gli effetti che possono essere utilizzati aggiungendo le proprietà associate a un controllo possono anche essere utilizzati da uno stile. L'esempio di codice XAML seguente mostra uno stile *esplicito* per l'effetto di ombreggiatura che può essere applicato ai controlli [`Label`](xref:Xamarin.Forms.Label):

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

[`Style`](xref:Xamarin.Forms.Style) può essere applicato a un elemento [`Label`](xref:Xamarin.Forms.Label) impostando la relativa proprietà [`Style`](xref:Xamarin.Forms.NavigableElement.Style) sull'istanza di `Style` utilizzando l'estensione di markup `StaticResource`, come illustrato nell'esempio di codice seguente:

```xaml
<Label Text="Label Shadow Effect" ... Style="{StaticResource ShadowEffectStyle}" />
```

Per altre informazioni sugli stili, vedere [Stili](~/xamarin-forms/user-interface/styles/index.md).

## <a name="creating-the-effect-on-each-platform"></a>Creazione dell'effetto in ogni piattaforma

Le sezioni seguenti illustrano l'implementazione specifica per la piattaforma della classe `LabelShadowEffect`.

### <a name="ios-project"></a>Progetto iOS

L'esempio di codice seguente mostra l'implementazione di `LabelShadowEffect` per il progetto iOS:

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

Il metodo `OnAttached` chiama i metodi che recuperano i valori della proprietà associata utilizzando i getter `ShadowEffect` e che impostano le proprietà `Control.Layer` sui valori necessari per creare l'ombreggiatura. Questa funzionalità è incapsulata in un blocco `try`/`catch`, nell'evenienza in cui il controllo a cui l'effetto è associato non avesse le proprietà `Control.Layer`. Il metodo `OnDetached` non fornisce alcuna implementazione perché non sono necessarie operazioni di pulizia.

#### <a name="responding-to-property-changes"></a>Rispondere alle modifiche delle proprietà

Se uno qualsiasi dei valori della proprietà associata `ShadowEffect` cambia in fase di esecuzione, l'effetto dovrà rispondere visualizzando le modifiche. La risposta alle modifiche delle proprietà associabili avviene in una versione sottoposta a override del metodo `OnElementPropertyChanged`, nella classe dell'effetto specifico della piattaforma, come illustrato nell'esempio di codice seguente:

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

Il metodo `OnElementPropertyChanged` aggiorna il raggio, il colore o l'offset dell'ombreggiatura, a condizione che valore della proprietà associata `ShadowEffect` appropriata sia stato modificato. Deve sempre essere eseguito un controllo della proprietà modificata, dal momento che l'override può essere chiamato più volte.

### <a name="android-project"></a>Progetto Android

L'esempio di codice seguente mostra l'implementazione di `LabelShadowEffect` per il progetto Android:

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

Il metodo `OnAttached` chiama i metodi che recuperano i valori della proprietà associata utilizzando i getter `ShadowEffect` e chiama il metodo [`TextView.SetShadowLayer`](xref:Android.Widget.TextView.SetShadowLayer*) per creare un'ombreggiatura utilizzando i valori della proprietà. Questa funzionalità è incapsulata in un blocco `try`/`catch`, nell'evenienza in cui il controllo a cui l'effetto è associato non avesse le proprietà `Control.Layer`. Il metodo `OnDetached` non fornisce alcuna implementazione perché non sono necessarie operazioni di pulizia.

#### <a name="responding-to-property-changes"></a>Rispondere alle modifiche delle proprietà

Se uno qualsiasi dei valori della proprietà associata `ShadowEffect` cambia in fase di esecuzione, l'effetto dovrà rispondere visualizzando le modifiche. La risposta alle modifiche delle proprietà associabili avviene in una versione sottoposta a override del metodo `OnElementPropertyChanged`, nella classe dell'effetto specifico della piattaforma, come illustrato nell'esempio di codice seguente:

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

Il metodo `OnElementPropertyChanged` aggiorna il raggio, il colore o l'offset dell'ombreggiatura, a condizione che valore della proprietà associata `ShadowEffect` appropriata sia stato modificato. Deve sempre essere eseguito un controllo della proprietà modificata, dal momento che l'override può essere chiamato più volte.

### <a name="universal-windows-platform-project"></a>Progetto UWP (Universal Windows Platform)

L'esempio di codice seguente mostra l'implementazione di `LabelShadowEffect` per il progetto UWP (Universal Windows Platform):

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

La piattaforma UWP non include un effetto di ombreggiatura, pertanto l'implementazione di `LabelShadowEffect` in entrambe le piattaforme ne simula uno tramite l'aggiunta di un secondo elemento [`Label`](xref:Xamarin.Forms.Label) sfalsato dietro all'elemento `Label` primario. Il metodo `OnAttached` crea il nuovo elemento `Label` e imposta alcune proprietà di layout per `Label`. Quindi chiama i metodi che recuperano i valori della proprietà associata utilizzando i getter `ShadowEffect` e crea l'ombreggiatura impostando le proprietà [`TextColor`](xref:Xamarin.Forms.Label.TextColor), [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) per controllare il colore e la posizione dell'elemento `Label`. `shadowLabel` viene quindi inserito sfalsato dietro l'elemento `Label` primario. Questa funzionalità è incapsulata in un blocco `try`/`catch`, nell'evenienza in cui il controllo a cui l'effetto è associato non avesse le proprietà `Control.Layer`. Il metodo `OnDetached` non fornisce alcuna implementazione perché non sono necessarie operazioni di pulizia.

#### <a name="responding-to-property-changes"></a>Rispondere alle modifiche delle proprietà

Se uno qualsiasi dei valori della proprietà associata `ShadowEffect` cambia in fase di esecuzione, l'effetto dovrà rispondere visualizzando le modifiche. La risposta alle modifiche delle proprietà associabili avviene in una versione sottoposta a override del metodo `OnElementPropertyChanged`, nella classe dell'effetto specifico della piattaforma, come illustrato nell'esempio di codice seguente:

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

Il metodo `OnElementPropertyChanged` aggiorna il colore o l'offset dell'ombreggiatura, a condizione che valore della proprietà associata `ShadowEffect` appropriata sia stato modificato. Deve sempre essere eseguito un controllo della proprietà modificata, dal momento che l'override può essere chiamato più volte.

## <a name="summary"></a>Riepilogo

Questo articolo ha spiegato come usare le proprietà associate per passare i parametri a un effetto e come modificare un parametro in fase di esecuzione. Le proprietà associate possono essere usate per definire i parametri di effetto che rispondono a modifiche delle proprietà in fase di esecuzione.

## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Shadow Effect (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffectruntimechange) (Esempio di effetto di ombreggiatura)
