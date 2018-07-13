---
title: Accelerazione hardware per le prestazioni dell'emulatore
description: Questo articolo descrive come usare le funzionalità di accelerazione hardware del computer per ottimizzare le prestazioni dell'emulatore Android.
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: c2bef2c614d4cc0655deb9732ccefec223a8318a
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38848467"
---
# <a name="hardware-acceleration-for-emulator-performance"></a>Accelerazione hardware per le prestazioni dell'emulatore

_Questo articolo descrive come usare le funzionalità di accelerazione hardware del computer per ottimizzare le prestazioni dell'emulatore Android._

## <a name="overview"></a>Panoramica

Per gli sviluppatori, Visual Studio semplifica il test e il debug del codice delle applicazioni Xamarin.Android con l'emulatore Android nelle situazioni in cui un dispositivo Android non è disponibile o risulta poco pratico.
Tuttavia, l'emulatore Android viene eseguito troppo lentamente se nel computer che lo esegue non è disponibile l'accelerazione hardware. È possibile migliorare notevolmente le prestazioni dell'emulatore Android usando immagini del dispositivo virtuale speciali destinate all'hardware x86 insieme a una delle due tecnologie di virtualizzazione:

1. **Microsoft Hyper-V e piattaforma Hypervisor**. Hyper-V è una funzionalità di virtualizzazione di Windows che consente l'esecuzione di sistemi di computer virtualizzati in un computer host fisico. Questa è la tecnologia di virtualizzazione consigliata da usare per l'accelerazione dell'emulatore Android. Per altre informazioni su Hyper-V, vedere [Hyper-V in Windows 10](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/).

2. **Intel Hardware Accelerated Execution Manager (HAXM)**. 
   HAXM è un motore di virtualizzazione per i computer che eseguono CPU Intel.
   Questo è il motore di virtualizzazione consigliato per i computer che non possono eseguire Hyper-V.

L'emulatore Android usa automaticamente l'accelerazione hardware se vengono soddisfatti i criteri seguenti:

-   L'accelerazione hardware è disponibile e abilitata nel computer di sviluppo.

-   L'emulatore esegue un'immagine dell'emulatore creata appositamente per un dispositivo virtuale basato su **x86**.

Per informazioni sull'avvio e il debug con l'emulatore Android, vedere [Debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md).

## <a name="hyper-v"></a>Hyper-V

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](~/media/shared/preview.png)

> [!NOTE]
> Il supporto di Hyper-V è attualmente in anteprima.

L'uso di Microsoft Hyper-V è vivamente consigliato agli sviluppatori che usano l'Aggiornamento di Windows 10 (aprile 2018 o versione successiva) per accelerare l'emulatore Android. Per usare l'emulatore Android con Hyper-V:

1. **Eseguire l'aggiornamento all'Aggiornamento di Windows 10 - aprile 2018 (build 1803) o versione successiva**.
   Per verificare la versione di Windows in esecuzione, fare clic sulla barra di ricerca di Cortana e digitare **Informazioni su**. Nei risultati della ricerca selezionare **Informazioni sul PC**. Scorrere verso il basso la finestra **Informazioni su** fino a visualizzare la sezione **Specifiche Windows**. Il valore di **Versione** non deve essere inferiore a 1803:

    [![Specifiche Windows](hardware-acceleration-images/win/12-about-windows.w10-sml.png)](hardware-acceleration-images/win/12-about-windows.w10.png#lightbox)

2. **Abilitare la piattaforma Windows Hypervisor** .
   Nella barra di ricerca di Cortana digitare **Attiva o disattiva funzionalità di Windows**.
   Scorrere verso il basso la finestra **Funzionalità Windows** e assicurarsi che sia abilitata l'opzione **Windows Hypervisor Platform** (Piattaforma Windows Hypervisor):

    [![Piattaforma Windows Hypervisor abilitata](hardware-acceleration-images/win/13-windows-features.w10-sml.png)](hardware-acceleration-images/win/13-windows-features.w10.png#lightbox)

   L'abilitazione della **piattaforma Windows Hypervisor** abilita automaticamente Hyper-V. È consigliabile riavviare Windows dopo avere apportato questa modifica.

3. **Installare [Visual Studio 15.8 Preview 1 o versione successiva](https://visualstudio.microsoft.com/vs/preview/)**.
   Questa versione di Visual Studio offre il supporto IDE per l'esecuzione dell'emulatore Android con Hyper-V.
 
4. **Installare il pacchetto dell'emulatore Android 27.2.7 o versione successiva**. Per installare questo pacchetto, passare a **Strumenti > Android > Android SDK Manager** in Visual Studio. Selezionare la scheda **Strumenti** e verificare che la versione dell'emulatore Android non sia inferiore alla 27.2.7. Verificare anche che la versione di Android SDK Tools corrisponda alla 26.1.1 o versione successiva:

    [![Finestra di dialogo Android SDK e strumenti Android](hardware-acceleration-images/win/14-sdk-manager.w158-sml.png)](hardware-acceleration-images/win/14-sdk-manager.w158.png#lightbox)

5. Se la versione dell'emulatore è almeno 27.2.7 ma inferiore a 27.3.1, per usare Hyper- V è necessaria la soluzione alternativa seguente:

    1.  Nella cartella **C:\\Utenti\\_nomeutente_\\.android** creare il file **advancedFeatures.ini**, se non esiste già.

    2.  Aggiungere la seguente riga ad **advancedFeatures.ini**:
        ```
        WindowsHypervisorPlatform = on
        ```


### <a name="known-issues"></a>Problemi noti

-   Se non si è in grado di eseguire l'aggiornamento alla versione dell'emulatore 27.2.7 o successiva dopo l'aggiornamento a Visual Studio Preview, potrebbe essere necessario installare direttamente il [programma di installazione della versione Preview](http://aka.ms/hyperv-emulator-dl) per abilitare le versioni più recenti dell'emulatore.

-   Le prestazioni possono risultare ridotte quando si usano determinati processori Intel e AMD.

-   Il caricamento dell'applicazione Android può richiedere un periodo di tempo molto lungo al momento della distribuzione.

-   Un errore di accesso MMIO può impedire in modo intermittente un avvio dell'emulatore Android. Riavviare l'emulatore per risolvere il problema.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per il supporto di Hyper-V è necessario Windows 10. Per altri dettagli, vedere [Requisiti di Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v#check-requirements).

-----

## <a name="haxm"></a>HAXM

HAXM è un motore di virtualizzazione assistita mediante hardware (hypervisor) che usa Intel Virtualization Technology (VT) per velocizzare l'emulazione delle app Android in un computer host. L'uso di HAXM in combinazione con le immagini x86 dell'emulatore Android fornite da Intel consente di velocizzare l'emulazione di Android nei sistemi abilitati per VT.

Se si sviluppa in un computer con una CPU Intel che dispone di funzionalità VT, è possibile usare HAXM per velocizzare notevolmente l'emulatore Android (se non si è certi che la CPU supporti VT, vedere [Il mio processore supporta Intel Virtualization Technology?](https://www.intel.com/content/www/us/en/support/processors/000005486.html)).

> [!NOTE]
> Non è possibile eseguire un emulatore con accelerazione della macchina virtuale all'interno di un'altra macchina virtuale, ad esempio una macchina ospitata da VirtualBox, VMWare o Docker. È necessario eseguire l'emulatore Android [direttamente nell'hardware del sistema](https://developer.android.com/studio/run/emulator-acceleration.html#extensions).

Prima di usare l'emulatore Android per la prima volta, è consigliabile verificare che HAXM sia installato e disponibile per l'uso con l'emulatore Android.

### <a name="verifying-haxm-installation"></a>Verifica dell'installazione di HAXM

È possibile verificare se HAXM è disponibile visualizzando la finestra **Avvio dell'emulatore Android** durante l'avvio dell'emulatore. Per avviare l'emulatore Android, seguire questa procedura:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Avviare Android Device Manager facendo clic su **Strumenti > Android > Android Device Manager**:

    [![Voce di menu Android Device Manager](hardware-acceleration-images/win/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/win/01-avd-manager-menu-item.png#lightbox)

2. Se viene visualizzata una finestra di dialogo **Avviso prestazioni** simile alla seguente, HAXM non è ancora installato o configurato correttamente nel computer:

    ![Finestra di dialogo Avviso prestazioni che indica che HAXM non è pronto](hardware-acceleration-images/win/11-perf-warn.png)

   Se viene visualizzata una finestra di dialogo **Avviso prestazioni** simile alla seguente, vedere [Avvisi di prestazioni](~/android/get-started/installation/android-emulator/troubleshooting.md#perfwarn) per identificare la causa e risolvere il problema.

3. Selezionare un'immagine **x86** (ad esempio, **VisualStudio\_android-23\_x86\_phone**) e fare clic su **Avvia**:

    ![Avvio dell'emulatore Android con un'immagine di dispositivo virtuale predefinita](hardware-acceleration-images/win/02-start-default-avd.png)

4. Controllare la finestra di dialogo **Avvio dell'emulatore Android** durante l'avvio dell'emulatore. Se HAXM è installato, verrà visualizzato il messaggio **HAX is working and emulator runs in fast virt mode** (HAX è in funzione e l'emulatore viene eseguito in modalità di virtualizzazione rapida), come illustrato in questo screenshot:

    ![HAXM è indicato come disponibile nella finestra di dialogo Avvio dell'emulatore Android](hardware-acceleration-images/win/03-haxm-detected.png)

   Se non viene visualizzato questo messaggio, HAXM probabilmente non è installato. Ecco ad esempio uno screenshot di un messaggio che potrebbe essere visualizzato se HAXM non è disponibile:

    ![HAXM non è disponibile su questo computer](hardware-acceleration-images/win/04-haxm-error.png)

   Se HAXM non è disponibile nel computer, usare la procedura descritta nella sezione successiva per installare HAXM.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Avviare Android Device Manager facendo clic su **Strumenti > Device Manager**:

    [![Voce di menu Android Device Manager](hardware-acceleration-images/mac/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/mac/01-avd-manager-menu-item.png#lightbox)

2. Se viene visualizzata una finestra di dialogo **Avviso prestazioni** simile alla seguente, HAXM non è ancora installato o configurato correttamente nel computer:

    ![Finestra di dialogo Avviso prestazioni che indica che HAXM non è pronto](hardware-acceleration-images/mac/04-avd-warning.png)

   Se viene visualizzata una finestra di dialogo **Avviso prestazioni** simile alla seguente, vedere [Avvisi di prestazioni](~/android/get-started/installation/android-emulator/troubleshooting.md#perfwarn) per identificare la causa e risolvere il problema.

3. Selezionare l'immagine **x86** (ad esempio, **Android\_Accelerated\_x86**) e fare clic su **Avvia**:

    [![Avvio dell'emulatore Android con un'immagine di dispositivo virtuale predefinita](hardware-acceleration-images/mac/02-start-default-avd-sml.png)](hardware-acceleration-images/mac/02-start-default-avd.png#lightbox)

3. Controllare la finestra di dialogo **Avvio dell'emulatore Android** durante l'avvio dell'emulatore. Se HAXM è installato, verrà visualizzato il messaggio **HAX is working and emulator runs in fast virt mode** (HAX è in funzione e l'emulatore viene eseguito in modalità di virtualizzazione rapida), come illustrato in questo screenshot:

    ![HAXM è indicato come disponibile nella finestra di dialogo Avvio dell'emulatore Android](hardware-acceleration-images/mac/03-haxm-detected.png)

   Se HAXM non è disponibile nel computer, ad esempio se viene visualizzato un messaggio di errore simile a _Please ensure Intel HAXM is properly installed and usable_ (Verificare che Intel HAXM sia installato correttamente e utilizzabile), usare la procedura descritta nella sezione successiva per installare HAXM.

-----

<a name="install-haxm" />

### <a name="installing-haxm"></a>Installazione di HAXM

Se l'emulatore non viene avviato, potrebbe essere necessario installare HAXM manualmente. I pacchetti di installazione di HAXM per Windows e macOS sono disponibili nella pagina [Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager). Usare la procedura seguente per scaricare e installare manualmente HAXM:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Scaricare dal sito Web di Intel la versione più recente del programma di installazione del [motore di virtualizzazione HAXM](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) per Windows. Scaricando il programma di installazione di HAXM direttamente dal sito Web di Intel, si può essere certi di usare la versione più recente.

   In alternativa, è possibile usare SDK Manager per scaricare il programma di installazione di HAXM: in SDK Manager fare clic su **Strumenti > Funzionalità aggiuntive > Intel x86 Emulator Accelerator (HAXM installer)** (Acceleratore emulatore Intel x86 (programma di installazione di HAXM)). Android SDK in genere scarica il programma di installazione di HAXM nel percorso seguente:

   **C:\\Program Files (x86)\\Android\\android-sdk\\extras\\intel\\Hardware\_Accelerated\_Execution\_Manager**

   Si noti che SDK Manager non installa HAXM, ma scarica semplicemente il programma di installazione HAXM nel percorso precedente. È comunque necessario avviarlo manualmente.

2. Eseguire **intelhaxm-android.exe** per avviare il programma di installazione di HAXM. Accettare i valori predefiniti nelle finestre di dialogo del programma di installazione:

   ![Finestra di installazione di Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/win/05-haxm-installer.png)

## <a name="hardware-acceleration-and-amd-cpus"></a>Accelerazione hardware e CPU AMD

Poiché l'emulatore Android attualmente supporta l'accelerazione hardware AMD [solo in Linux](https://developer.android.com/studio/run/emulator-acceleration.html#dependencies), l'accelerazione hardware non è disponibile per i computer basati su processori AMD che eseguono Windows.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Scaricare dal sito Web di Intel la versione più recente del programma di installazione del [motore di virtualizzazione HAXM](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) per macOS.

2. Eseguire il programma di installazione di HAXM. Accettare i valori predefiniti nelle finestre di dialogo del programma di installazione:

   [![Finestra di installazione di Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/mac/05-haxm-installer-sml.png)](hardware-acceleration-images/win/05-haxm-installer.png#lightbox)

-----


## <a name="related-links"></a>Collegamenti correlati

* [Run Apps on the Android Emulator](https://developer.android.com/studio/run/emulator) (Eseguire app nell'emulatore Android)
