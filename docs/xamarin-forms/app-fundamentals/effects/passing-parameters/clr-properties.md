---
title: "passaggio di parametri effetto come proprietà di Common Language Runtime" Descrizione: è possibile usare le proprietà CLR (Common Language Runtime) per definire i parametri degli effetti che non rispondono alle modifiche delle proprietà di Runtime. Questo articolo illustra l'uso delle proprietà CLR per passare parametri a un effetto ".
ms. prod: Novell MS. AssetID: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 08/05/2016 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>Passaggio dei parametri di un effetto come proprietà Common Language Runtime

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)

_È possibile utilizzare le proprietà CLR (Common Language Runtime) per definire i parametri degli effetti che non rispondono alle modifiche delle proprietà di Runtime. Questo articolo illustra l'uso delle proprietà CLR per passare parametri a un effetto._

Il processo di creazione dei parametri per un effetto che non rispondono a modifiche delle proprietà in fase di esecuzione è il seguente:

1. Creare una `public` classe che sottoclassi la [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) classe. La classe `RoutingEffect` rappresenta un effetto indipendente dalla piattaforma che esegue il wrapping di un effetto interno, in genere specifico della piattaforma.
1. Creare un costruttore che chiama il costruttore della classe di base, passando una concatenazione del nome del gruppo di risoluzione e dell'ID univoco specificato per ogni classe di effetto specifica della piattaforma.
1. Aggiungere proprietà alla classe per ogni parametro da passare all'effetto.

I parametri possono quindi essere passati all'effetto specificando i valori per ogni proprietà quando si crea un'istanza dell'effetto.

Nell'applicazione di esempio viene illustrato un oggetto `ShadowEffect` che aggiunge un'ombreggiatura al testo visualizzato da un [`Label`](xref:Xamarin.Forms.Label) controllo. Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazioni tra di essi:

![](clr-properties-images/shadow-effect.png "Shadow Effect Project Responsibilities")

Un [`Label`](xref:Xamarin.Forms.Label) controllo su `HomePage` viene personalizzato da `LabelShadowEffect` in ogni progetto specifico della piattaforma. I parametri vengono passati a ogni `LabelShadowEffect` tramite le proprietà nella classe `ShadowEffect`. Ogni classe `LabelShadowEffect` deriva dalla classe `PlatformEffect` per ogni piattaforma. Il risultato è l'aggiunta di un'ombreggiatura al testo visualizzato dal controllo `Label`, come illustrato negli screenshot seguenti:

![](clr-properties-images/screenshots.png "Shadow Effect on each Platform")

## <a name="creating-effect-parameters"></a>Creazione di parametri di effetto

`public`Classe che [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) consente di creare sottoclassi della classe per rappresentare i parametri degli effetti, come illustrato nell'esempio di codice seguente:

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

`ShadowEffect` contiene quattro proprietà che rappresentano i parametri da passare a ogni `LabelShadowEffect` specifico della piattaforma. Il costruttore della classe chiama il costruttore della classe di base, passando un parametro costituito da una concatenazione del nome del gruppo di risoluzione e dell'ID univoco specificato per ogni classe di effetto specifica della piattaforma. Pertanto, una nuova istanza di `MyCompany.LabelShadowEffect` verrà aggiunta alla raccolta di un controllo [`Effects`](xref:Xamarin.Forms.Element.Effects) quando viene creata un'istanza di un oggetto `ShadowEffect` .

## <a name="consuming-the-effect"></a>Utilizzo dell'effetto

Nell'esempio di codice XAML seguente viene illustrato un [`Label`](xref:Xamarin.Forms.Label) controllo a cui `ShadowEffect` è associato l'oggetto:

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

L'equivalente [`Label`](xref:Xamarin.Forms.Label) in C# è illustrato nell'esempio di codice seguente:

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

In entrambi gli esempi di codice viene creata un'istanza della `ShadowEffect` classe con i valori specificati per ogni proprietà, prima di essere aggiunti alla raccolta del controllo [`Effects`](xref:Xamarin.Forms.Element.Effects) . Si noti che la proprietà `ShadowEffect.Color` usa valori di colore specifici della piattaforma. Per altre informazioni, vedere [Classe Device](~/xamarin-forms/platform/device.md).

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
                    Control.Layer.ShadowRadius = effect.Radius;
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

Il `OnAttached` metodo recupera l' `ShadowEffect` istanza di e chiama il [`TextView.SetShadowLayer`](xref:Android.Widget.TextView.SetShadowLayer*) metodo per creare un'ombreggiatura usando i valori di proprietà specificati. Questa funzionalità viene sottoposta a wrapping in un blocco `try`/`catch`, nel caso il controllo a cui è associato l'effetto non abbia le proprietà `Control.Layer`. Il metodo `OnDetached` non fornisce alcuna implementazione perché non sono necessarie operazioni di pulizia.

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

Il piattaforma UWP (Universal Windows Platform) non fornisce un effetto di ombreggiatura, pertanto l' `LabelShadowEffect` implementazione su entrambe le piattaforme ne simula una aggiungendo un secondo offset [`Label`](xref:Xamarin.Forms.Label) dietro il database primario `Label` . Il metodo `OnAttached` recupera l'istanza di `ShadowEffect`, crea il nuovo elemento `Label` e imposta alcune proprietà di layout per `Label`. Viene quindi creata l'ombreggiatura impostando [`TextColor`](xref:Xamarin.Forms.Label.TextColor) le [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) proprietà, e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) per controllare il colore e la posizione dell'oggetto `Label` . `shadowLabel` viene quindi inserito sfalsato dietro all'elemento `Label` primario. Questa funzionalità viene sottoposta a wrapping in un blocco `try`/`catch`, nel caso il controllo a cui è associato l'effetto non abbia le proprietà `Control.Layer`. Il metodo `OnDetached` non fornisce alcuna implementazione perché non sono necessarie operazioni di pulizia.

## <a name="summary"></a>Summary

In questo articolo è stato illustrato l'uso delle proprietà CLR per passare parametri a un effetto. Le proprietà CLR possono essere usate per definire i parametri dell'effetto che non rispondono a modifiche delle proprietà in fase di esecuzione.

## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effetto](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Shadow Effect (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect) (Esempio di effetto di ombreggiatura)
