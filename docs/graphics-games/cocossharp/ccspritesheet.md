---
title: Miglioramento della frequenza dei fotogrammi con CCSpriteSheet
description: CCSpriteSheet fornisce funzionalità per la combinazione e l'utilizzo di molti file di immagine in una trama. Riducendo il numero di trama, è possibile migliorare i tempi di caricamento del gioco e frequenza dei fotogrammi.
ms.prod: xamarin
ms.assetid: A1334030-750C-4C60-8B84-1A8A54B0D00E
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 9487ddf5ccdb1d0caf820b10446eaff0f80a97ed
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="improving-frame-rate-with-ccspritesheet"></a>Miglioramento della frequenza dei fotogrammi con CCSpriteSheet

_CCSpriteSheet fornisce funzionalità per la combinazione e l'utilizzo di molti file di immagine in una trama. Riducendo il numero di trama, è possibile migliorare i tempi di caricamento del gioco e frequenza dei fotogrammi._

Molti dei giochi richiedono interventi di ottimizzazione di eseguire in modo uniforme e caricare rapidamente sull'hardware per dispositivi mobili. La `CCSpriteSheet` classe consente di risolvere molti problemi comuni di prestazioni rilevati da CocosSharp giochi. Questa guida illustra problemi comuni di prestazioni e su come risolverli utilizzando la `CCSpriteSheet` classe.


## <a name="what-is-a-sprite-sheet"></a>Che cos'è un foglio sprite?

Oggetto *foglio sprite*, che può anche essere indicato come un *trama*, un'immagine che combina più immagini in un file. Ciò può migliorare le prestazioni di runtime, nonché i tempi di caricamento del contenuto.

Ad esempio, l'immagine seguente è un foglio di sprite semplice creato da tre immagini separate. Le immagini di singoli possono essere di qualsiasi dimensione e il foglio sprite risultante non è necessario essere riempito completamente:

![](ccspritesheet-images/image1.png "Le immagini di singoli possono essere di qualsiasi dimensione e il foglio sprite risultante non è necessario essere riempito completamente")


### <a name="render-states"></a>Eseguire il rendering degli Stati

Gli oggetti visivi CocosSharp (ad esempio `CCSprite`) semplificano il codice per il rendering su tradizionale codice per il rendering dell'API grafico, ad esempio MonoGame o OpenGL, che richiedono la creazione di buffer dei vertici (come descritto nella [grafica 3D con Vertici MonoGame](~/graphics-games/monogame/3d/part2.md) Guida). Nonostante la semplicità, CocosSharp non elimina il costo dell'impostazione *rendering stati*, che sono il numero di volte che il codice per il rendering deve passare trame o altri Stati di rendering.

Codice interno del CocosSharp esegue il rendering di ogni elemento visivo in sequenza, attraversando iniziando dalla struttura ad albero visuale corrente `CCScene`. Si consideri ad esempio la scena seguente:

![](ccspritesheet-images/image2.png "Prendere in considerazione questa scena")

CocosSharp renderebbe quattro stelle in sequenza:

![](ccspritesheet-images/image3.png "CocosSharp il rendering di quattro stelle in sequenza")

Poiché ogni `CCSprite` utilizza la stessa trama CocosSharp possibile raggruppare tutti i quattro stelle. Questo codice richiede assegnazione di rendering di un solo stato (assegnazione della trama a stella) per ogni frame. Questo scenario è molto efficiente.

Naturalmente, giochi pochissime utilizzano solo un'immagine. Scena seguente introduce un elemento grafico pianeta:

![](ccspritesheet-images/image4.png "Questa scena introduce un elemento grafico pianeta")

In teoria CocosSharp deve essere disegnato sprite tutti tramite un'immagine prima (ad esempio gli asterischi), quindi nella parte restante di sprite tramite l'altra immagine (pianeta):

![](ccspritesheet-images/image5.png "In teoria CocosSharp deve essere disegnato sprite tutti con un'immagine, ad esempio gli asterischi, quindi nella parte restante di sprite tramite l'altra immagine del mondo")

L'ordinamento sopra richiede due stati di rendering: uno per quello a stella, prima del primo pianeta.

Se tutti `CCSprite` istanze sono elementi figlio dello stesso `CCNode`, quindi CocosSharp consente di ottimizzare l'ordine di disegno per ridurre le modifiche dello stato di rendering. Se, invece, il `CCSprite` istanze sono organizzate in modo che non è in grado di ottimizzare il rendering CocosSharp (ad esempio se fanno parte di entità diversa `CCNode` istanze), quindi l'ordine potrebbe non essere ottimale. Di seguito viene illustrato l'ordine di disegno possibili peggiore, risultante in cinque stati di rendering:

![](ccspritesheet-images/image6.png "Viene illustrato l'ordine di disegno possibili peggiore, risultante in cinque stati di rendering")

Stati di rendering possono essere difficili da ottimizzare perché l'ordine di disegno deve rispettare la struttura ad albero visuale di `CCNode` istanze. Questa struttura è spesso strutturata in modo da essere semplice da utilizzare (ad esempio, le entità che contiene i relativi elementi figlio visivi) o organizzata a causa di layout visivo desiderato come definito da un esperto.

Naturalmente, la situazione ideale è disporre di uno stato di rendering singolo, nonostante entrambe abbiano più immagini. Giochi CocosSharp scopo combinando tutte le immagini in un singolo file, quindi il caricamento di un file (insieme ai relativi che accompagna **. plist** file) in un `CCSpriteSheet`. Utilizzando la `CCSpriteSheet` classe diventa ancora più importante per i giochi che hanno un numero elevato di immagini, o che hanno molto complicato layout. 

### <a name="load-times"></a>Tempi di caricamento

Combinazione di più immagini in un unico file migliora anche tempi di caricamento di un gioco per diversi motivi:

 - La combinazione di più immagini in un singolo file può ridurre il numero complessivo di pixel utilizzati tramite compressione efficienti
 - Caricamento dei file di un numero inferiore indica che implica un sovraccarico minore per ogni file, ad esempio l'analisi delle intestazioni PNG
 - Caricamento di file meno richiede minore seek tempo, è importante per il supporto basato su disco, ad esempio DVD e dischi rigidi computer tradizionale

## <a name="using-ccspritesheet-in-code"></a>Utilizzo CCSpriteSheet nel codice

Per creare un `CCSpriteSheet` istanza, il codice deve fornire un'immagine e un file che definisce le aree dell'immagine da utilizzare per ogni fotogramma. L'immagine può essere caricato come un **PNG** oppure **.xnb** file (se si utilizza il [Pipeline contenuto](~/graphics-games/cocossharp/content-pipeline/index.md)). Il file che definisce i frame è un **. plist** file che può essere creati manualmente o *TexturePacker* (che verrà descritta di seguito).

Applicazione di esempio, che [può essere scaricata qui](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/), crea il `CCSpriteSheet` da un **PNG** e **. plist** file utilizzando il codice seguente:

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

Una volta caricati, il `CCSpriteSheet` contiene un `List` di `CCSpriteFrame` istanze: ogni istanza corrispondente a una delle immagini di origine utilizzate per creare l'intero foglio. In caso del **SpriteSheetDemo** progetto, il `CCSpriteSheet` contiene tre immagini. Il **. plist** file può essere controllate in Visual Studio per Mac o in qualsiasi editor di testo per visualizzare le immagini sono disponibili. Se si visualizza il **. plist** file in un editor di testo è possibile osservare i tre frame (sezioni omesse per evidenziare i nomi delle chiavi):


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

È possibile utilizzare il `Find` metodo per trovare i frame in base al nome, come indicato di seguito (codice omesso per enfatizzare `CCSpriteFrame` utilizzo):


```csharp
CCSpriteFrame frame;
...
frame = sheet.Frames.Find(item=>item.TextureFilename == "farBackground.png"); 
CCSprite sprite = new CCSprite (frame); 
...
```

Poiché il `CCSprite` costruttore può accettare un `CCSpriteFrame` parametro, il codice non deve mai esaminare i dettagli del `CCSpriteFrame`, ad esempio quali trama Usa o l'area dell'immagine nel foglio di sprite master.


## <a name="creating-a-sprite-sheet-plist"></a>Creazione di un foglio. plist sprite

Il file con estensione plist è un file basato su xml, che può essere creato e modificato manualmente. Analogamente, i programmi di modifica delle immagini consente di combinare più file in un file di dimensioni maggiore. Dopo la creazione e gestione fogli sprite possono richiedere molto tempo, verranno esaminati il programma TexturePacker che è possibile esportare i file nel formato CocosSharp. TexturePacker offre gratuito e una versione "Pro" ed è disponibile per Windows e Mac OS. Il resto di questa guida può essere seguito usando la versione gratuita. 

Può essere TexturePacker [scaricato dal sito Web TexturePacker](https://www.codeandweb.com/texturepacker). All'apertura, TexturePacker non dispone di un progetto caricato. La schermata inizia consente di aggiungere Sprite, aprire progetti recenti (se sono stati creati altri progetti) e selezionare il formato da utilizzare per il foglio sprite. CocosSharp utilizza il formato di dati Cocos2D:

![](ccspritesheet-images/image7.png "CocosSharp utilizza il formato di dati Cocos2D")

File di immagine (ad esempio **PNG**) possono essere aggiunti ai TexturePacker trascinamento li da Esplora risorse in Windows o Finder su Mac. TexturePacker Aggiorna l'anteprima del foglio sprite automaticamente ogni volta che viene aggiunto un file:

![](ccspritesheet-images/image8.png "L'anteprima del foglio sprite TexturePacker automaticamente aggiornato ogni volta che viene aggiunto un file")

Per esportare un foglio sprite, fare clic su di **foglio sprite pubblica** e selezionare un percorso per il foglio sprite. TexturePacker salverà un file con estensione plist e un file di immagine.

Per utilizzare i file risultanti, aggiungere il PNG e l'estensione plist a un progetto CocosSharp. Per informazioni sull'aggiunta di file a CocosSharp progetti, vedere la [BouncingGame Guida](~/graphics-games/cocossharp/bouncing-game.md). Dopo aver aggiunto i file, può essere caricati in un `CCSpriteSheet` come illustrato in precedenza nel codice precedente:

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

### <a name="considerations-for-maintaining-a-texturepacker-sprite-sheet"></a>Considerazioni per la gestione di un foglio di sprite TexturePacker

Come vengono sviluppati giochi, artisti possono aggiungere, rimuovere o modificare immagini. Qualsiasi modifica richiede un foglio sprite aggiornato. Manutenzione foglio sprite facilita le considerazioni seguenti:

 - Mantenere i file originali (i file utilizzati per creare i fogli sprite) in una cartella nel progetto e assicurarsi che vengono aggiunti al controllo della versione. Questi file è necessario creare nuovamente il foglio sprite ogni volta che viene apportata una modifica.
 - Non aggiungere i file originali a Visual Studio per Mac e Visual Studio, o se vengono aggiunti, impostare il **azione di compilazione** a **Nessuno**. Se i file vengono aggiunti e sono specifiche della piattaforma **azione di compilazione**, quindi aumentano inutilmente le dimensioni di file dell'applicazione risultante.
 - È consigliabile utilizzare *smart cartelle* in TexturePacker. Cartelle smart aggiungono automaticamente immagini di contenuto al foglio sprite. Questa funzionalità consente di risparmiare molto tempo quando lo sviluppo di giochi con un numero elevato di immagini. 

    ![](ccspritesheet-images/image9.png "Questa funzionalità consente di risparmiare molto tempo quando lo sviluppo di giochi con un numero elevato di immagini")
 - È consigliabile monitorare le dimensioni della trama di sprite foglio. Alcuni componenti hardware phone meno recenti non supporta le dimensioni della trama superiore a 2048 x 2048. Inoltre, un'immagine a 32 bit di 2048 x 2048 utilizza quasi 17 MB di RAM: una notevole quantità di memoria.
 - TexturePacker non include cartelle nei nomi sprite per impostazione predefinita, sono possibili conflitti di nome. È consigliabile decidere se includere cartella nomi o non all'inizio dello sviluppo. Giochi di dimensioni maggiori è consigliabile utilizzando i nomi di cartella per evitare conflitti. Per includere i percorsi delle cartelle, fare clic su **Mostra avanzate** nel **dati** sezione e controllare **nome della cartella anteporre**. 

    ![](ccspritesheet-images/image10.png "Per includere i percorsi delle cartelle, fare clic su Mostra Avanzate nella sezione dei dati e controllare il nome di cartella anteporre")

## <a name="summary"></a>Riepilogo

Questa guida viene descritto come creare e usare la `CCSpriteSheet` classe. Viene inoltre illustrato come creare file che possono essere caricati in `CCSpriteSheet` istanze utilizzando il programma TexturePacker.

## <a name="related-links"></a>Collegamenti correlati

- [CCSpriteSheet](https://developer.xamarin.com/api/type/CocosSharp.CCSpriteSheet/)
- [Demo completa (esempio)](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/)
