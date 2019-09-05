---
title: Colore Wide in Novell. iOS
description: Questo documento illustra il colore di grandi dimensioni e il modo in cui può essere usato in un'app Novell. iOS o Novell. Mac.
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: a1f5301d0c5c0674e162b3d7689c83bbb4f6ae90
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290538"
---
# <a name="wide-color-in-xamarinios"></a>Colore Wide in Novell. iOS

_Questo articolo illustra un ampio colore e come può essere usato in un'app Novell. iOS o Novell. Mac._

iOS 10 e macOS Sierra migliorano il supporto per i formati pixel estesi e gli spazi dei colori a gamma ampia in tutto il sistema, inclusi i Framework come la grafica principale, le immagini di base, i metal e i AVFoundation. Il supporto per i dispositivi con schermi Wide Color è ulteriormente facilitato fornendo questo comportamento nell'intero stack di grafica.

## <a name="asset-catalogs"></a>Cataloghi asset

### <a name="supporting-wide-color-with-asset-catalogs"></a>Supporto dei colori Wide con i cataloghi asset

In iOS 10 e macOS Sierra Apple ha espanso i cataloghi di asset usati per includere e categorizzare il contenuto di immagini statiche nel bundle dell'app per supportare il colore Wide.

L'uso di cataloghi asset offre i vantaggi seguenti a un'app:

- Forniscono la migliore opzione di distribuzione per gli asset di immagini statiche.
- Supportano la correzione automatica del colore.
- Supportano l'ottimizzazione automatica del formato pixel.
- Supportano il sezionamento delle app e l'assottigliamento delle app, che garantisce che solo il contenuto pertinente venga recapitato al dispositivo dell'utente finale.

In Apple sono stati apportati i miglioramenti seguenti ai cataloghi di asset per il supporto Wide Color:

- Supportano il contenuto di origine a 16 bit (per canale colori).
- Supportano la catalogazione del contenuto per gamma di visualizzazioni. È possibile contrassegnare il contenuto per sRGB o per visualizzare le gamme P3.

Lo sviluppatore dispone di tre opzioni per supportare il contenuto di colore Wide nelle app:

1. **Non eseguire alcuna operazione** , poiché il contenuto di colore ampio deve essere usato solo in situazioni in cui l'app deve visualizzare colori vividi (in cui l'esperienza dell'utente verrà migliorata), il contenuto esterno a questo requisito deve essere lasciato invariato. Il rendering continuerà a essere eseguito correttamente in tutte le situazioni hardware.
2. **Aggiornare il contenuto esistente per visualizzare P3** : è necessario che lo sviluppatore sostituisca il contenuto dell'immagine esistente nel catalogo asset con un nuovo file aggiornato nel formato P3 e lo contrassegna come tale nell'editor di asset. In fase di compilazione, verrà generata un'immagine derivata sRGB da tali asset.
3. **Fornire contenuto ottimizzato** per gli asset: in questa situazione, lo sviluppatore fornirà sia uno sRGB a 8 bit che una visione P3 a 16 bit di ogni immagine nel catalogo asset (correttamente etichettato nell'editor asset).

### <a name="asset-catalog-deployment"></a>Distribuzione del catalogo asset

Gli elementi seguenti si verificheranno quando lo sviluppatore Invia un'app all'App Store con cataloghi di asset che includono contenuto di immagini a colori Wide:

- Quando l'app viene distribuita all'utente finale, il sezionamento delle app garantirà che solo la variante di contenuto appropriata venga recapitata al dispositivo dell'utente.
- Sul dispositivo che non supporta un colore di grandi dimensioni, non è previsto alcun costo del payload per l'inclusione di contenuto a colori Wide, perché non viene mai spedito al dispositivo.
- `NSImage`in macOS Sierra (e versioni successive) selezionerà automaticamente la rappresentazione del contenuto migliore per la visualizzazione dell'hardware.
- Il contenuto visualizzato verrà aggiornato automaticamente quando o se le caratteristiche di visualizzazione dell'hardware dei dispositivi cambiano.

### <a name="asset-catalog-storage"></a>Archiviazione del catalogo asset

L'archiviazione del catalogo asset presenta le seguenti proprietà e implicazioni per un'app:

- In fase di compilazione, Apple tenta di ottimizzare l'archiviazione del contenuto dell'immagine tramite conversioni di immagini di alta qualità.
- i 16 bit vengono usati per canale di colore per il contenuto di colore Wide.
- La compressione dell'immagine dipendente dal contenuto viene usata per ridurre le dimensioni del contenuto finale. Sono state aggiunte nuove compressioni con perdita di dati per ottimizzare ulteriormente le dimensioni del contenuto.

## <a name="rendering-off-screen-images-in-app"></a>Rendering delle immagini fuori schermo nell'app

In base al tipo di app da creare, è possibile che l'utente includa il contenuto dell'immagine raccolto da Internet o crei contenuto di immagine direttamente all'interno dell'app, ad esempio un'app di disegno vettoriale.

In entrambi i casi, l'app può eseguire il rendering dell'immagine richiesta fuori schermo in un colore Wide usando funzionalità avanzate aggiunte a iOS e macOS.

### <a name="drawing-wide-color-in-ios"></a>Disegno di un colore Wide in iOS

Prima di illustrare come disegnare correttamente un'immagine a colori Wide in iOS 10, vedere il codice di disegno iOS comune seguente:

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    UIGraphics.BeginImageContext (size);

    ...

    UIGraphics.EndImageContext ();
    return image = UIGraphics.GetImageFromCurrentImageContext ();
    }
```

Si verificano problemi con il codice standard che dovranno essere risolti _prima_ di poter essere usati per creare un'immagine a colori Wide. Il `UIGraphics.BeginImageContext (size)` metodo usato per avviare il disegno di immagini iOS presenta le limitazioni seguenti:

- Non è possibile creare contesti di immagine con più di 8 bit per canale di colore.
- Non può rappresentare i colori nello spazio dei colori di sRGB con intervallo esteso.
- Non è in grado di fornire un'interfaccia per creare contesti in uno spazio di colore non sRGB a causa delle routine C di basso livello chiamate in background.

Per gestire queste limitazioni e creare un'immagine a colori Wide in iOS 10, usare invece il codice seguente:

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    var render = new UIGraphicsImageRenderer (size);

    var image = render.CreateImage ((UIGraphicsImageRendererContext context) => {
        var bounds = context.Format.Bounds;
        CGRect slice;
        CGRect remainder;
        bounds.Divide (bounds.Width / 2, CGRectEdge.MinXEdge, out slice, out remainder);

        var redP3 = UIColor.FromDisplayP3 (1.0F, 0.0F, 0.0F, 1.0F);
        redP3.SetColor ();
        context.FillRect (slice);

        var redRGB = UIColor.Red;
        redRGB.SetColor ();
        context.FillRect (remainder);
    });

    // Return results
    return image;
}
```

La nuova `UIGraphicsImageRenderer` classe crea un nuovo contesto di immagine in grado di gestire lo spazio colore sRGB esteso nell'intervallo e presenta le funzionalità seguenti:

- Per impostazione predefinita, il colore è completamente gestito.
- Per impostazione predefinita, supporta lo spazio dei colori sRGB di intervallo esteso.
- Stabilisce in modo intelligente se deve essere eseguito il rendering nello spazio di colore sRGB o nell'intervallo esteso sRGB, in base alle funzionalità del dispositivo iOS in cui è in esecuzione l'app.
- Gestisce completamente e automaticamente la durata del contesto dell'`CGContext`immagine () in modo che lo sviluppatore non debba preoccuparsi di chiamare i comandi di contesto Begin e end.
- È compatibile con il `UIGraphics.GetCurrentContext()` metodo.

Il `CreateImage` metodo`UIGraphicsImageRenderer` della classe viene chiamato per creare un'immagine a colori Wide e passa un gestore di completamento con il contesto dell'immagine in cui eseguire il progetto. Tutto il disegno viene eseguito all'interno di questo gestore di completamento come indicato di seguito:

- Il `UIColor.FromDisplayP3` metodo crea un nuovo colore rosso completamente saturo nella gamma ampia che visualizza lo spazio colore P3 e viene usato per creare la prima metà del rettangolo. 
- La seconda metà del rettangolo viene disegnata nei normali colori rossi completamente saturi per il confronto.

### <a name="drawing-wide-color-in-macos"></a>Disegno di un colore Wide in macOS

La `NSImage` classe è stata espansa in MacOS Sierra per supportare il disegno di immagini a colori Wide. Ad esempio:

```csharp
var size = CGSize(250,250);
var wideColorImage = new NSImage(size, false, (drawRect) =>{
    var rects = drawRect.Divide(drawRect.Size.Width/2,CGRect.MinXEdge);

    var color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    var path = new NSBezierPath(rects.Slice).Fill();

    color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    path = new NSBezierPath(rects.Remainder).Fill();

    // Return modified
    return true;
});
```

## <a name="rendering-on-screen-images-in-app"></a>Rendering di immagini sullo schermo nell'app

Per eseguire il rendering di immagini a colori Wide sullo schermo, il processo funziona in modo simile alla creazione di un'immagine a colori Wide fuori schermo per macOS e iOS presentati sopra.

### <a name="rendering-on-screen-in-ios"></a>Rendering sullo schermo in iOS

Quando l'app deve eseguire il rendering di un'immagine in un colore Wide sullo schermo in iOS, `Draw` eseguire l'override `UIView` del metodo di in questione come di consueto. Ad esempio:

```csharp
using System;
using UIKit;
using CoreGraphics;

namespace MonkeyTalk
{
    public class MonkeyView : UIView
    {
        public MonkeyView ()
        {
        }

        public override void Draw (CGRect rect)
        {
            base.Draw (rect);

            // Draw the image to screen
        ...
        }
    }
}
```

Con l'uso di iOS 10 `UIGraphicsImageRenderer` con la classe illustrata sopra, viene deciso in modo intelligente se deve essere eseguito il rendering nello spazio di colore sRGB o nell'intervallo esteso sRGB in base alle funzionalità del dispositivo iOS in cui `Draw` viene eseguita l'app quando viene chiamato il metodo. Inoltre, il `UIImageView` colore è stato gestito anche dopo iOS 9,3.

Se l'app deve essere in grado di eseguire il rendering su un `UIView` o `UIViewController`, può controllare `UITraitCollection` la nuova `DisplayGamut` proprietà della classe. Questo valore sarà un' `UIDisplayGamut` enumerazione dei seguenti elementi:

- P3
- SRGB
- Non specificata

Se l'app vuole controllare lo spazio dei colori usato per creare un'immagine, può usare una nuova `ContentsFormat` proprietà `CALayer` di per specificare lo spazio dei colori desiderato. Questo valore può essere un' `CAContentsFormat` enumerazione dei seguenti elementi:

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>Rendering sullo schermo in macOS

Quando l'app deve eseguire il rendering di un'immagine in un colore Wide sullo schermo in MacOS, `DrawRect` eseguire l'override `NSView` del metodo di in questione come di consueto. Ad esempio:

```csharp
using System;
using AppKit;
using CoreGraphics;
using CoreAnimation;

namespace MonkeyTalkMac
{
    public class MonkeyView : NSView
    {
        public MonkeyView ()
        {
        }

        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Draw graphics into the view
            ...
        }
    }
}
```

Anche in questo caso, decide in modo intelligente se eseguire il rendering nello spazio di colore sRGB o nell'intervallo esteso sRGB in base alle funzionalità dell'hardware Mac su cui è in esecuzione `DrawRect` l'app quando viene chiamato il metodo.

Se l'app vuole controllare quale spazio colore viene usato per creare un'immagine, può usare una nuova `DepthLimit` proprietà `NSWindow` della classe per specificare lo spazio dei colori desiderato. Questo valore può essere un' `NSWindowDepth` enumerazione dei seguenti elementi:

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb
