---
title: Guida introduttiva a macOS Mojave
description: Questo documento viene descritto come ottenere l'impostazione per compilazione macOS App Mojave con Xamarin.Mac. Illustra come scaricare Xcode 10 e l'aggiornamento di Visual Studio per Mac.
ms.prod: xamarin
ms.assetid: E9A7B68A-E164-4C5C-86AC-B2A3E7A30DA1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: ee5269bf314401328d0184631e817b37ce091479
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067346"
---
# <a name="getting-started-with-macos-mojave"></a>Guida introduttiva a macOS Mojave

![Anteprima](~/media/shared/preview.png)

> [!WARNING]
> MacOS Xamarin supporto Mojave è attualmente in anteprima, il che significa che può contenere bug, non completare funzionalità e potrebbe cambiare.
> Usarlo per esperimenti solo.

> [!NOTE]
> Per altre informazioni, vedere la [note sulla versione](https://releases.xamarin.com/preview-release-xcode-10-beta/) versione di anteprima di Xamarin.

Questo documento viene descritto come ottenere l'impostazione per compilazione macOS App Mojave con Xamarin.Mac. Illustra come scaricare Xcode 10 e l'aggiornamento di Visual Studio per Mac.

## <a name="download-and-install"></a>Scaricare e installare

1. **Installare la versione beta Xcode 10 più recente** : consente agli sviluppatori di Apple registrati è possono scaricare e installare la versione più recente di 10 Xcode dal [portale per sviluppatori Apple](https://developer.apple.com/download/).

   > [!NOTE]
   > MacOS che mojave SDK viene distribuita con Xcode 10.

2. **Eseguire Xcode 10** – eseguire Xcode 10 prima di aggiornare ed eseguire Visual Studio per Mac; installarlo alcuni strumenti che richiede di Xamarin.

3. **L'aggiornamento di Visual Studio per Mac** : seguire le istruzioni il [note sulla versione](https://releases.xamarin.com/preview-release-xcode-10-beta/) per installare l'anteprima di Xamarin.

4. _(facoltativo)_  **Installare la versione beta Mojave macOS più recente nel Mac** : consente di testare le app Xamarin.Mac che utilizzano appena introdotti macOS Mojave API, registrato possono agli sviluppatori di Apple [scaricare](https://developer.apple.com/download/) e installare il macOS Mojave per sviluppatori versione beta più recente.

   > [!TIP]
   > Anche se l'app non utilizza alcun nuovo macOS Mojave API, assicurarsi di compilare con macOS SDK Mojave (installato con Xcode 10) e di testarlo per assicurarsi che tutto funzioni come previsto. Se un'applicazione non chiama eventuali nuove API, è possibile ricompilare con macOS Mojave SDK ed eseguirne il test senza effettuare l'aggiornamento del sistema operativo del Mac.

   > [!IMPORTANT]
   > Prima dell'aggiornamento Mac a macOS Mojave per compilare e testare applicazioni Xamarin.Mac che chiamano macOS nuova API Mojave:
   > - Lettura [note sulla versione di Apple](https://developer.apple.com/download/) per l'aggiornamento del sistema operativo.
   > - Lettura di [note sulla versione](https://releases.xamarin.com/preview-release-xcode-10-beta/) versione di anteprima di Xamarin. Si noti che questa prima versione di anteprima non include le associazioni per macOS nuovo Mojave AppKit APIs (ad esempio la modalità scuro).

## <a name="related-links"></a>Collegamenti correlati

- [Scaricare Xcode 10](https://developer.apple.com/download/)
- Anteprima di Xamarin [note sulla versione](https://releases.xamarin.com/preview-release-xcode-10-beta/)
