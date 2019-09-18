---
title: Inizia a usare iOS 13, iPados 13, tvOS 13 e watchos 6
description: Questo documento descrive come ottenere la configurazione per compilare app iOS 13, iPados 13, tvOS 13 e watchos 6 con Novell. Viene illustrato come scaricare Xcode 11 e aggiornare Visual Studio per Mac.
ms.prod: xamarin
ms.assetid: 97414545-85D2-433C-9246-63B6763F488A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/01/2019
ms.openlocfilehash: 9dc6f234c4bc14c3644d953eef0d2e0f397436e5
ms.sourcegitcommit: 61a35d0643eb3bf5adb8f8831da54771d8dde626
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033081"
---
# <a name="get-started-with-ios-13"></a>Introduzione a iOS 13

![Funzionalità di anteprima](~/media/shared/preview.png)

Questo documento illustra come iniziare a creare app Novell che chiamano le API rilasciate con Xcode 11, per iOS 13. L'uso dell'anteprima richiede macOS 10.14.4 (Mojave) o versione successiva.

## <a name="download-and-install"></a>Scaricare e installare

1. **Installare Xcode 11 Beta** : gli sviluppatori Apple registrati possono scaricare e installare la versione più recente di Xcode 11 beta dal [portale Apple Developer](https://developer.apple.com/download/) o dall' **App Store**.

2. **Eseguire Xcode 11 Beta** : eseguire Xcode 11 prima di aggiornare ed eseguire Visual Studio per Mac, perché installa alcuni strumenti richiesti da Novell.

3. In Visual Studio per Mac selezionare **Visual Studio > Controlla aggiornamenti...** , selezionare il canale **Xcode 11** Preview e installare gli aggiornamenti disponibili. Se questo canale non viene visualizzato, assicurarsi di essere connessi all'account da **Visual Studio > account...** .

4. In Visual Studio per Mac selezionare **Visual Studio > preferenze > progetti > percorsi SDK > Apple** e selezionare **Xcode-beta. app**.

5. Opzionale Se si valuta questa anteprima usando _Xcode 11 Beta 3_, è necessario abilitare il collegamento. Fare clic con il pulsante destro del mouse sul progetto, passare a **opzioni > compilazione iOS > comportamento del linker** e impostare il valore del comportamento del linker su **collega solo SDK del Framework**. Questa soluzione alternativa non sarà necessaria in un'anteprima futura.

6. Opzionale **Installare iOS 13 nei dispositivi iOS** : per i test dei dispositivi delle app che usano le API introdotte con Xcode 11, gli sviluppatori Apple registrati possono [scaricare](https://developer.apple.com/download) e installare il sistema operativo nei dispositivi. Poiché iOS è in versione beta, è necessario installarlo nel dispositivo primario.

   > [!TIP]
   > Anche se l'app non usa nuove API, assicurarsi di compilarla con gli SDK Xcode 11 più recenti e testarla per verificare che tutto funzioni come previsto. Se un'app non chiama nuove API, è possibile ricompilarla con questi nuovi SDK e testarla nei dispositivi che non sono ancora stati aggiornati al nuovo sistema operativo.
   >
   > Prima di aggiornare i dispositivi alle versioni più recenti del sistema operativo da Apple per testare le app Novell, assicurarsi di:
   >
   > - Leggere le [Note sulla versione di Apple](https://developer.apple.com/download/) per gli aggiornamenti del sistema operativo.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica Xcode](https://developer.apple.com/download/)
- [Note sulla versione di anteprima di Novell. iOS](/xamarin/ios/release-notes/12/12.99)
