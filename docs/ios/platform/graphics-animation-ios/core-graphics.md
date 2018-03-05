---
title: Grafica di base
description: Questo articolo illustra i framework iOS grafica di base. Viene illustrato come utilizzare grafica di base per disegnare la geometria, immagini e PDF.
ms.topic: article
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: fe1796839524a271760a9beb82895fd6e93c7ad0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="core-graphics"></a>Grafica di base

_Questo articolo illustra i framework iOS grafica di base. Viene illustrato come utilizzare grafica di base per disegnare la geometria, immagini e PDF._

iOS include il [ *grafica di base* ](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html) framework per supportare il disegno basso livello. Questi modelli sono attivano le funzionalità con interfaccia grafiche all'interno di UIKit. 

Grafica di base è un framework di grafica 2D di basso livello che consente di disegno di grafica indipendenti dispositivo. Disegno in UIKit 2D tutti grafica di base viene utilizzata internamente.

Grafica di base supporta il disegno in un numero di scenari includono:

-  [Disegnare sullo schermo tramite un `UIView` ](#Drawing_in_a_UIView_Subclass) .
-  [Disegno di immagini in memoria o su schermo](#Drawing_Images_and_Text).
-  La creazione e disegno in PDF.
-  Lettura e creazione di un file PDF esistente.


## <a name="geometric-space"></a>Spazio geometrico

Indipendentemente dallo scenario, tutti i disegni effettuati con grafica di base viene eseguito nello spazio geometrico, ovvero che può essere utilizzata in punti astratti anziché in pixel. Si descrivono ciò che si desidera disegnata in termini di geometria e creazione di stato, ad esempio i colori, stili di riga e così via e grafica di base gestisce la conversione di tutti gli elementi in pixel. Questo stato viene aggiunto a un contesto di grafica, è possibile considerare come area di disegno di una funzione di copia.

Esistono alcuni vantaggi di questo approccio:

-  Codice di disegno diventa dinamico e successivamente possibile modificare immagini in fase di esecuzione.
-  Riducendo la necessità per le immagini statiche nel pacchetto di applicazione, è possibile ridurre le dimensioni dell'applicazione.
-  Grafica diventa più flessibile per le modifiche alla risoluzione tra i dispositivi.


## <a name="drawing-in-a-uiview-subclass"></a>Disegno in una sottoclasse UIView

Ogni `UIView` ha un `Draw` metodo che viene chiamato dal sistema quando deve essere disegnato. Aggiungere il codice di disegno di una vista, sottoclasse `UIView` ed eseguire l'override `Draw`:

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

Disegno non dovrebbe mai essere chiamato direttamente. Bensì dal sistema durante l'elaborazione di cicli di esecuzione. La prima volta attraverso il ciclo di esecuzione dopo l'aggiunta di una vista per la gerarchia della visualizzazione, il relativo `Draw` metodo viene chiamato. Le chiamate successive a `Draw` si verificano quando la vista è contrassegnata come da disegnare con una chiamata a `SetNeedsDisplay` o `SetNeedsDisplayInRect` nella vista.

### <a name="pattern-for-graphics-code"></a>Modello per il codice di grafica

Il codice di `Draw` implementazione deve descrivere le previsioni disegnata. Il codice di disegno segue uno schema in cui è imposta uno stato di disegno e chiama un metodo per richiedere da disegnare. Questo modello può essere generalizzato come indicato di seguito:

1. Ottenere un contesto grafico.

2. Consente di impostare gli attributi di disegno.

3. Il nome di alcuni geometry primitive di disegno.

4. Chiamare un metodo di disegno o di traccia.

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

Di seguito si suddividono questo codice:

```csharp
using (CGContext g = UIGraphics.GetCurrentContext ()) {
...
}
```
Con la riga seguente, innanzitutto di ottenere il contesto grafico corrente da utilizzare per il disegno. Può essere considerato un contesto grafico come area di disegno che disegno avviene, contenente dello stato relative al disegno, ad esempio tratto e colori di riempimento, nonché la geometria da disegnare.

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
``` 

Dopo aver ottenuto un contesto grafico il codice imposta alcuni attributi da utilizzare per disegnare, illustrato in precedenza. In questo caso impostati i colori delle linee larghezza, tratto e riempimento. Questi attributi verrà quindi utilizzati da qualsiasi disegno successive poiché vengono gestiti nello stato del contesto grafico.

Per creare il codice di geometria utilizza un `CGPath`, che consente a un percorso di grafica essere descritti da linee e curve. In questo caso, il percorso aggiunge linee che connettono una matrice di punti per creare un triangolo. Come visualizzato di seguito grafica di base viene utilizzato un sistema di coordinate per la visualizzazione di disegno, dove è l'origine in alto a sinistra, con connessione diretta x positivo a destra e la direzione-y positivo verso il basso:

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100), 
new CGPoint (220, 200)});

path.CloseSubpath ();
``` 

Una volta creato il percorso, viene aggiunto al contesto di grafica in modo che la chiamata `AddPath` e `DrawPath` rispettivamente possibile disegnarlo.

La visualizzazione risultante è illustrata di seguito:

 ![](core-graphics-images/00-bluetriangle.png "Il triangolo di output di esempio")

## <a name="creating-gradient-fills"></a>Creazione di riempimenti

Sono disponibili anche più forme di disegno. Ad esempio, grafica di base consente di creare riempimenti e applicare i tracciati di ritaglio. Per disegnare un riempimento sfumato all'interno del percorso dell'esempio precedente, prima di tutto il percorso deve essere impostata come tracciato di ritaglio:

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

Imposta il percorso corrente come tracciato di ritaglio vincola tutti i disegni successivi all'interno della geometria del percorso, ad esempio il codice seguente, che consente di tracciare una sfumatura lineare:

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

Queste modifiche producono un riempimento sfumato, come illustrato di seguito:

 ![](core-graphics-images/01-gradient-fill.png "L'esempio con un riempimento sfumato")

## <a name="modifying-line-patterns"></a>Modifica di modelli di riga

Gli attributi di disegno di righe possono essere modificati anche con grafica di base. È inclusa la modifica il colore di tratto e larghezza di riga, nonché il modello di riga stessa, come illustrato nel codice seguente:

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

Aggiunta di questo codice prima i risultati di operazioni di disegno in unità di tracce tratteggiate 10 lunga con 4 unità di spaziatura tra i trattini, come illustrato di seguito:

 ![](core-graphics-images/02-dashed-stroke.png "Aggiunta di questo codice prima i risultati di operazioni di disegno in tracce tratteggiate")
 
Si noti che quando si utilizza l'API unificata in xamarin. IOS, il tipo di matrice deve essere un `nfloat`e deve anche eseguire il cast esplicito a Math. PI.

## <a name="drawing-images-and-text"></a>Testo e immagini di disegno

Oltre alla creazione di percorsi nel contesto di una visualizzazione grafica, grafica di base supporta inoltre disegnare immagini e testo. Per disegnare un'immagine, creare semplicemente un `CGImage` e passarlo a un `DrawImage` chiamare:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

Tuttavia, ciò produce un'immagine tracciata capovolta, come illustrato di seguito:

 ![](core-graphics-images/03-upside-down-monkey.png "Un'immagine tracciata capovolta")

Il motivo è l'origine delle immagini di base per il disegno di immagini è in basso a sinistra, mentre la vista dispone di origine in alto a sinistra. Pertanto, per visualizzare l'immagine correttamente, l'origine deve essere modificata, che può essere eseguita modificando il *matrice di trasformazione corrente* *(CTM)*. Il CTM definisce punti di residenza, noto anche come *spazio utente*. Inversione CTM nella direzione y e spostandola dall'altezza dei limiti nella direzione y negativi invertire l'immagine.

Il contesto grafico dispone di metodi helper per trasformare il CTM. In questo caso, `ScaleCTM` "consente di capovolgere" il disegno e `TranslateCTM` sposta in alto a sinistra, come illustrato di seguito:

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

 ![](core-graphics-images/04-upright-monkey.png "Ruotate di immagine visualizzata l'esempio")

> [!IMPORTANT]
>  **Nota:** modifiche per il contesto grafico si applicano a tutte le operazioni di disegnare successive. Pertanto, quando viene trasformato il CTM, influisce negativamente qualsiasi disegno aggiuntivo. Ad esempio, se il triangolo disegnata dopo la trasformazione CTM, apparirà capovolto.

### <a name="adding-text-to-the-image"></a>Aggiunta di testo per l'immagine

Come con i percorsi e immagini, creazione di testo con grafica di base prevede lo stesso modello di base di impostare lo stato di alcuni elementi grafici e chiamando un metodo per disegnare. Nel caso di testo, il metodo per visualizzare il testo è `ShowText`. Quando aggiunto all'immagine di disegno di esempio, il codice seguente disegna un testo tramite grafica di base:

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

Come si può notare, l'impostazione dello stato di grafica per il disegno di testo è simile alla creazione di geometria. Per il disegno di testo, tuttavia, vengono applicate anche disegno modalità e il tipo di carattere del testo. In questo caso, un'ombreggiatura viene applicata anche, anche se l'applicazione shadows funziona allo stesso modo per il percorso di disegno.

Il testo risulta viene visualizzato con l'immagine come illustrato di seguito:

 ![](core-graphics-images/05-text-on-image.png "Il testo risulta viene visualizzato con l'immagine")

## <a name="memory-backed-images"></a>Immagini di supporto di memoria

Oltre a disegno al contesto di una visualizzazione grafica, grafica di base supporta disegno memoria sottoposti a immagini, noto anche come disegno fuori dallo schermo. In questo modo, è necessario:

-  Creazione di un contesto grafico supportata da una in memoria bitmap
-  Impostazione stato di disegno e l'esecuzione di comandi di disegno
-  Ottenere l'immagine dal contesto
-  Rimozione di contesto


A differenza di `Draw` (metodo), in cui il contesto viene fornito dalla visualizzazione, in questo caso è creare il contesto in uno dei due modi:

1. Chiamando `UIGraphics.BeginImageContext` (o `BeginImageContextWithOptions`)

2. Creando un nuovo `CGBitmapContextInstance`

 `CGBitmapContextInstance` è utile quando si lavora direttamente con i bit dell'immagine, ad esempio per i casi in cui si utilizza un algoritmo di modifica di immagini personalizzate. In tutti gli altri casi, è necessario utilizzare `BeginImageContext` o `BeginImageContextWithOptions`.

Dopo aver creato un contesto di immagine, l'aggiunta di codice di disegno è esattamente come si trova in un `UIView` sottoclasse. Ad esempio, l'esempio di codice utilizzata in precedenza per disegnare un triangolo utilizzabile per disegnare un'immagine in memoria anziché in un `UIView`, come illustrato di seguito:

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

È un utilizzo comune di disegno di una bitmap con supporto di memoria per acquisire un'immagine da qualsiasi `UIView`. Ad esempio, il codice seguente viene eseguito il rendering a livello di una visualizzazione a un contesto di bitmap e crea un `UIImage` da quest'ultimo:

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>Creazione di PDF

Oltre alle immagini, grafica di base supporta il disegno PDF. Ad esempio immagini, è possibile eseguire il rendering PDF in memoria, nonché un file PDF per il rendering in un `UIView`.

### <a name="pdf-in-a-uiview"></a>PDF in un UIView

Grafica di base supporta anche durante la lettura da un file PDF e di eseguirne il rendering in una visualizzazione utilizzando il `CGPDFDocument` classe. La `CGPDFDocument` classe rappresenta un file PDF nel codice e può essere utilizzato per leggere e creare pagine.

Ad esempio, il codice seguente un `UIView` sottoclasse legge un file PDF da un file in un `CGPDFDocument`:

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

Il `Draw` metodo può quindi utilizzare il `CGPDFDocument` di leggere una pagina in `CGPDFPage` ed eseguirne il rendering chiamando `DrawPDFPage`, come illustrato di seguito:

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

### <a name="memory-backed-pdf"></a>Supporto di memoria PDF

Per un file PDF in memoria, è necessario creare un contesto PDF chiamando `BeginPDFContext`. Disegno in formato PDF è granulare per le pagine. Ogni pagina è stata avviata chiamando `BeginPDFPage` e completata chiamando `EndPDFContent`, con la grafica codice intermedio. Inoltre, come il disegno di immagini, in memoria sottoposti a PDF disegno usa un'origine in basso a sinistra, che possa essere presi in considerazione modificando semplicemente il CTM come immagini.

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

Il testo risulta viene creato per il formato PDF, quindi contenute in un `NSData` che possono essere salvati, caricato, inviare tramite posta elettronica e così via.


## <a name="summary"></a>Riepilogo

In questo articolo è stato esaminato le funzionalità grafiche disponibili tramite il *grafica di base* framework. È stato illustrato come utilizzare grafica di base per disegnare la geometria, immagini e PDF all'interno del contesto di un `UIView,` nonché a contesti di grafici di supporto di memoria.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di grafica di base](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [Grafica e animazione di questa procedura dettagliata](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core Animation](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Recipe animazione core](https://developer.xamarin.com/recipes/ios/animation/coreanimation)
