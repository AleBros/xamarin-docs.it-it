---
title: Introduzione allo sviluppo di giochi con MonoGame
description: Questa procedura dettagliata più parte illustra come creare una semplice applicazione 2D con MonoGame.  Viene descritto come comune gioco i concetti di programmazione, ad esempio grafica, input, di gioco le entità e fisica.
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 4ab98d59bc74672f9531f4dbd3c33a6270582612
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386270"
---
# <a name="introduction-to-game-development-with-monogame"></a>Introduzione allo sviluppo di giochi con MonoGame

_Questa procedura dettagliata più parte illustra come creare una semplice applicazione 2D con MonoGame.  Viene descritto come comune gioco i concetti di programmazione, ad esempio grafica, input, di gioco le entità e fisica._

Questo articolo descrive la tecnologia per creare giochi multipiattaforma MonoGame API. Per un elenco completo delle piattaforme, vedere la [sito Web MonoGame](http://www.monogame.net/). Questa esercitazione verrà utilizzato C# per esempi di codice, anche se è completamente funzionale con MonoGame F# nonché.

MonoGame è multi-piattaforma, API, che fornisce grafica, la gestione dello stato di giochi, audio, input e una pipeline di contenuti per l'importazione gli asset con accelerazione hardware. A differenza di maggior parte dei motori di gioco, MonoGame non offre né imporre una struttura di progetto o modello.  Mentre ciò significa che gli sviluppatori sono liberi di organizzare il codice che desiderano, significa anche che è necessario un po' di codice di installazione durante l'avvio prima di tutto un nuovo progetto.

La prima sezione di questa procedura dettagliata è incentrato su come configurare un progetto vuoto. Nell'ultima sezione illustra la scrittura di tutti i nostri logica del gioco e il contenuto: più di cui sarà cross-platform.

Al termine di questa procedura dettagliata, si verrà creato un semplice gioco in cui il lettore possibile controllare un personaggio con input di tocco.  Anche se questo non è tecnicamente un gioco completo (perché non ha alcun vince o perde condizioni), vengono illustrati numerosi concetti di sviluppo di giochi e può essere utilizzato come base per molti tipi di giochi. 

Di seguito viene illustrato il risultato di questa procedura dettagliata:

![Animazione di carattere del gioco di esempio che segue il puntatore del mouse](images/image1.gif)

## <a name="monogame-and-xna"></a>Monogame e XNA

Consente di simulare libreria XNA di Microsoft nella sintassi e di funzionalità della libreria MonoGame.  Tutti gli oggetti MonoGame esistano Microsoft.Xna nello spazio dei nomi, che consente di gran parte del codice XNA da utilizzare in MonoGame senza modifiche. 

Gli sviluppatori esperti in XNA saranno già familiari con la sintassi del MonoGame e sviluppatori che cercano altre informazioni sull'uso con MonoGame saranno in grado di fare riferimento a procedure dettagliate per XNA online esistente, la documentazione dell'API e discussioni.


## <a name="walkthrough-parts"></a>Parti di questa procedura dettagliata

- [Parte 1: creazione di un progetto MonoGame multipiattaforma](~/graphics-games/monogame/introduction/part1.md)
- [Parte 2: implementazione di WalkingGame](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>Collegamenti correlati

- [Progetto MonoGame WalkingGame (esempio)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
- [I tipi di carattere XNB iOS](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Content/fonts)
- [XNB i tipi di carattere Android](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Assets/Content/fonts)
- [MonoGame Android in NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [IOS MonoGame in NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentazione API MonoGame](http://www.monogame.net/documentation/?page=main)
