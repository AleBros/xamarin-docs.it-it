---
title: Quali driver USB sono necessari per il debug di Android in Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 8be3f1b8803aa7e052ebc89af51dad3b659f95f5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757323"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>Quali driver USB sono necessari per il debug di Android in Windows?

## <a name="finding-usb-drivers"></a>Ricerca di driver USB

Per eseguire il debug su un dispositivo Android durante lo sviluppo in Windows; è necessario installare un driver USB compatibile. Per impostazione predefinita, il gestore Android SDK include "Google USB driver", che aggiunge il supporto per i dispositivi Nexus, come descritto di seguito:[https://developer.android.com/sdk/win-usb.html](https://developer.android.com/sdk/win-usb.html)

Altri dispositivi richiedono driver USB pubblicati in modo specifico dal produttore del dispositivo. In questa guida sono inclusi alcuni collegamenti per i produttori più comuni:[https://developer.android.com/tools/extras/oem-usb.html](https://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternative

A seconda del manfacturer, può essere difficile individuare il driver USB esatto necessario. Alcune alternative per testare le app Android sviluppate in Windows, tra cui l'uso di un emulatore Android o l'uso di servizi di test esterni. tra cui:

- [App Center](https://docs.microsoft.com/appcenter/test-cloud/) i servizi test-cloud testing vengono eseguiti su centinaia di dispositivi Android reali.

- [Visual Studio Emulator for Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md)
