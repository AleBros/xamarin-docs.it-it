---
title: Requisiti di Xamarin.Forms
description: Requisiti di piattaforma e sistema di sviluppo per Xamarin.Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/19/2018
ms.openlocfilehash: d2125c1ddaa3edc3e2ee76d8e03e384efdca42c6
ms.sourcegitcommit: 797597d902330652195931dec9ac3e0cc00792c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2018
---
# <a name="xamarinforms-requirements"></a>Requisiti di Xamarin.Forms

_Requisiti di piattaforma e sistema di sviluppo per Xamarin.Forms._

Vedere l'articolo [Installazione](~/cross-platform/get-started/installation/index.md) per una panoramica delle procedure di installazione e configurazione per le diverse piattaforme.

## <a name="target-platforms"></a>Piattaforme di destinazione

È possibile scrivere applicazioni Xamarin.Forms per i sistemi operativi seguenti:

-  iOS 8 o versioni successive
-  Android 4.0.3 (API 15) o versioni successive ([altri dettagli](#android))
-  Piattaforma UWP (Universal Windows Platform) di Windows 10 ([altri dettagli](#windows10))
-  Windows 8.1/Windows Phone 8.1 WinRT ([altri dettagli](#windows))
-  *Silverlight per Windows Phone 8 (DEPRECATO)*

Si presuppone che gli sviluppatori abbiano familiarità con le [librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md) e i [progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md).

<a name="android" />

### <a name="android"></a>Android

È consigliabile installare la versione più recente di Android SDK Tools e della piattaforma API Android. È possibile eseguire l'aggiornamento alle versioni più recenti usando [Android SDK Manager](~/android/get-started/installation/android-sdk.md).

La versione di destinazione/compilazione per i progetti Android **deve** inoltre essere impostata su *Usa la piattaforma installata più recente*. La versione minima può tuttavia essere impostata su API 15 in modo da poter continuare a supportare i dispositivi che usano Android 4.0.3 e versioni successive. Questi valori vengono impostati in **Opzioni progetto**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

**Opzioni progetto > Applicazione > Proprietà dell'applicazione**

![](installation-images/options-android-vs-sml.png "Opzioni di compilazione Android in Visual Studio")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

**Compila > Generale**

![](installation-images/options-general-sml.png "Compila > Generale")

**Compila > Applicazione Android**

![](installation-images/options-android-sml.png "Compila > Applicazione Android")

-----


<a name="windows10" />

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

I progetti UWP di Windows 10 non vengono aggiunti quando si crea una soluzione in macOS. Per istruzioni su come aggiungere questi progetti a una soluzione esistente, vedere [Adding a Universal Windows Platform (UWP) App](~/xamarin-forms/platform/windows/installation/universal.md) (Aggiunta di un'app UWP (Universal Windows Platform)).


<a name="windows" />

### <a name="windows-81--windows-phone-81-winrt"></a>Windows 8.1/Windows Phone 8.1 WinRT

I progetti Windows 8.1/Windows Phone 8.1 WinRT non vengono aggiunti quando si crea una soluzione in macOS. Per istruzioni su come aggiungere questi progetti a una soluzione esistente, vedere [Adding a Windows Phone App](~/xamarin-forms/platform/windows/installation/phone.md) (Aggiunta di un'app di Windows Phone) e [Adding a Windows App](~/xamarin-forms/platform/windows/installation/tablet.md) (Aggiunta di un'app di Windows).


## <a name="development-system-requirements"></a>Requisiti del sistema di sviluppo

È possibile sviluppare app Xamarin.Forms in macOS e Windows. Per creare versioni Windows dell'app sono tuttavia necessari Windows e Visual Studio.

## <a name="mac-system-requirements"></a>Requisiti di sistema Mac

È possibile usare Visual Studio per Mac per sviluppare app Xamarin.Forms in OS X El Capitan (10.11) o versioni successive. Per sviluppare app iOS è consigliabile avere installato almeno iOS 10 SDK e Xcode 8.

> [!NOTE]
>  Non è possibile sviluppare app di Windows in macOS.

## <a name="windows-system-requirements"></a>Requisiti di sistema Windows

È possibile compilare app Xamarin.Forms per iOS e Android in qualsiasi installazione di Windows che supporti lo sviluppo con Xamarin. È richiesto Visual Studio 2017 o versione successiva in esecuzione in Windows 7 o versione successiva. Per lo sviluppo di app iOS è necessario un Mac connesso in rete.

### <a name="universal-windows-platform-uwp"></a>Piattaforma UWP (Universal Windows Platform)

Lo sviluppo di app Xamarin.Forms per la piattaforma UWP richiede:

* Windows 10 (consigliato Fall Creators Update)

* Visual Studio 2017

* [Windows 10 SDK](https://dev.windows.com/downloads/windows-10-sdk)

I progetti UWP sono inclusi nelle soluzioni Xamarin.Forms create in Visual Studio 2015 e Visual Studio 2017.
È anche possibile [aggiungere un'app UWP (Universal Windows Platform)](~/xamarin-forms/platform/windows/installation/universal.md) a una soluzione Xamarin.Forms esistente.

