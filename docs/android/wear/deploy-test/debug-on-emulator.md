---
title: Eseguire il debug di Android usura in un emulatore
description: Questi articoli illustrano come eseguire il debug di un'applicazione di xamarin. Android usura in un emulatore.
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/21/2018
ms.openlocfilehash: baa8df87caf2c05d7b6202d5160c930e51656e10
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36934980"
---
# <a name="debug-android-wear-on-an-emulator"></a>Eseguire il debug di Android usura in un emulatore

_Questi articoli illustrano come eseguire il debug di un'applicazione di xamarin. Android usura in un emulatore._

## <a name="debug-wear-on-emulator-overview"></a>Eseguire il debug usura panoramica dell'emulatore

Lo sviluppo di applicazioni, usare Android richiede l'esecuzione dell'applicazione, uno su hardware fisico o un emulatore o simulatore. L'uso di hardware è l'approccio migliore, ma non sempre il più pratico. In molti casi, può essere più semplice e più conveniente per simulare/emulare hardware usura Android usando un emulatore, come descritto di seguito. Se non sono ancora familiarità con il processo di distribuzione ed esecuzione di App con Android, vedere [salve, usura](~/android/wear/get-started/hello-wear.md).

## <a name="configure-the-android-emulator"></a>Configurare l'emulatore Android

Per eseguire l'app usura in un emulatore, è necessario installare l'emulatore di Android SDK Android e configurarlo per usare Android. Per emulatore di Android SDK installazione e configurazione informazioni generali, vedere [programma di installazione dell'emulatore Android](~/android/get-started/installation/android-emulator/index.md).

Quando si crea un dispositivo virtuale usura, selezionare un profilo di dispositivo, usare Android (ad esempio **Android usura quadrato**). Per migliorare le prestazioni, utilizzare l'usura **x86** CPU/ABI come illustrato in questo esempio:

[![Configurazione di esempio usura dispositivo virtuale](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png#lightbox)


## <a name="launch-the-wear-virtual-device"></a>Avviare il dispositivo virtuale usura 

Dopo aver creato un dispositivo virtuale Android usura, puoi scegliere dal menu a discesa dispositivo nell'IDE prima di avviare il debug. Se il dispositivo virtuale non è disponibile nel menu a discesa del dispositivo, verificare che il progetto è un Android *usura* progetto di app (non un progetto di app Android) e che il livello API di destinazione è impostata per la stessa API di livello come il dispositivo virtuale. Ad esempio:

[![Scelta di un AVD usura nel menu dispositivo di Visual Studio](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png#lightbox)

Una volta avviato l'emulatore Android, xamarin. Android verrà distribuire l'app usura all'emulatore. L'emulatore esegue l'app con l'immagine del dispositivo virtuale configurato.

Non è sorprendente se viene visualizzato questo (o un'altra schermata intermedio) inizialmente. L'emulatore di espressioni di controllo può richiedere del tempo di avvio: 

![Guardare l'emulatore consente di visualizzare solo un minuto...](debug-on-emulator-images/please-wait.png)

L'emulatore può essere lasciato in esecuzione. Non è necessario arrestarlo e riavviarlo ogni volta che si esegue l'app.

 
## <a name="summary"></a>Riepilogo
 
Questa guida viene spiegato come configurare l'emulatore Android per lo sviluppo usura e avviare un dispositivo virtuale usura per il debug.
