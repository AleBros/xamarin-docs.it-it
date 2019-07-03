---
title: Introduzione a iOS 13, iPadOS 13, 13 tvOS e watchOS 6
description: Questo documento descrive come ottenere l'impostazione per compilazione iOS 13, iPadOS 13, 13 tvOS e watchOS 6 App con Xamarin. Illustra come scaricare Xcode 11 e aggiornare Visual Studio per Mac e Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 97414545-85D2-433C-9246-63B6763F488A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/01/2019
ms.openlocfilehash: d2bb69394d8e9bfeb949a734291179a3e6f5a495
ms.sourcegitcommit: a6ba6ed086bcde4f52fb05f83c59c68e8aa5e436
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540437"
---
# <a name="get-started-with-ios-13"></a>Introduzione a iOS 13

![Funzionalità di anteprima](~/media/shared/preview.png)

Questo documento descrive come iniziare a compilare App Xamarin che chiamano le API rilasciate con 11 Xcode per iOS 13. L'uso dell'anteprima richiede macOS 10.14.4 (Mojave) o versione successiva.

## <a name="download-and-install"></a>Scaricare e installare

1. **Installare Xcode 11 beta** – gli sviluppatori di Apple registrati è possono scaricare e installare la versione più recente di Xcode 11 beta dal [portale Apple Developer](https://developer.apple.com/download/) o il **App Store**.

2. **Eseguire Xcode 11 beta** – 11 di Xcode eseguire prima l'aggiornamento e l'esecuzione di Visual Studio per Mac, durante l'installazione di alcuni strumenti di Xamarin è necessario.

3. In Visual Studio per Mac, selezionare **Visual Studio > Controlla aggiornamenti...** , selezionare il **Xcode 11 anteprime** del canale e installare gli aggiornamenti disponibili.

4. In Visual Studio per Mac, selezionare **Visual Studio > Preferenze > progetti > percorsi SDK > Apple** e selezionare **Xcode-beta.app**.

5. (Facoltativo) Se la valutazione di questa anteprima usando _Xcode 11 beta 3_, è necessario abilitare il collegamento. Mouse sul progetto, passare a **Opzioni > compilazione iOS > comportamento del Linker** e impostare il valore di comportamento del linker **collegamento Framework SDK solo**. Questa soluzione non sarà necessaria in una futura versione di anteprima.

6. (Facoltativo) **Installare 13 iOS nei dispositivi iOS** , per il test del dispositivo dell'App che usano API introdotte con Xcode 11, Apple registrati gli sviluppatori possono [scaricare](https://developer.apple.com/download) e installare il sistema operativo nei dispositivi. Poiché iOS nella versione beta, prestare attenzione installarlo nel dispositivo primario.

   > [!TIP]
   > Anche se l'app non usa le nuove API, assicurarsi di compilare la soluzione con gli SDK di 11 più recente di Xcode e testarla per assicurarsi che tutto funzioni come previsto. Se un'app non chiama eventuali nuove API, è possibile ricompilarlo con questi nuovi SDK e testarla su dispositivi che non sono ancora stati aggiornati per il nuovo sistema operativo.
   >
   > Prima di aggiornare i dispositivi per il sistema operativo più recente rilasciata da Apple per testare le app di Xamarin, assicurarsi di:
   >
   > - Lettura [note sulla versione di Apple](https://developer.apple.com/download/) per gli aggiornamenti del sistema operativo.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica Xcode](https://developer.apple.com/download/)
