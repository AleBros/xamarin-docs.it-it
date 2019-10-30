---
title: Debug nell'emulatore Android
description: Questa guida illustra come avviare ed eseguire il debug delle app in Visual Studio usando l'emulatore Android.
ms.prod: xamarin
ms.assetid: AEA165A4-D81A-411B-91DF-2DED2EED27B5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 8ca13b4f9c961b8bb206d065ce3cf641a8662160
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028089"
---
# <a name="debugging-on-the-android-emulator"></a>Debug nell'emulatore Android

_Questa guida contiene informazioni su come avviare un dispositivo virtuale nell'emulatore Android per eseguire il debug e il test dell'app._

## <a name="overview"></a>Panoramica

L'emulatore Android (installato con il carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET**) può essere eseguito in diverse configurazioni per simulare dispositivi Android differenti. Ognuna di queste configurazioni viene creata come _dispositivo virtuale_. In questa guida viene illustrato come avviare l'emulatore da Visual Studio ed eseguire l'app in un dispositivo virtuale. Per informazioni sulla configurazione dell'emulatore Android e la creazione di nuovi dispositivi virtuali, vedere [Configurazione dell'emulatore Android](~/android/get-started/installation/android-emulator/index.md).

## <a name="using-a-pre-configured-virtual-device"></a>Uso di un dispositivo virtuale preconfigurato

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio include dispositivi virtuali preconfigurati che vengono visualizzati nel menu a discesa dei dispositivi. Nello screenshot di Visual Studio 2017 seguente, ad esempio, sono disponibili diversi dispositivi virtuali preconfigurati:

- **VisualStudio\_android-23\_arm\_phone**

- **VisualStudio\_android-23\_arm\_tablet**

- **VisualStudio\_android-23\_x86\_phone** 

- **VisualStudio\_android-23\_x86\_tablet** 

[![Dispositivi virtuali](debug-on-emulator-images/win/01-virtual-devices-sml.png)](debug-on-emulator-images/win/01-virtual-devices.png#lightbox)

In genere, per le attività di test e debug di un'app per il telefono si seleziona il dispositivo virtuale **VisualStudio\_android 23\_x86\_phone**. Se uno di questi dispositivi virtuali preconfigurati soddisfa i requisiti (ad esempio, corrisponde al livello API di destinazione dell'app), passare ad [Avvio dell'emulatore](#launching) per iniziare l'esecuzione dell'app nell'emulatore. Se non si ha ancora familiarità con i livelli API di Android, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Informazioni sui livelli API di Android).

Se il progetto Xamarin.Android usa un livello di framework di destinazione non compatibile con i dispositivi virtuali disponibili, nel menu a discesa vengono elencati i dispositivi virtuali inutilizzabili in **Dispositivi non supportati**. Ad esempio, per il progetto seguente è impostato il framework di destinazione **Android 7.1 Nougat (API 25)** , non compatibile con i dispositivi virtuali **Android 6.0** elencati in questo esempio:

[![Dispositivo virtuale non compatibile](debug-on-emulator-images/win/02-incompatible-level-sml.png)](debug-on-emulator-images/win/02-incompatible-level.png#lightbox)

È possibile fare clic su **Cambia destinazione minima di Android** per modificare la versione minima di Android del progetto in modo che corrisponda al livello API dei dispositivi virtuali disponibili. In alternativa, è possibile usare [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md) per creare nuovi dispositivi virtuali che supportano il livello API di destinazione.
Prima di poter configurare i dispositivi virtuali per un nuovo livello API, è necessario installare le immagini di sistema corrispondenti per tale livello API (vedere [Configurazione di Android SDK per Xamarin.Android](~/android/get-started/installation/android-sdk.md)).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Visual Studio per Mac include dispositivi virtuali preconfigurati visualizzati nel menu a discesa dei dispositivi. Nello screenshot seguente, ad esempio, sono disponibili due dispositivi virtuali preconfigurati:

- **Android\_Accelerated\_x86**

- **Android\_ARMv7a**

[![Dispositivi virtuali](debug-on-emulator-images/mac/01-virtual-devices-sml.png)](debug-on-emulator-images/mac/01-virtual-devices.png#lightbox)

Per le attività di test e debug di un'app per telefono, viene in genere selezionato il dispositivo virtuale **Android\_Accelerated\_x86**. Se questo dispositivo virtuale preconfigurato soddisfa i requisiti (ad esempio, corrisponde al livello API di destinazione dell'app), passare ad [Avvio dell'emulatore](#launching) per iniziare l'esecuzione dell'app nell'emulatore. Se non si ha ancora familiarità con i livelli API di Android, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Informazioni sui livelli API di Android).

-----

## <a name="editing-virtual-devices"></a>Modifica di dispositivi virtuali

Per modificare i dispositivi virtuali (o crearne di nuovi), occorre usare [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).

<a name="launching" />

## <a name="launching-the-emulator"></a>Avvio dell'emulatore

Nella parte superiore di Visual Studio è disponibile un menu a discesa che consente di selezionare la modalità **Debug** o **Versione**. Se si sceglie la modalità **Debug**, il debugger viene collegato al processo dell'applicazione in esecuzione all'interno dell'emulatore dopo l'avvio dell'app. Se si sceglie la modalità **Versione**, il debugger viene disabilitato (tuttavia, è comunque possibile eseguire l'app e usare le istruzioni di registrazione per il debug). Dopo aver scelto un dispositivo virtuale dal menu a discesa dei dispositivi, selezionare la modalità **Debug** o **Versione** e quindi fare clic sul pulsante Esegui per eseguire l'applicazione:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Modalità Debug e Versione, pulsante Esegui](debug-on-emulator-images/win/17-debug-release-sml.png)](debug-on-emulator-images/win/17-debug-release.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Modalità Debug e Versione, pulsante Esegui](debug-on-emulator-images/mac/16-debug-release-sml.png)](debug-on-emulator-images/mac/16-debug-release.png#lightbox)

-----

Dopo l'avvio dell'emulatore, Xamarin.Android distribuirà l'app nell'emulatore. L'emulatore esegue l'app con l'immagine del dispositivo virtuale configurato. Di seguito è visualizzato uno screenshot di esempio dell'emulatore Android. In questo esempio l'emulatore esegue un'app vuota chiamata **MyApp**:

![Emulatore che esegue un'app vuota](debug-on-emulator-images/emulator-running.png)

L'emulatore può essere lasciato in esecuzione: non è necessario arrestarlo e attenderne il riavvio ogni volta che si avvia l'app. Alla prima esecuzione di un'app Xamarin.Android nell'emulatore, viene installato il runtime condiviso di Xamarin.Android per il livello API di destinazione, seguito dall'applicazione. L'installazione del runtime può richiedere un po' di tempo. L'installazione del runtime viene eseguita solo in occasione della distribuzione della prima app Xamarin.Android nell'emulatore. Le distribuzioni successive sono più veloci perché nell'emulatore viene copiata solo l'app.

<a name="quick-boot" />

## <a name="quick-boot"></a>Avvio rapido

Le versioni più recenti dell'emulatore Android includono una funzionalità denominata _Avvio rapido_ che avvia l'emulatore in pochi secondi. Quando si chiude l'emulatore, viene eseguito uno snapshot dello stato del dispositivo virtuale, in modo che possa essere rapidamente ripristinato da tale stato quando viene riavviato.
Per accedere a questa funzionalità, è necessario disporre di:

- Emulatore Android 27.0.2 o versione successiva
- Android SDK Tools 26.1.1 o versione successiva

Quando sono installate le versioni dell'emulatore e di SDK Tools elencate in precedenza, la funzionalità di avvio rapido è abilitata per impostazione predefinita. 

Il primo avvio a freddo del dispositivo virtuale viene eseguito senza un miglioramento della velocità poiché non è stato ancora creato uno snapshot:

![Screenshot dell'avvio a freddo](debug-on-emulator-images/cold-boot.png)

Quando si esce dall'emulatore, la funzionalità di avvio rapido salva lo stato dell'emulatore in uno snapshot:

![Salvataggio dello stato in fase di arresto del sistema](debug-on-emulator-images/saving-state.png)

Gli avvii successivi del dispositivo virtuale sono molto più veloci perché l'emulatore ripristina semplicemente lo stato in cui è stato chiuso l'emulatore.

![Caricamento dello stato in fase di riavvio](debug-on-emulator-images/loading-state.png)

## <a name="troubleshooting"></a>Troubleshooting

Per suggerimenti e soluzioni alternative per i problemi comuni dell'emulatore, vedere [Risoluzione dei problemi di configurazione dell'emulatore](~/android/get-started/installation/android-emulator/troubleshooting.md).

## <a name="summary"></a>Riepilogo

In questa guida è stato descritto il processo di configurazione dell'emulatore Android per eseguire e testare le app Xamarin.Android. Sono state illustrate le procedure per avviare l'emulatore usando dispositivi virtuali preconfigurati e per distribuire un'applicazione nell'emulatore da Visual Studio. 

Per altre informazioni sull'uso dell'emulatore Android, vedere gli argomenti seguenti per sviluppatori Android:

- [Navigating on the Screen](https://developer.android.com/studio/run/emulator.html#navigate) (Spostamenti nello schermo)

- [Performing Basic Tasks in the Emulator](https://developer.android.com/studio/run/emulator.html#tasks) (Esecuzione di attività di base nell'emulatore)

- [Working with Extended Controls, Settings, and Help](https://developer.android.com/studio/run/emulator.html#extended) (Utilizzo di controlli estesi, impostazioni e Guida)

- [Run the emulator with Quick Boot](https://developer.android.com/studio/run/emulator#quickboot) (Eseguire l'emulatore con l'avvio rapido)
