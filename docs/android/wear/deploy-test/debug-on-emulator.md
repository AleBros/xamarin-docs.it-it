---
title: Eseguire il debug di Android Wear in un emulatore
description: Questi articoli illustrano come eseguire il debug di un'applicazione Novell. Android Wear in un emulatore.
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: f085aaffbedb2965222b98a22cf6a4bb2393642b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764031"
---
# <a name="debug-android-wear-on-an-emulator"></a>Eseguire il debug di Android Wear in un emulatore

_Questi articoli illustrano come eseguire il debug di un'applicazione Novell. Android Wear in un emulatore._

## <a name="debug-wear-on-emulator-overview"></a>Panoramica dell'uso di debug nell'emulatore

Per lo sviluppo di applicazioni Android Wear è necessario eseguire l'applicazione, su hardware fisico o usando un emulatore o simulatore. L'uso di hardware è l'approccio migliore, ma non sempre il più pratico. In molti casi, può essere più semplice ed economicamente conveniente simulare/emulare Android Wear hardware usando un emulatore, come descritto di seguito. Se non si ha ancora familiarità con il processo di distribuzione ed esecuzione di app Android Wear, vedere [Hello, Wear](~/android/wear/get-started/hello-wear.md).

## <a name="configure-the-android-emulator"></a>Configurare la emulatore Android

Per eseguire l'app Wear in un emulatore, è necessario installare il Android SDK emulatore Android e configurarlo per Android Wear. Per informazioni generali sull'installazione e sulla configurazione dell'emulatore Android SDK, vedere [emulatore Android installazione](~/android/get-started/installation/android-emulator/index.md).

Quando si crea un dispositivo virtuale Wear, selezionare un profilo del dispositivo Android Wear, ad esempio **Android Wear Square**. Per migliorare le prestazioni, usare il logo **x86** CPU/Abi come illustrato nell'esempio seguente:

[![Esempio di utilizzo della configurazione del dispositivo virtuale](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png#lightbox)

## <a name="launch-the-wear-virtual-device"></a>Avviare il dispositivo virtuale Wear 

Dopo aver creato un dispositivo virtuale Android Wear, è possibile sceglierlo dal menu a discesa del dispositivo nell'IDE prima di avviare il debug. Se il dispositivo virtuale non è disponibile nel pull del dispositivo, verificare che il progetto sia un progetto di app Android *Wear* (non un progetto di app Android) e che il livello API di destinazione sia impostato sullo stesso livello API del dispositivo virtuale. Ad esempio:

[![Scelta di un AVD Wear nel menu dispositivo di Visual Studio](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png#lightbox)

Dopo l'avvio dell'emulatore Android, Novell. Android distribuirà l'app Wear nell'emulatore. L'emulatore esegue l'app con l'immagine del dispositivo virtuale configurato.

Non sorprendere se viene visualizzato inizialmente questo (o un altro schermo interstiziale). L'avvio dell'emulatore Watch può richiedere qualche minuto: 

![L'emulatore Watch Visualizza solo un minuto...](debug-on-emulator-images/please-wait.png)

L'emulatore può essere lasciato in esecuzione. Non è necessario arrestarlo e riavviarlo ogni volta che si esegue l'app.

## <a name="summary"></a>Riepilogo

In questa guida è stato illustrato come configurare la emulatore Android per lo sviluppo di usura e avviare un dispositivo virtuale Wear per il debug.
