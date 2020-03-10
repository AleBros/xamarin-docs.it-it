---
title: Metodi di fusione porter Duff
description: Utilizzare i metodi di fusione Porter Duff per comporre le scene basate su immagini di origine e destinazione.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 57F172F8-BA03-43EC-A215-ED6B78696BB5
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: c15dd4606a75cc3cdffbad71f15299568157213a
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916393"
---
# <a name="porter-duff-blend-modes"></a>Metodi di fusione porter Duff

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

I metodi di fusione Porter Duff sono nominati a Thomas Porter e Tom Duff, che ha sviluppato un algebra di composizione durante l'utilizzo per Lucasfilm. Il documento relativo alla [_composizione di immagini digitali_](https://graphics.pixar.com/library/Compositing/paper.pdf) è stato pubblicato nel numero di luglio 1984 della _grafica del computer_, pagine da 253 a 259. Queste modalità di blend sono essenziali per la composizione di cui è assemblando diverse immagini in una scena composita:

![Esempio di Porter-Duff](porter-duff-images/PorterDuffSample.png "Esempio di Porter-Duff")

## <a name="porter-duff-concepts"></a>Concetti di porter Duff

Si supponga che un rettangolo brunastro occupa la superiore e sinistro due-terzi della superficie di visualizzazione:

![Destinazione Porter-Duff](porter-duff-images/PorterDuffDst.png "Destinazione Porter-Duff")

Quest'area è detta _destinazione_ o a volte lo _sfondo_ o lo _sfondo_.

Si vuole disegnare il rettangolo seguente, che è la stessa dimensione della matrice di destinazione. Il rettangolo è trasparente, ad eccezione di un'area blu che occupa destra e sotto due terzi:

![Origine Porter-Duff](porter-duff-images/PorterDuffSrc.png "Origine Porter-Duff")

Questa operazione viene definita _origine_ o a volte in _primo piano_.

Quando si visualizza l'origine nella destinazione, in questo caso è quello previsto:

![Origine Porter-Duff](porter-duff-images/PorterDuffSrcOver.png "Origine Porter-Duff")

I pixel trasparenti dell'origine consentono il colore di sfondo, mentre i pixel origine blu nascondano lo sfondo. Questo è il caso normale e viene definito in SkiaSharp come `SKBlendMode.SrcOver`. Tale valore è l'impostazione predefinita della proprietà `BlendMode` quando viene creata una prima istanza di un oggetto `SKPaint`.

Tuttavia, è possibile specificare una modalità blend diversi per un effetto di diversi. Se si specifica `SKBlendMode.DstOver`, quindi nell'area in cui si intersecano l'origine e la destinazione, viene visualizzata la destinazione anziché l'origine:

![Destinazione Porter-Duff su](porter-duff-images/PorterDuffDstOver.png "Destinazione Porter-Duff su")

La modalità `SKBlendMode.DstIn` Blend Visualizza solo l'area in cui si intersecano la destinazione e l'origine utilizzando il colore di destinazione:

![Destinazione Porter-Duff in](porter-duff-images/PorterDuffDstIn.png "Destinazione Porter-Duff in")

La modalità di Blend di `SKBlendMode.Xor` (OR esclusivo) non determina la visualizzazione di alcuna posizione in cui si sovrappongono le due aree:

![Porter-Duff esclusivo o](porter-duff-images/PorterDuffXor.png "Porter-Duff esclusivo o")

L'origine e destinazione di rettangoli colorati in modo efficace dividono la superficie di visualizzazione in quattro aree univoche che possono essere colorate in vari modi corrispondenti alla presenza dei rettangoli di origine e destinazione:

![Porter-Duff](porter-duff-images/PorterDuff.png "Porter Duff")

I rettangoli in alto a destra e in basso a sinistra sono sempre vuoti perché la destinazione e origine sono trasparenti in tali aree. Il colore di destinazione occupa l'area angolo superiore sinistro, in modo che l'area può essere impostata con il colore di destinazione o non ereditarli affatto. Analogamente, il colore di origine occupa l'area in basso a destra, in modo che può essere di colore con il colore di origine o non ereditarli affatto. L'intersezione tra la destinazione e di origine nella parte centrale può essere impostato con il colore di destinazione, il colore di origine, o niente affatto.

Il numero totale di combinazioni è 2 (per l'angolo superiore sinistro) volte 2 (angolo inferiore destro) volte 3 (per area) o 12. Queste sono le modalità di composizione Porter Duff base 12.

Verso la fine della _composizione di immagini digitali_ (pagina 256), Porter e Duff aggiungono una modalità XIII denominata Plus (corrispondente al membro `SKBlendMode.Plus` SkiaSharp e alla modalità _più_ _chiara_ W3C, che non deve essere confusa con la modalità di _alleggerimento_ W3C). Questa modalità di `Plus` aggiunge i colori di origine e di destinazione, un processo che verrà descritto più dettagliatamente a breve.

Skia aggiunge una modalità 14a denominata `Modulate` che è molto simile a `Plus` ad eccezione del fatto che i colori di origine e di destinazione sono moltiplicati. Ma può essere trattato come una modalità di blend Porter Duff aggiuntiva.

Di seguito sono le modalità Porter Duff 14 come definito in SkiaSharp. La tabella mostra come essi colori ognuna delle tre aree non vuota nel diagramma precedente:

| Modalità       | Destination | Intersezione | Origine |
| ---------- |:-----------:|:------------:|:------:|
| `Clear`    |             |              |        |
| `Src`      |             | Origine       | X      |
| `Dst`      | X           | Destination  |        |
| `SrcOver`  | X           | Origine       | X      |
| `DstOver`  | X           | Destination  | X      |
| `SrcIn`    |             | Origine       |        |
| `DstIn`    |             | Destination  |        |
| `SrcOut`   |             |              | X      |
| `DstOut`   | X           |              |        |
| `SrcATop`  | X           | Origine       |        |
| `DstATop`  |             | Destination  | X      |
| `Xor`      | X           |              | X      |
| `Plus`     | X           | Sum          | X      |
| `Modulate` |             | Prodotto      |        | 

Queste modalità di blend sono simmetrici. L'origine e destinazione possono essere scambiati e tutte le modalità sono ancora disponibili.

La convenzione di denominazione delle modalità segue alcune semplici regole:

- **Src** o **DST** indica che sono visibili solo i pixel di origine o di destinazione.
- Il suffisso **over** indica cosa è visibile nell'intersezione. L'origine o la destinazione viene disegnato "o" l'altra.
- Il suffisso **in** indica che solo l'intersezione è colorata. L'output è limitato al solo la parte dell'origine o di destinazione "in" altro.
- Il suffisso **out** indica che l'intersezione non è colorata. L'output è solo la parte dell'origine o destinazione che è "out" del punto di intersezione.
- Il suffisso in **cima** è l'Unione di **all'interno** e all' **esterno**. Include l'area in cui l'origine o la destinazione è "in cima" dell'altra.

Si noti la differenza con le modalità `Plus` e `Modulate`. Queste modalità siano eseguendo un altro tipo di calcolo sui pixel origine e destinazione. Vengono descritte in dettaglio tra breve.

La pagina della **griglia Porter-Duff** Mostra tutte e 14 le modalità in una schermata sotto forma di griglia. Ogni modalità è un'istanza separata di `SKCanvasView`. Per questo motivo, una classe viene derivata da `SKCanvasView` `PorterDuffCanvasView`denominata. Il costruttore statico consente di creare due immagini bitmap della stessa dimensione, una con un rettangolo brunastro nella relativa area angolo superiore sinistro e un'altra con un rettangolo blu:

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    static SKBitmap srcBitmap, dstBitmap;

    static PorterDuffCanvasView()
    {
        dstBitmap = new SKBitmap(300, 300);
        srcBitmap = new SKBitmap(300, 300);

        using (SKPaint paint = new SKPaint())
        {
            using (SKCanvas canvas = new SKCanvas(dstBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0xC0, 0x80, 0x00);
                canvas.DrawRect(new SKRect(0, 0, 200, 200), paint);
            }
            using (SKCanvas canvas = new SKCanvas(srcBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0x00, 0x80, 0xC0);
                canvas.DrawRect(new SKRect(100, 100, 300, 300), paint);
            }
        }
    }
    ···
}
```

Il costruttore di istanza ha un parametro di tipo `SKBlendMode`. Salva questo parametro in un campo. 

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    ···
    SKBlendMode blendMode;

    public PorterDuffCanvasView(SKBlendMode blendMode)
    {
        this.blendMode = blendMode;
    }

    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest square that fits
        float rectSize = Math.Min(info.Width, info.Height);
        float x = (info.Width - rectSize) / 2;
        float y = (info.Height - rectSize) / 2;
        SKRect rect = new SKRect(x, y, x + rectSize, y + rectSize);

        // Draw destination bitmap
        canvas.DrawBitmap(dstBitmap, rect);

        // Draw source bitmap
        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = blendMode;
            canvas.DrawBitmap(srcBitmap, rect, paint);
        }

        // Draw outline
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 2;
            rect.Inflate(-1, -1);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Il `OnPaintSurface` override disegna le due bitmap. La prima viene disegnata in genere:

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

Il secondo viene disegnato con un oggetto `SKPaint` in cui la proprietà `BlendMode` è stata impostata sull'argomento del costruttore:

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

Il resto della `OnPaintSurface` override disegna un rettangolo attorno alla bitmap per indicare le dimensioni.

La classe `PorterDuffGridPage` crea quattordici istanze di `PorterDurffCanvasView`, una per ogni membro della matrice di `blendModes`. L'ordine dei membri `SKBlendModes` nella matrice è leggermente diverso dalla tabella per poter posizionare le modalità simili adiacenti. Le 14 istanze di `PorterDuffCanvasView` sono organizzate insieme alle etichette in una `Grid`:

```csharp
public class PorterDuffGridPage : ContentPage
{
    public PorterDuffGridPage()
    {
        Title = "Porter-Duff Grid";

        SKBlendMode[] blendModes =
        {
            SKBlendMode.Src, SKBlendMode.Dst, SKBlendMode.SrcOver, SKBlendMode.DstOver,
            SKBlendMode.SrcIn, SKBlendMode.DstIn, SKBlendMode.SrcOut, SKBlendMode.DstOut,
            SKBlendMode.SrcATop, SKBlendMode.DstATop, SKBlendMode.Xor, SKBlendMode.Plus,
            SKBlendMode.Modulate, SKBlendMode.Clear
        };

        Grid grid = new Grid
        {
            Margin = new Thickness(5)
        };

        for (int row = 0; row < 4; row++)
        {
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Star });
        }

        for (int col = 0; col < 3; col++)
        {
            grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });
        }

        for (int i = 0; i < blendModes.Length; i++)
        {
            SKBlendMode blendMode = blendModes[i];
            int row = 2 * (i / 4);
            int col = i % 4;

            Label label = new Label
            {
                Text = blendMode.ToString(),
                HorizontalTextAlignment = TextAlignment.Center
            };
            Grid.SetRow(label, row);
            Grid.SetColumn(label, col);
            grid.Children.Add(label);

            PorterDuffCanvasView canvasView = new PorterDuffCanvasView(blendMode);

            Grid.SetRow(canvasView, row + 1);
            Grid.SetColumn(canvasView, col);
            grid.Children.Add(canvasView);
        }

        Content = grid;
    }
}
```

Il risultato è il seguente:

[![Griglia di Porter-Duff](porter-duff-images/PorterDuffGrid.png "Griglia di Porter-Duff")](porter-duff-images/PorterDuffGrid-Large.png#lightbox)

È opportuno per indurre manualmente che è fondamentale per il corretto funzionamento delle modalità di blend Porter Duff trasparenza. La classe `PorterDuffCanvasView` contiene un totale di tre chiamate al metodo `Canvas.Clear`. Tutti gli elementi usare il metodo senza parametri, che imposta tutti i pixel su trasparente:

```csharp
canvas.Clear();
```

Provare a eseguire la modifica di una di queste chiamate in modo che i pixel vengono impostati su nero opaco:

```csharp
canvas.Clear(SKColors.White);
```

In seguito tale modifica, alcune delle modalità di blend sembrerà funzionare, ma altri utenti non funzionerà. Se si imposta lo sfondo della bitmap di origine su bianco, la modalità di `SrcOver` non funziona perché non sono presenti pixel trasparenti nella bitmap di origine per consentire la visualizzazione della destinazione. Se si imposta lo sfondo della bitmap di destinazione o dell'area di disegno su bianco, `DstOver` non funziona perché la destinazione non contiene pixel trasparenti.

Potrebbe esserci una tentazione di sostituire le bitmap nella pagina della **griglia di Porter-Duff** con chiamate di `DrawRect` più semplici. Che funzionerà per il rettangolo di destinazione ma non per il rettangolo di origine. Il rettangolo di origine deve includere solo l'area di colore blu. Il rettangolo di origine deve includere un'area trasparente che corrisponde all'area colorata della destinazione. In questi solo a questo punto si mescoleranno lavoro modalità.

## <a name="using-mattes-with-porter-duff"></a>Uso di mascherini con Porter Duff

La pagina di **composizione Brick-Wall** Mostra un esempio di un'attività di composizione classica: un'immagine deve essere assemblata da più parti, inclusa una bitmap con uno sfondo che deve essere eliminata. Di seguito è illustrata la bitmap **SeatedMonkey. jpg** con lo sfondo problematico:

![Scimmia seduto](porter-duff-images/SeatedMonkey.jpg "Scimmia seduto")

In preparazione per la composizione, è stato creato un _opaco_ corrispondente, ovvero un'altra bitmap che è nera dove si desidera che l'immagine venga visualizzata e trasparente in caso contrario. Questo file è denominato **SeatedMonkeyMatte. png** ed è tra le risorse nella cartella **media** dell'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

![Scimmia seduto opaco](porter-duff-images/SeatedMonkeyMatte.png "Scimmia seduto opaco")

_Non_ si tratta di un matte creato in modo esperto. In modo ottimale, mascherino deve includere un pixel parzialmente trasparente intorno ai bordi dei pixel nero e questo mascherino non esiste.

Il file XAML per la pagina di **composizione Brick-Wall** crea un'istanza di un `SKCanvasView` e un `Button` che guida l'utente attraverso il processo di composizione dell'immagine finale:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BrickWallCompositingPage"
             Title="Brick-Wall Compositing">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Show sitting monkey"
                HorizontalOptions="Center"
                Margin="0, 10"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

Il file code-behind carica le due bitmap necessarie e gestisce l'evento `Clicked` della `Button`. Per ogni `Button` fare clic, il campo `step` viene incrementato e viene impostata una nuova proprietà `Text` per la `Button`. Quando `step` raggiunge 5, viene impostato di nuovo su 0:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkeyMatte.png");

    int step = 0;

    public BrickWallCompositingPage ()
    {
        InitializeComponent ();
    }

    void OnButtonClicked(object sender, EventArgs args)
    {
        Button btn = (Button)sender;
        step = (step + 1) % 5;

        switch (step)
        {
            case 0: btn.Text = "Show sitting monkey"; break;
            case 1: btn.Text = "Draw matte with DstIn"; break;
            case 2: btn.Text = "Draw sidewalk with DstOver"; break;
            case 3: btn.Text = "Draw brick wall with DstOver"; break;
            case 4: btn.Text = "Reset"; break;
        }

        canvasView.InvalidateSurface();
    }
    
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        ···
    }
}
```

Quando il programma viene eseguito per la prima volta, non viene visualizzato alcun elemento tranne il `Button`:

[![Passaggio 0 di composizione Brick-Wall](porter-duff-images/BrickWallCompositing0.png "Passaggio 0 di composizione Brick-Wall")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

Se si preme il `Button` una volta, `step` si incrementa a 1 e il gestore `PaintSurface` ora Visualizza **SeatedMonkey. jpg**:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        float x = (info.Width - monkeyBitmap.Width) / 2;
        float y = info.Height - monkeyBitmap.Height;

        // Draw monkey bitmap
        if (step >= 1)
        {
            canvas.DrawBitmap(monkeyBitmap, x, y);
        }
        ···
    }
}
```

Non esiste alcun oggetto `SKPaint` e pertanto nessuna modalità di Blend. La bitmap viene visualizzato nella parte inferiore della schermata:

[![Passaggio 1 della composizione Brick-Wall](porter-duff-images/BrickWallCompositing1.png "Passaggio 1 della composizione Brick-Wall")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

Premere di nuovo il `Button` e `step` incrementi a 2. Questo è il passaggio fondamentale per visualizzare il file **SeatedMonkeyMatte. png** :

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw matte to exclude monkey's surroundings
        if (step >= 2)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.BlendMode = SKBlendMode.DstIn;
                canvas.DrawBitmap(matteBitmap, x, y, paint);
            }
        }
        ···
    }
}
```

La modalità Blend è `SKBlendMode.DstIn`, il che significa che la destinazione verrà mantenuta in aree corrispondenti a aree non trasparenti dell'origine. Il resto del rettangolo di destinazione corrispondente nella bitmap originale diventa trasparente:

[![Passaggio 2 della composizione Brick-Wall](porter-duff-images/BrickWallCompositing2.png "Passaggio 2 della composizione Brick-Wall")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

Lo sfondo è stata rimossa. 

Il passaggio successivo è un rettangolo che è simile a un marciapiede che si trova il monkey in. L'aspetto di questo marciapiede si basa su una composizione di due shader: uno shader con colore a tinta unita e uno shader di Perlin noise:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        const float sidewalkHeight = 80;
        SKRect rect = new SKRect(info.Rect.Left, info.Rect.Bottom - sidewalkHeight,
                                 info.Rect.Right, info.Rect.Bottom);

        // Draw gravel sidewalk for monkey to sit on
        if (step >= 3)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateCompose(
                                    SKShader.CreateColor(SKColors.SandyBrown),
                                    SKShader.CreatePerlinNoiseTurbulence(0.1f, 0.3f, 1, 9));

                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(rect, paint);
            }
        }
        ···
    }
}
```

Poiché questo marciapiede deve andare dietro la scimmia, la modalità Blend è `DstOver`. La destinazione viene visualizzata solo se lo sfondo è trasparente:

[![Passaggio 3 della composizione Brick-Wall](porter-duff-images/BrickWallCompositing3.png "Passaggio 3 della composizione Brick-Wall")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

Il passaggio finale consiste nell'aggiungere una parete brick. Il programma usa il riquadro bitmap Brick-Wall disponibile come proprietà statica `BrickWallTile` nella classe `AlgorithmicBrickWallPage`. Una trasformazione traduzione viene aggiunta alla chiamata `SKShader.CreateBitmap` per spostare i riquadri in modo che la riga inferiore sia un riquadro completo:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw bitmap tiled brick wall behind monkey
        if (step >= 4)
        {
            using (SKPaint paint = new SKPaint())
            {
                SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;
                float yAdjust = (info.Height - sidewalkHeight) % bitmap.Height;

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     SKMatrix.MakeTranslation(0, yAdjust));
                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

Per praticità, la chiamata `DrawRect` Visualizza questo shader sull'intera area di disegno, ma la modalità `DstOver` limita l'output alla sola area dell'area di disegno che è ancora trasparente:

[![Passaggio 4 della composizione Brick-Wall](porter-duff-images/BrickWallCompositing4.png "Passaggio 4 della composizione Brick-Wall")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

Ovviamente esistono altri modi per comporre questa scena. È possibile creare backup iniziando in corrispondenza di background e procede in primo piano. Tuttavia, utilizzando le modalità di blend offre maggiore flessibilità. In particolare, l'utilizzo dell'alone consente lo sfondo di una bitmap da escludere dalla scena composta.

Come illustrato nell'articolo relativo al [ritaglio con percorsi e aree](../../curves/clipping.md), la classe `SKCanvas` definisce tre tipi di ritaglio, corrispondenti ai metodi [`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect*), [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath*)e [`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion*) . I metodi di fusione Porter Duff aggiungere un altro tipo di ritaglio, che consente di limitare un'immagine a tutto ciò che è possibile disegnare, incluse le bitmap. Il matte usato nella **composizione Brick-Wall** definisce essenzialmente un'area di ridimensionamento.

## <a name="gradient-transparency-and-transitions"></a>Le transizioni e trasparenza della sfumatura

Gli esempi del Porter-Duff blend modalità illustrata in precedenza in questo articolo sono tutti coinvolti immagini che è costituita da pixel opaco e i pixel trasparenti, ma non parzialmente trasparenti pixel. Le funzioni di blend-modalità vengono definite per tali anche pixel. La tabella seguente è una definizione più formale delle modalità di Blend di Porter-Duff che usa la notazione trovata nel [**riferimento SkBlendMode**](https://skia.org/user/api/SkBlendMode_Reference)di Skia. Poiché il **riferimento SkBlendMode** è un riferimento Skia, C++ viene utilizzata la sintassi.

Concettualmente, i componenti rossi, verdi, blu e alfa di ogni pixel vengono convertiti dai byte in numeri a virgola mobile compreso nell'intervallo da 0 a 1. Per il canale alfa, 0 è completamente trasparente e 1 è completamente opaco

La notazione nella tabella seguente vengono utilizzati le abbreviazioni seguenti:

- **Da** è il canale alfa di destinazione
- Il **controller** di dominio è il colore RGB di destinazione
- **Sa** è il canale alfa di origine
- **SC** è il colore RGB di origine

I colori RGB sono pre-moltiplicati per il valore alfa. Se, ad esempio, **SC** rappresenta il rosso puro ma **sa** è 0X80, il colore RGB è **(0x80, 0,0)** . Se **sa** è 0, anche tutti i componenti RGB sono pari a zero.

Il risultato viene visualizzato tra parentesi quadre con il canale alfa e il colore RGB separati da una virgola: **[alpha, color]** . Per il colore, il calcolo viene eseguito separatamente per i componenti rossi, verdi e blu:

| Modalità       | Operazione |
| ---------- | --------- |
| `Clear`    | [0, 0]    |
| `Src`      | [Sa, Sc]  |
| `Dst`      | [Applicazione distribuita, controller di dominio]  |
| `SrcOver`  | [Sa + Da· (1-Sa), Sc + Dc· (1 – Sa) | 
| `DstOver`  | [Da + Sa· (Da-1), controller di dominio + Sc· (1 – Da) |
| `SrcIn`    | [Sa· Applicazione distribuita, Sc· Da] |
| `DstIn`    | [Da· SA, Dc· Amministratore di sistema] |
| `SrcOut`   | [Sa· (Da-1), Sc· (1-Da]) |
| `DstOut`   | [Da· (1-Sa), Dc· (1-Sa]) |
| `SrcATop`  | [Applicazione distribuita, Sc· Da + Dc· (1-Sa]) |
| `DstATop`  | [Sa, Dc· SA + Sc· (1-Da]) |
| `Xor`      | [Sa + Da – 2· SA· Applicazione distribuita, Sc· (Da-1) + Dc· (1-Sa]) |
| `Plus`     | [Sa + Da, Sc + controller di dominio] |
| `Modulate` | [Sa· Applicazione distribuita, Sc· Controller di dominio] | 

Queste operazioni sono più facili da analizzare quando **da** e **sa** sono 0 o 1. Per la modalità `SrcOver` predefinita, ad esempio, se **sa** è 0, **SC** è anche 0 e il risultato è **[da, DC]** , l'alfa di destinazione e il colore. Se **sa** è 1, il risultato è **[SA, SC]** , l'Alpha di origine e il colore o **[1, SC]** .

Le modalità `Plus` e `Modulate` sono leggermente diverse dalle altre in quanto i nuovi colori possono risultare dalla combinazione dell'origine e della destinazione. È possibile interpretare la modalità `Plus` con i componenti di byte o i componenti a virgola mobile. Nella pagina della **griglia Porter-Duff** mostrata in precedenza, il colore di destinazione è **(0xc0, 0x80, 0x00)** e il colore di origine è **(0x00, 0x80, 0xC0)** . Viene aggiunto ogni coppia di componenti, ma la somma viene impostato a 0xFF. Il risultato è il colore **(0xc0, 0xFF, 0xC0)** . Che corrisponde al colore visualizzato nel punto di intersezione.

Per la modalità di `Modulate`, i valori RGB devono essere convertiti in un valore a virgola mobile. Il colore di destinazione è **(0,75, 0,5, 0)** e l'origine è **(0, 0,5, 0,75)** . Ognuno dei componenti RGB viene moltiplicato insieme e il risultato è **(0, 0,25, 0)** . Questo è il colore visualizzato nell'intersezione nella pagina della **griglia di Porter-Duff** per questa modalità.

La pagina di **trasparenza di Porter-Duff** consente di esaminare il modo in cui le modalità di Blend di Porter-Duff operano su oggetti grafici parzialmente trasparenti. Il file XAML include un `Picker` con le modalità Porter-Duff:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PorterDuffTransparencyPage"
             Title="Porter-Duff Transparency">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Clear" />
                    <x:Static Member="skia:SKBlendMode.Src" />
                    <x:Static Member="skia:SKBlendMode.Dst" />
                    <x:Static Member="skia:SKBlendMode.SrcOver" />
                    <x:Static Member="skia:SKBlendMode.DstOver" />
                    <x:Static Member="skia:SKBlendMode.SrcIn" />
                    <x:Static Member="skia:SKBlendMode.DstIn" />
                    <x:Static Member="skia:SKBlendMode.SrcOut" />
                    <x:Static Member="skia:SKBlendMode.DstOut" />
                    <x:Static Member="skia:SKBlendMode.SrcATop" />
                    <x:Static Member="skia:SKBlendMode.DstATop" />
                    <x:Static Member="skia:SKBlendMode.Xor" />
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                3
            </Picker.SelectedIndex>
        </Picker>
    </StackLayout>
</ContentPage>
```

Il file code-behind riempie i due rettangoli della stessa dimensione utilizzando una sfumatura lineare. La sfumatura di destinazione è in alto a destra in basso a sinistra. È brunastro nell'angolo superiore destro, ma quindi verso il centro inizia la dissolvenza su trasparente e trasparente nell'angolo inferiore sinistro. 

Il rettangolo di origine dispone di una sfumatura dall'alto a sinistra in basso a destra. Nell'angolo superiore sinistro è blu, ma anche in questo caso si dissolve verso trasparente e trasparente nell'angolo in basso a destra. 

```csharp
public partial class PorterDuffTransparencyPage : ContentPage
{
    public PorterDuffTransparencyPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Make square display rectangle smaller than canvas
        float size = 0.9f * Math.Min(info.Width, info.Height);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        SKRect rect = new SKRect(x, y, x + size, y + size);

        using (SKPaint paint = new SKPaint())
        {
            // Draw destination
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Right, rect.Top),
                                new SKPoint(rect.Left, rect.Bottom),
                                new SKColor[] { new SKColor(0xC0, 0x80, 0x00),
                                                new SKColor(0xC0, 0x80, 0x00, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            canvas.DrawRect(rect, paint);

            // Draw source
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { new SKColor(0x00, 0x80, 0xC0), 
                                                new SKColor(0x00, 0x80, 0xC0, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            // Get the blend mode from the picker
            paint.BlendMode = blendModePicker.SelectedIndex == -1 ? 0 :
                                    (SKBlendMode)blendModePicker.SelectedItem;

            canvas.DrawRect(rect, paint);

            // Stroke surrounding rectangle
            paint.Shader = null;
            paint.BlendMode = SKBlendMode.SrcOver;
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Questo programma illustra le modalità di blend Porter Duff possono essere utilizzate con oggetti grafici diversi da bitmap. Tuttavia, l'origine deve includere un'area trasparente. Si tratta in questo caso perché sfumatura riempie il rettangolo, ma parte della sfumatura è trasparente.

Ecco tre esempi:

[![Trasparenza di Porter-Duff](porter-duff-images/PorterDuffTransparency.png "Trasparenza di Porter-Duff")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

La configurazione della destinazione e l'origine sono molto simili a quelle illustrate nella pagina 255 della carta originale delle [_Immagini digitali_](https://graphics.pixar.com/library/Compositing/paper.pdf) per la composizione di Porter-Duff, ma in questa pagina viene illustrato che le modalità di Blend sono buone per le aree di trasparenza parziale.

È possibile utilizzare sfumature trasparente per alcuni effetti diversi. Una possibilità è la maschera, che è simile alla tecnica illustrata nella sezione [**gradienti radiali per la maschera**](../shaders/circular-gradients.md#radial-gradients-for-masking) della **pagina delle sfumature circolari SkiaSharp**. Gran parte della pagina **maschera di composizione** è simile al programma precedente. Carica una risorsa della bitmap e determina un rettangolo in cui si desidera visualizzarlo. Una sfumatura radiale viene creata in base un centro predeterminato e radius:

```csharp
public class CompositingMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(CompositingMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public CompositingMaskPage ()
    {
        Title = "Compositing Mask";

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

        // Find rectangle to display bitmap
        float scale = Math.Min((float)info.Width / bitmap.Width,
                               (float)info.Height / bitmap.Height);

        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);

        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap in rectangle
        canvas.DrawBitmap(bitmap, rect);

        // Adjust center and radius for scaled and offset bitmap
        SKPoint center = new SKPoint(scale * CENTER.X + x,
                                        scale * CENTER.Y + y);
        float radius = scale * RADIUS;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                center,
                                radius,
                                new SKColor[] { SKColors.Black,
                                                SKColors.Transparent },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            paint.BlendMode = SKBlendMode.DstIn;

            // Display rectangle using that gradient and blend mode
            canvas.DrawRect(rect, paint);
        }

        canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
    }
}
```

La differenza con questo programma è che la sfumatura inizia con nero al centro e termina con la trasparenza. Viene visualizzato sulla bitmap con una modalità di Blend di `DstIn`, che mostra la destinazione solo nelle aree dell'origine che non sono trasparenti.

Dopo la chiamata di `DrawRect`, l'intera superficie dell'area di disegno è trasparente, ad eccezione del cerchio definito dalla sfumatura radiale. Viene eseguita una chiamata finale:

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

Tutte le aree trasparenti dell'area di disegno sono di colore rosa:

[![Maschera di composizione](porter-duff-images/CompositingMask.png "Maschera di composizione")](porter-duff-images/CompositingMask-Large.png#lightbox)

È anche possibile usare Porter Duff modalità e le sfumature parzialmente trasparenti per le transizioni da un'immagine a un altro. La pagina **transizioni di sfumature** include un `Slider` per indicare un livello di avanzamento nella transizione da 0 a 1 e un `Picker` per scegliere il tipo di transizione desiderato:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.GradientTransitionsPage"
             Title="Gradient Transitions">
    
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference progressSlider},
                              Path=Value,
                              StringFormat='Progress = {0:F2}'}"
               HorizontalTextAlignment="Center" />

        <Picker x:Name="transitionPicker" 
                Title="Transition" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />
        
    </StackLayout>
</ContentPage>
```

Il file code-behind carica due risorse di bitmap per illustrare la transizione. Queste sono le stesse due immagini usate nella pagina **dissolvenza bitmap** riportata in precedenza in questo articolo. Il codice definisce anche un'enumerazione con tre membri corrispondenti a tre tipi di sfumature &mdash; lineari, radiali e sweep. Questi valori vengono caricati nel `Picker`:

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    SKBitmap bitmap1 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap bitmap2 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.FacePalm.jpg");

    enum TransitionMode
    {
        Linear,
        Radial,
        Sweep
    };

    public GradientTransitionsPage ()
    {
        InitializeComponent ();

        foreach (TransitionMode mode in Enum.GetValues(typeof(TransitionMode)))
        {
            transitionPicker.Items.Add(mode.ToString());
        }

        transitionPicker.SelectedIndex = 0;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }
    ···
}
```

Il file code-behind crea tre oggetti `SKPaint`. L'oggetto `paint0` non utilizza una modalità Blend. Questo oggetto Paint viene utilizzato per disegnare un rettangolo con una sfumatura che passa da nero a trasparente come indicato nella matrice `colors`. L'`positions` matrice è basata sulla posizione del `Slider`, ma è stata modificata in qualche modo. Se il `Slider` è minimo o massimo, i valori di `progress` sono 0 o 1 e una delle due bitmap deve essere completamente visibile. La matrice di `positions` deve essere impostata di conseguenza per tali valori.

Se il valore `progress` è 0, la matrice di `positions` contiene i valori-0,1 e 0. SkiaSharp verranno regolati in caso contrario, il primo valore per essere uguale a 0, a indicare che la sfumatura è nera solo a 0 e trasparente. Quando `progress` è 0,5, la matrice contiene i valori 0,45 e 0,55. La sfumatura è nera da 0 a 0,45, quindi esegue la transizione a trasparente ed è completamente trasparente dal 0,55 su 1. Quando `progress` è 1, la matrice di `positions` è 1 e 1,1, il che significa che la sfumatura è nera da 0 a 1.

Le matrici `colors` e `position` sono entrambe usate nei tre metodi di `SKShader` che creano una sfumatura. Solo uno di questi shader viene creato in base alla selezione `Picker`: 

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Assume both bitmaps are square for display rectangle
        float size = Math.Min(info.Width, info.Height);
        SKRect rect = SKRect.Create(size, size);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        rect.Offset(x, y);

        using (SKPaint paint0 = new SKPaint())
        using (SKPaint paint1 = new SKPaint())
        using (SKPaint paint2 = new SKPaint())
        {
            SKColor[] colors = new SKColor[] { SKColors.Black,
                                               SKColors.Transparent };

            float progress = (float)progressSlider.Value;

            float[] positions = new float[]{ 1.1f * progress - 0.1f,
                                             1.1f * progress };

            switch ((TransitionMode)transitionPicker.SelectedIndex)
            {
                case TransitionMode.Linear:
                    paint0.Shader = SKShader.CreateLinearGradient(
                                        new SKPoint(rect.Left, 0),
                                        new SKPoint(rect.Right, 0),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Radial:
                    paint0.Shader = SKShader.CreateRadialGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        (float)Math.Sqrt(Math.Pow(rect.Width / 2, 2) +
                                                         Math.Pow(rect.Height / 2, 2)),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Sweep:
                    paint0.Shader = SKShader.CreateSweepGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        colors,
                                        positions);
                    break;
            }

            canvas.DrawRect(rect, paint0);

            paint1.BlendMode = SKBlendMode.SrcOut;
            canvas.DrawBitmap(bitmap1, rect, paint1);

            paint2.BlendMode = SKBlendMode.DstOver;
            canvas.DrawBitmap(bitmap2, rect, paint2);
        }
    }
}
```

Tale gradiente viene visualizzato nel rettangolo senza una modalità blend. Al termine di questa `DrawRect` chiamata, l'area di disegno contiene semplicemente una sfumatura da nero a trasparente. La quantità di nero aumenta con valori di `Slider` superiore.

Nelle quattro istruzioni finali del gestore `PaintSurface` vengono visualizzate le due bitmap. La modalità `SrcOut` Blend indica che la prima bitmap viene visualizzata solo nelle aree trasparenti dello sfondo. La modalità `DstOver` per la seconda bitmap significa che la seconda bitmap viene visualizzata solo nelle aree in cui la prima bitmap non viene visualizzata.

Le schermate seguenti illustrano i tre tipi di diverse transizioni, ognuna in corrispondenza del contrassegno del 50%:

[![Transizioni di sfumature](porter-duff-images/GradientTransitions.png "Transizioni di sfumature")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
