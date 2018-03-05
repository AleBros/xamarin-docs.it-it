---
title: Strumenti e comandi
description: Panoramica degli strumenti inclusi in Sharpie obiettivo e gli argomenti della riga di comando di utilizzarli.
ms.topic: article
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: 0d6e953a6a45b78d470c7ff73e1d6faa7444a683
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
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

<table>
  <thead>
    <tr><td>Strumento</td><td>Descrizione</td>
  </thead>
  <tbody>
    <tr><td><b>xcode</b></td><td>Vengono fornite informazioni sull'installazione corrente di Xcode e le versioni di iOS e Mac SDK disponibili. Si utilizzerà queste informazioni in un secondo momento quando si genera il binding.</td></tr>
    <tr><td><b>pod</b></td><td>Cerca, configura, installa (in una directory locale) e associa Objective-C <a href="https://cocoapods.org">CocoaPod</a> librerie disponibili dal repository master specifica. Questo strumento viene valutato il CocoaPod installato per dedurre automaticamente l'input da passare al corretto di <code>bind</code> strumento elencato di seguito. <em><strong>3.0!</strong></em></td></tr>
    <tr><td><b>bind</b></td><td>Analizza i file di intestazione (<code>*.h</code>) nella libreria in Objective-C di <a href="~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md">iniziale <i>ApiDefinition.cs</i> e <i>StructsAndEnums.cs</i> file</a>.</td></tr>
    <tr><td><b>update</b></td><td>Verifica la presenza di versioni più recenti di Sharpie obiettivo e scarica e avvia il programma di installazione, se disponibile.</td></tr>
    <tr><td><b>verify-docs</b></td><td>Mostra informazioni dettagliate sugli <code>[Verify]</code> attributi.</td></tr>
    <tr><td><b>docs</b></td><td>Passa al documento nel browser web predefinito.</td></tr>
  </tbody>
</table>

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

