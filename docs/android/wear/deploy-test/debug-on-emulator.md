---
title: Eseguire il debug di Android Wear in un emulatore
description: Questi articoli illustrano come eseguire il debug di un'applicazione xamarin. Android Wear in un emulatore.
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 699fb3cc3a5730e8ab2c677feb7cdfbdcf106aeb
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120554"
---
# <a name="debug-android-wear-on-an-emulator"></a>Eseguire il debug di Android Wear in un emulatore

_Questi articoli illustrano come eseguire il debug di un'applicazione xamarin. Android Wear in un emulatore._

## <a name="debug-wear-on-emulator-overview"></a>Eseguire il debug Wear presentata una panoramica dell'emulatore

Lo sviluppo di applicazioni Android Wear richiede l'esecuzione dell'applicazione su hardware fisico o usando un emulatore o simulatore. L'uso di hardware è l'approccio migliore, ma non sempre il più pratico. In molti casi, può essere più semplice e più conveniente simulare/emulare hardware Android Wear usando un emulatore, come descritto di seguito. Se non si ha ancora familiarità con il processo di distribuzione ed esecuzione di App Android Wear, vedere [salve, Wear](~/android/wear/get-started/hello-wear.md).

## <a name="configure-the-android-emulator"></a>Configurare l'emulatore Android

Per eseguire l'app Wear in un emulatore, è necessario installare l'emulatore Android di Android SDK e configurarlo per Android Wear. Per emulatore di Android SDK installazione e configurazione di informazioni generali, vedere [configurazione dell'emulatore Android](~/android/get-started/installation/android-emulator/index.md).

Quando si crea un dispositivo virtuale Wear, selezionare un profilo di dispositivo Android Wear (ad esempio **Android Wear quadrato**). Per migliorare le prestazioni, usare l'usura **x86** CPU/ABI come illustrato in questo esempio:

[![Configurazione di esempio usura del dispositivo virtuale](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png#lightbox)


## <a name="launch-the-wear-virtual-device"></a>Avviare il dispositivo virtuale Wear 

Dopo aver creato un dispositivo virtuale Android Wear, è possibile scegliere, dal menu a discesa del dispositivo nell'IDE prima di avviare il debug. Se il dispositivo virtuale non è disponibile nel menu a discesa del dispositivo, verificare che il progetto sia un Android *Wear* progetto dell'app (non un progetto di app per Android) e che il livello API di destinazione sia impostata per la stessa API a livello del dispositivo virtuale. Ad esempio:

[![Scelta di un AVD Wear nel menu di Visual Studio dispositivo](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png#lightbox)

Dopo l'avvio dell'emulatore di Android, xamarin. Android distribuirà l'app Wear all'emulatore. L'emulatore esegue l'app con l'immagine del dispositivo virtuale configurato.

Non c'è da stupirsi se viene visualizzato questo errore (o un altro schermo intero intermedio) all'inizio. L'emulatore di espressioni di controllo può richiedere un po' di tempo per l'avvio: 

![Guarda l'emulatore Visualizza solo un minuto...](debug-on-emulator-images/please-wait.png)

L'emulatore può essere lasciato in esecuzione. Non è necessario arrestarlo e riavviarlo ogni volta che si esegue l'app.

 
## <a name="summary"></a>Riepilogo
 
Questa guida ha illustrato come configurare l'emulatore di Android per lo sviluppo di usura e avviare un dispositivo virtuale Wear per eseguire il debug.
