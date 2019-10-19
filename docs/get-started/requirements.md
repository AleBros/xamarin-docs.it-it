---
title: Requisiti di Xamarin.Forms
description: Requisiti di piattaforma e sistema di sviluppo per Xamarin.Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2019
ms.openlocfilehash: 46a72534fba7a45323a82ad121e5844410472812
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72584341"
---
# <a name="xamarinforms-requirements"></a>Requisiti di Xamarin.Forms

_Requisiti di piattaforma e sistema di sviluppo per Xamarin.Forms._

Vedere l'articolo [Installazione](installation/index.md) per una panoramica delle procedure di installazione e configurazione per le diverse piattaforme.

## <a name="target-platforms"></a>Piattaforme di destinazione

È possibile scrivere applicazioni Xamarin.Forms per i sistemi operativi seguenti:

- iOS 9 o versione successiva
- Android 4.4 (API 19) o versioni successive ([altri dettagli](#android))
- Piattaforma UWP (Universal Windows Platform) di Windows 10 ([altri dettagli](#windows10))

È tuttavia consigliabile utilizzare Android 5,0 (API 21) come API minima. In questo modo si garantisce la compatibilità completa con tutte le librerie di supporto Android, pur continuando a utilizzare la maggior parte dei dispositivi Android.

Si presuppone che gli sviluppatori abbiano familiarità con [.NET standard](~/cross-platform/app-fundamentals/net-standard.md).

### <a name="additional-platform-support"></a>Supporto di piattaforme aggiuntive

Lo stato di queste piattaforme è disponibile in [Xamarin.Forms GitHub](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support):

- Samsung Tizen
- macOS
- GTK#
- WPF

### <a name="android"></a>Android

È consigliabile installare la versione più recente di Android SDK Tools e della piattaforma API Android. È possibile eseguire l'aggiornamento alle versioni più recenti usando [Android SDK Manager](~/android/get-started/installation/android-sdk.md).

La versione di destinazione/compilazione per i progetti Android **deve** inoltre essere impostata su *Usa la piattaforma installata più recente*. La versione minima può tuttavia essere impostata su API 19 in modo da poter continuare a supportare i dispositivi che usano Android 4.4 e versioni successive. Questi valori vengono impostati in **Opzioni progetto**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

**Opzioni progetto > Applicazione > Proprietà dell'applicazione**

![Opzioni di compilazione Android in Visual Studio](requirements-images/options-android-vs-sml.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

**Compila > Generale**

![Selezionare il Framework di destinazione più recente](requirements-images/options-general-sml.png)

**Compila > Applicazione Android**

![Selezionare le versioni di Android minime e di destinazione per l'app](requirements-images/options-android-sml.png)

-----

## <a name="development-system-requirements"></a>Requisiti del sistema di sviluppo

È possibile sviluppare app Xamarin.Forms in macOS e Windows. Per creare versioni Windows dell'app sono tuttavia necessari Windows e Visual Studio.

## <a name="mac-system-requirements"></a>Requisiti di sistema Mac

È possibile usare Visual Studio per Mac per sviluppare app Novell. Forms in macOS High Sierra (10,13) o versione successiva. Per sviluppare app iOS, è consigliabile usare la versione più recente di Xcode, iOS e macOS. Per i requisiti di versione specifici, vedere le note sulla versione più recenti di [Novell. iOS](/xamarin/ios/release-notes/).

> [!NOTE]
> Non è possibile sviluppare app di Windows in macOS.

## <a name="windows-system-requirements"></a>Requisiti di sistema Windows

È possibile compilare app Xamarin.Forms per iOS e Android in qualsiasi installazione di Windows che supporti lo sviluppo con Xamarin. Per il supporto completo delle funzionalità della piattaforma corrente, usare la versione più recente di Visual Studio. 

Per lo sviluppo di iOS è necessario un Mac in rete con la versione più recente di Xcode e la versione minima di macOS specificata da Apple.

<a name="windows10" />

### <a name="universal-windows-platform-uwp"></a>Piattaforma UWP (Universal Windows Platform)

Lo sviluppo di app Xamarin.Forms per la piattaforma UWP richiede:

- Windows 10 (ultima versione consigliata, Fall Creators Update minimum)

- Visual Studio 2019 consigliato

- [Windows 10 SDK](https://dev.windows.com/downloads/windows-10-sdk)

È possibile [aggiungere un'app UWP (Universal Windows Platform)](~/xamarin-forms/platform/windows/installation/index.md) a una soluzione Xamarin.Forms esistente in qualsiasi momento.

## <a name="deprecated-platforms"></a>Piattaforme deprecate

Queste piattaforme non sono supportate quando si usa Novell. Forms 3,0 o versione successiva:

- *Windows 8.1 / Windows Phone 8.1 WinRT*
- *Windows Phone 8 Silverlight*
