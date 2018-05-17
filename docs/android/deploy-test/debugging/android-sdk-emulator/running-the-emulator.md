---
title: Esecuzione dell'emulatore Android di Google
description: Come eseguire il debug dell'app con l'emulatore Android di Google
ms.prod: xamarin
ms.assetid: AEA165A4-D81A-411B-91DF-2DED2EED27B5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 0e290b24c0d7a98b1abaf647fe76e56867042645
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="running-the-google-android-emulator"></a>Esecuzione dell'emulatore Android di Google

Questa guida contiene informazioni su come avviare un dispositivo virtuale nell'emulatore Android di Google per attività di debug e test dell'app.

## <a name="using-a-pre-configured-virtual-device"></a>Uso di un dispositivo virtuale preconfigurato

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Visual Studio include dispositivi virtuali preconfigurati che vengono visualizzati nel menu a discesa dei dispositivi. Nello screenshot di Visual Studio 2017 seguente, ad esempio, sono disponibili diversi dispositivi virtuali preconfigurati:

-   **VisualStudio\_android-23\_arm\_phone**

-   **VisualStudio\_android-23\_arm\_tablet**

-   **VisualStudio\_android-23\_x86\_phone** 

-   **VisualStudio\_android-23\_x86\_tablet** 

[![Dispositivi virtuali](running-the-emulator-images/win/01-virtual-devices-sml.png)](running-the-emulator-images/win/01-virtual-devices.png#lightbox)

In genere, per le attività di test e debug di un'app per il telefono si seleziona il dispositivo virtuale **VisualStudio\_android 23\_x86\_phone**. Se uno di questi dispositivi virtuali preconfigurati soddisfa i requisiti (ad esempio, corrisponde al livello API di destinazione dell'app), passare ad [Avvio dell'emulatore](#launching) per iniziare l'esecuzione dell'app nell'emulatore. Se non si ha ancora familiarità con i livelli API di Android, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Informazioni sui livelli API di Android).

Se il progetto Xamarin.Android usa un livello di framework di destinazione non compatibile con i dispositivi virtuali disponibili, nel menu di riepilogo a discesa verranno elencati i dispositivi virtuali inutilizzabili in **Dispositivi non supportati**. Ad esempio, per il progetto seguente è impostato il framework di destinazione **Android 7.1 Nougat (API 25)**, non compatibile con i dispositivi virtuali **Android 6.0** forniti per impostazione predefinita:

[![Dispositivo virtuale non compatibile](running-the-emulator-images/win/02-incompatible-level-sml.png)](running-the-emulator-images/win/02-incompatible-level.png#lightbox)

È possibile fare clic su **Cambia destinazione minima di Android** per modificare la versione minima di Android del progetto in modo che corrisponda al livello API dei dispositivi virtuali disponibili. In alternativa, è possibile usare **Gestione emulatori Android** per creare nuovi dispositivi virtuali che supportano il livello API di destinazione come descritto più avanti in [Configurazione dei dispositivi virtuali](#virtualdevice). Prima di poter configurare i dispositivi virtuali per un nuovo livello API, è necessario installare le immagini di sistema corrispondenti per tale livello API. Queste operazioni sono illustrate nella sezione successiva.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Visual Studio per Mac include dispositivi virtuali preconfigurati visualizzati nel menu a discesa dei dispositivi. Nello screenshot seguente, ad esempio, sono disponibili due dispositivi virtuali preconfigurati:

-   **Android\_Accelerated\_x86**

-   **Android\_ARMv7a**

[![Dispositivi virtuali](running-the-emulator-images/mac/01-virtual-devices-sml.png)](running-the-emulator-images/mac/01-virtual-devices.png#lightbox)

Per le attività di test e debug di un'app per telefono, viene in genere selezionato il dispositivo virtuale **Android\_Accelerated\_x86**. Se questo dispositivo virtuale preconfigurato soddisfa i requisiti (ad esempio, corrisponde al livello API di destinazione dell'app), passare ad [Avvio dell'emulatore](#launching) per iniziare l'esecuzione dell'app nell'emulatore. Se non si ha ancora familiarità con i livelli API di Android, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Informazioni sui livelli API di Android).

-----

## <a name="creating-custom-virtual-devices"></a>Creazione di dispositivi virtuali personalizzati

Per creare dispositivi virtuali personalizzati, è necessario usare Gestione dispositivi di Xamarin Android o la versione legacy di Gestione emulatori Google inclusa in Android SDK. Per altre informazioni sulla creazione e personalizzazione di dispositivi virtuali, vedere [Gestione dispositivi di Xamarin Android](~/android/get-started/installation/android-emulator/xamarin-device-manager.md).
Se si preferisce usare la versione legacy di Gestione emulatori Google, vedere [Gestione emulatori Google](~/android/get-started/installation/android-emulator/google-emulator-manager.md).

Si noti che se si sviluppa per Android 8.0 Oreo è necessario usare Gestione dispositivi di Xamarin Android.

<a name="launching" />

## <a name="launching-the-emulator"></a>Avvio dell'emulatore

Nella parte superiore dell'IDE è disponibile un menu a discesa che consente di selezionare la modalità **Debug** o **Versione**. Se si sceglie la modalità **Debug**, il debugger viene collegato al processo dell'applicazione in esecuzione all'interno dell'emulatore. 

Dopo aver scelto un dispositivo virtuale dal menu a discesa dei dispositivi, selezionare la modalità **Debug** o **Versione** e quindi fare clic sul pulsante **Esegui** per eseguire l'applicazione:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Modalità Debug e Versione, pulsante Esegui](running-the-emulator-images/win/17-debug-release-sml.png)](running-the-emulator-images/win/17-debug-release.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Modalità Debug e Versione, pulsante Esegui](running-the-emulator-images/mac/16-debug-release-sml.png)](running-the-emulator-images/mac/16-debug-release.png#lightbox)

-----

Dopo l'avvio dell'emulatore Android, Xamarin.Android distribuirà l'app nell'emulatore. L'emulatore esegue l'app con l'immagine del dispositivo virtuale configurato. Di seguito è disponibile uno screenshot di esempio dell'emulatore Android di Google. L'emulatore esegue un'app vuota denominata **MyApp**:

![Emulatore che esegue un'app vuota](running-the-emulator-images/emulator-running.png)

L'emulatore può essere lasciato in esecuzione. Non è necessario arrestarlo e riavviarlo ogni volta che si esegue l'app. Alla prima esecuzione di un'app Xamarin.Android nell'emulatore, viene installato il runtime condiviso di Xamarin.Android per il livello API di destinazione, seguito dall'applicazione. L'installazione del runtime può richiedere un po' di tempo. L'installazione del runtime viene eseguita solo in occasione della distribuzione della prima app Xamarin.Android nell'emulatore. Le distribuzioni successive sono più veloci perché nell'emulatore viene copiata solo l'app.

Per altre informazioni sull'uso dell'emulatore Android di Google, vedere gli argomenti seguenti per sviluppatori Android:

-   [Navigating on the Screen](https://developer.android.com/studio/run/emulator.html#navigate) (Spostamenti nello schermo)

-   [Performing Basic Tasks in the Emulator](https://developer.android.com/studio/run/emulator.html#tasks) (Esecuzione di attività di base nell'emulatore)

-   [Working with Extended Controls, Settings, and Help](https://developer.android.com/studio/run/emulator.html#extended) (Utilizzo di controlli estesi, impostazioni e Guida)

