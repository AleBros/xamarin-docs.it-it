---
title: Gestione più risoluzioni in CocosSharp
description: Questa guida viene illustrato come utilizzare CocosSharp per lo sviluppo di giochi visualizzati correttamente nei dispositivi di risoluzioni diverse.
ms.prod: xamarin
ms.assetid: 859ABF98-2646-431A-A4A8-3E7E48DA5A43
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 4077af2351b8ab3ef718a71cc672add54b6ef05a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="handling-multiple-resolutions-in-cocossharp"></a>Gestione più risoluzioni in CocosSharp

_Questa guida viene illustrato come utilizzare CocosSharp per lo sviluppo di giochi visualizzati correttamente nei dispositivi di risoluzioni diverse._

CocosSharp fornisce metodi per la standardizzazione di dimensioni di oggetto del gioco indipendentemente dal numero di pixel sullo schermo del dispositivo fisico. Tradizionalmente, giochi sviluppati per i PC e console di gioco è possibile destinare una singola soluzione. Giochi moderni e soprattutto i giochi per dispositivi mobili, devono essere compilati per supportare un'ampia gamma di dispositivi. Questa guida viene illustrato come standardizzare CocosSharp giochi indipendentemente dal fatto le caratteristiche di risoluzione dello schermo fisico.

Il comportamento di risoluzione predefinito di CocosSharp è per trovare la corrispondenza con le coordinate di gioco pixel fisici. Nella tabella seguente viene illustrato come vari dispositivi renderebbe sprite ambiente sfondo con larghezza e altezza di 368 x 240. La prima riga è tecnicamente non un dispositivo reale, ma piuttosto il rendering previsto di sprite, indipendentemente dalla risoluzione di dispositivo:


| **Dispositivo** | **Risoluzione dello schermo** | **Schermata di esempio** |
|--- | --- |--- |
|Visualizzazione desiderato|368 x 240 (con barre nere per proporzioni)| ![368 x 240 (con barre nere per proporzioni)](resolutions-images/image1.png) |
|iPhone 4s|960x640| ![iPhone 4s 960x640](resolutions-images/image2.png) |
|iPhone 6 Plus|1920x1080| ![iPhone 6 Plus 1920x1080](resolutions-images/image3.png) |

Questo documento viene illustrato come utilizzare CocosSharp per risolvere il problema, illustrato nella tabella precedente. Vale a dire, trattati come rendere qualsiasi dispositivo di rendering, come illustrato nella prima riga, indipendentemente dalla risoluzione dello schermo.


## <a name="working-with-setdesignresolutionsize"></a>Utilizzo di SetDesignResolutionSize

Il `CCScene` classe viene in genere utilizzata come contenitore radice per tutti gli oggetti visivi, ma fornisce anche un metodo statico denominato `SetDesignResolutionSize` per specificare la dimensione predefinita per tutte le scene. In altre parole `SetDesignResolutionSize` metodo consente agli sviluppatori di sviluppare giochi che verranno visualizzati correttamente su una vasta gamma di soluzioni hardware. I modelli di progetto CocosSharp utilizzano questo metodo per impostare le dimensioni predefinite del progetto su 1024 x 768, come illustrato nel codice seguente:


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

È possibile modificare il `desiredWidth` e `desiredHeight` variabili precedenti per modificare la visualizzazione in modo che corrisponda alla risoluzione desiderata del gioco. Ad esempio, il codice sopra riportato potrebbe essere modificato come segue per visualizzare sullo sfondo 368 x 240 come a schermo intero:


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

`SetDesignResolutionSize` Consente di specificare come finestra del gioco si adatta alla risoluzione desiderata. Nelle sezioni seguenti viene illustrato come viene visualizzata un'immagine di 500 x 500 con diversi `CCSceneResolutonPolicy` i valori passati al `SetDesignResolutionSize` metodo. Vengono forniti i valori seguenti per il `CCSceneResolutionPolicy` enum:

 - `ShowAll` : Visualizza l'intera risoluzione richiesta, mantenendo le proporzioni.
 - `ExactFit` : Visualizza l'intera risoluzione richiesta, ma non mantiene le proporzioni.
 - `FixedWidth` : Visualizza l'intera larghezza richiesta, mantenendo le proporzioni.
 - `FixedHeight` : Visualizza l'intera altezza richiesta, mantenendo le proporzioni.
 - `NoBorder` : Visualizza l'intera altezza o larghezza intera, mantenendo le proporzioni. Se le proporzioni del dispositivo è maggiore delle proporzioni della risoluzione desiderata, quindi viene visualizzato l'intera larghezza. Se le proporzioni del dispositivo è inferiore al rapporto di aspetto della risoluzione desiderata, viene visualizzato l'intero altezza.
 - `Custom` – La visualizzazione dipende il `Viewport` proprietà dell'oggetto corrente `CCScene`.

Tutte le schermate vengono generate iPhone 4s risoluzione (960x640) con orientamento orizzontale e utilizzano questa immagine:

![](resolutions-images/image4.png "Tutte le schermate vengono generate iPhone 4s risoluzione 960x640 con orientamento orizzontale e utilizzano questa immagine")


### <a name="ccsceneresolutionpolicyshowall"></a>CCSceneResolutionPolicy.ShowAll

`ShowAll` Specifica che la risoluzione di gioco intera sarà visibile sullo schermo, ma potrebbe essere visualizzato *letterboxing* (nere) per adattarsi ai proporzioni diverse. Questo criterio viene comunemente usato come garantisce che l'intera vista gioco verrà visualizzato sullo schermo senza una distorsione.

Esempio di codice:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

Letterboxing è visibile a sinistra e a destra dell'immagine per rappresentare le proporzioni fisica da più ampia rispetto alla risoluzione desiderata:

![](resolutions-images/image5.png "Letterboxing è visibile a sinistra e a destra dell'immagine per rappresentare le proporzioni fisica da più ampia rispetto alla risoluzione desiderata")


### <a name="ccsceneresolutionpolicyexactfit"></a>CCSceneResolutionPolicy.ExactFit

`ExactFit` Specifica che la risoluzione di gioco intera sarà visibile sullo schermo con nessun letterboxing. L'area visualizzabile distorto (proporzioni potrebbe non essere mantenuto) in base alle proporzioni dell'hardware.

Esempio di codice:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ExactFit);
```

Nessun letterboxing è visibile, ma poiché la risoluzione del dispositivo rettangolare la visualizzazione di gioco è distorto:

![](resolutions-images/image6.png "Nessun letterboxing è visibile, ma poiché la risoluzione del dispositivo rettangolare distorta la visualizzazione di gioco")


### <a name="ccsceneresolutionpolicyfixedwidth"></a>CCSceneResolutionPolicy.FixedWidth

`FixedWidth` Specifica che la larghezza della visualizzazione corrisponderà al valore di larghezza passato a `SetDesignResolutionSize`, ma l'altezza visualizzabile è soggetta alle proporzioni del dispositivo fisico. Il valore di altezza passato a `SetDesignResolutionSize` viene ignorato dal momento in cui verrà calcolata in fase di esecuzione in base alle proporzioni del dispositivo fisico. Ciò significa che l'altezza calcolata può essere inferiore a quanto l'altezza desiderata (che genera parti della visualizzazione gioco da fuori dello schermo) o l'altezza calcolata può essere maggiore rispetto all'altezza desiderata (che comporta più la visualizzazione del gioco). Poiché questo può comportare più di gioco viene visualizzato, quindi potrebbe sembrare come se si è verificato letterboxing; Tuttavia, lo spazio aggiuntivo non useranno necessariamente nero se viene visualizzato alcun oggetto visivo. 

Esempio di codice:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedWidth);
```

IPhone 4s presenta un rapporto di 3:2, pertanto l'altezza calcolata è circa 333 unità:

![](resolutions-images/image7.png "IPhone 4s dispone di un rapporto di 3:2, pertanto l'altezza calcolata è circa 333 unità")


### <a name="ccsceneresolutionpolicyfixedheight"></a>CCSceneResolutionPolicy.FixedHeight

Concettualmente, `FixedHeight` si comporta in modo analogo a `FixedWidth` : il gioco avverrà il valore di altezza passato a `SetDesignResolutionSize,` ma calcolerà la larghezza in fase di esecuzione in base alla risoluzione fisica. Come accennato in precedenza, ciò significa che la larghezza visualizzato minore o maggiore della larghezza desiderata, risultante nella parte del gioco da off schermata o più del gioco viene visualizzato, rispettivamente.

Esempio di codice:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

Poiché nella schermata seguente è stata creata con l'app in esecuzione in modalità orizzontale, la larghezza calcolata è maggiore di 500: 750 in modo specifico. Questo criterio consente di mantenere il valore di X pari a 0 allineata a sinistra, in modo risoluzione aggiuntiva è visualizzabile sul lato destro dello schermo.

![](resolutions-images/image8.png "Questo criterio mantiene il valore di X 0 allineata a sinistra, pertanto la risoluzione aggiuntiva è visualizzabile sul lato destro dello schermo")


### <a name="ccsceneresolutionpolicynoborder"></a>CCSceneResolutionPolicy.NoBorder

`NoBorder` tenta di visualizzare l'applicazione con nessun letterboxing mantenendo le proporzioni originali (Nessuna distorsione). Se le proporzioni della risoluzione richiesta corrisponde a proporzioni fisico del dispositivo, verrà effettuato alcun ritaglio. Se le proporzioni non corrispondono, quindi ritaglio si verificherà.

Esempio di codice:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

La schermata seguente vengono visualizzate le parti superiore e inferiore dello schermo troncato, mentre vengono visualizzati tutti i 500 pixel di larghezza di visualizzazione:

![](resolutions-images/image9.png "Questa schermata consente di visualizzare le parti superiore e inferiore dello schermo troncato, mentre vengono visualizzati tutti i 500 pixel di larghezza di visualizzazione")


### <a name="ccsceneresolutionpolicycustom"></a>CCSceneResolutionPolicy.Custom

`Custom` consente a ogni `CCScene` per specificare il proprio viewport personalizzata rispetto alla risoluzione specificata `SetDesignResolutionSize`.

Definiscono le scene loro `Viewport` proprietà costruendo un `CCViewport`, che a sua volta, viene costruito con un `CCRect`. Per ulteriori informazioni su `CCViewport` e `CCRect` vedere il [documentazione dell'API CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/). Nel contesto della creazione di un `CCViewport` il `CCRect` specificano i parametri del costruttore (in ordine):

 - x – la quantità di offset orizzontale del viewport, dove ogni unità rappresenta una larghezza intera schermata. Ad esempio, usando un valore di .5f risultati nella scena spostata a destra della metà della larghezza dello schermo.
 - y: il valore di offset verticale del viewport, dove ogni unità rappresenta l'altezza dello schermo intero uno. Ad esempio, usando un valore di .5f risultati nella scena spostata in basso a metà dell'altezza dello schermo.
 - Larghezza: la parte orizzontale che deve occupare la scena. Ad esempio, usando un valore pari a 1 / 3.0f comporta la scena in senso orizzontale che occupa un terzo della schermata.
 - Altezza: la parte verticale che deve occupare la scena. Ad esempio, usando un valore pari a 1 / 3.0f comporta la scena in senso verticale che occupa un terzo della schermata.

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

![](resolutions-images/image10.png "Il codice sopra i risultati in questa schermata")


## <a name="defaulttexeltocontentsizeratio"></a>DefaultTexelToContentSizeRatio

Il `DefaultTexelToContentSizeRatio` semplifica l'utilizzo di trame con risoluzione superiore per i dispositivi dotati di schermi a risoluzione superiore. In particolare, questa proprietà consente di giochi da usare con risoluzione superiore asset senza dover per modificare le dimensioni o il posizionamento di elementi visivi. 

Ad esempio, un gioco può includere un asset di immagini per un carattere di gioco che è 200 pixel in altezza e la schermata di gioco (come specificato da `SetDesignResolutionSize`) potrebbe essere 400 pixel di altezza. In questo caso, il carattere occupa la metà della schermata. Tuttavia, se un asset pixel di altezza 400 sono stati utilizzati per il carattere per i dispositivi con risoluzione superiore, il carattere occupata l'altezza dello schermo intero. Se si desidera mantenere il carattere le stesse dimensioni per sfruttare i vantaggi di più dispositivi di risoluzione, codice aggiuntivo, è necessario mantenere sprite caratteri a metà dell'altezza dello schermo.

Il semplice esempio presentato in precedenza rappresenta un problema comune di sviluppo di giochi: aggiunta di risorse di grandi dimensioni senza richiedere allo sviluppatore di eseguire il ridimensionamento in ogni elemento visivo in base alla risoluzione di dispositivo. `DefaultTexelToContentSizeRatio` è una proprietà globale utilizzata per ridimensionare gli elementi visivi. Questo valore viene ridimensionata tutti gli elementi visivi di un determinato tipo per il valore assegnato.

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

Per vedere come `DefaultTexelToContentSizeRatio` influisce sulle dimensioni di visual elementi, si consideri il codice presentato in precedenza:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

Sarà pertanto nell'immagine seguente utilizzando una trama 500 x 500:

![](resolutions-images/image5.png "Sarà pertanto in questa immagine utilizzando una trama 500 x 500")

IPad Retina viene eseguita una risoluzione fisica di 2048 x 1536. Ciò significa che il gioco, come nell'esempio sopra riportato sarebbe estensione 500 pixel su 1536 pixel fisici. Giochi possono sfruttare questa risoluzione aggiuntiva tramite la creazione di ciò che sono in genere definite *hd* principali: attività che sono progettati per schermi a risoluzione superiore. Ad esempio, il gioco è possibile utilizzare una versione hd di questo trama ridimensionata a 1000 x 1000. Tuttavia, utilizzando l'immagine più grande comporta il `CCSprite` con una larghezza e altezza di 1000 unità. Poiché il gioco viene sempre visualizzato 500 unità (a causa di `SetDesignResolutioSize` chiamare), quindi il nostro sprite 1000 x 1000 sarebbe troppo grande (Visualizza solo la parte sinistra nella parte inferiore di esso):

![](resolutions-images/image11.png "Poiché il gioco viene sempre visualizzato 500 unità a causa della chiamata SetDesignResolutioSize, sprite 1000 x 1000 sarebbe troppo grande che visualizza solo la parte sinistra nella parte inferiore di esso")

È possibile impostare `CCSprite.DefaultTexelToContentSizeRatio` per tenere conto per la trama di 1000 x 1000 da due volte come larghezza e altezza come la trama di 500 x 500 originale:


```csharp
CCSprite.DefaultTexelToContentSizeRatio = 2;
```

Se Esegui il gioco la trama di 1000 x 1000 verrà essere completamente visibile:

![](resolutions-images/image12.png "A questo punto se Esegui il gioco la trama di 1000 x 1000 sarà completamente visibile")


### <a name="defaulttexeltocontentsizeratio-details"></a>Dettagli DefaultTexelToContentSizeRatio

Il `DefaultTexelToContentSizeRatio` proprietà `static,` ovvero sprite tutti nell'applicazione condividono lo stesso valore. L'approccio tipico per i giochi le risorse per diverse risoluzioni è contenga un set completo di attività per ogni categoria di risoluzione. Per impostazione predefinita di CocosSharp di Visual Studio per i modelli di Mac fornire **ld** e **hd** cartelle per i beni, potrebbe essere utile per i giochi che supporta due set di trame. Una cartella del contenuto di esempio con contenuto potrebbe essere simile:

![](resolutions-images/image13.png "Simile a una cartella del contenuto di esempio con contenuto")

Si noti che il modello predefinito include anche codice per aggiungere in modo condizionale per l'applicazione `ContentSearchPaths`:


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

Ciò significa che l'applicazione cercherà i file nei percorsi in base che è in esecuzione in modalità di risoluzione regolare o ad alta risoluzione. Ad esempio, se il **hd** e **ld** cartelle contengono un file denominato **background.png** quindi il codice seguente verrà eseguita e selezionare il file corretto per la risoluzione:


```csharp
backgroundSprite  = new CCSprite ("background");
```


## <a name="summary"></a>Riepilogo

In questo articolo viene descritto come creare giochi che consentono di visualizzare correttamente indipendentemente dalla risoluzione di dispositivo. Sono riportati esempi dell'utilizzo di diverse `CCSceneResolutionPolicy` i valori per ridimensionare il gioco in base alla risoluzione del dispositivo. Fornisce inoltre un esempio di come `DefaultTexelToContentSizeRatio` può essere utilizzato per contenere più set di contenuti senza elementi visivi deve essere ridimensionato singolarmente.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione CocosSharp](~/graphics-games/cocossharp/index.md)
- [Documentazione CocosSharp API](https://developer.xamarin.com/api/namespace/CocosSharp/)
