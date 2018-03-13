---
title: Risoluzione dei problemi dell'emulatore di Android SDK
description: Come identificare e risolvere i problemi dell'emulatore di Android SDK
ms.topic: article
ms.prod: xamarin
ms.assetid: 4B05C3C5-E1F6-47A9-B098-C31E630194F6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 486df3bbee3f8af511140e2d287f9f95571c7b3d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="android-sdk-emulator-troubleshooting"></a>Risoluzione dei problemi dell'emulatore di Android SDK

In questo articolo vengono illustrati i problemi e i messaggi di avviso più comuni relativi all'emulatore di Android SDK (e le soluzioni corrispondenti).
 
<a name="perfwarn" />

## <a name="performance-warnings"></a>avvisi di prestazioni

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

A partire da Visual Studio 2017 versione 15.4, potrebbe essere visualizzata una finestra di dialogo di avviso per le prestazioni durante la prima distribuzione dell'app nell'emulatore di Android SDK. Queste finestre di dialogo di avviso sono spiegate di seguito.

### <a name="computer-does-not-contain-an-intel-procesor"></a>Computer Does Not Contain an Intel Processor (Il computer non contiene un processore Intel)

![Computer Does Not Contain an Intel Processor (Il computer non contiene un processore Intel)](troubleshooting-images/01-no-intel-processor.png)

Quando viene visualizzata questa finestra di dialogo, il computer non ha un processore Intel, necessario per l'accelerazione dell'emulatore di Android SDK. Se il computer non ha un processore Intel, è consigliabile usare un dispositivo Android fisico per lo sviluppo.

### <a name="hyper-v-is-installed-or-active"></a>Hyper-V Is Installed or Active (Hyper-V è installato o attivo)

![Hyper-V Is Installed or Active (Hyper-V è installato o attivo)](troubleshooting-images/02-hyper-v-active.png)

Quando viene visualizzata questa finestra di dialogo, Hyper-V è installato o è attivo e deve essere disabilitato. In [Disabilitazione di Hyper-V](~/android/get-started/installation/android-emulator/hardware-acceleration.md#disable-hyperv) viene spiegato come risolvere questo problema. 

### <a name="haxm-is-not-installed"></a>HAXM non è installato

![HAXM non è installato](troubleshooting-images/03-haxm-not-installed.png)

Questa finestra di dialogo indica che il computer ha un processore Intel, Hyper-V è disabilitato, ma HAXM non è installato.
In [Installing HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm) (Installazione di HAXM) è disponibile una descrizione della procedura per installare HAXM.

### <a name="haxm-process-not-running"></a>HAXM Process Not Running (Processo HAXM non in esecuzione)

![HAXM Process Not Running (Processo HAXM non in esecuzione)](troubleshooting-images/04-haxm-process-not-running.png)

Questa finestra di dialogo viene visualizzata se il computer ha un processore Intel, Hyper-V è disabilitato, Intel HAXM è installato, ma il processo HAXM non è in esecuzione. Per risolvere questo problema. aprire una finestra del prompt dei comandi e immettere il comando seguente:

```cmd
sc query intelhaxm
```

Se il processo HAXM è in esecuzione, dovrebbe essere visualizzato output simile al seguente:

```cmd
SERVICE_NAME: intelhaxm
    TYPE               : 1  KERNEL_DRIVER
    STATE              : 4  RUNNING
                            (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
    WIN32_EXIT_CODE    : 0  (0x0)
    SERVICE_EXIT_CODE  : 0  (0x0)
    CHECKPOINT         : 0x0
    WAIT_HINT          : 0x0
```


Se **STATE** non è impostato su **RUNNING**, per risolvere il problema vedere [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Come usare Intel Hardware Accelerated Execution Manager).


### <a name="other-failures"></a>Altri errori

![Altri errori](troubleshooting-images/05-other-failure.png)

Questa finestra di dialogo viene visualizzata se il computer ha un processore Intel, Hyper-V è disabilitato, Intel HAXM è installato, il processo HAXM è in esecuzione ma l'avvio dell'emulatore non riesce per motivi sconosciuti.
Per assistenza per la risoluzione di questo errore, vedere [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Come usare Intel Hardware Accelerated Execution Manager).

### <a name="disabling-performance-warnings"></a>Disabilitazione degli avvisi per le prestazioni

Se si preferisce non visualizzare gli avvisi per le prestazioni, è possibile disabilitarli. In Visual Studio fare clic su **Strumenti > Opzioni > Xamarin > Impostazioni Android** e disabilitare l'opzione **Avvisa se l'accelerazione AVD non è supportata (HAXM)**:

[![Disabilitazione degli avvisi per l'accelerazione AVD](troubleshooting-images/win/06-disable-perf-warnings-sml.png)](troubleshooting-images/win/06-disable-perf-warnings.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

A partire da Visual Studio per Mac 7.2 (build 559), potrebbe essere visualizzata una finestra di dialogo di avviso per le prestazioni durante la prima distribuzione dell'app nell'emulatore di Android SDK. Queste finestre di dialogo di avviso sono spiegate di seguito.

### <a name="haxm-is-not-installed"></a>HAXM non è installato

![HAXM non è installato](troubleshooting-images/03-haxm-not-installed.png)

Questa finestra di dialogo indica che HAXM non è installato.
In [Installing HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm) (Installazione di HAXM) è disponibile una descrizione della procedura per installare HAXM.

### <a name="haxm-process-not-running"></a>HAXM Process Not Running (Processo HAXM non in esecuzione)

![HAXM Process Not Running (Processo HAXM non in esecuzione)](troubleshooting-images/04-haxm-process-not-running.png)

Questa finestra di dialogo viene visualizzata se il processo HAXM non è in esecuzione. Per informazioni dettagliate utili per risolvere questo problema, vedere [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Come usare Intel Hardware Accelerated Execution Manager).

### <a name="other-failures"></a>Altri errori

![Altri errori](troubleshooting-images/05-other-failure.png)

Questa finestra di dialogo viene visualizzata se l'emulatore non viene avviato per qualche motivo sconosciuto. Per assistenza per la risoluzione di questo errore, vedere [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Come usare Intel Hardware Accelerated Execution Manager).

-----


## <a name="solutions-to-common-problems"></a>Soluzioni per problemi comuni

Molti problemi comuni dell'emulatore di Android SDK possono essere risolti apportando modifiche di configurazione nel computer o installando software aggiuntivo. Nelle sezioni seguenti vengono descritti questi problemi e le relative soluzioni.


### <a name="deployment-issues"></a>Problemi di distribuzione

Se viene visualizzato un errore per segnalare l'esito negativo dell'installazione dell'APK nell'emulatore o l'impossibilità di eseguire Android Debug Bridge (**adb**), verificare che Android SDK possa connettersi all'emulatore. A tale scopo, seguire questa procedura:

1. Avviare l'emulatore da **Android Virtual Device (AVD) Manager** (selezionare il dispositivo virtuale e fare clic su **Start**).

2. Aprire un prompt dei comandi e passare alla cartella in cui è installato **adb**. Ad esempio, in Windows, lo strumento potrebbe essere in **C:\\Programmi (x86)\\Android\\android-sdk\\platform-tools\\adb.exe**.

3. Digitare il comando seguente:

   ```shell
   adb devices
   ```

4. Se l'emulatore è accessibile da Android SDK, l'emulatore verrà visualizzato nell'elenco dei dispositivi collegati. Ad esempio:

   ```shell
   List of devices attached
   emulator-5554   device
   ```

5. Se l'emulatore non viene visualizzato nell'elenco, avviare **Android SDK Manager**, applicare tutti gli aggiornamenti, quindi provare ad avviare di nuovo l'emulatore.



### <a name="haxm-issues"></a>Problemi relativi a HAXM

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Se l'emulatore di Android SDK non viene avviato correttamente, la causa è riconducibile in genere a problemi relativi a HAXM. I problemi con HAXM sono spesso il risultato di conflitti con altre tecnologie di virtualizzazione, di impostazioni non corrette o di un driver HAXM non aggiornato.

<a name="virt-conflicts" />

#### <a name="haxm-virtualization-conflicts"></a>Conflitti di virtualizzazione HAXM

HAXM può essere in conflitto con altre tecnologie che usano la virtualizzazione, ad esempio Hyper-V, Windows Device Guard e alcuni software antivirus:

- **Hyper-V** &ndash; Se si usa Windows con Hyper-V abilitato, seguire la procedura in [Disabilitazione di Hyper-V](~/android/get-started/installation/android-emulator/hardware-acceleration.md#disable-hyperv).

- **Device Guard** &ndash; Device Guard e Credential Guard possono impedire la disabilitazione di Hyper-V nei computer Windows. Per disabilitare Device Guard e Credential Guard, vedere [Disabling Device Guard](~/android/get-started/installation/android-emulator/hardware-acceleration.md#disable-devguard) (Disabilitazione di Device Guard).

- **Software antivirus** &ndash; Se si esegue software antivirus che usa la virtualizzazione assistita mediante hardware (ad esempio Avast), disabilitare o disinstallare questo software, riavviare il computer e ritentare l'esecuzione dell'emulatore di Android SDK.


#### <a name="incorrect-bios-settings"></a>Impostazioni del BIOS non corrette

Se si usa HAXM in un PC Windows, HAXM non funzionerà a meno che la tecnologia di virtualizzazione (Intel VT-x) non sia abilitata nel BIOS. Se VT-x è disabilitato, verrà visualizzato un errore simile al seguente quando si tenta di avviare l'emulatore di Android SDK:

**This computer meets the requirements for HAXM, but Intel Virtualization Technology (VT-x) is not turned on.** (Il computer soddisfa i requisiti per HAXM, ma Intel Virtualization Technology (VT-x) non è attivato)

Per risolvere questo errore, avviare il computer nel BIOS, abilitare sia VT-x che SLAT (Second Level Address Translation) e quindi riavviare il computer in Windows.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Se l'emulatore di Android SDK non viene avviato correttamente, la causa è riconducibile in genere a problemi relativi a HAXM. I problemi con HAXM sono spesso il risultato di conflitti con altre tecnologie di virtualizzazione, di impostazioni non corrette o di un driver HAXM non aggiornato. Provare a reinstallare il driver HAXM, usando la procedura descritta in dettaglio in [Installing HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm) (Installazione di HAXM).

-----
