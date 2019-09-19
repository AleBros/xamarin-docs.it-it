---
title: Installazione di Novell in Visual Studio 2019
description: Questo documento descrive come installare Novell in Visual Studio 2019. Illustra i requisiti, il processo di installazione e la verifica dell'installazione.
ms.prod: xamarin
ms.assetid: E20D4463-368E-4B60-A059-F50DB8C5552D
author: conceptdev
ms.author: crdun
ms.date: 08/28/2018
ms.openlocfilehash: 970985e419c5710699b6bdda49da8b59ef3c6cb6
ms.sourcegitcommit: f324193bcfb1c674e421955cf6ee2a4249d61ac3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71119252"
---
# <a name="installing-xamarin-in-visual-studio-2019"></a>Installazione di Novell in Visual Studio 2019

<a name="requirements" />

Verificare i [requisiti di sistema](~/cross-platform/get-started/requirements.md) prima di iniziare.

## <a name="installation"></a>Installazione

[!include[](~/cross-platform/includes/install-xamarin-windows-2019.md)]

In Visual Studio 2019 verificare che Novell sia installato facendo clic **sul menu?** . Se Xamarin è installato, verrà visualizzata una voce di menu **Xamarin**, come in questo screenshot:

![Voce di menu Xamarin nel menu ?](windows-images/12-xamarin-menu-item.png "Voce di menu Xamarin nel menu ?")

È anche possibile fare clic su **? > informazioni su Microsoft Visual Studio** e scorrere l'elenco dei prodotti installati per verificare se Xamarin è installato:

![Schermata dei prodotti installati di Visual Studio 2019](windows-images/13-xamarin-is-installed.png "Schermata dei prodotti installati di Visual Studio 2019")

Per altre informazioni sull'individuazione delle informazioni sulla versione, vedere [Where can I find my version information and logs?](~/cross-platform/troubleshooting/questions/version-logs.md) (Dove è possibile trovare i log e le informazioni sulla versione).

## <a name="next-steps"></a>Passaggi successivi

L'installazione di Novell in Visual Studio 2019 consente di iniziare a scrivere il codice per le app, ma richiede una configurazione aggiuntiva per la compilazione e la distribuzione delle app in simulatore, emulatore e dispositivo. Vedere le guide seguenti per completare l'installazione e avviare la compilazione di app multipiattaforma.

### <a name="ios"></a>iOS

Per informazioni più dettagliate, vedere la guida [Installazione di Xamarin.iOS in Windows](~/ios/get-started/installation/windows/index.md). 

1. [Installare Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/installation)
2. [Connettere Visual Studio all'host di compilazione Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
3. [Configurazione per sviluppatori iOS](~/ios/get-started/installation/device-provisioning/index.md) - Richiesto per eseguire l'applicazione nel dispositivo
4. [Simulatore iOS remoto](~/tools/ios-simulator/index.md)
5. [Introduzione a Xamarin.iOS per Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)

### <a name="android"></a>Android

Per informazioni più dettagliate, vedere la guida [Installazione di Xamarin.Android in Windows](~/android/get-started/installation/windows.md).

1. [Configurazione di Xamarin.Android](~/android/get-started/installation/windows.md#configuration)
2. [Using the Xamarin Android SDK Manager](~/android/get-started/installation/android-sdk.md?ide=vs) (Uso di Xamarin Android SDK Manager)
3. [Android SDK Emulator](~/android/get-started/installation/android-emulator/index.md) (Emulatore di Android SDK)
4. [Set Up Device for Development](~/android/get-started/installation/set-up-device-for-development.md) (Configurare il dispositivo per lo sviluppo)
