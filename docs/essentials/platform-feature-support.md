---
title: Supporto di piattaforme e funzionalità Xamarin.Essentials
description: Xamarin.Essentials offre una singola API multipiattaforma supportata da qualsiasi applicazione iOS, Android o UWP accessibile da codice condiviso, indipendentemente dal modo in cui viene creata l'interfaccia utente.
ms.assetid: 63FA28A5-6F52-4CB7-AF39-8DF7B436B5A4
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
ms.openlocfilehash: 6d0e4df22ed363951759f24cc81f8147df482aef
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78295430"
---
# <a name="platform-support"></a>Supporto per piattaforme

Xamarin.Essentials supporta le piattaforme e i sistemi operativi seguenti:

| Piattaforma | Versione |
| --- | --- |
| Android | 4.4 (API 19) o versione successiva |
| iOS |10.0 o versione successiva |
| Tizen | 4.0 o versione successiva |
| tvOS | 10.0 o versione successiva |
| watchOS | 4.0 o versione successiva |
| UWP | 10.0.16299.0 o versione successiva |

> [!NOTE]
>
> * Tizen è ufficialmente supportato dal team di sviluppo Samsung.
> * tvOS e watchOS hanno una copertura limitata delle API. Per altre informazioni, vedere la guida alle funzionalità.

## <a name="feature-support"></a>Supporto delle funzionalità

Xamarin.Essentials tenta sempre di integrare le funzionalità in ogni piattaforma. Tuttavia, talvolta esistono limitazioni basate sul dispositivo. Di seguito è riportata una guida di quali funzionalità sono supportate su ogni piattaforma.

Guida alle icone:

* ![Supporto completo](~/media/shared/yes.png "supporto completo") - Supporto completo
* ![Supporto limitato](~/media/shared/warn.png "Supporto limitato") - Supporto limitato
* ![Non supportato](~/media/shared/no.png "Non supportate") - Non supportato

| Funzionalità | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| [Accelerometro](accelerometer.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Informazioni sull'app](app-information.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Tema dell'app](app-theme.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Barometro](barometer.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Batteria](battery.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![supporto limitato watchOS](~/media/shared/warn.png "supporto limitato watchOS") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Supporto limitato Tizen](~/media/shared/warn.png "Supporto limitato Tizen") | 
| [Appunti](clipboard.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") | 
| [Convertitori di colori](color-converters.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Bussola](compass.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Connettività](connectivity.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Rileva stretta](detect-shake.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Informazioni sullo schermo del dispositivo](device-display.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") | 
| [Informazioni sul dispositivo](device-information.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Posta elettronica](email.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Helper di file system](file-system-helpers.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Torcia elettrica](flashlight.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Geocodifica](geocoding.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Georilevazione](geolocation.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Giroscopio](gyroscope.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Utilità di avvio](launcher.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Magnetometro](magnetometer.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [MainThread](main-thread.md?content=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Mappe](maps.md?content=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Apri browser](open-browser.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Sensore di orientamento](orientation-sensor.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Autorizzazioni](permissions.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Dialer del telefono](phone-dialer.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Estensioni di piattaforma](platform-extensions.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Preferenze](preferences.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Archiviazione sicura](secure-storage.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Condividi](share.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Sms](sms.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Sintesi vocale](text-to-speech.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Convertitori di unità](unit-converters.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Rilevamento versione](version-tracking.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") | 
| [Vibrare](vibrate.md?context=xamarin/xamarin-forms) | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") |
