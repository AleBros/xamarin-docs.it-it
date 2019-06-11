---
title: Immagine di base in xamarin. IOS
description: Immagine di base è un nuovo framework introdotto con iOS 5 per fornire l'elaborazione di immagini e funzionalità di miglioramento video live. Questo articolo presenta queste caratteristiche con esempi di xamarin. IOS.
ms.prod: xamarin
ms.assetid: 91E0780B-FF8A-E70D-9CD4-419119612B2D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: b58cf2244d16e154cf0a979bc509e7356a75bdb2
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827832"
---
# <a name="core-image-in-xamarinios"></a>Immagine di base in xamarin. IOS

_Immagine di base è un nuovo framework introdotto con iOS 5 per fornire l'elaborazione di immagini e funzionalità di miglioramento video live. Questo articolo presenta queste caratteristiche con esempi di xamarin. IOS._

Immagine di base è un nuovo framework introdotto in iOS 5 che fornisce una serie di filtri predefiniti e gli effetti da applicare per le immagini e video, ad esempio rilevamento viso.

Questo documento contiene semplici esempi di:

-  Rilevamento viso.
-  Applicazione di filtri di un'immagine
-  Elencare i filtri disponibili.


Questi esempi devono aiutarti a iniziare l'inserimento di funzionalità di immagine di base nelle applicazioni xamarin. IOS.

## <a name="requirements"></a>Requisiti

È necessario usare la versione più recente di Xcode.

## <a name="face-detection"></a>Rilevamento viso

La funzionalità di rilevamento viso immagine Core viene semplicemente tutto ciò che afferma: tenta di identificare i visi nelle foto e restituisce le coordinate di tutti i volti che esso venga riconosciuto. Queste informazioni possono essere usate per contare il numero di persone in un'immagine, gli indicatori di disegno dell'immagine (ad es. per 'tag' persone in una fotografia), o qualsiasi altro elemento è possibile pensare.

Questo codice da CoreImage\SampleCode.cs viene illustrato come creare e usare il rilevamento viso in un'immagine incorporata:

```csharp
var image = new UIImage("photoFace.JPG");
var context = CIContext.FromOptions(null);
var detector = CIDetector.CreateFaceDetector (context, true);
var ciImage = CIImage.FromCGImage(image.CGImage);
CIFeature[] features = detector.FeaturesInImage(ciImage);
```

Matrice di funzionalità verrà popolata con `CIFaceFeature` oggetti (se sono stati rilevati eventuali volti). È presente un `CIFaceFeature` per ogni viso. `CIFaceFeature` presenta le proprietà seguenti:

-  HasMouthPosition: indica se è stata rilevata una bocca per il viso.
-  HasLeftEyePosition: indica se è stata rilevata l'occhio del lettore a sinistra per il viso.
-  HasRightEyePosition: indica se è stata rilevata l'occhio del lettore a destra per il viso. 
-  MouthPosition: le coordinate della bocca per il viso.
-  LeftEyePosition: le coordinate dell'occhio del lettore a sinistra per il viso.
-  RightEyePosition: le coordinate dell'occhio del lettore a destra per il viso.


Le coordinate per tutte queste proprietà hanno origine in basso a sinistra, a differenza di UIKit che utilizza la parte superiore sinistra come origine. Quando si usano le coordinate in `CIFaceFeature` assicurarsi ' capovolgere ' li. Questa vista molto semplice immagine personalizzata in CoreImage\CoreImageViewController.cs illustra su cui disegnare l'immagine 'indicatore viso' triangoli (nota di `FlipForBottomOrigin` (metodo)):

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

Quindi nel file SampleCode.cs l'immagine e le funzionalità vengono assegnate prima che l'immagine viene ridisegnato:

```csharp
faceView.Image = image;
faceView.Features = features;
faceView.SetNeedsDisplay();
```

Lo screenshot Mostra l'output di esempio: i percorsi delle caratteristiche del viso rilevati vengono visualizzati in un UITextView ed disegnati nell'immagine di origine usando CoreGraphics.

A causa della modalità di funzionamento di riconoscimento facciale, in alcuni casi rileva cose oltre i visi umani (ad esempio, queste esistono programmatori giocattoli!).

## <a name="filters"></a>Filtri

Sono presenti più di 50 diversi filtri predefiniti e il framework è estensibile, in modo che sia possono implementare nuovi filtri.

## <a name="using-filters"></a>Utilizzo di filtri

Applica un filtro a un'immagine con quattro passaggi distinti: il caricamento dell'immagine, la creazione del filtro, applicando il filtro e il salvataggio (o visualizzazione) il risultato.

In primo luogo, caricare un'immagine in un `CIImage` oggetto.

```csharp
var uiimage = UIImage.FromFile ("photo.JPG");
var ciimage = new CIImage (uiimage);
```

In secondo luogo, creare la classe di filtro e impostarne le proprietà.

```csharp
var sepia = new CISepiaTone();
sepia.Image = ciimage;
sepia.Intensity = 0.8f;
```

In terzo luogo, accedere il `OutputImage` proprietà e chiamare il `CreateCGImage` metodo per eseguire il rendering il risultato finale.

```csharp
CIImage output = sepia.OutputImage;
var context = CIContext.FromOptions(null);
var cgimage = context.CreateCGImage (output, output.Extent);
```

Infine, assegnare l'immagine a una vista per visualizzare il risultato. In un'applicazione reale l'immagine risultante potrebbe essere salvato il file System, Album di foto, un Tweet o messaggio di posta elettronica.

```csharp
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

Questi screenshot mostrano il risultato del `CISepia` e `CIHueAdjust` filtri che vengono illustrati il CoreImage.zip esempi di codice.

Vedere le [regolare contratto e la luminosità di un file di immagine Recipe](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image) per un esempio del `CIColorControls` filtro.

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

### <a name="listing-filters-and-their-properties"></a>Elenco di filtri e le relative proprietà

Questo codice da CoreImage\SampleCode.cs restituisce l'elenco completo dei filtri predefiniti e i relativi parametri.

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

Il [riferimento alla classe CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.html) vengono descritti i filtri incorporati 50 e le relative proprietà. Usando il codice sopra riportato è possibile eseguire una query le classi di filtro, inclusi i valori predefiniti per i parametri e i valori consentiti massimo e minimi (che potrebbero essere usati per convalidare gli input prima di applicare un filtro).

L'output di un elenco delle categorie è simile nel simulatore, è possibile scorrere l'elenco per visualizzare tutti i filtri e i relativi parametri.

 [![](introduction-to-coreimage-images/coreimage05.png "L'output di un elenco delle categorie è simile nel simulatore")](introduction-to-coreimage-images/coreimage05.png#lightbox)

Ogni filtro elencato è stato esposto come una classe in xamarin. IOS, in modo che è anche possibile esplorare l'API Xamarin.iOS.CoreImage nel Browser di Assembly o con completamento automatico in Visual Studio per Mac o Visual Studio. 

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come utilizzare alcune delle nuove funzionalità di framework iOS 5 Core immagine, ad esempio rilevamento viso e applicato i filtri di un'immagine. Sono disponibili decine di filtri diverse immagini in framework da usare.

## <a name="related-links"></a>Collegamenti correlati

- [Immagine di base (esempio)](https://developer.xamarin.com/samples/monotouch/CoreImage/)
- [Regolare la luminosità di un file di immagine Recipe e contratto](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)
- [Utilizzando i filtri di immagine di base](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Conceptual/CoreImaging/ci_tasks/ci_tasks.html)
- [Riferimento alla classe CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.htm)
