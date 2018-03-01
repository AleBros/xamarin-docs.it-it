---
title: Esempio reale utilizzando CocoaPods
ms.topic: article
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: ae92b491e6186371f1fc1ead835f918a94f18f86
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="real-world-example-using-cocoapods"></a>Esempio reale utilizzando CocoaPods


**Questo esempio viene utilizzato il [AFNetworking CocoaPod](https://cocoapods.org/pods/AFNetworking).**

Nuovo nella versione 3.0 supporta l'associazione CocoaPods Sharpie obiettivo e ha anche un comando di front-end (`sharpie pod`) per il download, configurazione e di compilazione CocoaPods molto semplice. È necessario [faimilarize manualmente con CocoaPods](https://cocoapods.org) in genere prima di utilizzare questa funzionalità.

Il `sharpie pod` comando dispone di un'opzione globale e due sottocomandi:

```csharp
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

```csharp
$ sharpie pod init -help
usage: sharpie pod init [INIT_OPTIONS] TARGET_SDK POD_SPEC_NAMES

Init Options:
  -f, -force       Initialize a new Podfile and run actions against
                   it even if one already exists
```

È possibile specificare più nomi CocoaPod e subspec a `init`.

<pre>$ <b>sharpie pod init ios AFNetworking</b>
<span class="terminal-green">**</span> Setting up CocoaPods master repo ...
   (this may take a while the first time)
<span class="terminal-green">**</span> Searching for requested CocoaPods ...
<span class="terminal-green">**</span> Working directory:
<span class="terminal-green">**</span>   - Writing Podfile ...
<span class="terminal-green">**</span>   - Installing CocoaPods ...
<span class="terminal-green">**</span>     (running `<span class="terminal-blue">pod install --no-integrate --no-repo-update</span>`)
Analyzing dependencies
Downloading dependencies
Installing AFNetworking (2.6.0)
Generating Pods project
Sending stats
<span class="terminal-green">**</span> 🍻 Success! You can now use other `<span class="terminal-green">sharpie pod</span>`  commands.</pre>

Dopo aver impostato la CocoaPod, è ora possibile creare l'associazione:

<pre>$ <b>sharpie pod bind</b></pre>

Verrà creato nel progetto CocoaPod Xcode viene compilata e quindi valutato e analizzato da Sharpie obiettivo. Molti dell'output di console verranno generato, ma devono comportare la definizione dell'associazione alla fine:

<pre><em>(... lots of build output ...)</em>

<span class="terminal-blue">Parsing 19 header files...</span>

<span class="terminal-magenta">Binding...</span>
  <span class="terminal-magenta">[write]</span> ApiDefinitions.cs
  <span class="terminal-magenta">[write]</span> StructsAndEnums.cs

<span class="terminal-green">Done.</span></pre>

