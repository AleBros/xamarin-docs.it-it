---
title: I driver USB necessaria eseguire il debug di Android in Windows
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: 8b49d02b9670e66d04060375e59b005905c41bf7
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935256"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>I driver USB necessaria eseguire il debug di Android in Windows

## <a name="finding-usb-drivers"></a>Ricerca di driver USB

Per eseguire il debug in un dispositivo Android quando si sviluppa in Windows. è necessario installare un driver USB compatibile. Android SDK Manager include il "Driver USB Google" per impostazione predefinita, che aggiunge il supporto per i dispositivi Nexus come descritto di seguito: [http://developer.android.com/sdk/win-usb.html](http://developer.android.com/sdk/win-usb.html)

Altri dispositivi hanno bisogno di driver USB pubblicati appositamente dal produttore del dispositivo. Alcuni collegamenti per i produttori più comuni sono inclusi in questa guida: [http://developer.android.com/tools/extras/oem-usb.html](http://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternative

A seconda fabbricante, può essere difficile da rilevare il driver USB esatto Necessito. Sono disponibili alcune alternative per i test di App Android sviluppato Windows incluse usando un emulatore Android o servizi di test esterni. tra cui:

- [Test di App Center](https://docs.microsoft.com/appcenter/test-cloud/) - test vengono eseguiti in centinaia di dispositivi Android reale i servizi Cloud.

- [Visual Studio Emulator for Android](https://www.visualstudio.com/en-us/features/msft-android-emulator-vs.aspx)

- [Il debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md)

