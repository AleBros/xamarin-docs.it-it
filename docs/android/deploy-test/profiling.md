---
title: Profilatura delle app Android
description: Questa guida spiega come usare gli strumenti del profiler per esaminare le prestazioni e l'utilizzo della memoria di un'app Android.
ms.topic: article
ms.prod: xamarin
ms.assetid: 8C823FEE-A6F6-4C31-9EB6-E51407A2FD8E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/03/2018
ms.openlocfilehash: fd9ebc7922428d2779e6985379c3118274a46aff
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066663"
---
# <a name="profiling-android-apps"></a>Profilatura delle app Android

Prima di distribuire l'app in un app store, è importante identificare e risolvere eventuali colli di bottiglia delle prestazioni, problemi di utilizzo eccessivo della memoria o uso inefficiente delle risorse di rete. A questo scopo sono disponibili due strumenti del profiler:

-  Xamarin Profiler 
-  Android Profiler in Android Studio

Questa guida presenta Xamarin Profiler e fornisce informazioni dettagliate per iniziare a usare Android Profiler.

 
## <a name="xamarin-profiler"></a>Xamarin Profiler

Xamarin Profiler è un'applicazione autonoma integrata con Visual Studio e Visual Studio per Mac per la profilatura di app Xamarin dall'interno dell'ambiente di sviluppo integrato (IDE). Per altre informazioni sull'uso di Xamarin Profiler, vedere [Xamarin Profiler](~/tools/profiler/index.md).

> [!NOTE]
> È necessario disporre di una sottoscrizione per [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/) per sbloccare la funzionalità Xamarin Profiler in Visual Studio Enterprise in Windows o in Visual Studio per Mac.
 
## <a name="android-studio-profiler"></a>Android Studio Profiler

Android Studio 3.0 e versione successiva include lo strumento Android Profiler. È possibile usare Android Profiler per misurare le prestazioni di un'app Xamarin Android compilata con Visual Studio, senza dover disporre di una licenza di Visual Studio Enterprise. Tuttavia, a differenza di Xamarin Profiler, Android Profiler non è integrato con Visual Studio e può essere usato solo per profilare un pacchetto dell'applicazione Android (APK) compilato in precedenza e importato in Android Profiler.

### <a name="launching-a-xamarin-android-app-in-android-profiler"></a>Avvio di un'app Xamarin Android in Android Profiler

I passaggi seguenti illustrano come avviare un'applicazione Xamarin Android nello strumento Android Profiler di Android Studio. Negli screenshot di esempio riportati di seguito l'app [XamagonXuzzle](https://developer.xamarin.com/samples/mobile/LivePlayer/XamagonXuzzleLP/) di Xamarin Forms viene compilata e profilata con Android Profiler:

1.  Nelle opzioni di compilazione del progetto Android disabilitare **Use Shared Runtime** (Usa runtime condiviso). In questo modo si garantisce che il pacchetto dell'applicazione Android (APK) venga compilato senza una dipendenza dal runtime di Mono in fase di sviluppo condiviso.

    ![Disabilitazione di Use Shared Runtime (Usa runtime condiviso)](profiling-images/vswin/01-turn-off-shared-runtime.png)

2.  Compilare l'app per **Debug** e distribuirla in un dispositivo fisico o in un emulatore. In questo modo, viene compilata una versione **Debug** firmata del pacchetto APK.
    Per l'esempio **XamagonXuzzle**, il pacchetto APK risultante è denominato **com.companyname.XamagonXuzzle-Signed.apk**.

3.  Aprire la cartella di progetto e passare a **bin/Debug**. In questa cartella individuare la versione **Signed.apk** dell'app e copiarla in una posizione facilmente accessibile (come il desktop). Nello screenshot seguente il file **com.companyname.XamagonXuzzle-Signed.apk** del pacchetto APK viene individuato e copiato sul desktop:

    [![Percorso del file del pacchetto APK firmato per il debug](profiling-images/vswin/02-locating-the-debug-apk-sml.png)](profiling-images/vswin/02-locating-the-debug-apk.png#lightbox)

4.  Avviare Android Studio e selezionare **Profile or debug APK** (Profila o esegui il debug del pacchetto APK):

    ![Avvio del profiler dalla schermata di avvio di Android Studio](profiling-images/vswin/03-android-studio.png)

5.  Nella finestra di dialogo **Select APK File** (Seleziona file APK) passare al pacchetto APK compilato e copiato in precedenza. Selezionare il pacchetto APK e fare clic su **OK**: 
    
    ![Selezione del pacchetto APK nella finestra di dialogo Select APK File (Seleziona file APK)](profiling-images/vswin/04-select-apk-dialog.png)

6.  Android Studio caricherà il pacchetto APK e disassemblerà **classes.dex**:

    ![Configurazione del pacchetto APK](profiling-images/vswin/05-setting-up-the-apk.png)

7.  Al termine del caricamento del pacchetto APK, Android Studio visualizza la schermata di progetto seguente per il pacchetto APK. Fare clic con il pulsante destro del mouse sul nome dell'app nella visualizzazione struttura ad albero a sinistra e selezionare **Open Module Settings** (Apri impostazioni modulo):

    [![Voce di menu Open Module Settings (Apri impostazioni modulo)](profiling-images/vswin/06-open-module-settings-sml.png)](profiling-images/vswin/06-open-module-settings.png#lightbox)

8.  Passare a **Project Settings > Modules** (Impostazioni progetto > Moduli), selezionare il nodo **-Signed** dell'app e quindi fare clic su **&lt;No SDK&gt;** (Nessun SDK):

    [![Spostamento alla configurazione dell'SDK](profiling-images/vswin/07-project-settings-modules-sml.png)](profiling-images/vswin/07-project-settings-modules.png#lightbox)

9.  Nel menu a discesa **Module SDK** (SDK modulo) selezionare il livello di Android SDK usato per compilare l'app (in questo esempio è stato usato il livello API 26 per compilare **XamagonXuzzle**):

    [![Impostazione del livello SDK di progetto](profiling-images/vswin/08-project-sdk-level-sml.png)](profiling-images/vswin/08-project-sdk-level.png#lightbox)

    Fare clic su **Apply** (Applica) e su **OK** per salvare questa impostazione.

10. Avviare il profiler dall'icona della barra degli strumenti:

    [![Posizione dell'icona della barra degli strumenti del profiler](profiling-images/vswin/09-launch-profiler-sml.png)](profiling-images/vswin/09-launch-profiler.png#lightbox)

11. Selezionare la destinazione di distribuzione per l'esecuzione/profilatura dell'app e fare clic su **OK**. La destinazione di distribuzione può essere un dispositivo fisico o un dispositivo virtuale in esecuzione in un emulatore. In questo esempio viene usato un dispositivo Nexus 5X:

    ![Selezione della destinazione di distribuzione](profiling-images/vswin/10-select-deployment-target.png)

12. Dopo l'avvio del profiler, saranno necessari alcuni secondi per la connessione al dispositivo di distribuzione e al processo dell'app. Durante l'installazione del pacchetto APK, Android Profiler segnalerà **No connected devices** (Nessun dispositivo connesso) e **No debuggable processes** (Nessun processo sottoponibile a debug).

    [![Il profiler installa il pacchetto APK](profiling-images/vswin/11-no-connected-devices-sml.png)](profiling-images/vswin/11-no-connected-devices.png#lightbox)

13. Dopo alcuni secondi, Android Profiler completerà l'installazione del pacchetto APK e lo avvierà, segnalando il nome del dispositivo e il nome del processo dell'app sottoposto a profilatura (in questo esempio, rispettivamente **LGE Nexus 5X** e  **com.companyname.XamagonXuzzle**):

    [![Finestra del profiler dopo l'avvio](profiling-images/vswin/12-profiler-starts-sml.png)](profiling-images/vswin/12-profiler-starts.png#lightbox)

14. Dopo avere identificato il dispositivo e il processo sottoponibile a debug, Android Profiler avvia la profilatura dell'app:

    [![Visualizzazione del profiler per l'app in esecuzione](profiling-images/vswin/13-profiler-running-sml.png)](profiling-images/vswin/13-profiler-running.png#lightbox)

15. Se si tocca il pulsante per la **scelta in modo casuale** in **XamagonXuzzle** (che determina la scelta casuale dei riquadri), verrà visualizzato l'aumento dell'utilizzo della CPU durante l'intervallo di scelta casuale dell'app:

    [![Utilizzo della CPU quando si tocca il pulsante per la scelta in modo casuale](profiling-images/vswin/14-tap-randomize-sml.png)](profiling-images/vswin/14-tap-randomize.png#lightbox)


### <a name="using-the-android-profiler"></a>Uso di Android Profiler

Informazioni dettagliate per l'uso di Android Profiler sono incluse nella [documentazione di Android Studio](https://developer.android.com/studio/profile/android-profiler.html).
Gli argomenti seguenti sono di particolare interesse per gli sviluppatori Xamarin Android:

-   [CPU Profiler](https://developer.android.com/studio/profile/cpu-profiler.html) &ndash; Illustra come controllare l'utilizzo della CPU e l'attività di thread dell'app in tempo reale.

-   [Memory Profiler](https://developer.android.com/studio/profile/memory-profiler.html) &ndash; Visualizza un grafico in tempo reale dell'utilizzo della memoria dell'app e include un pulsante per registrare le allocazioni di memoria per l'analisi.

-   [Network Profiler](https://developer.android.com/studio/profile/network-profiler.html) &ndash; Visualizza le attività di rete in tempo reale dei dati inviati e ricevuti dall'app.
