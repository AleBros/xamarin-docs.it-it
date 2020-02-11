---
title: Risoluzione dei problemi di Xamarin Workbooks in Android
description: Questo documento fornisce suggerimenti per la risoluzione dei problemi per l'uso di Xamarin Workbooks in Android. Viene illustrato il supporto dell'emulatore, le cartelle di lavoro che non verranno caricate e altri argomenti.
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: be19005ab1125c060ab0111e9f37568d5f4abe45
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029587"
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>Risoluzione dei problemi di Xamarin Workbooks in Android

## <a name="emulator-support"></a>Supporto emulatore

Per eseguire una cartella di lavoro Android, è necessario che l'emulatore Android sia disponibile per l'uso. I dispositivi Android fisici non sono supportati.

Si consiglia di usare l'emulatore di Google insieme a HAXM se il computer lo supporta.
Se Hyper-V è abilitato nel sistema, è necessario usare Visual Studio emulatore Android.

È necessario avere un emulatore che esegue Android 5,0 o versione successiva. Gli emulatori ARM non sono supportati. Usare solo i dispositivi `x86` o `x86_64`.

Se non si ha familiarità con il processo, consultare la [documentazione relativa alla configurazione degli emulatori Android][android-emu] .

> [!NOTE]
> Le cartelle di lavoro 1,1 e versioni precedenti proveranno (e non riusciranno) a usare gli emulatori ARM se sono disponibili. Per ovviare a questo problema, avviare l'emulatore x86 scelto prima di aprire o creare una cartella di lavoro Android. Per le cartelle di lavoro si preferisce sempre la connessione a un emulatore in esecuzione, purché sia compatibile.

## <a name="workbooks-wont-load"></a>Le cartelle di lavoro non vengono caricate

### <a name="workbook-window-spins-forever-never-loads-windows"></a>La finestra della cartella di lavoro si gira per sempre, mai caricata (Windows)

Verificare prima di tutto che l'emulatore disponga di un accesso alla rete completamente funzionante testando qualsiasi sito Web nel Web browser dell'emulatore.

### <a name="visual-studio-android-emulator-cannot-connect-to-the-internet"></a>emulatore Android di Visual Studio non è in grado di connettersi a Internet

Se l'emulatore non dispone di accesso alla rete, potrebbe essere necessario attenersi alla procedura seguente per correggere il commutire di rete Hyper-V. Se si passa spesso tra le reti Wi-Fi, potrebbe essere necessario ripetere periodicamente questa operazione:

1. **Verificare che tutte le operazioni di rete critiche siano completate, in quanto questa operazione potrebbe temporaneamente disconnettere Windows da Internet.**
1. Chiudere gli emulatori.
1. Aprire `Hyper-V Manager`.
1. In `Actions`aprire `Virtual Switch Manager...`.
1. Elimina tutti i commutatori virtuali.
1. Fare clic su `OK`.
1. Avviare Visual Studio emulatore Android. Probabilmente verrà richiesto di ricreare lo switch di rete virtuale.
1. Verificare che il browser di Visual Studio emulatore Android possa accedere a Internet.

[android-emu]: ~/android/deploy-test/debugging/debug-on-emulator.md

## <a name="related-links"></a>Collegamenti correlati

- [Segnalazione di bug](~/tools/workbooks/install.md#reporting-bugs)
