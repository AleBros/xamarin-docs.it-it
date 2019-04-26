---
title: Introduzione alle pipeline di contenuto
description: Contenuto le pipeline sono applicazioni o parti di applicazioni, che consentono di convertire i file in un formato che può essere caricato da progetti di giochi. La Pipeline di contenuti MonoGame è un'implementazione della pipeline di contenuti specifici per la conversione di file per i progetti CocosSharp e MonoGame.
ms.prod: xamarin
ms.assetid: 40628B5F-FAF7-4FA7-A929-6C3FEA83F8EC
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 712c430fb6309ba0f5c3e573267c59e422de8ad2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61029520"
---
# <a name="introduction-to-content-pipelines"></a>Introduzione alle pipeline di contenuto

_Contenuto le pipeline sono applicazioni o parti di applicazioni, che consentono di convertire i file in un formato che può essere caricato da progetti di giochi. La Pipeline di contenuti MonoGame è un'implementazione della pipeline di contenuti specifici per la conversione di file per i progetti CocosSharp e MonoGame._

Questo articolo offre una comprensione concettuale delle pipeline di contenuti, principalmente fermiamo a considerare il *MonoGame Content Pipeline*, un'implementazione della pipeline di contenuti con CocosSharp e MonoGame utilizzata.


## <a name="what-is-a-content-pipeline"></a>Che cos'è una pipeline di contenuti?

Il termine *pipeline di contenuti* è un termine generale per il processo di conversione di un file da un formato a altro. Il *input* della pipeline di contenuti è in genere un file restituito da uno strumento di creazione, ad esempio i file di immagine da Photoshop. Consente di creare la pipeline di contenuti di *output* file in un formato che può essere caricato direttamente da un progetto di gioco. In genere i file di output sono ottimizzati per il caricamento rapido e ridurre le dimensioni del disco.

Content pipeline possono essere da riga di comando eseguibili le applicazioni basate su GUI dedicate o plug-in è incorporato in un'altra applicazione, ad esempio Visual Studio. Le pipeline di contenuti in genere eseguiti prima dell'esecuzione di gioco. Se la pipeline di contenuti è associata a un'applicazione, ad esempio Visual Studio, quindi può eseguire durante la fase di compilazione. Se la pipeline di contenuti è un'applicazione autonoma, può essere eseguita quando indicarle in modo esplicito per eseguire questa operazione dall'utente. L'applicazione o il responsabile della conversione di un file di input specifico per la logica (ad esempio un **PNG**) a un output associato file fa riferimento come un *generatore*. 

È possibile visualizzare il percorso che accetta un file dalla creazione in vengono caricati in fase di esecuzione, come indicato di seguito:

![](introduction-images/image1.png "In questo diagramma viene visualizzato il percorso che accetta un file dalla creazione alla caricato in fase di esecuzione")

## <a name="why-use-a-content-pipeline"></a>Perché usare una pipeline di contenuti?

Le pipeline di contenuti introducono un passaggio aggiuntivo tra l'applicazione di creazione e il gioco, che può aumentare i tempi di compilazione e aggiungere complessità al processo di sviluppo. Nonostante queste considerazioni, le pipeline di contenuti introducono una serie di vantaggi per lo sviluppo di giochi:


### <a name="converting-to-a-format-understood-by-the-game"></a>La conversione in un formato riconosciuto dal gioco

CocosSharp e MonoGame forniscono metodi per il caricamento dei vari tipi di contenuto. Tuttavia, il contenuto deve essere formattato correttamente prima in fase di caricamento. La maggior parte dei tipi di contenuto richiedono un tipo di conversione prima in fase di caricamento. Ad esempio, effetti audio nel **wav** formato deve essere convertito in un **.xnb** file da caricare in fase di esecuzione poiché CocosSharp e MonoGame non supportano il caricamento di **wav** formato di file.


### <a name="converting-to-a-format-native-to-the-hardware"></a>La conversione in un formato nativo per l'hardware

Hardware diversi potrebbe trattare il contenuto in modo diverso in fase di esecuzione. Ad esempio, giochi CocosSharp possono caricare i file di immagine durante la creazione di un `CCSprite` istanza. Anche se lo stesso codice può essere usato per caricare i file in iOS e Android, ogni piattaforma archivia il file caricato in modo diverso. Di conseguenza, la Pipeline di contenuti MonoGame formati di trama **.xnb** i file in modo diverso a seconda della piattaforma di destinazione.


### <a name="reducing-size-on-disk"></a>Riduzione delle dimensioni su disco 

Le pipeline possono essere utilizzate per rimuovere le informazioni sul contenuto, che è utile in fase di autore, ma non necessari in fase di esecuzione. Il file originale (input) può archiviare tutte le informazioni che consentono ai creatori di contenuti di gestire il contenuto esistente, ma il file di output può essere ridotta per mantenere il file complessiva gioco piccole. Questa considerazione è particolarmente utile per i giochi per dispositivi mobili che vengono scaricati anziché essere distribuiti nei supporti di installazione.


### <a name="reducing-load-time"></a>Riducendo il tempo di caricamento

Giochi possono richiedere modifiche del contenuto per migliorare le prestazioni di runtime, per migliorare gli oggetti visivi o per aggiungere nuove funzionalità. Ad esempio molti giochi 3D calcolare l'illuminazione una sola volta e quindi il risultato di questo calcolo usano durante il rendering di scene complesse. Dopo l'esecuzione di questi calcoli durante il caricamento del contenuto può avere costi proibitivo il calcolo può invece essere eseguito quando viene compilato il gioco. I calcoli risultanti possono essere inclusi nel contenuto, consentendo il contenuto da caricare più velocemente rispetto a quanto sarebbe possibile. 


## <a name="xnb-file-extension"></a>estensione di file xnb

Il **.xnb** estensione di file è l'estensione per tutti i file generati dalla Pipeline di contenuti Monogame. Questo corrisponde all'estensione di file generati dalla Pipeline di contenuti di Microsoft XNA.

Il **.xnb** indipendentemente dal tipo di file originale viene usata l'estensione. In altre parole, i file di immagine (**PNG**), file audio (**wav**), e tutti i tipi di file personalizzati verranno tutti da includere nell'output come **.xnb** file quando viene passato tramite la pipeline di contenuti. Poiché l'estensione non può essere usata per distinguere tra diversi formati di file quindi entrambi CocosSharp MonoGame i metodi e utilizzate per caricare **.xnb** file delle estensioni non sono prevedono durante il caricamento del file.

File .xnb CocosSharp e MonoGame possono essere creati usando lo strumento della Monogame Pipeline coperta [in questa procedura dettagliata](~/graphics-games/cocossharp/content-pipeline/walkthrough.md).


## <a name="summary"></a>Riepilogo

Questo articolo contiene una panoramica e i vantaggi delle pipeline di contenuti in generale, nonché un'introduzione alla Pipeline di contenuti MonoGame.

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione di Pipeline MonoGame](http://www.monogame.net/documentation/?page=Pipeline)
