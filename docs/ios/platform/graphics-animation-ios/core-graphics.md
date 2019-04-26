---
title: Core Graphics in xamarin. IOS
description: Questo articolo illustra i framework iOS Core Graphics. Viene illustrato come utilizzare Core Graphics per disegnare la geometria, immagini e PDF.
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: f3fe22e56a2c45524923a316ef28e54e5a3cc3f8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61372415"
---
# <a name="core-graphics-in-xamarinios"></a>Core Graphics in xamarin. IOS

_Questo articolo illustra i framework iOS Core Graphics. Viene illustrato come utilizzare Core Graphics per disegnare la geometria, immagini e PDF._

iOS include la [ *Core Graphics* ](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html) framework per il supporto di disegno a basso livello. Questi Framework sono cosa abilitare la funzionalità grafiche avanzate in UIKit. 

Grafica di base è un framework di grafica 2D di basso livello che consente di disegno grafica indipendente dal dispositivo. Disegno in UIKit 2D tutti i Core Graphics viene utilizzata internamente.

Core della grafica supporta il disegno in diversi scenari, tra cui:

-  [Disegna sullo schermo tramite un `UIView` ](#Drawing_in_a_UIView_Subclass) .
-  [Disegno di immagini in memoria o su schermo](#Drawing_Images_and_Text).
-  La creazione e disegno in formato PDF.
-  La lettura e creazione di un file PDF esistente.


## <a name="geometric-space"></a>Spazio geometrico

Indipendentemente dallo scenario, tutti i disegni effettuati con elementi grafici Core viene eseguito nello spazio geometrico, ovvero che può essere utilizzata in punti abstract anziché pixel. Si descrivono ciò che si desidera disegnata in termini di geometria e stato, ad esempio i colori, stili di riga e così via di disegno e grafica Core gestisce tutti gli elementi della conversione in pixel. Stato di questo tipo viene aggiunto a un contesto di grafica che è possibile pensare come area di disegno del pittore.

Esistono alcuni vantaggi di questo approccio:

-  Codice di disegno diventa dinamico e successivamente modificare grafici in fase di esecuzione.
-  Riducendo la necessità di immagini statiche nel bundle dell'applicazione, è possibile ridurre le dimensioni dell'applicazione.
-  Grafica convertirsi in più resiliente alle modifiche di risoluzione per i dispositivi.

<a name="Drawing_in_a_UIView_Subclass"/>

## <a name="drawing-in-a-uiview-subclass"></a>Disegno di una sottoclasse UIView

Ogni `UIView` ha un `Draw` metodo chiamato dal sistema quando deve essere disegnato. Per aggiungere il codice di creazione di una vista, sottoclasse `UIView` ed eseguire l'override `Draw`:

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

Disegno non dovrebbe mai essere chiamato direttamente. Bensì dal sistema durante l'elaborazione di cicli di esecuzione. Alla prima iterazione del ciclo di esecuzione dopo aver aggiunto alla gerarchia di visualizzazione, una vista relativa `Draw` viene chiamato il metodo. Le chiamate successive a `Draw` si verificano quando la visualizzazione è contrassegnata come debba essere disegnato chiamando `SetNeedsDisplay` o `SetNeedsDisplayInRect` nella vista.

### <a name="pattern-for-graphics-code"></a>Modello per il codice di grafica

Il codice nel `Draw` implementazione deve descrivere le previsioni disegnata. Il codice di disegno segue uno schema in cui imposta uno stato di disegno e chiama un metodo per richiedere l'essere disegnato. Questo modello può essere generalizzato come indicato di seguito:

1. Ottiene un contesto grafico.

2. Consente di impostare gli attributi di disegno.

3. Creare alcuni geometria da primitive di disegno.

4. Chiamare un metodo di disegno o di tratto.

### <a name="basic-drawing-example"></a>Esempio di disegno di base

Ad esempio, si consideri il frammento di codice seguente:

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

Scomponendo questo codice:

```csharp
using (CGContext g = UIGraphics.GetCurrentContext ()) {
...
}
```
Con questa riga ottiene innanzitutto al contesto grafico corrente da usare per il disegno. È possibile pensare un contesto grafico come area di disegno che disegno si verifica, che contiene tutti gli stati relative al disegno, ad esempio tratto e colori di riempimento, nonché la geometria da disegnare.

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
``` 

Dopo aver ottenuto un contesto grafico il codice imposta alcuni attributi da utilizzare quando si disegnano, illustrato in precedenza. In questo caso i colori delle linee larghezza, tratto e riempimento sono impostati. Tutti i disegni successivi verranno quindi utilizzati questi attributi perché vengono gestiti nello stato del contesto della grafica.

Per creare geometria il codice Usa un `CGPath`, che consente a un percorso grafico essere descritti da linee e curve. In questo caso, il percorso aggiunge linee che connettono una matrice di punti per creare un triangolo. Come visualizzato di seguito Graphics Core Usa un sistema di coordinate per la visualizzazione di disegno, in cui l'origine è in alto a sinistra, con connessione diretta x positivo a destra e la direzione-y positivo verso il basso:

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100), 
new CGPoint (220, 200)});

path.CloseSubpath ();
``` 

Dopo aver creato il percorso, viene aggiunto al contesto di grafica in modo che la chiamata `AddPath` e `DrawPath` rispettivamente può disegnarlo.

La visualizzazione risultante è illustrata di seguito:

 ![](core-graphics-images/00-bluetriangle.png "Il triangolo di output di esempio")

## <a name="creating-gradient-fills"></a>Creazione di riempimenti sfumati

Sono disponibili anche forme più avanzate di disegno. Ad esempio, Core Graphics consente di creare sfumature e applicando i tracciati di ritaglio. Per tracciare una sfumatura all'interno del percorso dell'esempio precedente, prima di tutto il percorso deve essere impostato come tracciato di ritaglio:

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

Impostazione del percorso corrente come tracciato di ritaglio vincola tutti i disegni successivi all'interno della geometria del percorso, ad esempio il codice seguente, che consente di tracciare una sfumatura lineare:

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

Queste modifiche consentono una sfumatura come illustrato di seguito:

 ![](core-graphics-images/01-gradient-fill.png "L'esempio con un riempimento sfumato")

## <a name="modifying-line-patterns"></a>Modifica di modelli di riga

Gli attributi di disegno di linee possono essere modificati anche con grafica di base. È inclusa la modifica il colore tratto e larghezza di riga, nonché il modello di riga, come illustrato nel codice seguente:

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

Aggiunta di questo codice prima di eventuali risultati di operazioni di disegno in unità di tracce tratteggiate 10 tempi lunghi, con 4 unità di spaziatura tra trattini, come illustrato di seguito:

 ![](core-graphics-images/02-dashed-stroke.png "Aggiunta di questo codice prima i risultati di operazioni di disegno in tracce tratteggiate")
 
Si noti che quando si utilizzano API unificata di xamarin. IOS, il tipo di matrice deve essere un `nfloat`e deve anche eseguire il cast esplicito a Math. PI.

<a name="Drawing_Images_and_Text"/>

## <a name="drawing-images-and-text"></a>Creazione di immagini e testo

Oltre alla creazione di percorsi nel contesto di una visualizzazione grafica, Core Graphics supporta inoltre disegnare immagini e testo. Per disegnare un'immagine, è sufficiente creare una `CGImage` e passarlo a un `DrawImage` chiamare:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

Tuttavia, questo codice produce un'immagine tracciata capovolta, come illustrato di seguito:

 ![](core-graphics-images/03-upside-down-monkey.png "Un'immagine tracciata capovolta")

Il motivo è origin Core Graphics per disegnare immagine si trova in basso a sinistra, mentre la vista dispone di origine in alto a sinistra. Pertanto, per visualizzare l'immagine correttamente, l'origine deve essere modificato, che può essere eseguita modificando i *matrice di trasformazione corrente* *(CTM)*. Definisce il CTM in cui i punti di live, noto anche come *dello spazio utente*. Inversione CTM nella direzione y e spostandola dall'altezza dei limiti nella direzione y negativo possibile capovolgere l'immagine.

Il contesto grafico include metodi helper per trasformare il CTM. In questo caso `ScaleCTM` "capovolge" il disegno e `TranslateCTM` passa all'angolo superiore sinistro, come illustrato di seguito:

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

L'immagine risultante viene quindi visualizzato in verticale:

 ![](core-graphics-images/04-upright-monkey.png "Nave dritta immagine visualizzata l'esempio")

> [!IMPORTANT]
> Le modifiche per il contesto grafico si applicano a tutte le operazioni di disegnare successivi. Pertanto, quando viene trasformato il CTM, influirà sulle eventuali disegno aggiuntivo. Ad esempio, se è stata disegnata triangolo dopo la trasformazione CTM, apparirà capovolto.

### <a name="adding-text-to-the-image"></a>Aggiunta di testo all'immagine

Come con tracciati e immagini, disegno di testo con Core Graphics implica lo stesso modello di base di impostare lo stato di alcuni elementi grafici e chiamare un metodo da disegnare. Nel caso di testo, il metodo per visualizzare il testo è `ShowText`. Quando aggiunto all'immagine di esempio di disegno, il codice seguente consente di disegnare un testo tramite Core Graphics:

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

Come può notare, impostazione dello stato di grafica per il testo di disegno è simile al disegno della geometria. Per il disegno di testo, tuttavia, vengono applicate anche disegno modalità e il tipo di carattere del testo. In questo caso, un'ombreggiatura viene applicata anche, anche se l'applicazione delle ombreggiature funziona allo stesso modo per il percorso di disegno.

Il testo risultante viene visualizzato con l'immagine come illustrato di seguito:

 ![](core-graphics-images/05-text-on-image.png "Il testo risulta viene visualizzato con l'immagine")

## <a name="memory-backed-images"></a>Immagini di supporto di memoria

Oltre a disegno al contesto di una visualizzazione grafica, Core Graphics supporta il disegno memoria supportata immagini, noto anche come disegno fuori dallo schermo. In questo modo, è necessario:

-  Creazione di un contesto di grafica che è supportata da una in memoria bitmap
-  Impostare lo stato di disegno ed emissione di comandi di disegno
-  Ottenere l'immagine dal contesto
-  Rimozione di contesto


A differenza di `Draw` metodo, in cui il contesto viene fornito dalla visualizzazione, in questo caso è creare il contesto in uno dei due modi:

1. Chiamando `UIGraphics.BeginImageContext` (o `BeginImageContextWithOptions`)

2. Creando un nuovo `CGBitmapContextInstance`

 `CGBitmapContextInstance` è utile quando si lavora direttamente con i bit dell'immagine, ad esempio nei casi in cui si usa un algoritmo di manipolazione immagine personalizzata. In tutti gli altri casi, è consigliabile usare `BeginImageContext` o `BeginImageContextWithOptions`.

Dopo aver creato un contesto dell'immagine, l'aggiunta di codice di disegno è esattamente come si trova in un `UIView` sottoclasse. Ad esempio, l'esempio di codice usato in precedenza per disegnare un triangolo è utilizzabile per tracciare un'immagine in memoria anziché in un `UIView`, come illustrato di seguito:

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

Un uso comune di disegno per una mappa di bit supportato da memoria consiste nell'acquisire un'immagine da qualsiasi `UIView`. Ad esempio, il codice seguente viene eseguito il rendering a livello della visualizzazione a un contesto di bitmap e crea un `UIImage` da quest'ultimo:

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>Creazione di documenti PDF

Oltre alle immagini, Core Graphics supporta il disegno PDF. Ad esempio immagini, è possibile eseguire il rendering di un file PDF in memoria, nonché leggere un file PDF per il rendering in un `UIView`.

### <a name="pdf-in-a-uiview"></a>File PDF in un UIView

Grafica core supporta anche la lettura di un file PDF da un file e di eseguirne il rendering in una vista usando il `CGPDFDocument` classe. Il `CGPDFDocument` classe rappresenta un file PDF nel codice e può essere utilizzato per leggere e tracciare le pagine.

Ad esempio, il codice seguente una `UIView` sottoclasse legge un file PDF da un file in un `CGPDFDocument`:

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

Il `Draw` metodo può quindi usare il `CGPDFDocument` leggere una pagina in `CGPDFPage` ed eseguirne il rendering tramite la chiamata `DrawPDFPage`, come illustrato di seguito:

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

### <a name="memory-backed-pdf"></a>Memoria supportato da PDF

Per un file PDF in memoria, è necessario creare un contesto PDF chiamando `BeginPDFContext`. Disegno in formato PDF è granulare per le pagine. Ogni pagina viene avviata chiamando `BeginPDFPage` e completata chiamando `EndPDFContent`, con la grafica del codice intermedio. Inoltre, come il disegno di immagini, in memoria sottoposti PDF disegno usa un'entità origin in basso a sinistra, che possa essere prese in considerazione modificando semplicemente il CTM come con le immagini.

Il codice seguente viene illustrato come disegnare testo in formato PDF:

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

Viene disegnato il testo risulta in formato PDF, quindi incluso in un `NSData` che può essere salvato, caricato, inviato tramite posta elettronica e così via.


## <a name="summary"></a>Riepilogo

In questo articolo abbiamo esaminato le funzionalità grafiche offerte tramite il *Core Graphics* framework. È stato illustrato come utilizzare Core Graphics per disegnare la geometria, immagini e PDF nel contesto di un `UIView,` e anche ad contesti grafici di supporto di memoria.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di grafica core](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [Procedura dettagliata di animazione e grafica](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core Animation](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Recipe di animazione di base](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
