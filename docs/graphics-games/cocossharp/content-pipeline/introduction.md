---
title: Introduzione alla pipeline del contenuto
description: Contenuto le pipeline sono applicazioni o parti di applicazioni, che vengono utilizzati per convertire i file in un formato che può essere caricato da progetti di giochi. La Pipeline di contenuto MonoGame è un'implementazione di pipeline contenuto specifico per la conversione di file per i progetti CocosSharp e MonoGame.
ms.topic: article
ms.prod: xamarin
ms.assetid: 40628B5F-FAF7-4FA7-A929-6C3FEA83F8EC
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 7394ae5ddacb20a10e603fa50376799b82d2a3dc
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="introduction-to-content-pipelines"></a>Introduzione alla pipeline del contenuto

_Contenuto le pipeline sono applicazioni o parti di applicazioni, che vengono utilizzati per convertire i file in un formato che può essere caricato da progetti di giochi. La Pipeline di contenuto MonoGame è un'implementazione di pipeline contenuto specifico per la conversione di file per i progetti CocosSharp e MonoGame._

Questo articolo fornisce una comprensione concettuale delle pipeline contenute, principalmente con particolare attenzione il *MonoGame contenuto Pipeline*, che è un'implementazione di pipeline contenuto utilizzata con CocosSharp e MonoGame.


## <a name="what-is-a-content-pipeline"></a>Che cos'è una pipeline di contenuto?

Il termine *pipeline contenuta* è un termine generale per il processo di conversione di un file da un formato a altro. Il *input* della pipeline di contenuto è in genere un file di output da uno strumento di creazione, ad esempio i file di immagine da Photoshop. La pipeline di contenuto crea il *output* file in un formato che può essere caricato direttamente da un progetto di gioco. In genere i file di output sono ottimizzati per il caricamento rapido e riduzione delle dimensioni del disco.

Contenuto pipeline possono essere da riga di comando file eseguibili, le applicazioni con interfaccia grafica dedicate o plug-in è incorporato in un'altra applicazione, ad esempio Visual Studio. Pipeline di contenuto vengono in genere eseguiti prima dell'esecuzione il gioco. Se la pipeline di contenuto è associata a un'applicazione come Visual Studio, può eseguire in fase di compilazione. Se la pipeline di contenuto è un'applicazione autonoma, è possibile eseguire quando indicarle in modo esplicito dall'utente a tale scopo. L'applicazione o il responsabile della conversione di un file di input specifico logica (ad esempio un **PNG**) a un output associato file viene definito un *generatore*. 

È possibile visualizzare il percorso di un file dalla creazione in vengono caricati in fase di esecuzione, come indicato di seguito:

![](introduction-images/image1.png "Il percorso di un file dalla creazione in vengono caricati in fase di esecuzione viene visualizzato in questo diagramma")

## <a name="why-use-a-content-pipeline"></a>Perché utilizzare una pipeline di contenuto?

Pipeline contenute introducono un passaggio aggiuntivo tra l'applicazione di creazione e il gioco, che può aumentare i tempi di compilazione e aggiungere complessità al processo di sviluppo. Nonostante queste considerazioni, pipeline contenute introducono una serie di vantaggi per lo sviluppo di gioco di:


### <a name="converting-to-a-format-understood-by-the-game"></a>Conversione in un formato riconosciuto dal gioco

CocosSharp e MonoGame forniscono metodi per il caricamento di vari tipi di contenuto. Tuttavia, il contenuto deve essere formattato correttamente prima di caricamento. La maggior parte dei tipi di contenuto richiedono un tipo di conversione prima fase di caricamento. Ad esempio, gli effetti sonori nel **wav** formato deve essere convertito in un **.xnb** file da caricare in fase di esecuzione poiché CocosSharp e MonoGame non supporta il caricamento il **wav** formato di file.


### <a name="converting-to-a-format-native-to-the-hardware"></a>Conversione in un formato nativo per l'hardware

Hardware diverse può gestire il contenuto in modo diverso in fase di esecuzione. Ad esempio, giochi CocosSharp possono caricare i file di immagine durante la creazione di un `CCSprite` istanza. Anche se lo stesso codice può essere utilizzato per caricare i file in iOS e Android, ogni piattaforma archivia il file caricato in modo diverso. Di conseguenza, la Pipeline di contenuto MonoGame formati di trama **.xnb** file in modo diverso a seconda della piattaforma di destinazione.


### <a name="reducing-size-on-disk"></a>Riduzione delle dimensioni su disco 

Pipeline possono essere usate per rimuovere le informazioni di contenuto, che è utile in fase di modifica, ma non necessarie in fase di esecuzione. Il file originale (input) può archiviare tutte le informazioni che consentono ai creatori di contenuto di mantenere il contenuto esistente, ma il file di output può essere ridotta per mantenere il file complessiva gioco piccole. Questa considerazione è particolarmente utile per i giochi per dispositivi mobili che vengono scaricati anziché essere distribuiti su supporti di installazione.


### <a name="reducing-load-time"></a>Riducendo il tempo di carico

Giochi possono richiedere modifiche del contenuto per migliorare le prestazioni di runtime, per migliorare gli oggetti visivi, oppure per aggiungere nuove funzionalità. Ad esempio molti giochi 3D calcolare l'illuminazione una sola volta e quindi utilizzano il risultato del calcolo per il rendering di scene complesse. Dopo l'esecuzione di questi calcoli durante il caricamento del contenuto può essere proibitivo il calcolo può invece essere eseguito quando viene compilato il gioco. I calcoli risultanti possono essere inclusi nel contenuto, consentendo il contenuto da caricare più velocemente rispetto a quanto sarebbe possibile. 


## <a name="xnb-file-extension"></a>estensione di file xnb

Il **.xnb** estensione di file è l'estensione per tutti i file generati dalla Pipeline Monogame contenuto. Questo corrisponde all'estensione di file generati dalla Pipeline del Microsoft XNA contenuto.

Il **.xnb** estensione venga usata indipendentemente dal tipo di file originale. In altre parole, i file di immagine (**PNG**), file audio (**wav**), e tutti i tipi di file personalizzato verranno da produrre come **.xnb** file quando viene passato tramite la pipeline di contenuto. Poiché l'estensione non può essere usata per distinguere tra diversi formati di file quindi CocosSharp sia MonoGame metodi caricano **.xnb** file non sono prevedono estensioni quando il caricamento del file.

CocosSharp e MonoGame .xnb può essere creato con lo strumento di Monogame Pipeline coperta [in questa procedura dettagliata](~/graphics-games/cocossharp/content-pipeline/walkthrough.md).


## <a name="summary"></a>Riepilogo

In questo articolo fornita una panoramica e i vantaggi delle pipeline contenute in generale, nonché un'introduzione alla Pipeline MonoGame contenuto.

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione MonoGame Pipeline](http://www.monogame.net/documentation/?page=Pipeline)
