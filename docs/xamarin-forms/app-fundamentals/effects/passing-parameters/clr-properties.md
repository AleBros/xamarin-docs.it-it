---
title: Passaggio di parametri effetto come Common Language Runtime proprietà
description: Le proprietà di Common Language Runtime (CLR) sono utilizzabile per definire i parametri di effetto che non rispondono alle modifiche delle proprietà di runtime. Questo articolo illustra l'uso delle proprietà CLR per passare parametri a un effetto.
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 1bb357b256a7cc6d52d1d92613f38cbf48400c4c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995768"
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>Passaggio di parametri effetto come Common Language Runtime proprietà

_Le proprietà di Common Language Runtime (CLR) sono utilizzabile per definire i parametri di effetto che non rispondono alle modifiche delle proprietà di runtime. Questo articolo illustra l'uso delle proprietà CLR per passare parametri a un effetto._

Il processo di creazione dei parametri di effetto che non rispondono a modifiche alle proprietà runtime è come segue:

1. Creare un `public` classe che rappresenta una sottoclasse di [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) classe. Il `RoutingEffect` classe rappresenta un effetto indipendente dalla piattaforma che esegue il wrapping di un effetto interno che è in genere specifica della piattaforma.
1. Creare un costruttore che chiama il costruttore di classe di base, passando una concatenazione del nome del gruppo di risoluzione e l'ID univoco che è stata specificata in ogni classe effetto specifico della piattaforma.
1. Aggiungere proprietà alla classe per ogni parametro deve essere passato l'effetto.

I parametri possono quindi essere passati all'effetto specificando i valori per ogni proprietà quando si crea l'effetto.

L'applicazione di esempio viene illustrato un `ShadowEffect` che aggiunge un'ombreggiatura per il testo visualizzato da un [ `Label` ](xref:Xamarin.Forms.Label) controllo. Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con le relazioni tra di essi:

![](clr-properties-images/shadow-effect.png "Responsabilità di progetto effetto ombreggiatura")

Oggetto [ `Label` ](xref:Xamarin.Forms.Label) control per il `HomePage` personalizzato dal `LabelShadowEffect` in ogni progetto specifico della piattaforma. I parametri vengono passati a ciascun `LabelShadowEffect` tramite le proprietà nel `ShadowEffect` classe. Ciascuna `LabelShadowEffect` deriva dalla classe di `PlatformEffect` classe per ogni piattaforma. Il risultato è un'ombreggiatura da aggiungere al testo visualizzato per il `Label` controllare, come illustrato negli screenshot seguenti:

![](clr-properties-images/screenshots.png "Effetto di ombreggiatura per ogni piattaforma")

## <a name="creating-effect-parameters"></a>Creazione di parametri di effetto

Oggetto `public` classe che rappresenta una sottoclasse di [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) classe deve essere creata per rappresentare i parametri di effetto, come illustrato nell'esempio di codice seguente:

```csharp
public class ShadowEffect : RoutingEffect
{
  public float Radius { get; set; }

  public Color Color { get; set; }

  public float DistanceX { get; set; }

  public float DistanceY { get; set; }

  public ShadowEffect () : base ("MyCompany.LabelShadowEffect")
  {            
  }
}
```

Il `ShadowEffect` contiene quattro proprietà che rappresentano i parametri da passare per ogni specifico della piattaforma `LabelShadowEffect`. Il costruttore della classe chiama il costruttore di classe di base, passando un parametro costituito da una concatenazione del nome del gruppo di risoluzione e l'ID univoco che è stata specificata in ogni classe effetto specifico della piattaforma. Pertanto, una nuova istanza della `MyCompany.LabelShadowEffect` verrà aggiunto a un controllo [ `Effects` ](xref:Xamarin.Forms.Element.Effects) raccolta quando un `ShadowEffect` viene creata un'istanza.

## <a name="consuming-the-effect"></a>L'effetto di utilizzo

L'esempio di codice XAML seguente mostra una [ `Label` ](xref:Xamarin.Forms.Label) controllo a cui il `ShadowEffect` collegato:

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Effects>
    <local:ShadowEffect Radius="5" DistanceX="5" DistanceY="5">
      <local:ShadowEffect.Color>
        <OnPlatform x:TypeArguments="Color">
            <On Platform="iOS" Value="Black" />
            <On Platform="Android" Value="White" />
            <On Platform="UWP" Value="Red" />
        </OnPlatform>
      </local:ShadowEffect.Color>
    </local:ShadowEffect>
  </Label.Effects>
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

label.Effects.Add (new ShadowEffect {
  Radius = 5,
  Color = color,
  DistanceX = 5,
  DistanceY = 5
});
```

In entrambi gli esempi di codice, un'istanza di `ShadowEffect` viene creata un'istanza di classe con i valori specificati per ogni proprietà, prima di essere aggiunti al controllo [ `Effects` ](xref:Xamarin.Forms.Element.Effects) raccolta. Si noti che il `ShadowEffect.Color` proprietà Usa i valori di colore specifici della piattaforma. Per altre informazioni, vedere [classe Device](~/xamarin-forms/platform/device.md).

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
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    Control.Layer.CornerRadius = effect.Radius;
                    Control.Layer.ShadowColor = effect.Color.ToCGColor ();
                    Control.Layer.ShadowOffset = new CGSize (effect.DistanceX, effect.DistanceY);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

Il `OnAttached` recuperata dal metodo di `ShadowEffect` istanza e imposta `Control.Layer` proprietà ai valori della proprietà specificata per creare l'ombreggiatura. Questa funzionalità viene eseguito il wrapping un `try` / `catch` blocco nel caso in cui il controllo cui è collegata l'effetto non dispone di `Control.Layer` proprietà. Nessuna implementazione avviene tramite il `OnDetached` metodo perché non è necessaria alcuna operazione di pulizia.

### <a name="android-project"></a>Progetto Android

Nell'esempio di codice riportato di seguito viene illustrato il `LabelShadowEffect` implementazione per il progetto Android:

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                var control = Control as Android.Widget.TextView;
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    float radius = effect.Radius;
                    float distanceX = effect.DistanceX;
                    float distanceY = effect.DistanceY;
                    Android.Graphics.Color color = effect.Color.ToAndroid ();
                    control.SetShadowLayer (radius, distanceX, distanceY, color);
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

Il `OnAttached` recuperata dal metodo il `ShadowEffect` istanza e chiama il [ `TextView.SetShadowLayer` ](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/) metodo per creare un'ombreggiatura usando i valori di proprietà specificato. Questa funzionalità viene eseguito il wrapping un `try` / `catch` blocco nel caso in cui il controllo cui è collegata l'effetto non dispone di `Control.Layer` proprietà. Nessuna implementazione avviene tramite il `OnDetached` metodo perché non è necessaria alcuna operazione di pulizia.

### <a name="universal-windows-platform-project"></a>Progetto della piattaforma Windows universale

Nell'esempio di codice riportato di seguito viene illustrato il `LabelShadowEffect` implementazione per il progetto Universal Windows Platform (UWP):

```csharp
[assembly: ResolutionGroupName ("Xamarin")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                    if (effect != null) {
                        var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;
                        var shadowLabel = new Label ();
                        shadowLabel.Text = textBlock.Text;
                        shadowLabel.FontAttributes = FontAttributes.Bold;
                        shadowLabel.HorizontalOptions = LayoutOptions.Center;
                        shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;
                        shadowLabel.TextColor = effect.Color;
                        shadowLabel.TranslationX = effect.DistanceX;
                        shadowLabel.TranslationY = effect.DistanceY;

                        ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                        shadowAdded = true;
                    }
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

La piattaforma Windows universale non fornisce un effetto di ombreggiatura e pertanto `LabelShadowEffect` implementazione su entrambe le piattaforme simula uno tramite l'aggiunta di un offset secondo [ `Label` ](xref:Xamarin.Forms.Label) dietro quelle primarie `Label`. Il `OnAttached` recuperata dal metodo di `ShadowEffect` dell'istanza, crea il nuovo `Label`e configura alcune proprietà del layout nel `Label`. Crea quindi l'ombreggiatura impostando il [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor), [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX), e [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) le proprietà per controllare il colore e il percorso del `Label`. Il `shadowLabel` viene quindi inserito offset dietro quelle primarie `Label`. Questa funzionalità viene eseguito il wrapping un `try` / `catch` blocco nel caso in cui il controllo cui è collegata l'effetto non dispone di `Control.Layer` proprietà. Nessuna implementazione avviene tramite il `OnDetached` metodo perché non è necessaria alcuna operazione di pulizia.

## <a name="summary"></a>Riepilogo

Questo articolo ha dimostrato l'utilizzo delle proprietà CLR per passare parametri a un effetto. Proprietà CLR sono utilizzabile per definire i parametri di effetto che non rispondono alle modifiche delle proprietà di runtime.


## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effetto](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Effetto di ombreggiatura (esempio)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
