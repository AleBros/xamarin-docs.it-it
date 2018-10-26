---
title: Quali driver USB è necessario eseguire il debug di Android in Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 8b996a1cc89acedc47c7169ec579dfb99dae788f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114535"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>Quali driver USB è necessario eseguire il debug di Android in Windows?

## <a name="finding-usb-drivers"></a>Come trovare i driver USB

Per eseguire il debug in un dispositivo Android quando si sviluppa in Windows; è necessario installare un driver USB compatibile. Android SDK Manager include il "Driver USB Google" per impostazione predefinita, che aggiunge il supporto per i dispositivi Nexus come descritto di seguito: [http://developer.android.com/sdk/win-usb.html](http://developer.android.com/sdk/win-usb.html)

Altri dispositivi hanno bisogno di driver USB pubblicati appositamente dal produttore del dispositivo. In questa guida sono inclusi alcuni collegamenti per i produttori più comuni: [http://developer.android.com/tools/extras/oem-usb.html](http://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternative

A seconda fabbricante, può essere difficile tenere i driver USB esatti Necessito. Alcune alternative per testare App Android sviluppata in Windows, tra cui usando un emulatore Android o uso di servizi di test esterni. tra cui:

- [Test App Center](https://docs.microsoft.com/appcenter/test-cloud/) : test vengono eseguiti su centinaia di dispositivi Android reali i servizi Cloud.

- [Visual Studio Emulator for Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md)

