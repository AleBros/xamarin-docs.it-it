---
title: Gestione di più risoluzioni in CocosSharp
description: Questa guida viene illustrato come lavorare con CocosSharp per sviluppare giochi che consentono di visualizzare correttamente nei dispositivi di risoluzioni diverse.
ms.prod: xamarin
ms.assetid: 859ABF98-2646-431A-A4A8-3E7E48DA5A43
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 6803dc2668b89ee2d037da8b34e202191dd5465d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61307743"
---
# <a name="handling-multiple-resolutions-in-cocossharp"></a>Gestione di più risoluzioni in CocosSharp

_Questa guida viene illustrato come lavorare con CocosSharp per sviluppare giochi che consentono di visualizzare correttamente nei dispositivi di risoluzioni diverse._

CocosSharp fornisce metodi per la standardizzazione con dimensioni di oggetti nel tuo gioco indipendentemente dal numero di pixel sullo schermo del dispositivo fisico. Tradizionalmente, giochi sviluppati per i PC e console per videogiochi è stato possibile per una risoluzione singola. I giochi moderni e in particolare i giochi per dispositivi mobili, devono essere compilati per supportare un'ampia gamma di dispositivi. Questa guida viene illustrato come standardizzare CocosSharp giochi indipendentemente dal fatto le caratteristiche di risoluzione dello schermo fisico.

Il comportamento di risoluzione predefinito di CocosSharp è la corrispondenza pixel fisici con le coordinate di gioco. La tabella seguente mostra i dispositivi come le varie visualizzerebbe sprite dello sfondo ambiente con larghezza e altezza del 368 x 240. La prima riga è tecnicamente non un dispositivo reale, ma piuttosto il rendering previsto degli sprite, indipendentemente dalla risoluzione del dispositivo:


| **Dispositivo** | **Risoluzione dello schermo** | **Screenshot di esempio** |
|--- | --- |--- |
|Visualizzazione desiderata|368 x 240 (con barre nere per proporzioni)| ![368 x 240 (con barre nere per proporzioni)](resolutions-images/image1.png) |
|iPhone 4s|960x640| ![iPhone 4s 960x640](resolutions-images/image2.png) |
|iPhone 6 Plus|1920x1080| ![iPhone 6 Plus 1920x1080](resolutions-images/image3.png) |

Questo documento illustra come usare CocosSharp per risolvere il problema illustrato nella tabella precedente. Vale a dire, illustreremo come rendere tutti i dispositivi di eseguire il rendering come illustrato nella prima riga, indipendentemente dalla risoluzione dello schermo.


## <a name="working-with-setdesignresolutionsize"></a>Utilizzo di SetDesignResolutionSize

Il `CCScene` classe viene in genere utilizzata come contenitore radice per tutti gli oggetti visivi, ma fornisce anche un metodo statico denominato `SetDesignResolutionSize` per specificare la dimensione predefinita per tutte le scene. In altre parole `SetDesignResolutionSize` metodo consente agli sviluppatori di sviluppare giochi che verranno visualizzati correttamente in un'ampia gamma di risoluzioni di hardware. I modelli di progetto CocosSharp usano questo metodo per impostare le dimensioni predefinite del progetto su 1024 x 768, come illustrato nel codice seguente:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");
    CCSize windowSize = mainWindow.WindowSizeInPixels;
    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDesignResolutionSize (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    ...
```

È possibile modificare il `desiredWidth` e `desiredHeight` variabili precedenti per modificare la visualizzazione in modo da corrispondere la risoluzione desiderata del tuo gioco. Ad esempio, il codice sopra riportato può essere modificato come indicato di seguito per visualizzare lo sfondo 368 x 240 con schermo:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");
    CCSize windowSize = mainWindow.WindowSizeInPixels;
    float desiredWidth = 368.0f;
    float desiredHeight = 240.0f;
    
    // This will set the world bounds to(0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDesignResolutionSize (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    ...
```


## <a name="ccsceneresolutionpolicy"></a>CCSceneResolutionPolicy

`SetDesignResolutionSize` Consente di specificare come finestra del gioco viene regolata in base alla risoluzione desiderata. Le sezioni seguenti illustrano come viene visualizzata un'immagine di 500 x 500 con diversi `CCSceneResolutonPolicy` i valori passati al `SetDesignResolutionSize` (metodo). I valori seguenti vengono forniti dal `CCSceneResolutionPolicy` enum:

 - `ShowAll` : Visualizza la risoluzione richiesta intera, mantenendo le proporzioni.
 - `ExactFit` : Visualizza la risoluzione richiesta intera, ma non mantiene le proporzioni.
 - `FixedWidth` : Visualizza l'intera larghezza richiesta, mantenendo le proporzioni.
 - `FixedHeight` : Visualizza l'intera altezza richiesta, mantenendo le proporzioni.
 - `NoBorder` : Visualizza l'intera altezza o larghezza intera, mantenendo le proporzioni. Se le proporzioni del dispositivo è superiore alle proporzioni della risoluzione desiderata, viene visualizzata l'intera larghezza. Se le proporzioni del dispositivo è inferiore al rapporto di aspetto della risoluzione desiderata, viene visualizzata l'intera altezza.
 - `Custom` – La visualizzazione dipende il `Viewport` proprietà dell'oggetto corrente `CCScene`.

Tutte le schermate vengono prodotte iPhone 4s risoluzione (960 x 640) con orientamento orizzontale e usano questa immagine:

![](resolutions-images/image4.png "Tutte le schermate vengono prodotte iPhone 4s risoluzione di 960 x 640 con orientamento orizzontale e usano questa immagine")


### <a name="ccsceneresolutionpolicyshowall"></a>CCSceneResolutionPolicy.ShowAll

`ShowAll` Specifica che la risoluzione di gioco intera sarà visibile sullo schermo, ma può visualizzare *letterboxing* (barre nere) per regolare la proporzioni diverse. Questo criterio viene comunemente usato come garantisce che l'intera vista gioco verrà visualizzato sullo schermo senza distorsioni.

Esempio di codice:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

Letterboxing è visibile a sinistra e a destra dell'immagine per tenere conto delle proporzioni fisica in corso più ampio rispetto alla risoluzione desiderata:

![](resolutions-images/image5.png "Letterboxing è visibile a sinistra e a destra dell'immagine per tenere conto delle proporzioni fisica in corso più ampio rispetto alla risoluzione desiderata")


### <a name="ccsceneresolutionpolicyexactfit"></a>CCSceneResolutionPolicy.ExactFit

`ExactFit` Specifica che la risoluzione di gioco intera sarà visibile sullo schermo con nessun letterboxing. Area visualizzabile distorto (proporzioni potrebbe non essere mantenuta) in base alle proporzioni dell'hardware.

Esempio di codice:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ExactFit);
```

Nessun letterboxing è visibile, ma poiché la risoluzione del dispositivo è rettangolare è distorta la visualizzazione del gioco:

![](resolutions-images/image6.png "Nessun letterboxing è visibile, ma poiché la risoluzione del dispositivo è rettangolare è distorta la visualizzazione del gioco")


### <a name="ccsceneresolutionpolicyfixedwidth"></a>CCSceneResolutionPolicy.FixedWidth

`FixedWidth` Specifica che la larghezza della visualizzazione corrisponderà al valore di larghezza passato a `SetDesignResolutionSize`, ma l'altezza visualizzabile è soggette alle proporzioni del dispositivo fisico. Il valore di altezza passato a `SetDesignResolutionSize` viene ignorato dal momento in cui verrà calcolata in base alle proporzioni del dispositivo fisico fase di esecuzione. Ciò significa che l'altezza calcolata può essere minore dell'altezza desiderata (con conseguente parti della visualizzazione del gioco viene fuori schermo) o l'altezza calcolata può essere maggiore rispetto all'altezza desiderata (che comporta più la visualizzazione del gioco). Poiché questo può comportare più del gioco viene visualizzato, quindi potrebbe sembrare come se si è verificato letterboxing; Tuttavia, lo spazio aggiuntivo non necessariamente sarà nero se viene visualizzato qualsiasi oggetto visivo. 

Esempio di codice:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedWidth);
```

L'iPhone 4s presenta proporzioni 3:2, in modo che l'altezza calcolata è di circa 333 unità:

![](resolutions-images/image7.png "L'iPhone 4s con proporzioni 3:2, in modo che l'altezza calcolata è di circa 333 unità")


### <a name="ccsceneresolutionpolicyfixedheight"></a>CCSceneResolutionPolicy.FixedHeight

Concettualmente `FixedHeight` si comporta in modo analogo al `FixedWidth` – il gioco avverrà il valore di altezza passato a `SetDesignResolutionSize,` ma calcolerà la larghezza in fase di esecuzione in base di fuori della risoluzione fisica. Come accennato in precedenza, questo significa che la larghezza in visualizzato inferiori o superiori rispetto alla larghezza desiderata, risultante in parte del gioco in fase largo dello schermo o più del gioco viene visualizzato, rispettivamente.

Esempio di codice:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

Poiché lo screenshot seguente è stato creato con l'app in esecuzione in modalità orizzontale, la larghezza calcolata è superiore a 500: 750 in modo specifico. Il valore X 0 questo criterio mantiene allineato a sinistra, in modo che la risoluzione aggiuntiva è visualizzabile sul lato destro dello schermo.

![](resolutions-images/image8.png "Questo criterio mantiene il valore X 0 allineato a sinistra, in modo che la risoluzione aggiuntiva è visualizzabile sul lato destro dello schermo")


### <a name="ccsceneresolutionpolicynoborder"></a>CCSceneResolutionPolicy.NoBorder

`NoBorder` prova a visualizzare l'applicazione con nessun letterboxing, mantenendo le proporzioni originali (senza distorsione). Se le proporzioni della risoluzione richiesta corrisponde al rapporto di aspetto fisico del dispositivo, si verificherà alcun ridimensionamento. Se le proporzioni non corrispondono, quindi di ritaglio si verificherà.

Esempio di codice:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

La schermata riportata di seguito vengono visualizzate le parti superiore e inferiore dello schermo ridotto, mentre vengono visualizzati tutti i 500 pixel di larghezza di visualizzazione:

![](resolutions-images/image9.png "Questa schermata consente di visualizzare le parti superiore e inferiore dello schermo ridotto, mentre vengono visualizzati tutti i 500 pixel di larghezza di visualizzazione")


### <a name="ccsceneresolutionpolicycustom"></a>CCSceneResolutionPolicy.Custom

`Custom` consente a ognuno `CCScene` per specificare il proprio viewport personalizzato relativo alla risoluzione specificata nel `SetDesignResolutionSize`.

Definiscono le scene loro `Viewport` proprietà costruendo un `CCViewport`, che a sua volta, viene costruito con un `CCRect`. Per ulteriori informazioni sul `CCViewport` e `CCRect` vedere la [documentazione dell'API CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/). Nel contesto di creazione di un `CCViewport` il `CCRect` i parametri del costruttore specificano (in ordine):

 - x – il valore di offset orizzontale del viewport, in cui ogni unità rappresenta uno spessore di una schermata intera. Ad esempio, usando un valore dei risultati .5f nella scena spostata a destra della metà della larghezza dello schermo.
 - y: il valore di offset verticale del viewport, in cui ogni unità rappresenta un'altezza di schermo intero. Ad esempio, usando un valore dei risultati .5f nella scena spostata in basso di metà dell'altezza dello schermo.
 - Width: la parte orizzontale che deve occupare la scena. Ad esempio, usando un valore pari a 1 / 3.0f comporta la scena in senso orizzontale che occupa un terzo dello schermo.
 - Height: la parte verticale che deve occupare la scena. Ad esempio, usando un valore pari a 1 / 3.0f comporta la scena in senso verticale che occupa un terzo dello schermo.

Esempio di codice:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.Custom);
...
float horizontalDisplayPortion = 2 / 3.0f;
float verticalDisplayPortion = 1 / 2.0f;
float displayOffsetInScreenWidths = 0;
float displayOffsetInScreenHeights = .5f;
var rectangle = new CCRect (
    displayOffsetInScreenWidths, 
    displayOffsetInScreenHeights, 
    horizontalDisplayPortion, 
    verticalDisplayPortion);
scene.Viewport = new CCViewport (rectangle);
```

Il codice precedente produce quanto segue:

![](resolutions-images/image10.png "Il codice precedente comporta in questo screenshot")


## <a name="defaulttexeltocontentsizeratio"></a>DefaultTexelToContentSizeRatio

Il `DefaultTexelToContentSizeRatio` semplifica l'uso di trame con risoluzione superiore per i dispositivi dotati di schermi a risoluzione superiore. In particolare, questa proprietà consente di giochi per usare gli asset con risoluzione superiore senza dover per modificare le dimensioni o il posizionamento di elementi visivi. 

Ad esempio, un gioco può includere un asset di immagini per un carattere di gioco che è di 200 pixel di altezza e la schermata del gioco (come specificato da `SetDesignResolutionSize`) potrebbe essere 400 pixel di altezza. In questo caso, il carattere occupa la metà della schermata. Tuttavia, se un asset di pixel talla 400 sono stati usati per il carattere per i dispositivi con risoluzione superiore, il carattere verrebbe occupare l'intera altezza dello schermo. Se si desidera mantenere le stesse dimensioni per sfruttare i vantaggi dei dispositivi risoluzione superiore il carattere, codice aggiuntivo, è necessario mantenere sprite caratteri a metà dell'altezza dello schermo.

Nell'esempio viene presentata nell'esempio precedente rappresenta un problema comune nello sviluppo di giochi-aggiunta di asset di grandi dimensioni senza richiedere agli sviluppatori di eseguire il ridimensionamento su ogni elemento visivo in base alla risoluzione del dispositivo. `DefaultTexelToContentSizeRatio` è una proprietà globale utilizzata per ridimensionare gli elementi visivi. Questo valore viene ridimensionata tutti gli elementi visivi di un determinato tipo per il valore assegnato.

Questa proprietà è presente nelle seguenti classi: 

 - `CCApplication`
 - `CCSprite`
 - `CCLabelTtf`
 - `CCLabelBMFont`
 - `CCTMXLayer`

CocosSharp Visual Studio per il set di modelli Mac `CCSprite.DefaultTexelToContentSizeRatio` in base alla larghezza del dispositivo come un esempio di come può essere usato. Il codice seguente è contenuto `CCApplicationDelegate.ApplicationDidFinishLaunching`:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...
    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
           
    ...
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }
    ...           
}
```


### <a name="defaulttexeltocontentsizeratio-example"></a>Esempio DefaultTexelToContentSizeRatio

Per vedere come `DefaultTexelToContentSizeRatio` influisce sulle dimensioni dell'oggetto visivo elementi, si consideri il codice presentato in precedenza:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

Verrà visualizzato un risultato nell'immagine seguente con una trama di 500 x 500:

![](resolutions-images/image5.png "Verrà visualizzato un risultato in questa immagine usando una trama di 500 x 500")

IPad Retina esegue una risoluzione fisica di 2048 x 1536. Ciò significa che il gioco come visualizzato in precedenza potrebbe seriamente 500 pixel su pixel fisici 1536. Giochi possono sfruttare questa risoluzione aggiuntiva tramite la creazione di che cosa sono in genere detti *hd* asset – asset che vengono progettati per schermi a risoluzione superiore. Ad esempio, questo gioco potrebbe utilizzare una versione hd della trama ridimensionata a 1000 x 1000. Tuttavia, usando l'immagine più grande comporterebbe il `CCSprite` avere larghezza e altezza di 1000 unità. Poiché il gioco viene sempre visualizzato 500 unità (a causa dell'errore di `SetDesignResolutioSize` chiamare), nostro sprite 1000 x 1000 sarebbe troppo grande (consente di visualizzare solo la parte sinistra inferiore di esso):

![](resolutions-images/image11.png "Poiché il gioco viene sempre visualizzato 500 unità a causa della chiamata SetDesignResolutioSize, sprite 1000 x 1000 sarebbe troppo grande che consente di visualizzare solo la parte sinistra inferiore di esso")

È possibile impostare `CCSprite.DefaultTexelToContentSizeRatio` per tenere conto per la trama 1000 x 1000 da due volte più ampio e risulterà alta la trama 500 x 500 originale:


```csharp
CCSprite.DefaultTexelToContentSizeRatio = 2;
```

Ora se eseguiamo il gioco della trama 1000 x 1000 saranno completamente visibile:

![](resolutions-images/image12.png "Ora se eseguiamo il gioco della trama 1000 x 1000 saranno completamente visibile")


### <a name="defaulttexeltocontentsizeratio-details"></a>DefaultTexelToContentSizeRatio details

Il `DefaultTexelToContentSizeRatio` è di proprietà `static,` ovvero sprite tutte nell'applicazione condividerà lo stesso valore. L'approccio tipico per i giochi con Asset apportate per diverse risoluzioni deve contenere un set completo degli asset per ogni categoria di risoluzione. Per impostazione predefinita di CocosSharp Visual Studio per i modelli Mac forniscono **ld** e **hd** cartelle per gli asset, che potrebbe essere utile per i giochi che supportano due set di trame. Una cartella del contenuto di esempio con il contenuto potrebbe essere simile:

![](resolutions-images/image13.png "Una cartella del contenuto di esempio con il contenuto sarà simile a")

Si noti che il modello predefinito include anche codice da aggiungere in modo condizionale all'applicazione `ContentSearchPaths`:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }
    ...           
}
```

Ciò significa che l'applicazione eseguirà la ricerca per i file nei percorsi in base al fatto che venga eseguito in modalità di risoluzione regolare o ad alta risoluzione. Ad esempio, se il **hd** e **ld** cartelle contengono un file denominato **background.png** quindi il codice seguente sarebbe eseguire e selezionare il file corretto per la risoluzione:


```csharp
backgroundSprite  = new CCSprite ("background");
```


## <a name="summary"></a>Riepilogo

Questo articolo illustra come creare giochi che visualizzano correttamente indipendentemente dalla risoluzione del dispositivo. Esempi di utilizzo mostra diverse `CCSceneResolutionPolicy` valori per il ridimensionamento del gioco in base alla risoluzione del dispositivo. Fornisce inoltre un esempio di come `DefaultTexelToContentSizeRatio` può essere utilizzato per supportare più set di contenuto senza richiedere gli elementi visivi per il ridimensionamento singolarmente.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione di CocosSharp](~/graphics-games/cocossharp/index.md)
- [Documentazione API CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
