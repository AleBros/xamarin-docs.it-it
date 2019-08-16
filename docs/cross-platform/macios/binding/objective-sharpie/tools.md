---
title: Comandi & per gli strumenti di Sharpie
description: Questo documento fornisce una panoramica degli strumenti inclusi con Objective Sharpie e gli argomenti della riga di comando da usare con essi.
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: ddfe0f99991808214a6006c9504d267179adf2ab
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521865"
---
# <a name="objective-sharpie-tools--commands"></a>Comandi & per gli strumenti di Sharpie

_Panoramica degli strumenti inclusi in Objective Sharpie e degli argomenti della riga di comando per utilizzarli._

Una volta completata l' [installazione](~/cross-platform/macios/binding/objective-sharpie/get-started.md), aprire un terminale e acquisire familiarità con i *comandi* che l'obiettivo Sharpie può offrire:

```
$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, -help                Show detailed help
  -v, -version             Show version information

Telemetry Options:
  -tlm-about               Show a detailed overview of what usage and binding
                             information will be submitted to Xamarin by
                             default when using Objective Sharpie.
  -tlm-do-not-submit       Do not submit any usage or binding information to
                             Xamarin. Run 'sharpie -tml-about' for more
                             information.
  -tlm-do-not-identify     Do not submit Xamarin account information when
                             submitting usage or binding information to Xamarin
                             for analysis. Binding attempts and usage data will
                             be submitted anonymously if this option is
                             specified.

Available Tools:
  xcode              Get information about Xcode installations and available SDKs.
  pod                Create a Xamarin C# binding to Objective-C CocoaPods
  bind               Create a Xamarin C# binding to Objective-C APIs
  update             Update to the latest release of Objective Sharpie
  verify-docs        Show cross reference documentation for [Verify] attributes
  docs               Open the Objective Sharpie online documentation
```

L'obiettivo Sharpie fornisce gli strumenti seguenti:

|Strumento|Descrizione|
|--- |--- |
|**xcode**|Fornisce informazioni sull'installazione corrente di Xcode e sulle versioni di iOS e Mac SDK disponibili. Queste informazioni verranno usate in un secondo momento durante la generazione dei binding.|
|**pod**|Consente di cercare, configurare, installare (in una directory locale) e di associare le librerie [CocoaPod](https://cocoapods.org/) di Objective-C disponibili dal repository delle specifiche master. Questo strumento valuta la CocoaPod installata per dedurre automaticamente l'input corretto da passare allo `bind` strumento riportato di seguito. Novità in 3,0!|
|**bind**|Analizza i file di intestazione (`*.h`) nella libreria Objective-C nei file [ApiDefinition.cs e StructsAndEnums.cs](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) iniziali.|
|**update**|Verifica la presenza di versioni più recenti di Objective Sharpie e Scarica e avvia il programma di installazione, se disponibile.|
|**verify-docs**|Mostra informazioni dettagliate sugli `[Verify]` attributi.|
|**docs**|Passa a questo documento nel Web browser predefinito.|

Per ottenere informazioni sulla guida su uno specifico strumento di Sharpie, immettere il nome dello strumento e `-help` l'opzione. Ad esempio, `sharpie xcode -help` restituisce l'output seguente:

```
$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.
```

Prima di poter avviare il processo di associazione, è necessario ottenere informazioni sugli SDK installati correnti immettendo il comando seguente nel terminale `sharpie xcode -sdks`. L'output può variare a seconda della versione o delle versioni di Xcode installate. L'obiettivo Sharpie cerca gli SDK installati in `Xcode*.app` qualsiasi sotto `/Applications` la directory:

```
$ sharpie xcode -sdks
sdk: appletvos9.0    arch: arm64
sdk: iphoneos9.1     arch: arm64   armv7
sdk: iphoneos9.0     arch: arm64   armv7
sdk: iphoneos8.4     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: macosx10.10     arch: x86_64  i386
sdk: watchos2.0      arch: armv7
```

Dal precedente è possibile vedere che l'SDK è installato nel `iphoneos9.1` computer e `arm64` il supporto per l'architettura. Questo valore verrà usato per tutti gli esempi in questa sezione. Con queste informazioni, è possibile analizzare i file di intestazione della libreria Objective-C nell'oggetto iniziale `ApiDefinition.cs` e `StructsAndEnums.cs` per il progetto di associazione.
