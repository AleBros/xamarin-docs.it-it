---
title: Quali driver USB sono necessari per il debug di Android in Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 21fd8eff64d374e52e64194524a8c096cdf4d90e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027032"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>Quali driver USB sono necessari per il debug di Android in Windows?

## <a name="finding-usb-drivers"></a>Ricerca di driver USB

Per eseguire il debug su un dispositivo Android durante lo sviluppo in Windows; è necessario installare un driver USB compatibile. Android SDK Manager include il "Google USB Driver" per impostazione predefinita, che aggiunge il supporto per i dispositivi Nexus come descritto di seguito:[https://developer.android.com/sdk/win-usb.html](https://developer.android.com/sdk/win-usb.html)

Altri dispositivi richiedono driver USB appositamente pubblicati dal produttore del dispositivo. Alcuni collegamenti per i produttori più comuni sono inclusi in questa guida:[https://developer.android.com/tools/extras/oem-usb.html](https://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternativi

A seconda del manfacturer, può essere difficile rintracciare l'esatto driver USB necessario. Alcune alternative per testare le app Android sviluppate in Windows, tra cui l'uso di un emulatore Android o l'uso di servizi di test esterni. tra cui:

- [App Center Test](https://docs.microsoft.com/appcenter/test-cloud/) - I servizi di test cloud vengono eseguiti su centinaia di dispositivi Android reali.

- [Emulatore di Visual Studio per Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md)
