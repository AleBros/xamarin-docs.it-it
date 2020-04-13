---
title: Passaggio dei parametri di un effetto come proprietà Common Language Runtime
description: Le proprietà CLR (Common Language Runtime) possono essere usate per definire parametri per gli effetti che non rispondono alle modifiche delle proprietà in fase di esecuzione. Questo articolo illustra l'uso delle proprietà CLR per il passaggio di parametri a un effetto.
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 04d96dad455cbcf8360f12ee97a0540e7e746d62
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771464"
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>Passaggio dei parametri di un effetto come proprietà Common Language Runtime

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)

_Le proprietà CLR (Common Language Runtime) possono essere utilizzate per definire i parametri degli effetti che non rispondono alle modifiche delle proprietà di runtime. In questo articolo viene illustrato l'utilizzo di proprietà CLR per passare parametri a un effetto._

Il processo di creazione dei parametri per un effetto che non rispondono a modifiche delle proprietà in fase di esecuzione è il seguente:

1. Creare `public` una classe che [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) sottoclassi della classe. La classe `RoutingEffect` rappresenta un effetto indipendente dalla piattaforma che esegue il wrapping di un effetto interno, in genere specifico della piattaforma.
1. Creare un costruttore che chiama il costruttore della classe di base, passando una concatenazione del nome del gruppo di risoluzione e dell'ID univoco specificato per ogni classe di effetto specifica della piattaforma.
1. Aggiungere proprietà alla classe per ogni parametro da passare all'effetto.

I parametri possono quindi essere passati all'effetto specificando i valori per ogni proprietà quando si crea un'istanza dell'effetto.

L'applicazione di `ShadowEffect` esempio viene illustrato un che [`Label`](xref:Xamarin.Forms.Label) aggiunge un'ombreggiatura al testo visualizzato da un controllo. Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazioni tra di essi:

![](clr-properties-images/shadow-effect.png "Shadow Effect Project Responsibilities")

Un [`Label`](xref:Xamarin.Forms.Label) controllo `HomePage` su è personalizzato `LabelShadowEffect` dal in ogni progetto specifico della piattaforma. I parametri vengono passati a ogni `LabelShadowEffect` tramite le proprietà nella classe `ShadowEffect`. Ogni classe `LabelShadowEffect` deriva dalla classe `PlatformEffect` per ogni piattaforma. Il risultato è l'aggiunta di un'ombreggiatura al testo visualizzato dal controllo `Label`, come illustrato negli screenshot seguenti:

![](clr-properties-images/screenshots.png "Shadow Effect on each Platform")

## <a name="creating-effect-parameters"></a>Creazione di parametri di effetto

Una `public` classe che [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) crea una sottoclasse della classe per rappresentare i parametri dell'effetto, come illustrato nell'esempio di codice seguente:A class that subclasses the class should be created to represent effect parameters, as demonstrated in the following code example:

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

`ShadowEffect` contiene quattro proprietà che rappresentano i parametri da passare a ogni `LabelShadowEffect` specifico della piattaforma. Il costruttore della classe chiama il costruttore della classe di base, passando un parametro costituito da una concatenazione del nome del gruppo di risoluzione e dell'ID univoco specificato per ogni classe di effetto specifica della piattaforma. Pertanto, una nuova `MyCompany.LabelShadowEffect` istanza di verrà aggiunta [`Effects`](xref:Xamarin.Forms.Element.Effects) alla `ShadowEffect` raccolta di un controllo quando viene creata un'istanza di un controllo.

## <a name="consuming-the-effect"></a>Utilizzo dell'effetto

Nell'esempio di codice [`Label`](xref:Xamarin.Forms.Label) XAML seguente `ShadowEffect` viene illustrato un controllo a cui è associato l'oggetto:

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

Nell'esempio di codice seguente viene illustrato l'equivalente [`Label`](xref:Xamarin.Forms.Label) in C:

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

In entrambi gli esempi di `ShadowEffect` codice viene creata un'istanza della classe con valori specificati [`Effects`](xref:Xamarin.Forms.Element.Effects) per ogni proprietà, prima di essere aggiunti alla raccolta del controllo. Si noti che la proprietà `ShadowEffect.Color` usa valori di colore specifici della piattaforma. Per altre informazioni, vedere [Classe Device](~/xamarin-forms/platform/device.md).

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

Il `OnAttached` metodo recupera `ShadowEffect` l'istanza [`TextView.SetShadowLayer`](xref:Android.Widget.TextView.SetShadowLayer*) e chiama il metodo per creare un'ombreggiatura utilizzando i valori della proprietà specificati. Questa funzionalità viene sottoposta a wrapping in un blocco `try`/`catch`, nel caso il controllo a cui è associato l'effetto non abbia le proprietà `Control.Layer`. Il metodo `OnDetached` non fornisce alcuna implementazione perché non sono necessarie operazioni di pulizia.

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

La piattaforma Windows universale non fornisce un effetto `LabelShadowEffect` ombra e pertanto l'implementazione [`Label`](xref:Xamarin.Forms.Label) su `Label`entrambe le piattaforme simula uno aggiungendo un secondo offset dietro il primario . Il metodo `OnAttached` recupera l'istanza di `ShadowEffect`, crea il nuovo elemento `Label` e imposta alcune proprietà di layout per `Label`. Viene quindi creata l'ombreggiatura impostando [`TextColor`](xref:Xamarin.Forms.Label.TextColor)le proprietà [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX), e `Label` [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) per controllare il colore e la posizione dell'oggetto . `shadowLabel` viene quindi inserito sfalsato dietro all'elemento `Label` primario. Questa funzionalità viene sottoposta a wrapping in un blocco `try`/`catch`, nel caso il controllo a cui è associato l'effetto non abbia le proprietà `Control.Layer`. Il metodo `OnDetached` non fornisce alcuna implementazione perché non sono necessarie operazioni di pulizia.

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato l'uso delle proprietà CLR per passare parametri a un effetto. Le proprietà CLR possono essere usate per definire i parametri dell'effetto che non rispondono a modifiche delle proprietà in fase di esecuzione.

## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effetto](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Shadow Effect (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect) (Esempio di effetto di ombreggiatura)
