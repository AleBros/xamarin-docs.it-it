---
title: Emulatore della riga di comando
ms.prod: xamarin
ms.assetid: E592AA32-5E83-B7E5-1753-12416551B23C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 11/05/2018
ms.openlocfilehash: 35e10ffc20e075e0245c7e42f7fd0aff24de4abb
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73021574"
---
# <a name="command-line-emulator"></a>Emulatore della riga di comando

## <a name="running-the-android-emulator-from-the-command-line"></a>Esecuzione dell'emulatore Android dalla scheda riga di comando

Per abilitare l'esecuzione dell'emulatore Android dalla riga di comando, è possibile usare lo strumento "emulatore" disponibile in Android SDK. Questo strumento può essere usato per eseguire l'emulatore dal terminale in OS X o dal prompt dei comandi in un computer Windows.

Per avviare uno specifico emulatore Android, eseguire il comando seguente dalla directory tools nella posizione di Android SDK (ad esempio, C:\android-sdk-windows\tools):

In Windows

```cmd
emulator.exe -avd NameOfYourEmulator -partition-size 512
```

In macOS

```bash
./emulator -avd NameOfYourEmulator -partition-size 512
```

Le dimensioni della partizione sono necessarie per consentire all'emulatore di avere spazio sufficiente per l'installazione della piattaforma Xamarin.Android nell'emulatore perché, per impostazione predefinita, l'emulatore è di piccole dimensioni.

Puoi trovare maggiori informazioni sui parametri extra sul sito Android qui -[https://developer.android.com/studio/run/emulator-commandline](https://developer.android.com/studio/run/emulator-commandline)
