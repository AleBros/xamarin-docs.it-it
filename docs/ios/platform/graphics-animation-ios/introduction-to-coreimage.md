---
title: Immagine di base in Novell. iOS
description: L'immagine principale è un nuovo Framework introdotto con iOS 5 per fornire funzionalità di elaborazione delle immagini e di miglioramento dei video in tempo reale. Questo articolo presenta le seguenti funzionalità con gli esempi di Novell. iOS.
ms.prod: xamarin
ms.assetid: 91E0780B-FF8A-E70D-9CD4-419119612B2D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: ffaa6553830a64589818c991e8f729ff7232e367
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752839"
---
# <a name="core-image-in-xamarinios"></a>Immagine di base in Novell. iOS

_L'immagine principale è un nuovo Framework introdotto con iOS 5 per fornire funzionalità di elaborazione delle immagini e di miglioramento dei video in tempo reale. Questo articolo presenta le seguenti funzionalità con gli esempi di Novell. iOS._

L'immagine principale è un nuovo Framework introdotto in iOS 5 che offre una serie di filtri e effetti predefiniti da applicare alle immagini e ai video, incluso il rilevamento dei visi.

Questo documento contiene esempi semplici di:

- Rilevamento viso.
- Applicazione di filtri a un'immagine
- Elenco dei filtri disponibili.

Questi esempi sono utili per iniziare a incorporare le funzionalità di base delle immagini nelle applicazioni Novell. iOS.

## <a name="requirements"></a>Requisiti

È necessario usare la versione più recente di Xcode.

## <a name="face-detection"></a>Rilevamento viso

La funzionalità di rilevamento del volto dell'immagine principale esegue solo le operazioni indicate, tenta di identificare i visi in una foto e restituisce le coordinate di qualsiasi visi che riconosce. Queste informazioni possono essere usate per contare il numero di persone in un'immagine, per creare indicatori sull'immagine, ad esempio per l'assegnazione di tag alle persone in una fotografia) o qualsiasi altra cosa si possa pensare.

Questo codice di CoreImage\SampleCode.cs illustra come creare e usare il rilevamento viso su un'immagine incorporata:

```csharp
var image = new UIImage("photoFace.JPG");
var context = CIContext.FromOptions(null);
var detector = CIDetector.CreateFaceDetector (context, true);
var ciImage = CIImage.FromCGImage(image.CGImage);
CIFeature[] features = detector.FeaturesInImage(ciImage);
```

La matrice delle funzionalità verrà popolata `CIFaceFeature` con gli oggetti (se sono state rilevate facce). Esiste un oggetto `CIFaceFeature` per ogni face. `CIFaceFeature`dispone delle proprietà seguenti:

- HasMouthPosition: indica se è stata rilevata una bocca per questa faccia.
- HasLeftEyePosition: indica se l'occhio sinistro è stato rilevato per questa faccia.
- HasRightEyePosition: indica se l'occhio destro è stato rilevato per questa faccia. 
- MouthPosition: coordinate della bocca per questa faccia.
- LeftEyePosition: le coordinate dell'occhio sinistro per questa faccia.
- RightEyePosition: coordinate dell'occhio destro per questa faccia.

Le coordinate per tutte queste proprietà hanno origine in basso a sinistra, a differenza di UIKit che usa la parte superiore sinistra dell'origine. Quando `CIFaceFeature` si usano le coordinate, assicurarsi di "capovolgerle". Questa visualizzazione di immagini personalizzate di base in CoreImage\CoreImageViewController.cs illustra come creare triangoli dell'indicatore del volto sull'immagine (si noti il `FlipForBottomOrigin` metodo):

```csharp
public class FaceDetectImageView : UIView
{
    public Xamarin.iOS.CoreImage.CIFeature[] Features;
    public UIImage Image;
    public FaceDetectImageView (RectangleF rect) : base(rect) {}
    CGPath path;
    public override void Draw (RectangleF rect) {
        base.Draw (rect);
        if (Image != null)
            Image.Draw(rect);

        using (var context = UIGraphics.GetCurrentContext()) {
            context.SetLineWidth(4);
            UIColor.Red.SetStroke ();
            UIColor.Clear.SetFill ();
            if (Features != null) {
                foreach (var feature in Features) { // for each face
                    var facefeature = (CIFaceFeature)feature;
                    path = new CGPath ();
                    path.AddLines(new PointF[]{ // assumes all 3 features found
                        FlipForBottomOrigin(facefeature.LeftEyePosition, 200),
                        FlipForBottomOrigin(facefeature.RightEyePosition, 200),
                        FlipForBottomOrigin(facefeature.MouthPosition, 200)
                    });
                    path.CloseSubpath();
                    context.AddPath(path);
                    context.DrawPath(CGPathDrawingMode.FillStroke);
                }
            }
        }
    }
    /// <summary>
    /// Face recognition coordinates have their origin in the bottom-left
    /// but we are drawing with the origin in the top-left, so "flip" the point
    /// </summary>
    PointF FlipForBottomOrigin (PointF point, int height)
    {
        return new PointF(point.X, height - point.Y);
    }
}
```

Quindi, nel file SampleCode.cs le immagini e le funzionalità vengono assegnate prima che l'immagine venga ridisegnato:

```csharp
faceView.Image = image;
faceView.Features = features;
faceView.SetNeedsDisplay();
```

Lo screenshot mostra l'output di esempio: i percorsi delle funzionalità facciali rilevate vengono visualizzati in un UITextView e disegnati sull'immagine di origine usando CoreGraphics.

A causa del modo in cui il riconoscimento facciale funziona, occasionalmente rileverà elementi oltre ai visi umani, ad esempio le scimmie giocattolo.

## <a name="filters"></a>Filtri

Sono presenti più di 50 filtri incorporati diversi e il Framework è estensibile in modo che sia possibile implementare nuovi filtri.

## <a name="using-filters"></a>Uso dei filtri

L'applicazione di un filtro a un'immagine prevede quattro passaggi distinti: caricare l'immagine, creare il filtro, applicare il filtro e salvare o visualizzare il risultato.

Prima di tutto, caricare un'immagine `CIImage` in un oggetto.

```csharp
var uiimage = UIImage.FromFile ("photo.JPG");
var ciimage = new CIImage (uiimage);
```

In secondo luogo, creare la classe filter e impostarne le proprietà.

```csharp
var sepia = new CISepiaTone();
sepia.Image = ciimage;
sepia.Intensity = 0.8f;
```

In terzo luogo, `OutputImage` accedere alla proprietà e `CreateCGImage` chiamare il metodo per eseguire il rendering del risultato finale.

```csharp
CIImage output = sepia.OutputImage;
var context = CIContext.FromOptions(null);
var cgimage = context.CreateCGImage (output, output.Extent);
```

Infine, assegnare l'immagine a una visualizzazione per visualizzare il risultato. In un'applicazione reale l'immagine risultante potrebbe essere salvata nel file System, nell'album di foto, in un tweet o in un messaggio di posta elettronica.

```csharp
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

Queste schermate mostrano il risultato dei `CISepia` filtri e `CIHueAdjust` illustrati nel codice di esempio CoreImage. zip.

Per un esempio del filtro, vedere la pagina relativa alla [regolazione del contratto e della luminosità di una ricetta](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image) per le `CIColorControls` immagini.

```csharp
var uiimage = UIImage.FromFile("photo.JPG");
var ciimage = new CIImage(uiimage);
var hueAdjust = new CIHueAdjust();   // first filter
hueAdjust.Image = ciimage;
hueAdjust.Angle = 2.094f;
var sepia = new CISepiaTone();       // second filter
sepia.Image = hueAdjust.OutputImage; // output from last filter, input to this one
sepia.Intensity = 0.3f;
CIFilter color = new CIColorControls() { // third filter
    Saturation = 2,
    Brightness = 1,
    Contrast = 3,
    Image = sepia.OutputImage    // output from last filter, input to this one
};
var output = color.OutputImage;
var context = CIContext.FromOptions(null);
// ONLY when CreateCGImage is called do all the effects get rendered
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

```csharp
var context = CIContext.FromOptions (null);
```

```csharp
var context = CIContext.FromOptions(new CIContextOptions() {
    UseSoftwareRenderer = true  // CPU
});
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

### <a name="listing-filters-and-their-properties"></a>Elenco di filtri e relative proprietà

Questo codice di CoreImage\SampleCode.cs restituisce l'elenco completo dei filtri predefiniti e dei relativi parametri.

```csharp
var filters = CIFilter.FilterNamesInCategories(new string[0]);
foreach (var filter in filters){
   display.Text += filter +"\n";
   var f = CIFilter.FromName (filter);
   foreach (var key in f.InputKeys){
     var attributes = (NSDictionary)f.Attributes[new NSString(key)];
     var attributeClass = attributes[new NSString("CIAttributeClass")];
     display.Text += "   " + key;
     display.Text += "   " + attributeClass + "\n";
   }
}
```

Il [riferimento alla classe CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.html) descrive i filtri incorporati 50 e le relative proprietà. Utilizzando il codice precedente, è possibile eseguire una query sulle classi di filtro, inclusi i valori predefiniti per i parametri e i valori massimi e minimi consentiti (che possono essere utilizzati per convalidare gli input prima di applicare un filtro).

L'output delle categorie elenco è simile al seguente nel simulatore: è possibile scorrere l'elenco per visualizzare tutti i filtri e i relativi parametri.

 [![](introduction-to-coreimage-images/coreimage05.png "L'output delle categorie elenco è simile al seguente nel simulatore")](introduction-to-coreimage-images/coreimage05.png#lightbox)

Ogni filtro elencato è stato esposto come una classe in Novell. iOS, quindi è anche possibile esplorare l'API Novell. iOS. CoreImage nel browser assembly o usare il completamento automatico in Visual Studio per Mac o Visual Studio. 

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare alcune delle nuove funzionalità del Framework per immagini iOS 5 core come il rilevamento del viso e l'applicazione di filtri a un'immagine. Nel Framework sono disponibili dozzine di filtri immagine diversi che è possibile usare.

## <a name="related-links"></a>Collegamenti correlati

- [Immagine principale (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/coreimage)
- [Regolazione del contratto e della luminosità di una ricetta per le immagini](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)
- [Uso dei filtri immagine di base](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Conceptual/CoreImaging/ci_tasks/ci_tasks.html)
- [Riferimento alla classe CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.htm)
