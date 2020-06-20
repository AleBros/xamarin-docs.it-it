---
title: Modalità di Blend non separabili
description: Usare le modalità di Blend non separabili per modificare tonalità, saturazione o luminosità.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97FA2730-87C0-4914-8C9F-C64A02CF9EEF
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 52be7641ac3b2983f537e11bccd76f2a5b52574d
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84130182"
---
# <a name="the-non-separable-blend-modes"></a>Modalità di Blend non separabili

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Come illustrato nell'articolo [**SkiaSharp separable Blend mode**](separable.md), le modalità di Blend separabile eseguono operazioni sui canali rosso, verde e blu separatamente. Le modalità di Blend non separabili non sono disponibili. Operando sui livelli di tonalità, saturazione e luminosità del colore, le modalità di Blend non separabili possono modificare i colori in modi interessanti:

![Esempio non separabile](non-separable-images/NonSeparableSample.png "Esempio non separabile")

## <a name="the-hue-saturation-luminosity-model"></a>Modello Hue-Saturation-luminosità

Per comprendere le modalità di Blend non separabili, è necessario considerare i pixel di destinazione e di origine come colori nel modello Hue-Saturation-luminosità. (Luminosità è detta anche leggerezza).

Il modello di colore HSL è stato discusso nell'articolo [**integrazione con Xamarin.Forms **](../../basics/integration.md) e un programma di esempio in questo articolo consente la sperimentazione con colori HSL. È possibile creare un `SKColor` valore usando i valori di tonalità, saturazione e luminosità con il [`SKColor.FromHsl`](xref:SkiaSharp.SKColor.FromHsl*) metodo statico.

Hue rappresenta la lunghezza di onda dominante del colore. I valori di tonalità variano da 0 a 360 e passano attraverso le primarie additive e sottrattive: rosso è il valore 0, il giallo è 60, il verde è 120, cyan è 180, blu è 240, Magenta è 300 e il ciclo torna a rosso a 360.

Se ad esempio non è presente alcun colore dominante &mdash; , il colore è bianco o nero o una sfumatura grigia, &mdash; quindi la tonalità non è definita e in genere è impostata su 0. 

I valori di saturazione possono variare da 0 a 100 e indicare la purezza del colore. Il valore di saturazione 100 è il colore più puro mentre i valori inferiori a 100 fanno sì che il colore diventi più grigio. Il valore di saturazione 0 produce una sfumatura di grigio.

Il valore di luminosità (o luminosità) indica la luminosità del colore. Un valore di luminosità pari a 0 è nero indipendentemente dalle altre impostazioni. Analogamente, un valore di luminosità di 100 è bianco. 

Il valore HSL (0, 100, 50) è il valore RGB (FF, 00, 00), che è rosso puro. Il valore HSL (180, 100, 50) è il valore RGB (00, FF, FF), cyan puro. Con la riduzione della saturazione, il componente colore dominante viene ridotto e gli altri componenti vengono aumentati. A un livello di saturazione pari a 0, tutti i componenti sono uguali e il colore è una sfumatura grigia. Diminuire la luminosità per passare al nero; aumentare la luminosità per passare al bianco.

## <a name="the-blend-modes-in-detail"></a>Modalità di Blend in dettaglio

Analogamente alle altre modalità di Blend, le quattro modalità di Blend non separabili coinvolgono una destinazione (che è spesso un'immagine bitmap) e un'origine, che è spesso un singolo colore o una sfumatura. Le modalità di Blend combinano i valori di tonalità, saturazione e luminosità dalla destinazione e dall'origine:

| Modalità Blend   | Componenti dall'origine | Componenti dalla destinazione |
| ------------ | ---------------------- | --------------------------- |
| `Hue`        | Tonalità                    | Saturazione e luminosità   |
| `Saturation` | Saturazione             | Tonalità e luminosità          |
| `Color`      | Tonalità e saturazione     | Luminosità                  | 
| `Luminosity` | Luminosità             | Tonalità e saturazione          | 

Per gli algoritmi, vedere la specifica W3C [**compositing and blending Level 1**](https://www.w3.org/TR/compositing-1/) .

La pagina **modalità Blend non separabile** contiene un oggetto `Picker` per selezionare una di queste modalità di combinazione e tre `Slider` visualizzazioni per selezionare un colore HSL:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.NonSeparableBlendModesPage"
             Title="Non-Separable Blend Modes">

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
                    <x:Static Member="skia:SKBlendMode.Hue" />
                    <x:Static Member="skia:SKBlendMode.Saturation" />
                    <x:Static Member="skia:SKBlendMode.Color" />
                    <x:Static Member="skia:SKBlendMode.Luminosity" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="satSlider"
                Maximum="100"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="lumSlider"
                Maximum="100"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <StackLayout Orientation="Horizontal">
            <Label x:Name="hslLabel"
                   HorizontalOptions="CenterAndExpand" />

            <Label x:Name="rgbLabel"
                   HorizontalOptions="CenterAndExpand" />

        </StackLayout>
    </StackLayout>
</ContentPage>
```

Per risparmiare spazio, le tre `Slider` visualizzazioni non vengono identificate nell'interfaccia utente del programma. È necessario ricordare che l'ordine è tonalità, saturazione e luminosità. Due `Label` visualizzazioni nella parte inferiore della pagina mostrano i valori dei colori HSL e RGB.

Il file code-behind carica una delle risorse bitmap, ne Visualizza il maggior numero possibile nell'area di disegno e quindi copre l'area di disegno con un rettangolo. Il colore del rettangolo è basato sulle tre `Slider` visualizzazioni e la modalità di Blend è quella selezionata in `Picker` :

```csharp
public partial class NonSeparableBlendModesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(NonSeparableBlendModesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKColor color;

    public NonSeparableBlendModesPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        // Calculate new color based on sliders
        color = SKColor.FromHsl((float)hueSlider.Value,
                                (float)satSlider.Value,
                                (float)lumSlider.Value);

        // Use labels to display HSL and RGB color values
        color.ToHsl(out float hue, out float sat, out float lum);

        hslLabel.Text = String.Format("HSL = {0:F0} {1:F0} {2:F0}",
                                      hue, sat, lum);

        rgbLabel.Text = String.Format("RGB = {0:X2} {1:X2} {2:X2}",
                                      color.Red, color.Green, color.Blue);

        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);

        // Get blend mode from Picker
        SKBlendMode blendMode =
            (SKBlendMode)(blendModePicker.SelectedIndex == -1 ?
                                        0 : blendModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = color;
            paint.BlendMode = blendMode;
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Si noti che il programma non Visualizza il valore del colore HSL selezionato dai tre dispositivi di scorrimento. Viene invece creato un valore di colore da tali dispositivi di scorrimento, quindi viene utilizzato il [`ToHsl`](xref:SkiaSharp.SKColor.ToHsl*) metodo per ottenere i valori di tonalità, saturazione e luminosità. Questo perché il `FromHsl` metodo converte un colore HSL in un colore RGB, che viene archiviato internamente nella `SKColor` struttura. Il `ToHsl` metodo converte da RGB a HSL, ma il risultato non sarà sempre il valore originale. 

Ad esempio, `FromHsl` converte il valore HSL (180, 50, 0) nel colore RGB (0, 0, 0) perché `Luminosity` è zero. Il `ToHsl` metodo converte il colore RGB (0, 0, 0) nel colore HSL (0, 0, 0) perché i valori di tonalità e saturazione sono irrilevanti. Quando si utilizza questo programma, è preferibile visualizzare la rappresentazione del colore HSL utilizzato dal programma anziché quello specificato con i dispositivi di scorrimento.

La `SKBlendModes.Hue` modalità di Blend utilizza il livello di tonalità dell'origine, mantenendo i livelli di saturazione e luminosità della destinazione. Quando si esegue il test di questa modalità di Blend, i dispositivi di scorrimento di saturazione e luminosità devono essere impostati su un valore diverso da 0 o 100 perché, in questi casi, la tonalità non è definita in modo univoco.

[![Modalità di Blend non separabili-Hue](non-separable-images/NonSeparableBlendModes-Hue.png "Modalità di Blend non separabili-Hue")](non-separable-images/NonSeparableBlendModes-Hue-Large.png#lightbox)

Quando si usa impostare il dispositivo di scorrimento su 0 (come nello screenshot iOS a sinistra), tutto risulta rossastro. Tuttavia, ciò non significa che l'immagine è interamente assente di verde e blu. Nel risultato sono ovviamente ancora presenti sfumature di grigio. Ad esempio, il colore RGB (40, 40, C0) equivale al colore HSL (240, 50, 50). Hue è blu, ma il valore di saturazione 50 indica che sono presenti anche componenti rosso e verde. Se Hue è impostato su 0 con `SKBlendModes.Hue` , il colore HSL è (0, 50, 50), che corrisponde al colore RGB (C0, 40, 40). Sono ancora presenti componenti blu e verde, ma ora il componente dominante è rosso.

La `SKBlendModes.Saturation` modalità Blend combina il livello di saturazione dell'origine con la tonalità e la luminosità della destinazione. Analogamente alla tonalità, la saturazione non è ben definita se la luminosità è 0 o 100. In teoria, qualsiasi impostazione di luminosità tra questi due estremi dovrebbe funzionare. Tuttavia, l'impostazione della luminosità sembra influire sul risultato più di quanto previsto. Impostare luminosità su 50 ed è possibile vedere come impostare il livello di saturazione dell'immagine:

[![Modalità di Blend non separabili-saturazione](non-separable-images/NonSeparableBlendModes-Saturation.png "Modalità di Blend non separabili-saturazione")](non-separable-images/NonSeparableBlendModes-Saturation-Large.png#lightbox)

È possibile usare questa modalità di Blend per aumentare la saturazione del colore di un'immagine opaca oppure è possibile ridurre la saturazione fino a zero (come nello screenshot iOS a sinistra) per un'immagine risultante composta solo da sfumature di grigio.

La `SKBlendModes.Color` modalità Blend mantiene la luminosità della destinazione, ma usa la tonalità e la saturazione dell'origine. Anche in questo caso, ciò implica che qualsiasi impostazione del dispositivo di scorrimento della luminosità in un punto qualsiasi tra gli estremi dovrebbe funzionare. 

[![Modalità di Blend non separabili-colore](non-separable-images/NonSeparableBlendModes-Color.png "Modalità di Blend non separabili-colore")](non-separable-images/NonSeparableBlendModes-Color-Large.png#lightbox)

Verrà visualizzata a breve un'applicazione di questa modalità di Blend.

Infine, la `SKBlendModes.Luminosity` modalità di Blend è il contrario di `SKBlendModes.Color` . Mantiene la tonalità e la saturazione della destinazione, ma usa la luminosità dell'origine. La `Luminosity` modalità Blend è la più misteriosa del batch: i dispositivi di scorrimento Hue e saturazione influiscono sull'immagine, ma anche alla luminosità media, l'immagine non è diversa:

[![Modalità di Blend non separabili-luminosità](non-separable-images/NonSeparableBlendModes-Luminosity.png "Modalità di Blend non separabili-luminosità")](non-separable-images/NonSeparableBlendModes-Luminosity-Large.png#lightbox)

In teoria, aumentare o diminuire la luminosità di un'immagine dovrebbe renderlo più chiaro o più scuro. È interessante notare che l' [esempio di luminosità nel **riferimento SkBlendMode** di Skia](https://skia.org/user/api/SkBlendMode_Reference#Luminosity) è molto simile.

In genere, non è consigliabile usare una delle modalità di Blend non separabili con un'origine costituita da un singolo colore applicato all'intera immagine di destinazione. L'effetto è troppo grande. È possibile limitare l'effetto a una parte dell'immagine. In tal caso, è probabile che l'origine includa trasparenza o che l'origine sarà limitata a un grafico di dimensioni inferiori.

## <a name="a-matte-for-a-separable-mode"></a>Opaco per una modalità separabile

Ecco una delle bitmap incluse come risorsa nell'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . Il nome del file è **Banana.jpg**:

![Scimmia banana](non-separable-images/Banana.jpg "Scimmia banana")

È possibile creare una mascherina che comprende solo la banana. Si tratta anche di una risorsa nell'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . Il nome del file è **BananaMatte.png**:

![Banana opaco](non-separable-images/BananaMatte.png "Banana opaco")

A parte la forma banana nera, il resto della bitmap è trasparente.

La pagina **blu banana** usa tale opacità per modificare la tonalità e la saturazione della banana che la scimmia mantiene, ma per non modificare nient'altro nell'immagine. 

Nella classe seguente `BlueBananaPage` la bitmap **Banana.jpg** viene caricata come campo. Il costruttore carica la bitmap **BananaMatte.png** come `matteBitmap` oggetto, ma non mantiene tale oggetto oltre il costruttore. Viene invece creata una terza bitmap denominata `blueBananaBitmap` . `matteBitmap`Viene disegnato su `blueBananaBitmap` seguito da un oggetto `SKPaint` con la proprietà `Color` impostata su blu e la relativa proprietà `BlendMode` impostata su `SKBlendMode.SrcIn` . L'oggetto `blueBananaBitmap` rimane principalmente trasparente, ma con un'immagine a tinta unita pura della banana:

```csharp
public class BlueBananaPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BlueBananaPage),
        "SkiaSharpFormsDemos.Media.Banana.jpg");

    SKBitmap blueBananaBitmap;

    public BlueBananaPage()
    {
        Title = "Blue Banana";

        // Load banana matte bitmap (black on transparent)
        SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
            typeof(BlueBananaPage),
            "SkiaSharpFormsDemos.Media.BananaMatte.png");

        // Create a bitmap with a solid blue banana and transparent otherwise
        blueBananaBitmap = new SKBitmap(matteBitmap.Width, matteBitmap.Height);

        using (SKCanvas canvas = new SKCanvas(blueBananaBitmap))
        {
            canvas.Clear();
            canvas.DrawBitmap(matteBitmap, new SKPoint(0, 0));

            using (SKPaint paint = new SKPaint())
            {
                paint.Color = SKColors.Blue;
                paint.BlendMode = SKBlendMode.SrcIn;
                canvas.DrawPaint(paint);
            }
        }

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);

        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = SKBlendMode.Color;
            canvas.DrawBitmap(blueBananaBitmap, 
                              info.Rect, 
                              BitmapStretch.Uniform, 
                              paint: paint);
        }
    }
}
```

Il `PaintSurface` gestore disegna la bitmap con la scimmia che contiene la banana. Questo codice è seguito dalla visualizzazione di `blueBananaBitmap` con `SKBlendMode.Color` . Sulla superficie della banana, la tonalità e la saturazione di ogni pixel viene sostituita dal blu continuo, che corrisponde a un valore di tonalità di 240 e a un valore di saturazione pari a 100. La luminosità, tuttavia, rimane invariata, il che significa che la banana continua a avere una trama realistica nonostante il nuovo colore:

[![Banana blu](non-separable-images/BlueBanana.png "Banana blu")](non-separable-images/BlueBanana-Large.png#lightbox)

Provare a modificare la modalità di Blend in `SKBlendMode.Saturation` . La banana rimane gialla ma è un colore giallo più intenso. In un'applicazione reale, questo potrebbe essere un effetto più auspicabile rispetto alla trasformazione della banana blu.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
