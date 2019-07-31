---
title: Le modalità di blend non separabili
description: Utilizzare le modalità di blend non separabili per modificare la tonalità, saturazione e luminosità.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97FA2730-87C0-4914-8C9F-C64A02CF9EEF
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 9054539b08da89c0f7d8a93150866fb1b41e63f1
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642788"
---
# <a name="the-non-separable-blend-modes"></a>Le modalità di blend non separabili

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Come illustrato nell'articolo [ **modalità di blend in SkiaSharp separabili**](separable.md), le modalità di blend separabili eseguono operazioni sui canali di colore rosso, verde e blu separatamente. Le modalità di blend non separabili non lo sono. Operando al momento i livelli di tonalità, saturazione e luminosità del colore, le modalità di blend non separabili possono modificare i colori in modi interessanti:

![Esempio non separabili](non-separable-images/NonSeparableSample.png "Non separabili esempio")

## <a name="the-hue-saturation-luminosity-model"></a>Il modello di Tonalità-saturazione-luminosità

Per comprendere le modalità di blend non separabili, è necessario trattare i pixel di destinazione e di origine come i colori nel modello di Tonalità-saturazione-luminosità. (Luminosità è detta anche Lightness.)

Il modello di colori HSL illustrato nell'articolo [ **l'integrazione con xamarin. Forms** ](../../basics/integration.md) e un programma di esempio in questo articolo consente la sperimentazione con colori HSL. È possibile creare un `SKColor` valore utilizzando i valori di tonalità, saturazione e luminosità con il metodo statico [ `SKColor.FromHsl` ](xref:SkiaSharp.SKColor.FromHsl*) (metodo).

La tonalità rappresenta la lunghezza d'onda dominante del colore. I valori di tonalità variano da 0 a 360 e passano attraverso le primarie additive e sottrattive: Il valore rosso è 0, il giallo è 60, il verde è 120, cyan è 180, il blu è 240, il magenta è 300 e il ciclo torna a rosso a 360.

Se non vi è alcun colore dominante &mdash; ad esempio, il colore è bianco o il nero o a una gradazione di grigio &mdash; la tonalità è non definita e in genere impostata su 0. 

I valori di saturazione possono variare da 0 a 100 e indicare alla purezza del colore. Un valore di saturazione pari a 100 corrisponde al colore più puri mentre i valori inferiori a 100 causa il colore a diventare più grayish. Un valore di saturazione pari a 0 produce una sfumatura di colore grigio.

Il valore di luminosità (o Lightness) indica il colore chiaro come è. Un valore di luminosità 0 è nero indipendentemente dalle altre impostazioni. Analogamente, un valore di luminosità di 100 è bianco. 

Il valore HSL (0, 100, 50) è il valore RGB (FF, 00, 00), ovvero rosso puro. Il valore HSL (180, 100, 50) è il valore RGB (00, FF, FF), puro ciano. Come viene ridotta la saturazione, il componente di colore dominante è ridotto e gli altri componenti vengono aumentati. A un livello di saturazione pari a 0, tutti i componenti sono uguali e il colore è una gradazione di grigio. Riduzione della luminosità venga in bianco. aumentare la luminosità per passare al bianco.

## <a name="the-blend-modes-in-detail"></a>Le modalità di blend in dettaglio

Come le altre modalità di blend, le quattro modalità blend non separabili implicano una destinazione (che è spesso un'immagine bitmap) e un'origine, che è spesso una sfumatura o un solo colore. I metodi di fusione combinano i valori di tonalità, saturazione e luminosità di destinazione e l'origine:

| Modalità di blend   | Componenti di origine | Componenti dalla destinazione |
| ------------ | ---------------------- | --------------------------- |
| `Hue`        | Tonalità                    | Saturazione e luminosità   |
| `Saturation` | Saturazione             | Hue e luminosità          |
| `Color`      | Colore e la saturazione     | Luminosità                  | 
| `Luminosity` | Luminosità             | Colore e la saturazione          | 

Vedere il W3C [ **la composizione e fusione 1 livello** ](https://www.w3.org/TR/compositing-1/) specifica per gli algoritmi.

Il **modalità Blend Non separabili** pagina contiene un `Picker` per selezionare uno di questi blend le modalità e tre `Slider` viste per selezionare un colore HSL:

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

Per risparmiare spazio, i tre `Slider` viste non vengono identificate nell'interfaccia utente del programma. È necessario tenere presente che l'ordine è tonalità, saturazione e luminosità. Due `Label` visualizzazioni nella parte inferiore della pagina mostrano i valori di colore RGB e HSL.

Il file code-behind carica una delle risorse bitmap, che consente di visualizzare più grande possibile nel canvas e quindi descrive l'area di disegno con un rettangolo. Il colore del rettangolo si basa su tre `Slider` viste e la modalità di sfumatura è quello selezionato nel `Picker`:

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

Si noti che il programma non visualizza il valore del colore HSL come scelto dai tre dispositivi di scorrimento. Ma crea un valore di colore da tali dispositivi di scorrimento e quindi Usa il [ `ToHsl` ](xref:SkiaSharp.SKColor.ToHsl*) metodo per ottenere i valori di tonalità, saturazione e luminosità. Infatti, il `FromHsl` metodo converte un colore HSL in un colore RGB, che viene archiviato internamente nel `SKColor` struttura. Il `ToHsl` metodo esegue la conversione da RGB HSL, ma il risultato non è sempre il valore originale. 

Ad esempio, `FromHsl` converte il valore HSL (180, 50, 0) per il colore RGB (0, 0, 0), in quanto il `Luminosity` è uguale a zero. Il `ToHsl` metodo converte il colore RGB (0, 0, 0) sul colore HSL (0, 0, 0), perché i valori di colore e la saturazione sono irrilevanti. Quando si usa questo programma, è preferibile che noterete la rappresentazione del colore HSL che utilizza il programma anziché di quello specificato con i dispositivi di scorrimento.

Il `SKBlendModes.Hue` modalità blend utilizza il livello di Hue dell'origine, mantenendo i livelli di luminosità e saturazione della destinazione. Quando si testa questa modalità di blend, i dispositivi di scorrimento Saturazione e luminosità deve essere impostate su un elemento diverso da 0 o 100 perché in questi casi, la tonalità non è in modo univoco è definita.

[![Modalità di Blend non separabili - tonalità](non-separable-images/NonSeparableBlendModes-Hue.png "modalità Blend Non separabili - Hue")](non-separable-images/NonSeparableBlendModes-Hue-Large.png#lightbox)

Quando si utilizza set il dispositivo di scorrimento su 0 (come con la schermata iOS a sinistra), tutto diventa rossastri. Ma ciò non significa che l'immagine è totalmente assente di verde e blu. Ovviamente esistono sfumature di grigio ancora presenti nel risultato. Ad esempio, il colore RGB (40, 40, C0) è equivalente al colore HSL (240, 50, 50). La tonalità è blu, ma il valore di saturazione pari a 50 indica che esistono anche componenti rosso e verde. Se la tonalità è impostata su 0 con `SKBlendModes.Hue`, il colore HSL è (0, 50, 50), che è il colore RGB (C0, 40, 40). Sono presenti componenti ancora verde e blu, ma ora il componente dominante è rosso.

Il `SKBlendModes.Saturation` fusione combina il livello di saturazione di origine con la tonalità e luminosità della destinazione. Come tonalità, saturazione non è ben definita se la luminosità è 0 o 100. In teoria, dovrebbe funzionare qualsiasi impostazione luminosità tra questi due estremi. Tuttavia, l'impostazione di luminosità sembra alterare il risultato più del previsto. Impostare la luminosità su 50 e si può vedere come è possibile impostare il livello di saturazione dell'immagine:

[![Modalità di Blend non separabili - saturazione](non-separable-images/NonSeparableBlendModes-Saturation.png "modalità Blend Non separabili - saturazione")](non-separable-images/NonSeparableBlendModes-Saturation-Large.png#lightbox)

È possibile usare questa modalità di blend per aumentare il colore di saturazione di un'immagine spogli oppure è possibile ridurre la saturazione fino a zero (come illustrato nella schermata iOS a sinistra) per un'immagine risultante, composta da solo sfumature di grigio.

Il `SKBlendModes.Color` fusione mantiene la luminosità dell'oggetto di destinazione, ma usa il colore e la saturazione dell'origine. Anche in questo caso, ciò significa che tutte le impostazioni del dispositivo di scorrimento in una posizione tra i due casi estremi luminosità dovrebbero funzionare. 

[![Modalità Blend non separabili - colorare](non-separable-images/NonSeparableBlendModes-Color.png "modalità Blend Non separabili - colore")](non-separable-images/NonSeparableBlendModes-Color-Large.png#lightbox)

Si vedrà a breve un'applicazione di questa modalità blend.

Infine, il `SKBlendModes.Luminosity` modalità blend è l'opposto di `SKBlendModes.Color`. Mantiene il colore e la saturazione della destinazione, ma usa la luminosità dell'origine. La `Luminosity` modalità Blend è la più misteriosa del batch: I dispositivi di scorrimento Hue e saturazione influiscono sull'immagine, ma anche alla luminosità media, l'immagine non è diversa:

[![Modalità di Blend non separabili - luminosità](non-separable-images/NonSeparableBlendModes-Luminosity.png "modalità Blend Non separabili - luminosità")](non-separable-images/NonSeparableBlendModes-Luminosity-Large.png#lightbox)

In teoria, aumentando o riducendo la luminosità di un'immagine necessario renderlo più chiaro o scuro. È interessante notare che il [esempio di luminosità nella Skia **riferimento SkBlendMode** ](https://skia.org/user/api/SkBlendMode_Reference#Luminosity) è abbastanza simile.

In genere non è il caso che è opportuno usare una delle modalità blend non separabili con un'origine che è costituito da un solo colore applicato all'immagine di destinazione intero. L'effetto è troppo grande. È opportuno limitare l'effetto di una parte dell'immagine. In tal caso, l'origine probabile incorporerà transparancy, o forse l'origine sarà limitata a un elemento grafico più piccolo.

## <a name="a-matte-for-a-separable-mode"></a>Per una modalità separabile mascherino

Ecco un'immagine bitmap inclusa come risorsa nel [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) esempio. Il nome del file viene **Banana.jpg**:

![Monkey banana](non-separable-images/Banana.jpg "Monkey Banana")

È possibile creare un alone che include solo il banana. Si tratta inoltre di una risorsa nel [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) esempio. Il nome del file viene **BananaMatte.png**:

![Banana mascherino](non-separable-images/BananaMatte.png "mascherino Banana")

Insieme alla forma banana nero, il resto della bitmap è trasparente.

Il **Banana blu** pagina usa tale mascherino per modificare il colore e la saturazione di banana che mantiene la monkey, ma modificare esclusivamente nell'immagine. 

Di seguito `BlueBananaPage` (classe), il **Banana.jpg** bitmap viene caricato come un campo. Il costruttore di caricamenti il **BananaMatte.png** bitmap come il `matteBitmap` oggetto, ma non viene mantenuto tale oggetto oltre il costruttore. Al contrario, una terza bitmap denominata `blueBananaBitmap` viene creato. Il `matteBitmap` viene disegnato sul `blueBananaBitmap` seguita da un `SKPaint` con relativo `Color` impostato su blu e il relativo `BlendMode` impostato su `SKBlendMode.SrcIn`. Il `blueBananaBitmap` rimangono quasi del tutto trasparente, ma con un'immagine di blu pure a tinta unita del banana:

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

Il `PaintSurface` gestore la bitmap disegnata con la monkey che contiene il banana. Questo codice è seguito dalla visualizzazione delle `blueBananaBitmap` con `SKBlendMode.Color`. Sulla superficie della finestra di banana, colore e la saturazione di ciascun pixel viene sostituito da blu a tinta unita, che corrisponde a un valore di tonalità di 240 e un valore di saturazione pari a 100. La luminosità, tuttavia, rimane invariato, ovvero che il banana continua a disporre di una trama realistica nonostante il nuovo colore:

[![Blu Banana](non-separable-images/BlueBanana.png "blu Banana")](non-separable-images/BlueBanana-Large.png#lightbox)

Provare a modificare la modalità di sfumatura da `SKBlendMode.Saturation`. Il banana rimane giallo ma si tratta di un più intenso è giallo. In un'applicazione reale, potrebbe trattarsi di un effetto più vantaggiosa dell'attivazione di banana blu.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
