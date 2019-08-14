---
title: Introduzione allo sviluppo di giochi con monogame
description: Questa procedura dettagliata multiparte Mostra come creare una semplice applicazione 2D usando monogame.  Vengono illustrati i concetti comuni di programmazione del gioco, ad esempio grafica, input, entità del gioco e fisica.
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 1a94a29f0da6f7916eca697a24ad504a0af46b7c
ms.sourcegitcommit: 41a029c69925e3a9d2de883751ebfd649e8747cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68978385"
---
# <a name="introduction-to-game-development-with-monogame"></a>Introduzione allo sviluppo di giochi con monogame

_Questa procedura dettagliata multiparte Mostra come creare una semplice applicazione 2D usando monogame.  Vengono illustrati i concetti comuni di programmazione del gioco, ad esempio grafica, input, entità del gioco e fisica._

Questo articolo descrive la tecnologia di monogame API per la creazione di giochi multipiattaforma. Per un elenco completo delle piattaforme, vedere il [sito Web monogame](http://www.monogame.net/). Questa esercitazione userà C# per gli esempi di codice, anche se monogame è completamente F# funzionante con.

MonoGame è un'API multipiattaforma con accelerazione hardware che fornisce grafica, audio, gestione dello stato del gioco, input e una pipeline di contenuti per l'importazione di asset. Diversamente dalla maggior parte dei motori di gioco, monogame non fornisce né impone alcun modello o struttura del progetto.  Questo significa che gli sviluppatori sono liberi di organizzare il proprio codice come desiderano, ma è anche possibile che sia necessario un po' di codice di installazione quando si avvia per la prima volta un nuovo progetto.

La prima sezione di questa procedura dettagliata è incentrata sull'impostazione di un progetto vuoto. Nell'ultima sezione viene illustrata la scrittura di tutti i contenuti e la logica del gioco, la maggior parte dei quali sarà multipiattaforma.

Al termine di questa procedura dettagliata, verrà creato un semplice gioco in cui il lettore può controllare un carattere animato con input tocco.  Anche se non si tratta tecnicamente di un gioco completo (poiché non presenta condizioni di vittoria o di perdita), illustra diversi concetti di sviluppo di giochi e può essere usato come base per molti tipi di giochi.

Di seguito viene illustrato il risultato di questa procedura dettagliata:

![Animazione del carattere del gioco di esempio che segue il mouse](images/image1.gif)

## <a name="monogame-and-xna"></a>Monogame e XNA

La libreria monogame è progettata per simulare la libreria XNA di Microsoft in sintassi e funzionalità.  Tutti gli oggetti monogame sono presenti nello spazio dei nomi Microsoft. XNA, consentendo la maggior parte del codice XNA da usare in monogame senza alcuna modifica.

Gli sviluppatori che hanno familiarità con XNA acquisiranno già familiarità con la sintassi di monogame e gli sviluppatori che cercano informazioni aggiuntive sull'uso di monogame potranno fare riferimento a procedure dettagliate, documentazione API e discussioni di XNA online esistenti.

## <a name="walkthrough-parts"></a>Parti della procedura dettagliata

- [Parte 1: creazione di un progetto MonoGame multipiattaforma](~/graphics-games/monogame/introduction/part1.md)
- [Parte 2: implementazione di WalkingGame](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>Collegamenti correlati

- [Progetto MonoGame WalkingGame (esempio)](https://docs.microsoft.com/samples/xamarin/mobile-samples/walkinggamemg/)
- [Tipi di carattere XNB iOS](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Content/fonts)
- [Tipi di carattere XNB Android](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Assets/Content/fonts)
- [Monogame Android in NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [Monogame iOS in NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentazione dell'API monogame](http://www.monogame.net/documentation/?page=main)
