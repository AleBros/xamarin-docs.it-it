---
title: Miglioramento della frequenza dei fotogrammi con CCSpriteSheet
description: CCSpriteSheet fornisce funzionalità per la combinazione e l'uso di molti file di immagine in una trama. Riducendo il numero di trama può migliorare i tempi di caricamento del gioco e frequenza dei fotogrammi.
ms.prod: xamarin
ms.assetid: A1334030-750C-4C60-8B84-1A8A54B0D00E
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: eab6153653a8c8df2068aaaf879d84d35473c541
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123564"
---
# <a name="improving-frame-rate-with-ccspritesheet"></a>Miglioramento della frequenza dei fotogrammi con CCSpriteSheet

_CCSpriteSheet fornisce funzionalità per la combinazione e l'uso di molti file di immagine in una trama. Riducendo il numero di trama può migliorare i tempi di caricamento del gioco e frequenza dei fotogrammi._

Molti giochi richiedono attività di ottimizzazione eseguita senza intoppi e caricare rapidamente su hardware per dispositivi mobili. Il `CCSpriteSheet` classe consentono di risolvere molti problemi comuni di prestazioni rilevati da CocosSharp giochi. Questa guida vengono illustrati i problemi di prestazioni comuni e come risolverli usando il `CCSpriteSheet` classe.


## <a name="what-is-a-sprite-sheet"></a>Che cos'è un foglio di sprite?

Oggetto *foglio di sprite*, che può anche essere indicato come una *sprite*, è un'immagine che combina più immagini in un unico file. Ciò può migliorare le prestazioni di runtime, nonché tempi di caricamento del contenuto.

Ad esempio, l'immagine seguente è un foglio di sprite semplice creato da tre immagini separate. Le singole immagini possono essere di qualsiasi dimensione e il foglio di sprite risultante non deve essere riempito completamente:

![](ccspritesheet-images/image1.png "Le singole immagini possono essere di qualsiasi dimensione e il foglio di sprite risultante non deve essere riempito completamente")


### <a name="render-states"></a>Eseguire il rendering degli Stati

Gli oggetti visivi CocosSharp (ad esempio `CCSprite`) semplificano il codice per il rendering rispetto al tradizionale codice per il rendering di API con interfaccia grafico, ad esempio MonoGame o OpenGL, che richiedono la creazione di buffer dei vertici (come descritto nel [disegno di grafica 3D con Vertici in MonoGame](~/graphics-games/monogame/3d/part2.md) Guida). Nonostante la semplicità, CocosSharp non elimina il costo dell'impostazione *eseguire il rendering degli stati*, che sono il numero di volte che il codice per il rendering deve passare le trame o altri Stati correlati per il rendering.

Codice interno di CocosSharp esegue il rendering di ogni elemento visivo in sequenza, attraversando l'inizio di struttura ad albero visuale con l'attuale `CCScene`. Ad esempio, prendere in considerazione la scena seguente:

![](ccspritesheet-images/image2.png "Prendere in considerazione questa scena")

CocosSharp visualizzerebbe le quattro stelle in sequenza:

![](ccspritesheet-images/image3.png "CocosSharp visualizzerebbe le quattro stelle in sequenza")

Poiché ogni `CCSprite` Usa la trama stessa, CocosSharp possibile raggruppare tutti i quattro stelle. Questo codice richiede solo un rendering stato assegnazione (assegnazione della trama a stella) per ogni fotogramma. Questo scenario è molto efficiente.

Naturalmente, un numero molto ridotto giochi utilizzano solo un'immagine. La scena seguente presenta un grafico con copertura globale:

![](ccspritesheet-images/image4.png "Questa scena introduce un grafico con copertura globale")

In teoria CocosSharp deve essere disegnato sprite tutte tramite un'immagine prima di tutto (ad esempio le stelle), quindi nella parte restante di sprite usando l'altra immagine (il pianeta):

![](ccspritesheet-images/image5.png "Idealmente deve essere disegnato da sprite tutti prima tramite un'immagine, ad esempio le stelle, quindi nella parte restante di sprite usando l'immagine del pianeta altri CocosSharp")

Sopra l'ordinamento richiede due rendering stati: uno in uno a stella, prima del primo pianeta.

Se tutti gli oggetti `CCSprite` istanze sono elementi figlio dello stesso `CCNode`, quindi CocosSharp sarà possibile ottimizzare l'ordine di disegno per ridurre le modifiche dello stato di rendering. Se, d'altra parte, il `CCSprite` istanze sono organizzati in modo che non è in grado di ottimizzare il rendering CocosSharp (ad esempio se fanno parte di entità diversi `CCNode` istanze), quindi l'ordine potrebbe non essere ottimale. Di seguito viene illustrato l'ordine di disegno possibili peggiore, risultante in cinque stati di rendering:

![](ccspritesheet-images/image6.png "Viene illustrato l'ordine di disegno possibili peggiore, risultante in cinque stati di rendering")

Stati di rendering possono essere difficili ottimizzare perché l'ordine di disegno deve rispettare la struttura ad albero visuale di `CCNode` istanze. Questa struttura ad albero è spesso strutturato in modo da essere facile da usare (ad esempio, le entità che contiene i relativi elementi figlio visual) o organizzato a causa di un layout visivo desiderato, come definito da un esperto.

Naturalmente, la situazione ideale è avere uno stato di rendering singolo, anche se più immagini. Giochi CocosSharp possono ottenere questo risultato combinando tutte le immagini in un singolo file e quindi il caricamento di un file (insieme ai relativi che accompagna **plist** file) in un `CCSpriteSheet`. Usando il `CCSpriteSheet` classe diventa ancora più importante per i giochi che hanno un numero elevato di immagini, o che hanno molto complicato layout. 

### <a name="load-times"></a>Tempi di caricamento

La combinazione di più immagini in un unico file migliora anche tempi di caricamento di un gioco per diversi motivi:

 - Combinazione di più immagini in un singolo file può ridurre il numero complessivo di pixel utilizzata tramite compressione efficienti
 - Il caricamento dei file meno comporta meno overhead per ogni file, ad esempio l'analisi delle intestazioni. PNG
 - Il caricamento dei file meno richiede meno tempo, che è importante per i supporti su disco, ad esempio i DVD e unità disco rigido tradizionale computer di ricerca

## <a name="using-ccspritesheet-in-code"></a>Con CCSpriteSheet nel codice

Per creare un `CCSpriteSheet` istanza, il codice deve fornire un'immagine e un file che definisce le aree dell'immagine da usare per ogni fotogramma. L'immagine può essere caricato come un **PNG** oppure **.xnb** file (se si usa il [della Pipeline di contenuti](~/graphics-games/cocossharp/content-pipeline/index.md)). Il file che definisce i frame è un **plist** file che può essere creati manualmente o *TexturePacker* (che saranno illustrate di seguito).

L'app di esempio che [può essere scaricato da qui](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/), crea il `CCSpriteSheet` da un **PNG** e **plist** file usando il codice seguente:

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

Una volta caricato, il `CCSpriteSheet` contiene un `List` di `CCSpriteFrame` istanze – ogni istanza corrispondente a una delle immagini di origine utilizzate per creare l'intero foglio. Nel caso del **SpriteSheetDemo** progetto, il `CCSpriteSheet` contiene tre immagini. Il **plist** file può essere controllata in Visual Studio per Mac o in qualsiasi editor di testo per visualizzare le immagini che sono disponibili. Se si visualizza il **plist** file in un editor di testo è possibile osservare tre frame (sezioni omesse per evidenziare i nomi delle chiavi):


```csharp
...
<dict>
    <key>frames</key>
    <dict>
        <key>farBackground.png</key>
        ...
        <key>foreground.png</key>
        ...
<key>forestBackground.png</key>
...
```

È possibile usare la `Find` metodo per trovare i frame in base al nome, come indicato di seguito (codice omesso per enfatizzare `CCSpriteFrame` utilizzo):


```csharp
CCSpriteFrame frame;
...
frame = sheet.Frames.Find(item=>item.TextureFilename == "farBackground.png"); 
CCSprite sprite = new CCSprite (frame); 
...
```

Poiché il `CCSprite` costruttore può accettare una `CCSpriteFrame` parametro, il codice non deve mai esaminare i dettagli del `CCSpriteFrame`, ad esempio quale trama Usa o nell'area dell'immagine nel foglio di sprite master.


## <a name="creating-a-sprite-sheet-plist"></a>Creazione di un foglio di sprite estensione plist

Il file con estensione plist è un file basato su xml, che può essere creato e modificato manualmente. Analogamente, programmi di elaborazione grafica utilizzabile per combinare più file in un file di dimensioni maggiori. Dopo la creazione e gestione di fogli di sprite possono richiedere molto tempo, si esaminerà il programma TexturePacker che è possibile esportare i file nel formato CocosSharp. TexturePacker offre una gratuita e una versione "Plus" ed è disponibile per Windows e Mac OS. Il resto di questa guida può essere eseguito utilizzando la versione gratuita. 

Può essere TexturePacker [scaricato dal sito Web TexturePacker](https://www.codeandweb.com/texturepacker). All'apertura, TexturePacker non è caricato un progetto. La schermata inizia consente di aggiungere Sprite, aprire progetti recenti (se sono stati creati altri progetti) e selezionare il formato da utilizzare per il foglio di sprite. CocosSharp Usa il formato di dati Cocos2D:

![](ccspritesheet-images/image7.png "CocosSharp Usa il formato dati Cocos2D")

File di immagine (ad esempio **PNG**) può essere aggiunto a TexturePacker mediante trascinamento della selezione li da Esplora risorse di Windows in Windows o il Finder nel Mac. TexturePacker Aggiorna l'anteprima di foglio di sprite automaticamente ogni volta che viene aggiunto un file:

![](ccspritesheet-images/image8.png "L'anteprima di foglio di sprite TexturePacker automaticamente aggiornato ogni volta che viene aggiunto un file")

Per esportare un foglio di sprite, scegliere il **foglio di sprite pubblica** e selezionare un percorso per il foglio di sprite. TexturePacker salverà un file con estensione plist e un file di immagine.

Per usare i file risultanti, aggiungere il PNG e con estensione plist a un progetto CocosSharp. Per informazioni sull'aggiunta di file per i progetti di CocosSharp, vedere la [Guida di BouncingGame](~/graphics-games/cocossharp/bouncing-game.md). Una volta aggiunti i file, possono essere caricati in un `CCSpriteSheet` come illustrato in precedenza nel codice precedente:

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

### <a name="considerations-for-maintaining-a-texturepacker-sprite-sheet"></a>Considerazioni per la gestione di un foglio di sprite TexturePacker

Come vengono sviluppati giochi, gli artisti possono aggiungere, rimuovere o modificare arte. Qualsiasi modifica richiede un foglio di sprite aggiornato. Le considerazioni seguenti possono semplificare la manutenzione di foglio di sprite:

 - Mantenere i file originali (i file usati per creare i fogli di sprite) in una cartella nel progetto e assicurarsi che vengono aggiunti al controllo della versione. Questi file saranno necessari per ricreare il foglio di sprite ogni volta che viene apportata una modifica.
 - Non aggiungere i file originali a Visual Studio per Mac/Visual Studio o se vengono aggiunti, impostare il **Build Action** al **None**. Se i file vengono aggiunti e sono specifiche della piattaforma **azione di compilazione**, quindi si verranno aumenta inutilmente le dimensioni del file dell'app risultante.
 - È consigliabile usare *intelligente cartelle* in TexturePacker. Cartelle smart aggiungono automaticamente immagini contenute al foglio di sprite. Questa funzionalità consente di risparmiare molto tempo quando lo sviluppo di giochi con un numero elevato di immagini. 

    ![](ccspritesheet-images/image9.png "Questa funzionalità consente di risparmiare molto tempo quando lo sviluppo di giochi con un numero elevato di immagini")
 - Tenere sotto controllo le dimensioni della trama di foglio di sprite. Alcuni dispositivi hardware phone precedenti non supporta le dimensioni della trama maggiori pari a 2048x2048. Inoltre, un'immagine a 32 bit di pari a 2048x2048 Usa circa 17 MB di RAM-una quantità significativa di memoria.
 - TexturePacker non include le cartelle nei nomi di sprite per impostazione predefinita, pertanto sono possibili conflitti di nome. È consigliabile decidere se includere cartella denomina o non all'inizio dello sviluppo. Giochi di dimensioni maggiori devono prendere in considerazione utilizzando i nomi di cartella per evitare conflitti. Per includere i percorsi delle cartelle, fare clic su **Mostra impostazioni avanzate** nel **Data** sezione e controllare **nome cartella Prepend**. 

    ![](ccspritesheet-images/image10.png "Per includere i percorsi delle cartelle, fare clic su Mostra impostazioni avanzate nella sezione dei dati e controllare il nome di cartella Prepend")

## <a name="summary"></a>Riepilogo

Questa guida illustra come creare e usare il `CCSpriteSheet` classe. Viene anche illustrato come costruire i file che possono essere caricati in `CCSpriteSheet` istanze usando il programma TexturePacker.

## <a name="related-links"></a>Collegamenti correlati

- [CCSpriteSheet](https://developer.xamarin.com/api/type/CocosSharp.CCSpriteSheet/)
- [Demo completa (esempio)](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/)
