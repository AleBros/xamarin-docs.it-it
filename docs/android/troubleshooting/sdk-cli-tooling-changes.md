---
title: Modifiche agli strumenti di Android SDK
description: Modifiche al modo in cui il Android SDK gestisce i livelli di API e AVD installati.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 4c559a76d7354fd957d065717ef14d91591d1be0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019505"
---
# <a name="changes-to-the-android-sdk-tooling"></a>Modifiche agli strumenti di Android SDK

_Modifiche al modo in cui il Android SDK gestisce i livelli di API e AVD installati._

## <a name="changes-to-android-sdk-tooling"></a>Modifiche agli strumenti di Android SDK

Nelle versioni recenti del SDK Tools per Android, Google ha rimosso i gestori di AVD e SDK esistenti a favore dei nuovi strumenti CLI (interfaccia della riga di comando). Il programma **Android** è stato rimosso e i manager di Google GUI (interfaccia utente grafica) in Visual Studio per Mac e le versioni precedenti di strumenti di Visual Studio per Novell non funzioneranno più oltre la versione 25.2.5 di Android SDK Tools. Ad esempio, se si tenta di usare il programma **Android** tramite la riga di comando, verrà visualizzato un messaggio di errore simile al seguente:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

Le sezioni seguenti illustrano come gestire i dispositivi virtuali Android SDK e Android usando Android SDK 25.3.0 e versioni successive.

### <a name="ui-tools"></a>Strumenti dell'interfaccia utente

Visual Studio e Visual Studio per Mac ora forniscono sostituzioni Novell per i manager basati su GUI di Google obsoleti:

- Per scaricare Android SDK strumenti, le piattaforme e gli altri componenti necessari per lo sviluppo di app Xamarin.Android, usare [novell Android SDK Manager](~/android/get-started/installation/android-sdk.md) invece di Google SDK Manager legacy.

- Per creare e configurare i dispositivi virtuali Android, usare il [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md) invece di Gestione emulatori Google legacy.

Questi strumenti sono equivalenti dal punto di vista funzionale ai manager basati su Google GUI che sostituiscono.

### <a name="cli-tools"></a>Strumenti dell'interfaccia della riga di comando

In alternativa, è possibile usare gli strumenti dell'interfaccia della riga di comando per gestire e aggiornare gli emulatori e Android SDK. I programmi seguenti costituiscono ora l'interfaccia della riga di comando per gli strumenti di Android SDK:

#### <a name="sdkmanager"></a>sdkmanager

**Aggiunto in:** Android SDK Tools 25.2.3 (novembre, 2016) e versioni successive.

È disponibile un nuovo programma denominato **sdkmanager** nella cartella **Tools/bin** del Android SDK. Questo strumento viene utilizzato per mantenere il Android SDK dalla riga di comando. Per ulteriori informazioni sull'utilizzo di questo strumento, vedere [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Aggiunto in:** Android SDK Tools 25.3.0 (marzo, 2017) e versioni successive.

È disponibile un nuovo programma denominato **avdmanager** nella cartella **Tools/bin** del Android SDK. Questo strumento viene usato per gestire AVD per la emulatore Android. Per ulteriori informazioni sull'utilizzo di questo strumento, vedere [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Declassamento

È possibile effettuare il downgrade della versione **Android SDK Tools** installando una versione precedente del Android SDK dal [sito Web per sviluppatori Android](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>Uso della GUI precedente

È comunque possibile usare l'interfaccia utente grafica originale eseguendo il programma **Android** all'interno della cartella **degli strumenti** , purché si sia **Android SDK Tools** versione **25.2.5** o precedente.

## <a name="related-links"></a>Collegamenti correlati

- [Installazione di Android SDK](~/android/get-started/installation/android-sdk.md)
- [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)
- [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Informazioni sui livelli API Android)
- [Note sulla versione di SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
