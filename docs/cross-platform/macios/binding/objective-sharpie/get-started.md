---
title: Introduzione
ms.prod: xamarin
ms.assetid: 577512BF-1A90-41E5-89DE-9E056C478678
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 5f61d0794e1f5a38370f0d840504720043f69590
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="getting-started"></a>Introduzione

> [!IMPORTANT]
> Obiettivo Sharpie è uno strumento per sviluppatori Xamarin esperti con conoscenze avanzate di Objective-C (e, di conseguenza, C). Prima di tentare di eseguire l'associazione di una libreria Objective-C è necessario avere conoscenze a tinta unita di come compilare la libreria nativa nella riga di comando (e una buona conoscenza del funzionamento della libreria nativa).

<a name="installing" />

## <a name="installing-objective-sharpie"></a>L'installazione Sharpie obiettivo

Obiettivo Sharpie attualmente è uno strumento da riga di comando autonomo per Mac OS X 10.10 e successive ed è _non è un prodotto Xamarin completamente supportato_. E deve essere utilizzato solo dagli sviluppatori avanzati per la creazione di un progetto di associazione per un 3rd party libreria Objective-C.

Sharpie obiettivo può essere scaricato come un programma di installazione di pacchetti X del sistema operativo standard.
Eseguire il programma di installazione e seguire tutte le istruzioni visualizzate nell'installazione guidata:

- **Versione corrente: 3.4**
  - [Scaricare la versione più recente](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
  - [Forum annuncio](https://forums.xamarin.com/discussion/104800/objective-sharpie-3-4)

> [!TIP]
> Utilizzare il `sharpie update` comando per l'aggiornamento alla versione più recente.

## <a name="basic-walkthrough"></a>Procedura dettagliata di base

Obiettivo Sharpie è uno strumento da riga di comando fornite da Xamarin che assiste nella creazione di definizioni di necessario per associare una libreria di Objective-C 3rd party a c#.
Anche quando si usa Sharpie obiettivo, lo sviluppatore *verrà* necessario modificare i file generati al termine Sharpie obiettivo per risolvere i problemi che non è stato possibile gestire automaticamente dallo strumento.

Dove possibile, obiettivo Sharpie annoterà API con cui presenta alcuni dubbi su come associare correttamente (numerosi costrutti di codice nativo sono ambigui).
Queste annotazioni verranno visualizzati come [ `[Verify]` attributi](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md).

L'output dell'obiettivo Sharpie è una coppia di file - [ `ApiDefinition.cs` e `StructsAndEnums.cs` ](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) -che può essere utilizzato per creare un progetto di associazione che consente di compilare in una libreria è possibile usare in App Xamarin.

> [!IMPORTANT]
> Obiettivo Sharpie viene fornito con uno **principali** regola per l'utilizzo corretto: deve assolutamente passarlo gli argomenti della riga di comando del compilatore clang corretto per garantire l'analisi corretta. Perché il Sharpie obiettivo fase di analisi è semplicemente di uno strumento [implementata con l'API di libtooling clang](http://clang.llvm.org/docs/LibTooling.html).

Ciò significa che Sharpie obiettivo ha tutta la potenza di Clang (il compilatore C/Objective-C/C++ che in realtà viene compilata la libreria nativa che è necessario associare) e tutte le relative informazioni interne dei file di intestazione per l'associazione.
Anziché convertire analizzata [AST](http://en.wikipedia.org/wiki/Abstract_syntax_tree) al codice oggetto, obiettivo Sharpie traduce l'albero sintattico Astratto per c# associazione "lo scaffolding" appropriato per l'input per il `bmac` e `btouch` strumenti di associazione di Xamarin.

Se obiettivo Sharpie gli errori di timeout durante l'analisi, significa che clang con errori di timeout durante la relativa analisi fase tenta di costruire l'albero sintattico Astratto ed è necessario capire il motivo.

**NUOVO!** versione 3.0 tenta di risolvere alcuni di questa complessità supportando progetti Xcode direttamente. Se una libreria nativa dispone di un progetto Xcode valido, obiettivo Sharpie possibile valutare il progetto per una destinazione specificata e la configurazione di dedurre il file di intestazione di input necessarie e i flag del compilatore.

Se non è disponibile alcun progetto Xcode, è necessario da maggiore familiarità con il progetto consiste nel dedurre i file di intestazione di input corretti, i percorsi di ricerca di file di intestazione e altri flag di compilatore necessarie. È importante tenere presente che i flag del compilatore utilizzati per compilare la libreria nativa sono gli stessi che deve essere passato a Sharpie obiettivo. Si tratta di un processo manuale e uno che richiedono un bit di una certa familiarità con la compilazione di codice nativo nella riga di comando con la toolchain Clang.

**NUOVO!** versione 3.0 include anche uno strumento per l'associazione facilmente [CocoaPods](https://cocoapods.org) tramite il `sharpie pod` comando.
Se la raccolta a cui che si è interessati è disponibile come un CocoaPod, è consigliabile che iniziare dal tentativo di associare il CocoaPod con Sharpie obiettivo (e il tentativo di associare direttamente sull'origine).
