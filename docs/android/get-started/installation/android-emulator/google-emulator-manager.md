---
title: Gestione emulatori Google
description: Come creare e gestire dispositivi virtuali Android mediante Gestione emulatori Google
ms.topic: article
ms.prod: xamarin
ms.assetid: 0C0BBEC0-C84A-4558-B905-4EF81FCD62F9
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 1e90ebe55dbec9fdcc57b9eaab99e614fdc5cbbc
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="google-emulator-manager"></a>Gestione emulatori Google

Dopo la verifica dell'abilitazione dell'accelerazione hardware (procedura descritta in [Accelerazione hardware dell'emulatore Android](~/android/get-started/installation/android-emulator/hardware-acceleration.md)), il passaggio successivo è la creazione di dispositivi virtuali per il test e il debug dell'app. È possibile usare la funzionalità legacy Gestione emulatori Google (anche denominato *Android Virtual Device (AVD) Manager*) per creare i dispositivi virtuali per l'uso da parte dell'emulatore di Android SDK.

> [!NOTE]
> Se la destinazione è Android Oreo 8.0, è necessario usare [Gestione dispositivi di Xamarin Android](~/android/get-started/installation/android-emulator/xamarin-device-manager.md) per creare e configurare i dispositivi virtuali.


## <a name="installing-system-images"></a>Installazione delle immagini di sistema

A seconda dei livelli API Android di destinazione, è necessario scaricare e installare le immagini di sistema specifiche per i livelli API usate dall'emulatore di Android SDK. Per ogni livello API Android, è disponibile una serie di immagini di sistema **x86** che è necessario scaricare e installare per la creazione di dispositivi virtuali.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per installare le immagini di sistema necessarie, avviare Android SDK Manager (**Strumenti > Android > Android SDK Manager**) e individuare i livelli API che si intende supportare. Per ogni livello API, abilitare il segno di spunta accanto alle immagini di sistema seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per installare le immagini di sistema necessarie, avviare Android SDK Manager (**Strumenti > SDK Manager**) e individuare i livelli API che si intende supportare. Per ogni livello API, abilitare il segno di spunta accanto alle immagini di sistema seguenti:

-----

-   **Intel x86 Atom System Image** (Immagine di sistema Intel x86 Atom)
-   **Google APIs Intel x86 Atom System Image** (Immagine di sistema Intel x86 Atom con API Google)

Quest'ultima immagine di sistema aggiunge le API Google (ad esempio, le API di Google Maps) al dispositivo virtuale. 

Nello screenshot seguente verranno installate le immagini **Intel x86 Atom**, in modo da consentire la creazione di dispositivi virtuali che eseguono Android 6.0:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Selezione delle immagini di sistema x86 Android 6.0 per l'emulatore Android](google-emulator-manager-images/win/03-select-x86-images-sml.png)](google-emulator-manager-images/win/03-select-x86-images.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Selezione delle immagini di sistema x86 Android 6.0 per l'emulatore Android](google-emulator-manager-images/mac/02-select-x86-images-sml.png)](google-emulator-manager-images/mac/02-select-x86-images.png#lightbox)

-----

Se si sviluppano app a 64 bit, installare le immagini di sistema seguenti:

-   **Intel x86 Atom_64 System Image** (Immagine di sistema Intel x86 Atom_64)
-   **Google APIs Intel x86 Atom_64 System Image** (Immagine di sistema Intel x86 Atom_64 con API Google)

È possibile usare queste immagini di sistema a 64 bit per eseguire app a 32 bit, tuttavia l'**immagine di sistema Intel x86 Atom** a 32 bit viene eseguita in modo leggermente più veloce nell'emulatore di Android SDK.

Se si sviluppano app per Android Wear, installare le immagini di sistema seguenti:

-   **Android Wear Intel x86 Atom System Image** (Immagine di sistema Intel x86 Atom per Android Wear)
-   **Google APIs Intel x86 Atom System Image** (Immagine di sistema Intel x86 Atom con API Google)

Dopo avere installato queste immagini di sistema, è possibile creare dispositivi virtuali Android basati su **x86** selezionando le impostazioni appropriate per il livello API e CPU/ABI durante la configurazione del dispositivo virtuale (questo passaggio è descritto di seguito).


## <a name="configuring-virtual-devices"></a>Configurazione dei dispositivi virtuali

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

I dispositivi virtuali vengono configurati mediante **Gestione emulatori Google** (anche denominato _Android Virtual Device Manager_ o _AVD Manager_). Per avviare Gestione emulatori Android da Visual Studio, fare clic sull'icona **Gestione emulatori Android** sulla barra degli strumenti:

[![Posizione dell'icona AVD](google-emulator-manager-images/win/04-avd-icon-sml.png)](google-emulator-manager-images/win/04-avd-icon.png#lightbox)

È anche possibile avviare Gestione emulatori Android dalla barra dei menu selezionando **Strumenti > Android > Gestione emulatori Android**:

[![Posizione della voce di menu Gestione emulatori Android](google-emulator-manager-images/win/05-avd-manager-menu-item-sml.png)](google-emulator-manager-images/win/05-avd-manager-menu-item.png#lightbox)

Nella finestra di dialogo **Android Virtual Device (AVD) Manager** viene visualizzato un elenco dei dispositivi virtuali Android esistenti:

[![Android Virtual Device Manager](google-emulator-manager-images/win/06-virtual-device-manager-sml.png)](google-emulator-manager-images/win/06-virtual-device-manager.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

I dispositivi virtuali vengono configurati mediante **Gestione emulatori Google** (anche denominato _Android Virtual Device Manager_ o _AVD Manager_). 

È possibile avviare Gestione emulatori Android dalla barra dei menu selezionando **Strumenti > Gestione emulatori Google**:

[![Posizione della voce di menu Gestione emulatori Android](google-emulator-manager-images/mac/03-avd-manager-menu-item-sml.png)](google-emulator-manager-images/mac/03-avd-manager-menu-item.png#lightbox)

Nella finestra di dialogo **Android Virtual Device (AVD) Manager** viene visualizzato un elenco dei dispositivi virtuali Android esistenti:

[![Android Virtual Device Manager](google-emulator-manager-images/mac/05-virtual-device-manager-sml.png)](google-emulator-manager-images/mac/05-virtual-device-manager.png#lightbox)

-----

È possibile creare nuove immagini di dispositivi virtuali con le caratteristiche e i livelli API di diversi dispositivi: nella sezione successiva viene spiegato come creare definizioni di dispositivo personalizzate e dispositivi virtuali.


### <a name="creating-a-custom-device-definition"></a>Creazione di una definizione di dispositivo personalizzata

Per creare una definizione di dispositivo personalizzata, fare clic su **Crea** in **Android Virtual Device (AVD) Manager**. Verrà visualizzata la finestra di dialogo **Create new Android Virtual Device (AVD)** (Crea nuovo dispositivo virtuale Android (AVD)):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Definizione di dispositivo personalizzata basata su 6 Nexus](google-emulator-manager-images/win/07-custom-device-sml.png)](google-emulator-manager-images/win/07-custom-device.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Definizione di dispositivo personalizzata basata su 6 Nexus](google-emulator-manager-images/mac/06-custom-device-sml.png)](google-emulator-manager-images/mac/06-custom-device.png#lightbox)

-----

In questa finestra di dialogo configurare le opzioni seguenti:

-   **AVD Name** (Nome AVD) &ndash; Nome univoco per la definizione di dispositivo. Nello screenshot di esempio precedente il nome è impostato su **MyNexus**. Si noti che il nome del dispositivo AVD non può contenere spazi: se si tenta di usare spazi nel nome del dispositivo AVD, il pulsante **OK** sarà disabilitato.

-   **Dispositivo** &ndash; Selezionare il profilo hardware da emulare (ad esempio, **Nexus 5** o **Nexus 6**).

-   **Destinazione** &ndash; Selezionare il livello API Android per il dispositivo virtuale. Questa impostazione deve essere maggiore o uguale alla versione minima di Android dell'app.

-   **CPU/ABI** &ndash; Selezionare **Google APIs Intel Atom (x86)** (Intel Atom (x86) con API Google) in modo da rendere disponibili le API Google nella definizione del dispositivo.

-   **Interfaccia** &ndash; Selezionare l'aspetto del dispositivo virtuale. Nello screenshot di esempio precedente è selezionata l'interfaccia **HVGA** (lo screenshot dell'emulatore alla fine di questo articolo è un esempio dell'interfaccia **HVGA**).

-   **Memory Options** (Opzioni per la memoria) &ndash; In genere, l'impostazione della RAM è troppo alta e in Windows viene generato questo avviso: **emulating RAM greater than 768M may fail** (l'emulazione di RAM superiore a 768 MB potrebbe non riuscire). Per la maggior parte degli utenti, è consigliabile impostare la RAM su 768 MB (come illustrato nello screenshot precedente). Valori elevati della RAM possono rallentare l'emulatore.

-   **Use Host GPU** (Usa GPU host) &ndash; Questa opzione indica all'emulatore di usare l'unità di elaborazione grafica (GPU) del computer host per eseguire le operazioni di grafica. È consigliabile abilitare questa opzione per aumentare ulteriormente le prestazioni dell'emulatore. Per altre informazioni sulla sezione **Emulation Options** (Opzioni di emulazione), vedere [What are Snapshot and Use Host GPU emulation options used for?](https://android.stackexchange.com/questions/51739/what-is-snapshot-and-use-host-gpu-emulation-options-for) (Qual è la funzione delle opzioni di emulazione Snapshot e Use Host GPU?)


Per le altre opzioni, è possibile mantenere le impostazioni predefinite. Quando si è pronti, fare clic su **OK** per creare il nuovo dispositivo virtuale. Nella finestra di dialogo successiva sono descritti in dettaglio i risultati della nuova configurazione del dispositivo virtuale:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Finestra di dialogo dei risultati dopo la creazione di un nuovo AVD](google-emulator-manager-images/win/08-create-results.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Finestra di dialogo dei risultati dopo la creazione di un nuovo AVD](google-emulator-manager-images/mac/07-create-results.png)

-----

Per una spiegazione dettagliata delle proprietà di configurazione elencate in questa finestra di dialogo, vedere [Hardware Profile Properties](https://developer.android.com/studio/run/managing-avds.html#hpproperties) (Proprietà del profilo hardware).
Dopo aver fatto clic su **OK**, la nuova configurazione del dispositivo è visualizzata nell'elenco dei dispositivi virtuali Android esistenti. Nello screenshot seguente, **MyNexus** è stato aggiunto all'elenco:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![MyNexus aggiunto all'elenco dei dispositivi](google-emulator-manager-images/win/09-added-to-list-sml.png)](google-emulator-manager-images/win/09-added-to-list.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![MyNexus aggiunto all'elenco dei dispositivi](google-emulator-manager-images/mac/08-added-to-list-sml.png)](google-emulator-manager-images/mac/08-added-to-list.png#lightbox)

-----

Il nuovo dispositivo virtuale personalizzato viene anche aggiunto al menu a discesa del dispositivo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![MyNexus aggiunto al menu a discesa del dispositivo](google-emulator-manager-images/win/10-available-custom-device-sml.png)](google-emulator-manager-images/win/10-available-custom-device.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![MyNexus aggiunto al menu a discesa del dispositivo](google-emulator-manager-images/mac/09-available-custom-device-sml.png)](google-emulator-manager-images/mac/09-available-custom-device.png#lightbox)

-----



### <a name="cloning-a-device-definition"></a>Clonazione di una definizione di dispositivo

È possibile selezionare una definizione di dispositivo esistente e *clonarla* per creare una nuova definizione di dispositivo personalizzata. Si tratta di una strategia efficace quando è presente una definizione di dispositivo esistente che necessita solo di alcune modifiche per soddisfare le esigenze. La scheda **Device Definitions** (Definizioni di dispositivo) in **Android Virtual Device (AVD) Manager** elenca tutte le definizioni di dispositivo disponibili:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Elenco delle definizioni di dispositivo disponibili](google-emulator-manager-images/win/11-device-definitions-sml.png)](google-emulator-manager-images/win/11-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Elenco delle definizioni di dispositivo disponibili](google-emulator-manager-images/mac/10-device-definitions-sml.png)](google-emulator-manager-images/mac/10-device-definitions.png#lightbox)

-----

I dispositivi preconfigurati in questo elenco non possono essere modificati: è possibile modificare solo i dispositivi virtuali creati dall'utente. È possibile derivare una nuova definizione di dispositivo da una definizione di dispositivo preconfigurata selezionando la definizione di dispositivo e facendo clic su **Clone** (Clona). Ad esempio, selezionando la definizione **Nexus 5** e facendo clic su **Clone** (Clona), viene visualizzata la finestra di dialogo seguente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Finestra di dialogo per la clonazione del dispositivo](google-emulator-manager-images/win/12-clone-device-sml.png)](google-emulator-manager-images/win/12-clone-device.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Finestra di dialogo per la clonazione del dispositivo](google-emulator-manager-images/mac/11-clone-device-sml.png)](google-emulator-manager-images/mac/11-clone-device.png#lightbox)

-----

Nello screenshot successivo, il nome viene modificato in **Nexus 5 Custom** e i parametri del dispositivo vengono modificati per creare una nuova definizione di dispositivo personalizzata:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![AVD Nexus 5 personalizzato](google-emulator-manager-images/win/13-custom-nexus.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![AVD Nexus 5 personalizzato](google-emulator-manager-images/mac/12-custom-nexus-sml.png)](google-emulator-manager-images/mac/12-custom-nexus.png#lightbox)

-----

Fare clic su **Clone Device** (Clona dispositivo) per creare la nuova definizione di dispositivo, che viene visualizzata nell'elenco **Device Definitions** (Definizioni di dispositivo):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Nexus 5 Custom viene visualizzato come nuova definizione di dispositivo dell'utente](google-emulator-manager-images/win/14-new-definition-sml.png)](google-emulator-manager-images/win/14-new-definition.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Nexus 5 Custom viene visualizzato come nuova definizione di dispositivo dell'utente](google-emulator-manager-images/mac/13-new-definition-sml.png)](google-emulator-manager-images/mac/13-new-definition.png#lightbox)

-----

Si noti che ogni definizione di dispositivo creata dall'utente viene visualizzata con un'icona verde, come illustrato in precedenza. Questa nuova definizione di dispositivo può essere usata per creare un nuovo AVD selezionando la definizione e facendo clic su **Crea AVD**. Verrà visualizzata la finestra di dialogo **Create new Android Virtual Device (AVD)** (Crea nuovo dispositivo virtuale Android (AVD)). Nell'esempio seguente è stato generato automaticamente il nome **AVD\_for\_Nexus\_5\_Custom** per il nuovo dispositivo virtuale:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Creazione di un AVD dalla definizione di dispositivo dell'utente Nexus 5 Custom](google-emulator-manager-images/win/15-create-avd-sml.png)](google-emulator-manager-images/win/15-create-avd.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Creazione di un AVD dalla definizione di dispositivo dell'utente Nexus 5 Custom](google-emulator-manager-images/mac/14-create-avd-sml.png)](google-emulator-manager-images/mac/14-create-avd.png#lightbox)

-----

Dopo aver fatto clic su **OK**, la configurazione del dispositivo personalizzata è visualizzata nell'elenco dei dispositivi virtuali Android esistenti. Inoltre, viene aggiunta al menu a discesa del dispositivo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Nuovo AVD personalizzato aggiunto al menu a discesa del dispositivo](google-emulator-manager-images/win/16-new-avd-sml.png)](google-emulator-manager-images/win/16-new-avd.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Nuovo AVD personalizzato aggiunto al menu a discesa del dispositivo](google-emulator-manager-images/mac/15-new-avd-sml.png)](google-emulator-manager-images/mac/15-new-avd.png#lightbox)

-----

