---
title: Introduzione allo sviluppo di giochi con MonoGame
description: In questa procedura dettagliata multiparte viene illustrato come creare una semplice applicazione 2D utilizzando MonoGame.  Copre gioco comune di gioco i concetti di programmazione, ad esempio immagini, input, entità e fisica.
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 46cc3a7e3bb6c58e04626c9d2cc9437c16ba19f5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="introduction-to-game-development-with-monogame"></a>Introduzione allo sviluppo di giochi con MonoGame

_In questa procedura dettagliata multiparte viene illustrato come creare una semplice applicazione 2D utilizzando MonoGame.  Copre gioco comune di gioco i concetti di programmazione, ad esempio immagini, input, entità e fisica._

Questo articolo descrive la tecnologia MonoGame API per l'esecuzione di giochi multipiattaforma. Per un elenco completo delle piattaforme, vedere il [sito Web MonoGame](http://www.monogame.net/). In questa esercitazione verrà usato c# per esempi di codice, sebbene anche MonoGame è completamente funzionale con F #.

MonoGame è una multipiattaforma, API che fornisce grafica, la gestione dello stato di gioco, audio, input e una pipeline di contenuto per l'importazione di asset con accelerazione hardware. La maggior parte dei motori di giochi, a differenza di MonoGame non fornire o imporre qualsiasi struttura di progetto o modello.  Anche se questo significa che gli sviluppatori sono liberi organizzare il proprio codice che desiderano, significa anche che è necessario un po' di codice di installazione al primo avvio di un nuovo progetto.

La prima sezione di questa procedura dettagliata si concentra sulla configurazione di un progetto vuoto. L'ultima sezione vengono illustrate la scrittura di tutta la logica del gioco e il contenuto di più di cui sarà su più piattaforme.

Al termine di questa procedura dettagliata, si avrà creato un semplice gioco in cui il giocatore possibile controllare un personaggio input tramite tocco.  Anche se non si tratta tecnicamente un gioco completo (perché ha non win o perdere condizioni), vengono illustrati numerosi concetti di sviluppo di giochi e può essere utilizzato come base per molti tipi di giochi. 

Di seguito viene illustrato il risultato di questa procedura dettagliata:

![Animazione di carattere del gioco di esempio che segue il puntatore del mouse](images/image1.gif)

## <a name="monogame-and-xna"></a>Monogame e XNA

La libreria MonoGame deve simulare una libreria di Microsoft XNA sia sintassi e funzionalità.  Tutti gli oggetti MonoGame esistano nello spazio dei nomi Microsoft.Xna – consentendo gran parte del codice XNA da utilizzare in MonoGame senza modifiche. 

Gli sviluppatori esperti in XNA saranno già familiari con la sintassi del MonoGame e gli sviluppatori che desiderano per ulteriori informazioni sull'utilizzo di MonoGame saranno in grado di fare riferimento esistente procedure dettagliate XNA online, la documentazione dell'API e discussioni di carattere.


## <a name="walkthrough-parts"></a>Parti di questa procedura dettagliata

- [Parte 1: creazione di un progetto MonoGame multipiattaforma](~/graphics-games/monogame/introduction/part1.md)
- [Parte 2: implementazione di WalkingGame](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>Collegamenti correlati

- [Progetto MonoGame WalkingGame (esempio)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
- [Tipi di carattere XNB iOS](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Content/fonts)
- [XNB Android di tipi di carattere](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Assets/Content/fonts)
- [MonoGame Android in NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [IOS MonoGame in NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentazione MonoGame API](http://www.monogame.net/documentation/?page=main)
