---
title: I metodi di fusione separabili
description: Utilizzare i metodi di fusione separabili per modificare i colori rossi, verdi e blu.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 66D1A537-A247-484E-B5B9-FBCB7838FBE9
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 8c86782d5b8b8250049d0ae060ca7bd548c5a4ef
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61387360"
---
# <a name="the-separable-blend-modes"></a>I metodi di fusione separabili

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

Come illustrato nell'articolo [ **SkiaSharp Porter-Duff blend modalità**](porter-duff.md), le modalità di blend-Duff Porter in genere eseguono operazioni di ritaglio. I metodi di fusione separabili sono diversi. Le modalità separabili modificano i componenti di colore rosso, verde e blu singoli di un'immagine. I metodi di fusione separabili possono combinare colore per dimostrare che la combinazione di colore rosso, verde e blu è effettivamente bianca:

![Colori primari](separable-images/SeparableSample.png "colori primari")

## <a name="lighten-and-darken-two-ways"></a>Rendere più chiari e più scura due modi 

È comune disporre di una mappa di bit che è un po' troppo chiaro o scuro troppo. È possibile usare i metodi di fusione separabili a o ridurre la luminosità di imabe.  In effetti, due delle modalità blend separabili nel [ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode) vengono denominati enumerazione `Lighten` e `Darken`. 

Vengono illustrate queste due modalità di **schiarire e scurimento** pagina. Il file XAML crea un'istanza di due `SKCanvasView` gli oggetti e due `Slider` viste:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.LightenAndDarkenPage"
             Title="Lighten and Darken">
    <StackLayout>
        <skia:SKCanvasView x:Name="lightenCanvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="lightenSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />

        <skia:SKCanvasView x:Name="darkenCanvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="darkenSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />
    </StackLayout>
</ContentPage>
```

Il primo `SKCanvasView` e `Slider` illustrare `SKBlendMode.Lighten` e viene illustrata la seconda coppia `SKBlendMode.Darken`. I due `Slider` visualizzazioni condividono lo stesso `ValueChanged` gestore e le due `SKCanvasView` condividono lo stesso `PaintSurface` gestore. Controllo entrambi i gestori eventi che sta generando l'evento:

```csharp
public partial class LightenAndDarkenPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                typeof(SeparableBlendModesPage),
                "SkiaSharpFormsDemos.Media.Banana.jpg");

    public LightenAndDarkenPage ()
    {
        InitializeComponent ();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if ((Slider)sender == lightenSlider)
        {
            lightenCanvasView.InvalidateSurface();
        }
        else
        {
            darkenCanvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest size rectangle in canvas
        float scale = Math.Min((float)info.Width / bitmap.Width,
                               (float)info.Height / bitmap.Height);
        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);
        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap
        canvas.DrawBitmap(bitmap, rect);

        // Display gray rectangle with blend mode
        using (SKPaint paint = new SKPaint())
        {
            if ((SKCanvasView)sender == lightenCanvasView)
            {
                byte value = (byte)(255 * lightenSlider.Value);
                paint.Color = new SKColor(value, value, value);
                paint.BlendMode = SKBlendMode.Lighten;
            }
            else
            {
                byte value = (byte)(255 * (1 - darkenSlider.Value));
                paint.Color = new SKColor(value, value, value);
                paint.BlendMode = SKBlendMode.Darken;
            }

            canvas.DrawRect(rect, paint);
        }
    }
}
```

Il `PaintSurface` gestore calcola un rettangolo adatto per la bitmap. Il gestore visualizza tale immagine bitmap e quindi visualizza un rettangolo rispetto all'uso di bitmap un' `SKPaint` dell'oggetto con relativo `BlendMode` impostata su `SKBlendMode.Lighten` o `SKBlendMode.Darken`. Il `Color` proprietà è una gradazione di grigio di base di `Slider`. Per il `Lighten` modalità, gli intervalli di colori dal nero al bianco, ma per il `Darken` modalità compreso tra bianco e nero.

Gli screenshot da sinistra a destra mostrano sempre più grandi `Slider` i valori come immagine superiore ottenuti più chiaro e scuro dall'immagine inferiore:

[![Rendere più chiari e più scura](separable-images/LightenAndDarken.png "rendere più chiari e più scura")](separable-images/LightenAndDarken-Large.png#lightbox)

Questo programma illustra il modo consueto in cui vengono usati i metodi di fusione separabili: La destinazione è un'immagine di qualche tipo, molto spesso una bitmap. L'origine è un rettangolo visualizzato utilizzando un `SKPaint` dell'oggetto con relativo `BlendMode` proprietà è impostata su una modalità blend separabili. Il rettangolo può essere un colore a tinta unita (come succede qui) o una sfumatura. La trasparenza _non_ generalmente usati con le modalità di blend separabili.

Durante l'esperimento con questo programma, si scoprirà che queste modalità due blend non rendere più chiari e quale scurire l'immagine in modo uniforme. Al contrario, il `Slider` sembra impostare una soglia di qualche tipo. Ad esempio, quando si aumenta il `Slider` per il `Lighten` modalità, le aree più scure dell'immagine di accedervi light prima mentre le aree più chiare rimangono invariati.

Per il `Lighten` modalità, se il pixel di destinazione è il valore di colore RGB (ripristino di emergenza, gruppo di distribuzione, Db) e il pixel di origine corrisponde al colore (Sr, gruppo di protezione, Service Bus), quindi l'output è (o Og, Ob) calcolato come segue:

 `Or = max(Dr, Sr)` `Og = max(Dg, Sg)`
 `Ob = max(Db, Sb)`

Per il rosso, verde e blu separatamente, il risultato è il valore maggiore di origine e destinazione. Questa operazione produce l'effetto di con l'opzione prima di tutto le aree note della destinazione.

Il `Darken` modalità è simile ad eccezione del fatto che il risultato è il valore inferiore tra l'origine e destinazione:

 `Or = min(Dr, Sr)` `Og = min(Dg, Sg)`
 `Ob = min(Db, Sb)`

I componenti rossi, verdi e blu sono ognuno gestite separatamente, motivo per cui queste modalità di blend sono detti il _separabili_ le modalità di blend. Per questo motivo, le abbreviazioni **Dc** e **Sc** può essere utilizzato per i colori di origine e destinazione e si noti che i calcoli vengono applicate separatamente a ogni componente rossi, verde e blu.

La tabella seguente illustra tutte le modalità di blend separabili con una breve spiegazione di cosa fanno. La seconda colonna Mostra il colore di origine non produce alcun cambiamento:

| Modalità di blend   | Nessuna modifica | Operazione |
| ------------ | --------- | --------- |
| `Plus`       | Nero     | Schiarisce mediante l'aggiunta di colori: Sc + Dc |
| `Modulate`   | Bianco     | Diventa più scura moltiplicando colori: Sc·Dc | 
| `Screen`     | Nero     | Si integra con prodotti di integra: SC + Dc &ndash; Sc· Controller di dominio |
| `Overlay`    | Grigio      | Inverso della `HardLight` |
| `Darken`     | Bianco     | Minimo dei colori: min (Sc, controller di dominio) |
| `Lighten`    | Nero     | Numero massimo di colori: max (Sc, controller di dominio) |
| `ColorDodge` | Nero     | Schiarisce basata sull'origine di destinazione |
| `ColorBurn`  | Bianco     | Diventa più scura basata sull'origine di destinazione | 
| `HardLight`  | Grigio      | Simile all'effetto di spotlight harsh |
| `SoftLight`  | Grigio      | Simile all'effetto di software spotlight | 
| `Difference` | Nero     | Sottrae il colore più scuro dal più chiaro: Abs(Dc &ndash; Sc) | 
| `Exclusion`  | Nero     | Simile a `Difference` ma inferiore a contrasto elevato |
| `Multiply`   | Bianco     | Diventa più scura moltiplicando colori: Sc·Dc |

Algoritmi più dettagliati sono reperibili nel W3C [ **la composizione e fusione 1 livello** ](https://www.w3.org/TR/compositing-1/) specifica e il Skia [ **SkBlendMode riferimento** ](https://skia.org/user/api/SkBlendMode_Reference), anche se la notazione in queste due origini non corrisponde. Tenere presente che `Plus` comunemente viene considerato come una modalità di blend-Duff Porter, e `Modulate` non fa parte della specifica W3C.

Se l'origine è trasparente, per tutti i separabili blend modalità eccetto `Modulate`, la modalità di sfumatura non ha alcun effetto. Come si è visto in precedenza, il `Modulate` fusione incorpora il canale alfa di moltiplicazione. In caso contrario, `Modulate` ha lo stesso effetto `Multiply`. 

Si noti che le due modalità denominata `ColorDodge` e `ColorBurn`. Le parole _scherma_ e _masterizzare_ ha avuto origine procedure oscura fotografica. Un'utilità rende una stampa fotografica di cercare di sottrarci light tramite un valore negativo. Con la luce non, la stampa è il bianca. La stampa diventa più scura come più luce cade di stampa per un periodo di tempo più lungo. Produttori di stampa utilizzato spesso una mano o un piccolo oggetto per bloccare alcune della luce da che rientrano in una parte determinata di stampa, rendendo più chiaro tale area. Questo è noto come _schermare_. Al contrario, opaco materiale con un foro nel (o mani bloccando la maggior parte della luce) può essere usato per indirizzare più chiaro in un punto particolare viene resa più scura denominato _masterizzazione_.

Il **scherma e Burn** program è molto simile alla **schiarire e scurimento**. Il file XAML è strutturato lo stesso, ma con nomi di elementi diversi e il file code-behind è abbastanza simile, ma l'effetto di queste modalità due blend è piuttosto diverso:

[![Scherma e Burn](separable-images/DodgeAndBurn.png "scherma e Burn")](separable-images/DodgeAndBurn-Large.png#lightbox)

Per le piccole `Slider` valori, il `Lighten` modalità schiarisce scure aree in primo luogo, mentre `ColorDodge` schiarisce in modo più uniforme.

Programmi applicativi l'elaborazione di immagini consentono spesso di schermare e masterizzazione viene limitato per aree specifiche, proprio come in un'oscura. A questo scopo da gradienti o da una bitmap con diverse gradazioni di grigio.

## <a name="exploring-the-separable-blend-modes"></a>Esplorare le modalità di blend separabili

Il **separabili modalità Blend** pagina consente di esaminare tutte le modalità di blend separabili. Visualizza una destinazione di bitmap e un'origine rettangolo colorato mediante una delle modalità blend. 

Il file XAML definisce un `Picker` (per selezionare la modalità di sfumatura) e quattro i dispositivi di scorrimento. I primi tre dispositivi di scorrimento consentono di impostare i componenti rossi, verdi e blu dell'origine. Il quarto cursore deve eseguire l'override di tali valori impostando una gradazione di grigio. Non sono identificati i singoli dispositivi di scorrimento, ma i colori indicano la funzione:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.SeparableBlendModesPage"
             Title="Separable Blend Modes">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                    <x:Static Member="skia:SKBlendMode.Screen" />
                    <x:Static Member="skia:SKBlendMode.Overlay" />
                    <x:Static Member="skia:SKBlendMode.Darken" />
                    <x:Static Member="skia:SKBlendMode.Lighten" />
                    <x:Static Member="skia:SKBlendMode.ColorDodge" />
                    <x:Static Member="skia:SKBlendMode.ColorBurn" />
                    <x:Static Member="skia:SKBlendMode.HardLight" />
                    <x:Static Member="skia:SKBlendMode.SoftLight" />
                    <x:Static Member="skia:SKBlendMode.Difference" />
                    <x:Static Member="skia:SKBlendMode.Exclusion" />
                    <x:Static Member="skia:SKBlendMode.Multiply" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="redSlider"
                MinimumTrackColor="Red"
                MaximumTrackColor="Red"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="greenSlider"
                MinimumTrackColor="Green"
                MaximumTrackColor="Green"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="blueSlider"
                MinimumTrackColor="Blue"
                MaximumTrackColor="Blue"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="graySlider"
                MinimumTrackColor="Gray"
                MaximumTrackColor="Gray"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="colorLabel"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

Il file code-behind carica una delle risorse di bitmap e lo disegna due volte, una volta nella metà superiore dell'area di disegno e di nuovo nella parte inferiore della metà dell'area di disegno:

```csharp
public partial class SeparableBlendModesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(SeparableBlendModesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg"); 

    public SeparableBlendModesPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        if (sender == graySlider)
        {
            redSlider.Value = greenSlider.Value = blueSlider.Value = graySlider.Value;
        }

        colorLabel.Text = String.Format("Color = {0:X2} {1:X2} {2:X2}",
                                        (byte)(255 * redSlider.Value),
                                        (byte)(255 * greenSlider.Value),
                                        (byte)(255 * blueSlider.Value));

        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw bitmap in top half
        SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
        canvas.DrawBitmap(bitmap, rect, BitmapStretch.Uniform);

        // Draw bitmap in bottom halr
        rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
        canvas.DrawBitmap(bitmap, rect, BitmapStretch.Uniform);

        // Get values from XAML controls
        SKBlendMode blendMode =
            (SKBlendMode)(blendModePicker.SelectedIndex == -1 ?
                                        0 : blendModePicker.SelectedItem);

        SKColor color = new SKColor((byte)(255 * redSlider.Value),
                                    (byte)(255 * greenSlider.Value),
                                    (byte)(255 * blueSlider.Value));

        // Draw rectangle with blend mode in bottom half
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = color;
            paint.BlendMode = blendMode;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Nella parte inferiore del `PaintSurface` gestore, un rettangolo viene disegnato sulla seconda bitmap con la modalità di sfumatura selezionato e il colore selezionato. È possibile confrontare la bitmap modificata nella parte inferiore con la bitmap originale nella parte superiore:

[![I metodi di fusione separabili](separable-images/SeparableBlendModes.png "modalità separabili Blend")](separable-images/SeparableBlendModes-Large.png#lightbox)

## <a name="additive-and-subtractive-primary-colors"></a>Additivi e sottrattivi colori primari

Il **colori primari** pagina consente di disegnare tre cerchi sovrapposti di rosso, verde e blu:

[![Colori primari additive](separable-images/PrimaryColors-Additive.png "Additive colori primari")](separable-images/PrimaryColors-Additive.png#lightbox)

Questi sono i colori primari sommano tra loro. Combinazioni di due producono ciano, magenta e giallo e una combinazione di tutti e tre è il bianca.

Questi tre cerchi vengono disegnati con le `SKBlendMode.Plus` modalità, ma è anche possibile usare `Screen`, `Lighten`, o `Difference` per lo stesso effetto. Ecco il programma:

```csharp
public class PrimaryColorsPage : ContentPage
{
    bool isSubtractive;

    public PrimaryColorsPage ()
    {
        Title = "Primary Colors";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;

        // Switch between additive and subtractive primaries at tap
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            isSubtractive ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);

        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        float radius = Math.Min(info.Width, info.Height) / 4;
        float distance = 0.8f * radius;     // from canvas center to circle center
        SKPoint center1 = center + 
            new SKPoint(distance * (float)Math.Cos(9 * Math.PI / 6),
                        distance * (float)Math.Sin(9 * Math.PI / 6));
        SKPoint center2 = center +
            new SKPoint(distance * (float)Math.Cos(1 * Math.PI / 6),
                        distance * (float)Math.Sin(1 * Math.PI / 6));
        SKPoint center3 = center +
            new SKPoint(distance * (float)Math.Cos(5 * Math.PI / 6),
                        distance * (float)Math.Sin(5 * Math.PI / 6));

        using (SKPaint paint = new SKPaint())
        {
            if (!isSubtractive)
            {
                paint.BlendMode = SKBlendMode.Plus; 
                System.Diagnostics.Debug.WriteLine(paint.BlendMode);

                paint.Color = SKColors.Red;
                canvas.DrawCircle(center1, radius, paint);

                paint.Color = SKColors.Lime;    // == (00, FF, 00)
                canvas.DrawCircle(center2, radius, paint);

                paint.Color = SKColors.Blue;
                canvas.DrawCircle(center3, radius, paint);
            }
            else
            {
                paint.BlendMode = SKBlendMode.Multiply
                System.Diagnostics.Debug.WriteLine(paint.BlendMode);

                paint.Color = SKColors.Cyan;
                canvas.DrawCircle(center1, radius, paint);

                paint.Color = SKColors.Magenta;
                canvas.DrawCircle(center2, radius, paint);

                paint.Color = SKColors.Yellow;
                canvas.DrawCircle(center3, radius, paint);
            }
        }
    }
}
```

Il programma include un `TabGestureRecognizer`. Quando si toccare o fare clic su schermo, il programma Usa `SKBlendMode.Multiply` per visualizzare le tre repliche primarie sottrattive:

[![Colori primari sottrattivo](separable-images/PrimaryColors-Subtractive.png "sottrattivo colori primari")](separable-images/PrimaryColors-Subtractive-Large.png#lightbox)

Il `Darken` modalità funziona anche per questo stesso effetto.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
