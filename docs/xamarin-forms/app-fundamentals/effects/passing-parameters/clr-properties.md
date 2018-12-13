---
title: Passaggio dei parametri di un effetto come proprietà Common Language Runtime
description: Le proprietà CLR (Common Language Runtime) possono essere usate per definire parametri per gli effetti che non rispondono alle modifiche delle proprietà in fase di esecuzione. Questo articolo illustra l'uso delle proprietà CLR per il passaggio di parametri a un effetto.
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 1bb357b256a7cc6d52d1d92613f38cbf48400c4c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995768"
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>Passaggio dei parametri di un effetto come proprietà Common Language Runtime

_Le proprietà CLR (Common Language Runtime) possono essere usate per definire parametri per gli effetti che non rispondono alle modifiche delle proprietà in fase di esecuzione. Questo articolo illustra l'uso delle proprietà CLR per il passaggio di parametri a un effetto._

Il processo di creazione dei parametri per un effetto che non rispondono a modifiche delle proprietà in fase di esecuzione è il seguente:

1. Creare una classe `public` come sottoclasse della classe [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect). La classe `RoutingEffect` rappresenta un effetto indipendente dalla piattaforma che esegue il wrapping di un effetto interno, in genere specifico della piattaforma.
1. Creare un costruttore che chiama il costruttore della classe di base, passando una concatenazione del nome del gruppo di risoluzione e dell'ID univoco specificato per ogni classe di effetto specifica della piattaforma.
1. Aggiungere proprietà alla classe per ogni parametro da passare all'effetto.

I parametri possono quindi essere passati all'effetto specificando i valori per ogni proprietà quando si crea un'istanza dell'effetto.

L'applicazione di esempio illustra un `ShadowEffect` che aggiunge un'ombreggiatura al testo visualizzato da un controllo [`Label`](xref:Xamarin.Forms.Label). Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazioni tra di essi:

![](clr-properties-images/shadow-effect.png "Responsabilità dei progetti per l'effetto di ombreggiatura")

Un controllo [`Label`](xref:Xamarin.Forms.Label) per la `HomePage` è personalizzato da `LabelShadowEffect` in ogni progetto specifico della piattaforma. I parametri vengono passati a ogni `LabelShadowEffect` tramite le proprietà nella classe `ShadowEffect`. Ogni classe `LabelShadowEffect` deriva dalla classe `PlatformEffect` per ogni piattaforma. Il risultato è l'aggiunta di un'ombreggiatura al testo visualizzato dal controllo `Label`, come illustrato negli screenshot seguenti:

![](clr-properties-images/screenshots.png "Effetto di ombreggiatura per ogni piattaforma")

## <a name="creating-effect-parameters"></a>Creazione di parametri per l'effetto

È necessario creare una classe `public` come sottoclasse della classe [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) per rappresentare i parametri dell'effetto, come illustrato nell'esempio di codice seguente:

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

`ShadowEffect` contiene quattro proprietà che rappresentano i parametri da passare a ogni `LabelShadowEffect` specifico della piattaforma. Il costruttore della classe chiama il costruttore della classe di base, passando un parametro costituito da una concatenazione del nome del gruppo di risoluzione e dell'ID univoco specificato per ogni classe di effetto specifica della piattaforma. Verrà quindi aggiunta una nuova istanza di `MyCompany.LabelShadowEffect` alla raccolta [`Effects`](xref:Xamarin.Forms.Element.Effects) di un controllo quando viene creata un'istanza di `ShadowEffect`.

## <a name="consuming-the-effect"></a>Utilizzo dell'effetto

L'esempio di codice XAML seguente mostra un controllo [`Label`](xref:Xamarin.Forms.Label) a cui è associato `ShadowEffect`:

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

Il codice C# equivalente per [`Label`](xref:Xamarin.Forms.Label) è visualizzato nell'esempio seguente:

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

In entrambi gli esempi di codice viene creata un'istanza della classe `ShadowEffect` con i valori specificati per ogni proprietà, prima dell'aggiunta alla raccolta [`Effects`](xref:Xamarin.Forms.Element.Effects) del controllo. Si noti che la proprietà `ShadowEffect.Color` usa valori di colore specifici della piattaforma. Per altre informazioni, vedere [Classe Device](~/xamarin-forms/platform/device.md).

## <a name="creating-the-effect-on-each-platform"></a>Creazione dell'effetto in ogni piattaforma

Le sezioni seguenti illustrano l'implementazione specifica della piattaforma della classe `LabelShadowEffect`.

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

Il metodo `OnAttached` recupera l'istanza di `ShadowEffect` e imposta le proprietà `Control.Layer` sui valori di proprietà specificati per creare l'ombreggiatura. Questa funzionalità viene sottoposta a wrapping in un blocco `try`/`catch`, nel caso il controllo a cui è associato l'effetto non abbia le proprietà `Control.Layer`. Il metodo `OnDetached` non fornisce alcuna implementazione perché non sono necessarie operazioni di pulizia.

### <a name="android-project"></a>Progetto Android

L'esempio di codice seguente mostra l'implementazione di `LabelShadowEffect` per il progetto Android:

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

Il metodo `OnAttached` recupera l'istanza di `ShadowEffect` e chiama il metodo [`TextView.SetShadowLayer`](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/) per creare un'ombreggiatura usando i valori di proprietà specificati. Questa funzionalità viene sottoposta a wrapping in un blocco `try`/`catch`, nel caso il controllo a cui è associato l'effetto non abbia le proprietà `Control.Layer`. Il metodo `OnDetached` non fornisce alcuna implementazione perché non sono necessarie operazioni di pulizia.

### <a name="universal-windows-platform-project"></a>Progetto UWP (Universal Windows Platform)

L'esempio di codice seguente mostra l'implementazione di `LabelShadowEffect` per il progetto UWP (Universal Windows Platform):

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

La piattaforma UWP non include un effetto di ombreggiatura, pertanto l'implementazione di `LabelShadowEffect` in entrambe le piattaforme ne simula uno tramite l'aggiunta di un secondo elemento [`Label`](xref:Xamarin.Forms.Label) sfalsato dietro all'elemento `Label` primario. Il metodo `OnAttached` recupera l'istanza di `ShadowEffect`, crea il nuovo elemento `Label` e imposta alcune proprietà di layout per `Label`. Crea quindi l'ombreggiatura impostando le proprietà [`TextColor`](xref:Xamarin.Forms.Label.TextColor), [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) per controllare il colore e la posizione di `Label`. `shadowLabel` viene quindi inserito sfalsato dietro all'elemento `Label` primario. Questa funzionalità viene sottoposta a wrapping in un blocco `try`/`catch`, nel caso il controllo a cui è associato l'effetto non abbia le proprietà `Control.Layer`. Il metodo `OnDetached` non fornisce alcuna implementazione perché non sono necessarie operazioni di pulizia.

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato l'uso delle proprietà CLR per passare parametri a un effetto. Le proprietà CLR possono essere usate per definire i parametri dell'effetto che non rispondono a modifiche delle proprietà in fase di esecuzione.


## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Shadow Effect (sample)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/) (Esempio di effetto di ombreggiatura)
