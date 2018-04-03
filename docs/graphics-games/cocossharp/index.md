---
title: CocosSharp
description: Questo documento include collegamenti ad articoli diversi sullo sviluppo di gioco con CocosSharp.
ms.topic: article
ms.prod: xamarin
ms.assetid: 5E72869D-3541-408B-AB64-D34C777AFB79
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/29/2018
ms.openlocfilehash: d61f74aefad09935b957b15ebb0daafb61dab8d5
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="cocossharp"></a>CocosSharp

_CocosSharp è una libreria per la creazione di giochi 2D con c# e F #. È una porta .NET del motore di Cocos2D più diffuso._

## <a name="introduction-to-cocossharp"></a>Introduzione a CocosSharp

Il motore di giochi 2D CocosSharp offre una tecnologia per l'esecuzione di giochi multipiattaforma. Per un elenco completo delle piattaforme supportate, vedere il [CocosSharp wiki su GitHub](https://github.com/mono/CocosSharp/wiki).
Queste guide usare c# per gli esempi di codice, sebbene anche CocosSharp sarà completamente funzionale con F #.

Viene fornito il nucleo di CocosSharp dal [MonoGame framework](http://www.monogame.net/), che è una multipiattaforma, API che fornisce grafica, la gestione dello stato di gioco, audio, input e una pipeline di contenuto per l'importazione di asset con accelerazione hardware.
CocosSharp è un livello di astrazione efficiente particolarmente utile per i giochi 2D.
Inoltre, giochi più grande possono eseguire le proprie ottimizzazioni all'esterno le librerie di base giochi aumento della complessità. In altre parole, CocosSharp fornisce una combinazione di facilità di utilizzo e prestazioni, consentendo agli sviluppatori di iniziare rapidamente senza limitare la dimensione del gioco o della complessità.

In questo video pratico viene illustrato come creare un semplice CocosSharp multipiattaforma gioco:

> [!Video https://channel9.msdn.com/Shows/Visual-Studio-Toolbox/Developing-Cross-platform-2D-Games-in-C-and-CocosSharp/player]

## <a name="bouncinggamegraphics-gamescocossharpbouncing-gamemd"></a>[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)

![BouncingGame](images/bouncing-game.png "BouncingGame")

Questa guida descrive BouncingGame, inclusa la modalità di lavoro con contenuto di gioco, vari elementi visivi utilizzati per compilare un gioco, aggiungere logica del gioco e altro ancora.

## <a name="fruity-falls-gamegraphics-gamescocossharpfruity-fallsmd"></a>[Gioco cade Fruity](~/graphics-games/cocossharp/fruity-falls.md)

![Rientra Fruity gioco screenshot](images/fruity-falls.png "Fruity cade gioco schermata")

Questa guida viene descritto il gioco, Fruity cade copertura CocosSharp comuni e i concetti di sviluppo di giochi, ad esempio fisica, gestione dei contenuti, lo stato del gioco e progettazione di gioco.  

## <a name="coin-time-gamegraphics-gamescocossharpcointimemd"></a>[Offre ora gioco](~/graphics-games/cocossharp/cointime.md)

![Coin-tempo schermata di gioco](images/cointime.png "ora moneta schermata di gioco")

Ora di moneta è un platformer completo giochi per iOS e Android. L'obiettivo del gioco è per raccogliere tutti monete in un livello e quindi raggiungere la porta di uscita evitando nemici e ostacoli.

## <a name="drawing-geometry-with-ccdrawnodegraphics-gamescocossharpccdrawnodemd"></a>[Geometria di disegno con CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md)

![Forme disegnate con CCDrawNode](images/ccdrawnode.png "forme disegnate con CCDrawNode")

CCDrawNode fornisce metodi per oggetti primitivi, ad esempio linee, cerchi e triangoli.

## <a name="animating-with-ccactiongraphics-gamescocossharpccactionmd"></a>[Animazione con CCAction](~/graphics-games/cocossharp/ccaction.md)

![Un'animazione CCAction](images/ccaction.png "A CCAction animazione")

`CCAction` è una classe base che può essere usata per animare oggetti CocosSharp. Questa guida illustra incorporato `CCAction` implementazioni per le attività comuni, ad esempio il posizionamento, scalabilità e la rotazione. Esamina inoltre come creare le implementazioni personalizzate ereditando dalla `CCAction`.

## <a name="using-tiled-with-cocossharpgraphics-gamescocossharptiledmd"></a>[Uso di Tiled con CocosSharp](~/graphics-games/cocossharp/tiled.md)

![Un livello in un gioco](images/tiled.png "un livello in un gioco")

Affiancato è una potente, flessibile e applicazione obsoleta per la creazione di riquadro ortogonale e l'effetto viene eseguito il mapping per i giochi. CocosSharp fornisce integrazione incorporata per il formato di file nativi dell'affiancato.

## <a name="entities-in-cocossharpgraphics-gamescocossharpentitiesmd"></a>[Entità in CocosSharp](~/graphics-games/cocossharp/entities.md)

![Un'astronave dal gioco](images/entities.png "un'astronave dal gioco")

Il modello di entità è un metodo efficace per organizzare codice del gioco. Migliora la leggibilità, codice risulta più facile da gestire e utilizza le funzionalità predefinite padre/figlio.

## <a name="handling-multiple-resolutions-in-cocossharpgraphics-gamescocossharpresolutionsmd"></a>[Gestione più risoluzioni in CocosSharp](~/graphics-games/cocossharp/resolutions.md)

![Una griglia che rappresenta la risoluzione dello schermo](images/resolutions.png "una griglia che rappresenta la risoluzione dello schermo")

Questa guida viene illustrato come utilizzare CocosSharp per lo sviluppo di giochi visualizzati correttamente nei dispositivi di risoluzioni diverse.

## <a name="cocossharp-content-pipelinegraphics-gamescocossharpcontent-pipelineindexmd"></a>[Pipeline di contenuto CocosSharp](~/graphics-games/cocossharp/content-pipeline/index.md)

![XNB](images/content-pipeline.png "XNB")

Le pipeline contenute vengono spesso utilizzate nello sviluppo di gioco per ottimizzare il contenuto e formattare tale campo in modo che può essere caricato su determinati componenti hardware o con alcuni Framework di sviluppo di giochi.

## <a name="improving-frame-rate-with-ccspritesheetgraphics-gamescocossharpccspritesheetmd"></a>[Miglioramento della frequenza dei fotogrammi con CCSpriteSheet](~/graphics-games/cocossharp/ccspritesheet.md)

![Un albero da un CCSpriteSheet](images/ccspritesheet.png "un albero da un CCSpriteSheet")

`CCSpriteSheet` fornisce funzionalità per la combinazione e l'utilizzo di molti file di immagine in una trama. Riducendo il numero di trama, è possibile migliorare i tempi di caricamento del gioco e frequenza dei fotogrammi.

## <a name="texture-caching-using-cctexturecachegraphics-gamescocossharptexture-cachemd"></a>[Utilizzando CCTextureCache la memorizzazione nella cache della trama](~/graphics-games/cocossharp/texture-cache.md)

![Una rappresentazione del modo in cui CocosSharp memorizza nella cache le immagini](images/texture-cache.png "una rappresentazione del modo in cui CocosSharp memorizza nella cache di immagini")

Del CocosSharp `CCTextureCache` classe fornisce un modo standard per organizzare e memorizzare nella cache, scaricare il contenuto. 

## <a name="2d-math-with-cocossharpgraphics-gamescocossharpmathmd"></a>[Matematiche 2D con CocosSharp](~/graphics-games/cocossharp/math.md)

![Un'immagine da ruotare](images/math.png "un'immagine da ruotare")

Questa guida illustra 2D matematica per lo sviluppo di gioco. Utilizza CocosSharp in cui viene illustrato come eseguire attività comuni di sviluppo di giochi e spiega i calcoli dietro queste attività.

## <a name="performance-and-visual-effects-with-ccrendertexturegraphics-gamescocossharpccrendertexturemd"></a>[Prestazioni ed effetti visivi con CCRenderTexture](~/graphics-games/cocossharp/ccrendertexture.md)

![Sprite dal gioco](images/ccrendertexture.png "sprite dal gioco")

La `CCRenderTexture` classe fornisce funzionalità per il rendering di più oggetti di CocosSharp a una sola trama. Una volta creato, `CCRenderTexture` le istanze possono essere utilizzate per il rendering della grafica in modo efficiente e implementare effetti visivi.
