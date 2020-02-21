---
title: Accelerazione hardware per le prestazioni dell'emulatore (Hyper-V e HAXM)
description: Questo articolo descrive come usare le funzionalità di accelerazione hardware del computer per ottimizzare le prestazioni dell'emulatore Android.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/27/2018
ms.openlocfilehash: a724a21dfffead307ca3d65d5ff134cf2d7c90db
ms.sourcegitcommit: 24883be72e485e5311dd0eb91f9a22f78eeec11a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2020
ms.locfileid: "77374036"
---
# <a name="hardware-acceleration-for-emulator-performance-hyper-v--haxm"></a>Accelerazione hardware per le prestazioni dell'emulatore (Hyper-V e HAXM)

_Questo articolo descrive come usare le funzionalità di accelerazione hardware del computer per ottimizzare le prestazioni dell'emulatore Android._

Per gli sviluppatori, Visual Studio semplifica il test e il debug del codice delle applicazioni Xamarin.Android con l'emulatore Android nelle situazioni in cui un dispositivo Android non è disponibile o risulta poco pratico.
Tuttavia, l'emulatore Android viene eseguito troppo lentamente se nel computer che lo esegue non è disponibile l'accelerazione hardware. È possibile migliorare notevolmente le prestazioni dell'emulatore Android usando immagini x86 speciali del dispositivo virtuale insieme alle funzionalità di virtualizzazione del computer.

| Scenario    | HAXM        | WHPX       | Hypervisor.Framework |
| ----------- | ----------- | -----------| ----------- |
| È disponibile un processore Intel | x | x | x |
| È disponibile un processore AMD   |   | x |   |
| Si vuole supportare Hyper-V |   | x |   |
| Si vuole supportare la virtualizzazione annidata |   | Limitato |   |
| Si vogliono usare tecnologie come Docker  |   | x | x |

::: zone pivot="windows"

## <a name="accelerating-android-emulators-on-windows"></a>Accelerazione di emulatori Android in Windows

Le tecnologie di virtualizzazione seguenti sono disponibili per l'accelerazione dell'emulatore Android:

1. **Microsoft Hyper-V e la piattaforma WHPX (Windows Hypervisor Platform)** .
   [Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/) è una funzionalità di virtualizzazione di Windows che consente l'esecuzione di sistemi di computer virtualizzati in un computer host fisico.

2. **Intel Hardware Accelerated Execution Manager (HAXM)** .
   HAXM è un motore di virtualizzazione per i computer che eseguono CPU Intel.

Per un'esperienza ottimale in Windows, è consigliabile usare WHPX per l'accelerazione dell'emulatore di Android. Se WHPX non è disponibile nel computer in uso, è possibile usare HAXM. L'emulatore Android usa automaticamente l'accelerazione hardware se vengono soddisfatti i criteri seguenti:

- L'accelerazione hardware è disponibile e abilitata nel computer di sviluppo.

- L'emulatore esegue un'immagine del sistema creata per un dispositivo virtuale basato su **x86**.

> [!IMPORTANT]
> Non è possibile eseguire un emulatore con accelerazione della macchina virtuale all'interno di un'altra macchina virtuale, ad esempio una macchina ospitata da VirtualBox, VMWare o Docker. È necessario eseguire l'emulatore Android [direttamente nell'hardware del sistema](https://developer.android.com/studio/run/emulator-acceleration.html#extensions).

Per informazioni sull'avvio e sul debug con l'emulatore Android, vedere [Debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md).

<a name="hyper-v-win" />

## <a name="accelerating-with-hyper-v"></a>Accelerazione con Hyper-V

Prima di abilitare Hyper-V, leggere la sezione seguente per verificare che il computer supporti Hyper-V.

### <a name="verifying-support-for-hyper-v"></a>Verifica del supporto per Hyper-V

Hyper-V viene eseguito nella piattaforma Windows Hypervisor. Per usare l'emulatore Android con Hyper-V, il computer deve soddisfare i criteri seguenti per supportare la piattaforma Windows Hypervisor:

- L'hardware del computer deve soddisfare i requisiti seguenti:

  - CPU Intel o AMD Ryzen a 64 bit con Second Level Address Translation (SLAT).
  - Supporto di CPU per estensione della modalità di monitoraggio VM (VT-c nelle CPU Intel).
  - Almeno 4 GB di memoria.

- Nel BIOS del computer è necessario abilitare gli elementi seguenti:

  - Tecnologia di virtualizzazione (potrebbe avere un'etichetta diversa a seconda del produttore della scheda madre).
  - Protezione esecuzione programmi basata su hardware.

- Il computer deve disporre dell'Aggiornamento di Windows 10 - aprile 2018 (build 1803) o versione successiva. È possibile verificare se la versione di Windows è aggiornata usando la procedura seguente:

  1. Nella casella di ricerca Windows digitare **informazioni**.
  2. Nei risultati della ricerca selezionare **Informazioni sul PC**.
  3. Scorrere verso il basso la finestra **Informazioni su** fino a visualizzare la sezione **Specifiche Windows**.
  4. Verificare che la **versione** sia almeno 1803:

      [![Specifiche Windows](hardware-acceleration-images/win/01-about-windows-w10-sml.png)](hardware-acceleration-images/win/01-about-windows-w10.png#lightbox)

Per verificare che l'hardware e il software del computer siano compatibili con Hyper-V, aprire un prompt dei comandi e digitare il comando seguente:

```cmd
systeminfo
```

Se tutti i requisiti di Hyper-V elencati hanno un valore **Yes**, i computer possono supportare Hyper-V. Esempio:

[![Output di systeminfo di esempio](hardware-acceleration-images/win/02-systeminfo-w158-sml.png)](hardware-acceleration-images/win/02-systeminfo-w158.png#lightbox)

### <a name="enabling-hyper-v-acceleration"></a>Abilitazione dell'accelerazione di Hyper-V

Se il computer soddisfa i criteri sopra riportati, seguire questa procedura per accelerare l'emulatore Android con Hyper-v:

1. Immettere **funzionalità di windows** nella casella di ricerca Windows e selezionare **Attivazione o disattivazione delle funzionalità Windows** nei risultati della ricerca. Nella finestra di dialogo **Funzionalità Windows** abilitare **Hyper-V** e **Piattaforma Windows Hypervisor**:

    [![Abilitazione di Hyper-V e Piattaforma Windows Hypervisor](hardware-acceleration-images/win/03-hyper-v-settings-w158-sml.png)](hardware-acceleration-images/win/03-hyper-v-settings-w158.png#lightbox)

   Dopo aver apportato queste modifiche, riavviare il computer.
   
> [!IMPORTANT]
>
> Nell'aggiornamento di ottobre 2018 di Windows 10 (RS5) e versioni successive, è necessario abilitare solo Hyper-V, perché la piattaforma WHPX (Windows Hypervisor Platform) verrà usata automaticamente.

2. **Installare [Visual Studio 15.8 o versioni successive](https://visualstudio.microsoft.com/vs/)** . Questa versione di Visual Studio offre il supporto IDE per l'esecuzione dell'emulatore Android con Hyper-V.

3. **Installare il pacchetto dell'emulatore Android 27.2.7 o versione successiva**. Per installare questo pacchetto, passare a **Strumenti > Android > Android SDK Manager** in Visual Studio. Selezionare la scheda **Strumenti** e verificare che la versione dell'emulatore Android non sia inferiore alla 27.2.7. Verificare anche che la versione di Android SDK Tools corrisponda alla 26.1.1 o versione successiva:

    [![Finestra di dialogo Android SDK e strumenti Android](hardware-acceleration-images/win/04-sdk-manager-w158-sml.png)](hardware-acceleration-images/win/04-sdk-manager-w158.png#lightbox)

Quando si crea un dispositivo virtuale (vedere [Gestione di dispositivi virtuali con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), assicurarsi di selezionare un'immagine del sistema basata su **x86**. Se si usa un'immagine del sistema basata su ARM, il dispositivo virtuale non verrà accelerato e verrà eseguito lentamente.

## <a name="accelerating-with-haxm"></a>Accelerazione con HAXM

Usare HAXM per accelerare l'emulatore Android se il computer non supporta Hyper-V. È necessario [disabilitare Device Guard](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vswin#disable-devguard) se si intende usare HAXM.

### <a name="verifying-haxm-support"></a>Verifica del supporto HAXM

Per determinare se l'hardware supporta HAXM, seguire i passaggi descritti in [Il mio processore supporta Intel Virtualization Technology?](https://www.intel.com/content/www/us/en/support/processors/000005486.html).
Se l'hardware supporta HAXM, è possibile controllare se HAXM è già installato usando la procedura seguente:

1. Aprire una finestra del prompt dei comandi e digitare il comando seguente:

    ```cmd
    sc query intelhaxm
    ```

2. Esaminare l'output per vedere se il processo HAXM è in esecuzione. In caso affermativo, lo stato di `intelhaxm` verrà visualizzato come `RUNNING`. Esempio:

    ![Output del comando sc query se HAXM è disponibile](hardware-acceleration-images/win/05-sc_query-w158.png)

   Se `STATE` non è impostato su `RUNNING`, HAXM non è installato.

Se il computer può supportare HAXM, ma quest'ultimo non è installato, usare la procedura descritta nella sezione successiva per installare HAXM.

<a name="install-haxm-win" />

### <a name="installing-haxm"></a>Installazione di HAXM

I pacchetti di installazione di HAXM per Windows sono disponibili nella pagina [Intel Hardware Accelerated Execution Manager](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager). Usare la procedura seguente per scaricare e installare HAXM:

1. Scaricare dal sito Web di Intel la versione più recente del programma di installazione del [motore di virtualizzazione HAXM](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager/) per Windows. Scaricando il programma di installazione di HAXM direttamente dal sito Web di Intel, si può essere certi di usare la versione più recente.

2. Eseguire **intelhaxm-android.exe** per avviare il programma di installazione di HAXM. Accettare i valori predefiniti nelle finestre di dialogo del programma di installazione:

   ![Finestra di installazione di Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/win/06-haxm-installer.png)

Quando si crea un dispositivo virtuale (vedere [Gestione di dispositivi virtuali con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), assicurarsi di selezionare un'immagine del sistema basata su **x86**. Se si usa un'immagine del sistema basata su ARM, il dispositivo virtuale non verrà accelerato e verrà eseguito lentamente.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per informazioni su come risolvere i problemi di accelerazione hardware, vedere la guida alla [risoluzione dei problemi](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vswin#accel-issues-win) dell'emulatore Android.

::: zone-end
::: zone pivot="macos"

## <a name="accelerating-android-emulators-on-macos"></a>Accelerazione di emulatori Android in macOS

Le tecnologie di virtualizzazione seguenti sono disponibili per l'accelerazione dell'emulatore Android:

1. **Framework di Hypervisor di Apple**.
   [Hypervisor](https://developer.apple.com/documentation/hypervisor) è una funzionalità di macOS 10.10 e versioni successive che consente di eseguire macchine virtuali in un computer Mac.

2. **Intel Hardware Accelerated Execution Manager (HAXM)** .
   [HAXM](https://software.intel.com/articles/intel-hardware-accelerated-execution-manager-intel-haxm) è un motore di virtualizzazione per i computer che eseguono CPU Intel.

È consigliabile usare il framework Hypervisor per l'accelerazione dell'emulatore Android. Se il framework Hypervisor non è disponibile nel computer Mac, è possibile usare HAXM. L'emulatore Android usa automaticamente l'accelerazione hardware se vengono soddisfatti i criteri seguenti:

- L'accelerazione hardware è disponibile e abilitata nel computer di sviluppo.

- L'emulatore esegue un'immagine del sistema creata per un dispositivo virtuale basato su **x86**.

> [!IMPORTANT]
>
> Non è possibile eseguire un emulatore con accelerazione della macchina virtuale all'interno di un'altra macchina virtuale, ad esempio una macchina ospitata da VirtualBox, VMware o Docker. È necessario eseguire l'emulatore Android [direttamente nell'hardware del sistema](https://developer.android.com/studio/run/emulator-acceleration.html#extensions).

Per informazioni sull'avvio e sul debug con l'emulatore Android, vedere [Debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md).

<a name="hypervisor" />

## <a name="accelerating-with-the-hypervisor-framework"></a>Accelerazione con il framework Hypervisor

Per usare l'emulatore Android con il framework Hypervisor, il computer Mac deve soddisfare i criteri seguenti:

- Il computer Mac deve eseguire macOS 10.10 o versioni successive.

- La CPU del Mac deve essere in grado di supportare il framework Hypervisor.

Se il computer Mac soddisfa questi criteri, l'emulatore Android userà automaticamente il framework Hypervisor per l'accelerazione. Se non si è certi che il framework Hypervisor sia supportato nel computer Mac, vedere la guida alla [risoluzione dei problemi](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vsmac#hypervisor-issues) per informazioni su come verificare se il Mac supporta Hypervisor.

Se il framework Hypervisor non è supportato da Mac, è possibile usare HAXM per accelerare l'emulatore Android (descritto di seguito).

<a name="haxm-mac" />

## <a name="accelerating-with-haxm"></a>Accelerazione con HAXM

Se il computer Mac non supporta il framework Hypervisor (o si usa una versione di macOS precedente a 10.10), è possibile usare **Intel Hardware Accelerated Execution Manager** ([HAXM](https://software.intel.com/articles/intel-hardware-accelerated-execution-manager-intel-haxm)) per velocizzare l'emulatore Android.

Prima di usare l'emulatore Android con HAXM per la prima volta, è consigliabile verificare che HAXM sia installato e disponibile per l'uso con l'emulatore Android.

### <a name="verifying-haxm-support"></a>Verifica del supporto HAXM

È possibile controllare se HAXM è già installato usando la procedura seguente:

1. Aprire un terminale e immettere il comando seguente:

    ```bash
    ~/Library/Developer/Xamarin/android-sdk-macosx/tools/emulator -accel-check
    ```

   Questo comando presuppone che Android SDK sia installato nel percorso predefinito **~/Library/Developer/Xamarin/android-sdk-macosx**; in caso contrario, modificare il percorso e selezionare la posizione di Android SDK nel computer Mac.

2. Se HAXM è installato, il comando precedente restituirà un messaggio simile al risultato seguente:

    ```bash
    HAXM version 7.2.0 (3) is installed and usable.
    ```

   Se HAXM *non* è installato, verrà restituito un messaggio simile all'output seguente:

    ```bash
    HAXM is not installed on this machine (/dev/HAX is missing).
    ```

Se HAXM non è installato, usare la procedura descritta nella sezione successiva per installare HAXM.

<a name="install-haxm-mac" />

### <a name="installing-haxm"></a>Installazione di HAXM

I pacchetti di installazione di HAXM per macOS sono disponibili nella pagina [Intel Hardware Accelerated Execution Manager](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager). Usare la procedura seguente per scaricare e installare HAXM:

1. Scaricare dal sito Web di Intel la versione più recente del programma di installazione del [motore di virtualizzazione HAXM](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager/) per macOS.

2. Eseguire il programma di installazione di HAXM. Accettare i valori predefiniti nelle finestre di dialogo del programma di installazione:

   [![Finestra di installazione di Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/mac/01-haxm-installer-sml.png)](hardware-acceleration-images/mac/01-haxm-installer.png#lightbox)

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per informazioni su come risolvere i problemi di accelerazione hardware, vedere la guida alla [risoluzione dei problemi](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vsmac#accel-issues-mac) dell'emulatore Android.

::: zone-end

## <a name="related-links"></a>Collegamenti correlati

- [Run Apps on the Android Emulator](https://developer.android.com/studio/run/emulator) (Eseguire app nell'emulatore Android)
