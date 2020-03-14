---
title: Piattaforme supportate da Xamarin.Forms
description: Requisiti di piattaforma e sistema di sviluppo per Xamarin.Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2020
ms.openlocfilehash: 5751983bf4a818e8ee91af98b0c05658efaca8b2
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304275"
---
# <a name="xamarinforms-supported-platforms"></a>Piattaforme supportate da Xamarin.Forms

È possibile scrivere applicazioni Xamarin.Forms per i sistemi operativi seguenti:

- iOS 9 o versione successiva.
- Android 4.4 (API 19) o versione successiva ([altri dettagli](#android-platform-support)). È tuttavia consigliabile usare Android 5.0 (API 21) come API minima, perché garantisce la compatibilità completa con tutte le librerie di supporto Android, pur continuando ad avere come destinazione la maggior parte dei dispositivi Android.
- Piattaforma UWP (Universal Windows Platform) per Windows 10.

Le app Xamarin.Forms per iOS, Android e la piattaforma UWP (Universal Windows Platform) possono essere compilate in Visual Studio. Per lo sviluppo iOS è tuttavia necessario un Mac connesso alla rete con la versione più recente di Xcode e la versione minima di macOS specificata da Apple. Per altre informazioni, vedere [Requisiti Windows](~/cross-platform/get-started/requirements.md#windows-requirements).

Le app Xamarin.Forms per iOS e Android possono essere compilate in Visual Studio per Mac. Per altre informazioni, vedere [Requisiti macOS](~/cross-platform/get-started/requirements.md#macos-requirements).

> [!NOTE]
> Lo sviluppo di app con Xamarin.Forms richiede familiarità con [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="additional-platform-support"></a>Supporto di piattaforme aggiuntive

Xamarin.Forms supporta altre piattaforme oltre a iOS, Android e Windows:

- Samsung Tizen
- macOS
- GTK#
- WPF

Lo stato di queste piattaforme è disponibile nella [wiki di GitHub sul supporto delle piattaforme di Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

## <a name="android-platform-support"></a>Supporto della piattaforma Android

È consigliabile installare la versione più recente di Android SDK Tools e della piattaforma API Android. È possibile eseguire l'aggiornamento alle versioni più recenti usando [Android SDK Manager](~/android/get-started/installation/android-sdk.md).

La versione di destinazione/compilazione per i progetti Android **deve** inoltre essere impostata su *Usa la piattaforma installata più recente*. La versione minima può tuttavia essere impostata su API 19 in modo da poter continuare a supportare i dispositivi che usano Android 4.4 e versioni successive. Questi valori vengono impostati in **Opzioni progetto**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

**Opzioni progetto > Applicazione > Proprietà dell'applicazione**

![Opzioni di compilazione Android in Visual Studio](requirements-images/options-android-vs-sml.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

**Compila > Generale**

![Selezionare il framework di destinazione più recente](requirements-images/options-general-sml.png)

**Compila > Applicazione Android**

![Selezionare le versioni di Android minima e di destinazione per l'app](requirements-images/options-android-sml.png)

-----

## <a name="deprecated-platforms"></a>Piattaforme deprecate

Queste piattaforme non sono supportate quando si usa Xamarin.Forms 3.0 o versione successiva:

- *Windows 8.1 / Windows Phone 8.1 WinRT*
- *Windows Phone 8 Silverlight*
