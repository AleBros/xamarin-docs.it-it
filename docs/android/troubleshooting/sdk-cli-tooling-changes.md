---
title: Modifiche agli strumenti di Android SDK
description: Modifiche al modo in cui Android SDK gestisce i livelli API installati e gli AVD.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 4c559a76d7354fd957d065717ef14d91591d1be0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019505"
---
# <a name="changes-to-the-android-sdk-tooling"></a>Modifiche agli strumenti di Android SDK

_Modifiche al modo in cui Android SDK gestisce i livelli API installati e gli AVD._

## <a name="changes-to-android-sdk-tooling"></a>Modifiche agli strumenti di Android SDKChanges to Android SDK Tooling

Nelle versioni recenti di SDK Tools per Android, Google ha rimosso i gestori AVD e SDK esistenti a favore dei nuovi strumenti CLI (Command Line Interface). Il programma **Android** è stato rimosso e i gestori di Google GUI (Graphical User Interface) in Visual Studio per Mac e le versioni precedenti di Visual Studio Tools per Xamarin non funzioneranno più oltre la versione 25.2.5 di Android SDK Tools. Ad esempio, il tentativo di utilizzare il programma **Android** tramite la riga di comando genererà un messaggio di errore simile al seguente:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

Le sezioni seguenti illustrano come gestire Android SDK e Android Virtual Devices usando Android SDK 25.3.0 e versioni successive.

### <a name="ui-tools"></a>Strumenti dell'interfaccia utente

Visual Studio e Visual Studio per Mac ora forniscono sostituzioni Xamarin per i gestori basati su Google GUI non più disponibili:

- Per scaricare gli strumenti, le piattaforme e altri componenti di Android SDK necessari per lo sviluppo di app Xamarin.Android, usa [Xamarin Android SDK Manager](~/android/get-started/installation/android-sdk.md) anziché Google SDK Manager legacy.

- Per creare e configurare dispositivi virtuali Android, utilizzare [Gestione dispositivi Android](~/android/get-started/installation/android-emulator/device-manager.md) anziché Gestione emulatori Google legacy.

Questi strumenti sono funzionalmente equivalenti ai gestori basati su GUI di Google che sostituiscono.

### <a name="cli-tools"></a>Strumenti CLI

In alternativa, è possibile utilizzare gli strumenti CLI per gestire e aggiornare gli emulatori e Android SDK. I seguenti programmi ora costituiscono l'interfaccia della riga di comando per gli strumenti di Android SDK:

#### <a name="sdkmanager"></a>sdkmanager

**Aggiunto in:** Android SDK Tools 25.2.3 (novembre, 2016) e versioni successive.

C'è un nuovo programma chiamato **sdkmanager** nella cartella **tools/bin** del tuo Android SDK. Questo strumento viene utilizzato per gestire l'SDK di Android dalla riga di comando. Per ulteriori informazioni sull'utilizzo di questo strumento, vedere [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Aggiunto in:** Android SDK Tools 25.3.0 (marzo 2017) e versioni successive.

C'è un nuovo programma chiamato **avdmanager** nella cartella **tools/bin** del tuo Android SDK. Questo strumento viene utilizzato per mantenere gli AVD per l'emulatore Android. Per ulteriori informazioni sull'utilizzo di questo strumento, vedere [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Declassamento

Puoi effettuare il downgrade della versione di **Android SDK Tools** installando una versione precedente dell'SDK di Android dal sito Web di Android [Developer.](https://developer.android.com/studio/index.html)

### <a name="using-the-old-gui"></a>Utilizzo della vecchia GUI

È comunque possibile utilizzare la GUI originale eseguendo il programma **Android** all'interno della cartella **degli strumenti,** purché si è su **Android SDK Tools** versione **25.2.5** o inferiore.

## <a name="related-links"></a>Collegamenti correlati

- [Installazione di Android SDK](~/android/get-started/installation/android-sdk.md)
- [Gestione dispositivi Android](~/android/get-started/installation/android-emulator/device-manager.md)
- [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Informazioni sui livelli API Android)
- [Note sulla versione di SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
