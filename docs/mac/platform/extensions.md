---
title: Supporto delle estensioni Xamarin.Mac
description: Questo documento illustra il supporto di Novell. Mac per le estensioni Finder, share e Today. Esamina le limitazioni e i problemi noti, i collegamenti a una procedura dettagliata e l'app di esempio e fornisce suggerimenti per l'uso delle estensioni.
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 13b19d60569884f244f2ea9c79aaa6989838d0d3
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290700"
---
# <a name="xamarinmac-extension-support"></a>Supporto delle estensioni Xamarin.Mac

In Novell. Mac 2,10 è stato aggiunto il supporto per più punti di estensione macOS:

- Finder
- Condividi
- Oggi

<a name="Limitations-and-Known-Issues" />

## <a name="limitations-and-known-issues"></a>Limitazioni e problemi noti

Di seguito sono riportate le limitazioni e i problemi che possono verificarsi durante lo sviluppo di estensioni in Novell. Mac:

- Non è attualmente disponibile alcun supporto per il debug in Visual Studio per Mac. Tutto il debug deve essere eseguito tramite **NSLog** e la **console**. Per informazioni dettagliate, vedere la sezione suggerimenti riportata di seguito.
- Le estensioni devono essere contenute in un'applicazione host, che viene eseguita una sola volta con Register con il sistema. Devono quindi essere abilitati nella sezione relativa all' **estensione** delle **Preferenze di sistema**. 
- Alcuni arresti anomali dell'estensione possono destabilizzare l'applicazione host e causare un comportamento anomalo. In particolare, il **Finder** e la sezione **odierna** del **Centro notifiche** potrebbero diventare "bloccati" e non rispondere. Questo è stato sperimentato anche nei progetti di estensione in Xcode e attualmente non è correlato a Novell. Mac. Spesso questa operazione può essere visualizzata nel registro di sistema (tramite la **console**di, vedere Suggerimenti per i dettagli) stampa di messaggi di errore ripetuti. Per risolvere il problema, riavviare macOS.

<a name="Tips" />

## <a name="tips"></a>Suggerimenti

I suggerimenti seguenti possono essere utili quando si utilizzano le estensioni in Novell. Mac:

- Poiché Novell. Mac attualmente non supporta le estensioni di debug, l'esperienza di debug dipende principalmente dall'esecuzione `printf` e dalle istruzioni like. Tuttavia, le estensioni vengono eseguite in un processo sandbox `Console.WriteLine` , pertanto non funzionerà come in altre applicazioni Novell. Mac. Se si richiama [ direttamente,imessaggididebugvengonorestituitinelregistrodisistema.`NSLog` ](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554)
- Eventuali eccezioni non rilevate arresteranno in modo anomalo il processo di estensione, fornendo solo una piccola quantità di informazioni utili nel **Registro di sistema**. Il wrapping di codice fastidioso `try/catch` in un blocco (eccezione `NSLog`) che precede il rigenerazione può risultare utile.
- È possibile accedere al **Registro di sistema** dall'app **console** in**utilità** **applicazioni** > :

    [![](extensions-images/extension02.png "Registro di sistema")](extensions-images/extension02.png#lightbox)
- Come indicato in precedenza, l'esecuzione dell'applicazione host di estensione la registrerà nel sistema. Eliminazione del bundle dell'applicazione con l'annullamento della registrazione. 
- Se vengono registrate le versioni "randagi" delle estensioni di un'app, usare il comando seguente per individuarle (in modo che possano essere eliminate):`plugin kit -mv`


<a name="Walkthrough-and-Sample-App" />

## <a name="walkthrough-and-sample-app"></a>Procedura dettagliata e app di esempio

Poiché lo sviluppatore creerà e collaborerà con le estensioni di Novell. Mac nello stesso modo in cui si riferisce alle estensioni di Novell. iOS, vedere la documentazione [introduttiva alle estensioni](~/ios/platform/extensions.md) per altri dettagli.

Un progetto Novell. Mac di esempio contenente esempi di dimensioni ridotte e funzionante di ogni tipo di estensione è disponibile [qui](https://docs.microsoft.com/samples/xamarin/mac-samples/extensionsamples).

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato rapidamente l'uso delle estensioni in un'app Novell. Mac versione 2,10 (e versioni successive).

## <a name="related-links"></a>Collegamenti correlati

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](https://docs.microsoft.com/samples/xamarin/mac-samples/extensionsamples)
- [Linee guida dell'interfaccia umana macOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)
