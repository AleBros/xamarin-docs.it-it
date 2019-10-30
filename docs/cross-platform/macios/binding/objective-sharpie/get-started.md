---
title: Introduzione con l'obiettivo Sharpie
description: Questo documento fornisce una panoramica generale di Objective Sharpie, lo strumento usato per automatizzare la creazione di C# associazioni nel codice Objective-C.
ms.prod: xamarin
ms.assetid: 577512BF-1A90-41E5-89DE-9E056C478678
author: davidortinau
ms.author: daortin
ms.date: 10/11/2017
ms.openlocfilehash: b055ecadc007a07ff1946df4ac4203f36ebf88ee
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016207"
---
# <a name="getting-started-with-objective-sharpie"></a>Introduzione con l'obiettivo Sharpie

> [!IMPORTANT]
> Objective Sharpie è uno strumento per gli sviluppatori esperti di Novell con una conoscenza avanzata di Objective-C (e per estensione, C). Prima di tentare di associare una libreria Objective-C, è necessario avere una conoscenza approfondita di come compilare la libreria nativa dalla riga di comando (e una buona conoscenza del funzionamento della libreria nativa).

<a name="installing" />

## <a name="installing-objective-sharpie"></a>Installazione dell'obiettivo Sharpie

Objective Sharpie è attualmente uno strumento da riga di comando autonomo per Mac OS X 10,10 e versioni successive e _non è un prodotto Novell completamente supportato_. Deve essere usato solo da sviluppatori avanzati per semplificare la creazione di un progetto di associazione a una libreria Objective-C di terze parti.

L'obiettivo Sharpie può essere scaricato come programma di installazione del pacchetto OS X standard.
Eseguire il programma di installazione e seguire tutte le richieste sullo schermo dall'installazione guidata:

- **Versione corrente: 3,4**
  - [Scarica la versione più recente](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
  - [Annuncio Forum](https://forums.xamarin.com/discussion/104800/objective-sharpie-3-4)

> [!TIP]
> Usare il comando `sharpie update` per eseguire l'aggiornamento alla versione più recente.

## <a name="basic-walkthrough"></a>Procedura dettagliata di base

Objective Sharpie è uno strumento da riga di comando fornito da Novell che facilita la creazione delle definizioni necessarie per associare una libreria Objective-C di terze parti a C#.
Anche quando si usa l'obiettivo Sharpie, lo sviluppatore *dovrà* modificare i file generati dopo che Objective Sharpy termina per risolvere eventuali problemi che non possono essere gestiti automaticamente dallo strumento.

Laddove possibile, l'obiettivo Sharpie annotare le API in cui si è verificato un certo dubbio sulla modalità di associazione corretta (molti costrutti nel codice nativo sono ambigui).
Queste annotazioni verranno visualizzate come [attributi`[Verify]`](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md).

L'output dell'obiettivo Sharpie è costituito da una coppia di file, [`ApiDefinition.cs` e `StructsAndEnums.cs`](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) , che possono essere usati per creare un progetto di associazione che si compila in una libreria che è possibile usare nelle app Novell.

> [!IMPORTANT]
> L'obiettivo Sharpie presenta una regola **principale** per l'utilizzo corretto: è necessario passargli gli argomenti della riga di comando del compilatore Clang corretti per garantire l'analisi corretta. Questo è dovuto al fatto che la fase di analisi dell'obiettivo Sharpie è semplicemente uno strumento [implementato con l'API libtooling Clang](https://clang.llvm.org/docs/LibTooling.html).

Ciò significa che l'obiettivo Sharpie ha tutta la potenza di Clang (il compilatore C/Objective-C++ c/compilatore che compila effettivamente la libreria nativa da associare) e tutte le conoscenze interne dei file di intestazione per l'associazione.
Anziché tradurre l' [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) analizzato nel codice oggetto, l'obiettivo Sharpie converte l'albero sintattico astratto in un' C# associazione "impalcatura" adatta per l'input ai`bmac`e`btouch`strumenti di associazione Novell.

Se durante l'analisi si verificano errori oggettivi di Sharpie, significa che Clang ha avuto errore durante la fase di analisi, tentando di costruire l'albero sintattico astratto ed è necessario individuare il motivo.

**Nuovo!** la versione 3,0 tenta di risolvere parte di questa complessità supportando direttamente i progetti Xcode. Se una libreria nativa dispone di un progetto Xcode valido, Objective Sharpie può valutare il progetto per una destinazione e una configurazione specifiche per dedurre i file di intestazione di input e i flag del compilatore necessari.

Se non è disponibile alcun progetto Xcode, sarà necessario acquisire familiarità con il progetto dedurre i file di intestazione di input corretti, i percorsi di ricerca dei file di intestazione e altri flag del compilatore necessari. È importante tenere presente che i flag del compilatore usati per compilare la libreria nativa sono gli stessi che devono essere passati a Objective Sharpe. Si tratta di un processo più manuale, che richiede un po' di familiarità con la compilazione di codice nativo nella riga di comando con la sequenza di testing Clang.

**Nuovo!** la versione 3,0 introduce anche uno strumento che consente di associare facilmente [CocoaPods](https://cocoapods.org) tramite il comando `sharpie pod`.
Se la libreria a cui si è interessati è disponibile come CocoaPod, è consigliabile iniziare tentando di associare CocoaPod con Objective Sharpie (anziché tentare di eseguire l'associazione direttamente all'origine).
