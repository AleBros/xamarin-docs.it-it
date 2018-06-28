---
title: I driver USB necessaria eseguire il debug di Android in Windows
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: ee3f2b1e1ff6a3ac1bec2d73d4af6e740979aa04
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066871"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>I driver USB necessaria eseguire il debug di Android in Windows

## <a name="finding-usb-drivers"></a>Ricerca di driver USB

Per eseguire il debug in un dispositivo Android quando si sviluppa in Windows. è necessario installare un driver USB compatibile. Android SDK Manager include il "Driver USB Google" per impostazione predefinita, che aggiunge il supporto per i dispositivi Nexus come descritto di seguito: [http://developer.android.com/sdk/win-usb.html](http://developer.android.com/sdk/win-usb.html)

Altri dispositivi hanno bisogno di driver USB pubblicati appositamente dal produttore del dispositivo. Alcuni collegamenti per i produttori più comuni sono inclusi in questa guida: [http://developer.android.com/tools/extras/oem-usb.html](http://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternative

A seconda fabbricante, può essere difficile da rilevare il driver USB esatto Necessito. Sono disponibili alcune alternative per i test di App Android sviluppato Windows incluse usando un emulatore Android o servizi di test esterni. tra cui:

- [Test di App Center](https://docs.microsoft.com/appcenter/test-cloud/) - test vengono eseguiti in centinaia di dispositivi Android reale i servizi Cloud.

- [Visual Studio Emulator for Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Il debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md)

