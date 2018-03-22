---
title: Risoluzione dei problemi di cartelle di lavoro di Xamarin in Android
ms.topic: article
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 530abec733ec1d842559bf9c898217a8e45465aa
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2018
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>Risoluzione dei problemi di cartelle di lavoro di Xamarin in Android

## <a name="emulator-support"></a>Supporto dell'emulatore

Per eseguire una cartella di lavoro Android, un emulatore Android deve essere disponibile per l'utilizzo. Non sono supportati i dispositivi Android fisici.

Se il computer supporta, è consigliabile emulatore Google insieme HAXM.
Se è abilitata nel sistema di Hyper-V, usare invece l'emulatore Android di Visual Studio.

È necessario disporre di un emulatore che esegue Android 5.0 o versione successiva. Gli emulatori ARM non sono supportati. Utilizzare `x86` o `x86_64` solo per dispositivi.

Leggere [documentazione su come configurare gli emulatori Android] [ android-emu] se non si ha familiarità con il processo.

> [!NOTE]
> 1.1 e versioni precedenti delle cartelle di lavoro richiederà provare (e non sarà!) per utilizzare ARM emulatori se sono disponibili. Per evitare l'emulatore di avvio x86 desiderato prima di aprire o creare una cartella di lavoro Android. Le cartelle di lavoro preferiranno sempre di connettersi a un emulatore in esecuzione, purché sia compatibile.

## <a name="workbooks-wont-load"></a>Non caricare le cartelle di lavoro

### <a name="workbook-window-spins-forever-never-loads-windows"></a>Cartella di lavoro esegue rotazioni sempre, mai Carica (Windows)

Verificare innanzitutto che l'emulatore ha accesso alla rete completamente funzionante testando un sito Web nel browser web dell'emulatore.

### <a name="visual-studio-android-emulator-cannot-connect-to-the-internet"></a>Emulatore Android di Visual Studio non è possibile connettersi a internet

Se l'emulatore non dispone di accesso alla rete, si potrebbe essere necessario seguire questi passaggi per risolvere il commutatore di rete Hyper-V. Se si passa tra reti Wi-Fi spesso occorre ripetere periodicamente questo:

0. **Verificare che eventuali operazioni di rete critiche siano completi, come si potrebbero essere temporaneamente interrotte Windows da internet.**
1. Chiudere gli emulatori.
2. Aprire `Hyper-V Manager`.
3. In `Actions`aprire `Virtual Switch Manager...`.
4. Eliminare tutti i commutatori virtuali.
5. Fare clic su `OK`.
6. Avvia l'emulatore Android di Visual Studio. È probabilmente dovrà ricreare commutatore di rete virtuale.
7. Verificare che il browser dell'emulatore di Visual Studio Android può accedere a internet.

[android-emu]: https://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debug-on-emulator/


## <a name="related-links"></a>Collegamenti correlati

- [Segnalazione dei bug](~/tools/workbooks/install.md#reporting-bugs)
