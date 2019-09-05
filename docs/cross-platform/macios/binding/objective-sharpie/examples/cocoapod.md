---
title: Esempio reale con CocoaPods
description: Questo documento illustra come usare Objective Sharpie per generare automaticamente le C# definizioni di binding da un CocoaPod.
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
author: conceptdev
ms.author: crdun
ms.date: 03/28/2018
ms.openlocfilehash: 0f730b1c0a0deacdb84c198cfe4af47308a268cc
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290034"
---
# <a name="real-world-example-using-cocoapods"></a>Esempio reale con CocoaPods

> [!NOTE]
> Questo esempio usa il [CocoaPod AFNetworking](https://cocoapods.org/pods/AFNetworking).

Novità della versione 3,0, Objective Sharpie supporta l'associazione CocoaPods e include anche un comando (`sharpie pod`) per facilitare il download, la configurazione e la compilazione di CocoaPods. Prima di usare questa funzionalità, è necessario [acquisire familiarità con CocoaPods](https://cocoapods.org) in generale.

## <a name="creating-a-binding-for-a-cocoapod"></a>Creazione di un'associazione per un CocoaPod

Il `sharpie pod` comando dispone di un'opzione globale e di due sottocomandi:

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

Il `init` sottocomando dispone inoltre di una guida utile:

```bash
$ sharpie pod init -help
usage: sharpie pod init [INIT_OPTIONS] TARGET_SDK POD_SPEC_NAMES

Init Options:
  -f, -force       Initialize a new Podfile and run actions against
                   it even if one already exists
```

È possibile fornire più nomi CocoaPod e nomi di sottospecifiche `init`a.

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

Una volta configurata la CocoaPod, è ora possibile creare il binding:

```bash
$ sharpie pod bind
```

Questa operazione comporterà la compilazione e la valutazione e l'analisi da parte dell'obiettivo del progetto Xcode CocoaPod. Verrà generata una grande quantità di output della console, ma la definizione dell'associazione dovrebbe risultare alla fine:

```bash
(... lots of build output ...)

Parsing 19 header files...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Done.
```

## <a name="next-steps"></a>Passaggi successivi

Dopo avere generato i file **ApiDefinitions.cs** e **StructsAndEnums.cs** , vedere la documentazione seguente per generare un assembly da usare nelle app:

- [Panoramica di binding Objective-C](~/cross-platform/macios/binding/overview.md)
- [Binding di librerie Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Procedura dettagliata: Associazione di una libreria Objective-C iOS](~/ios/platform/binding-objective-c/walkthrough.md)
