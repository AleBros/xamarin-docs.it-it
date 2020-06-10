---
title: "polilinee e equazioni parametriche" Descrizione: "questo articolo spiega come usare SkiaSharp per eseguire il rendering di qualsiasi riga che è possibile definire con equazioni parametriche e illustra questa operazione con il codice di esempio".
ms. prod: Novell MS. AssetID: 85AEBB33-E954-4364-A6E1-808FAB197BEE ms. Technology: Novell-skiasharp autore: davidbritch ms. Author: dabritch ms. Date: 03/10/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="polylines-and-parametric-equations"></a>Polilinee ed equazioni parametriche

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Usare SkiaSharp per eseguire il rendering di qualsiasi riga che è possibile definire con equazioni parametriche_

Nella sezione [**curve e percorsi di SkiaSharp**](../curves/index.md) di questa guida, verranno visualizzati i vari metodi che [`SKPath`](xref:SkiaSharp.SKPath) definiscono per eseguire il rendering di determinati tipi di curve. Tuttavia, a volte è necessario creare un tipo di curva non supportata direttamente da `SKPath` . In tal caso, è possibile utilizzare una polilinea (una raccolta di linee connesse) per creare qualsiasi curva che è possibile definire in modo matematico. Se le righe sono sufficientemente piccole e numerose, il risultato sarà simile a una curva. Questa spirale è effettivamente di 3.600 righe:

![](polylines-images/spiralexample.png "A spiral")

In genere è preferibile definire una curva in termini di una coppia di equazioni parametriche. Si tratta di equazioni per le coordinate X e Y che dipendono da una terza variabile, talvolta chiamata `t` per il tempo. Ad esempio, le equazioni parametriche seguenti definiscono un cerchio con un raggio di 1 centrato al punto (0,0) per *t* compreso tra 0 e 1:

`x = cos(2πt)`

`y = sin(2πt)`

 Se si vuole un raggio maggiore di 1, è sufficiente moltiplicare i valori del seno e del coseno per quel raggio e, se è necessario spostare il centro in un'altra posizione, aggiungere i valori seguenti:

`x = xCenter + radius·cos(2πt)`

`y = yCenter + radius·sin(2πt)`

Per un'ellisse con gli assi paralleli a orizzontale e verticale, sono interessati due raggi:

`x = xCenter + xRadius·cos(2πt)`

`y = yCenter + yRadius·sin(2πt)`

È quindi possibile inserire il codice SkiaSharp equivalente in un ciclo che calcola i vari punti e li aggiunge a un percorso. Il codice SkiaSharp seguente crea un `SKPath` oggetto per un'ellisse che riempie la superficie di visualizzazione. Il ciclo scorre direttamente i 360 gradi. Il centro è metà della larghezza e dell'altezza della superficie di visualizzazione, quindi sono i due raggi:

```csharp
SKPath path = new SKPath();

for (float angle = 0; angle < 360; angle += 1)
{
    double radians = Math.PI * angle / 180;
    float x = info.Width / 2 + (info.Width / 2) * (float)Math.Cos(radians);
    float y = info.Height / 2 + (info.Height / 2) * (float)Math.Sin(radians);

    if (angle == 0)
    {
        path.MoveTo(x, y);
    }
    else
    {
        path.LineTo(x, y);
    }
}
path.Close();
```

In questo modo si ottiene un'ellisse definita da un minimo di 360 righe. Quando viene sottoposto a rendering, appare smussato.

Naturalmente, non è necessario creare un'ellisse usando una polilinea perché `SKPath` include un `AddOval` metodo che esegue questa operazione. Tuttavia, potrebbe essere necessario creare un oggetto visivo non fornito da `SKPath` .

Il codice della pagina **spirale Archimede** è simile al codice dell'ellisse, ma con una differenza fondamentale. Esegue il loop intorno ai 360 gradi del cerchio 10 volte, regolando continuamente il raggio:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(center.X, center.Y);

    using (SKPath path = new SKPath())
    {
        for (float angle = 0; angle < 3600; angle += 1)
        {
            float scaledRadius = radius * angle / 3600;
            double radians = Math.PI * angle / 180;
            float x = center.X + scaledRadius * (float)Math.Cos(radians);
            float y = center.Y + scaledRadius * (float)Math.Sin(radians);
            SKPoint point = new SKPoint(x, y);

            if (angle == 0)
            {
                path.MoveTo(point);
            }
            else
            {
                path.LineTo(point);
            }
        }

        SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 5
        };

        canvas.DrawPath(path, paint);
    }
}
```

Il risultato viene anche definito *spirale aritmetica* perché l'offset tra ogni ciclo è costante:

[![](polylines-images/archimedeanspiral-small.png "Triple screenshot of the Archimedean Spiral page")](polylines-images/archimedeanspiral-large.png#lightbox "Triple screenshot of the Archimedean Spiral page")

Si noti che `SKPath` viene creato in un `using` blocco. In questo modo `SKPath` viene utilizzata una quantità maggiore di memoria rispetto agli `SKPath` oggetti dei programmi precedenti, il che suggerisce che un `using` blocco è più appropriato per eliminare le risorse non gestite.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
