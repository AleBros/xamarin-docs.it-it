---
title: Esempio del mondo reale con CocoaPods
description: Questo documento illustra come usare Sharpie obiettivo per generare automaticamente la C# le definizioni da una CocoaPod di associazione.
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
author: asb3993
ms.author: amburns
ms.date: 03/28/2018
ms.openlocfilehash: bac34f662e24c6b08a67cd8da1f41b37b43b3faf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61200315"
---
# <a name="real-world-example-using-cocoapods"></a>Esempio del mondo reale con CocoaPods

> [!NOTE]
> Questo esempio Usa la [AFNetworking CocoaPod](https://cocoapods.org/pods/AFNetworking).

Nuovo nella versione 3.0, supporta l'associazione CocoaPods Sharpie obiettivo e include anche un comando (`sharpie pod`) per rendere il download, la configurazione e creazione CocoaPods molto semplice. È consigliabile [acquisire familiarità con CocoaPods](https://cocoapods.org) in genere prima di usare questa funzionalità.

## <a name="creating-a-binding-for-a-cocoapod"></a>Creazione di un'associazione per un CocoaPod

Il `sharpie pod` comando è un'opzione globale e due sottocomandi:

```bash
$ sharpie pod -help
usage: sharpie pod [OPTIONS] COMMAND [COMMAND_OPTIONS]

Pod Options:
  -d, -dir DIR     Use DIR as the CocoaPods binding directory,
                   defaulting to the current directory

Available Commands:
  init         Initialize a new Xamarin C# CocoaPods binding project
  bind         Bind an existing Xamarin C# CocoaPods project
```

Il `init` sottocomando ha anche un utile supporto:

```bash
$ sharpie pod init -help
usage: sharpie pod init [INIT_OPTIONS] TARGET_SDK POD_SPEC_NAMES

Init Options:
  -f, -force       Initialize a new Podfile and run actions against
                   it even if one already exists
```

È possibile specificare più nomi di CocoaPod e nomi subspec a `init`.

```bash
$ sharpie pod init ios AFNetworking
** Setting up CocoaPods master repo ...
   (this may take a while the first time)
** Searching for requested CocoaPods ...
** Working directory:
**   - Writing Podfile ...
**   - Installing CocoaPods ...
**     (running `pod install --no-integrate --no-repo-update`)
Analyzing dependencies
Downloading dependencies
Installing AFNetworking (2.6.0)
Generating Pods project
Sending stats
** 🍻 Success! You can now use other `sharpie podn`  commands.
```

Dopo aver impostato la CocoaPod, è ora possibile creare l'associazione:

```bash
$ sharpie pod bind
```

Ciò comporterà il progetto CocoaPod Xcode viene compilato e quindi valutato e analizzate dal Sharpie obiettivo. Una grande quantità di output della console verrà generato, ma deve comportare la definizione di associazione alla fine:

```bash
(... lots of build output ...)

Parsing 19 header files...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Done.
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver generato il **ApiDefinitions.cs** e **StructsAndEnums.cs** file, esaminare la documentazione seguente per generare un assembly per l'uso nelle App:

- [Panoramica del binding Objective-C](~/cross-platform/macios/binding/overview.md)
- [Associazione di librerie Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Procedura dettagliata: Associazione di una libreria Objective-C iOS](~/ios/platform/binding-objective-c/walkthrough.md)
- [Corsi di Xamarin University: Compila una libreria di binding Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Corsi di Xamarin University: Creazione di una libreria di binding Objective-C con Sharpie obiettivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
