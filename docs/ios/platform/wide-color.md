---
title: Ampia gamma di colori in xamarin. IOS
description: Questo documento illustra l'ampia gamma di colori e come può essere usato in un'app xamarin. IOS o xamarin. Mac. Fornisce inoltre una panoramica generale di molti concetti importanti correlate al colore, ad esempio gli spazi colore, canali e repliche primarie.
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: f139bcceda12752e43a3a8330fa0a0e038e539f9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61423563"
---
# <a name="wide-color-in-xamarinios"></a>Ampia gamma di colori in xamarin. IOS

_Questo articolo illustra l'ampia gamma di colori e come può essere usato in un'app xamarin. IOS o xamarin. Mac._

iOS 10 e macOS Sierra migliora il supporto per formati pixel intervalli estesi e gli spazi colore a livello di gamma in tutto il sistema, tra cui Framework quali Core Graphics, immagine di base, bare Metal e AVFoundation. Supporto per i dispositivi dotati di schermi di ampia gamma di colori viene diminuito ulteriormente fornendo questo comportamento in tutto lo stack di grafica intero.

## <a name="about-wide-color"></a>Sull'ampia gamma di colori

Come indicato in precedenza, iOS 10 e macOS Sierra migliora il supporto per formati pixel intervalli estesi e gli spazi colore a livello di gamma in tutto il sistema, tra cui Framework quali Core Graphics, immagine di base, bare Metal e AVFoundation. Supporto per i dispositivi dotati di schermi di ampia gamma di colori viene diminuito ulteriormente fornendo questo comportamento in tutto lo stack di grafica intero.

Nel misero Apple creato ColorSync per gestire i colori di elaborazione nel Mac. Sono anche aiutati trovato International Color Consortium (ICC) per creare e alzare di livello un set di standard per la gestione di colore sull'hardware del computer. Lavoro di Apple con ICC era incluso nel ColorSync ed è stata compilata in base di OS X (ora denominato macOS).

Apple è stata apportata anche assicurarle una posizione di tecnologia di visualizzazione con l'hardware, ad esempio il Retina di visualizzazione, il nuovo visualizzare P3 e visualizzare P3 dello spazio colore (rilasciato nel iMac nel 2015) e il TrueTone consente di visualizzare i professionisti di iPad, iPhone 7 e iPhone 7 Plus.

Con l'ampia gamma di colori in macOS Sierra e iOS 10, Apple sta cambiando il modo in cui sia macOS e iOS gestiscono colore per sfruttare al meglio queste nuove tecnologie di visualizzazione.

## <a name="core-color-concepts"></a>Concetti di colore

Tenere in considerazione prima di portare un approfondimento sul colore in macOS e iOS i concetti di colore di base seguenti:

### <a name="color-space"></a>Spazio colore

Uno spazio dei colori è un ambiente in cui i colori possono essere rappresentati e confrontati. Può essere uno spazio dimensionale da una a quattro definito dall'intensità dei relativi componenti di colore. 

[![](wide-color-images/color00.png "Uno spazio dei colori")](wide-color-images/color00.png#lightbox)

### <a name="color-channels"></a>I canali di colore

I componenti di colore possono essere indicati anche come i canali di colore. Alcune rappresentazioni familiare sarebbe il RGB spazi, spazi di grigio, CMYK, spazi o spazi indipendente dal dispositivo. 

[![](wide-color-images/color02.png "I componenti di colore possono anche essere indicati come i canali di colore")](wide-color-images/color02.png#lightbox)

### <a name="color-primaries"></a>Colori primari

Colori primari forniscono il sistema di coordinate che viene usato per confrontare e calcolare i colori. Colori primari rientrano in genere più intenso è versione del colore specificato che può essere generato all'interno del canale di colore.

[![](wide-color-images/color01.png "Colori primari forniscono il sistema di coordinate che viene usato per confrontare e calcolare i colori")](wide-color-images/color01.png#lightbox)

Nel caso lo spazio di colore RGB rappresentato in precedenza, i colori primari siano dove il `1.0` coordinate sono ancorate (ad esempio `[1.0, 0.0, 0.0]` per red).

### <a name="color-gamut"></a>Gamma di colori

Gamma di colori fa riferimento a tutti i colori che possono essere definiti come una combinazione dei canali di colore singolo all'interno di un assegnare allo spazio colore.

[![](wide-color-images/color03.png "Esempio di gamma di colori")](wide-color-images/color03.png#lightbox)

## <a name="what-is-wide-color"></a>Che cos'è l'ampia gamma di colori

Prima di analizzare l'argomento dell'ampia gamma di colori, occorre tenere presenti una discussione sulle corrente standard del settore per colore, lo spazio di colore RGB Standard (sRGB). È lo spazio colore più ampiamente usati nei sistemi informatici oggi e lo spazio colore predefinito per iOS e macOS.

Spazio dei colori di sRGB presenta le proprietà seguenti:

- È basato sullo standard BT. 709 ITU-R.
- Ha un valore approssimativo Gamma è 2.2.
- Rappresenta le condizioni di illuminazione tipico (D65).

Poiché sRGB è ampiamente che usato nel settore, uno sviluppatore può stabilire alcuni presupposti che il colore specificato sarà rappresentato fedelmente su qualsiasi dispositivo che viene visualizzato nella. Tuttavia, questo potrebbe non essere sempre il caso. Esistono inoltre diversi colori che non rientrano nella spazio dei colori sRGB e di conseguenza, non possono essere rappresentati in esso.

Ad esempio, molti tessile sono progettati con thread usano molte inchiostri e tinture che rientrano all'esterno di sRGB. Anche molti prodotti che rileva una persona nella loro vita quotidiana vengono creati con i colori brillanti, vividi che non rientrano spazio colori sRGB. Alcuni degli esempi più interessanti di colori che non possono essere rappresentati in sRGB sono aspetti in natura, come i tramonti, in autunno, flowers originali e tropicali terreno.

Gli utenti che hanno stato acquisizione di immagini digitali in formato non ELABORATO possono avere immagini nei dispositivi che contengono tutti i dati colore, anche se si non può essere rappresentato correttamente in spazio colori sRGB e, pertanto, non può essere visualizzato correttamente nella schermata.

### <a name="the-display-p3-color-space"></a>Lo spazio di colore Display P3

2015, Apple ha rilasciato i nuovi prodotti (iMac e iPad Pro 9,7") che forniscono il nuovo spazio di colore Display P3 per gestire i problemi creati da spazio colori sRGB.

[![](wide-color-images/color04.png "Il nuovo spazio di colore Display P3")](wide-color-images/color04.png#lightbox)

Lo spazio di colore Display P3 presenta le proprietà seguenti:

- Supporta uno spazio di ampia gamma di colori per le piattaforme hardware moderni.
- Basato sullo standard DCI SMPTE-P3. DCI P3 è stata progettata per i proiettori digitali, ma è stato modificato da Apple per supportare i monitor.
- Ha un valore approssimativo Gamma è 2.2.
- Rappresenta le condizioni di illuminazione tipico (D65).

In base a Apple, gli utenti spostano i flussi di lavoro per le proprie piattaforme per dispositivi mobili. Risolvere i problemi di presentazione di colore presentati per i dispositivi mobili professionali (iPad Pro), richiesta anche più della semplice visualizzazione un'ampia gamma di colori di sRGB. Una delle soluzioni era per aggiornare la calibrazione factory, in modo che ciascun singolo dispositivo è stato tarato di garantire una factory che dal dispositivo a altro, visualizzazione a colori sia accurate e coerenti.

Un'altra soluzione, è l'inclusione di gestione dei colori di sistema completo, che Apple ha integrato in iOS 10 e macOS Sierra. 

### <a name="the-extended-range-srgb-color-space"></a>Extended Range sRGB spazio colore

La nuova gestione colori a livello di sistema iOS 10 ha per conto di tutte le app iOS esistenti che vengono compilate e ottimizzare per sRGB. È stato progettato per garantire che entrambi prestazioni di app o rappresentazione di colore di queste App esistenti che non è stata sul.

Per gestire questa situazione, Apple ha inserito il Extended Range sRGB, lo spazio colore in iOS 10 (e anche macOS Sierra).

Extended Range sRGB spazio colore presenta le proprietà seguenti:

- Include tutte le repliche primarie di sRGB stesso.
- Ha un valore approssimativo Gamma è 2.2.
- Rappresenta le condizioni di illuminazione tipico (D65).
- Supporta i valori negativi e valori superiore a uno (1).

Per consentendo la valori minori di zero e maggiore di uno spazio colore consente non solo per le app esistenti per presenti colori di sRGB senza impatto sulle prestazioni o una distorsione, mentre il Extended Range sRGB consente lo spazio colore rappresentare qualsiasi colore all'interno di visibile spettro. Tutto ciò avviene garantendo nel contempo gli stessi punti di ancoraggio come spazio dei colori sRGB.

### <a name="extended-range-srgb-in-action"></a>Extended Range sRGB in azione

Per visualizzare il funzionamento valori di fuori di zero e uno in Extended Range sRGB spazio colore, considerare di esempio seguente il di rosso massima saturazione disponibile nello spazio colore Display P3:

[![](wide-color-images/color05.png "Modalità di funzionamento di valori di fuori di zero e uno in Extended Range sRGB spazio colore")](wide-color-images/color05.png#lightbox)

Nella finestra Display P3, questo colore viene rappresentato come `[1.0, 0.0, 0.0]` ed Extended Range sRGB sarebbe `[1.358, -0.074, -0.012]`. Poiché i valori di sRGB sono pieni indipendente all'interno di Display P3 e i valori Display P3 layout "esterno" degli intervalli di sRGB.

Per l'hardware fisico che ammette i valori di pixel passare da extreme positivo a valori negativi estremi, può visualizzare qualsiasi colore nello spettro visibile disponibile e questi valori possono essere rappresentati in Extended Range sRGB spazio colore.

### <a name="device-pixel-formats"></a>Formati Pixel dispositivo 

SRGB è stato in gran parte dello spazio colore standardizzare utilizzando un formato di Pixel a 8 bit, perché è di 8 bit per canale di colore per lo più sufficiente per descrivere i colori di sRGB. Ciò non è perfetto, ma sufficientemente valida, e offre un buon compromesso tra l'utilizzo della memoria e processore per visualizzare le immagini.

Poiché rappresentano le coordinate di colore all'esterno di spazio dei colori sRGB Display P3, è necessario 16-bit per canale di colore per rappresentare correttamente i colori con Extended Range sRGB spazio colore.

## <a name="system-wide-wide-color-support"></a>Supporto a livello di sistema ampia gamma di colori

Per supportare completamente l'ampia gamma di colori e ampia gamma di all'interno di iOS 10 e macOS Sierra, Apple ha esteso i framework seguenti per sfruttare al meglio di Extended Range sRGB spazio colore e Display P3:

- UIKit (solo per iOS)
- SceneKit
- Core Graphics
- ImageIO
- Immagine di base
- WebKit
- SpriteKit
- Core Animation
- AppKit (per macOS)

Inoltre, Display Retina supporto è stato migliorato per lo spazio colore di Extended Range sRGB e Display P3 viene visualizzato.

Ampia gamma di colori è supportata e può essere usato nei seguenti tipi di contenuto dell'applicazione:

- Risorse immagini statiche incluse nel bundle dell'app.
- Documento e basati sulla rete le risorse immagine.
- Supporto avanzato, ad esempio Live foto o immagini in formato non ELABORATO.
- Immagini texture shader grafica 3D.

## <a name="solving-the-color-problem"></a>Risoluzione dei problemi di colore

Il contenuto visualizzato in un'app può provenire da un'ampia gamma di origini ricchi di colore. Inoltre, questo contenuto può essere visualizzato su una vasta gamma di dispositivi, ognuno con i propri gamma di funzionalità di colore dello schermo.

Un'app per iOS 10 colma la differenza tra questi due problemi utilizzando il nuovo incorporati, a livello di sistema colore gestire sistema. Questo sistema garantisce che lo stesso aspetto su tutti i dispositivi iOS, indipendentemente da quale spazio colore l'immagine è stata codificata in un'immagine.

Gestione dei colori viene avviata con tutte le immagini con un spazio colore associato (o profilo colori). Queste informazioni vengono utilizzate nel _processo di corrispondenza del colore_ in cui vengono confrontati i colori usati nell'immagine di origine per i colori nella periferica di output. Poiché ogni pixel nell'immagine deve essere di colore corrispondente, può richiedere molto tempo ed esaurire al momento della CPU del dispositivo.

A causa della natura del _processo di corrispondenza del colore_, questa conversione può essere potenzialmente "perdita di dati" se il dispositivo di output ha una gamma più piccola rispetto all'immagine di origine.

Per fortuna, i calcoli in cui vengono inseriti i _processo di corrispondenza del colore_ può facilmente essere con accelerazione hardware (sia per la CPU o GPU) e Apple assicura che funziona automaticamente mediante la generazione di supporto in sistemi di base, ad esempio Quartz 2D, ColorSync e animazione di base. Per il contenuto contrassegnato in modo corretto, nessun tipo di codifica è necessario per sfruttare i vantaggi di queste funzionalità.

Gestione dei colori è supportata in tutte le piattaforme come indicato di seguito:

- **macOS** -macOS è stato gestito dall'inizio di colore.
- **iOS** -iOS è supportata la gestione automatica dei colori poiché iOS 9.3 (e versioni successive).

### <a name="designing-for-wide-gamut"></a>Progettazione della vasta gamma di colori

Apple ha il suggerimento seguente per la progettazione e utilizzando a livello dei colori, contenuto immagine vasta gamma di App iOS e macOS:

- Usare solo il contenuto di vasta gamma la posizione della marca rilevare nell'app, non dovrebbero automaticamente essere usati ovunque.
- Usare solo il contenuto di vasta gamma dove colori vivaci miglioreranno l'esperienza dell'utente.
- In non è necessario modificare tutti i contenuti a P3 per le app esistenti.

Toolchain di Apple rende il supporto per il contenuto dell'immagine di vasta gamma di colori una graduale acconsenti esplicitamente, in modo da supportare l'ampia gamma di colori in un'app non è una situazione di tipo tutto o niente.

### <a name="upgrading-existing-content-to-wide-color"></a>L'aggiornamento del contenuto esistente a ampia gamma di colori

Apple ha i seguenti suggerimenti per l'aggiornamento di contenuti immagine esistenti a ampia gamma di colori:

- Non appena "assegnare" un profilo P3 per il contenuto nell'immagine di app di modifica. In questo modo è sufficiente modificare il mapping del colore esistente contenuto al nuovo spazio di colore con risultati imprevisti, ad esempio l'estensione i colori per rientrare nel nuovo spazio di conseguenza modifica l'immagine.
- Il contenuto dell'immagine dovrà essere "convertiti" per il profilo Display P3 mediante un'app di modifica delle immagini.

### <a name="file-formats-and-color-profiles"></a>Formati di file e i profili di colore

Apple ha i seguenti suggerimenti per i formati di file e profili colori utilizzati nel contenuto di immagine ampia gamma di colori dell'app:

- Usare il profilo del colore "Display P3" per gli spazi di lavoro RGB.
- Usare 16 bit per ogni modalità del canale di colore.
- Usare un iMac Late 2015 (o versione successiva) per visualizzare in anteprima in modo accurato il contenuto di immagine.
- Esportare gli asset delle immagini come file PNG di 16 bit con un profilo "Display P3" ICC incorporato.

> [!IMPORTANT]
> Usando il **salvare per il Web** o **esportare gli asset** funzionalità disponibili nel software di modifica delle immagini più diffuse _non verrà_ funzionano per le immagini di ampia gamma di colori poiché queste funzionalità non sono stati ancora aggiornato per supportare le specifiche di formato di file richiesto.

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

## <a name="colors-in-user-interfaces"></a>Colori nelle interfacce utente

Quando si lavora con i colori in un'interfaccia utente, la maggior parte dei pixel sullo schermo è un colore a tinta unita. Inoltre, la maggior parte di questi pixel non provengono dagli asset di immagini, ma vengono disegnata direttamente dall'app (o dal sistema operativo per conto dell'app).

Colore di vasta gamma può presentare numerose sfide quando si lavora a livello di interfaccia utente:

- **Comunicazione colori** : quando si parla di colore tra progettisti e sviluppatori in genere un' _presuppone_ sRGB spazio colore coinvolti. Un colore può essere comunicato come `rgb(128, 45, 56)` o `#FF0456`. In progettazione una vasta gamma di colori, queste rappresentazioni non forniscono informazioni sufficienti per rappresentare accuratamente il colore specificato, l'utilizzo dello spazio colore deve essere inclusa. Suggerisce l'uso di Apple `P3(128, 45, 56)` e `P3#FF0456` invece. 
- **Selezione colori** : la maggior parte della progettazione software subisce le stesse limitazioni di spazio dei colori sRGB quando si usano le selezioni di colori predefiniti e modifica delle immagini popolari. La finestra di progettazione deve assicurarsi che siano nello spazio colore "Display P3" nella selezione colori quando si lavora con le strutture di ampia gamma di colori.
- **Codifica dei colori** - entrambe `NSColor` (macOS) e `UIColor` (iOS e tvOS) hanno nuovi metodi pratici per la generazione di colori P3 direttamente e sono state entrambe estese per supportare i colori nella Extended Range sRGB spazio colore anche.
- **L'archiviazione dei colori** -deve prestare attenzione durante l'archiviazione vasta gamma di colori nel documento di un'app. In cui 8 bit per canale di colore hanno avuto esito positivo per spazio dei colori sRGB, 16 bit per canale di colore da utilizzare per i colori di wide. Lo sviluppatore deve inoltre controllare le istanze di assunto lo spazio colore (quanto tutto ciò che era tradizionalmente sRGB solo).

## <a name="colors-on-the-web"></a>Colori sul Web

Dovrebbe prestare attenzione quando si lavora con l'ampia gamma di colori nelle pagine web e nei dispositivi che supportano la visualizzazione a colori wide. Se tutto il contenuto di immagine che include il sito Web è stata contrassegnata in modo appropriato, iOS e macOS verrà automaticamente corrispondenza dei colori e visualizzati correttamente.

Sono disponibili per risolvere gli asset tra dispositivi compatibili P3 e sRGB anche query sui supporti:

```xml
<picture>
    <source srcset="monkey-p3.jpg" media="(color-gamut: p3)">
    <source srcset="monkey-rpg.jpg">
</picture>
```

Apple ha anche una proposta di WebKit che consentirà di CSS essere specificato in altri spazi colore oltre lo spazio di sRGB assunto.

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

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato ampia gamma di colori e i modi che può essere implementato e utilizzata all'interno di un'app xamarin. IOS o xamarin. Mac.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
