---
title: Risoluzione dei problemi di Novell e iOS 13
description: Questa sezione contiene suggerimenti per la risoluzione dei problemi relativi alle funzionalità Novell correlate a iOS 13.
ms.prod: xamarin
ms.assetid: 00DE8C33-1407-45C0-A0C7-32AF1E490034
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/12/2019
ms.openlocfilehash: 6ccd0a100e2a1f01d33fee481df85d976d2191a8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031704"
---
# <a name="troubleshooting-tips-for-ios-13-and-xamarinios"></a>Suggerimenti per la risoluzione dei problemi per iOS 13 e Novell. iOS

## <a name="updating-to-xcode-11-stops-the-simulator-from-launching"></a>L'aggiornamento a Xcode 11 interrompe l'avvio del simulatore

Dopo l'aggiornamento a **Xcode 11 Beta 1** ogni volta che viene avviato un simulatore viene generata la seguente eccezione e il simulatore non viene avviato. Questa operazione viene eseguita con tutti i simulatori.

### <a name="exception"></a>Eccezione

`Foundation.ObjCException: NSInvalidArgumentException: -[SimDevice registerNotificationHandler:]: unrecognized selector sent to instance 0x7ffbf5d1e110`

### <a name="workaround"></a>Soluzione alternativa

Fino a quando non si verifica una [correzione](https://github.com/xamarin/xamarin-macios/issues/6216), è possibile seguire questa procedura per reinstallare il Framework del simulatore precedente per consentire agli sviluppatori di continuare a lavorare:

> [!NOTE]
> Questi passaggi presuppongono che siano disponibili due applicazioni Xcode:
>
> - **Xcode11-beta1. app** : versione beta che non funziona con simulatori e Visual Studio per Mac.
> - **Xcode102. app** : versione stabile di Xcode 10. Potrebbe anche essere chiamato **Xcode. app**.
>
> Modificare gli esempi di riga di comando riportati di seguito in base alla propria configurazione.

1. Assicurarsi di aver selezionato Xcode 11 tramite Xcode-Select:

   `sudo xcode-select -s /Applications/Xcode11-beta1.app/Contents/Developer/`

2. Eseguire, se necessario, gli strumenti di installazione per la prima volta.

    `/Applications/Xcode11-beta1.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

3. Rimuovere il Framework seguente:

    `sudo rm -Rf  /Library/Developer/PrivateFrameworks/CoreSimulator.framework/Versions/*`

4. Tornare alla versione precedente di Xcode

   `sudo xcode-select -s /Applications/Xcode102.app/Contents/Developer/`

5. Eseguire nuovamente il primo strumento di avvio per la versione _precedente_ di Xcode appena selezionata

   `/Applications/Xcode102.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

Dopo aver seguito questa procedura, dovrebbe essere possibile usare nuovamente il simulatore iOS.
