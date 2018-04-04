---
title: Colore Wide
description: Questo articolo descrive il colore ampia e come può essere usato in un'app xamarin o Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 5f56b396715159cbc1539ae9a7f30cc7ad2236bf
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="wide-color"></a>Colore Wide

_Questo articolo descrive il colore ampia e come può essere usato in un'app xamarin o Xamarin.Mac._

iOS 10 e macOS Sierra migliora il supporto per i formati di pixel di intervallo esteso e spazi colore ampia in tutto il sistema incluso Framework, ad esempio grafica di base, l'immagine di base, Metal e AVFoundation. Supporto per i dispositivi dotati di schermi di colori ampia ulteriormente è agevolato fornendo questo comportamento in tutto lo stack dell'intero grafico.

## <a name="about-wide-color"></a>Informazioni sui colori Wide

Come descritto in precedenza, iOS 10 e macOS Sierra migliora il supporto per i formati di pixel di intervallo esteso e spazi colore ampia in tutto il sistema, inclusi Framework, ad esempio grafica di base, l'immagine di base, Metal e AVFoundation. Supporto per i dispositivi dotati di schermi di colori ampia ulteriormente è agevolato fornendo questo comportamento in tutto lo stack dell'intero grafico.

Nel misero Apple creato ColorSync per gestire i colori di elaborazione sul Mac. Sono inoltre consente di trovare il colore ICC (International Consortium) per creare e alzare di livello un set di standard per la gestione di colore sull'hardware del computer. Lavoro Apple con ICC è stata inclusa in ColorSync ed è stato compilato in un elemento fondamentale di OS X (ora denominato macOS).

Apple è stata inoltre all'avanguardia in termini di tecnologia di visualizzazione con hardware, ad esempio Retina Visualizza, P3 visualizzare la nuova visualizzazione di spazio colore P3 (rilasciato nel iMac 2015) e il TrueTone consente di visualizzare i professionisti iPad, iPhone 7 e iPhone 7 Plus.

Con colore Wide macOS Sierra e iOS 10, Apple sta cambiando il modo in cui sia macOS e iOS gestiscono colore per usufruire di queste nuove tecnologie di visualizzazione.

## <a name="core-color-concepts"></a>Concetti di colore

I seguenti concetti di colore di base devono essere considerati prima di eseguire un'analisi approfondita relativa al colore macOS e iOS:

### <a name="color-space"></a>Spazio colore

Spazio colore è un ambiente in cui i colori possono essere rappresentati e confrontati. Può trattarsi di uno spazio di quota da una a quattro definito dall'intensità dei componenti di colore. 

[![](wide-color-images/color00.png "Uno spazio colore")](wide-color-images/color00.png#lightbox)

### <a name="color-channels"></a>I canali di colore

I componenti di colore possono anche essere detto canali di colore. Alcune rappresentazioni familiarità sarebbe spazi RGB, spazi di grigio, CMYK, spazi o spazi indipendente dal dispositivo. 

[![](wide-color-images/color02.png "I componenti di colore possono anche essere indicati come canali di colore")](wide-color-images/color02.png#lightbox)

### <a name="color-primaries"></a>Colori primari

Colori primari forniscono il sistema di coordinate utilizzato per confrontare e calcolare i colori. Colori primari rientrano in genere versione più intenso il colore specificato che può essere generato all'interno del canale di colore.

[![](wide-color-images/color01.png "Colori primari forniscono il sistema di coordinate utilizzato per confrontare e i colori di calcolo")](wide-color-images/color01.png#lightbox)

Nel caso lo spazio colore RGB rappresentato in precedenza, i colori primari sono dove il `1.0` le coordinate sono ancorate (ad esempio `[1.0, 0.0, 0.0]` del rosso).

### <a name="color-gamut"></a>Gamma di colori

Gamma di colori fa riferimento a tutti i colori che possono essere definiti come una combinazione dei canali di colore singoli all'interno di un offrono spazio colore.

[![](wide-color-images/color03.png "Esempio di colore scala")](wide-color-images/color03.png#lightbox)

## <a name="what-is-wide-color"></a>Che cos'è ampia colore

Prima di analizzare l'argomento di colori ampia, una descrizione deve essere non corrente standard del settore per colore, lo spazio di colore RGB Standard (sRGB). È lo spazio colore maggiormente utilizzati nel calcolo oggi e lo spazio colore predefinito per iOS e Mac OS.

Spazio colore sRGB dispone delle proprietà seguenti:

- È basato sullo standard ITU-R BT. 709.
- Ha un valore approssimativo Gamma 2.2.
- Rappresenta le condizioni di illuminazione tipico (D65).

Poiché sRGB è ampiamente che utilizzata nel settore, uno sviluppatore può rendere alcune ipotesi che il colore specificato verrà rappresentato fedelmente su qualsiasi dispositivo a che cui viene visualizzato. Tuttavia, questo potrebbe non essere sempre il caso. Esistono inoltre diversi colori che non rientrano in sRGB spazio colore e di conseguenza, non possono essere rappresentati in essa contenuti.

Ad esempio, molti tessili progettati con thread usano molte inchiostri e tinture fallback di fuori di sRGB. Anche molti prodotti che rileva una persona in tutto il periodo giornaliero vengono creati con colori chiaro, vividi rientrano sRGB spazio colore. Alcuni degli esempi più interessanti di colori che non possono essere rappresentati in sRGB sono aspetti di natura come i tramonti, in autunno, fiori esotiche e acque tropicali.

Gli utenti che acquisizione di immagini digitali in formato non ELABORATO possono disporre di immagini nei dispositivi che contengono tutti i dati colore, anche se si non può essere rappresentato correttamente in sRGB spazio colore e, pertanto, non può essere visualizzato correttamente nella schermata.

### <a name="the-display-p3-color-space"></a>Lo spazio colore P3 di visualizzazione

2015, Apple ha rilasciato nuovi prodotti (iMac e iPad Pro 9.7") che forniscono lo spazio di visualizzazione P3 colore nuovo per gestire i problemi di spazio colore sRGB.

[![](wide-color-images/color04.png "Lo spazio colore di nuovo visualizzato P3")](wide-color-images/color04.png#lightbox)

Lo spazio colore P3 di visualizzazione dispone delle proprietà seguenti:

- Supporta uno spazio colore wide per piattaforme moderne hardware.
- In base allo standard DCI SMPTE-P3. DCI P3 è stato progettato per i proiettori digitali, ma è stato modificato da Apple per supportare i monitoraggi.
- Ha un valore approssimativo Gamma 2.2.
- Rappresenta le condizioni di illuminazione tipico (D65).

In base a Apple, gli utenti sono lo spostamento di flussi di lavoro per le piattaforme per dispositivi mobili. Risolvere i problemi di presentazione di colore presentati da sRGB nei dispositivi mobili professionali (iPad Pro), richiesta anche molto più di una visualizzazione a colori wide. Una delle soluzioni di stato per aggiornare la calibrazione factory, in modo che ogni singolo dispositivo è stato calibrato la factory di garantire che al dispositivo, visualizzazione a colori è accurati e coerenti.

Un'altra soluzione consiste nell'inclusione di sistema completo, gestione del colore che Apple è compilato in iOS 10 e macOS Sierra. 

### <a name="the-extended-range-srgb-color-space"></a>Spazio colore sRGB intervallo esteso

Account per tutte le app iOS esistente che vengano compilate e ottimizzate per sRGB è nuovo Gestione iOS 10 colore a livello di sistema. È stato progettato per garantire che non influire entrambi colore rappresentazione o app di queste App esistenti.

Per gestire questa situazione, Apple ha incluso l'intervallo esteso sRGB spazio colore in iOS 10 (e macOS Sierra).

Spazio colore sRGB intervallo esteso dispone delle proprietà seguenti:

- Dispone di tutte la stessa sRGB primari.
- Ha un valore approssimativo Gamma 2.2.
- Rappresenta le condizioni di illuminazione tipico (D65).
- Supporta i valori negativi e valori maggiore di uno (1).

Per consentire per i valori minori di zero e maggiore di uno, sRGB intervallo esteso che spazio colore consente non solo per le app esistenti presenti colori sRGB senza riduzioni delle prestazioni o la distorsione, ma consente lo spazio colore rappresentare qualsiasi colore all'interno di visibile spettro. Tutto ciò avviene mantenendo comunque gli stessi punti di ancoraggio come spazio colore sRGB.

### <a name="extended-range-srgb-in-action"></a>SRGB intervallo esteso in azione

Per visualizzare il funzionano di valori di fuori di zero e uno in spazio di colore sRGB intervallo esteso, eseguire l'esempio seguente del di rosso più saturo disponibile nello spazio colore P3 di visualizzazione:

[![](wide-color-images/color05.png "Modalità di funzionamento di valori di fuori di zero e uno in intervallo esteso sRGB spazio colore")](wide-color-images/color05.png#lightbox)

In visualizzazione P3, questo colore viene rappresentato come `[1.0, 0.0, 0.0]` e di intervallo esteso sRGB sarebbe `[1.358, -0.074, -0.012]`. Poiché valori sRGB sono pieni contenuti all'interno di visualizzazione P3 e i valori di visualizzazione P3 disporre "esterno" degli intervalli di sRGB.

Per informazioni su hardware fisico che ammette i valori pixel passare da extreme positivo a valori negativi estremi, può visualizzare qualsiasi colore nello spettro visibile disponibile e questi valori possono essere rappresentati in intervallo esteso sRGB spazio colore.

### <a name="device-pixel-formats"></a>Formati di Pixel di dispositivo 

Standardizzare sRGB spazio colore è stato in gran parte utilizzando un formato Pixel a 8 bit, poiché a 8 bit per canale di colore sono in genere sufficiente per descrivere i colori in sRGB. Questo non è perfetto, ma sufficientemente valida, e offre un buon compromesso tra l'utilizzo di memoria e processore di visualizzare immagini.

La visualizzazione P3 rappresentano le coordinate di colori di fuori dello spazio colore sRGB, pertanto richiede 16-bit per canale di colore per rappresentare correttamente i colori con intervallo esteso sRGB spazio colore.

## <a name="system-wide-wide-color-support"></a>Supporto del colore Wide a livello di sistema

Per supportare completamente colori ampia e ampia gamma di all'interno di iOS 10 e macOS Sierra, Apple ha esteso il Framework per sfruttare completo di spazio colore sRGB intervallo esteso e visualizzazione P3 seguenti:

- UIKit (solo per iOS)
- SceneKit
- Grafica di base
- ImageIO
- Immagine di base
- WebKit
- SpriteKit
- Animazione di base
- AppKit (per macOS)

Inoltre, Display Retina supporto è stato migliorato per intervallo esteso sRGB spazio colore e visualizzazione P3 Visualizza.

Colore Wide è supportata e può essere usato in tipi di contenuto applicazione seguenti:

- Risorse immagine statica incluse nel bundle di app.
- Documento e basati sulla rete risorse immagine.
- Supporto avanzato, ad esempio Live foto o immagini in formato non ELABORATO.
- Immagini di trama shader grafica 3D.

## <a name="solving-the-color-problem"></a>Per risolvere il problema di colore

Il contenuto visualizzato in un'applicazione può provenire da un'ampia gamma di origini e ricco di colore. Inoltre, è possibile visualizzare questo contenuto su una vasta gamma di dispositivi, ognuno con alla propria gamma di funzionalità di visualizzazione di colori.

Un'app iOS 10 colma la differenza tra questi due problemi utilizzando il nuovo sistema predefinite, colore gestire System. Questo sistema garantisce che lo stesso aspetto su qualsiasi dispositivo iOS, indipendentemente da quale spazio colore l'immagine è stata codificata in un'immagine.

Gestione colori inizia con tutte le immagini con un spazio colore associato (o profilo colori). Queste informazioni vengono utilizzate nel _il processo di corrispondenza dei colori_ in cui vengono confrontati i colori dell'immagine di origine per i colori nel dispositivo di output. Poiché ogni pixel dell'immagine deve essere di colore corrispondente, può richiedere molto tempo ed esaurire al momento della CPU del dispositivo.

A causa della natura del _il processo di corrispondenza dei colori_, questa conversione può essere potenzialmente "perdita di dati" se il dispositivo di output è una gamma più piccola rispetto all'immagine di origine.

Fortunatamente, i calcoli che costituiscono il _il processo di corrispondenza dei colori_ può facilmente essere con accelerazione hardware (sia per la CPU o GPU) e Apple assicura che il funzionamento automaticamente tramite la compilazione di supporto nei sistemi di base, ad esempio quarzo 2D, ColorSync e animazione Core. Per il contenuto in modo corretto con tag, richiedere codice non è necessario per sfruttare queste funzionalità.

Gestione colori è stato introdotto in ciascuna piattaforma, come indicato di seguito:

- **macOS** -macOS è stato gestito dall'inizio del colore.
- **iOS** -iOS è supportata la gestione automatica del colore da iOS 9.3 (e versioni successive).

### <a name="designing-for-wide-gamut"></a>Progettazione della vasta gamma di colori

Apple ha i seguenti suggerimenti per la progettazione e utilizzando wide colore, il contenuto dell'immagine ampia gamma di App iOS e Mac OS:

- Utilizzare solo contenuto ampia gamma di colori in cui verificare rilevare nell'app, non deve automaticamente essere utilizzate ovunque.
- Solo utilizzare contenuto ampia gamma di dove colori miglioreranno l'esperienza utente.
- In non è necessario modificare tutto il contenuto a P3 per le app esistenti.

Toolchain Apple rende il supporto per il contenuto dell'immagine ampia gamma una graduale acconsentire esplicitamente, pertanto colore "wide" di supporto in un'applicazione non è una situazione radicale.

### <a name="upgrading-existing-content-to-wide-color"></a>L'aggiornamento contenuto esistente a colori ampia

Apple ha i seguenti suggerimenti per l'aggiornamento di contenuto immagine esistente a colori ampia:

- Non appena "assegnare" un profilo P3 al contenuto dell'immagine, la modifica di app. In questo modo verrà semplicemente modificare il mapping del colore esistente contenuto nel nuovo spazio colore con risultati imprevisti, ad esempio l'estensione i colori in modo da adattarsi al nuovo spazio in questo modo la modifica dell'immagine.
- Sarà necessario il contenuto dell'immagine di "convertire" al profilo P3 visualizzato usando un'immagine della modifica di app.

### <a name="file-formats-and-color-profiles"></a>Formati di file e i profili di colore

Apple ha i seguenti suggerimenti per i formati di file e i profili di colore utilizzati nel contenuto di immagine colori ampia dell'app:

- Utilizzare il profilo colori "Visualizzazione P3" per gli spazi di lavoro RGB.
- Utilizzare i 16 bit per ogni modalità di colore del canale.
- Utilizzare un iMac 2015 in ritardo (o versione successiva) per visualizzare in anteprima in modo accurato il contenuto dell'immagine.
- Esportare gli asset delle immagini come file PNG a 16 bit con un profilo di "Visualizzazione P3" ICC incorporato.

> [!IMPORTANT]
> Utilizzando il **salvare per il Web** o **esportare asset** funzionalità disponibili in modifica software più diffusi immagine _non_ funzionano per le immagini ampia gamma di colori poiché queste funzionalità non sono stati ancora aggiornato per supportare le specifiche di formato di file richiesto.

### <a name="supporting-wide-color-with-asset-catalogs"></a>Supporto di colori ampia Asset cataloghi

In iOS 10 e macOS Sierra, Apple ha espanso i cataloghi Asset consentono di includere e classificazione di contenuto immagine statica nel bundle dell'app per supportare colori ampia.

Utilizzando Asset cataloghi offrono i vantaggi seguenti a un'app:

- Forniscono la migliore opzione di distribuzione per asset immagine statica.
- Supportano la correzione automatica del colore.
- Supportano l'ottimizzazione di formato di pixel automatico.
- Supportano App sezionamento e App assottigliamento che garantisce che solo il contenuto che è pertinente get recapitato al dispositivo dell'utente finale.

Apple ha i seguenti miglioramenti ai cataloghi Asset per il supporto di colore wide:

- Supportano il contenuto di origine a 16 bit (per il canale di colore).
- Supportano contenuto catalogazione per la gamma di visualizzazione. Il contenuto può essere contrassegnato per sRGB o gamme P3 di visualizzazione.

Lo sviluppatore dispone di tre opzioni per il supporto dei colori wide nelle proprie App:

1. **Non eseguire alcuna operazione** -poiché il contenuto di colori ampia deve essere utilizzato solo in situazioni in cui l'app deve visualizzare colori (in cui si migliorerà l'esperienza dell'utente), il contenuto di fuori di questo requisito deve essere lasciato come-è. Continuerà a essere visualizzati correttamente in tutte le situazioni di hardware.
2. **Aggiornare il contenuto esistente a visualizzazione P3** -questa operazione richiede allo sviluppatore di sostituire il contenuto dell'immagine esistente nel loro catalogo di Asset con un nuovo file nel formato P3 aggiornato e contrassegnarlo come tale nell'Editor di risorse. In fase di compilazione, verrà generata un'immagine di sRGB derivati da queste risorse.
3. **Fornire con ottimizzazione per la risorsa contenuto** -In questo caso, lo sviluppatore fornirà un sRGB 8 bit sia una visione P3 visualizzazione a 16 bit di ogni immagine nel catalogo di Asset (con codifica in modo corretto nell'editor di risorse).

### <a name="asset-catalog-deployment"></a>Distribuzione di catalogo di asset

Di seguito si verifica quando lo sviluppatore invia un'app nell'archivio di App con i cataloghi di Asset che includono il contenuto dell'immagine di colore wide:

- Quando l'app viene distribuito all'utente finale, il sezionamento App garantisce che solo la variante di contenuto appropriata viene recapitata al dispositivo dell'utente.
- Sul dispositivo che non supportano i colori ampia, non è senza alcun costo di payload per l'inclusione colori ampia, non è stato fornito al dispositivo.
- `NSImage` in macOS Sierra (e versioni successive) verranno selezionati automaticamente la migliore rappresentazione del contenuto per la visualizzazione dell'hardware.
- Il contenuto visualizzato verrà aggiornato automaticamente quando o se l'hardware di dispositivi Visualizza caratteristiche modifica.

### <a name="asset-catalog-storage"></a>Archiviazione di catalogo di asset

Archiviazione di catalogo di asset ha le proprietà e le implicazioni per un'app seguenti:

- In fase di compilazione Apple tenta di ottimizzare l'archiviazione del contenuto immagine tramite le conversioni di immagine di alta qualità.
- 16-bit vengono utilizzati per il canale di colore per il contenuto di colori ampia.
- La compressione di contenuto immagine dipendente viene utilizzata per ridurre le dimensioni del contenuto del risultato finale. Nuovo compressione "perdita di dati" sono state aggiunte per ottimizzare ulteriormente le dimensioni del contenuto.

## <a name="colors-in-user-interfaces"></a>Colori nelle interfacce utente

Quando si lavora con i colori in un'interfaccia utente, la maggior parte dei pixel sullo schermo è un colore a tinta unita. Inoltre, la maggior parte di questi pixel non provengono da asset di immagini, ma vengono disegnata direttamente dall'app (o dal sistema operativo per conto dell'applicazione).

Colore ampia gamma può presentare criticità diversi quando si lavora a livello di interfaccia utente:

- **Comunicazione colori** : durante la comunicazione con i colori tra le finestre di progettazione e gli sviluppatori in genere un _presuppone_ sRGB spazio colore coinvolti. Pertanto un colore può essere comunicato come `rgb(128, 45, 56)` o `#FF0456`. In progettazione una vasta gamma di colori, queste rappresentazioni non forniscono informazioni sufficienti per rappresentare accuratamente il colore specificato, il lavoro spazio colore deve essere incluso. Apple consiglia di usare `P3(128, 45, 56)` e `P3#FF0456` invece. 
- **Selezione colori** - la maggior parte dell'immagine comuni la modifica e la progettazione software subisce le stesse limitazioni di spazio colore sRGB quando si utilizzano i controlli di selezione di colori predefinita. La finestra di progettazione deve assicurarsi che siano nello spazio colore "Visualizzazione P3" la selezione colori quando si lavora con le strutture di colori ampia.
- **Codifica a colori** - entrambi `NSColor` (macOS) e `UIColor` (iOS & tvOS) hanno nuovi metodi pratici per la generazione di colori P3 direttamente ed entrambi sono state estese per supportare i colori in intervallo esteso sRGB spazio colore anche.
- **Memorizzazione dei colori** -prestare attenzione durante l'archiviazione ampia gamma di colori nel documento di un'app. Dove a 8 bit per canale di colore funzionato correttamente per lo spazio colore sRGB, a 16 bit per canale di colore da utilizzare per i colori wide. Lo sviluppatore deve anche controllare le istanze di spazio di colore assunto (poiché tutto ciò che è in genere solo sRGB).

## <a name="colors-on-the-web"></a>Colori sul Web

Deve prestare attenzione quando si lavora con colori ampia nelle pagine web e nei dispositivi che supportano la visualizzazione a colori wide. Se tutto il contenuto di immagine che è stato incluso nel sito Web è stata contrassegnata in modo appropriato, iOS e macOS verrà automaticamente corrispondenza dei colori e di visualizzarli correttamente.

Sono disponibili per facilitare la risoluzione delle risorse tra i dispositivi in grado di supportare P3 e sRGB anche le query di supporto:

```xml
<picture>
    <source srcset="monkey-p3.jpg" media="(color-gamut: p3)">
    <source srcset="monkey-rpg.jpg">
</picture>
```

Apple ha anche una proposta di WebKit che consentirà di CSS a essere specificati in altri spazi colore oltre lo spazio sRGB assunto.

## <a name="rendering-off-screen-images-in-app"></a>Rendering di immagini esterno allo schermo nell'App

In base al tipo di app viene creata, è possibile che l'utente da includere il contenuto dell'immagine vengono raccolti da internet o creare il contenuto dell'immagine direttamente all'interno dell'applicazione (ad esempio un disegno vettoriale app, ad esempio).

In entrambi i casi, l'app può eseguire il rendering di immagini necessarie esterno allo schermo wide colori utilizzo delle funzionalità avanzate aggiunta iOS sia macOS.

### <a name="drawing-wide-color-in-ios"></a>Disegno di colori ampia in iOS

Prima di illustrare come disegnare correttamente un'immagine di colori ampia in iOS 10, esaminare il codice di disegno iOS comuni seguenti:

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

Si verificano problemi con il codice standard che dovranno essere risolti _prima_ e può essere utilizzato per disegnare un'immagine di colori ampia. Il `UIGraphics.BeginImageContext (size)` metodo utilizzato per avviare il disegno di immagini iOS presenta le limitazioni seguenti:

- Impossibile creare contesti di immagine con più di 8 bit per canale di colore.
- Non può rappresentare i colori in sRGB intervallo esteso spazio colore.
- Non ha la possibilità di fornire un'interfaccia per creare contesti in uno spazio colore non sRGB a causa di routine di basso livello C la chiamata in background.

Per gestire queste limitazioni di disegnare un'immagine di colori ampia in iOS 10, utilizzare invece il codice seguente:

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

Il nuovo `UIGraphicsImageRenderer` classe crea un nuovo contesto di immagine che è in grado di gestire lo spazio colore sRGB intervallo esteso e ha le caratteristiche seguenti:

- È completamente gestiti per impostazione predefinita.
- Supporta sRGB intervallo esteso spazio colore per impostazione predefinita.
- Decide se è necessario eseguire il rendering nella sRGB o intervallo esteso sRGB spazio colore in base alle funzionalità del dispositivo iOS che è in esecuzione l'app.
- Completamente e automaticamente gestisce il contesto di immagine (`CGContext`) durata, in modo lo sviluppatore non è necessario chiamare il metodo di inizio e fine di comandi del contesto.
- È compatibile con il `UIGraphics.GetCurrentContext()` metodo.

Il `CreateImage` metodo il `UIGraphicsImageRenderer` classe per creare un'immagine di colori ampia e viene passata un gestore di completamento con il contesto di immagine in cui disegnare. Tutto il disegno viene eseguita all'interno di questo gestore di completamento come indicato di seguito:

- Il `UIColor.FromDisplayP3` metodo crea un nuovo colore rosso completamente saturo l'ampia gamma di spazio di visualizzazione P3 colore e viene utilizzato per disegnare la prima metà del rettangolo. 
- La seconda metà del rettangolo viene disegnato in sRGB normale completamente saturazione colore rosso per il confronto.

### <a name="drawing-wide-color-in-macos"></a>Disegno di colore Wide macOS

La `NSImage` classe è stata espansa nel macOS Sierra per supportare il disegno di immagini a colori Wide. Ad esempio:

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

## <a name="rendering-on-screen-images-in-app"></a>Il rendering sullo schermo immagini in App

Per eseguire il rendering di immagini a colori larghezza su schermo, il processo è simile alla creazione di un'immagine di colori ampia fuori dello schermo per macOS e iOS presentato in precedenza.

### <a name="rendering-on-screen-in-ios"></a>Il rendering sullo schermo in iOS

Quando l'app deve eseguire il rendering di un'immagine in wide colore sullo schermo in iOS, eseguire l'override di `Draw` metodo il `UIView` in questione come al solito. Ad esempio:

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

Come iOS 10 con il `UIGraphicsImageRenderer` classe illustrato in precedenza, decide se è necessario eseguire il rendering nella sRGB o intervallo esteso sRGB spazio colore in base alle funzionalità del dispositivo iOS che viene eseguita l'app quando il `Draw` metodo viene chiamato. Inoltre, il `UIImageView` è stato gestito da iOS 9.3 colori.

Se l'app deve sapere come viene eseguito per il rendering in un `UIView` o `UIViewController`, è possibile verificare il nuovo `DisplayGamut` proprietà la `UITraitCollection` classe. Questo valore sarà una `UIDisplayGamut` enum delle operazioni seguenti:

- P3
- SRGB
- Non specificato

Se l'applicazione è richiesto il controllo spazio colore utilizzato per disegnare un'immagine, è possibile utilizzare un nuovo `ContentsFormat` proprietà del `CALayer` per specificare lo spazio colore desiderato. Questo valore può essere un `CAContentsFormat` enum delle operazioni seguenti:

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>Il rendering sullo schermo in macOS

Quando l'app deve eseguire il rendering di un'immagine in colore larghezza su schermo macOS, eseguire l'override di `DrawRect` metodo il `NSView` in questione come al solito. Ad esempio:

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

Nuovamente, in modo intelligente decide se è necessario eseguire il rendering nella sRGB o intervallo esteso sRGB spazio colore in base alle funzionalità dell'hardware Mac che l'app è in esecuzione al momento il `DrawRect` metodo viene chiamato.

Se l'applicazione è richiesto il controllo spazio colore utilizzato per disegnare un'immagine, è possibile utilizzare un nuovo `DepthLimit` proprietà la `NSWindow` classe per specificare lo spazio colore desiderato. Questo valore può essere un `NSWindowDepth` enum delle operazioni seguenti:

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati colori ampia e le modalità che può essere implementato e utilizzato all'interno di un'app xamarin o Xamarin.Mac.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
