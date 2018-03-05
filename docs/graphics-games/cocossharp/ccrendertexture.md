---
title: Prestazioni ed effetti visivi con CCRenderTexture
description: "CCRenderTexture consente agli sviluppatori di migliorare le prestazioni dei giochi CocosSharp riducendo le chiamate di disegno e può essere usato per creare effetti visivi. Questa guida vengono forniti il codice di esempio CCRenderTexture per fornire un esempio pratico di come utilizzare efficacemente questa classe."
ms.topic: article
ms.prod: xamarin
ms.assetid: F02147C2-754B-4FB4-8BE0-8261F1C5F574
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: 8283c299d0e6529ef4cf8c285ec47b4d42fc682a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="performance-and-visual-effects-with-ccrendertexture"></a>Prestazioni ed effetti visivi con CCRenderTexture

_CCRenderTexture consente agli sviluppatori di migliorare le prestazioni dei giochi CocosSharp riducendo le chiamate di disegno e può essere usato per creare effetti visivi. Questa guida vengono forniti il codice di esempio CCRenderTexture per fornire un esempio pratico di come utilizzare efficacemente questa classe._

La `CCRenderTexture` classe fornisce funzionalità per il rendering di più oggetti di CocosSharp a una sola trama. Una volta creato, `CCRenderTexture` le istanze possono essere utilizzate per il rendering della grafica in modo efficiente e implementare effetti visivi. `CCRenderTexture` consente a più oggetti da sottoporre a rendering su una trama sola una volta. La trama può quindi essere riutilizzate ogni fotogramma, riducendo il numero totale di chiamate di disegno.

Questa guida viene esaminato come utilizzare il `CCRenderTexture` oggetto per migliorare le prestazioni delle schede per il rendering in un gioco da raccogliere (CCG). Viene inoltre illustrato come `CCRenderTexture` può essere usato per rendere trasparente l'intera entità. Questa guida fa riferimento il `CCRenderTexture` [progetto di esempio](https://developer.xamarin.com/samples/mobile/CCRenderTexture/).

![](ccrendertexture-images/image1.png "Questa guida fa riferimento il progetto di esempio CCRenderTexture")


# <a name="card--a-typical-entity"></a>Scheda – un'entità tipico

Prima di esaminare in che modo utilizzare `CCRenderTexture` dell'oggetto, si verrà innanzitutto acquisita familiarità con la `Card` entità che verranno utilizzate in tutto il progetto per esplorare la `CCRenderTexture` classe. Il `Card` classe è un'entità tipico, descritte nel modello di entità di [Guida entità](~/graphics-games/cocossharp/entities.md). La classe di Smart Card dispone di tutti i relativi componenti visual (istanze di `CCSprite` e `CCLabel`) elencati come campi:


```csharp
class Card : CCNode
{
    bool usesRenderTexture;
    List<CCNode> visualComponents = new List<CCNode>();
    CCSprite background;
    CCSprite colorIcon;
    CCSprite monsterSprite;
    CCLabel monsterNameDisplay;
    CCLabel hpDisplay;
    CCLabel descriptionDisplay;
```

Rendering di istanze di smart card possono essere eseguite utilizzando un `CCRenderTexture`, o disegnando singolarmente ogni componente visual. Anche se ogni componente è un oggetto indipendente, la `CCNode` relazione padre-figlio di sistema utilizzate nelle entità rende il `Card` si comportano come un singolo oggetto, almeno per la maggior parte. Ad esempio, se un `Card` entità viene riposizionato, ridimensionato o ruotato, quindi tutti gli oggetti visivi contenuti sono interessati per visualizzare la scheda da un singolo oggetto. Per visualizzare le schede si comportano come un singolo oggetto, è possibile modificare il `GameLayer.AddedToScene` per impostare il `useRenderTextures` variabile `false`:

    
```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    ...
}
```

Il `GameLayer` codice non consente di spostare ogni elemento visivo in modo indipendente, ancora ogni elemento visivo all'interno di `Card` entità è posizionato correttamente:

![](ccrendertexture-images/image1.png "Il codice GameLayer non spostare ogni elemento visivo in modo indipendente, ma ogni elemento visivo all'interno dell'entità di Smart Card è posizionato correttamente")

L'esempio è codificata per esporre due problemi che possono verificarsi quando viene eseguito il rendering ogni componente visual:

- Riduzione delle prestazioni a causa di più chiamate di disegno
- Alcuni effetti visivi, ad esempio la trasparenza, non possono essere implementati in modo accurato, come verrà illustrato più avanti


## <a name="card-draw-calls"></a>Chiamate di disegno smart card

Il codice è una semplificazione di ciò che potrebbero essere presenti in una procedura completa di *gioco da raccogliere* (CCG), ad esempio "Magic: la raccolta" o "Hearthstone". Il gioco solo Visualizza tre schede in una sola volta e ha un numero ridotto di unità possibili (blu, verde e arancione). Al contrario, un gioco completo potrebbe essere oltre venti schede visualizzate in un determinato momento e lettori potrebbero essere centinaia di schede da selezionare quando si crea i ponti. Anche se il gioco non attualmente subisce problemi di prestazioni, potrebbe essere un gioco completo con un'implementazione simile.

CocosSharp fornisce approfondire le prestazioni di rendering esponendo le chiamate di disegno eseguita per frame. Il nostro `GameLayer.AddedToScene` metodo imposta la `GameView.Stats.Enabled` a `true`, con conseguente informazioni delle prestazioni visualizzate nella parte inferiore dello schermo:

![](ccrendertexture-images/image2.png "Il metodo GameLayer.AddedToScene imposta il GameView.Stats.Enabled su true, pertanto le informazioni sulle prestazioni visualizzate nella parte inferiore della schermata")

Notare che, nonostante tre schede nella schermata, è 19 le chiamate di disegno (risultati ogni smart card in sei chiamate di disegno, il testo visualizzando gli account di informazioni sulle prestazioni per un altro). Le chiamate di disegno di avere un impatto significativo sulle prestazioni di un gioco, in modo CocosSharp fornisce una serie di modi per ridurli. Una tecnica è descritta nel [CCSpriteSheet Guida](~/graphics-games/cocossharp/ccspritesheet.md). Un'altra tecnica consiste nell'usare il `CCRenderTexture` ridurre ogni entità fino a una chiamata, come verrà esaminato in questa Guida.


## <a name="card-transparency"></a>Trasparenza di smart card

Il nostro `Card` entità include un `Opacity` proprietà la trasparenza di controllo, come illustrato nel frammento di codice seguente:


```csharp
public override byte Opacity
{
    get
    {
        return base.Opacity;
    }
    set
    {
        base.Opacity = value;
        if (usesRenderTexture)
        {
            this.renderTexture.Sprite.Opacity = value;
        }
        else
        {
            foreach (var component in visualComponents)
            {
                component.Opacity = value;
            }
        }
    }
}
```

Si noti che il metodo di impostazione supporta l'utilizzo di rendering, trame o il rendering di ogni componente singolarmente. Per visualizzare il relativo effetto, è possibile modificare il `opacity` valore `127` (circa metà opacità) in `GameLayer.AddedToScene` sarà pertanto ciascun componente con un `Opacity` valore `127`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    const byte opacity = 127;
    ...
}
```

Il gioco verrà ora eseguito il rendering di schede con alcuni trasparenza, provocando scure poiché lo sfondo è bianco:

![](ccrendertexture-images/image3.png "Il gioco ora esegue il rendering di schede con alcuni trasparenza, provocando scure poiché lo sfondo è bianco")

A prima vista potrebbe apparire come se lo stesso correttamente apportato trasparente. Tuttavia, la schermata Visualizza un numero di problemi di vista.

Poiché il nostro sfondo nero, si aspetta che ogni parte del nostro card diventa più scura a causa della trasparenza. Vale a dire diventa una carta più trasparente, più scura diventa. Opacità 0, un `Card` sarà invisibile (completamente nero). Tuttavia, alcune parti del nostro card non diventano più scure quando opacità è stato modificato in `127`. Peggio ancora, alcune parti del nostro card effettivamente è diventato più luminosi quando sono diventate più trasparente. Diamo un'occhiata a parti del nostro scheda che sono stati nere *prima* fossero trasparenti, in particolare il testo dei dettagli e le strutture neri intorno all'immagine monster. Se si inserisce questi side-by, è possibile osservare l'impatto dell'applicazione di trasparenza:

![](ccrendertexture-images/image4.png "Se inserito side-by-side, può essere visualizzato l'impatto dell'applicazione di trasparenza")

Come indicato in precedenza, tutte le parti della scheda dovrebbero diventare più scure quando diventando più trasparente, ma in numerose aree non è il caso:

- Struttura del robot diventa più chiara (va da nero in grigio)
- Il testo della descrizione diventa più chiaro (va da nero in grigio)
- Alla parte verde del robot diventa meno saturo, ma non diventa più scura

Per facilitare la visualizzazione perché in questo caso, è necessario tenere presente che ogni componente visual viene disegnato in modo indipendente, in ogni parte trasparente. Il primo componente visual disegnato è sfondo della scheda. Gli elementi trasparenti successivi verranno disegnati sopra la scheda e saranno interessati dallo sfondo della scheda. Se si rimuove un testo dalla scheda e spostare l'elemento grafico robot verso il basso, possiamo vedere robot di impatto lo sfondo della scheda. Si noti la riga arancione nella casella superiore può essere visualizzata in robot, e che l'area del robot che si sovrappone strisce blu al centro della scheda viene disegnato più scura:

![](ccrendertexture-images/image5.png "Si noti la riga arancione nella casella superiore può essere visualizzata in robot, e che l'area del robot che si sovrappone strisce blu al centro della scheda viene disegnato più scuro")

Utilizzando un `CCRenderTexture` consente di rendere trasparente l'intera scheda senza conseguenze per il rendering dei singoli componenti all'interno di scheda, come verrà illustrato più avanti in questa Guida.


# <a name="using-ccrendertexture"></a>Utilizzando CCRenderTexture

Ora che sono stati identificati i problemi con il rendering di ogni componente singolarmente, si verrà attivato per il rendering di un `CCRenderTexture` e confrontare il comportamento.

Per attivare il rendering di un `CCRenderTexture`, modificare il `userRenderTextures` variabile `true` in `GameLayer.AddedToScene`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = true;
```


## <a name="card-draw-calls"></a>Chiamate di disegno smart card

Se si esegue ora il gioco, vedremo le chiamate di disegno ridotte da diciannove a quattro (ogni scheda ridotta da 6 a uno):

![](ccrendertexture-images/image6.png "Se Esegui il gioco, chiamate di disegno ridotto da diciannove a quattro ogni scheda ridotto da 6 a uno")

Come accennato in precedenza, questo tipo di riduzione può avere un impatto significativo sul giochi con altre entità visual sullo schermo.


## <a name="card-transparency"></a>Trasparenza di smart card

Una volta il `useRenderTextures` è impostato su `true`, schede trasparente verranno eseguito il rendering in modo diverso:

![](ccrendertexture-images/image7.png "Una volta il useRenderTextures è impostata su true, trasparente schede verrà eseguito il rendering in modo diverso")

Confrontare quindi la scheda di robot trasparente utilizzando le trame di rendering (sinistra) e senza (destra):

![](ccrendertexture-images/image8.png "Confrontare la scheda di robot trasparente utilizzando Visual Studio (a sinistra) rendering trame senza (destra)")

Le differenze più evidenti sono nel testo dettagli (nero anziché grigio chiaro) e sprite robot (scuro anziché chiaro e saturazione).


# <a name="ccrendertexture-details"></a>Dettagli CCRenderTexture

Dopo aver esaminato i vantaggi dell'utilizzo `CCRenderTexture`, diamo un'occhiata alla modalità di utilizzo nel `Card` entità.

Il `CCRenderTexture` è un'area di disegno che può essere la destinazione di rendering. Presenta due principali differenze rispetto alla schermata di gioco:

1. Il `CCRenderTexture` persiste tra frame. Ciò significa che un `CCRenderTexture` deve essere visualizzato solo quando vengono apportate. In questo caso, il `Card` entità non cambia mai, pertanto viene visualizzato solo una volta. Se qualsiasi `Card` componenti modificati, quindi la scheda dovrà essere ridisegnato per relativo `CCRenderTexture`. Ad esempio, se il valore di HP (punti di integrità) modificato in caso di attacco, scheda sarebbe necessario eseguire il rendering in modo da riflettere il nuovo valore di HP.
1. Il `CCRenderTexture` dimensioni in pixel non vengono associate alla schermata. Oggetto `CCRenderTexture` può essere maggiore o minore rispetto alla risoluzione del dispositivo. Il `Card` codice crea un `CCRenderTexture` utilizzando le dimensioni dei relativi sprite in background. La scheda contiene un riferimento a un `CCRenderTexture` chiamato `renderTexture`:


```csharp
CCRenderTexture renderTexture;
```

Il `renderTexture` istanza rimane `null` fino a quando il `UseRenderTexture` proprietà viene assegnata a true, che chiama `SwitchToRenderTexture`:


```csharp
private void SwitchToRenderTexture()
{
    // The card needs to be moved to the origin (0,0) so it's rendered on the render target. 
    // After it's rendered to the CCRenderTexture, it will be moved back to its old position
    var oldPosition = this.Position;
    // Make sure visuals are part of the card so they get rendered
    bool areVisualComponentsAlreadyAdded = this.Children != null && this.Children.Contains(visualComponents[0]);
    if (!areVisualComponentsAlreadyAdded)
    {
        // Temporarily add them so we can render the object:
        foreach (var component in visualComponents)
        {
            this.AddChild(component);
        }
    }
    // Create the render texture if it hasn't yet been made:
    if (renderTexture == null)
    {
        // Even though the game is zoomed in to create a pixellated look, we are using
        // high-resolution textures. Therefore, we want to have our canvas be 2x as big as 
        // the background so fonts don't appear pixellated
        var pixelResolution = background.ContentSize * 2;
        var unitResolution = background.ContentSize;
        renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
        //renderTexture.Sprite.Scale = .5f;
    }
    // We don't want the render target to be a child of this when we update it:
    if (this.Children != null && this.Children.Contains(renderTexture.Sprite))
    {
        this.Children.Remove(renderTexture.Sprite);
    }
    // Move this instance back to the origin so it is rendered inside the render texture:
    this.Position = CCPoint.Zero;
    // Clears the CCRenderTexture
    renderTexture.BeginWithClear(CCColor4B.Transparent);
    // Visit renders this object and all of its children
    this.Visit();
    // Ends the rendering, which means the CCRenderTexture's Sprite can be used
    renderTexture.End();
    // We no longer want the individual components to be drawn, so remove them:
    foreach (var component in visualComponents)
    {
        this.RemoveChild(component);
    }
    // Move this back to its original position:
    this.Position = oldPosition;
    // add the render texture sprite to this:
    renderTexture.Sprite.AnchorPoint = CCPoint.Zero;
    this.AddChild(renderTexture.Sprite);
}
```

Il `SwitchToRenderTexture` metodo può essere chiamato ogni volta che la trama deve essere aggiornato. Può essere chiamato se sta già utilizzando la scheda relativa `CCRenderTexture` o è il passaggio al `CCRenderTexture` per la prima volta.

Esplorare le sezioni seguenti di `SwitchToRenderTexture` metodo. 


## <a name="ccrendertexture-size"></a>Dimensioni CCRenderTexture

Il costruttore CCRenderTexture richiede due set di dimensioni. Il primo controlla le dimensioni del `CCRenderTexture` quando viene disegnato e il secondo specifica la larghezza in pixel e l'altezza del relativo contenuto. Il `Card` crea un'istanza di entità relativi `CCRenderTexture` utilizzando lo sfondo [ContentSize](https://developer.xamarin.com/api/property/CocosSharp.CCSprite.ContentSize/). Il gioco è un `DesignResolution` di 512 da 384, come illustrato nella `ViewController.LoadGame` in iOS e `MainActivity.LoadGame` in Android:


```csharp
int width = 512;
int height = 384;
// Set world dimensions
gameView.DesignResolution = new CCSizeI(width, height);
```

Il `CCRenderTexture` costruttore viene chiamato con il `background.ContentSize` come primo parametro, che indica che il `CCRenderTexture` deve essere di dimensioni come sfondo `CCSprite`. Poiché lo sfondo della scheda `CCSprite` è 200 pixel in altezza, la smart card occupano circa la metà dell'altezza verticale dello schermo.

Il secondo parametro passato per il `CCRenderTexture` costruttore specifica la risoluzione di pixel del `CCRenderTexture`. Come descritto nel [Guida alla risoluzione CocosSharp](~/graphics-games/cocossharp/resolutions.md), la larghezza e l'altezza dell'area visualizzabile in unità gioco spesso non è lo stesso come la risoluzione dello schermo in pixel. Analogamente, un CCRenderTexture potrebbe utilizzare una risoluzione maggiore rispetto alle relative dimensioni in modo gli oggetti visivi vengano visualizzati più dispositivi ad alta risoluzione.

La risoluzione dei pixel è due volte la dimensione di CCRenderTexture per evitare che il testo di livello professionale:


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize * 2;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

Per confrontare, è possibile modificare il valore pixelResolution in modo che corrisponda il `background.ContentSize` (senza viene raddoppiata) e confrontare il risultato: 


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

![](ccrendertexture-images/image9.png "Per confrontare, può modificare il valore di pixelResolution per corrispondere allo sfondo. ContentSize senza viene raddoppiata e confrontare il risultato")


## <a name="rendering-to-a-ccrendertexture"></a>Esegue il rendering in un CCRenderTexture

In genere, gli oggetti visivi CocosSharp non vengono visualizzati in modo esplicito. Invece, gli oggetti visivi vengono aggiunti a un `CCLayer` che fa parte di un `CCScene`. CocosSharp esegue automaticamente il rendering di `CCScene` e la relativa gerarchia visual in ogni frame senza alcun codice per il rendering viene chiamato. 

Al contrario, il `CCRenderTexture` deve essere disegnato in modo esplicito su. Il rendering può essere suddiviso in tre passaggi:

1. `CCRenderTexture.BeginWithClear` viene chiamato, che indica che la chiamata verrà associato a ogni rendering successivo `CCRenderTexture`.
1. Gli oggetti che ereditano da `CCNode` (ad esempio il `Card` entità) vengono sottoposti a rendering il `CCRenderTexture` chiamando `Visit`.
1. `CCRenderTexture.End` viene chiamato, che indica che per il rendering per il `CCRenderTexture` è stata completata.

Qualsiasi numero di oggetti può essere visualizzato in un `CCRenderTexture` tra relativo `Begin` e `End` chiamate. Prima del rendering, tutti gli oggetti visibili necessari vengono aggiunti come elementi figlio:


```csharp
bool areVisualComponentsAlreadyAdded = this.Children != null && this.Children.Contains(visualComponents[0]);
if (!areVisualComponentsAlreadyAdded)
{
    // Temporarily add them so we can render the object:
    foreach (var component in visualComponents)
    {
        this.AddChild(component);
    }
}
```

Il `renderTexture` non devono far parte della scheda per il rendering, viene rimosso:


```csharp
// We don't want the render texture to be a child of the card 
// when we call Visit
if (this.Children != null && this.Children.Contains(renderTexture.Sprite))
{
    this.RemoveChild(renderTexture.Sprite);
}
```

A questo punto il `Card` istanza può eseguire il rendering per il `CCRenderTexture` istanza:


```csharp
// Clears the CCRenderTexture
renderTexture.BeginWithClear(CCColor4B.Transparent);
// Visit renders this object and all of its children
this.Visit();
// Ends the rendering, which means the CCRenderTexture's Sprite can be used
renderTexture.End();
```

Una volta completato il rendering, vengono rimossi i singoli componenti e `CCRenderTexture` viene nuovamente aggiunto.


```csharp
// We no longer want the individual components to be drawn, so remove them:
foreach (var component in visualComponents)
{
    this.RemoveChild(component);
}
// add the render target sprite to this:
this.AddChild(renderTexture.Sprite);
```

# <a name="summary"></a>Riepilogo

Trattati in questa Guida di `CCRenderTexture` classe utilizzando un `Card` entità che potrebbero essere usate in un gioco ritirabile. È stato spiegato come utilizzare la `CCRenderTexture` classe per migliorare la frequenza dei fotogrammi e implementare correttamente la trasparenza a livello di entità.

Sebbene questa guida è utilizzato un `CCRenderTexture` contenuti all'interno di un'entità, questa classe può essere utilizzato per il rendering di più entità, o addirittura interi `CCLayer` istanze per effetti a livello di schermata e miglioramenti delle prestazioni.

## <a name="related-links"></a>Collegamenti correlati

- [Riferimento all'API CCRenderTexture](https://developer.xamarin.com/api/type/CocosSharp.CCRenderTexture/)
- [Progetto completo (esempio)](https://developer.xamarin.com/samples/mobile/CCRenderTexture/)
