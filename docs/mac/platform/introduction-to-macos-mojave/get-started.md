---
title: Introduzione a macOS Mojave
description: Questo documento descrive come ottenere l'impostazione per compilazione macOS Mojave App con xamarin. Mac. Illustra come scaricare Xcode 10 e l'aggiornamento di Visual Studio per Mac.
ms.prod: xamarin
ms.assetid: E9A7B68A-E164-4C5C-86AC-B2A3E7A30DA1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: ee5269bf314401328d0184631e817b37ce091479
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831345"
---
# <a name="getting-started-with-macos-mojave"></a>Introduzione a macOS Mojave

![Anteprima](~/media/shared/preview.png)

> [!WARNING]
> MacOS di Xamarin supporto Mojave è attualmente in anteprima, il che significa che può contenere bug, non è di funzionalità di completamento e può cambiare.
> Usarlo solo come riferimento.

> [!NOTE]
> Per altre informazioni, vedere la [note sulla versione](https://releases.xamarin.com/preview-release-xcode-10-beta/) versione di anteprima di Xamarin.

Questo documento descrive come ottenere l'impostazione per compilazione macOS Mojave App con xamarin. Mac. Illustra come scaricare Xcode 10 e l'aggiornamento di Visual Studio per Mac.

## <a name="download-and-install"></a>Scaricare e installare

1. **Installare la versione beta più recente di Xcode 10** : consente agli sviluppatori di Apple registrati è possono scaricare e installare la versione più recente di Xcode 10 dal [portale Apple Developer](https://developer.apple.com/download/).

   > [!NOTE]
   > MacOS che mojave SDK viene distribuito con Xcode 10.

2. **Eseguire Xcode 10** – eseguire Xcode 10 prima dell'aggiornamento e l'esecuzione di Visual Studio per Mac; Installa alcuni strumenti che richiede Xamarin.

3. **Aggiornare Visual Studio per Mac** : seguire le istruzioni nel [note sulla versione](https://releases.xamarin.com/preview-release-xcode-10-beta/) per installare l'anteprima di Xamarin.

4. _(facoltativo)_  **Installare la versione beta più recente Mojave macOS nel Mac** : consente di testare le app xamarin. Mac che usano macOS appena introdotta Mojave API, registrato consente agli sviluppatori di Apple [scaricare](https://developer.apple.com/download/) e installare il macOS Mojave per sviluppatori versione beta più recente.

   > [!TIP]
   > Anche se l'app non usa alcun nuovo macOS Mojave API, assicurarsi di compilare la soluzione con macOS SDK Mojave (installato con 10 Xcode) e testarla per assicurarsi che tutto funzioni come previsto. Se un'app non chiama eventuali nuove API, è possibile ricompilarlo con macOS SDK Mojave e testarla senza effettuare l'aggiornamento del sistema operativo del Mac.

   > [!IMPORTANT]
   > Prima di aggiornare il computer Mac per macOS Mojave per compilare e testare le applicazioni xamarin. Mac che chiamano macOS nuovo Mojave API:
   > - Lettura [note sulla versione di Apple](https://developer.apple.com/download/) per l'aggiornamento del sistema operativo.
   > - Leggere il [note sulla versione](https://releases.xamarin.com/preview-release-xcode-10-beta/) versione di anteprima di Xamarin. Si noti che questa prima versione di anteprima non include le associazioni per macOS nuovo Mojave AppKit APIs (ad esempio la modalità scuro).

## <a name="related-links"></a>Collegamenti correlati

- [Scarica Xcode 10](https://developer.apple.com/download/)
- Anteprima di Xamarin [note sulla versione](https://releases.xamarin.com/preview-release-xcode-10-beta/)
