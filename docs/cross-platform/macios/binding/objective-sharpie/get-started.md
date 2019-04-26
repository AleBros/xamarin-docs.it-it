---
title: Guida introduttiva a Sharpie obiettivo
description: Questo documento fornisce una panoramica generale delle Sharpie obiettivo, lo strumento utilizzato per automatizzare la creazione di C# binding a codice Objective-C.
ms.prod: xamarin
ms.assetid: 577512BF-1A90-41E5-89DE-9E056C478678
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: c1831467ca0cbb4329a1e77fb355698f2d16cd6a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199780"
---
# <a name="getting-started-with-objective-sharpie"></a>Guida introduttiva a Sharpie obiettivo

> [!IMPORTANT]
> Obiettivo Sharpie è uno strumento per gli sviluppatori esperti di Xamarin con conoscenza approfondita di Objective-C (e di conseguenza, C). Prima di provare a eseguire l'associazione di una libreria Objective-C è solido delle conoscenze necessarie per compilare la libreria nativa nella riga di comando (e una buona conoscenza del funzionamento della libreria nativa).

<a name="installing" />

## <a name="installing-objective-sharpie"></a>Installazione Sharpie obiettivo

Obiettivo Sharpie è attualmente uno strumento da riga di comando autonomi per Mac OS X 10.10 e versioni successive e viene _non è un prodotto Xamarin completamente supportato_. Si deve solo utilizzabile da sviluppatori esperti per fornire assistenza nella creazione di un progetto di associazione di un 3rd party libreria Objective-C.

Obiettivo Sharpie può essere scaricati come un programma di installazione di pacchetti del sistema operativo X standard.
Eseguire il programma di installazione e seguire tutte le istruzioni visualizzate nell'installazione guidata:

- **Versione corrente: 3.4**
  - [Scaricare la versione più recente](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
  - [Annuncio di forum](https://forums.xamarin.com/discussion/104800/objective-sharpie-3-4)

> [!TIP]
> Usare il `sharpie update` comando per l'aggiornamento alla versione più recente.

## <a name="basic-walkthrough"></a>Procedura dettagliata di base

Obiettivo Sharpie è uno strumento da riga di comando fornito da Xamarin che consente di creare le definizioni necessarie per eseguire l'associazione di una libreria Objective-C di terze parti 3rd a C#.
Anche quando si usa Sharpie obiettivo, lo sviluppatore *verranno* necessario modificare i file generati al termine Sharpie descrive come risolvere eventuali problemi che non può essere gestite automaticamente dallo strumento.

Dove possibile, obiettivo Sharpie annoterà le API con cui presenta alcuni dubbi su come associare correttamente (molti costrutti nel codice nativo sono ambigui).
Queste annotazioni verranno visualizzato come [ `[Verify]` attributi](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md).

L'output di Sharpie obiettivo è una coppia di file - [ `ApiDefinition.cs` e `StructsAndEnums.cs` ](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) -che può essere utilizzato per creare un progetto di associazione di compilare in una libreria è possibile usare nelle App Xamarin.

> [!IMPORTANT]
> Obiettivo Sharpie dotato di uno **principali** regola per l'utilizzo corretto: è necessario assolutamente chiamarla gli argomenti della riga di comando del compilatore clang corretto per garantire l'analisi corretta. Questo accade perché il Sharpie obiettivo fase di analisi è semplicemente uno strumento [implementate con l'API di libtooling clang](http://clang.llvm.org/docs/LibTooling.html).

Questo significa che descrive come Sharpie tutta la potenza di Clang (il compilatore C/Objective-C/C++ che consente di compilare effettivamente la libreria nativa che è necessario associare il) e tutte le relative informazioni interne dei file di intestazione per l'associazione.
Anziché convertire analizzata [dell'albero sintattico Astratto](https://en.wikipedia.org/wiki/Abstract_syntax_tree) al codice dell'oggetto, Sharpie obiettivo si traduce l'albero sintattico Astratto per un C# associazione di "eseguire lo scaffolding" appropriato per l'input per il `bmac` e `btouch` strumenti di associazione di Xamarin.

Se errori Sharpie obiettivo durante l'analisi, significa che tale clang che hanno generato errori durante il processo di analisi fase cercando di costruire l'albero sintattico Astratto ed è necessario capire il motivo.

**NUOVO!** versione 3.0 tentativi risolvere alcuni di questa complessità grazie al supporto direttamente i progetti Xcode. Se una libreria nativa ha un progetto Xcode valido, obiettivo Sharpie possibile valutare il progetto per una destinazione specificata e la configurazione di dedurre il file di intestazione di input necessari e i flag del compilatore.

Se è disponibile alcun progetto Xcode, è necessario avere più familiarità con il progetto per dedurre i file di intestazione input corretto, i percorsi di ricerca di file di intestazione e altri flag del compilatore necessarie. È importante tenere presente che il flag del compilatore usato per compilare la libreria nativa siano gli stessi che deve essere passato a Sharpie obiettivo. Si tratta di un processo manuale e quella che richiedono un po' di familiarità con la compilazione di codice nativo nella riga di comando con la toolchain Clang.

**NUOVO!** versione 3.0 introduce anche uno strumento per l'associazione di facilmente [CocoaPods](https://cocoapods.org) tramite il `sharpie pod` comando.
Se è disponibile come un CocoaPod la libreria che si è interessati, è consigliabile che iniziare cercando di eseguire l'associazione di CocoaPod con obiettivo Sharpie (rispetto a è stato effettuato un tentativo di associare direttamente sull'origine).

## <a name="related-links"></a>Collegamenti correlati

- [Corsi di Xamarin University: Compila una libreria di binding Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Corsi di Xamarin University: Creazione di una libreria di binding Objective-C con Sharpie obiettivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)