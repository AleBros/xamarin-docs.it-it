---
title: Metodi di fusione porter Duff
description: Utilizzare i metodi di fusione Porter Duff per comporre le scene basate su immagini di origine e destinazione.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 57F172F8-BA03-43EC-A215-ED6B78696BB5
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: ebd4db28b2c20bd2b9e1d93e03dd101ebc5da663
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131681"
---
# <a name="porter-duff-blend-modes"></a>Metodi di fusione porter Duff

I metodi di fusione Porter Duff sono nominati a Thomas Porter e Tom Duff, che ha sviluppato un algebra di composizione durante l'utilizzo per Lucasfilm. La carta [ _la composizione di immagini digitali_ ](https://graphics.pixar.com/library/Compositing/paper.pdf) è stato pubblicato nel numero di luglio 1984 di _infografica_, pagine da 253 a 259. Queste modalità di blend sono essenziali per la composizione di cui è assemblando diverse immagini in una scena composita:

![Esempio porter Duff](porter-duff-images/PorterDuffSample.png "Porter Duff esempio")

## <a name="porter-duff-concepts"></a>Concetti di porter Duff

Si supponga che un rettangolo brunastro occupa la superiore e sinistro due-terzi della superficie di visualizzazione:

![Destinazione porter Duff](porter-duff-images/PorterDuffDst.png "Porter Duff destinazione")

Quest'area viene chiamata il _destinazione_ o in alcuni casi il _sfondo_ o _backdrop_.

Si vuole disegnare il rettangolo seguente, che è la stessa dimensione della matrice di destinazione. Il rettangolo è trasparente, ad eccezione di un'area blu che occupa destra e sotto due terzi:

![Origine porter Duff](porter-duff-images/PorterDuffSrc.png "Porter Duff origine")

Questa operazione viene definita la _origine_ o in alcuni casi il _in primo piano_.

Quando si visualizza l'origine nella destinazione, in questo caso è quello previsto:

![Over origine porter Duff](porter-duff-images/PorterDuffSrcOver.png "Porter Duff Over origine")

I pixel trasparenti dell'origine consentono il colore di sfondo, mentre i pixel origine blu nascondano lo sfondo. In questo caso normale e viene definita in SkiaSharp come `SKBlendMode.SrcOver`. Che indica l'impostazione predefinita il `BlendMode` proprietà quando un `SKPaint` oggetto viene innanzitutto creata un'istanza.

Tuttavia, è possibile specificare una modalità blend diversi per un effetto di diversi. Se si specifica `SKBlendMode.DstOver`, quindi nell'area di intersezione tra l'origine e destinazione, viene visualizzata nella destinazione anziché l'origine:

![Destinazione porter Duff tramite](porter-duff-images/PorterDuffDstOver.png "destinazione Porter Duff su")

Il `SKBlendMode.DstIn` modalità blend consente di visualizzare solo l'area in cui l'origine e destinazione intersect utilizzando il colore di destinazione:

![Destinazione porter Duff](porter-duff-images/PorterDuffDstIn.png "destinazione Porter Duff In")

La modalità di sfumatura di `SKBlendMode.Xor` (OR esclusivo) fa in modo che non venga visualizzato in due aree si sovrappongono:

![Esclusivo porter Duff oppure](porter-duff-images/PorterDuffXor.png "Porter Duff esclusivo o")

L'origine e destinazione di rettangoli colorati in modo efficace dividono la superficie di visualizzazione in quattro aree univoche che possono essere colorate in vari modi corrispondenti alla presenza dei rettangoli di origine e destinazione:

![Porter Duff](porter-duff-images/PorterDuff.png "Porter Duff")

I rettangoli in alto a destra e in basso a sinistra sono sempre vuoti perché la destinazione e origine sono trasparenti in tali aree. Il colore di destinazione occupa l'area angolo superiore sinistro, in modo che l'area può essere impostata con il colore di destinazione o non ereditarli affatto. Analogamente, il colore di origine occupa l'area in basso a destra, in modo che può essere di colore con il colore di origine o non ereditarli affatto. L'intersezione tra la destinazione e di origine nella parte centrale può essere impostato con il colore di destinazione, il colore di origine, o niente affatto.

Il numero totale di combinazioni è 2 (per l'angolo superiore sinistro) volte 2 (angolo inferiore destro) volte 3 (per area) o 12. Queste sono le modalità di composizione Porter Duff base 12.

Verso la fine del _immagini digitali composizione_ (pagina 256) Porter e Duff aggiungere una modalità 13 denominata _plus_ (corrispondente di SkiaSharp `SKBlendMode.Plus` membro e il W3C _più chiaro_  modalità (non ovvero deve essere confusa con la W3C _schiarire_ modalità.) Ciò `Plus` modalità aggiunge i colori di origine e destinazione, un processo che verrà illustrato tra breve in modo più dettagliato.

Skia aggiunge una modalità 14 denominata `Modulate` che è molto simile a `Plus` ad eccezione del fatto che vengono moltiplicati i colori di origine e destinazione. Ma può essere trattato come una modalità di blend Porter Duff aggiuntiva.

Di seguito sono le modalità Porter Duff 14 come definito in SkiaSharp. La tabella mostra come essi colori ognuna delle tre aree non vuota nel diagramma precedente:

| Modalità       | Destinazione | Intersezione | Origine |
| ---------- |:-----------:|:------------:|:------:|
| `Clear`    |             |              |        |
| `Src`      |             | Origine       | x      |
| `Dst`      | x           | Destinazione  |        |
| `SrcOver`  | x           | Origine       | x      |
| `DstOver`  | x           | Destinazione  | x      |
| `SrcIn`    |             | Origine       |        |
| `DstIn`    |             | Destinazione  |        |
| `SrcOut`   |             |              | x      |
| `DstOut`   | X           |              |        |
| `SrcATop`  | x           | Origine       |        |
| `DstATop`  |             | Destinazione  | x      |
| `Xor`      | X           |              | X      |
| `Plus`     | x           | Sum          | x      |
| `Modulate` |             | Prodotto      |        | 

Queste modalità di blend sono simmetrici. L'origine e destinazione possono essere scambiati e tutte le modalità sono ancora disponibili.

La convenzione di denominazione delle modalità segue alcune semplici regole:

- **Src** oppure **Dst** autonomamente significa che solo i pixel di origine o di destinazione siano visibili.
- Il **failover** suffisso indica ciò che è visibile nel punto di intersezione. L'origine o la destinazione viene disegnato "o" l'altra.
- Il **In** suffisso significa che solo l'intersezione è colorata. L'output è limitato al solo la parte dell'origine o di destinazione "in" altro.
- Il **Out** suffisso significa che l'intersezione non viene colorata. L'output è solo la parte dell'origine o destinazione che è "out" del punto di intersezione.
- Il **sopra** suffisso rappresenta l'unione tra **nelle** e **Out**. Include l'area in cui l'origine o la destinazione è "su" di altro.

Si noti la differenza con il `Plus` e `Modulate` modalità. Queste modalità siano eseguendo un altro tipo di calcolo sui pixel origine e destinazione. Vengono descritte in dettaglio tra breve.

Il **Porter Duff griglia** pagina Mostra tutte le modalità di 14 su uno schermo sotto forma di una griglia. Ogni modalità è un'istanza separata di `SKCanvasView`. Per questo motivo, una classe è derivata da `SKCanvasView` denominato `PorterDuffCanvasView`. Il costruttore statico consente di creare due immagini bitmap della stessa dimensione, una con un rettangolo brunastro nella relativa area angolo superiore sinistro e un'altra con un rettangolo blu:

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

Il `OnPaintSurface` sostituzione consente di disegnare bitmap di due. La prima viene disegnata in genere:

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

Il secondo viene disegnato con un' `SKPaint` oggetti in cui il `BlendMode` proprietà è stata impostata per l'argomento del costruttore:

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

Il resto del `OnPaintSurface` override Disegna un rettangolo attorno alla bitmap per indicare le relative dimensioni.

Il `PorterDuffGridPage` classe crea quattordici istanze degli `PorterDurffCanvasView`, uno per ogni membro del `blendModes` matrice. L'ordine dei `SKBlendModes` i membri della matrice è leggermente diverso da quello della tabella per posizionare le modalità simili accanto a altra. Le istanze di 14 `PorterDuffCanvasView` sono organizzati insieme alle etichette in un `Grid`:

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

Ecco il risultato:

[![Griglia porter Duff](porter-duff-images/PorterDuffGrid.png "Porter Duff griglia")](porter-duff-images/PorterDuffGrid-Large.png#lightbox)

È opportuno per indurre manualmente che è fondamentale per il corretto funzionamento delle modalità di blend Porter Duff trasparenza. Il `PorterDuffCanvasView` classe contiene in totale tre chiamate al `Canvas.Clear` (metodo). Tutti gli elementi usare il metodo senza parametri, che imposta tutti i pixel su trasparente:

```csharp
canvas.Clear();
```

Provare a eseguire la modifica di una di queste chiamate in modo che i pixel vengono impostati su nero opaco:

```csharp
canvas.Clear(SKColors.White);
```

In seguito tale modifica, alcune delle modalità di blend sembrerà funzionare, ma altri utenti non funzionerà. Se si imposta lo sfondo della bitmap di origine verso il bianco, il `SrcOver` modalità non funziona perché non vi è alcun trasparente pixel nella bitmap di origine per consentire la destinazione sono visibili attraverso. Se si imposta lo sfondo della bitmap di destinazione o l'area di disegno al bianco, quindi `DstOver` non funziona perché la destinazione non dispone di qualsiasi pixel trasparenti.

Potrebbe esserci una tentazione di sostituire le bitmap nel **griglia Porter Duff** pagina con più semplice `DrawRect` chiamate. Che funzionerà per il rettangolo di destinazione ma non per il rettangolo di origine. Il rettangolo di origine deve includere solo l'area di colore blu. Il rettangolo di origine deve includere un'area trasparente che corrisponde all'area colorata della destinazione. In questi solo a questo punto si mescoleranno lavoro modalità.

## <a name="using-mattes-with-porter-duff"></a>Uso di mascherini con Porter Duff

Il **composizione dei Brick-Wall** pagina mostra un esempio di un'attività di composizione classico: un'immagine deve essere raccolto da più parti, tra cui una bitmap con uno sfondo che deve essere eliminata. Di seguito è riportato il **SeatedMonkey.jpg** bitmap con lo sfondo problematico:

![Seduto Monkey](porter-duff-images/SeatedMonkey.jpg "seduto Monkey")

In preparazione per la composizione di un oggetto corrispondente _mascherino_ è stato creato, ovvero un'altra mappa di bit che è in caso contrario, nero in cui si desidera che l'immagine da visualizzare e trasparente. Questo file è denominato **SeatedMonkeyMatte.png** ed è tra le risorse nel **supporti** cartella il [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) esempio :

![Seduto Monkey mascherino](porter-duff-images/SeatedMonkeyMatte.png "seduto mascherino Monkey")

Si tratta _non_ un alone creato in modo accurato. In modo ottimale, mascherino deve includere un pixel parzialmente trasparente intorno ai bordi dei pixel nero e questo mascherino non esiste.

Il file XAML per il **composizione dei Brick-Wall** crea un'istanza di pagina un' `SKCanvasView` e un `Button` che guida l'utente attraverso il processo di creazione dell'immagine finale:

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

Il file code-behind carica la due bitmap necessarie e gestisce i `Clicked` eventi del `Button`. Per ogni `Button` fare clic su, il `step` campo viene incrementato e un nuovo oggetto `Text` viene impostata per il `Button`. Quando `step` raggiunge 5, viene reimpostato su 0:

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

Quando il programma viene eseguito prima di tutto, non è visibile, ad eccezione di `Button`:

[![Passaggio di composizione dei brick-Wall 0](porter-duff-images/BrickWallCompositing0.png "Brick-Wall composizione passaggio 0")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

Premendo il `Button` provoca una sola volta `step` incremento a 1 e il `PaintSurface` ora Visualizza gestore **SeatedMonkey.jpg**:

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

È presente alcun `SKPaint` oggetto e pertanto alcuna modalità blend. La bitmap viene visualizzato nella parte inferiore della schermata:

[![Composizione dei brick-Wall Passaggio1](porter-duff-images/BrickWallCompositing1.png "Brick-Wall composizione passaggio 1")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

Premere il `Button` nuovamente e `step` incrementi a 2. Questo è il passaggio fondamentale di visualizzare il **SeatedMonkeyMatte.png** file:

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

La modalità di blend `SKBlendMode.DstIn`, il che significa che la destinazione venga preservata nelle aree corrispondenti alle aree non trasparente dell'origine. Il resto del rettangolo di destinazione corrispondente nella bitmap originale diventa trasparente:

[![Brick-Wall composizione passaggio 2](porter-duff-images/BrickWallCompositing2.png "Brick-Wall composizione passaggio 2")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

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

Poiché questo marciapiede deve passare dietro il monkey, la modalità di sfumatura è `DstOver`. La destinazione viene visualizzata solo se lo sfondo è trasparente:

[![Brick-Wall composizione passaggio 3](porter-duff-images/BrickWallCompositing3.png "Brick-Wall composizione passaggio 3")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

Il passaggio finale consiste nell'aggiungere una parete brick. Il programma Usa la tessera mappa di bit brick-wall disponibile come proprietà statica `BrickWallTile` nella `AlgorithmicBrickWallPage` classe. Viene aggiunta una trasformazione di conversione per la `SKShader.CreateBitmap` chiamata per spostare i riquadri in modo che la riga inferiore è un riquadro completo:

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

Per praticità, la `DrawRect` chiamata Visualizza questo shader tramite l'intera area di disegno, ma il `DstOver` modalità limita l'output solo nell'area dell'area di disegno ancora trasparente:

[![Composizione dei brick-Wall Passaggio4](porter-duff-images/BrickWallCompositing4.png "Brick-Wall composizione passaggio 4")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

Ovviamente esistono altri modi per comporre questa scena. È possibile creare backup iniziando in corrispondenza di background e procede in primo piano. Tuttavia, utilizzando le modalità di blend offre maggiore flessibilità. In particolare, l'utilizzo dell'alone consente lo sfondo di una bitmap da escludere dalla scena composta.

Come illustrato nell'articolo [ritaglio con tracciati e aree geografiche](../../curves/clipping.md), il `SKCanvas` classe definisce tre tipi di ritaglio, corrispondente al [ `ClipRect` ](xref:SkiaSharp.SKCanvas.ClipRect*), [ `ClipPath` ](xref:SkiaSharp.SKCanvas.ClipPath*), e [ `ClipRegion` ](xref:SkiaSharp.SKCanvas.ClipRegion*) metodi. I metodi di fusione Porter Duff aggiungere un altro tipo di ritaglio, che consente di limitare un'immagine a tutto ciò che è possibile disegnare, incluse le bitmap. Mascherino usato nel **composizione dei Brick-Wall** essenzialmente definisce un'area di ritaglio.

## <a name="gradient-transparency-and-transitions"></a>Le transizioni e trasparenza della sfumatura

Gli esempi del Porter-Duff blend modalità illustrata in precedenza in questo articolo sono tutti coinvolti immagini che è costituita da pixel opaco e i pixel trasparenti, ma non parzialmente trasparenti pixel. Le funzioni di blend-modalità vengono definite per tali anche pixel. Nella tabella seguente è una definizione più formale delle modalità di blend Porter Duff che usa la notazione trovata nel Skia [ **riferimento SkBlendMode**](https://skia.org/user/api/SkBlendMode_Reference). (Poiché **SkBlendMode riferimento** è un riferimento Skia, viene utilizzata la sintassi di C++.)

Concettualmente, i componenti rossi, verdi, blu e alfa di ogni pixel vengono convertiti dai byte in numeri a virgola mobile compreso nell'intervallo da 0 a 1. Per il canale alfa, 0 è completamente trasparente e 1 è completamente opaco

La notazione nella tabella seguente vengono utilizzati le abbreviazioni seguenti:

- **Da** è il canale alfa di destinazione
- **Controller di dominio** costituisce la destinazione di colore RGB
- **SA** è il canale alfa di origine
- **SC** è l'origine di colore RGB

I colori RGB sono pre-moltiplicati per il valore alfa. Ad esempio, se **Sc** rappresenta il rosso puro ma **Sa** è 0x80, allora è il colore RGB **(0x80, 0, 0)**. Se **Sa** è 0, quindi tutti i componenti RGB inoltre sono pari a zero.

Il risultato viene visualizzato tra parentesi quadre con il canale alfa e il colore RGB separati da virgole: **[colore alfa,]**. Per il colore, il calcolo viene eseguito separatamente per i componenti rossi, verdi e blu:

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

Queste operazioni sono più facili da analizzare quando **Da** e **Sa** sono 0 o 1. Ad esempio, per impostazione predefinita `SrcOver` modalità, se **Sa** è 0, quindi **Sc** viene inoltre 0 e il risultato è **[Da, Cc]**, il valore alfa di destinazione e il colore. Se **Sa** è 1, il risultato è **[Sa, Sc]**, i valori alfa origine e il colore, o **[1, Sc]**.

Il `Plus` e `Modulate` modalità sono un po' diversa dagli altri, in quanto possono causare nuovi colori dalla combinazione di origine e destinazione. Il `Plus` modalità può essere interpretata con componenti di byte o a virgola mobile. Nel **griglia Porter Duff** pagina mostrata in precedenza, il colore di destinazione viene **(0xC0, 0x80, 0x00)** e il colore di origine è **(0x00, 0x80, 0xC0)**. Viene aggiunto ogni coppia di componenti, ma la somma viene impostato a 0xFF. Il risultato è il colore **(0xC0, 0xFF, 0xC0)**. Che corrisponde al colore visualizzato nel punto di intersezione.

Per il `Modulate` modalità, i valori RGB devono essere convertiti a virgola mobile. Il colore di destinazione è **(0.75, 0,5, 0)** e l'origine è **(0, 0.5, 0.75)**. I componenti RGB sono ognuno moltiplicati insieme e il risultato viene **(0, 0.25, 0)**. Che rappresenta il colore indicato nel punto di intersezione nel **Porter Duff griglia** pagina per questa modalità.

Il **Porter Duff trasparenza** pagina consente di esaminare il modo in cui le modalità di blend Porter Duff operano su oggetti grafici che sono parzialmente trasparenti. Il file XAML include un `Picker` con le modalità Porter Duff:

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

[![Trasparenza porter Duff](porter-duff-images/PorterDuffTransparency.png "Porter Duff trasparenza")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

La configurazione di destinazione e di origine è molto simile ai diagrammi illustrati nella pagina 255 dell'originale Porter-Duff [ _la composizione di immagini digitali_ ](https://graphics.pixar.com/library/Compositing/paper.pdf) presente articolo, ma in questa pagina viene dimostrato che la modalità di blend sono ben progettate per le aree di trasparenza parziale.

È possibile utilizzare sfumature trasparente per alcuni effetti diversi. Una possibilità applica la maschera, che è simile alla tecnica illustrata nel [ **sfumature radiali per il mascheramento** ](../shaders/circular-gradients.md#radial-gradients-for-masking) sezione la **pagina sfumature circolare SkiaSharp**. Che misura il **maschera la composizione** pagina è simile al programma precedente. Carica una risorsa della bitmap e determina un rettangolo in cui si desidera visualizzarlo. Una sfumatura radiale viene creata in base un centro predeterminato e radius:

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

La differenza con questo programma è che la sfumatura inizia con nero al centro e termina con la trasparenza. Viene visualizzato su una bitmap con fusione di `DstIn`, che mostra la destinazione solo nelle aree di origine che non sono trasparenti.

Dopo il `DrawRect` chiamata, l'intera superficie dell'area di disegno è trasparente, ad eccezione del cerchio definito da sfumatura radiale. Viene eseguita una chiamata finale:

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

Tutte le aree trasparenti dell'area di disegno sono di colore rosa:

[![Maschera di composizione](porter-duff-images/CompositingMask.png "maschera la composizione")](porter-duff-images/CompositingMask-Large.png#lightbox)

È anche possibile usare Porter Duff modalità e le sfumature parzialmente trasparenti per le transizioni da un'immagine a un altro. Il **transizioni sfumatura** pagina include un `Slider` per indicare un livello di avanzamento nella transizione da 0 a 1 e un `Picker` per scegliere il tipo di transizione desiderato:

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

Il file code-behind carica due risorse di bitmap per illustrare la transizione. Queste sono le stesse due immagini utilizzate nel **Bitmap sciogliere** pagina più indietro in questo articolo. Il codice definisce anche un'enumerazione con tre membri che corrispondono ai tre tipi di sfumature &mdash; lineare, radiale e durante lo sweep. Questi valori vengono caricati i `Picker`:

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

Il file code-behind vengono creati tre `SKPaint` oggetti. Il `paint0` oggetto non usa una modalità blend. Questo oggetto paint viene usato per disegnare un rettangolo con una sfumatura che va da nero a trasparente come indicato nella `colors` matrice. Il `positions` matrice si basa sulla posizione del `Slider`, ma modificati in qualche modo. Se il `Slider` si trova il valore minimo o massimo, il `progress` i valori sono 0 o 1, e un'immagine due bitmap deve essere completamente visibile. Il `positions` matrice deve essere impostata di conseguenza per tali valori.

Se il `progress` valore è 0, il `positions` matrice contiene i valori 0 e -0.1. SkiaSharp verranno regolati in caso contrario, il primo valore per essere uguale a 0, a indicare che la sfumatura è nera solo a 0 e trasparente. Quando si `progress` è pari a 0,5, quindi la matrice contiene i valori 0,45 e 0,55. La sfumatura è nera da 0 a 0,45, quindi esegue la transizione a trasparente ed è completamente trasparente dal 0,55 su 1. Quando `progress` è 1, il `positions` array è 1 e 1.1, che significa che la sfumatura è nera da 0 a 1.

Il `colors` e `position` entrambe le matrici sono utilizzate per i tre metodi di `SKShader` che creano una sfumatura. Solo uno di questi shader creato sulla base di `Picker` selezione: 

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

Tale gradiente viene visualizzato nel rettangolo senza una modalità blend. In seguito `DrawRect` chiamata, l'area di disegno contiene semplicemente una sfumatura dal nero trasparente. Aumenta la quantità di colore nero con versioni successive `Slider` valori.

Nelle quattro istruzioni finale del `PaintSurface` gestore, vengono visualizzate le due immagini bitmap. Il `SrcOut` fusione indica che la bitmap primo viene visualizzata solo nelle aree dello sfondo trasparente. Il `DstOver` modalità per la seconda bitmap indica che la seconda bitmap viene visualizzata solo in quelle aree in cui la bitmap primo non viene visualizzata.

Le schermate seguenti illustrano i tre tipi di diverse transizioni, ognuna in corrispondenza del contrassegno del 50%:

[![Transizioni di sfumatura](porter-duff-images/GradientTransitions.png "transizioni sfumatura")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)