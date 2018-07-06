---
title: I comandi e strumenti Sharpie obiettivo
description: Questo documento viene fornita una panoramica degli strumenti inclusi con obiettivo Sharpie e gli argomenti della riga di comando da usare con essi.
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: 718b5104ddc4593d080b88b062c42d371d9e8e2e
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855068"
---
# <a name="objective-sharpie-tools--commands"></a>I comandi e strumenti Sharpie obiettivo

_Panoramica degli strumenti inclusi con obiettivo Sharpie e gli argomenti della riga di comando per usarli._

<style type="text/css"> .terminal-blu {colore: rgb(10,96,254);} .terminal-verde {color: rgb(12,156,26);} .terminal-magenta {color: rgb(152,12,103);} </style>


Al termine obiettivo Sharpie correttamente [installata](~/cross-platform/macios/binding/objective-sharpie/get-started.md), aprire un terminale e acquisire familiarità con la <em>comandi</em> obiettivo Sharpie ha da offrire:

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

Sharpie obiettivo offre gli strumenti seguenti:

|Strumento|Descrizione|
|--- |--- |
|**xcode**|Vengono fornite informazioni sull'installazione corrente di Xcode e le versioni di iOS e Mac SDK che sono disponibili. Si userà queste informazioni in un secondo momento quando si genera il binding.|
|**POD**|Cerca, configura, installa (in una directory locale) e associa Objective-C [CocoaPod](https://cocoapods.org/) librerie disponibile dal repository master specifica. Questo strumento viene valutata la CocoaPod installata per dedurre automaticamente l'input corretto da passare al `bind` strumento riportato di seguito. Novità nella versione 3.0|
|**bind**|Analizza i file di intestazione (`*.h`) nella libreria Objective-C in iniziale [ApiDefinition.cs e StructsAndEnums.cs](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) file.|
|**update**|Verifica la presenza di versioni più recenti di Sharpie obiettivo e scarica e avvia il programma di installazione, se disponibile.|
|**verify-docs**|Vengono visualizzate informazioni dettagliate relative a `[Verify]` attributi.|
|**docs**|Consente di passare a questo documento nel web browser predefinito.|

Per ottenere informazioni su uno strumento Sharpie obiettivo specifico, immettere il nome dello strumento e `-help` opzione. Ad esempio, `sharpie xcode -help` restituisce l'output seguente:

<pre>$ <b>sharpie xcode -help</b>
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.</pre>

Prima di poter iniziare il processo di associazione, è necessario ottenere informazioni sui nostri SDK installati corrente immettendo il comando seguente nel terminale `sharpie xcode -sdks`. L'output può variare a seconda di quale versione di Xcode è stato installato. Obiettivo Sharpie esegue la ricerca di SDK installati in qualsiasi `Xcode*.app` sotto il `/Applications` directory:

<pre>$ <b>sharpie xcode -sdks</b>
<span class="terminal-blue">sdk:</span> appletvos9.0    <span class="terminal-green">arch:</span> arm64
<span class="terminal-blue">sdk:</span> iphoneos9.1     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos9.0     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos8.4     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> macosx10.11     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> macosx10.10     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> watchos2.0      <span class="terminal-green">arch:</span> armv7</pre>

Da quanto sopra, possiamo vedere che abbiamo la `iphoneos9.1` SDK installato sul computer e ha `arm64` supporto dell'architettura. Si userà questo valore per tutti gli esempi in questa sezione. Con queste informazioni posto, siamo pronti analizzare un file di intestazione libreria Objective-C in iniziale `ApiDefinition.cs` e `StructsAndEnums.cs` per il progetto di associazione.

## <a name="related-links"></a>Collegamenti correlati

- [Corso di Xamarin University: Creazione di una libreria di binding di Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Corsi di Xamarin University: Compilare una libreria di binding Objective-C con Sharpie obiettivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)