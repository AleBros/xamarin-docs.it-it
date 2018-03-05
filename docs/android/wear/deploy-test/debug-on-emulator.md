---
title: Eseguire il debug di Android usura in un emulatore
description: Questi articoli viene illustrato come eseguire il debug di un'applicazione di xamarin usura in un emulatore.
ms.topic: article
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 1ad3c193261bf22b7ee344aa1ccabb226533b907
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="debug-android-wear-on-an-emulator"></a>Eseguire il debug di Android usura in un emulatore

_Questi articoli viene illustrato come eseguire il debug di un'applicazione di xamarin usura in un emulatore._

## <a name="debug-wear-on-emulator-overview"></a>Eseguire il debug usura di panoramica dell'emulatore

Lo sviluppo di applicazioni, usare Android richiede l'esecuzione dell'applicazione, uno su hardware fisico o un emulatore o simulatore. L'uso di hardware è l'approccio migliore, ma non sempre il più pratico. In molti casi, può essere più semplice e più conveniente per simulare/emulare hardware, usare Android usando un emulatore, come descritto di seguito. Se non sono ancora familiarità con il processo di distribuzione ed esecuzione di App, usare Android, vedere [salve, usura](~/android/wear/get-started/hello-wear.md).

## <a name="configure-the-android-sdk-emulator"></a>Configurare l'emulatore di Android SDK

Per eseguire l'app usura in un emulatore, è necessario installare l'emulatore di Android SDK Android e configurarlo per Android con accenti. Per emulatore di Android SDK installazione e configurazione informazioni generali, vedere [emulatore di Android SDK](~/android/deploy-test/debugging/android-sdk-emulator/index.md).

Quando si crea un dispositivo virtuale usura, selezionare un profilo di dispositivo, usare Android (ad esempio **Android usura quadrato**). Per migliorare le prestazioni, utilizzare l'usura **x86** CPU/ABI, come illustrato in questo esempio:

[![Configurazione di esempio usura dispositivo virtuale](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png)


## <a name="launch-the-wear-virtual-device"></a>Avviare il dispositivo virtuale usura 

Dopo aver creato un dispositivo virtuale Android accenti, puoi scegliere dal menu a discesa dispositivo nell'IDE prima di iniziare il debug. Se il dispositivo virtuale non è disponibile nel menu a discesa del dispositivo, verificare che il progetto è un Android *accenti* il progetto di app (non un progetto di app Android) e che il livello API di destinazione viene impostata la stessa API a livello di dispositivo virtuale. Ad esempio:

[ ![Scelta di un AVD accenti nel menu dispositivo di Visual Studio](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png)

Una volta avviato l'emulatore Android, xamarin distribuirà usura app nell'emulatore. L'emulatore esegue l'app con l'immagine del dispositivo virtuale configurato.

Non sorprendere se viene visualizzato questo (o un'altra schermata intermedio) inizialmente. L'emulatore di espressioni di controllo può richiedere del tempo di avvio: 

![Guardare l'emulatore consente di visualizzare solo un minuto...](debug-on-emulator-images/please-wait.png)

L'emulatore può essere lasciato in esecuzione. Non è necessario arrestarlo e riavviarlo ogni volta che si esegue l'app.

 
## <a name="summary"></a>Riepilogo
 
Questa guida viene spiegato come configurare l'emulatore di Android SDK per lo sviluppo di usura e avviare un dispositivo virtuale usura per il debug.
