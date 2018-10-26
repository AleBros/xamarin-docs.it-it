---
title: Supporto delle estensioni xamarin. Mac
description: Questo documento viene descritto il supporto di xamarin. Mac per le estensioni di ricerca, condivisione e la data odierna. Esamina le limitazioni e problemi noti, i collegamenti a un'app di esempio e procedura dettagliata e vengono forniti suggerimenti per l'uso di estensioni.
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 60b981a764a2656210730ae0602ff32dc580cd0a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117564"
---
# <a name="xamarinmac-extension-support"></a>Supporto delle estensioni xamarin. Mac

In fase di anteprima di xamarin. Mac 2.10 è stato aggiunto il supporto per più punti di estensione macOS:

- Finder
- Condividi
- Oggi

<a name="Limitations-and-Known-Issues" />

## <a name="limitations-and-known-issues"></a>Problemi noti e limitazioni

Di seguito è elencate le limitazioni e conoscenza i problemi che possono verificarsi durante lo sviluppo di estensioni in xamarin. Mac:

* Non è attualmente alcun supporto per il debug in Visual Studio per Mac. Debug di tutti i dovrà essere eseguita tramite **NSLog** e il **Console**. Vedere la sezione dei suggerimenti di seguito per informazioni dettagliate.
* Le estensioni devono essere contenute in un'applicazione host, che, quando eseguito una volta con la registrazione con il sistema. Quindi deve essere abilitati nel **estensione** sezione **preferenze di sistema**. 
* Arresti anomali del sistema alcune estensioni potrebbero destabilizzare l'applicazione host e causare un comportamento anomalo. In particolare, **Finder** e il **oggi stesso** sezione del **centro notifiche** possono diventare "fogli bloccati" e non rispondere. Questo è stato esperto nei progetti di estensione in Xcode anche e risulta attualmente non correlato a xamarin. Mac. Spesso può essere visualizzato nel Registro di sistema (tramite **Console**, vedere Suggerimenti per i dettagli) stampa i messaggi di errore ripetuti. Il riavvio di macOS verrà visualizzata per risolvere il problema.

<a name="Tips" />

## <a name="tips"></a>Suggerimenti

I suggerimenti seguenti possono essere utili quando si lavora con le estensioni in xamarin. Mac:

- Poiché xamarin. Mac non supporta attualmente le estensioni di debug, l'esperienza di debug dipenderà principalmente durante l'esecuzione e `printf` , ad esempio le istruzioni. Tuttavia, le estensioni eseguite in un processo sandbox, quindi `Console.WriteLine` non si comporterà come accade in altre applicazioni xamarin. Mac. Richiamo [ `NSLog` direttamente](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554) restituirà messaggi di debug nel Registro di sistema.
- Tutte le eccezioni non rilevate si arresteranno il processo di estensione, che fornisce solo una piccola quantità di informazioni utili nel **Registro di sistema**. Wrapping di codice problematico in un `try/catch` (eccezione) di blocco `NSLog`della prima di generare di nuovo può risultare utile.
- Il **Registro di sistema** sono accessibili dal **Console** app sotto **applicazioni** > **utilità**:

    [![](extensions-images/extension02.png "Il Registro di sistema")](extensions-images/extension02.png#lightbox)
- Come indicato in precedenza, eseguire l'applicazione host di estensione eseguirà la registrazione con il sistema. Il bundle dell'applicazione con l'eliminazione annullarne la registrazione. 
- Se sono registrate "errate" versioni delle estensioni di un'app, usare il comando seguente per individuarli (in modo che possano essere eliminate): `plugin kit -mv`


<a name="Walkthrough-and-Sample-App" />

## <a name="walkthrough-and-sample-app"></a>Procedura dettagliata e App di esempio

Poiché lo sviluppatore verrà creare e usare xamarin. Mac estensioni nello stesso modo come estensioni di xamarin. IOS, vedere la [Introduzione alle estensioni](~/ios/platform/extensions.md) informazioni più dettagliate.

Un progetto di esempio xamarin. Mac che contiene di piccole dimensioni, sono disponibili esempi reali di ogni tipo di estensione [qui](https://developer.xamarin.com/samples/mac/ExtensionSamples/).

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha assunto un esame rapido di lavorare con le estensioni in un'app Xamarin.Max versione 2.10 (e versioni successiva).

## <a name="related-links"></a>Collegamenti correlati

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](https://developer.xamarin.com/samples/mac/ExtensionSamples/)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
