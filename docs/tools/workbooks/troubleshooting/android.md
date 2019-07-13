---
title: Risoluzione dei problemi delle cartelle di lavoro Xamarin in Android
description: Questo documento vengono forniti suggerimenti sulla risoluzione dei problemi per l'utilizzo di cartelle di lavoro di Xamarin in Android. Viene descritto il supporto dell'emulatore, le cartelle di lavoro non vengono caricati e altri argomenti.
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 08fa7f57f3fe44721bc00f0d59ed5df93300cf1e
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864039"
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>Risoluzione dei problemi delle cartelle di lavoro Xamarin in Android

## <a name="emulator-support"></a>Supporto dell'emulatore

Per eseguire una cartella di lavoro Android, un emulatore Android deve essere disponibile per l'uso. Non sono supportati i dispositivi Android fisici.

Se il computer supporta, è consigliabile emulatore di Google con HAXM.
Se è necessario avere abilitato nel sistema di Hyper-V, procedere invece con l'emulatore Android di Visual Studio.

È necessario avere un emulatore che esegue Android 5.0 o versioni successive. Gli emulatori ARM non sono supportati. Uso `x86` o `x86_64` solo per i dispositivi.

Leggi [la documentazione su come configurare emulatori Android][android-emu] se non si ha familiarità con il processo.

> [!NOTE]
> Cartelle di lavoro 1.1 e versioni precedenti verranno provare (e non riuscire!) per gli emulatori ARM usare se sono disponibili. Per ovviare a questo, emulatore di avvio x86 di propria scelta prima di aprire o creare una cartella di lavoro Android. Le cartelle di lavoro preferiranno sempre di connettersi a un emulatore in esecuzione, purché sia compatibile.

## <a name="workbooks-wont-load"></a>Non caricherà cartelle di lavoro

### <a name="workbook-window-spins-forever-never-loads-windows"></a>Finestra della cartella di lavoro fa girare per sempre, mai carichi (Windows)

In primo luogo, verificare che l'emulatore abbia accesso alla rete completamente funzionante eseguendo il test di qualsiasi sito Web nel browser web dell'emulatore.

### <a name="visual-studio-android-emulator-cannot-connect-to-the-internet"></a>Emulatore Android di Visual Studio non è possibile connettersi a internet

Se l'emulatore non ha accesso alla rete, devi seguire questi passaggi per risolvere il commutatore di rete Hyper-V. Se si passa tra le reti Wi-Fi spesso occorre ripetere questa operazione periodicamente:

1. **Assicurarsi che eventuali operazioni di rete critiche vengono completate, come si può disconnettere temporaneamente Windows da internet.**
1. Chiudere gli emulatori.
1. Aprire `Hyper-V Manager`.
1. Sotto `Actions`, aprire `Virtual Switch Manager...`.
1. Eliminare tutti i commutatori virtuali.
1. Fare clic su `OK`.
1. Avviare l'emulatore Android di Visual Studio. Richiederà probabilmente ricreare commutatore di rete virtuale.
1. Verificare che il browser dell'emulatore di Visual Studio Android può accedere a internet.

[android-emu]: https://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debug-on-emulator/


## <a name="related-links"></a>Collegamenti correlati

- [Segnalazione dei bug](~/tools/workbooks/install.md#reporting-bugs)
