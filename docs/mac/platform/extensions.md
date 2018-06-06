---
title: Supporto delle estensioni Xamarin.Mac
description: Questo documento viene descritto il supporto del Xamarin.Mac per le estensioni di ricerca, condivisione e la data odierna. Esamina le limitazioni e problemi noti, collegamenti a una procedura dettagliata e app di esempio e vengono forniti suggerimenti per l'utilizzo di estensioni.
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 4cef6e89b89ef41a0c93b687c9df11a8aa971ed5
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792378"
---
# <a name="xamarinmac-extension-support"></a>Supporto delle estensioni Xamarin.Mac

Nell'anteprima Xamarin.Mac 2.10 è stato aggiunto il supporto per più punti di estensione macOS:

- Finder
- Condividi
- Oggi

<a name="Limitations-and-Known-Issues" />

## <a name="limitations-and-known-issues"></a>Limitazioni e problemi noti

Di seguito è elencate le limitazioni e conosce i problemi che possono verificarsi durante lo sviluppo di estensioni in Xamarin.Mac:

* Non è attualmente alcun supporto per il debug in Visual Studio per Mac. Debug tutti dovrà essere eseguita tramite **NSLog** e **Console**. Vedere la sezione suggerimenti seguente per informazioni dettagliate.
* Le estensioni devono essere contenute in un'applicazione host, che, quando eseguita una sola volta a un registro di sistema. Quindi deve essere abilitate nel **estensione** sezione **preferenze di sistema**. 
* Alcuni degli arresti anomali di estensione potrebbero destabilizzare l'applicazione host e causare un comportamento anomalo. In particolare, **Finder** e il **oggi** sezione del **centro notifiche** potrebbe diventare "fogli bloccati" e non rispondere. Questo è stato esperto nei progetti di estensione in Xcode nonché e risulta attualmente non correlato al Xamarin.Mac. Spesso questo può essere visualizzato nel Registro di sistema (tramite **Console**, vedere Suggerimenti per i dettagli) Stampa messaggi di errore ripetuti. Il riavvio macOS viene visualizzata per risolvere il problema.

<a name="Tips" />

## <a name="tips"></a>Suggerimenti

I suggerimenti seguenti possono essere utili quando si lavora con le estensioni in Xamarin.Mac:

- Come Xamarin.Mac attualmente non supporta le estensioni di debug, l'esperienza di debug principalmente dipenderanno esecuzione e `printf` come istruzioni. Tuttavia, le estensioni eseguito in un processo sandbox, pertanto `Console.WriteLine` non si comporterà come invece avviene in altre applicazioni Xamarin.Mac. Richiamare [ `NSLog` direttamente](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554) genera messaggi di debug nel Registro di sistema.
- Eventuali eccezioni non rilevate verranno un arresto anomalo del processo di estensione e avere solo una piccola quantità di informazioni utili nel **Registro di sistema**. Wrapping codice problematico in un `try/catch` (eccezione) che blocca `NSLog`del precedente potrebbe essere utile generare nuovamente.
- Il **Registro di sistema** è possibile accedere dal **Console** app in **applicazioni** > **utilità**:

    [![](extensions-images/extension02.png "Il Registro di sistema")](extensions-images/extension02.png#lightbox)
- Come indicato in precedenza, in esecuzione l'applicazione host di estensione verrà registrato con il sistema. Eliminare il pacchetto di applicazione con annullamento della registrazione. 
- Se sono registrate "errate" versioni delle estensioni di un'app, è possibile utilizzare il comando seguente per individuarli (in modo da poter essere eliminati): `plugin kit -mv`


<a name="Walkthrough-and-Sample-App" />

## <a name="walkthrough-and-sample-app"></a>Procedura dettagliata e App di esempio

Poiché lo sviluppatore verrà creare e lavorare con le estensioni Xamarin.Mac nello stesso modo come estensioni di xamarin. IOS, consultare il nostro [Introduzione alle estensioni](~/ios/platform/extensions.md) documentazione per maggiori dettagli.

Un progetto di esempio Xamarin.Mac contenente piccole, esempi reali di ogni tipo di estensione sono reperibile [qui](https://developer.xamarin.com/samples/mac/ExtensionSamples/).

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato un rapido controllo di utilizzo di estensioni in un'app Xamarin.Max versione 2.10 (e versioni successiva).

## <a name="related-links"></a>Collegamenti correlati

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](https://developer.xamarin.com/samples/mac/ExtensionSamples/)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
