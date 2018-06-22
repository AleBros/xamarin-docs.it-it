---
title: Esempio reale utilizzando CocoaPods
description: Questo documento viene illustrato come utilizzare Sharpie obiettivo di generare automaticamente le definizioni di associazione c# da un CocoaPod.
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
author: asb3993
ms.author: amburns
ms.date: 03/28/2018
ms.openlocfilehash: 026b2c46f7c294d4ac4a110376131ec83c7c112e
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33947394"
---
# <a name="real-world-example-using-cocoapods"></a>Esempio reale utilizzando CocoaPods

> [!NOTE]
> Questo esempio viene utilizzata la [AFNetworking CocoaPod](https://cocoapods.org/pods/AFNetworking).

Nuovo nella versione 3.0, supporta l'associazione dei CocoaPods Sharpie obiettivo e include anche un comando (`sharpie pod`) per il download, configurazione e di compilazione CocoaPods molto semplice. È necessario [acquisire familiarità con CocoaPods](https://cocoapods.org) in genere prima di utilizzare questa funzionalità.

## <a name="creating-a-binding-for-a-cocoapod"></a>Creazione di un'associazione per un CocoaPod

Il `sharpie pod` comando dispone di un'opzione globale e due sottocomandi:

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

Il `init` sottocomando mancano alcuni argomenti della Guida utili:

```bash
$ sharpie pod init -help
usage: sharpie pod init [INIT_OPTIONS] TARGET_SDK POD_SPEC_NAMES

Init Options:
  -f, -force       Initialize a new Podfile and run actions against
                   it even if one already exists
```

È possibile specificare più nomi CocoaPod e subspec a `init`.

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

Verrà creato nel progetto CocoaPod Xcode viene compilata e quindi valutato e analizzato da Sharpie obiettivo. Molti dell'output di console verranno generato, ma devono comportare la definizione dell'associazione alla fine:

```bash
(... lots of build output ...)

Parsing 19 header files...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Done.
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver generato il **ApiDefinitions.cs** e **StructsAndEnums.cs** file, esaminare la documentazione seguente per generare un assembly da utilizzare nelle app di Windows:

- [Panoramica del binding Objective-C](~/cross-platform/macios/binding/overview.md)
- [Associazione di raccolte Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Procedura dettagliata: Associazione di una libreria di Objective-C iOS](~/ios/platform/binding-objective-c/walkthrough.md)

