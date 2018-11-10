---
title: Emulatore della riga di comando
ms.prod: xamarin
ms.assetid: E592AA32-5E83-B7E5-1753-12416551B23C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 11/05/2018
ms.openlocfilehash: 995b0783604f752915daaa77a8362899ac61e174
ms.sourcegitcommit: f541a92b4f896474f6a5467ccff2028dafa6fee7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "50983588"
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

Per altre informazioni sui parametri, visitare l'indirizzo seguente nel sito di Android: [https://developer.android.com/studio/run/emulator-commandline](https://developer.android.com/studio/run/emulator-commandline).
