---
title: Risoluzione dei problemi dell'emulatore Android
description: Questo articolo descrive come individuare e risolvere i problemi che possono verificarsi quando si usa l'emulatore Android.
ms.prod: xamarin
ms.assetid: 4F053CC9-9378-47CB-8002-978A6558C4D0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: 1d13a3dae509fea4a2e955c4ad206a81a57e75ed
ms.sourcegitcommit: 6433b424410a850f504e0f934bbb5baf8f093e49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2018
ms.locfileid: "39067334"
---
# <a name="android-emulator-troubleshooting"></a>Risoluzione dei problemi dell'emulatore Android

_In questo articolo vengono descritti i problemi e i messaggi di avviso più comuni che si verificano durante la configurazione e l'esecuzione dell'emulatore Android, oltre a suggerimenti e soluzioni alternative._

<a name="perfwarn" />

## <a name="performance-warnings"></a>avvisi di prestazioni

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

A partire da Visual Studio 2017 versione 15.4, potrebbe essere visualizzata una finestra di dialogo di avviso per le prestazioni durante la prima distribuzione dell'app nell'emulatore Android. Queste finestre di dialogo di avviso sono spiegate di seguito.

### <a name="computer-does-not-contain-an-intel-procesor"></a>Computer Does Not Contain an Intel Processor (Il computer non contiene un processore Intel)

![Computer Does Not Contain an Intel Processor (Il computer non contiene un processore Intel)](troubleshooting-images/01-no-intel-processor.png)

Quando viene visualizzata questa finestra di dialogo, il computer non ha un processore Intel, necessario per l'accelerazione dell'emulatore di Android SDK. Se il computer non ha un processore Intel, è consigliabile usare un dispositivo Android fisico per lo sviluppo.

### <a name="hyper-v-is-installed-or-active"></a>Hyper-V Is Installed or Active (Hyper-V è installato o attivo)

![Hyper-V Is Installed or Active (Hyper-V è installato o attivo)](troubleshooting-images/02-hyper-v-active.png)

Quando viene visualizzata questa finestra di dialogo, Hyper-V è installato o è attivo e deve essere disabilitato. In [Disabilitazione di Hyper-V](#disable-hyperv) viene spiegato come risolvere questo problema.

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

Se `STATE` non è impostato su `RUNNING`, per risolvere il problema vedere [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Come usare Intel Hardware Accelerated Execution Manager).


### <a name="other-failures"></a>Altri errori

![Altri errori](troubleshooting-images/05-other-failure.png)

Questa finestra di dialogo viene visualizzata se il computer ha un processore Intel, Hyper-V è disabilitato, Intel HAXM è installato, il processo HAXM è in esecuzione ma l'avvio dell'emulatore non riesce per motivi sconosciuti.
Per assistenza per la risoluzione di questo errore, vedere [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Come usare Intel Hardware Accelerated Execution Manager).

### <a name="disabling-performance-warnings"></a>Disabilitazione degli avvisi per le prestazioni

Se si preferisce non visualizzare gli avvisi per le prestazioni, è possibile disabilitarli. In Visual Studio fare clic su **Strumenti > Opzioni > Xamarin > Impostazioni Android** e disabilitare l'opzione **Avvisa se l'accelerazione AVD non è supportata (HAXM)**:

[![Disabilitazione degli avvisi per l'accelerazione AVD](troubleshooting-images/win/06-disable-perf-warnings-sml.png)](troubleshooting-images/win/06-disable-perf-warnings.png#lightbox)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

A partire da Visual Studio per Mac versione 7.2 (build 559), potrebbe essere visualizzata una finestra di dialogo di avviso per le prestazioni durante la prima distribuzione dell'app nell'emulatore Android. Queste finestre di dialogo di avviso sono spiegate di seguito.

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

## <a name="deployment-issues"></a>Problemi di distribuzione

Se viene visualizzato un errore per segnalare l'esito negativo dell'installazione dell'APK nell'emulatore o l'impossibilità di eseguire Android Debug Bridge (**adb**), verificare che Android SDK possa connettersi all'emulatore. A tale scopo, seguire questa procedura:

1. Avviare l'emulatore da **Android Device Manager**, selezionando il dispositivo virtuale e facendo clic su **Start** (Avvia).

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


## <a name="haxm-issues"></a>Problemi relativi a HAXM

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Se l'emulatore Android non viene avviato correttamente, la causa è spesso riconducibile a problemi relativi a HAXM. I problemi con HAXM sono spesso il risultato di conflitti con altre tecnologie di virtualizzazione, di impostazioni non corrette o di un driver HAXM non aggiornato.

<a name="virt-conflicts" />

### <a name="haxm-virtualization-conflicts"></a>Conflitti di virtualizzazione HAXM

HAXM può essere in conflitto con altre tecnologie che usano la virtualizzazione, ad esempio Hyper-V, Windows Device Guard e alcuni software antivirus:

- **Hyper-V** &ndash; Se si usa una versione di Windows precedente all'**Aggiornamento di Windows 10 - aprile 2018 (build 1803)** e Hyper-V è abilitato, seguire la procedura in [Disabilitazione di Hyper-V](#disable-hyperv).

- **Device Guard** &ndash; Device Guard e Credential Guard possono impedire la disabilitazione di Hyper-V nei computer Windows. Per disabilitare Device Guard e Credential Guard, vedere [Disabling Device Guard](#disable-devguard) (Disabilitazione di Device Guard).

- **Software antivirus** &ndash; Se si esegue software antivirus che usa la virtualizzazione assistita mediante hardware (ad esempio Avast), disabilitare o disinstallare questo software, riavviare il computer e ritentare l'esecuzione dell'emulatore di Android SDK.


### <a name="incorrect-bios-settings"></a>Impostazioni del BIOS non corrette

Se si usa HAXM in un PC Windows, HAXM non funzionerà a meno che la tecnologia di virtualizzazione (Intel VT-x) non sia abilitata nel BIOS. Se VT-x è disabilitato, viene visualizzato un errore simile al seguente quando si tenta di avviare l'emulatore Android:

**This computer meets the requirements for HAXM, but Intel Virtualization Technology (VT-x) is not turned on.** (Il computer soddisfa i requisiti per HAXM, ma Intel Virtualization Technology (VT-x) non è attivato)

Per risolvere questo errore, avviare il computer nel BIOS, abilitare sia VT-x che SLAT (Second Level Address Translation) e quindi riavviare il computer in Windows.

<a name="disable-hyperv" />

### <a name="disabling-hyper-v"></a>Disabilitazione di Hyper-V

Se si usa una versione di Windows precedente all'**Aggiornamento di Windows 10 - aprile 2018 (build 1803)** e Hyper-V è abilitato, è necessario disabilitare Hyper-V e riavviare il computer per installare e usare HAXM. Se si usa l'**Aggiornamento di Windows 10 - aprile 2018 (build 1803)** o versione successiva, l'emulatore Android 27.2.7 o versione successiva può usare Hyper-V (anziché HAXM) per l'accelerazione hardware, pertanto non è necessario disabilitare Hyper-V.

È possibile disabilitare Hyper-V dal Pannello di controllo attenendosi alla procedura seguente:

1. Nella casella di ricerca di Windows immettere **Programmi** e quindi fare clic su **Programmi e funzionalità** nei risultati della ricerca.

2. Nella finestra di dialogo **Programmi e funzionalità** del Pannello di controllo fare clic su **Attiva o disattiva funzionalità di Windows**:

    ![Attiva o disattiva le funzionalità Windows](troubleshooting-images/win/07-turn-windows-features.png)

3. Deselezionare **Hyper-V** e riavviare il computer:

    ![Disabilitazione di Hyper-V nella finestra di dialogo Funzionalità Windows](troubleshooting-images/win/08-uncheck-hyper-v.png)

In alternativa, è possibile usare il cmdlet PowerShell seguente per disabilitare Hyper-V:

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

Intel HAXM e Microsoft Hyper-V non possono essere attivi contemporaneamente. Purtroppo, non è possibile passare da Hyper-V a HAXM e viceversa senza riavviare il computer. 

In alcuni casi, non è possibile disabilitare Hyper-V tramite la procedura precedente se sono abilitati Device Guard e Credential Guard. Se non è possibile disabilitare Hyper-V (o se risulta disabilitato ma l'installazione di HAXM ha comunque esito negativo), usare la procedura descritta nella sezione successiva per disabilitare Device Guard e Credential Guard.

<a name="disable-devguard" />

### <a name="disabling-device-guard"></a>Disabilitazione di Device Guard

Device Guard e Credential Guard possono impedire la disabilitazione di Hyper-V nei computer Windows. Questo rappresenta spesso un problema per i computer aggiunti a un dominio che sono configurati e controllati da un'organizzazione.
In Windows 10, attenersi alla seguente procedura per verificare se **Device Guard** è in esecuzione:

1. Nella **casella di ricerca di Windows** digitare **System info** per avviare l'app **System Information**.

2. In **Risorse di sistema** verificare che **Device Guard: sicurezza basata sulla virtualizzazione** sia presente e nello stato **In esecuzione**:

   [![Device Guard è presente e in esecuzione](troubleshooting-images/win/09-device-guard-sml.png)](troubleshooting-images/win/09-device-guard.png#lightbox)

Se Device Guard è abilitato, usare la procedura seguente per disabilitarlo:

1. Verificare che **Hyper-V** sia disabilitato (in **Attiva o disattiva funzionalità di Windows**), come descritto nella sezione precedente.

2. Nella casella di ricerca di Windows digitare **gpedit** e selezionare il risultato della ricerca **Modifica Criteri di gruppo**. Verrà avviato l'**Editor Criteri di gruppo locali**.

3. Nell'**Editor Criteri di gruppo locali** passare a **Configurazione computer > Modelli amministrativi > Sistema > Device Guard**:

   [![Device Guard nell'Editor Criteri di gruppo locali](troubleshooting-images/win/10-group-policy-editor-sml.png)](troubleshooting-images/win/10-group-policy-editor.png#lightbox)

4. Modificare **Attiva sicurezza basata su virtualizzazione** in **Disabilitato** (come illustrato in precedenza) e chiudere l'**Editor Criteri di gruppo locali**.

5. Nella casella di ricerca di Windows digitare **cmd**. Quando viene visualizzato **Prompt dei comandi** nei risultati della ricerca, fare clic con il pulsante destro del mouse su **Prompt dei comandi** e selezionare **Esegui come amministratore**.

6. Copiare e incollare i comandi seguenti nella finestra del prompt dei comandi (se l'unità **Z:** è in uso, selezionare una lettera di unità inutilizzata da usare in alternativa):

        mountvol Z: /s
        copy %WINDIR%\System32\SecConfig.efi Z:\EFI\Microsoft\Boot\SecConfig.efi /Y
        bcdedit /create {0cb3b571-2f2e-4343-a879-d86a476d7215} /d "DebugTool" /application osloader
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} path "\EFI\Microsoft\Boot\SecConfig.efi"
        bcdedit /set {bootmgr} bootsequence {0cb3b571-2f2e-4343-a879-d86a476d7215}
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} loadoptions DISABLE-LSA-ISO,DISABLE-VBS
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} device partition=Z:
        mountvol Z: /d

7. Riavviare il computer. Nella schermata di avvio verrà visualizzata una richiesta simile alla seguente:

   **Disabilitare Credential Guard?**

   Premere il tasto indicato per disabilitare Credential Guard come richiesto.

8. Dopo il riavvio del computer, verificare nuovamente che Hyper-V sia disabilitato (come descritto nei passaggi precedenti).

Se Hyper-V non è disabilitato, i criteri del computer aggiunto al dominio potrebbero impedire la disabilitazione di Device Guard o Credential Guard. In questo caso, è possibile richiedere un'esenzione all'amministratore di dominio in modo da poter rifiutare esplicitamente Credential Guard. In alternativa, è possibile impiegare un computer non aggiunto al dominio per usare HAXM.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Se l'emulatore Android non viene avviato correttamente, la causa è spesso riconducibile a problemi relativi a HAXM. I problemi con HAXM sono spesso il risultato di conflitti con altre tecnologie di virtualizzazione, di impostazioni non corrette o di un driver HAXM non aggiornato. Provare a reinstallare il driver HAXM, usando la procedura descritta in dettaglio in [Installing HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm) (Installazione di HAXM).

-----

