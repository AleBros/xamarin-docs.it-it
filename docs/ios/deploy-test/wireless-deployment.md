---
title: Distribuzione wireless per app Xamarin.iOS e tvOS
description: Questo documento descrive come distribuire in modalità wireless un'app Xamarin.iOS in un dispositivo iOS da Visual Studio per Mac o Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 5AB4C5A9-4FBB-4DCB-BD72-0022D5439E65
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 01/22/2019
ms.openlocfilehash: b0eb471bd41bb38d62ced38ed66cfa0dfee37bbf
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73023414"
---
# <a name="wireless-deployment-for-xamarinios-and-tvos-apps"></a>Distribuzione wireless per app Xamarin.iOS e tvOS

Una parte importante del flusso di lavoro di uno sviluppatore è la distribuzione in un dispositivo. In Xcode 9 è stata introdotta l'opzione per la distribuzione in un dispositivo iOS o in Apple TV tramite una rete, invece di dover collegare i dispositivi ogni volta che si vuole distribuire l'app ed eseguirne il debug. Questa funzionalità è stata introdotta in Visual Studio per Mac 7.4 e Visual Studio 15.6.

In questa guida viene illustrato come eseguire l'associazione e la distribuzione in un dispositivo in rete.

## <a name="requirements"></a>Requisiti

La distribuzione wireless è disponibile come funzionalità sia in Visual Studio per Mac che in Visual Studio.

Per usare la distribuzione wireless, è necessario quanto segue:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

- macOS 10.12.4
- Ultima versione di Visual Studio per Mac
- Xcode 9.0 o versione successiva
- Un dispositivo con iOS 11.0 o tvOS 11.0 e versioni successive

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

- Ultima versione di Visual Studio
- Un dispositivo con iOS 11.0 o tvOS 11.0 e versioni successive

Nell'host di compilazione Mac devono essere installati i componenti seguenti:

- macOS 10.12.4
- Visual Studio per Mac
- Xcode 9.0 o versione successiva

-----

## <a name="connecting-a-device"></a>Connessione di un dispositivo

Per eseguire la distribuzione e il debug in modalità wireless nel dispositivo, è necessario associare il dispositivo iOS o Apple TV con Xcode nel Mac. Una volta associato, il dispositivo può essere selezionato nell'elenco delle destinazioni dei dispositivi in Visual Studio. 

Il processo di associazione seguente deve essere eseguito una sola volta per ogni dispositivo. Xcode manterrà le impostazioni di connessione.

<a name="pair" />

### <a name="pairing-an-ios-device-with-xcode"></a>Associazione di un dispositivo iOS con Xcode

1. Aprire Xcode e passare a **Window > Devices and Simulators** (Finestra > Dispositivi e simulatori).
2. Collegare il dispositivo iOS al Mac tramite un cavo Lightning. Potrebbe essere necessario selezionare **Trust This Computer** (Considera attendibile il computer) sul dispositivo.
3. Selezionare il dispositivo e quindi selezionare la casella di controllo **Connect via network** (Connetti tramite la rete) per associare il dispositivo: ![Finestra di dispositivi e simulatori con l'opzione per la connessione tramite la rete](wireless-deployment-images/image2.png)

### <a name="pairing-an-apple-tv-with-xcode"></a>Associazione di una Apple TV con Xcode

1. Verificare che il Mac e la Apple TV siamo connessi alla stessa rete.

2. Aprire Xcode e passare a **Window > Devices and Simulators** (Finestra > Dispositivi e simulatori).

3. Nella Apple TV passare a **Settings > Remotes and Devices > Remote App and Devices** (Impostazioni > Telecomandi e dispositivi > App remote e dispositivi).

4. Selezionare Apple TV nell'area **Discovered** (Individuati) in Xcode e immettere il codice di verifica visualizzato sull'Apple TV.

5. Fare clic sul pulsante **Connetti**. Una volta eseguita l'associazione, accanto all'Apple TV viene visualizzata un'icona della connessione di rete.

## <a name="deploy-to-a-device"></a>Eseguire la distribuzione in un dispositivo

Quando un dispositivo è connesso in modalità wireless e pronto per essere usato per la distribuzione, viene visualizzato nell'elenco delle destinazioni dei dispositivi, come se il dispositivo fosse connesso tramite USB.

Per eseguire il test in un dispositivo fisico, è necessario effettuare il [provisioning](~/ios/get-started/installation/device-provisioning/index.md) del dispositivo. Assicurarsi di eseguire questa operazione prima di tentare di effettuare la distribuzione in un dispositivo. 

Per eseguire la distribuzione in un dispositivo iOS o tvOS, attenersi alla procedura seguente:

1. Verificare che il computer di distribuzione e il dispositivo di destinazione siano connessi alla stessa rete wireless. 

2. Selezionare il dispositivo dall'elenco delle destinazioni dei dispositivi ed eseguire l'applicazione.

3. Se il dispositivo è bloccato, verrà richiesto di sbloccare il dispositivo. Dopo aver sbloccato il dispositivo, l'app viene distribuita al dispositivo.

Il debug wireless viene abilitato automaticamente dopo la distribuzione wireless, pertanto è possibile usare i punti di interruzione impostati precedentemente e continuare il flusso di lavoro di debug come di consueto.

## <a name="troubleshooting"></a>Risoluzione dei problemi

1. Verificare sempre che il dispositivo iOS o Apple TV sia connesso alla stessa rete del Mac.

2. Se il dispositivo non viene visualizzato in Visual Studio, controllare la finestra **Devices and Simulators** (Dispositivi e simulatori) di Xcode. 

    - Se Xcode **non** mostra il dispositivo come connesso, provare ad [associare](#pair) nuovamente il dispositivo.

    - Se Xcode mostra il dispositivo come connesso, provare a riavviare Visual Studio e il dispositivo.

3. Se non è ancora stato fatto, sarà necessario effettuare il [provisioning](~/ios/get-started/installation/device-provisioning/index.md) del dispositivo.

4. Se si verificano problemi con questa funzionalità che non possono essere corretti tramite i passaggi precedenti, inviare una segnalazione nella [community degli sviluppatori](https://developercommunity.visualstudio.com/spaces/41/index.html).

## <a name="related-links"></a>Collegamenti correlati

- [Pair a wireless device with Xcode (Associare un dispositivo wireless con Xcode)](https://help.apple.com/xcode/mac/9.0/index.html?localePath=en.lproj#/devbc48d1bad)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Debug-to-iOS-Devices-Over-Wi-Fi/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
