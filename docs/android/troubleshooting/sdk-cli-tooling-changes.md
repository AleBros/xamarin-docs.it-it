---
title: Modifiche agli strumenti di Android SDK
description: Modifiche al modo in cui Android SDK gestisce i livelli API di installati e Avd.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: dbd3287e7c646be7fd969699eab685906a1c6c1a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093935"
---
# <a name="changes-to-the-android-sdk-tooling"></a>Modifiche agli strumenti di Android SDK

_Modifiche al modo in cui Android SDK gestisce i livelli API di installati e Avd._

## <a name="changes-to-android-sdk-tooling"></a>Modifiche apportate agli strumenti Android SDK

Nelle versioni recenti di SDK Tools per Android di Google ha rimosso l'AVD e SDK Manager esistenti a favore di nuovi strumenti CLI (interfaccia della riga di comando). Il **android** programma è stata rimossa e i responsabili di Google GUI (interfaccia utente grafica) in Visual Studio per Mac e le versioni precedenti di Visual Studio Tools per Xamarin non funzionerà più oltre la versione 25.2.5 di Android SDK Tools. Ad esempio, il tentativo di utilizzare il **android** programma tramite la riga di comando comporterà un messaggio di errore simile al seguente:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

Le sezioni seguenti illustrano come gestire Android SDK e i dispositivi virtuali Android con Android SDK 25.3.0 e versioni successive.

### <a name="ui-tools"></a>Strumenti dell'interfaccia utente

Visual Studio e Visual Studio per Mac ora fornire sostituzioni di Xamarin per i responsabili di Google con interfaccia grafica non più utilizzate:

-   Per scaricare gli strumenti Android SDK, piattaforme e altri componenti necessari per lo sviluppo di App xamarin. Android, usare il [Xamarin Android SDK Manager](~/android/get-started/installation/android-sdk.md) anziché istanza legacy di Google SDK Manager.

-   Per creare e configurare dispositivi virtuali Android, usare il [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md) anziché legacy Gestione emulatori Google.

Questi strumenti sono funzionalmente equivalenti di Google con interfaccia grafica che sostituiscono i responsabili.

### <a name="cli-tools"></a>Strumenti CLI

In alternativa, è possibile utilizzare gli strumenti CLI per gestire e aggiornare gli emulatori e Android SDK. Ora che i programmi seguenti costituiscono l'interfaccia della riga di comando per gli strumenti di Android SDK:

#### <a name="sdkmanager"></a>sdkmanager

**Aggiunto In:** Android SDK Tools 25.2.3 (novembre 2016) e versioni successive.

È presente un nuovo programma chiamato **sdkmanager** nel **tools/bin** cartella dell'Android SDK. Questo strumento viene usato per gestire Android SDK dalla riga di comando. Per altre informazioni sull'uso di questo strumento, vedere [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Aggiunto In:** Android SDK Tools 25.3.0 (marzo 2017) e versioni successive.

È presente un nuovo programma chiamato **avdmanager** nel **tools/bin** cartella dell'Android SDK. Questo strumento viene utilizzato per mantenere l'Avd per l'emulatore di Android. Per altre informazioni sull'uso di questo strumento, vedere [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Il downgrade

È possibile effettuare il downgrade la **Android SDK Tools** versione tramite l'installazione di una versione precedente di Android SDK è disponibile il [sito Web per sviluppatori Android](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>Usando l'interfaccia utente grafica precedente

È comunque possibile usare l'interfaccia utente grafica originale eseguendo il **android** all'interno del programma il **strumenti** cartella fino a quando si usa **Android SDK Tools** versione **versione 25.2.5 degli**  o inferiore.


## <a name="related-links"></a>Collegamenti correlati

- [Installazione di Android SDK](~/android/get-started/installation/android-sdk.md)
- [Gestione di dispositivi Android](~/android/get-started/installation/android-emulator/device-manager.md)
- [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Informazioni sui livelli API Android)
- [Note sulla versione di SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
