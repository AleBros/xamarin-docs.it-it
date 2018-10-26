---
title: Usando CCTextureCache la memorizzazione nella cache della trama
description: Classe CCTextureCache di CocosSharp offre un metodo standard per organizzare, cache e scaricare il contenuto. È particolarmente utile per i giochi di grandi dimensioni che potrebbero non rientrare interamente dalla RAM, semplificando il processo di raggruppamento e l'eliminazione delle trame.
ms.prod: xamarin
ms.assetid: 1B5F3F85-9E68-42A7-B516-E90E54BA7102
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 232363d6ce1cb93499716b2c1247c48403cf6cea
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117382"
---
# <a name="texture-caching-using-cctexturecache"></a>Usando CCTextureCache la memorizzazione nella cache della trama

_Classe CCTextureCache di CocosSharp offre un metodo standard per organizzare, cache e scaricare il contenuto. È particolarmente utile per i giochi di grandi dimensioni che potrebbero non rientrare interamente nello RAM, semplificando il processo di raggruppamento e l'eliminazione delle trame._

Il `CCTextureCache` classe è una parte essenziale di sviluppo di giochi con CocosSharp. Uso di CocosSharp la maggior parte dei giochi il `CCTextureCache` dell'oggetto, anche se non in modo esplicito, tutti i metodi di CocosSharp usano internamente una *condivisa* cache delle trame.

Questa guida vengono illustrati il `CCTextureCache` e il motivo è importante per lo sviluppo di giochi. In particolare viene descritto come:

 - Il motivo per cui è rilevante la memorizzazione nella cache delle trame
 - Ciclo di vita di trama
 - Usando SharedTextureCache
 - Caricamento e pre-caricamento con AddImage Lazy
 - Eliminazione di trame


## <a name="why-texture-caching-matters"></a>Il motivo per cui è rilevante la memorizzazione nella cache delle trame

La memorizzazione nella cache della trama è un aspetto importante nello sviluppo di giochi come il caricamento della trama è un'operazione impegnativa e trame richiedono una notevole quantità di RAM in fase di esecuzione.

Come per qualsiasi operazione su file, il caricamento delle trame dal disco può essere un'operazione costosa. Durante il caricamento della trama può richiedere più tempo se il file da caricare richiede l'elaborazione, ad esempio essere decompressi (come avviene per le immagini png e jpg). La memorizzazione nella cache della trama, è possibile ridurre il numero di volte che l'applicazione deve caricare i file dal disco.

Come indicato in precedenza, le trame occupano anche una grande quantità di memoria di runtime. Ad esempio un'immagine di sfondo ridimensionata per la risoluzione di un iPhone 6 (1344 750) occuperebbe 4 MB di RAM, anche se il file PNG è solo di alcuni kilobyte di dimensioni. La memorizzazione nella cache della trama offre un modo per condividere i riferimenti di trama all'interno di un'app e anche un modo semplice per scaricare tutto il contenuto durante la transizione tra stati diversi del gioco.


## <a name="texture-lifespan"></a>Ciclo di vita di trama

Le trame CocosSharp potrebbero essere mantenute in memoria per l'intera durata dell'esecuzione di un'app oppure potrebbe trattarsi di breve durate. Per ridurre al minimo della memoria utilizzo un'app deve eliminare le trame quando non servono più. Naturalmente, ciò significa che le trame possono essere eliminate e nuovamente caricate in un secondo momento, che consentono di aumentare i tempi di caricamento o influire negativamente sulle prestazioni durante il caricamento. 

Il caricamento di trama è spesso necessario raggiungere un compromesso tra i tempi di caricamento e utilizzo memoria / le prestazioni di runtime. Giochi che utilizzano una piccola quantità di memoria trame possono mantenere tutte le trame in memoria in base alle necessità, ma potrebbe essere necessario scaricare le trame per liberare spazio su giochi di dimensioni maggiori.

Il diagramma seguente illustra un semplice gioco che carica le trame in base alle esigenze e li mantiene in memoria per l'intera durata dell'esecuzione:

![](texture-cache-images/image1.png "Il diagramma seguente illustra un semplice gioco che carica le trame in base alle esigenze e li mantiene in memoria per l'intera durata dell'esecuzione")

Le prime due barre rappresentano le trame che sono necessari immediatamente al termine dell'esecuzione del gioco. I seguenti tre barre rappresentano le trame per ogni livello, caricato in base alle esigenze.

Se il gioco è grande abbastanza viene infine carica sufficiente trame fino a riempire tutte RAM fornito dal dispositivo e del sistema operativo. Per risolvere questo problema, un gioco può scaricare i dati di trama quando non è più necessario. Ad esempio, il diagramma seguente mostra un gioco che scarica Level1Texture quando non è più necessario, e poi carica Level2Texture per il livello successivo. Il risultato finale è che solo tre le trame vengono mantenute in memoria in qualsiasi momento: 

![](texture-cache-images/image2.png "Il risultato finale è che solo tre le trame vengono mantenute in memoria in qualsiasi momento")


Il diagramma illustrato in precedenza indica che utilizzo della memoria trama può essere ridotto limitando lo scaricamento, ma ciò potrebbe richiedere tempi di caricamento aggiuntivi se si decide di riprodurre un livello di un lettore. È anche importante notare che le trame UITexture e MainCharacter siano caricate e non vengono mai scaricate. Ciò implica che le trame sono necessari in tutti i livelli, in modo che vengono sempre mantenuti in memoria. 


## <a name="using-sharedtexturecache"></a>Usando SharedTextureCache

CocosSharp memorizza le trame automaticamente durante il caricamento tramite il `CCSprite` costruttore. Ad esempio il codice seguente crea solo un'istanza di trama:


```csharp
for (int i = 0; i < 100; i++)
{
    CCSprite starSprite = new CCSprite ("star.png");
    starSprite.PositionX = i * 32;
    this.AddChild (starSprite);
} 
```

CocosSharp memorizza automaticamente il `star.png` trama per evitare l'alternativa dispendiosa della creazione di numerosi identici `CCTexture2D` istanze. Questa operazione viene eseguita `AddImage` viene chiamato su un oggetto condiviso `CCTextureCache` dell'istanza, in particolare `CCTextureCache.SharedTextureCache.Shared`. Per comprendere come il `SharedTextureCache` viene usato possibile esaminare il codice seguente è funzionalmente identico alla chiamata di `CCSprite` costruttore con un parametro di stringa:


```

CCSprite starSprite = new CCSprite ();
 starSprite.Texture = CCTextureCache.SharedTextureCache.AddImage ("star.png");
```

`AddImage` Controlla se il file di argomento (in questo caso `star.png`) è già stato caricato. In questo caso, viene restituita l'istanza memorizzata nella cache. Se quindi non viene caricato dal file system e un riferimento per la trama viene archiviato internamente per le successive `AddImage` chiamate. In altre parole il `star.png` immagine venga caricata solo una volta e le chiamate successive non richiedono alcun accesso al disco aggiuntive o memoria trame aggiuntive.


## <a name="lazy-loading-vs-pre-loading-with-addimage"></a>Caricamento e pre-caricamento con AddImage Lazy

`AddImage` Consente di scrivere lo stesso codice che la trama richiesta è già stata caricata. Ciò significa che il contenuto non verrà caricato finché non è necessario; Tuttavia, ciò può causare anche problemi di prestazioni in fase di esecuzione a causa di un contenuto imprevedibile il caricamento.

Si consideri ad esempio un gioco in cui può essere aggiornato arma del lettore. Con l'aggiornamento, l'arma e i proiettili visibilmente cambieranno, generando nuove trame in uso. Se il contenuto viene effettuato il caricamento lazy quindi le trame associate armi aggiornati non verranno caricate inizialmente, ma piuttosto in un secondo momento quando il giocatore acquisisce gli aggiornamenti. 

Il caricamento di metà gioco può causare il gioco per giungere *pop*, ovvero un blocco brevi ma percettibile in esecuzione. Per evitare questo problema, il codice può stimare quali trame potrebbero essere necessario preliminare e pre-caricarli. Ad esempio, di seguito consente di precaricare le trame:


```csharp
void PreLoadImages()
{
    var cache = CCTextureCache.SharedTextureCache;

    cache.AddImage ("powerup1.png");
    cache.AddImage ("powerup2.png");
    cache.AddImage ("powerup3.png");

    cache.AddImage ("enemy1.png");
    cache.AddImage ("enemy2.png");
    cache.AddImage ("enemy3.png");

    // pre-load any additional content here to 
    // prevent pops at runtime
} 
```

Il precaricamento dei può comportare uno spreco della memoria e può aumentare il tempo di avvio. Ad esempio, il giocatore mai effettivamente può ottenere un power-up rappresentato dal `powerup3.png` trama, in modo che venga caricato inutilmente. Naturalmente è possibile un costo necessario pagare evitare un potenziale pop nella modalità di gioco, pertanto è in genere consigliabile precaricamento contenuto se rientra nella RAM.


## <a name="disposing-textures"></a>Eliminazione di trame

Se un gioco non richiede più memoria di trama rispetto a quella disponibile sul dispositivo specifica minimo trame non sono più necessario in fase di eliminazione. D'altra parte, giochi più grandi potrebbe essere necessario liberare memoria trame per liberare spazio per nuovi contenuti. Ad esempio un gioco potrebbe usare una grande quantità di memoria trame per un ambiente di archiviazione. Se l'ambiente viene usato solo in un livello specifico deve essere scaricato quando termina il livello.


### <a name="disposing-a-single-texture"></a>Eliminazione di una trama singola

Rimozione di una trama singola prima di tutto è necessario chiamare il `Dispose` metodo, quindi la rimozione manuale dal `CCTextureCache`.

L'esempio seguente illustra come rimuovere completamente un sprite in background insieme ai relativi trama:


```csharp
void DisposeBackground()
{
    // Assuming this is called from a CCLayer:
    this.RemoveChild (backgroundSprite);

    CCTextureCache.SharedTextureCache.RemoveTexture (backgroundsprite.Texture);

    backgroundSprite.Texture.Dispose ();
} 
```

Eliminazione direttamente le trame può essere efficace durante la gestione di un numero ridotto di trame, ma questo può diventare soggetta a errori quando si lavora con set di trame più grandi.

Le trame possono essere raggruppate in personalizzata (non condiviso) `CCTextureCache` istanze per semplificare la pulizia di trama.

Ad esempio, si consideri un esempio in cui il contenuto sia stato precaricato con un specifico livello `CCTextureCache` istanza. Il `CCTextureCache` istanza può essere definita nella classe che definisce il livello (che può essere un `CCLayer` o `CCScene`):


```csharp
CCTextureCache levelTextures; 
```

Il `levelTextures` istanza può quindi essere utilizzata per precaricare le trame specifiche a livello di: 


```

void PreloadLevelTextures(CCApplication application)
{
    levelTextures = new CCTextureCache (application);

    levelTextures.AddImage ("Background.png");
    levelTextures.AddImage ("Foreground.png");
    levelTextures.AddImage ("Enemy1.png");
    levelTextures.AddImage ("Enemy2.png");
    levelTextures.AddImage ("Enemy3.png");

    levelTextures.AddImage ("Powerups.png");
    levelTextures.AddImage ("Particles.png");
} 
```

Infine quando termina, il livello di trame possono trovarsi tutti è state eliminate in una sola volta tramite la `CCTextureCache`:


```csharp
void EndLevel()
{
    levelTextures.Dispose ();
    // Perform any other end-level cleanup
} 
```

Il metodo Dispose eliminerà tutte le trame interne, cancellare la memoria usata da queste trame. La combinazione `CCTextureCache.Shared` con uno specifico di modalità di gioco o a un livello `CCTextureCache` istanza comporta alcuni trame persistenza tramite il gioco intero e alcuni in corso lo scaricamento come terminano i livelli, simile al diagramma presentato all'inizio di questa guida: 

![](texture-cache-images/image2.png "La combinazione di CCTextureCache.Shared con un gioco o a un livello specifico della modalità CCTextureCache istanza restituisce alcuni trame persistenza tramite il gioco intero e alcuni in corso lo scaricamento come terminano i livelli, simile al diagramma presentato all'inizio di questa Guida")




## <a name="summary"></a>Riepilogo

Questa guida illustra come usare il `CCTextureCache` classe per le prestazioni di runtime e sull'utilizzo della memoria saldo. `CCTexturCache.SharedTextureCache` è possibile in modo esplicito o utilizzata in modo implicito per il caricamento e memorizzare nella cache le trame per tutta la durata dell'applicazione, mentre `CCTextureCache` istanze possono essere utilizzate per scaricare le trame per ridurre l'utilizzo della memoria.

## <a name="related-links"></a>Collegamenti correlati

- [https://github.com/mono/CocosSharp](https://github.com/mono/CocosSharp)
- [/api/type/CocosSharp.CCTextureCache/](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
