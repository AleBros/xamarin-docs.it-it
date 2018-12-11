---
title: Connessione delle anteprime di Visual Studio 2019 in Windows e macOS
description: Questa guida contiene istruzioni su come usare l'anteprima di Visual Studio 2019 in Windows per compilare app iOS, quando si usa l'anteprima di Visual Studio 2019 per Mac su macOS per ospitare le compilazioni.
ms.prod: xamarin
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/04/2018
ms.openlocfilehash: 1eeb79737bd712da64ce34a6b4fe83ce2823e3eb
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899364"
---
# <a name="visual-studio-2019-preview-pairing"></a>Associazione di anteprima a Visual Studio 2019

![Anteprima](~/media/shared/preview.png)

Con l'[anteprima di Visual Studio per Mac 2019](https://docs.microsoft.com/visualstudio/mac/install-preview) è la prima volta che l'installazione side-by-side di Visual Studio è completamente supportata in macOS. Ciò crea una nuova situazione per gli sviluppatori di Windows che compilano applicazioni iOS: se l'host di compilazione Mac ha sia Visual Studio 2017 per Mac (versione 7.x) e l'anteprima di Visual Studio 2019 per Mac (versione 8.0), quale si usa come host di compilazione per l'installazione in Windows?

_Per impostazione predefinita_, la versione stabile &ndash; Visual Studio 2017 per Mac (versione 7.7) &ndash; verrà usata da Visual Studio in Windows a prescindere dal fatto che si usi Visual Studio 2017 o l'anteprima di Visual Studio 2019 in Windows.

Per testare correttamente le anteprime di Visual Studio 2019 sia in Windows che in macOS:

1. Installare e usare l'anteprima di Visual Studio 2019 (versione 16.0) nel computer Windows.
2. Installare l'anteprima di Visual Studio 2019 per Mac (versione 8.0) nel Mac.
3. In **Visual Studio 2019 per Mac**:

    a. Scegliere la voce di menu **Visual Studio > Controlla la disponibilità di aggiornamenti**.

    b. Nella finestra **Visual Studio Update** selezionare **Xamarin Preview** dal canale di aggiornamento. Verrà visualizzato l'avviso seguente:

    > [!WARNING]
    > Provare le build più recenti di Visual Studio 2019 per Mac Preview con il runtime di Mono e gli SDK di Xamarin più aggiornati. **L'installazione delle build da questo canale causa un'interruzione della versione Visual Studio 2017 per Mac.** Usare questo canale solo se si compilano app Xamarin in un sistema operativo Windows usando le versioni Visual Studio 2019 Preview.

    c. Premere il pulsante **Switch channel** (Cambia canale) per abilitare l'host di compilazione di anteprima.

4. Seguire le istruzioni di [Associa a Mac per lo sviluppo di Xamarin.iOS](index.md) e i [suggerimenti per la risoluzione dei problemi](troubleshooting.md), se necessario.