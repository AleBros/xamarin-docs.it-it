---
title: Prestazioni e gli effetti visivi con CCRenderTexture
description: CCRenderTexture consente agli sviluppatori di migliorare le prestazioni dei giochi, CocosSharp riducendo le chiamate di disegno e può essere usato per creare effetti visivi. Questa Guida accompagna l'esempio CCRenderTexture per fornire un esempio pratico di come usare questa classe in modo efficace.
ms.prod: xamarin
ms.assetid: F02147C2-754B-4FB4-8BE0-8261F1C5F574
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 95227689303a8367785202956a6aaef921c1c593
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526442"
---
# <a name="performance-and-visual-effects-with-ccrendertexture"></a>Prestazioni e gli effetti visivi con CCRenderTexture

_CCRenderTexture consente agli sviluppatori di migliorare le prestazioni dei giochi, CocosSharp riducendo le chiamate di disegno e può essere usato per creare effetti visivi. Questa Guida accompagna l'esempio CCRenderTexture per fornire un esempio pratico di come usare questa classe in modo efficace._

Il `CCRenderTexture` classe fornisce funzionalità per il rendering di più oggetti CocosSharp a sola trama. Una volta creata, `CCRenderTexture` istanze possono essere usate per eseguire il rendering della grafica in modo efficiente e implementare gli effetti visivi. `CCRenderTexture` consente a più oggetti da sottoporre a rendering per una singola trama una sola volta. Tale trama può quindi essere riutilizzato per ogni fotogramma, riducendo il numero totale di chiamate di disegno.

Questa guida viene illustrato come utilizzare il `CCRenderTexture` oggetto per migliorare le prestazioni di schede per il rendering in un gioco di carte da raccogliere (CCG). Viene inoltre illustrato come `CCRenderTexture` può essere utilizzato per rendere trasparente un'intera entità. Questa guida vengono trattati i `CCRenderTexture` [del progetto di esempio](https://developer.xamarin.com/samples/mobile/CCRenderTexture/).

![](ccrendertexture-images/image1.png "Questa guida fa riferimento al progetto di esempio CCRenderTexture")


## <a name="card--a-typical-entity"></a>Scheda – un'entità tipica

Prima di esaminare come utilizzare `CCRenderTexture` dell'oggetto, si verrà innanzitutto acquisita familiarità con il `Card` entità che verranno utilizzate nel corso di questo progetto per esplorare il `CCRenderTexture` classe. Il `Card` classe è un'entità tipiche, seguendo il modello di entità descritto nel [Guida entità](~/graphics-games/cocossharp/entities.md). La classe Smart Card presenta tutti i relativi componenti visual (istanze di `CCSprite` e `CCLabel`) elencati come campi:


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

Le istanze di smart card possono eseguire il rendering usando un `CCRenderTexture`, o disegnando singolarmente ogni componente visivo. Anche se ogni componente è un oggetto indipendente, la `CCNode` padre di sistema utilizzate nelle entità rende il `Card` si comportano come un singolo oggetto, almeno per la maggior parte. Ad esempio, se un `Card` entità viene riposizionato, ridimensionato o ruotato, quindi sono interessati tutti gli oggetti visivi contenuti per ingrandire la scheda sembra essere un singolo oggetto. Per visualizzare le schede si comportano come un singolo oggetto, è possibile modificare il `GameLayer.AddedToScene` per impostare il `useRenderTextures` variabile `false`:

    
```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    ...
}
```

Il `GameLayer` codice non sposta ogni oggetto visivo in modo indipendente, ancora ogni elemento visivo all'interno di `Card` entità è posizionata correttamente:

![](ccrendertexture-images/image1.png "Il codice GameLayer non consente di spostare in modo indipendente ogni elemento visivo, anche se ogni elemento visivo all'interno dell'entità di Smart Card è posizionata correttamente")

Nell'esempio viene codificato per esporre due problemi che possono verificarsi quando viene eseguito il rendering ogni componente visivo:

- Le prestazioni potrebbero diminuire a causa di più chiamate di disegno
- Non possono essere implementati in modo accurato, alcuni effetti visivi, ad esempio la trasparenza, come verrà presa in esame in un secondo momento


### <a name="card-draw-calls"></a>Chiamate di disegno di smart card

Il codice è una semplificazione di ciò che potrebbero essere presenti in una procedura completa *gioco di carte da raccogliere* (CCG), ad esempio "Magic: la raccolta" o "Hearthstone". Il gioco solo consente di visualizzare tre schede in una sola volta e ha un numero ridotto di unità possibili (blu, verde e arancione). Al contrario, un gioco completo potrebbe essere schede oltre venti sullo schermo in un determinato momento e lettori potrebbero essere centinaia di schede a scegliere durante la creazione i ponti. Anche se il gioco non è attualmente soggetta a problemi di prestazioni, potrebbe essere un gioco completo con un'implementazione simile.

CocosSharp fornisce alcuni approfondimenti sulle prestazioni di rendering esponendo le chiamate di disegno eseguite per fotogramma. Nostri `GameLayer.AddedToScene` metodo imposta la `GameView.Stats.Enabled` a `true`, ottenendo informazioni sulle prestazioni mostrate in basso a sinistra della schermata:

![](ccrendertexture-images/image2.png "Il metodo GameLayer.AddedToScene imposta la GameView.Stats.Enabled su true, determinando le informazioni sulle prestazioni mostrate in basso a sinistra della schermata")

Notare che, nonostante le tre schede nella schermata, è possibile diciannove le chiamate di disegno (risultati ogni smart card in sei chiamate di disegno, il testo visualizzato gli account di informazioni sulle prestazioni per un altro). Le chiamate di disegno hanno un impatto significativo sulle prestazioni del gioco, in modo CocosSharp fornisce una serie di modi per ridurli. Una tecnica è descritta nel [CCSpriteSheet Guida](~/graphics-games/cocossharp/ccspritesheet.md). Un'altra tecnica consiste nell'usare il `CCRenderTexture` ridurre ogni entità a una sola chiamata, come verrà esaminato in questa Guida.


### <a name="card-transparency"></a>Trasparenza di smart card

Nostri `Card` entità include un `Opacity` proprietà alla trasparenza del controllo come illustrato nel frammento di codice seguente:


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

Si noti che il setter supporta l'utilizzo di eseguire il rendering di trame o il rendering di ogni componente singolarmente. Per visualizzare il relativo effetto, modificare il `opacity` valore per `127` (circa la metà opacity) in `GameLayer.AddedToScene` verrà visualizzato un risultato in ogni componente con un `Opacity` pari a `127`:


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

Il gioco a questo punto verrà eseguito il rendering di schede con alcuni trasparenza, comportando la scure poiché lo sfondo nero:

![](ccrendertexture-images/image3.png "A questo punto, il gioco verrà eseguito il rendering, le schede con alcuni trasparenza, comportando la scure poiché lo sfondo nero")

A prima vista potrebbe apparire come se le schede in modo corretto reso trasparente. Tuttavia, la schermata Visualizza una serie di problemi visual.

Poiché nostro sfondo nero, prevediamo che ogni parte del nostro card diventa più scura a causa della trasparenza. Vale a dire, diventa più trasparente una scheda, più scura diventa. Opacità 0, una `Card` saranno completamente trasparente (totalmente nero). Tuttavia, alcune parti del nostro carta non diventano più scure quando opacità è stato modificato in `127`. Peggio ancora, alcune parti del nostro card effettivamente è diventata più brillanti quando essi divennero più evidenti. Diamo un'occhiata a parti del nostro card preconfezionati nere *prima di* fossero trasparente, in particolare il testo dei dettagli e i contorni neri intorno all'immagine di grandi volumi. Se si posizionano queste fianco a fianco, è possibile osservare l'impatto dell'applicazione della trasparenza:

![](ccrendertexture-images/image4.png "Se posizionata side-by-side, può essere visualizzato l'impatto dell'applicazione della trasparenza")

Come indicato in precedenza, tutte le parti della scheda dovrebbero diventare più scure quando sta diventando più trasparente, ma in diverse aree richiedano ciò non avviene:

- Struttura del robot diventa più chiaro (va dal nero al grigio)
- Il testo della descrizione diventa più chiaro (va dal nero al grigio)
- Alla parte verde del robot diventa meno saturo, ma non diventa più scura

Per semplificare la visualizzazione perché in questo caso, è necessario tenere presente che ogni componente visivo viene disegnato in modo indipendente e in ogni parte trasparenti. Il primo componente visual disegnato è dello sfondo della scheda. Gli elementi trasparenti successivi verranno disegnati sopra la scheda e saranno interessati dallo sfondo della scheda. Se è possibile rimuovere un testo dalla carta e spostare l'elemento grafico robot verso il basso, è possibile osservare come lo sfondo scheda compromette il robot. Si noti la linea arancione nella casella superiore può essere visualizzata nei robot e che l'area del robot che si sovrappone la striscia blu al centro della scheda viene disegnato più scuri:

![](ccrendertexture-images/image5.png "Si noti la linea arancione nella casella superiore può essere visualizzata nei robot e che l'area del robot che si sovrappone la striscia blu al centro della scheda viene disegnato più scuro")

Usando un `CCRenderTexture` consente di rendere trasparente l'intera scheda senza conseguenze per il rendering dei singoli componenti all'interno della scheda, come vedremo più avanti in questa Guida.


## <a name="using-ccrendertexture"></a>Usando CCRenderTexture

Ora che sono stati identificati i problemi con il rendering di ogni componente singolarmente, si sarà attiva per il rendering di un `CCRenderTexture` e confrontare il comportamento.

Per abilitare il rendering in un `CCRenderTexture`, modificare il `userRenderTextures` variabile `true` in `GameLayer.AddedToScene`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = true;
```


### <a name="card-draw-calls"></a>Chiamate di disegno di smart card

Se eseguiamo il gioco a questo punto, si vedrà chiamate di disegno ridotte da diciannove a quattro (ogni scheda ridotti da 6 a uno):

![](ccrendertexture-images/image6.png "Se il gioco viene eseguito ora, chiamate di disegno ridotto da diciannove a quattro ogni scheda ha ridotto da 6 a uno")

Come accennato in precedenza, questo tipo di riduzione può avere un impatto significativo su giochi con altre entità visual sullo schermo.


### <a name="card-transparency"></a>Trasparenza di smart card

Una volta il `useRenderTextures` è impostata su `true`, schede trasparente verranno eseguito il rendering in modo diverso:

![](ccrendertexture-images/image7.png "Una volta il useRenderTextures è impostato su true, trasparente per le schede verrà eseguito il rendering in modo diverso")

Ecco un confronto tra la scheda robot trasparente utilizzando le trame di rendering (sinistra) o senza (destra):

![](ccrendertexture-images/image8.png "Confrontare la scheda robot trasparente con Visual Studio (a sinistra) rendering trame senza (destra)")

Le differenze più evidenti sono nel testo dei dettagli (nero invece di colore grigio chiaro) e sprite robot (scuro invece di luce e desaturato).


## <a name="ccrendertexture-details"></a>Dettagli CCRenderTexture

Ora che abbiamo visto i vantaggi dell'uso `CCRenderTexture`, diamo un'occhiata a come viene utilizzato nel `Card` entità.

Il `CCRenderTexture` è un'area di disegno che può essere la destinazione di rendering. Ha due principali differenze rispetto alla schermata di gioco:

1. Il `CCRenderTexture` persiste tra i frame. Ciò significa che un `CCRenderTexture` deve solo eseguire il rendering quando vengono apportate. In questo caso, il `Card` entità non cambia mai, pertanto viene visualizzato solo una volta. Eventuale `Card` i componenti modificati, quindi la scheda dovrà essere ridisegnarsi a relativo `CCRenderTexture`. Ad esempio, se il valore di HP (punti di integrità) modificato quando viene attaccato, quindi la scheda sarebbe necessario eseguire il rendering in modo da riflettere il nuovo valore di HP.
1. Il `CCRenderTexture` dimensioni in pixel non sono legate alla schermata. Oggetto `CCRenderTexture` possono essere maggiori o minori rispetto alla risoluzione del dispositivo. Il `Card` codice crea un `CCRenderTexture` utilizzando le dimensioni dei relativi sprite in background. La scheda contiene un riferimento a un `CCRenderTexture` chiamato `renderTexture`:


```csharp
CCRenderTexture renderTexture;
```

Il `renderTexture` istanza rimane `null` fino a quando non le `UseRenderTexture` proprietà viene assegnata a true, che chiama `SwitchToRenderTexture`:


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

Il `SwitchToRenderTexture` metodo può essere chiamato ogni volta che la trama deve essere aggiornato. Può essere chiamato se sta già usando la scheda relativa `CCRenderTexture` o è il passaggio al `CCRenderTexture` per la prima volta.

Le sezioni seguenti illustrano il `SwitchToRenderTexture` (metodo). 


### <a name="ccrendertexture-size"></a>Dimensioni CCRenderTexture

Il costruttore CCRenderTexture richiede due set di dimensioni. Il primo controlla le dimensioni del `CCRenderTexture` quando l'oggetto viene disegnato e il secondo specifica la larghezza in pixel e l'altezza del relativo contenuto. Il `Card` crea un'istanza di entità relativo `CCRenderTexture` usando lo sfondo [ContentSize](https://developer.xamarin.com/api/property/CocosSharp.CCSprite.ContentSize/). Il gioco è un `DesignResolution` di 512 da 384, come illustrato nella `ViewController.LoadGame` in iOS e `MainActivity.LoadGame` in Android:


```csharp
int width = 512;
int height = 384;
// Set world dimensions
gameView.DesignResolution = new CCSizeI(width, height);
```

Il `CCRenderTexture` costruttore viene chiamato con il `background.ContentSize` come primo parametro, che indica che il `CCRenderTexture` dovrebbe arrivare solo fino a background `CCSprite`. Poiché lo sfondo scheda `CCSprite` è 200 pixel in altezza, la scheda occupano circa la metà dell'altezza verticale dello schermo.

Il secondo parametro passato per il `CCRenderTexture` costruttore specifica la risoluzione di pixel del `CCRenderTexture`. Come descritto nel [Guida alla risoluzione di CocosSharp](~/graphics-games/cocossharp/resolutions.md), la larghezza e altezza dell'area visualizzabile in unità di misura del gioco spesso non è quello utilizzato per la risoluzione dello schermo in pixel. Analogamente, un CCRenderTexture potrebbe usare una risoluzione maggiore rispetto alle relative dimensioni in modo che vengano visualizzati nitida dispositivi ad alta risoluzione.

La risoluzione dei pixel è due volte la dimensione di CCRenderTexture per evitare che il testo da disturbate dall'aspetto professionale:


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize * 2;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

Per un confronto, è possibile modificare il valore pixelResolution in modo che corrisponda il `background.ContentSize` (senza essere raddoppiata) e confrontare il risultato: 


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

![](ccrendertexture-images/image9.png "Per confrontare, può modificare il valore pixelResolution per corrispondere allo sfondo. ContentSize senza essere raddoppiata e confrontare il risultato")


### <a name="rendering-to-a-ccrendertexture"></a>Rendering in un CCRenderTexture

In genere, gli oggetti visivi in CocosSharp non vengono visualizzati in modo esplicito. Al contrario, gli oggetti visivi vengono aggiunti a un `CCLayer` che fa parte di un `CCScene`. CocosSharp esegue automaticamente il rendering di `CCScene` e relativa gerarchia visiva in ogni fotogramma senza codice per il rendering viene chiamato. 

Al contrario, il `CCRenderTexture` deve essere disegnato in modo esplicito a. Il rendering può essere suddiviso in tre passaggi:

1. `CCRenderTexture.BeginWithClear` viene chiamato, che indica che ogni rendering successivo saranno destinati a chiamante `CCRenderTexture`.
1. Gli oggetti che eredita da `CCNode` (come il `Card` entità) viene eseguito il rendering le `CCRenderTexture` chiamando `Visit`.
1. `CCRenderTexture.End` viene chiamato, che indica che il rendering di `CCRenderTexture` è stata completata.

Può eseguire il rendering di un numero qualsiasi di oggetti a un `CCRenderTexture` tra i relativi `Begin` e `End` chiamate. Prima del rendering, tutti gli oggetti visibili necessari vengono aggiunti come elementi figlio:


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

Il `renderTexture` non devono far parte della scheda per il rendering, viene rimossa:


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

Una volta terminato il rendering, vengono rimossi i singoli componenti e `CCRenderTexture` viene nuovamente aggiunto.


```csharp
// We no longer want the individual components to be drawn, so remove them:
foreach (var component in visualComponents)
{
    this.RemoveChild(component);
}
// add the render target sprite to this:
this.AddChild(renderTexture.Sprite);
```

## <a name="summary"></a>Riepilogo

Questa guida sono stati illustrati il `CCRenderTexture` classe utilizzando un `Card` entità che possono essere usati in un gioco di carte ritirabile. È stato illustrato come usare il `CCRenderTexture` classe per migliorare la frequenza dei fotogrammi e implementare correttamente la trasparenza a livello di entità.

Anche se usato in questa Guida una `CCRenderTexture` contenuti all'interno di un'entità, questa classe può essere usate per eseguire il rendering di più entità, o addirittura interi `CCLayer` istanze per gli effetti a livello di schermata e miglioramenti delle prestazioni.

## <a name="related-links"></a>Collegamenti correlati

- [Riferimento all'API CCRenderTexture](https://developer.xamarin.com/api/type/CocosSharp.CCRenderTexture/)
- [Progetto completo (esempio)](https://developer.xamarin.com/samples/mobile/CCRenderTexture/)
