---
title: Utilizzando CCTextureCache la memorizzazione nella cache della trama
description: Classe CCTextureCache del CocosSharp fornisce un modo standard per organizzare, cache e scaricare il contenuto. È particolarmente utile per i giochi di grande che potrebbero non essere adatto interamente nella memoria RAM, semplificando il processo di raggruppamento e all'eliminazione delle trame.
ms.prod: xamarin
ms.assetid: 1B5F3F85-9E68-42A7-B516-E90E54BA7102
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: c217d8a935ae971aab472b05968c0251366362b2
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783688"
---
# <a name="texture-caching-using-cctexturecache"></a>Utilizzando CCTextureCache la memorizzazione nella cache della trama

_Classe CCTextureCache del CocosSharp fornisce un modo standard per organizzare, cache e scaricare il contenuto. È particolarmente utile per i giochi di grandi dimensioni che potrebbero non rientra completamente nella memoria RAM, semplificando il processo di raggruppamento e l'eliminazione delle trame._

La `CCTextureCache` classe è una parte essenziale di sviluppo di giochi CocosSharp. CocosSharp la maggior parte dei giochi utilizzare il `CCTextureCache` dell'oggetto, anche se non in modo esplicito, tutti i metodi di CocosSharp utilizzano internamente un *condivisa* trama della cache.

Questa guida viene analizzato il `CCTextureCache` e perché è importante per lo sviluppo di gioco. In particolare vengono trattati:

 - Trama per questioni di memorizzazione nella cache
 - Ciclo di vita di trama
 - Utilizzo SharedTextureCache
 - Caricamento e pre-caricamento con AddImage Lazy
 - Eliminazione di trame


## <a name="why-texture-caching-matters"></a>Trama per questioni di memorizzazione nella cache

La memorizzazione nella cache di trama è una considerazione importante nello sviluppo di gioco, come il caricamento di trama è un'operazione richiede molto tempo e le trame richiedono una notevole quantità di RAM in fase di esecuzione.

Come per qualsiasi operazione di file, il caricamento delle trame dal disco può essere un'operazione costosa. Il caricamento di trama può richiedere più tempo se il file da caricare richiede l'elaborazione, ad esempio essere decompresso (come accade per le immagini png e jpg). La memorizzazione nella cache di trama, è possibile ridurre il numero di volte che l'applicazione deve caricare file dal disco.

Come indicato in precedenza, le trame occupano anche una grande quantità di memoria di runtime. Ad esempio un'immagine di sfondo dimensione per la risoluzione di un iPhone 6 (750 1344) occuperebbero 4 MB di RAM, anche se solo alcuni kilobyte di dimensioni del file PNG. La memorizzazione nella cache di trama fornisce un modo per condividere i riferimenti di trama all'interno di un'applicazione e anche un modo semplice per scaricare tutto il contenuto durante la transizione tra stati diversi di giochi.


## <a name="texture-lifespan"></a>Ciclo di vita di trama

Le trame CocosSharp possono essere mantenute in memoria per l'intera durata dell'esecuzione di un'app o potrebbe trattarsi di breve durate. Per ridurre al minimo della memoria è necessario eliminarlo sull'utilizzo di un'app di trame quando non è più necessario. Naturalmente, ciò significa che le trame possono essere eliminate e nuovamente caricate in un secondo momento, che può aumentare i tempi di caricamento o ridurre le prestazioni durante il caricamento. 

Il caricamento di trama è spesso necessario un compromesso tra i tempi di carico e di utilizzo della memoria o le prestazioni di runtime. Giochi che utilizzano una piccola quantità di memoria texture possono mantenere tutte le trame in memoria in base alle necessità, ma maggiore giochi potrebbero essere necessario scaricare le trame per liberare spazio.

Il diagramma seguente illustra un semplice gioco che carica le trame in base alle esigenze e li mantiene in memoria per l'intera durata di esecuzione:

![](texture-cache-images/image1.png "Questo diagramma mostra un semplice gioco che carica le trame in base alle esigenze e li mantiene in memoria per l'intera durata di esecuzione")

Le prime due barre rappresentano le trame che sono necessari immediatamente al termine dell'esecuzione del gioco. Le barre di tre seguenti rappresentano le trame per ogni livello, caricati in base alle esigenze.

Se il gioco è grande abbastanza e infine carica sufficiente trame per riempire tutti RAM fornito dal dispositivo e del sistema operativo. Per risolvere questo problema, un gioco può scaricare i dati di trama quando non è più necessario. Ad esempio, il diagramma seguente mostra un gioco che scarica Level1Texture quando non è più necessario, quindi carica Level2Texture per il livello successivo. Il risultato finale è che solo tre le trame vengono mantenute in memoria in qualsiasi momento: 

![](texture-cache-images/image2.png "Il risultato finale è che solo tre le trame vengono mantenute in memoria in qualsiasi momento")


Il diagramma precedente indica che l'utilizzo della memoria di trama può essere ridotto scaricando, ma tale operazione potrebbe richiedere tempi di caricamento aggiuntive se un lettore decide di riprodurre un livello. È inoltre importante notare che le trame UITexture e MainCharacter vengono caricate e non vengono mai scaricate. Ciò implica che le trame sono necessari in tutti i livelli, in modo che vengono sempre mantenuti in memoria. 


## <a name="using-sharedtexturecache"></a>Utilizzo SharedTextureCache

CocosSharp automaticamente memorizza nella cache le trame durante il caricamento tramite il `CCSprite` costruttore. Ad esempio il codice seguente crea solo un'istanza di trama:


```csharp
for (int i = 0; i < 100; i++)
{
    CCSprite starSprite = new CCSprite ("star.png");
    starSprite.PositionX = i * 32;
    this.AddChild (starSprite);
} 
```

CocosSharp memorizza nella cache automaticamente il `star.png` trama per evitare l'alternativa costosa di creazione di numerosi identici `CCTexture2D` istanze. Questa operazione viene eseguita da `AddImage` chiamato su un oggetto condiviso `CCTextureCache` istanza, in particolare `CCTextureCache.SharedTextureCache.Shared`. Per comprendere come `SharedTextureCache` viene utilizzato è possibile esaminare il codice seguente è funzionalmente identico alla chiamata di `CCSprite` costruttore con un parametro di stringa:


```

CCSprite starSprite = new CCSprite ();
 starSprite.Texture = CCTextureCache.SharedTextureCache.AddImage ("star.png");
```

`AddImage` Controlla se il file di argomento (in questo caso `star.png`) è già stato caricato. In questo caso, viene restituito l'istanza memorizzata nella cache. Se quindi non viene caricato dal file system e un riferimento per la trama viene archiviato internamente per le successive `AddImage` chiamate. In altre parole il `star.png` immagine viene caricata solo una volta e le chiamate successive non richiedono alcun accesso aggiuntivo su disco o memoria aggiuntive texture.


## <a name="lazy-loading-vs-pre-loading-with-addimage"></a>Caricamento e pre-caricamento con AddImage Lazy

`AddImage` Consente di scrivere lo stesso codice che la trama richiesta è già caricata. Ciò significa che il contenuto non verrà caricato finché è richiesta; Tuttavia, possono verificarsi problemi di prestazioni in fase di esecuzione a causa di contenuto imprevisto durante il caricamento.

Ad esempio, si consideri un gioco in cui può essere aggiornato arma del lettore. Quando aggiornato, l'arma e lanciati visibilmente passerà, risultante in nuove texture in uso. Se il contenuto viene effettuato il caricamento lazy quindi le trame associate armi aggiornati non verranno caricate inizialmente, ma piuttosto in un secondo momento quando il giocatore acquisisce gli aggiornamenti. 

Il caricamento di metà gioco può causare il gioco per *pop*, ovvero un blocco breve ma percettibile in esecuzione. Per evitare questo problema, il codice può stimare le trame potrebbero essere necessario preliminare e pre-caricarli. Ad esempio, il seguente può essere utilizzato per precaricare trame:


```csharp
void PreLoadImages()
{
    var cache = CCTextureCache.SharedTextureCache;

    cache.AddImage ("powerup1.png");
    cache.AddImage ("powerup2.png");
    cache.AddImage ("powerup3.png");

    cache.AddImage ("enemy1.png");
    cache.AddImage ("enemy2.png");
    cache.AddImage ("enemy3.png");

    // pre-load any additional content here to 
    // prevent pops at runtime
} 
```

Questa pre-caricamento può comportare memoria inutilizzata e può aumentare il tempo di avvio. Ad esempio, il lettore non può mai ottenere un accensione rappresentato dal `powerup3.png` trama, pertanto verrà caricato inutilmente. Naturalmente è possibile un costo necessario pagare evitare un potenziale pop nella modalità di gioco, pertanto è in genere consigliabile precaricamento contenuto se rientra nella RAM.


## <a name="disposing-textures"></a>Eliminazione di trame

Se un gioco non richiede più memoria trama è disponibile sul dispositivo specifica minimo trame non è necessario essere eliminati. D'altra parte, giochi maggiore potrebbe essere necessario liberare memoria di trama per liberare spazio per il nuovo contenuto. Ad esempio un gioco può utilizzare una grande quantità di memoria di archiviazione di trame per un ambiente. Se l'ambiente viene utilizzata solo in un livello specifico deve essere scaricato al termine il livello.


### <a name="disposing-a-single-texture"></a>Eliminazione di una trama singola

Rimozione di una sola trama innanzitutto è necessario chiamare il `Dispose` (metodo), quindi la rimozione manuale del `CCTextureCache`.

Di seguito viene illustrato come rimuovere completamente un sprite sfondo e la trama:


```csharp
void DisposeBackground()
{
    // Assuming this is called from a CCLayer:
    this.RemoveChild (backgroundSprite);

    CCTextureCache.SharedTextureCache.RemoveTexture (backgroundsprite.Texture);

    backgroundSprite.Texture.Dispose ();
} 
```

Può essere efficiente direttamente disposing trame quando la gestione di un numero ridotto di trame, ma ciò può diventare soggetta a errori quando si lavora con set di trama di dimensioni maggiori.

Le trame possono essere raggruppate in personalizzata (non condivisi) `CCTextureCache` istanze per semplificare la pulizia di trama.

Ad esempio, si consideri un esempio in cui contenuto è stato precaricato mediante un oggetto specifico di livello `CCTextureCache` istanza. Il `CCTextureCache` istanza può essere definita nella classe che definisce il livello (che può essere un `CCLayer` o `CCScene`):


```csharp
CCTextureCache levelTextures; 
```

Il `levelTextures` istanza può quindi essere utilizzata per precaricare le trame specifici a livello di: 


```

void PreloadLevelTextures(CCApplication application)
{
    levelTextures = new CCTextureCache (application);

    levelTextures.AddImage ("Background.png");
    levelTextures.AddImage ("Foreground.png");
    levelTextures.AddImage ("Enemy1.png");
    levelTextures.AddImage ("Enemy2.png");
    levelTextures.AddImage ("Enemy3.png");

    levelTextures.AddImage ("Powerups.png");
    levelTextures.AddImage ("Particles.png");
} 
```

Infine al termine, il livello di trame possono trovarsi tutti è state eliminate in una sola volta tramite la `CCTextureCache`:


```csharp
void EndLevel()
{
    levelTextures.Dispose ();
    // Perform any other end-level cleanup
} 
```

Il metodo Dispose eliminerà tutte le trame interne, cancellare la memoria usata da queste trame. La combinazione di `CCTextureCache.Shared` con uno specifico livello di gioco modalità `CCTextureCache` istanza comporta alcuni trame persistenza tramite il gioco intera e alcuni in corso lo scaricamento come terminano livelli, simile al diagramma presentato all'inizio di questa guida: 

![](texture-cache-images/image2.png "Combinazione di CCTextureCache.Shared con un gioco o a un livello specifico della modalità CCTextureCache istanza comporta alcuni trame persistenza tramite il gioco intera e alcuni in corso lo scaricamento come terminano livelli, simile al diagramma presentato all'inizio di questa Guida")




## <a name="summary"></a>Riepilogo

Questa guida viene illustrato come utilizzare la `CCTextureCache` classe per le prestazioni di runtime e sull'utilizzo della memoria saldo. `CCTexturCache.SharedTextureCache` è possibile in modo esplicito o implicito consente di caricare e memorizzare nella cache delle trame per la durata dell'applicazione, mentre `CCTextureCache` istanze possono essere usate per scaricare le trame per ridurre l'utilizzo della memoria.

## <a name="related-links"></a>Collegamenti correlati

- [https://github.com/mono/CocosSharp](https://github.com/mono/CocosSharp)
- [/api/type/CocosSharp.CCTextureCache/](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
