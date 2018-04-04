---
title: Strumenti e comandi
description: Panoramica degli strumenti inclusi in Sharpie obiettivo e gli argomenti della riga di comando di utilizzarli.
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: 8a307739134fe3b76692fbef5c1dc028af01017d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="tools--commands"></a>Strumenti e comandi

_Panoramica degli strumenti inclusi in Sharpie obiettivo e gli argomenti della riga di comando di utilizzarli._

<style type="text/css"> .terminal blu {color: rgb(10,96,254);} .terminal verde {colore: rgb(12,156,26);} .terminal magenta {colore: rgb(152,12,103);} </style>


Una volta Sharpie obiettivo è stato correttamente [installato](~/cross-platform/macios/binding/objective-sharpie/get-started.md), aprire un terminale e acquisire familiarità con la <em>comandi</em> Sharpie obiettivo è offrire:

<pre>$ <b>sharpie -help</b>
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
  docs               Open the Objective Sharpie online documentation</pre>

Obiettivo Sharpie fornisce gli strumenti seguenti:

|Strumento|Descrizione|
|--- |--- |
|**xcode**|Vengono fornite informazioni sull'installazione corrente di Xcode e le versioni di iOS e Mac SDK disponibili. Si utilizzerà queste informazioni in un secondo momento quando si genera il binding.|
|**pod**|Cerca, configura, installa (in una directory locale) e associa Objective-C [CocoaPod](https://cocoapods.org/) librerie disponibili dal repository master specifica. Questo strumento viene valutato il CocoaPod installato per dedurre automaticamente l'input da passare al corretto di `bind` strumento elencato di seguito. 3.0!|
|**bind**|Analizza i file di intestazione (`*.h`) nella libreria Objective-C in iniziale [ApiDefinition.cs e StructsAndEnums.cs](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) file.|
|**update**|Verifica la presenza di versioni più recenti di Sharpie obiettivo e scarica e avvia il programma di installazione, se disponibile.|
|**verify-docs**|Mostra informazioni dettagliate sugli `[Verify]` attributi.|
|**docs**|Passa al documento nel browser web predefinito.|

Per ottenere informazioni su uno strumento Sharpie obiettivo specifico, immettere il nome dello strumento e `-help` opzione. Ad esempio, `sharpie xcode -help` restituisce il seguente output:

<pre>$ <b>sharpie xcode -help</b>
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.</pre>

Prima di poter iniziare il processo di associazione, è necessario ottenere informazioni sui nostri SDK installati corrente immettendo il comando seguente nel terminale `sharpie xcode -sdks`. L'output può differire a seconda di quale versione di Xcode è stato installato. Obiettivo Sharpie Cerca SDK installato in qualsiasi `Xcode*.app` sotto il `/Applications` directory:

<pre>$ <b>sharpie xcode -sdks</b>
<span class="terminal-blue">sdk:</span> appletvos9.0    <span class="terminal-green">arch:</span> arm64
<span class="terminal-blue">sdk:</span> iphoneos9.1     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos9.0     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos8.4     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> macosx10.11     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> macosx10.10     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> watchos2.0      <span class="terminal-green">arch:</span> armv7</pre>

In precedenza, possiamo vedere che abbiamo il `iphoneos9.1` SDK installato sul computer in uso e ha `arm64` supporto dell'architettura. Si utilizzerà questo valore per tutti gli esempi in questa sezione. Con queste informazioni sul posto, si è pronti per analizzare un file di intestazione libreria Objective-C in iniziale `ApiDefinition.cs` e `StructsAndEnums.cs` per il progetto di associazione.

