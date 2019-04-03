---
title: Requisiti di Xamarin.Forms
description: Requisiti di piattaforma e sistema di sviluppo per Xamarin.Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/23/2018
ms.openlocfilehash: 66785fcd4b38f29ca0358e1e8885e6b7da59f5a7
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58854742"
---
# <a name="xamarinforms-requirements"></a>Requisiti di Xamarin.Forms

_Requisiti di piattaforma e sistema di sviluppo per Xamarin.Forms._

Vedere l'articolo [Installazione](installation/index.md) per una panoramica delle procedure di installazione e configurazione per le diverse piattaforme.

## <a name="target-platforms"></a>Piattaforme di destinazione

È possibile scrivere applicazioni Xamarin.Forms per i sistemi operativi seguenti:

- iOS 8 o versioni successive
- Android 4.4 (API 19) o versioni successive ([altri dettagli](#android))
- Piattaforma UWP (Universal Windows Platform) di Windows 10 ([altri dettagli](#windows10))

Si presuppone che gli sviluppatori abbiano familiarità con [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md) e i [progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md).

### <a name="additional-platform-support"></a>Supporto di piattaforme aggiuntive

Lo stato di queste piattaforme è disponibile in [Xamarin.Forms GitHub](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support):

- Samsung Tizen
- macOS
- GTK#
- WPF

### <a name="platforms-from-earlier-versions"></a>Piattaforme da versioni precedenti

Queste piattaforme non sono supportate quando si usa Xamarin.Forms 3.0:

- *Windows 8.1/Windows Phone 8.1 WinRT*
- *Windows Phone 8 Silverlight*

### <a name="android"></a>Android

È consigliabile installare la versione più recente di Android SDK Tools e della piattaforma API Android. È possibile eseguire l'aggiornamento alle versioni più recenti usando [Android SDK Manager](~/android/get-started/installation/android-sdk.md).

La versione di destinazione/compilazione per i progetti Android **deve** inoltre essere impostata su *Usa la piattaforma installata più recente*. La versione minima può tuttavia essere impostata su API 19 in modo da poter continuare a supportare i dispositivi che usano Android 4.4 e versioni successive. Questi valori vengono impostati in **Opzioni progetto**:

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

**Opzioni progetto > applicazione > proprietà dell'applicazione**

![Opzioni di compilazione Android in Visual Studio](requirements-images/options-android-vs-sml.png)

# [<a name="visual-studio-for-mac"></a>Visual Studio per Mac](#tab/macos)

**Compilazione > generale**

![Selezionare il framework di destinazione più recente](requirements-images/options-general-sml.png)

**Compilazione > applicazione Android**

![Selezionare il valore minimo e di destinazione le versioni di Android per la tua app](requirements-images/options-android-sml.png)

-----

## <a name="development-system-requirements"></a>Requisiti del sistema di sviluppo

È possibile sviluppare app Xamarin.Forms in macOS e Windows. Per creare versioni Windows dell'app sono tuttavia necessari Windows e Visual Studio.

## <a name="mac-system-requirements"></a>Requisiti di sistema Mac

È possibile usare Visual Studio per Mac per sviluppare App xamarin. Forms in macOS High Sierra (10.13) o versione successiva. Per sviluppare le app iOS, è consigliabile avere almeno iOS 10 SDK e Xcode 9 installato.

> [!NOTE]
>  Non è possibile sviluppare app di Windows in macOS.

## <a name="windows-system-requirements"></a>Requisiti di sistema Windows

È possibile compilare app Xamarin.Forms per iOS e Android in qualsiasi installazione di Windows che supporti lo sviluppo con Xamarin. È richiesto Visual Studio 2017 o versione successiva in esecuzione in Windows 7 o versione successiva. Per lo sviluppo di app iOS è necessario un Mac connesso in rete.

<a name="windows10" />

### <a name="universal-windows-platform-uwp"></a>Piattaforma UWP (Universal Windows Platform)

Lo sviluppo di app Xamarin.Forms per la piattaforma UWP richiede:

- Windows 10 (versione più recente, consigliato Fall Creators Update minimo)

- Visual Studio 2019 consigliato (Visual Studio 2017 versione 15.8 minimo)

- [Windows 10 SDK](https://dev.windows.com/downloads/windows-10-sdk)

È possibile [aggiungere un'app UWP (Universal Windows Platform)](~/xamarin-forms/platform/windows/installation/index.md) a una soluzione Xamarin.Forms esistente in qualsiasi momento.
