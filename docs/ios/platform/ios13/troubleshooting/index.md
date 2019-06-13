---
title: Xamarin e la risoluzione dei problemi di iOS 13
description: In questa sezione contiene suggerimenti sulla risoluzione dei problemi per la funzionalità Xamarin correlate ai dispositivi iOS 13.
ms.prod: xamarin
ms.assetid: 00DE8C33-1407-45C0-A0C7-32AF1E490034
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/12/2019
ms.openlocfilehash: 99fd7e41e1521c6a9254d286bf989281658ccf24
ms.sourcegitcommit: 85c45dc28ab3625321c271804768d8e4fce62faf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67039788"
---
# <a name="troubleshooting-tips-for-ios-13-and-xamarinios"></a>Risoluzione dei problemi di suggerimenti per 13 iOS e xamarin. IOS

![Funzionalità di anteprima](~/media/shared/preview.png)

## <a name="updating-to-xcode-11-stops-the-simulator-from-launching"></a>L'aggiornamento a Xcode 11 si arresta il simulatore l'avvio

Dopo l'aggiornamento a **Xcode 11 beta 1** ogni volta che viene avviato un simulatore viene generata l'eccezione seguente, e non si avvia il simulatore. Ciò accade con tutti i simulatori.

### <a name="exception"></a>Eccezione

`Foundation.ObjCException: NSInvalidArgumentException: -[SimDevice registerNotificationHandler:]: unrecognized selector sent to instance 0x7ffbf5d1e110`

### <a name="workaround"></a>Soluzione alternativa

Fino a quando non è presente una [correggere](https://github.com/xamarin/xamarin-macios/issues/6216), possono essere seguiti i passaggi seguenti per installare nuovamente il vecchio framework del simulatore per consentire agli sviluppatori di continuare a usare:

> [!NOTE]
> Questi passaggi si presuppone due applicazioni di Xcode:
> - **Xcode11 beta1.app** : la versione beta, che non funziona con i simulatori e Visual Studio per Mac.
> - **Xcode102.app** : una versione stabile di Xcode 10. Quelle in uso potrebbe essere anche chiamato **Xcode.app**.
>
> Modificare gli esempi di riga di comando seguenti come appropriato per la configurazione.

1. Assicurarsi di aver 11 Xcode selezionato tramite xcode-select:

   `sudo xcode-select -s /Applications/Xcode11-beta1.app/Contents/Developer/`

2. Eseguire, se necessario, il programma di installazione degli strumenti per la prima volta.

    `/Applications/Xcode11-beta1.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

3. Rimuovere il framework seguente:

    `sudo rm -Rf  /Library/Developer/PrivateFrameworks/CoreSimulator.framework/Versions/*`

4. Tornare alla versione precedente di Xcode

   `sudo xcode-select -s /Applications/Xcode102.app/Contents/Developer/`

5. Eseguire nuovamente il primo strumento di avvio per il _vecchio_ Xcode versione appena selezionata

   `/Applications/Xcode102.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

Dopo aver completato questi passaggi, sarà possibile usare nuovamente con il simulatore iOS.