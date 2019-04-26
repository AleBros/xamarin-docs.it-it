---
title: Motore di gioco 2D CocosSharp
description: Questo documento include diversi articoli sullo sviluppo di giochi con CocosSharp. Contenuto collegato descrive le app di esempio, disegno, animazione e altro ancora.
ms.prod: xamarin
ms.assetid: 5E72869D-3541-408B-AB64-D34C777AFB79
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: add73360ea98d8c516e413f0cc0264f68c58d79d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61024454"
---
# <a name="cocossharp-2d-game-engine"></a>Motore di gioco 2D CocosSharp

_CocosSharp è una libreria per la creazione di giochi 2D usando C# e F#. È una porta .NET del motore di Cocos2D più diffusi._

## <a name="introduction-to-cocossharp"></a>Introduzione a CocosSharp

Il motore di gioco 2D CocosSharp fornisce la tecnologia per creare giochi multipiattaforma. Per un elenco completo delle piattaforme supportate, vedere la [CocosSharp wiki su GitHub](https://github.com/mono/CocosSharp/wiki).
Usano queste guide C# per esempi di codice, anche se è completamente funzionale con CocosSharp F# nonché.

Il nucleo di CocosSharp avviene tramite il [MonoGame framework](http://www.monogame.net/), che è a sua volta multi-piattaforma, API, che fornisce grafica, la gestione dello stato di giochi, audio, input e una pipeline di contenuti per l'importazione gli asset con accelerazione hardware.
CocosSharp è un livello di astrazione efficiente particolarmente adatto per giochi 2D.
Inoltre, giochi più grandi possono eseguire le proprie ottimizzazioni all'esterno le librerie di base di man mano che aumenta di giochi in termini di complessità. In altre parole, CocosSharp fornisce una combinazione di semplicità d'uso e le prestazioni, consentendo agli sviluppatori di iniziare a usare rapidamente senza limitare la dimensione del gioco o complessità.

In questo video pratico viene illustrato come creare un semplice CocosSharp multipiattaforma gioco:

> [!Video https://channel9.msdn.com/Shows/Visual-Studio-Toolbox/Developing-Cross-platform-2D-Games-in-C-and-CocosSharp/player]

## <a name="bouncinggamegraphics-gamescocossharpbouncing-gamemd"></a>[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)

![BouncingGame](images/bouncing-game.png "BouncingGame")

Questa guida descrive BouncingGame, incluse le procedure lavorare con i contenuti dei giochi, vari elementi visivi usati per compilare un gioco, aggiungendo la logica del gioco e altro ancora.

## <a name="fruity-falls-gamegraphics-gamescocossharpfruity-fallsmd"></a>[Gioco Fruity cade](~/graphics-games/cocossharp/fruity-falls.md)

![Gioco Fruity screenshot](images/fruity-falls.png "del gioco Fruity screenshot")

Questa guida viene descritto il gioco Fruity cade, che coprono CocosSharp comuni e i concetti di sviluppo di giochi, ad esempio fisica, la gestione dei contenuti, lo stato del gioco e progettazione di giochi.  

## <a name="coin-time-gamegraphics-gamescocossharpcointimemd"></a>[Medaglia ora gioco](~/graphics-games/cocossharp/cointime.md)

![Tempo screenshot del gioco coin](images/cointime.png "screenshot del gioco moneta ora")

Medaglia tempo è un gioco per iOS e Android di platformer completo. L'obiettivo del gioco è per raccogliere tutti i monete in un livello e quindi raggiungere la porta di uscita e si evitano nemici e ostacoli.

## <a name="drawing-geometry-with-ccdrawnodegraphics-gamescocossharpccdrawnodemd"></a>[Disegno di geometria con CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md)

![Le forme disegnate con CCDrawNode](images/ccdrawnode.png "forme disegnate con CCDrawNode")

CCDrawNode fornisce metodi per oggetti disegno di primitivi, ad esempio linee, cerchi e triangoli.

## <a name="animating-with-ccactiongraphics-gamescocossharpccactionmd"></a>[Animazione con CCAction](~/graphics-games/cocossharp/ccaction.md)

![Un'animazione con CCAction](images/ccaction.png "CCAction un'animazione")

`CCAction` è una classe di base che può essere utilizzata per aggiungere un'animazione CocosSharp oggetti. Questa guida illustra incorporati `CCAction` implementazioni per le attività comuni, ad esempio il posizionamento, il ridimensionamento e rotazione. Descrive anche come creare implementazioni personalizzate ereditando da `CCAction`.

## <a name="using-tiled-with-cocossharpgraphics-gamescocossharptiledmd"></a>[Uso di Tiled con CocosSharp](~/graphics-games/cocossharp/tiled.md)

![Un livello in un gioco](images/tiled.png "un livello in un gioco")

Affiancato è una potente, flessibile e applicazione obsoleta per la creazione di riquadro ortogonale e isometrica viene eseguito il mapping per i giochi. CocosSharp offre integrazione predefinita per il formato di file nativi dell'affiancato.

## <a name="entities-in-cocossharpgraphics-gamescocossharpentitiesmd"></a>[Entità in CocosSharp](~/graphics-games/cocossharp/entities.md)

![Un'astronave da un gioco](images/entities.png "un'astronave da un gioco")

Il modello di entità è un metodo efficace per organizzare il codice del gioco. Migliora la leggibilità, rende più facile da gestire, il codice e utilizza le funzionalità predefinite padre/figlio.

## <a name="handling-multiple-resolutions-in-cocossharpgraphics-gamescocossharpresolutionsmd"></a>[Gestione di più risoluzioni in CocosSharp](~/graphics-games/cocossharp/resolutions.md)

![Una griglia che rappresenta la risoluzione dello schermo](images/resolutions.png "una griglia che rappresenta la risoluzione dello schermo")

Questa guida viene illustrato come lavorare con CocosSharp per sviluppare giochi che consentono di visualizzare correttamente nei dispositivi di risoluzioni diverse.

## <a name="cocossharp-content-pipelinegraphics-gamescocossharpcontent-pipelineindexmd"></a>[Pipeline di contenuto CocosSharp](~/graphics-games/cocossharp/content-pipeline/index.md)

![XNB](images/content-pipeline.png "XNB")

Le pipeline di contenuti vengono spesso usate nello sviluppo di giochi per ottimizzare il contenuto e formattarla in modo che possa essere caricato su determinati componenti hardware o con determinati Framework di sviluppo di giochi.

## <a name="improving-frame-rate-with-ccspritesheetgraphics-gamescocossharpccspritesheetmd"></a>[Miglioramento della frequenza dei fotogrammi con CCSpriteSheet](~/graphics-games/cocossharp/ccspritesheet.md)

![Un albero da un CCSpriteSheet](images/ccspritesheet.png "una struttura ad albero da un CCSpriteSheet")

`CCSpriteSheet` fornisce funzionalità per la combinazione e l'utilizzo di molti file di immagine in una trama. Riducendo il numero di trama può migliorare i tempi di caricamento del gioco e frequenza dei fotogrammi.

## <a name="texture-caching-using-cctexturecachegraphics-gamescocossharptexture-cachemd"></a>[Usando CCTextureCache la memorizzazione nella cache della trama](~/graphics-games/cocossharp/texture-cache.md)

![Una rappresentazione del modo in cui CocosSharp memorizza nella cache di immagini](images/texture-cache.png "una rappresentazione del modo in cui CocosSharp memorizza nella cache di immagini")

Di CocosSharp `CCTextureCache` classe offre un metodo standard per organizzare, memorizzare nella cache e scaricare il contenuto. 

## <a name="2d-math-with-cocossharpgraphics-gamescocossharpmathmd"></a>[Matematica 2D con CocosSharp](~/graphics-games/cocossharp/math.md)

![Un'immagine da ruotare](images/math.png "un'immagine da ruotare")

Questa guida illustra la matematica 2D per lo sviluppo di giochi. Usa CocosSharp in cui viene illustrato come eseguire attività comuni di sviluppo di giochi e illustra le operazioni matematiche alla base di queste attività.

## <a name="performance-and-visual-effects-with-ccrendertexturegraphics-gamescocossharpccrendertexturemd"></a>[Prestazioni e gli effetti visivi con CCRenderTexture](~/graphics-games/cocossharp/ccrendertexture.md)

![Sprite da un gioco](images/ccrendertexture.png "sprite da un gioco")

Il `CCRenderTexture` classe fornisce funzionalità per il rendering di più oggetti CocosSharp a sola trama. Una volta creata, `CCRenderTexture` istanze possono essere usate per eseguire il rendering della grafica in modo efficiente e implementare gli effetti visivi.
