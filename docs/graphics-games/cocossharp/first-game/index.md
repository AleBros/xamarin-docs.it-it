---
title: Introduzione allo sviluppo di giochi con CocosSharp
description: In questa procedura dettagliata multiparte viene illustrato come creare un semplice gioco 2D utilizzando CocosSharp. Vengono illustrate comuni gioco i concetti di programmazione, ad esempio immagini, input e fisica.
ms.topic: article
ms.prod: xamarin
ms.assetid: BCA99A61-A48D-4207-9A35-4C62F10C9AA5
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 5ab6f68aed791dd21516d663367ac5435e92d6cc
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="introduction-to-game-development-with-cocossharp"></a>Introduzione allo sviluppo di giochi con CocosSharp

_In questa procedura dettagliata multiparte viene illustrato come creare un semplice gioco 2D utilizzando CocosSharp. Vengono illustrate comuni gioco i concetti di programmazione, ad esempio immagini, input e fisica._

Il motore di giochi 2D CocosSharp offre una tecnologia per l'esecuzione di giochi multipiattaforma. Per un elenco completo delle piattaforme supportate, vedere il [CocosSharp wiki su GitHub](https://github.com/mono/CocosSharp/wiki). In questa esercitazione verrà usato c# per esempi di codice, sebbene anche CocosSharp è completamente funzionale con F #.

Viene fornito il nucleo di CocosSharp dal [MonoGame framework](http://www.monogame.net/), che è una multipiattaforma, API che fornisce grafica, la gestione dello stato di gioco, audio, input e una pipeline di contenuto per l'importazione di asset con accelerazione hardware. CocosSharp è un livello di astrazione efficiente particolarmente utile per i giochi 2D. Inoltre, giochi più grande possono eseguire le proprie ottimizzazioni all'esterno le librerie di base giochi aumento della complessità. In altre parole, CocosSharp fornisce una combinazione di facilità di utilizzo e prestazioni, consentendo agli sviluppatori di iniziare rapidamente senza limitare la dimensione del gioco o della complessità.

La prima sezione di questa procedura dettagliata sono incentrate sulla configurazione di un progetto vuoto.  La seconda parte viene illustrata la scrittura di tutta la logica del gioco. 

Al termine di questa procedura dettagliata si avrà creato un semplice gioco in obiettivo del lettore è diapositiva una racchetta orizzontalmente per tentare di mantenere una palla di caduta visualizzato sullo schermo. Ogni bounce aumenterà il punteggio del lettore di un punto.

![](images/image1.png "Ogni bounce aumenterà il punteggio del lettore di un punto")

# <a name="walkthrough-parts"></a>Parti di questa procedura dettagliata

* [Parte 1: creazione di un progetto CocosSharp](~/graphics-games/cocossharp/first-game/part1.md)
* [Parte 2: implementazione di BouncingGame](~/graphics-games/cocossharp/first-game/part2.md)

## <a name="related-links"></a>Collegamenti correlati

- [Contenuto del gioco (esempio)](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)
- [Progetto completato (esempio)](https://developer.xamarin.com/samples/mobile/BouncingGame/)
- [Libreria di classi Portabile CocosSharp in NuGet](http://www.nuget.org/packages/CocosSharp.PCL.Shared/)
- [Documentazione CocosSharp API](https://developer.xamarin.com/api/namespace/CocosSharp/)
