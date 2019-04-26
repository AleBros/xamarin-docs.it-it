---
title: Quali driver USB sono necessari per il debug di Android in Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 85045967f5c63eb39c45f917b957d2a393a3a068
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60945562"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>Quali driver USB sono necessari per il debug di Android in Windows?

## <a name="finding-usb-drivers"></a>Come trovare i driver USB

Per eseguire il debug in un dispositivo Android quando si sviluppa in Windows; è necessario installare un driver USB compatibile. Android SDK Manager include il "Driver USB Google" per impostazione predefinita, che aggiunge il supporto per i dispositivi Nexus come descritto di seguito: [https://developer.android.com/sdk/win-usb.html](https://developer.android.com/sdk/win-usb.html)

Altri dispositivi hanno bisogno di driver USB pubblicati appositamente dal produttore del dispositivo. In questa guida sono inclusi alcuni collegamenti per i produttori più comuni: [https://developer.android.com/tools/extras/oem-usb.html](https://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternative

A seconda fabbricante, può essere difficile tenere i driver USB esatti Necessito. Alcune alternative per testare App Android sviluppata in Windows, tra cui usando un emulatore Android o uso di servizi di test esterni. tra cui:

- [Test App Center](https://docs.microsoft.com/appcenter/test-cloud/) : test vengono eseguiti su centinaia di dispositivi Android reali i servizi Cloud.

- [Visual Studio Emulator for Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md)

