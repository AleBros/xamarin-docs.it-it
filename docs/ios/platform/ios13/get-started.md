---
title: Inizia a usare iOS 13, iPados 13, tvOS 13 e watchos 6
description: Questo documento descrive come ottenere la configurazione per compilare app iOS 13, iPados 13, tvOS 13 e watchos 6 con Novell. Viene illustrato come scaricare Xcode 11 e aggiornare Visual Studio per Mac.
ms.prod: xamarin
ms.assetid: 97414545-85D2-433C-9246-63B6763F488A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/01/2019
ms.openlocfilehash: 1f190ef2f99e71c8cf21680f9902caccf3454450
ms.sourcegitcommit: 09bc69d7119a04684c9e804c5cb113b8b1bb7dfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71206330"
---
# <a name="get-started-with-ios-13"></a>Introduzione a iOS 13

Questo documento illustra come iniziare a creare app Novell che chiamano le API rilasciate con Xcode 11, per iOS 13. L'uso dell'anteprima richiede macOS 10.14.4 (Mojave) o versione successiva.

## <a name="download-and-install"></a>Scaricare e installare

1. **Installare Xcode 11** : gli sviluppatori Apple registrati possono scaricare e installare la versione più recente di Xcode 11 dal [portale Apple Developer](https://developer.apple.com/download/) o dall' **App Store**.

2. **Eseguire Xcode 11** -eseguire Xcode 11 prima di aggiornare ed eseguire Visual Studio per Mac, perché installa alcuni strumenti richiesti da Novell.

3. In Visual Studio per Mac selezionare **Visual Studio > Verifica disponibilità aggiornamenti...** , selezionare il canale **stabile** e installare gli aggiornamenti disponibili.

4. Opzionale **Installare iOS 13 nei dispositivi iOS** : per i test dei dispositivi delle app che usano le API introdotte con Xcode 11, gli sviluppatori Apple registrati possono [scaricare](https://developer.apple.com/download) e installare il sistema operativo nei dispositivi. 

   > [!TIP]
   > Anche se l'app non usa nuove API, assicurarsi di compilarla con gli SDK Xcode 11 più recenti e testarla per verificare che tutto funzioni come previsto. Se un'app non chiama nuove API, è possibile ricompilarla con questi nuovi SDK e testarla nei dispositivi che non sono ancora stati aggiornati al nuovo sistema operativo.
   >
   > Prima di aggiornare i dispositivi alle versioni più recenti del sistema operativo da Apple per testare le app Novell, assicurarsi di:
   >
   > - Leggere le [Note sulla versione di Apple](https://developer.apple.com/download/) per gli aggiornamenti del sistema operativo.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica Xcode](https://developer.apple.com/download/)
- [Note sulla versione di Xamarin.iOS](/xamarin/ios/release-notes/13/13.0)
