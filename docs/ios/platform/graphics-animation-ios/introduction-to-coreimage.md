---
title: Immagine di core in xamarin. IOS
description: Immagine dei componenti di base è un nuovo framework introdotte con iOS 5 per fornire l'elaborazione di immagini e funzionalità di miglioramento video live. Questo articolo descrive queste funzionalità con gli esempi di xamarin. IOS.
ms.prod: xamarin
ms.assetid: 91E0780B-FF8A-E70D-9CD4-419119612B2D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 6032554a0ddbda26ff5de94f6035bc4f8c15a22a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786632"
---
# <a name="core-image-in-xamarinios"></a>Immagine di core in xamarin. IOS

_Immagine dei componenti di base è un nuovo framework introdotte con iOS 5 per fornire l'elaborazione di immagini e funzionalità di miglioramento video live. Questo articolo descrive queste funzionalità con gli esempi di xamarin. IOS._

Immagine dei componenti di base è un nuovo framework introdotto in iOS 5 che fornisce una serie di filtri predefiniti e gli effetti da applicare a immagini e video, ad esempio il rilevamento viso in.

Questo documento contiene esempi semplici di:

-  Rilevamento di tipo di carattere.
-  Applicazione di filtri a un'immagine
-  Elenca i filtri disponibili.


Questi esempi dovrebbero iniziare l'inserimento di immagine Core funzionalità nelle applicazioni di xamarin. IOS.

## <a name="requirements"></a>Requisiti

È necessario utilizzare la versione più recente di Xcode.

## <a name="face-detection"></a>Rilevamento viso

La funzionalità di rilevamento viso immagine Core consente solo, poiché tenta di identificare i caratteri tipografici in una foto e restituisce le coordinate di tutti i caratteri tipografici che esso venga riconosciuto. Queste informazioni consente di contare il numero di persone in un'immagine, disegnare indicatori dell'immagine (ad es. per 'tag' persone in una fotografia), o qualsiasi altro elemento può essere considerato.

Questo codice da CoreImage\SampleCode.cs di seguito viene illustrato come creare e utilizzare il rilevamento viso in un'immagine incorporata:

```csharp
var image = new UIImage("photoFace.JPG");
var context = CIContext.FromOptions(null);
var detector = CIDetector.CreateFaceDetector (context, true);
var ciImage = CIImage.FromCGImage(image.CGImage);
CIFeature[] features = detector.FeaturesInImage(ciImage);
```

Matrice di funzionalità verrà popolata con `CIFaceFeature` oggetti (se sono stati rilevati tutti i caratteri tipografici). È presente un `CIFaceFeature` per ogni tipo di carattere. `CIFaceFeature` presenta le seguenti proprietà:

-  HasMouthPosition – se una bocca rilevata per questo tipo di carattere.
-  HasLeftEyePosition – indica se l'occhio sinistro rilevato per questo tipo di carattere.
-  HasRightEyePosition – indica se all'occhio destro è stato rilevato per questo tipo di carattere. 
-  MouthPosition: le coordinate della bocca per questo tipo di carattere.
-  LeftEyePosition: le coordinate dell'occhio per questo tipo di carattere a sinistra.
-  RightEyePosition: le coordinate di destra occhio per questo tipo di carattere.


Le coordinate per tutte queste proprietà hanno origine in basso a sinistra, a differenza di UIKit che utilizza in alto a sinistra come origine. Quando si usano le coordinate in `CIFaceFeature` assicurarsi capovolgere '' li. Questa vista molto semplice immagine personalizzata in CoreImage\CoreImageViewController.cs viene illustrato come disegnare ' carattere ' triangolo nell'immagine (si noti il `FlipForBottomOrigin` (metodo)):

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

La schermata mostra l'output di esempio: i percorsi delle caratteristiche del viso rilevate siano visualizzati in un UITextView e disegnati sull'immagine di origine utilizzando CoreGraphics.

A causa del modo in cui il riconoscimento facciale funziona, in alcuni casi rileva elementi oltre a risorse umane facce (ad esempio, questi scimmie giocattoli!).

## <a name="filters"></a>Filtri

Sono presenti più di 50 diversi filtri predefiniti e il framework è estensibile, in modo che sia possono implementare nuovi filtri.

## <a name="using-filters"></a>Utilizzo di filtri

Applica un filtro a un'immagine costituito da quattro fasi distinte: il caricamento dell'immagine, la creazione del filtro, applicare il filtro e il salvataggio (o visualizzazione) il risultato.

Innanzitutto, caricare un'immagine in un `CIImage` oggetto.

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

In terzo luogo, accedere il `OutputImage` proprietà e chiamare il `CreateCGImage` metodo per eseguire il rendering del risultato finale.

```csharp
CIImage output = sepia.OutputImage;
var context = CIContext.FromOptions(null);
var cgimage = context.CreateCGImage (output, output.Extent);
```

Assegnare infine l'immagine a una vista per visualizzare il risultato. In un'applicazione reale, l'immagine risultante potrebbe essere salvato il file System, Album foto, un Tweet o posta elettronica.

```csharp
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

Questi screenshot mostrano il risultato del `CISepia` e `CIHueAdjust` i filtri vengono illustrati il CoreImage.zip con codice di esempio.

Vedere il [regolare contratto e luminosità di un'immagine di Recipe](https://developer.xamarin.com/recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image) per un esempio del `CIColorControls` filtro.

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

Il [riferimento alla classe CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.html) descrive i filtri incorporati 50 e le relative proprietà. Tramite il codice sopra riportato è possibile eseguire una query le classi di filtro, inclusi i valori predefiniti per i parametri e i valori consentiti massimo e minimi (che potrebbero essere usati per convalidare gli input prima di applicare un filtro).

L'elenco categorie output è simile nel simulatore, è possibile scorrere l'elenco per visualizzare tutti i filtri e i relativi parametri.

 [![](introduction-to-coreimage-images/coreimage05.png "L'elenco categorie output è simile nel simulatore")](introduction-to-coreimage-images/coreimage05.png#lightbox)

Ogni filtro elencato è stato esposto come una classe in xamarin. IOS, pertanto è anche possibile esplorare l'API Xamarin.iOS.CoreImage nel Browser di Assembly o utilizzo del completamento automatico in Visual Studio per Mac o Visual Studio. 

## <a name="summary"></a>Riepilogo

In questo articolo ha illustrato come utilizzare le nuove funzionalità di framework iOS 5 immagine Core come rilevamento viso e applicazione di filtri a un'immagine. Sono disponibili moltissime filtri immagine diversa in framework da usare.

## <a name="related-links"></a>Collegamenti correlati

- [Immagine di base (esempio)](https://developer.xamarin.com/samples/CoreImage/)
- [Regolare contratto e la luminosità di un'immagine di Recipe](https://developer.xamarin.com/recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)
- [Utilizzo di filtri di immagine dei componenti di base](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Conceptual/CoreImaging/ci_tasks/ci_tasks.html)
- [Riferimento alla classe CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.htm)
