---
title: Modalità di Blend separabili
description: Utilizzare le modalità di Blend separabili per modificare i colori rosso, verde e blu.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 66D1A537-A247-484E-B5B9-FBCB7838FBE9
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c1939c30cbefdbf8d6546761a8c6ac7199bfff62
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139685"
---
# <a name="the-separable-blend-modes"></a>Modalità di Blend separabili

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Come illustrato nell'articolo modalità di [**Blend di SkiaSharp Porter-Duff**](porter-duff.md), le modalità di Blend di Porter-Duff eseguono in genere operazioni di ritaglio. Le modalità di Blend separabili sono diverse. Le modalità separabili modificano i singoli componenti di colore rosso, verde e blu di un'immagine. Le modalità di Blend separabili possono combinare colori per dimostrare che la combinazione di rosso, verde e blu è effettivamente bianca:

![Colori primari](separable-images/SeparableSample.png "Colori primari")

## <a name="lighten-and-darken-two-ways"></a>Schiarire e scurire due modi 

È comune avere una bitmap troppo scura o troppo leggera. È possibile usare le modalità separable Blend per schiarire o scurire imabe.  In realtà, due delle modalità di Blend separabili nell' [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) enumerazione sono denominate `Lighten` e `Darken` . 

Queste due modalità sono illustrate nella pagina **schiarimento e scurimento** . Il file XAML crea un'istanza `SKCanvasView` di due oggetti e due `Slider` Visualizzazioni:

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

La prima `SKCanvasView` e `Slider` `SKBlendMode.Lighten` la dimostrazione e la seconda coppia dimostrano `SKBlendMode.Darken` . Le due `Slider` visualizzazioni condividono lo stesso `ValueChanged` gestore e le due `SKCanvasView` condividono lo stesso `PaintSurface` gestore. Entrambi i gestori eventi controllano l'oggetto che genera l'evento:

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

Il `PaintSurface` gestore calcola un rettangolo adatto per la bitmap. Il gestore Visualizza la bitmap e quindi Visualizza un rettangolo sulla bitmap usando un `SKPaint` oggetto con la relativa `BlendMode` proprietà impostata su `SKBlendMode.Lighten` o `SKBlendMode.Darken` . La `Color` proprietà è una sfumatura grigia basata sull'oggetto `Slider` . Per la `Lighten` modalità, il colore varia da nero a bianco, ma per la `Darken` modalità è compreso tra bianco e nero.

Le schermate da sinistra a destra mostrano valori sempre più grandi `Slider` quando l'immagine superiore diventa più chiara e l'immagine inferiore diventa più scura:

[![Schiarire e scurire](separable-images/LightenAndDarken.png "Schiarire e scurire")](separable-images/LightenAndDarken-Large.png#lightbox)

Questo programma illustra il modo normale in cui vengono usate le modalità di Blend separabili: la destinazione è un'immagine di un ordinamento, molto spesso una bitmap. L'origine è un rettangolo visualizzato utilizzando un `SKPaint` oggetto con la relativa `BlendMode` proprietà impostata su una modalità di Blend separabile. Il rettangolo può essere un colore a tinta unita (come qui) o una sfumatura. La trasparenza _non_ viene in genere utilizzata con le modalità di Blend separabili.

Quando si sperimenta questo programma, si noterà che queste due modalità di Blend non alleggeriscono e scuriscono l'immagine in modo uniforme. Al contrario, `Slider` sembra impostare una soglia di un ordinamento. Ad esempio, quando si aumenta la `Slider` per la `Lighten` modalità, le aree più scure dell'immagine ottengono la luce prima mentre le aree più chiare rimangono invariate.

Per la `Lighten` modalità, se il pixel di destinazione è il valore di colore RGB (Dr, DG, DB) e il pixel di origine è il colore (SR, SG, SB), l'output è (o, og, ob) calcolato come segue:

 `Or = max(Dr, Sr)` `Og = max(Dg, Sg)`
 `Ob = max(Db, Sb)`

Per il colore rosso, verde e blu separatamente, il risultato è maggiore della destinazione e dell'origine. Ciò produce l'effetto di alleggerire prima le aree scure della destinazione.

La `Darken` modalità è simile, ad eccezione del fatto che il risultato è il minore della destinazione e dell'origine:

 `Or = min(Dr, Sr)` `Og = min(Dg, Sg)`
 `Ob = min(Db, Sb)`

I componenti rosso, verde e blu vengono gestiti separatamente, motivo per cui queste modalità di Blend sono definite modalità di Blend _separabili_ . Per questo motivo, è possibile usare le abbreviazioni **DC** e **SC** per i colori di origine e di destinazione e si comprende che i calcoli si applicano separatamente a ogni componente rosso, verde e blu.

Nella tabella seguente vengono illustrate tutte le modalità di Blend separabili con brevi spiegazioni sulle operazioni eseguite. La seconda colonna Mostra il colore di origine che non produce alcuna modifica:

| Modalità Blend   | Nessuna modifica | Operazione |
| ------------ | --------- | --------- |
| `Plus`       | Nero     | Schiarisce aggiungendo colori: SC + DC |
| `Modulate`   | bianco     | Si scurisce moltiplicando i colori: SC · DC | 
| `Screen`     | Nero     | Complemento al prodotto di complemento: SC + DC &ndash; SC · DC |
| `Overlay`    | Grigio      | Inverso di`HardLight` |
| `Darken`     | bianco     | Minimo colori: min (SC, DC) |
| `Lighten`    | Nero     | Massimo colori: Max (SC, DC) |
| `ColorDodge` | Nero     | Schiarisce la destinazione in base all'origine |
| `ColorBurn`  | bianco     | Scurisce la destinazione in base all'origine | 
| `HardLight`  | Grigio      | Simile all'effetto del primo Spotlight |
| `SoftLight`  | Grigio      | Simile all'effetto del soft Spotlight | 
| `Difference` | Nero     | Sottrae il più scuro dal più chiaro: ABS (DC &ndash; SC) | 
| `Exclusion`  | Nero     | Simile al `Difference` contrasto ma inferiore |
| `Multiply`   | bianco     | Si scurisce moltiplicando i colori: SC · DC |

Algoritmi più dettagliati sono disponibili nella specifica W3C [**compositing and blending Level 1**](https://www.w3.org/TR/compositing-1/) e il [**riferimento SkBlendMode**](https://skia.org/user/api/SkBlendMode_Reference)Skia, anche se la notazione in queste due origini non è la stessa. Tenere presente che `Plus` viene comunemente considerato come modalità di Blend Porter-Duff e `Modulate` non fa parte della specifica W3C.

Se l'origine è trasparente, per tutte le modalità di Blend separabili ad eccezione di `Modulate` , la modalità di Blend non ha alcun effetto. Come illustrato in precedenza, la `Modulate` modalità Blend incorpora il canale alfa nella moltiplicazione. In caso contrario, `Modulate` ha lo stesso effetto di `Multiply` . 

Si notino le due modalità denominate `ColorDodge` e `ColorBurn` . Le parole _Dodge_ e _Burn_ hanno avuto origine in procedure di fotocamera fotografica. Un ampliamento crea una stampa fotografica illuminando la luce su un valore negativo. Con nessuna luce, la stampa è bianca. La stampa diventa più scura, perché la luce ricade sulla stampa per un periodo di tempo più lungo. I creatori di stampa usavano spesso una mano o un piccolo oggetto per impedire che parte della luce rientri in una certa parte della stampa, rendendo tale area più chiara. Questa operazione è nota come _schivata_. Viceversa, il materiale opaco con un foro al suo interno (o che blocca la maggior parte della luce) può essere usato per indirizzare più luce in un particolare punto per scurirlo, denominato _Burning_.

Il programma **Dodge and Burn** è molto simile a **alleggerire e scurire**. Il file XAML è strutturato nello stesso modo, ma con nomi di elementi diversi e il file code-behind è simile, ma l'effetto di queste due modalità di Blend è piuttosto diverso:

[![Dodge e Burn](separable-images/DodgeAndBurn.png "Dodge e Burn")](separable-images/DodgeAndBurn-Large.png#lightbox)

Per i valori di piccole dimensioni `Slider` , la `Lighten` modalità alleggerisce innanzitutto le aree scure, mentre si `ColorDodge` schiarisce più in modo uniforme.

I programmi applicativi per l'elaborazione di immagini spesso consentono la limitazione e la masterizzazione in aree specifiche, esattamente come in una camera oscura. Questa operazione può essere eseguita da gradienti o da una bitmap con sfumature di grigio diverse.

## <a name="exploring-the-separable-blend-modes"></a>Esplorazione delle modalità di Blend separabili

La pagina **modalità di Blend separabile** consente di esaminare tutte le modalità di Blend separabili. Viene visualizzata una destinazione bitmap e un'origine rettangolare colorata utilizzando una delle modalità di Blend. 

Il file XAML definisce `Picker` (per selezionare la modalità di fusione) e quattro dispositivi di scorrimento. I primi tre dispositivi di scorrimento consentono di impostare i componenti rosso, verde e blu dell'origine. Il quarto dispositivo di scorrimento ha lo scopo di eseguire l'override di questi valori impostando una sfumatura grigia. I singoli dispositivi di scorrimento non vengono identificati, ma i colori ne indicano la funzione:

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

Il file code-behind carica una delle risorse bitmap e la disegna due volte, una volta nella metà superiore dell'area di disegno e nuovamente nella metà inferiore dell'area di disegno:

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

Verso la fine del `PaintSurface` gestore, viene disegnato un rettangolo sulla seconda bitmap con la modalità di Blend selezionata e il colore selezionato. È possibile confrontare la bitmap modificata in basso con la bitmap originale nella parte superiore:

[![Modalità di fusione separabili](separable-images/SeparableBlendModes.png "Modalità di fusione separabili")](separable-images/SeparableBlendModes-Large.png#lightbox)

## <a name="additive-and-subtractive-primary-colors"></a>Colori primari additivi e sottrattivi

La pagina **colori primari** disegna tre cerchi sovrapposti di rosso, verde e blu:

[![Colori primari additivi](separable-images/PrimaryColors-Additive.png "Colori primari additivi")](separable-images/PrimaryColors-Additive.png#lightbox)

Si tratta dei colori primari additivi. Le combinazioni di due producono cyan, magenta e Yellow e una combinazione di tutti e tre è bianca.

Questi tre cerchi vengono disegnati con la `SKBlendMode.Plus` modalità, ma è anche possibile usare `Screen` , `Lighten` o `Difference` per lo stesso effetto. Ecco il programma:

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

Il programma include un `TabGestureRecognizer` . Quando si tocca o si fa clic sullo schermo, il programma usa `SKBlendMode.Multiply` per visualizzare le tre primarie sottrattive:

[![Colori primari sottrattivi](separable-images/PrimaryColors-Subtractive.png "Colori primari sottrattivi")](separable-images/PrimaryColors-Subtractive-Large.png#lightbox)

La `Darken` modalità funziona anche per lo stesso effetto.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
