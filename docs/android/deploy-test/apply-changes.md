---
title: Applicazione di modifiche
description: Come iniziare a utilizzare Applica modifiche nei progetti Xamarin.Android.
ms.assetid: 38950B0C-8880-448F-B12E-08D5BFE87D0D
author: JonDouglas
ms.author: jodou
ms.date: 03/09/2020
ms.openlocfilehash: 03bace97908a53ac6112cd2600b20d429fe2f521
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "80215348"
---
# <a name="apply-changes"></a>Applicazione di modifiche

Applica modifiche consente di eseguire il push delle modifiche delle risorse all'app in esecuzione senza riavviarla. In questo modo puoi controllare la quantità di riavvio dell'app quando vuoi distribuire e testare piccole modifiche incrementali mantenendo lo stato corrente del dispositivo o dell'emulatore.

Applica modifiche usa funzionalità [nell'implementazione JVMTI di Android](https://docs.oracle.com/javase/8/docs/platform/jvmti/jvmti.html#bci) che è supportata nei dispositivi o negli emulatori che eseguono Android 8.0 (livello API 26) o versione successiva.Apply Changes uses capabilities in the Android JVMTI implementation which is supported on devices or emulators running Android 8.0 (API level 26) or higher.

## <a name="requirements"></a>Requisiti

Nell'elenco seguente vengono illustrati i requisiti per l'utilizzo di Applica modifiche:

- **Visual Studio** - In Windows, aggiornamento a Visual Studio 2019 versione 16.5 o successiva. In macOS, eseguire l'aggiornamento a Visual Studio 2019 per Mac versione 8.5 o successiva.
- **Xamarin.Android** - Xamarin.Android 10.2 o versione successiva deve essere installato con Visual Studio (Xamarin.Android viene installato automaticamente come parte del carico di lavoro **di sviluppo per dispositivi mobili con .NET** in Windows e installato come parte di Visual Studio per Mac **Installer**).
- **Android SDK** - Android API 28 o versione successiva deve essere installato tramite Android SDK Manager.
- **Dispositivo di destinazione o emulatore:** il dispositivo o l'emulatore deve eseguire Android 8.0 (livello API 26) o versione successiva.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="get-started"></a>Introduzione

Per iniziare a usare Applica modifiche, devi assicurarti che un dispositivo o un emulatore esegua Android 8.0 (livello API 26) o versione successiva. Quindi eseguire l'applicazione Android con o senza il debug.

È quindi possibile interagire con Applica modifiche con gli approcci seguenti:You can then interact with Apply Changes with the following approaches:

1. **Icona della barra degli strumenti.** È possibile fare clic sull'icona della barra degli strumenti Applica modifiche per applicare le modifiche al dispositivo o all'emulatore di destinazione.

    [![Icona Applica modifiche - Barra degli strumenti](apply-changes-images/Apply-Changes-Toolbar.png)](apply-changes-images/Apply-Changes-Toolbar.png#lightbox)

2. **Scelta rapida da tastiera.** È possibile utilizzare la scorciatoia da tastiera **Maiusc , Alt e F5** per applicare le modifiche al dispositivo o all'emulatore di destinazione.
3. **dal menu Debug.** È possibile utilizzare la voce di menu **Debug > Applica modifiche** per applicare le modifiche al dispositivo o all'emulatore di destinazione.

    [![Applica modifiche - Menu Debug](apply-changes-images/Apply-Changes-Debug-Menu.png)](apply-changes-images/Apply-Changes-Debug-Menu.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

## <a name="get-started"></a>Introduzione

Per iniziare a usare Applica modifiche, devi assicurarti che un dispositivo o un emulatore esegua Android 8.0 (livello API 26) o versione successiva. Quindi eseguire l'applicazione Android con o senza il debug.

È quindi possibile interagire con Applica modifiche con gli approcci seguenti:You can then interact with Apply Changes with the following approaches:

1. **Scelta rapida da tastiera.** Per applicare le modifiche al dispositivo o all'emulatore di destinazione, è possibile usare la scorciatoia da tastiera **.**
2. **dal menu Esegui.** È possibile utilizzare la voce di menu **Esegui > Applica modifiche** per applicare le modifiche al dispositivo o all'emulatore di destinazione.

    [![Applica modifiche - Menu Debug](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png)](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png#lightbox)

-----

## <a name="limitations"></a>Limitazioni

Le modifiche seguenti richiedono il riavvio dell'applicazione:The following changes require an application restart:

- Modifica del codice di C.
- Aggiunta o rimozione di una risorsa.
- Modifica del file AndroidManifest.xml.
- Modifica delle librerie native (file .so).

## <a name="related-links"></a>Collegamenti correlati

- [Applicazione di modifiche](https://developer.android.com/studio/run#apply-changes)
