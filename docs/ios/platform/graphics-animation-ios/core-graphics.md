---
title: Grafica principale in Novell. iOS
description: Questo articolo illustra i Framework iOS di base della grafica. Viene illustrato come utilizzare la grafica di base per creare geometria, immagini e PDF.
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: dac81a40983ea8414ec730f10c4c1f17e4d9915c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653803"
---
# <a name="core-graphics-in-xamarinios"></a>Grafica principale in Novell. iOS

_Questo articolo illustra i Framework iOS di base della grafica. Viene illustrato come utilizzare la grafica di base per creare geometria, immagini e PDF._

iOS include il Framework di [*grafica principale*](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html) per fornire supporto per il disegno di basso livello. Questi Framework consentono di abilitare le funzionalità grafiche avanzate all'interno di UIKit. 

La grafica principale è un Framework di grafica 2D di basso livello che consente di disegnare grafica indipendente dal dispositivo. Tutto il disegno 2D in UIKit usa la grafica di base internamente.

La grafica principale supporta il disegno in diversi scenari, tra cui:

-  [Disegno sullo schermo tramite un oggetto `UIView` ](#Drawing_in_a_UIView_Subclass) .
-  [Disegno di immagini in memoria o sullo schermo](#Drawing_Images_and_Text).
-  Creazione e disegno in un file PDF.
-  Lettura e creazione di un file PDF esistente.


## <a name="geometric-space"></a>Spazio geometrico

Indipendentemente dallo scenario, tutto il disegno con la grafica principale viene eseguito nello spazio geometrico, ovvero funziona in punti astratti anziché in pixel. Si descrive cosa si vuole disegnare in termini di geometria e di disegno, ad esempio colori, stili di linea e così via, e la grafica di base gestisce la conversione di tutto in pixel. Tale stato viene aggiunto a un contesto grafico, che può essere considerato come un'area di disegno del pittore.

Questo approccio presenta alcuni vantaggi:

-  Il disegno del codice diventa dinamico e successivamente può modificare la grafica in fase di esecuzione.
-  Ridurre la necessità di immagini statiche nel bundle dell'applicazione può ridurre le dimensioni dell'applicazione.
-  I grafici diventano più resilienti alle modifiche di risoluzione nei dispositivi.

<a name="Drawing_in_a_UIView_Subclass"/>

## <a name="drawing-in-a-uiview-subclass"></a>Disegno in una sottoclasse UIView

Ogni `UIView` ha un `Draw` metodo chiamato dal sistema quando deve essere disegnato. Per aggiungere il codice di disegno a una vista `UIView` , una `Draw`sottoclasse ed eseguire l'override:

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

Il metodo di richiamo non deve mai essere chiamato direttamente. Bensì dal sistema durante l'elaborazione di cicli di esecuzione. Alla prima iterazione del ciclo di esecuzione dopo aver aggiunto alla gerarchia di visualizzazione, una vista relativa `Draw` viene chiamato il metodo. Le chiamate successive a `Draw` si verificano quando la visualizzazione è contrassegnata come debba essere disegnato chiamando `SetNeedsDisplay` o `SetNeedsDisplayInRect` nella vista.

### <a name="pattern-for-graphics-code"></a>Modello per il codice grafico

Il codice nell' `Draw` implementazione dovrebbe descrivere il tipo di disegno. Il codice di disegno segue un modello in cui imposta uno stato di disegno e chiama un metodo per richiedere che venga disegnato. Questo modello può essere generalizzato come indicato di seguito:

1. Ottenere un contesto grafico.

2. Configurare gli attributi di disegno.

3. Creare una geometria dalle primitive di disegno.

4. Chiamare un metodo di disegnare o Stroke.

### <a name="basic-drawing-example"></a>Esempio di disegno di base

Si consideri, ad esempio, il frammento di codice seguente:

```csharp
//get graphics context
using (CGContext g = UIGraphics.GetCurrentContext ()) {
            
    //set up drawing attributes
    g.SetLineWidth (10);
    UIColor.Blue.SetFill ();
    UIColor.Red.SetStroke ();

    //create geometry
    var path = new CGPath ();

    path.AddLines (new CGPoint[]{
    new CGPoint (100, 200),
    new CGPoint (160, 100), 
    new CGPoint (220, 200)});

    path.CloseSubpath ();

    //add geometry to graphics context and draw it
    g.AddPath (path);       
    g.DrawPath (CGPathDrawingMode.FillStroke);
}
```

Interrompiamo questo codice:

```csharp
using (CGContext g = UIGraphics.GetCurrentContext ()) {
...
}
```
Con questa riga, ottiene prima di tutto il contesto grafico corrente da usare per il disegno. È possibile considerare un contesto grafico come l'area di disegno in cui si trova il disegno, che contiene tutti gli stati relativi al disegno, ad esempio i colori di tratto e riempimento, nonché la geometria da disegnare.

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
``` 

Dopo aver ricevuto un contesto grafico, il codice imposta alcuni attributi da usare durante il disegno, illustrato in precedenza. In questo caso vengono impostati i colori di lunghezza riga, tratto e riempimento. Tutti i disegni successivi utilizzeranno quindi questi attributi perché vengono mantenuti nello stato del contesto grafico.

Per creare la geometria, il codice `CGPath`usa un oggetto, che consente di descrivere un percorso grafico da linee e curve. In questo caso, il percorso aggiunge linee che connettono una matrice di punti per creare un triangolo. Come visualizzato sotto la grafica principale, viene usato un sistema di coordinate per il disegno della vista, dove l'origine si trova in alto a sinistra, con un segno x positivo a destra e la direzione positiva-y verso il basso:

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100), 
new CGPoint (220, 200)});

path.CloseSubpath ();
``` 

Una volta creato il percorso, questo viene aggiunto al contesto grafico, in modo che `AddPath` la `DrawPath` chiamata a e rispettivamente possa essere tracciata da.

La visualizzazione risultante è illustrata di seguito:

 ![](core-graphics-images/00-bluetriangle.png "Triangolo di output di esempio")

## <a name="creating-gradient-fills"></a>Creazione di riempimenti sfumatura

Sono disponibili anche le forme di disegno più avanzate. Ad esempio, la grafica principale consente la creazione di riempimenti sfumati e l'applicazione dei percorsi di ritaglio. Per tracciare un riempimento sfumato all'interno del percorso dell'esempio precedente, è necessario impostare prima il percorso come tracciato di ritaglio:

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

L'impostazione del percorso corrente come tracciato di ritaglio vincola tutto il disegno successivo all'interno della geometria del percorso, ad esempio il codice seguente, che disegna una sfumatura lineare:

```csharp
// the color space determines how Core Graphics interprets color information
    using (CGColorSpace rgb = CGColorSpace.CreateDeviceRGB()) {
        CGGradient gradient = new CGGradient (rgb, new CGColor[] {
        UIColor.Blue.CGColor,
        UIColor.Yellow.CGColor
    });

// draw a linear gradient
    g.DrawLinearGradient (
        gradient, 
        new CGPoint (path.BoundingBox.Left, path.BoundingBox.Top), 
        new CGPoint (path.BoundingBox.Right, path.BoundingBox.Bottom), 
        CGGradientDrawingOptions.DrawsBeforeStartLocation);
    }
```

Queste modifiche producono un riempimento sfumato come illustrato di seguito:

 ![](core-graphics-images/01-gradient-fill.png "Esempio con riempimento sfumato")

## <a name="modifying-line-patterns"></a>Modifica di modelli di linea

Gli attributi di disegno delle linee possono anche essere modificati con la grafica di base. Ciò include la modifica della lunghezza della riga e del colore del tratto, nonché il modello di linea stesso, come illustrato nel codice seguente:

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

L'aggiunta di questo codice prima di qualsiasi operazione di disegno produce tratti tratteggiati di 10 unità di lunghezza, con 4 unità di spaziatura tra i trattini, come illustrato di seguito:

 ![](core-graphics-images/02-dashed-stroke.png "L'aggiunta di questo codice prima di qualsiasi operazione di disegno comporta tratti tratteggiati")
 
Si noti che quando si usa il API unificata in Novell. iOS, il tipo di matrice deve `nfloat`essere un ed è necessario eseguire il cast esplicito a Math. pi.

<a name="Drawing_Images_and_Text"/>

## <a name="drawing-images-and-text"></a>Disegno di immagini e testo

Oltre a disegnare i percorsi nel contesto grafico di una visualizzazione, i componenti grafici principali supportano anche il disegno di immagini e testo. Per creare un'immagine, è sufficiente creare `CGImage` un oggetto e passarlo `DrawImage` a una chiamata:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

Tuttavia, in questo modo viene creata un'immagine capovolta, come illustrato di seguito:

 ![](core-graphics-images/03-upside-down-monkey.png "Immagine disegnata capovolta")

Il motivo è che l'origine grafica principale per il disegno di immagini è in basso a sinistra, mentre la vista ha l'origine in alto a sinistra. Pertanto, per visualizzare correttamente l'immagine, è necessario modificare l'origine, che può essere eseguita modificando la *matrice di trasformazione corrente* *(CTM)* . Il CTM definisce la posizione dei punti Live, nota anche come *spazio utente*. Invertire il CTM nella direzione y e spostarlo in base all'altezza dei limiti nella direzione y negativa può capovolgere l'immagine.

Il contesto grafico include metodi helper per trasformare il CTM. In questo caso, `ScaleCTM` "capovolge" il disegno e `TranslateCTM` lo sposta in alto a sinistra, come mostrato di seguito:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);
    
    using (CGContext g = UIGraphics.GetCurrentContext ()) {

        // scale and translate the CTM so the image appears upright
        g.ScaleCTM (1, -1);
        g.TranslateCTM (0, -Bounds.Height);
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
}   
```

L'immagine risultante viene quindi visualizzata verticalmente:

 ![](core-graphics-images/04-upright-monkey.png "Immagine di esempio visualizzata verticalmente")

> [!IMPORTANT]
> Le modifiche apportate al contesto grafico si applicano a tutte le operazioni di disegno successive. Pertanto, quando il CTM viene trasformato, avrà effetto su qualsiasi disegno aggiuntivo. Se, ad esempio, il triangolo è stato disegnato dopo la trasformazione CTM, viene visualizzato un capovolto.

### <a name="adding-text-to-the-image"></a>Aggiunta di testo all'immagine

Come per i percorsi e le immagini, il disegno di testo con la grafica principale prevede lo stesso modello di base per l'impostazione di uno stato di grafica e la chiamata di un metodo da disegnare. Nel caso del testo, il metodo per visualizzare il testo è `ShowText`. Quando viene aggiunto all'esempio di disegno dell'immagine, il codice seguente disegna un testo usando la grafica principale:

```csharp
public override void Draw (RectangleF rect)
{
    base.Draw (rect);
    
    // image drawing code omitted for brevity ...

    // translate the CTM by the font size so it displays on screen
    float fontSize = 35f;
    g.TranslateCTM (0, fontSize);

    // set general-purpose graphics state
    g.SetLineWidth (1.0f);
    g.SetStrokeColor (UIColor.Yellow.CGColor);
    g.SetFillColor (UIColor.Red.CGColor);
    g.SetShadow (new CGSize (5, 5), 0, UIColor.Blue.CGColor);

    // set text specific graphics state
    g.SetTextDrawingMode (CGTextDrawingMode.FillStroke);
    g.SelectFont ("Helvetica", fontSize, CGTextEncoding.MacRoman);

    // show the text
    g.ShowText ("Hello Core Graphics");
}
```

Come si può notare, l'impostazione dello stato di grafica per il disegno del testo è simile alla geometria del disegno. Per il disegno del testo, tuttavia, vengono applicate anche la modalità di disegno del testo e il tipo di carattere. In questo caso viene applicata anche un'ombreggiatura, anche se l'applicazione di ombre funziona allo stesso modo per il disegno del percorso.

Il testo risultante viene visualizzato con l'immagine, come illustrato di seguito:

 ![](core-graphics-images/05-text-on-image.png "Il testo risultante viene visualizzato con l'immagine")

## <a name="memory-backed-images"></a>Immagini supportate da memoria

Oltre a creare il contesto grafico di una visualizzazione, la grafica principale supporta la creazione di immagini supportate da memoria, nota anche come disegno fuori schermo. Questa operazione richiede:

-  Creazione di un contesto grafico supportato da una bitmap in memoria
-  Impostazione dello stato di disegno ed esecuzione di comandi di disegno
-  Recupero dell'immagine dal contesto
-  Rimozione del contesto


A differenza del `Draw` metodo, in cui il contesto viene fornito dalla vista, in questo caso il contesto viene creato in uno dei due modi seguenti:

1. Chiamando `UIGraphics.BeginImageContext` (o `BeginImageContextWithOptions`)

2. Creazione di un nuovo`CGBitmapContextInstance`

 `CGBitmapContextInstance`è utile quando si lavora direttamente con i bit dell'immagine, ad esempio nei casi in cui si usa un algoritmo di manipolazione delle immagini personalizzato. In tutti gli altri casi, è necessario `BeginImageContext` utilizzare `BeginImageContextWithOptions`o.

Quando si dispone di un contesto immagine, l'aggiunta del codice di disegno è analoga `UIView` a quella in una sottoclasse. Ad esempio, l'esempio di codice usato in precedenza per creare un triangolo può essere usato per creare un'immagine in memoria anziché in un `UIView`, come illustrato di seguito:

```csharp
UIImage DrawTriangle ()
{
    UIImage triangleImage;

    //push a memory backed bitmap context on the context stack
    UIGraphics.BeginImageContext (new CGSize (200.0f, 200.0f));

    //get graphics context
    using(CGContext g = UIGraphics.GetCurrentContext ()){

        //set up drawing attributes
        g.SetLineWidth(4);
        UIColor.Purple.SetFill ();
        UIColor.Black.SetStroke ();

        //create geometry
        path = new CGPath ();

        path.AddLines(new CGPoint[]{
            new CGPoint(100,200),
            new CGPoint(160,100), 
            new CGPoint(220,200)});

        path.CloseSubpath();

        //add geometry to graphics context and draw it
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);

        //get a UIImage from the context
        triangleImage = UIGraphics.GetImageFromCurrentImageContext ();
    }
    
    return triangleImage;
}
```

Un uso comune del disegno in una bitmap supportata dalla memoria consiste nell'acquisire un'immagine da qualsiasi `UIView`. Il codice seguente, ad esempio, esegue il rendering del livello di una visualizzazione in un contesto bitmap `UIImage` e ne crea una da:

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>Disegno di PDF

Oltre alle immagini, la grafica principale supporta il disegno PDF. Analogamente alle immagini, è possibile eseguire il rendering di un file PDF in memoria e leggere un file `UIView`PDF per il rendering in un.

### <a name="pdf-in-a-uiview"></a>PDF in una UIView

La grafica principale supporta anche la lettura di un file PDF da un file e il relativo rendering `CGPDFDocument` in una visualizzazione usando la classe. La `CGPDFDocument` classe rappresenta un file PDF nel codice e può essere utilizzata per leggere e creare pagine.

Ad esempio, il codice seguente in una `UIView` sottoclasse legge un file PDF da un file `CGPDFDocument`in un:

```csharp
public class PDFView : UIView
{
    CGPDFDocument pdfDoc;

    public PDFView ()
    {
        //create a CGPDFDocument from file.pdf included in the main bundle
        pdfDoc = CGPDFDocument.FromFile ("file.pdf");
    }
  
     public override void Draw (Rectangle rect)
    {
        ...
    }
}
```

Il `Draw` metodo può quindi `CGPDFDocument` usare per leggere una pagina in `CGPDFPage` ed eseguirne il rendering chiamando `DrawPDFPage`, come illustrato di seguito:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);
        
    //flip the CTM so the PDF will be drawn upright
    using (CGContext g = UIGraphics.GetCurrentContext ()) {
        g.TranslateCTM (0, Bounds.Height);
        g.ScaleCTM (1, -1);
        
        // render the first page of the PDF
        using (CGPDFPage pdfPage = pdfDoc.GetPage (1)) {
            
        //get the affine transform that defines where the PDF is drawn
        CGAffineTransform t = pdfPage.GetDrawingTransform (CGPDFBox.Crop, rect, 0, true);
        
        //concatenate the pdf transform with the CTM for display in the view
        g.ConcatCTM (t);
        
        //draw the pdf page
        g.DrawPDFPage (pdfPage);
        }
    }
}
```

### <a name="memory-backed-pdf"></a>PDF con supporto di memoria

Per un file PDF in memoria, è necessario creare un contesto PDF chiamando `BeginPDFContext`. Il disegno in formato PDF è granulare in pagine. Ogni pagina viene avviata `BeginPDFPage` chiamando e completata `EndPDFContent`chiamando, con il codice grafico compreso tra. Inoltre, come per il disegno di immagini, il disegno PDF supportato dalla memoria usa un'origine in basso a sinistra, che può essere considerato modificando il CTM esattamente come con le immagini.

Il codice seguente illustra come creare un testo in un file PDF:

```csharp
//data buffer to hold the PDF
NSMutableData data = new NSMutableData ();

//create a PDF with empty rectangle, which will configure it for 8.5x11 inches
UIGraphics.BeginPDFContext (data, CGRect.Empty, null);

//start a PDF page
UIGraphics.BeginPDFPage ();
       
using (CGContext g = UIGraphics.GetCurrentContext ()) {
    g.ScaleCTM (1, -1);
    g.TranslateCTM (0, -25);      
    g.SelectFont ("Helvetica", 25, CGTextEncoding.MacRoman);
    g.ShowText ("Hello Core Graphics");
    }
    
//complete a PDF page
UIGraphics.EndPDFContent ();
```

Il testo risultante viene disegnato in formato PDF, che è quindi contenuto in un `NSData` oggetto che può essere salvato, caricato, inviato tramite posta elettronica e così via.


## <a name="summary"></a>Riepilogo

In questo articolo sono state esaminate le funzionalità grafiche fornite tramite il Framework di *grafica principale* . È stato illustrato come usare la grafica di base per creare la geometria, le immagini e i file `UIView,` PDF all'interno del contesto di un e di contesti grafici con supporto di memoria.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Core Graphics](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation)
- [Procedura dettagliata per grafica e animazione](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core Animation](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Ricette di animazione Core](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
