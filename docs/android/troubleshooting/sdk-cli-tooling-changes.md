---
title: Modifiche agli strumenti di Android SDK
description: Modifiche come Android SDK gestisce i livelli di API e i AVDs installato.
ms.topic: article
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: 69e9f08870a01c056951700978d07277af5edfa8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="changes-to-the-android-sdk-tooling"></a>Modifiche agli strumenti di Android SDK

_Modifiche come Android SDK gestisce i livelli di API e i AVDs installato._

## <a name="changes-to--android-sdk-tooling"></a>Modifiche per gli strumenti di Android SDK

Nelle versioni più recenti degli strumenti di SDK per Android, Google ha rimosso le Gestioni AVD e SDK esistenti favore nuovi _interfaccia della riga di comando_ strumenti (CLI). Il primo **android** programma è stato rimosso e i gestori di interfaccia utente grafica (GUI) in Visual Studio per Mac e le versioni precedenti di Xamarin per Visual Studio non funzionerà più dopo gli strumenti SDK Android versione.


![Menu IDE Android in Visual Studio](sdk-cli-tooling-changes-images/android-ide-menu.png)

Tentativo di utilizzare il **android** programma tramite la riga di comando comporterà un messaggio di errore simile al seguente:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

È pertanto necessario utilizzare gli strumenti CLI per gestire e aggiornare le emulatori Android SDK.

### <a name="cli-tools"></a>Strumenti CLI

I seguenti programmi che ora costituiscono l'interfaccia della riga di comando per gli strumenti di Android SDK:

#### <a name="sdkmanager"></a>sdkmanager

**Aggiunto In:** strumenti Android SDK 25.2.3 (novembre 2016) e versioni successive.

È un nuovo programma chiamato **sdkmanager** nel **tools/bin** cartella il Android SDK. Questo strumento viene utilizzato per mantenere il Android SDK dalla riga di comando. Per ulteriori informazioni sull'utilizzo di questo strumento, vedere [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Aggiunto In:** strumenti Android SDK (marzo 2017) 25.3.0 e versioni successive.

È un nuovo programma chiamato **avdmanager** nel **tools/bin** cartella il Android SDK. Questo strumento viene utilizzato per gestire il AVD per l'emulatore Android di Google. Per ulteriori informazioni sull'utilizzo di questo strumento, vedere [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Downgrade

È possibile effettuare il downgrade del **strumenti Android SDK** installa una versione precedente di Android SDK da una versione di [sito Web Android Developer](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>Tramite l'interfaccia utente grafica precedente

È comunque possibile usare l'interfaccia utente grafica originale eseguendo il **android** programma all'interno del **strumenti** a condizione che si è nella cartella **strumenti Android SDK** versione **25.2.5**  o inferiore.


## <a name="related-links"></a>Collegamenti correlati

- [Installazione di Android SDK](~/android/get-started/installation/android-sdk.md)
- [Informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md)
- [Note sulla versione (Google) degli strumenti SDK](https://developer.android.com/studiohttps://developer.xamarin.com/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
