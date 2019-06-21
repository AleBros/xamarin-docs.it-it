---
title: Ampia gamma di colori in xamarin. IOS
description: Questo documento illustra l'ampia gamma di colori e come può essere usato in un'app xamarin. IOS o xamarin. Mac.
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 523aa1a97e1c536b5afbdb66c52f605382fa338d
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268801"
---
# <a name="wide-color-in-xamarinios"></a>Ampia gamma di colori in xamarin. IOS

_Questo articolo illustra l'ampia gamma di colori e come può essere usato in un'app xamarin. IOS o xamarin. Mac._

iOS 10 e macOS Sierra migliora il supporto per formati pixel intervalli estesi e gli spazi colore a livello di gamma in tutto il sistema, tra cui Framework, ad esempio Core Graphics, l'immagine di base, bare Metal e AVFoundation. Supporto per i dispositivi dotati di schermi di ampia gamma di colori viene diminuito ulteriormente fornendo questo comportamento in tutto lo stack di grafica intero.

## <a name="asset-catalogs"></a>Cataloghi Asset

### <a name="supporting-wide-color-with-asset-catalogs"></a>Supporto di ampia gamma di colori con i cataloghi Asset

In iOS 10 e macOS Sierra, Apple ha espanso i cataloghi Asset consentono di includere e classificare il contenuto di immagine statica nel bundle dell'app per supportare l'ampia gamma di colori.

Tramite cataloghi Asset offrono i vantaggi seguenti a un'app:

- Offrono la migliore opzione di distribuzione per gli asset delle immagini statiche.
- Supportano la correzione automatica dei colori.
- Supportano ottimizzazione formato automatico in pixel.
- Supportano il sezionamento App e App assottigliamento che assicura che solo il contenuto che è pertinente get recapitato al dispositivo dell'utente finale.

Apple ha reso i miglioramenti seguenti ai cataloghi Asset per il supporto di ampia gamma di colori:

- Supportano il contenuto di origine a 16 bit (per ogni canale di colore).
- Supportano contenuto catalogazione dalla vasta gamma di visualizzazione. Il contenuto può essere contrassegnato per sRGB o gamme Display P3.

Lo sviluppatore è disponibili tre opzioni per il supporto di contenuto ampia gamma di colori nelle proprie App:

1. **Non eseguire alcuna operazione** -poiché contenuto ampia gamma di colori debba essere usato solo nelle situazioni in cui l'app deve visualizzare colori vivaci (in cui si miglioreranno l'esperienza dell'utente), contenuto di fuori di tale requisito deve essere lasciato come-è. Continuerà a essere eseguito il rendering correttamente in tutte le situazioni di hardware.
2. **Aggiornare il contenuto esistente a Display P3** -questa operazione richiede allo sviluppatore di sostituire il contenuto dell'immagine esistente nel catalogo di Asset con un nuovo file aggiornato nel formato P3 e contrassegnarlo come tale nell'Editor di Asset. In fase di compilazione, un'immagine derivativo sRGB verrà generata da questi asset.
3. **Fornire contenuti di Asset con ottimizzazione per la** -In questo caso, lo sviluppatore fornirà un sRGB 8 bit sia una visione Display P3 16 bit di ogni immagine nel catalogo di Asset (con codifica in modo corretto nell'editor di asset).

### <a name="asset-catalog-deployment"></a>Distribuzione di catalogo di asset

Di seguito si verifica quando lo sviluppatore invia un'app di Store l'App con i cataloghi Asset che includono contenuto dell'immagine di ampia gamma di colori:

- Quando l'app viene distribuito all'utente finale, il sezionamento App garantisce che solo la variante di contenuto appropriata sia recapitata al dispositivo dell'utente.
- Nel dispositivo che non supportano l'ampia gamma di colori, non sono previsti costi di payload per l'inclusione di contenuto ampia gamma di colori, come non viene mai inviato al dispositivo.
- `NSImage` in macOS Sierra (e versioni successive) selezionerà automaticamente la migliore rappresentazione del contenuto per la visualizzazione dell'hardware.
- Il contenuto visualizzato verrà aggiornato automaticamente quando o se l'hardware di dispositivi Visualizza caratteristiche modifica.

### <a name="asset-catalog-storage"></a>Archiviazione del catalogo Asset

Archiviazione del catalogo asset ha le proprietà e le implicazioni per un'app per le seguenti:

- In fase di compilazione Apple tenta di ottimizzare l'archiviazione del contenuto immagine tramite le conversioni di immagine di alta qualità.
- 16-bit usati per ogni canale di colore per il contenuto di ampia gamma di colori.
- La compressione di contenuto immagine dipendente viene usata per ridurre le dimensioni del contenuto del risultato finale. Nuovo compressioni "perdita di dati" sono state aggiunte per ottimizzare ulteriormente le dimensioni del contenuto.

## <a name="rendering-off-screen-images-in-app"></a>Il rendering fuori schermo immagini nell'App

In base al tipo dell'app per la creazione, può consentire all'utente di includere il contenuto di immagine vengono raccolti da internet o creare contenuto immagine direttamente all'interno dell'app (ad esempio, un disegno vettoriale app, ad esempio).

In entrambi i casi, l'app può eseguire il rendering di immagini necessarie fuori schermo nell'ampia gamma di colori con avanzate funzionalità aggiunte a iOS e macOS.

### <a name="drawing-wide-color-in-ios"></a>Ampia gamma di colori di disegno in iOS

Prima di illustrare come creare correttamente un'immagine di ampia gamma di colori in iOS 10, esaminiamo il codice di disegno iOS comuni seguenti:

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

Si verificano problemi con il codice standard che dovranno essere risolti _prima di_ può essere utilizzato per disegnare un'immagine di ampia gamma di colori. Il `UIGraphics.BeginImageContext (size)` metodo utilizzato per l'avvio di disegno immagine di iOS presenta le limitazioni seguenti:

- Impossibile creare contesti di immagine con più di 8 bit per canale di colore.
- Non può rappresentare i colori nella Extended Range sRGB spazio colore.
- Non hanno la possibilità di fornire un'interfaccia per creare contesti in uno spazio dei colori non sRGB a causa di routine di basso livello C chiamate in background.

Per gestire queste limitazioni di disegnare un'immagine di ampia gamma di colori in iOS 10, usare invece il codice seguente:

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

Il nuovo `UIGraphicsImageRenderer` classe crea un nuovo contesto di immagine che è in grado di gestire Extended Range sRGB spazio colore e ha le caratteristiche seguenti:

- È completamente gestito per impostazione predefinita di colore.
- Supporta il Extended Range sRGB spazio colore per impostazione predefinita.
- Decide se è necessario eseguire il rendering nel sRGB o Extended Range sRGB spazio colore basato sulle funzionalità del dispositivo iOS che viene eseguita l'app.
- Completamente e automaticamente gestisce il contesto dell'immagine (`CGContext`) durata in modo che lo sviluppatore non dovrà preoccuparsi di chiamare il metodo begin ed end i comandi contestuali.
- È compatibile con la `UIGraphics.GetCurrentContext()` (metodo).

Il `CreateImage` metodo del `UIGraphicsImageRenderer` classe per creare un'immagine di ampia gamma di colori e viene passata un gestore di completamento con il contesto dell'immagine in cui disegnare. Tutti il disegno viene eseguito all'interno di questo gestore di completamento come indicato di seguito:

- Il `UIColor.FromDisplayP3` metodo crea un nuovo colore rosso saturazione completa nella vasta gamma di spazio di colore Display P3 e viene utilizzato per disegnare la prima metà del rettangolo. 
- La seconda metà del rettangolo viene disegnato in sRGB normale completamente saturazione colore rosso per il confronto.

### <a name="drawing-wide-color-in-macos"></a>Ampia gamma di colori di disegno in macOS

Il `NSImage` classe è stata estesa in macOS Sierra per supportare il disegno di immagini di ampia gamma di colori. Ad esempio:

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

## <a name="rendering-on-screen-images-in-app"></a>Il rendering sullo schermo di immagini nell'App

Per eseguire il rendering delle immagini a colori wide sullo schermo, il processo è analogo al disegno di un'immagine fuori schermo ampia gamma di colori per macOS e iOS presentato in precedenza.

### <a name="rendering-on-screen-in-ios"></a>Il rendering sullo schermo in iOS

Quando l'app deve eseguire il rendering di un'immagine nell'ampia gamma di colori sullo schermo in iOS, eseguire l'override di `Draw` metodo di `UIView` in questione come di consueto. Ad esempio:

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

Come con iOS 10 la `UIGraphicsImageRenderer` classe mostrato in precedenza, in modo intelligente decide se è necessario eseguire il rendering nel sRGB o Extended Range sRGB spazio colore in base alle funzionalità del dispositivo iOS a cui viene eseguita quando l'app di `Draw` viene chiamato il metodo. Inoltre, il `UIImageView` è stata colore gestito da iOS 9.3.

Se l'app deve sapere come è fatta per il rendering in un `UIView` o `UIViewController`, è possibile verificare la nuova `DisplayGamut` proprietà del `UITraitCollection` classe. Questo valore sarà una `UIDisplayGamut` enum delle operazioni seguenti:

- P3
- SRGB
- Non specificato

Se l'app è richiesto il controllo dello spazio colore utilizzato per disegnare un'immagine, è possibile utilizzare una nuova `ContentsFormat` proprietà del `CALayer` per specificare lo spazio colore desiderato. Questo valore può essere un `CAContentsFormat` enum delle operazioni seguenti:

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>Il rendering sullo schermo in macOS

Quando l'app deve eseguire il rendering di un'immagine nell'ampia gamma di colori sullo schermo in macOS, eseguire l'override di `DrawRect` metodo di `NSView` in questione come di consueto. Ad esempio:

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

Anche in questo caso, decide se è necessario eseguire il rendering nel sRGB o Extended Range sRGB spazio colore in base alle capacità dell'hardware Mac che l'app è in esecuzione su quando il `DrawRect` viene chiamato il metodo.

Se l'app è richiesto il controllo dello spazio colore utilizzato per disegnare un'immagine, è possibile utilizzare una nuova `DepthLimit` proprietà del `NSWindow` classe per specificare lo spazio colore desiderato. Questo valore può essere un `NSWindowDepth` enum delle operazioni seguenti:

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb
