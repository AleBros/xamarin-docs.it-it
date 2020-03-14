---
title: Risoluzione dei problemi dell'emulatore Android
description: Questo articolo descrive come individuare e risolvere i problemi che possono verificarsi quando si usa l'emulatore Android.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 4F053CC9-9378-47CB-8002-978A6558C4D0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/27/2018
ms.openlocfilehash: d73c72fa70a22bacf122f5c3957b789914dfd765
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304051"
---
# <a name="android-emulator-troubleshooting"></a>Risoluzione dei problemi dell'emulatore Android

_Questo articolo descrive i messaggi di avviso e i problemi più comuni che si verificano durante la configurazione e l'esecuzione del emulatore Android. Vengono inoltre descritte le soluzioni per la risoluzione di questi errori, oltre a vari suggerimenti per la risoluzione dei problemi che consentono di diagnosticare i problemi dell'emulatore._

::: zone pivot="windows"

## <a name="deployment-issues-on-windows"></a>Problemi di distribuzione in Windows

Alcuni messaggi di errore possono essere visualizzati dall'emulatore quando si distribuisce l'app. Gli errori e le soluzioni più comuni sono illustrati di seguito.

### <a name="deployment-errors"></a>Errori di distribuzione

Se viene visualizzato un errore per segnalare l'esito negativo dell'installazione dell'APK nell'emulatore o l'impossibilità di eseguire Android Debug Bridge (**adb**), verificare che Android SDK possa connettersi all'emulatore. Per verificare la connettività dell'emulatore, procedere come segue:

1. Avviare l'emulatore da **Android Device Manager**, selezionando il dispositivo virtuale e facendo clic su **Start** (Avvia).

2. Aprire un prompt dei comandi e passare alla cartella in cui è installato **adb**. Se Android SDK è installato nel percorso predefinito, **adb** si trova in **C:\\Programmi (x86)\\Android\\android-sdk\\platform-tools\\adb.exe**; in caso contrario, modificare il percorso e selezionare la posizione di Android SDK nel computer.

3. Digitare il comando seguente:

   ```shell
   adb devices
   ```

4. Se l'emulatore è accessibile da Android SDK, l'emulatore verrà visualizzato nell'elenco dei dispositivi collegati. Ad esempio,

   ```shell
   List of devices attached
   emulator-5554   device
   ```

5. Se l'emulatore non viene visualizzato nell'elenco, avviare **Android SDK Manager**, applicare tutti gli aggiornamenti, quindi provare ad avviare di nuovo l'emulatore.

### <a name="mmio-access-error"></a>Errore di accesso MMIO

Se viene visualizzato il messaggio **An MMIO access error has occurred** (Si è verificato un errore di accesso MMIO), riavviare l'emulatore.

<a name="gps-win" />

## <a name="missing-google-play-services"></a>Google Play Services mancante

Se nel dispositivo virtuale in esecuzione nell'emulatore non è installato Google Play Services o Google Play Store, questa condizione è spesso dovuta alla creazione di un dispositivo virtuale senza includere questi pacchetti. Quando si crea un dispositivo virtuale (vedere [Gestione di dispositivi virtuali con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), assicurarsi di selezionare una o entrambe le opzioni seguenti:

- **API di Google** &ndash; Includono Google Play Services nel dispositivo virtuale.
- **Google Play Store** &ndash; Include Google Play Store nel dispositivo virtuale.

Ad esempio, questo dispositivo virtuale includerà Google Play Services e Google Play Store:

[![Dispositivo virtuale Android di esempio con Google Play Services e Google Play Store abilitati](troubleshooting-images/win/00-add-gps-w158-sml.png)](troubleshooting-images/win/00-add-gps-w158.png#lightbox)

> [!NOTE]
> Le immagini Google Play Store sono disponibili solo per alcuni tipi di dispositivi di base, ad esempio Pixel, Pixel 2, Nexus 5 e Nexus 5X.

<a name="perf-win" />

## <a name="performance-issues"></a>Prestazioni

I problemi relativi alle prestazioni sono in genere riconducibili ai problemi seguenti:

- L'emulatore è in esecuzione senza accelerazione hardware.

- Il dispositivo virtuale in esecuzione nell'emulatore non usa un'immagine del sistema basata su x86.

Le sezioni seguenti illustrano questi scenari in modo più dettagliato.

### <a name="hardware-acceleration-is-not-enabled"></a>Non è abilitata l'accelerazione hardware

Se non è abilitata l'accelerazione hardware, l'avvio di un dispositivo virtuale da Device Manager determina l'apertura di una finestra di dialogo con un messaggio di errore indicante che la piattaforma Windows Hypervisor (WHPX) non è configurata correttamente:

![Avviso di Device Manager di esempio](troubleshooting-images/win/01-dev-mgr-warning-w158.png)

Se viene visualizzato questo messaggio di errore, vedere [Problemi di accelerazione Hardware](#accel-issues-win) di seguito per i passaggi da eseguire per verificare e abilitare l'accelerazione hardware.

### <a name="acceleration-is-enabled-but-the-emulator-runs-too-slowly"></a>L'accelerazione è abilitata ma l'esecuzione dell'emulatore è troppo lenta 

Una causa comune all'origine di questo problema è il mancato utilizzo di un'immagine basata su x86 nel dispositivo virtuale (AVD). Quando si crea un dispositivo virtuale (vedere [Gestione di dispositivi virtuali con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), assicurarsi di selezionare un'immagine del sistema basata su x86:

[![Selezione di un'immagine di sistema x86 per un dispositivo virtuale](troubleshooting-images/win/02-x86-virtual-device-w158-sml.png)](troubleshooting-images/win/02-x86-virtual-device-w158.png#lightbox)

<a name="accel-issues-win" />

## <a name="hardware-acceleration-issues"></a>Problemi di accelerazione hardware

Se si usa Hyper-V o HAXM per l'accelerazione hardware, è possibile riscontrare problemi o conflitti di configurazione con altri software nel computer. È possibile verificare se è abilitata l'accelerazione hardware (e quale metodo di accelerazione è usato dall'emulatore) aprendo un prompt dei comandi e immettendo il comando seguente:

```cmd
"C:\Program Files (x86)\Android\android-sdk\emulator\emulator-check.exe" accel
```

Questo comando presuppone che Android SDK sia installato nel percorso predefinito **C:\\Programmi (x86)\\Android\\android-sdk**; in caso contrario, modificare il percorso e selezionare la posizione di Android SDK nel computer.

### <a name="hardware-acceleration-not-available"></a>L'accelerazione hardware non è disponibile

Se è disponibile Hyper-V, viene restituito un messaggio simile all'esempio seguente dal comando **emulator-check.exe accel**:

```cmd
HAXM is not installed, but Windows Hypervisor Platform is available.
```

Se è disponibile HAXM, viene restituito un messaggio simile al seguente:

```cmd
HAXM version 6.2.1 (4) is installed and usable.
```

Se l'accelerazione hardware non è disponibile, viene visualizzato un messaggio simile al seguente (l'emulatore cerca HAXM se non è in grado di trovare Hyper-V):

```cmd
HAXM is not installed on this machine
```

Se l'accelerazione hardware non è disponibile, vedere [Accelerazione con Hyper-V](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vswin#hyper-v-win) per informazioni su come abilitare l'accelerazione hardware nel computer.

### <a name="incorrect-bios-settings"></a>Impostazioni del BIOS non corrette

Se il BIOS non è stato configurato correttamente per supportare l'accelerazione hardware, viene visualizzato un messaggio simile all'esempio seguente quando si esegue il comando **emulator-check.exe accel**:

```cmd
VT feature disabled in BIOS/UEFI
```

Per risolvere questo problema, eseguire il riavvio del computer in modalità BIOS e abilitare le opzioni seguenti:

- Tecnologia di virtualizzazione (potrebbe avere un'etichetta diversa a seconda del produttore della scheda madre).
- Protezione esecuzione programmi imposta dall'hardware.

Se l'accelerazione hardware è abilitata e il BIOS è configurato correttamente, l'emulatore dovrebbe essere in esecuzione con l'accelerazione hardware.
Si potrebbero comunque riscontrare problemi specifici di Hyper-V e HAXM, come descritto di seguito.

### <a name="hyper-v-issues"></a>Problemi di Hyper-V

In alcuni casi, l'abilitazione di **Hyper-V** e della **piattaforma Windows Hypervisor** nella finestra di dialogo **Attivazione o disattivazione delle funzionalità Windows** potrebbe non abilitare correttamente Hyper-V. Per verificare che Hyper-V sia abilitato, procedere come segue:

1. Immettere **powershell** nella casella di ricerca Windows.

2. Fare clic con il pulsante destro del mouse su **Windows PowerShell** nei risultati di ricerca e selezionare **Esegui come amministratore**.

3. Nella console di PowerShell eseguire il comando seguente:

    ```powershell
    Get-WindowsOptionalFeature -FeatureName Microsoft-Hyper-V-All -Online
    ```

    Se Hyper-V non è abilitato, verrà visualizzato un messaggio simile all'esempio seguente per indicare che lo stato di Hyper-V è **disabilitato**:

    ```
    FeatureName      : Microsoft-Hyper-V-All
    DisplayName      : Hyper-V
    Description      : Provides services and management tools for creating and running virtual machines and their resources.
    RestartRequired  : Possible
    State            : Disabled
    CustomProperties : 
    ```

4. Nella console di PowerShell eseguire il comando seguente:

    ```powershell
    Get-WindowsOptionalFeature -FeatureName HypervisorPlatform -Online
    ```

    Se Hypervisor non è abilitato, verrà visualizzato un messaggio simile all'esempio seguente per indicare che lo stato della piattaforma Hypervisor è **disabilitato**:

    ```
    FeatureName      : HypervisorPlatform
    DisplayName      : Windows Hypervisor Platform
    Description      : Enables virtualization software to run on the Windows hypervisor
    RestartRequired  : Possible
    State            : Disabled
    CustomProperties : 
    ```

Se Hyper-V e/o la piattaforma Hypervisor non sono abilitati, usare i comandi PowerShell seguenti per abilitarli:

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
Enable-WindowsOptionalFeature -Online -FeatureName HypervisorPlatform -All
```

Dopo aver completato questi comandi, riavviare il sistema. 

Per altre informazioni sull'abilitazione di Hyper-V (incluse le tecniche per abilitare Hyper-V mediante lo strumento Gestione e manutenzione immagini distribuzione), vedere [Installare Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

### <a name="haxm-issues"></a>Problemi di HAXM

I problemi con HAXM sono spesso il risultato di conflitti con altre tecnologie di virtualizzazione, di impostazioni non corrette o di un driver HAXM non aggiornato.

### <a name="haxm-process-is-not-running"></a>HAXM Process Not Running (Processo HAXM non in esecuzione)

Se HAXM è installato, per verificare che il processo HAXM sia in esecuzione, aprire un prompt dei comandi e immettere il comando seguente:

```cmd
sc query intelhaxm
```

Se il processo HAXM è in esecuzione, dovrebbe essere visualizzato un output simile al seguente:

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

Se `STATE` non è impostato su `RUNNING`, per risolvere il problema vedere [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Come usare Intel Hardware Accelerated Execution Manager).

<a name="virt-conflicts" />

#### <a name="haxm-virtualization-conflicts"></a>Conflitti di virtualizzazione HAXM

HAXM può essere in conflitto con altre tecnologie che usano la virtualizzazione, ad esempio Hyper-V, Windows Device Guard e alcuni software antivirus:

- **Hyper-V** &ndash; Se si usa una versione di Windows precedente all'**Aggiornamento di Windows 10 di aprile 2018 (build 1803)** e Hyper-V è abilitato, seguire la procedura in [Disabilitazione di Hyper-V](#disable-hyperv) per abilitare HAXM.

- **Device Guard** &ndash; Device Guard e Credential Guard possono impedire la disabilitazione di Hyper-V nei computer Windows. Per disabilitare Device Guard e Credential Guard, vedere [Disabling Device Guard](#disable-devguard) (Disabilitazione di Device Guard).

- **Software antivirus** &ndash; Se si esegue software antivirus che usa la virtualizzazione assistita mediante hardware (ad esempio Avast), disabilitare o disinstallare questo software, riavviare il computer e ritentare l'esecuzione dell'emulatore di Android.

#### <a name="incorrect-bios-settings"></a>Impostazioni del BIOS non corrette

Se si usa HAXM in un PC Windows, HAXM non funzionerà a meno che la tecnologia di virtualizzazione (Intel VT-x) non sia abilitata nel BIOS. Se VT-x è disabilitato, viene visualizzato un errore simile al seguente quando si tenta di avviare l'emulatore Android:

**This computer meets the requirements for HAXM, but Intel Virtualization Technology (VT-x) is not turned on.** (Il computer soddisfa i requisiti per HAXM, ma Intel Virtualization Technology (VT-x) non è attivato)

Per risolvere questo errore, avviare il computer nel BIOS, abilitare sia VT-x che SLAT (Second Level Address Translation) e quindi riavviare il computer in Windows.

<a name="disable-hyperv" />

#### <a name="disabling-hyper-v"></a>Disabilitazione di Hyper-V

Se si usa una versione di Windows precedente all'**Aggiornamento di Windows 10 - aprile 2018 (build 1803)** e Hyper-V è abilitato, è necessario disabilitare Hyper-V e riavviare il computer per installare e usare HAXM. Se si usa l'**Aggiornamento di Windows 10 - aprile 2018 (build 1803)** o versione successiva, l'emulatore Android 27.2.7 o versione successiva può usare Hyper-V (anziché HAXM) per l'accelerazione hardware, pertanto non è necessario disabilitare Hyper-V.

È possibile disabilitare Hyper-V dal Pannello di controllo attenendosi alla procedura seguente:

1. Immettere **funzionalità di windows** nella casella di ricerca Windows e selezionare **Attivazione o disattivazione delle funzionalità Windows** nei risultati della ricerca.

2. Deselezionare **Hyper-V**:

    ![Disabilitazione di Hyper-V nella finestra di dialogo Funzionalità Windows](troubleshooting-images/win/03-uncheck-hyper-v.png)

3. Riavvia il computer.

In alternativa, è possibile usare il cmdlet PowerShell seguente per disabilitare l'hypervisor Hyper-V:

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

Intel HAXM e Microsoft Hyper-V non possono essere attivi contemporaneamente. Purtroppo, non è possibile passare da Hyper-V a HAXM e viceversa senza riavviare il computer. 

In alcuni casi, non è possibile disabilitare Hyper-V tramite la procedura precedente se sono abilitati Device Guard e Credential Guard. Se non è possibile disabilitare Hyper-V (o se risulta disabilitato ma l'installazione di HAXM ha comunque esito negativo), usare la procedura descritta nella sezione successiva per disabilitare Device Guard e Credential Guard.

<a name="disable-devguard" />

#### <a name="disabling-device-guard"></a>Disabilitazione di Device Guard

Device Guard e Credential Guard possono impedire la disabilitazione di Hyper-V nei computer Windows. Questa situazione rappresenta spesso un problema per i computer aggiunti a un dominio che sono configurati e controllati da un'organizzazione. In Windows 10, attenersi alla seguente procedura per verificare se **Device Guard** è in esecuzione:

1. Immettere **informazioni di sistema** nella casella di ricerca Windows e selezionare **Informazioni di sistema** nei risultati della ricerca.

2. In **Risorse di sistema** verificare che **Device Guard: sicurezza basata sulla virtualizzazione** sia presente e nello stato **In esecuzione**:

   [![Device Guard è presente e in esecuzione](troubleshooting-images/win/04-device-guard-sml.png)](troubleshooting-images/win/04-device-guard.png#lightbox)

Se Device Guard è abilitato, usare la procedura seguente per disabilitarlo:

1. Verificare che **Hyper-V** sia disabilitato (in **Attiva o disattiva funzionalità di Windows**), come descritto nella sezione precedente.

2. Nella casella di ricerca Windows immettere **gpedit.msc** e selezionare il risultato della ricerca **Modifica Criteri di gruppo**. Verrà avviato l'**Editor Criteri di gruppo locali**.

3. Nell'**Editor Criteri di gruppo locali** passare a **Configurazione computer > Modelli amministrativi > Sistema > Device Guard**:

   [![Device Guard nell'Editor Criteri di gruppo locali](troubleshooting-images/win/05-group-policy-editor-sml.png)](troubleshooting-images/win/05-group-policy-editor.png#lightbox)

4. Modificare **Attiva sicurezza basata su virtualizzazione** in **Disabilitato** (come illustrato in precedenza) e chiudere l'**Editor Criteri di gruppo locali**.

5. Nella casella di ricerca Windows digitare **cmd**. Quando viene visualizzato **Prompt dei comandi** nei risultati della ricerca, fare clic con il pulsante destro del mouse su **Prompt dei comandi** e selezionare **Esegui come amministratore**.

6. Copiare e incollare i comandi seguenti nella finestra del prompt dei comandi (se l'unità **Z:** è in uso, selezionare una lettera di unità inutilizzata da usare in alternativa):

    ```cmd
    mountvol Z: /s
    copy %WINDIR%\System32\SecConfig.efi Z:\EFI\Microsoft\Boot\SecConfig.efi /Y
    bcdedit /create {0cb3b571-2f2e-4343-a879-d86a476d7215} /d "DebugTool" /application osloader
    bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} path "\EFI\Microsoft\Boot\SecConfig.efi"
    bcdedit /set {bootmgr} bootsequence {0cb3b571-2f2e-4343-a879-d86a476d7215}
    bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} loadoptions DISABLE-LSA-ISO,DISABLE-VBS
    bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} device partition=Z:
    mountvol Z: /d
    ```

7. Riavviare il computer. Nella schermata di avvio verrà visualizzata una richiesta simile al messaggio seguente:

   **Disabilitare Credential Guard?**

   Premere il tasto indicato per disabilitare Credential Guard come richiesto.

8. Dopo il riavvio del computer, verificare nuovamente che Hyper-V sia disabilitato (come descritto nei passaggi precedenti).

Se Hyper-V non è disabilitato, i criteri del computer aggiunto al dominio potrebbero impedire la disabilitazione di Device Guard o Credential Guard. In questo caso, è possibile richiedere un'esenzione all'amministratore di dominio in modo da poter rifiutare esplicitamente Credential Guard. In alternativa, è possibile impiegare un computer non aggiunto al dominio per usare HAXM.

## <a name="additional-troubleshooting-tips"></a>Altri suggerimenti sulla risoluzione dei problemi

I suggerimenti seguenti sono spesso utili nella diagnosi dei problemi dell'emulatore Android.

### <a name="starting-the-emulator-from-the-command-line"></a>Avvio dell'emulatore dalla riga di comando

Se l'emulatore non è già in esecuzione, è possibile avviarlo dalla riga di comando (piuttosto che da Visual Studio) per visualizzare l'output corrispondente. In genere, le immagini AVD dell'emulatore Android vengono archiviate nel percorso seguente (sostituire *nomeutente* con il nome utente di Windows):

**C:\\Utenti\\*nomeutente*\\.android\\avd**

È possibile avviare l'emulatore con un'immagine AVD da questo percorso, passando il nome della cartella del dispositivo virtuale Android. Ad esempio, questo comando avvia un dispositivo virtuale Android denominato **Pixel_API_27**:

```cmd
"C:\Program Files (x86)\Android\android-sdk\emulator\emulator.exe" -partition-size 512 -no-boot-anim -verbose -feature WindowsHypervisorPlatform -avd Pixel_API_27 -prop monodroid.avdname=Pixel_API_27
```

Questo esempio presuppone che Android SDK sia installato nel percorso predefinito **C:\\Programmi (x86)\\Android\\android-sdk**; in caso contrario, modificare il percorso e selezionare la posizione di Android SDK nel computer.

L'esecuzione di questo comando genera molte righe di output durante l'avvio dell'emulatore. In particolare verranno indicate righe come quelle dell'esempio seguente se l'accelerazione hardware è abilitata e funziona correttamente (in questo esempio, HAXM verrà usato per l'accelerazione hardware):

```cmd
emulator: CPU Acceleration: working
emulator: CPU Acceleration status: HAXM version 6.2.1 (4) is installed and usable.
```

### <a name="viewing-device-manager-logs"></a>Visualizzazione dei log di Device Manager

Spesso è possibile diagnosticare problemi dell'emulatore visualizzando i log di Device Manager. I log vengono scritti nella seguente posizione:

**C:\\Utenti\\*nomeutente*\\AppData\\Roaming\\XamarinDeviceManager**

È possibile visualizzare ogni file **DeviceManager.log** mediante un editor di testo come Blocco note. La voce di registro di esempio seguente indica che HAXM non è presente nel computer:

```cmd
Component Intel x86 Emulator Accelerator (HAXM installer) r6.2.1 [Extra: (Intel Corporation)] not present on the system
```

::: zone-end
::: zone pivot="macos"

## <a name="deployment-issues-on-macos"></a>Problemi di distribuzione in macOS

Alcuni messaggi di errore possono essere visualizzati dall'emulatore quando si distribuisce l'app. Gli errori e le soluzioni più comuni sono illustrati di seguito.

### <a name="deployment-errors"></a>Errori di distribuzione

Se viene visualizzato un errore per segnalare l'esito negativo dell'installazione dell'APK nell'emulatore o l'impossibilità di eseguire Android Debug Bridge (**adb**), verificare che Android SDK possa connettersi all'emulatore. Per verificare la connettività, procedere come segue:

1. Avviare l'emulatore da **Android Device Manager**, selezionando il dispositivo virtuale e facendo clic su **Start** (Avvia).

2. Aprire un prompt dei comandi e passare alla cartella in cui è installato **adb**. Se Android SDK è installato nel percorso predefinito, **adb** si trova in **~/Library/Developer/Xamarin/android-sdk-macosx/platform-tools/adb**; in caso contrario, modificare il percorso e selezionare la posizione di Android SDK nel computer.

3. Digitare il comando seguente:

   ```shell
   adb devices
   ```

4. Se l'emulatore è accessibile da Android SDK, l'emulatore verrà visualizzato nell'elenco dei dispositivi collegati. Ad esempio,

   ```shell
   List of devices attached
   emulator-5554   device
   ```

5. Se l'emulatore non viene visualizzato nell'elenco, avviare **Android SDK Manager**, applicare tutti gli aggiornamenti, quindi provare ad avviare di nuovo l'emulatore.

### <a name="mmio-access-error"></a>Errore di accesso MMIO

Se viene visualizzato il messaggio **An MMIO access error has occurred** (Si è verificato un errore di accesso MMIO), riavviare l'emulatore.

<a name="gps-mac" />

## <a name="missing-google-play-services"></a>Google Play Services mancante

Se nel dispositivo virtuale in esecuzione nell'emulatore non è installato Google Play Services o Google Play Store, questa condizione è in genere dovuta alla creazione di un dispositivo virtuale senza includere questi pacchetti. Quando si crea un dispositivo virtuale (vedere [Gestione di dispositivi virtuali con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), assicurarsi di selezionare una o entrambe le opzioni seguenti:

- **API di Google** &ndash; Includono Google Play Services nel dispositivo virtuale.
- **Google Play Store** &ndash; Include Google Play Store nel dispositivo virtuale.

Ad esempio, questo dispositivo virtuale includerà Google Play Services e Google Play Store:

[![Dispositivo virtuale Android di esempio con Google Play Services e Google Play Store abilitati](troubleshooting-images/mac/01-google-play-services-m75-sml.png)](troubleshooting-images/mac/01-google-play-services-m75.png#lightbox)

> [!NOTE]
> Le immagini Google Play Store sono disponibili solo per alcuni tipi di dispositivi di base, ad esempio Pixel, Pixel 2, Nexus 5 e Nexus 5X.

<a name="perf-mac" />

## <a name="performance-issues"></a>Prestazioni

I problemi relativi alle prestazioni sono in genere riconducibili ai problemi seguenti:

- L'emulatore è in esecuzione senza accelerazione hardware.

- Il dispositivo virtuale in esecuzione nell'emulatore non usa un'immagine del sistema basata su x86.

Le sezioni seguenti illustrano questi scenari in modo più dettagliato.

### <a name="hardware-acceleration-is-not-enabled"></a>Non è abilitata l'accelerazione hardware

Se non è abilitata l'accelerazione hardware, potrebbe essere visualizzata una finestra di dialogo con un messaggio, ad esempio indicante che **il dispositivo verrà eseguito senza accelerazione**, quando si distribuisce l'app nell'emulatore Android. Se non si è certi se l'accelerazione hardware sia abilitata nel computer (o si intende conoscere la tecnologia che fornisce l'accelerazione), vedere [Problemi di accelerazione hardware](#accel-issues-mac) di seguito per i passaggi da eseguire per verificare e abilitare l'accelerazione hardware.

### <a name="acceleration-is-enabled-but-the-emulator-runs-too-slowly"></a>L'accelerazione è abilitata ma l'esecuzione dell'emulatore è troppo lenta 

Una causa comune all'origine di questo problema è il mancato utilizzo di un'immagine basata su x86 nel dispositivo virtuale. Quando si crea un dispositivo virtuale (vedere [Gestione di dispositivi virtuali con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), assicurarsi di selezionare un'immagine del sistema basata su x86:

[![Selezione di un'immagine di sistema x86 per un dispositivo virtuale](troubleshooting-images/mac/02-x86-virtual-device-m75-sml.png)](troubleshooting-images/mac/02-x86-virtual-device-m75.png#lightbox)

<a name="accel-issues-mac" />

## <a name="hardware-acceleration-issues"></a>Problemi di accelerazione hardware

Se si usa il framework Hypervisor o HAXM per l'accelerazione hardware dell'emulatore, potrebbero verificarsi problemi causati da errori di installazione o una versione obsoleta di macOS. Le sezioni seguenti consentono di risolvere il problema.

<a name="hypervisor-issues" />

### <a name="hypervisor-framework-issues"></a>Problemi del framework Hypervisor

Se si usa macOS 10.10 o versione successiva su un Mac più recente, l'emulatore Android usa automaticamente il framework Hypervisor per l'accelerazione hardware. Tuttavia, alcuni Mac meno recenti o che eseguono una versione di macOS precedente alla 10.10 potrebbero non fornire il supporto per il framework Hypervisor.

Per determinare se il Mac supporta il framework di Hypervisor, aprire un terminale e digitare il comando seguente:

```bash
sysctl kern.hv_support
```

Se il Mac supporta il framework Hypervisor, il comando precedente restituisce il risultato seguente:

```bash
kern.hv_support: 1
```

Se il framework Hypervisor non è disponibile nel computer Mac, è possibile seguire i passaggi descritti in [Accelerazione con HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vsmac#haxm-mac) per usare HAXM per l'accelerazione.

### <a name="haxm-issues"></a>Problemi di HAXM

Se l'emulatore Android non viene avviato correttamente, il problema è spesso riconducibile a errori relativi a HAXM. I problemi con HAXM sono spesso il risultato di conflitti con altre tecnologie di virtualizzazione, di impostazioni non corrette o di un driver HAXM non aggiornato. Provare a reinstallare il driver HAXM, usando la procedura descritta in dettaglio in [Installing HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vsmac#install-haxm-mac) (Installazione di HAXM).

## <a name="additional-troubleshooting-tips"></a>Altri suggerimenti sulla risoluzione dei problemi

I suggerimenti seguenti sono spesso utili nella diagnosi dei problemi dell'emulatore Android.

### <a name="starting-the-emulator-from-the-command-line"></a>Avvio dell'emulatore dalla riga di comando

Se l'emulatore non è già in esecuzione, è possibile avviarlo dalla riga di comando (piuttosto che da Visual Studio per Mac) per visualizzare l'output corrispondente. In genere, le immagini AVD dell'emulatore Android vengono archiviate nel percorso seguente:

**~/.android/avd**

È possibile avviare l'emulatore con un'immagine AVD da questo percorso, passando il nome della cartella del dispositivo virtuale Android. Ad esempio, questo comando avvia un dispositivo virtuale Android denominato **Pixel_2_API_28**:

```cmd
~/Library/Developer/Xamarin/android-sdk-macosx/emulator/emulator -partition-size 512 -no-boot-anim -verbose -feature WindowsHypervisorPlatform -avd Pixel_2_API_28 -prop monodroid.avdname=Pixel_2_API_28
```

Se Android SDK è installato nel percorso predefinito, l'emulatore si trova nella directory **~/Library/Developer/Xamarin/android-sdk-macosx/emulator**; in caso contrario, modificare il percorso e selezionare la posizione di Android SDK nel Mac.

L'esecuzione di questo comando genera molte righe di output durante l'avvio dell'emulatore. In particolare verranno indicate righe come quelle dell'esempio seguente se l'accelerazione hardware è abilitata e funziona correttamente (in questo esempio, il framework Hypervisor verrà usato per l'accelerazione hardware):

```cmd
emulator: CPU Acceleration: working
emulator: CPU Acceleration status: Hypervisor.Framework OS X Version 10.13
```

### <a name="viewing-device-manager-logs"></a>Visualizzazione dei log di Device Manager

Spesso è possibile diagnosticare problemi dell'emulatore visualizzando i log di Device Manager. I log vengono scritti nella seguente posizione:

**~/Library/Logs/XamarinDeviceManager**

È possibile visualizzare ogni file **Android Devices.log** facendo doppio clic per aprirlo nell'app Console. La voce di registro di esempio seguente indica che HAXM non è disponibile:

```cmd
Component Intel x86 Emulator Accelerator (HAXM installer) r6.2.1 [Extra: (Intel Corporation)] not present on the system
```

::: zone-end
