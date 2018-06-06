---
title: I driver USB necessario eseguire il debug di Android in Windows
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/30/2018
ms.openlocfilehash: 7b3a4c2f807839897a099959fe3a6ea9ec25df78
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732749"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>I driver USB necessario eseguire il debug di Android in Windows

## <a name="finding-usb-drivers"></a>Come trovare i driver USB

Per eseguire il debug in un dispositivo Android quando si sviluppa in Windows. è necessario installare un driver USB compatibile. Android SDK Manager include il "Driver USB Google" per impostazione predefinita, che aggiunge il supporto per i dispositivi Nexus come descritto di seguito: [http://developer.android.com/sdk/win-usb.html](http://developer.android.com/sdk/win-usb.html)

I driver USB pubblicati appositamente dal produttore del dispositivo sono necessari altri dispositivi. Alcuni collegamenti per i produttori più comuni sono inclusi in questa guida: [http://developer.android.com/tools/extras/oem-usb.html](http://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternative

A seconda fabbricante, può essere difficile individuare i driver USB esatti Necessito. Sono disponibili alcune alternative per il test di App Android sviluppate in Windows inclusi usando un emulatore Android o verificare i servizi esterni. tra cui:

- [Test di App Center](https://docs.microsoft.com/appcenter/test-cloud/) - test vengono eseguiti in centinaia di dispositivi Android reale i servizi Cloud.

- [Visual Studio Emulator for Android](https://www.visualstudio.com/en-us/features/msft-android-emulator-vs.aspx)

- [Debug con l'emulatore Android di Google](~/android/deploy-test/debugging/android-sdk-emulator/index.md)

