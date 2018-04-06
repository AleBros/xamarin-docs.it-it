---
title: Accelerazione hardware dell'emulatore Android
description: Come preparare il computer per ottenere prestazioni ottimali dall'emulatore Android SDK
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 12/22/2017
ms.openlocfilehash: f074bca7571188b14a36bd4e6c59a6fdf8df9339
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="android-emulator-hardware-acceleration"></a>Accelerazione hardware dell'emulatore Android

Poiché l'emulatore di Android SDK è estremamente lento senza l'accelerazione hardware, è consigliabile usare Intel HAXM (Hardware Accelerated Execution Manager) per migliorarne notevolmente le prestazioni.


## <a name="haxm-overview"></a>Panoramica di HAXM

HAXM è un motore di virtualizzazione assistita mediante hardware (hypervisor) che usa Intel Virtualization Technology (VT) per velocizzare l'emulazione delle app Android in un computer host. In combinazione con le immagini x86 dell'emulatore Android fornite da Intel e dalla versione ufficiale di Android SDK Manager, HAXM consente di velocizzare l'emulazione di Android nei sistemi abilitati per VT. Se si sviluppa in un computer con una CPU Intel che dispone di funzionalità VT, è possibile trarre vantaggio da HAXM per velocizzare notevolmente l'emulatore di Android SDK (se non si è certi che la CPU supporti VT, vedere l'articolo su come [determinare se il processore supporta Intel Virtualization Technology](https://www.intel.com/content/www/us/en/support/processors/000005486.html)).

L'emulatore di Android SDK usa automaticamente HAXM quando è disponibile. Quando si seleziona un dispositivo virtuale basato su **x86** (come descritto in [Configurazione e uso](~/android/deploy-test/debugging/android-sdk-emulator/index.md)), tale dispositivo virtuale userà HAXM per l'accelerazione hardware. Prima di usare l'emulatore di Android SDK per la prima volta, è consigliabile verificare che HAXM sia installato e disponibile per l'emulatore di Android SDK.

> [!NOTE]
> Non è possibile eseguire HAXM in una macchina virtuale.


## <a name="verifying-haxm-installation"></a>Verifica dell'installazione di HAXM

È possibile verificare se HAXM è disponibile visualizzando la finestra **Avvio dell'emulatore Android** durante l'avvio dell'emulatore. Per avviare l'emulatore di Android SDK, seguire questa procedura:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Avviare Gestione emulatori Android facendo clic su **Strumenti > Android > Gestione emulatori Android**:

    [![Posizione della voce di menu Gestione emulatori Android](hardware-acceleration-images/win/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/win/01-avd-manager-menu-item.png#lightbox)

2. Se viene visualizzata una finestra di dialogo **Avviso prestazioni** simile alla seguente, HAXM non è ancora installato o configurato correttamente nel computer:

    ![Finestra di dialogo Avviso prestazioni che indica che HAXM non è pronto](hardware-acceleration-images/win/11-perf-warn.png)

   Se viene visualizzata una finestra di dialogo **Avviso prestazioni** simile alla seguente, vedere [Avvisi di prestazioni](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#perfwarn) per identificare la causa e risolvere il problema.

3. Selezionare un'immagine **x86** (ad esempio, **VisualStudio\_android-23\_x86\_phone**), fare clic su **Avvia**, quindi di nuovo su **Avvia**:

    ![Avvio dell'emulatore di Android SDK con un'immagine di dispositivo virtuale predefinita](hardware-acceleration-images/win/02-start-default-avd.png)

4. Controllare la finestra di dialogo **Avvio dell'emulatore Android** durante l'avvio dell'emulatore. Se HAXM è installato, verrà visualizzato il messaggio **HAX is working and emulator runs in fast virt mode** (HAX è in funzione e l'emulatore viene eseguito in modalità di virtualizzazione rapida), come illustrato in questo screenshot:

    ![HAXM è indicato come disponibile nella finestra di dialogo Avvio dell'emulatore Android](hardware-acceleration-images/win/03-haxm-detected.png)

   Se non viene visualizzato questo messaggio, HAXM probabilmente non è installato. Ecco ad esempio uno screenshot di un messaggio che potrebbe essere visualizzato se HAXM non è disponibile:

    ![HAXM non è disponibile su questo computer](hardware-acceleration-images/win/04-haxm-error.png)

   Se HAXM non è disponibile nel computer, usare la procedura descritta nella sezione successiva per installare HAXM.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Avviare Gestione emulatori Android facendo clic su **Strumenti > Gestione emulatori Google**:

    [![Posizione della voce di menu Gestione emulatori Android](hardware-acceleration-images/mac/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/mac/01-avd-manager-menu-item.png#lightbox)

2. Se viene visualizzata una finestra di dialogo **Avviso prestazioni** simile alla seguente, HAXM non è ancora installato o configurato correttamente nel computer:

    ![Finestra di dialogo Avviso prestazioni che indica che HAXM non è pronto](hardware-acceleration-images/mac/04-avd-warning.png)

   Se viene visualizzata una finestra di dialogo **Avviso prestazioni** simile alla seguente, vedere [Avvisi di prestazioni](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#perfwarn) per identificare la causa e risolvere il problema.

3. Selezionare l'immagine **x86** (ad esempio, **Android\_Accelerated\_x86**), fare clic su **Avvia**, quindi di nuovo su **Avvia**:

    [![Avvio dell'emulatore di Android SDK con un'immagine di dispositivo virtuale predefinita](hardware-acceleration-images/mac/02-start-default-avd-sml.png)](hardware-acceleration-images/mac/02-start-default-avd.png#lightbox)

3. Controllare la finestra di dialogo **Avvio dell'emulatore Android** durante l'avvio dell'emulatore. Se HAXM è installato, verrà visualizzato il messaggio **HAX is working and emulator runs in fast virt mode** (HAX è in funzione e l'emulatore viene eseguito in modalità di virtualizzazione rapida), come illustrato in questo screenshot:

    ![HAXM è indicato come disponibile nella finestra di dialogo Avvio dell'emulatore Android](hardware-acceleration-images/mac/03-haxm-detected.png)

   Se HAXM non è disponibile nel computer, ad esempio se viene visualizzato un messaggio di errore simile a _Please ensure Intel HAXM is properly installed and usable_ (Verificare che Intel HAXM sia installato correttamente e utilizzabile), usare la procedura descritta nella sezione successiva per installare HAXM.


-----

<a name="install-haxm" />

## <a name="installing-haxm"></a>Installazione di HAXM

Se l'emulatore non viene avviato, potrebbe essere necessario installare HAXM manualmente. I pacchetti di installazione di HAXM per Windows e macOS sono disponibili nella pagina [Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager). Usare la procedura seguente per scaricare e installare manualmente HAXM:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Scaricare dal sito Web di Intel la versione più recente del programma di installazione del [motore di virtualizzazione HAXM](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) per Windows. Scaricando il programma di installazione di HAXM direttamente dal sito Web di Intel, si può essere certi di usare la versione più recente.

   In alternativa, è possibile usare SDK Manager per scaricare il programma di installazione di HAXM: in SDK Manager fare clic su **Strumenti > Funzionalità aggiuntive > Intel x86 Emulator Accelerator (HAXM installer)** (Acceleratore emulatore Intel x86 (programma di installazione di HAXM)). Android SDK in genere scarica il programma di installazione di HAXM nel percorso seguente:

   **C:\\Program Files (x86)\\Android\\android-sdk\\extras\\intel\\Hardware\_Accelerated\_Execution\_Manager**

   Si noti che SDK Manager non installa HAXM, ma scarica semplicemente il programma di installazione HAXM nel percorso precedente. È comunque necessario avviarlo manualmente.

2. Eseguire **intelhaxm-android.exe** per avviare il programma di installazione di HAXM. Accettare i valori predefiniti nelle finestre di dialogo del programma di installazione:

   ![Finestra di installazione di Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/win/05-haxm-installer.png)

Se viene visualizzata la finestra di dialogo di errore _This computer does not support Intel Virtualization Technology (VT-x) or it is being exclusively used by Hyper-V_ (Questo computer non supporta Intel Virtualization Technology (VT-x) o è usato in modo esclusivo da Hyper-V), Hyper-V deve essere disabilitato prima di poter installare HAXM:

![HAXM non può essere installato a causa di un conflitto con Hyper-V](hardware-acceleration-images/win/06-cant-install-haxm.png)

Nella sezione successiva viene spiegato come disabilitare Hyper-V.

<a name="disable-hyperv" />

## <a name="disabling-hyper-v"></a>Disabilitazione di Hyper-V

Se si usa Windows con Hyper-V abilitato, è necessario disabilitarlo e riavviare il computer per installare e usare HAXM. È possibile disabilitare Hyper-V dal Pannello di controllo attenendosi alla procedura seguente:

1. Nella casella di ricerca di Windows immettere **Programmi** e quindi fare clic su **Programmi e funzionalità** nei risultati della ricerca.

2. Nella finestra di dialogo **Programmi e funzionalità** del Pannello di controllo fare clic su **Attiva o disattiva funzionalità di Windows**:

    ![Attiva o disattiva le funzionalità Windows](hardware-acceleration-images/win/07-turn-windows-features.png)

3. Deselezionare **Hyper-V** e riavviare il computer:

    ![Disabilitazione di Hyper-V nella finestra di dialogo Funzionalità Windows](hardware-acceleration-images/win/08-uncheck-hyper-v.png)

In alternativa, è possibile usare il cmdlet PowerShell seguente per disabilitare Hyper-V:

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

Intel HAXM e Microsoft Hyper-V non possono essere attivi contemporaneamente. Purtroppo, attualmente non è possibile passare da Hyper-V a HAXM e viceversa senza riavviare il computer. Se si vuole usare [Visual Studio Emulator for Android](~/android/deploy-test/debugging/visual-studio-android-emulator.md) (che dipende da Hyper-V), non sarà possibile usare l'emulatore di Android SDK senza riavviare il computer. È possibile usare sia Hyper-V che HAXM per creare una configurazione ad avvio multiplo, come descritto in [Creating a no hypervisor boot entry](https://blogs.msdn.microsoft.com/virtual_pc_guy/2008/04/14/creating-a-no-hypervisor-boot-entry/) (Creazione di una voce di avvio no hypervisor).

In alcuni casi, non è possibile disabilitare Hyper-V tramite la procedura precedente se sono abilitati Device Guard e Credential Guard. Se non è possibile disabilitare Hyper-V (o se risulta disabilitato ma l'installazione di HAXM ha comunque esito negativo), usare la procedura descritta nella sezione successiva per disabilitare Device Guard e Credential Guard.

<a name="disable-devguard" />

## <a name="disabling-device-guard"></a>Disabilitazione di Device Guard

Device Guard e Credential Guard possono impedire la disabilitazione di Hyper-V nei computer Windows. Questo rappresenta spesso un problema per i computer aggiunti a un dominio che sono configurati e controllati da un'organizzazione.
In Windows 10, attenersi alla seguente procedura per verificare se **Device Guard** è in esecuzione:

1. Nella **casella di ricerca di Windows** digitare **System info** per avviare l'app **System Information**.

2. In **Risorse di sistema** verificare che **Device Guard: sicurezza basata sulla virtualizzazione** sia presente e nello stato **In esecuzione**:

   [![Device Guard è presente e in esecuzione](hardware-acceleration-images/win/09-device-guard-sml.png)](hardware-acceleration-images/win/09-device-guard.png#lightbox)

Se Device Guard è abilitato, usare la procedura seguente per disabilitarlo:

1. Verificare che **Hyper-V** sia disabilitato (in **Attiva o disattiva funzionalità di Windows**), come descritto nella sezione precedente.

2. Nella casella di ricerca di Windows digitare **gpedit** e selezionare il risultato della ricerca **Modifica Criteri di gruppo**. Verrà avviato l'**Editor Criteri di gruppo locali**.

3. Nell'**Editor Criteri di gruppo locali** passare a **Configurazione computer > Modelli amministrativi > Sistema > Device Guard**:

   [![Device Guard nell'Editor Criteri di gruppo locali](hardware-acceleration-images/win/10-group-policy-editor-sml.png)](hardware-acceleration-images/win/10-group-policy-editor.png#lightbox)

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

1. Scaricare dal sito Web di Intel la versione più recente del programma di installazione del [motore di virtualizzazione HAXM](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) per macOS.

2. Eseguire il programma di installazione di HAXM. Accettare i valori predefiniti nelle finestre di dialogo del programma di installazione:

   [![Finestra di installazione di Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/mac/05-haxm-installer-sml.png)](hardware-acceleration-images/win/05-haxm-installer.png#lightbox)

-----
