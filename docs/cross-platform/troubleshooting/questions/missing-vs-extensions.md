---
title: Estensioni di Visual Studio mancanti dopo l'installazione
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 066d36a3-e553-48d6-8769-c972274d7641
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/20/2017
ms.openlocfilehash: 72870b9bf6ff6c3068ee037e6405e4ec03546cd6
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="missing-visual-studio-extensions-after-installation"></a>Estensioni di Visual Studio mancanti dopo l'installazione

## <a name="error-message-this-project-is-incompatible-with-the-current-edition-of-visual-studio"></a>Messaggio di errore: Il progetto è incompatibile con l'edizione corrente di Visual Studio

Verificare che sia installata una versione compatibile di Visual Studio:

-   Visual Studio 2017 (Community, Professional o Enterprise)
-   Visual Studio 2015 (Community, Professional o Enterprise)

Vedere anche il [requisiti di Windows](~/cross-platform/get-started/requirements.md#windows).

## <a name="possible-fix-1-change-the-installation-to-make-sure-the-visual-studio-extensions-are-installed"></a>Possibile correzione 1: Modificare l'installazione per verificare che siano installate le estensioni di Visual Studio

In alcuni casi, il programma di installazione di Xamarin potrebbe automaticamente deselezionare le opzioni di installazione per le estensioni di Visual Studio. Se è la causa del problema, installare le estensioni di Visual Studio mancanti utilizzando il programma di installazione **modifica** comando. Ad esempio, per installare le estensioni per Visual Studio 2013:

1. Aprire le finestre **programmi e funzionalità** Pannello di controllo.

2. Fare clic destro la **Xamarin** voce, quindi selezionare **modifica**.

3. Fare clic su **successivo**, quindi **modifica**.

4. Assicurarsi che il **Xamarin per Visual Studio 2013** opzione è impostata per l'installazione:

    ![](missing-vs-extensions-images/installer.png "Abilitare Xamarin per l'opzione di installazione di Visual Studio 2013")

5. Procedere con il resto dell'installazione guidata.

## <a name="possible-fix-2-ask-visual-studio-to-set-up-the-extensions-again"></a>Possibile correzione 2: Richiedere Visual Studio per impostare nuovamente le estensioni

1. Controllare se le estensioni di Xamarin sono state copiate nella cartella delle estensioni di Visual Studio:

    `C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\Extensions\Xamarin\Xamarin\3.1.228.0`

    Se le estensioni vengono installate correttamente (per la versione 3.1.228), vi sarà 60 elementi nella cartella:


    ![](missing-vs-extensions-images/folder.png "Elenco 'Xamarin\3.1.228.0' del contenuto della cartella in Esplora")

2. Dopo avere verificato che questa cartella è corretta, indicare a Visual Studio per provare a configurare le estensioni di nuovo:

    `"C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\devenv.exe" /setup`

## <a name="possible-fix-3-try-a-fresh-reinstall-of-xamarin"></a>Possibile correzione 3: Provare a reinstallare aggiornata di Xamarin

1.  Dal Pannello di controllo di Windows, disinstallare uno qualsiasi dei seguenti elementi, che sono presenti:

    *   Xamarin

    *   Xamarin per Windows

    *   Xamarin.Android

    *   Xamarin.iOS

    *   Xamarin for Visual Studio

2.  In Esplora, eliminare eventuali file rimanenti dalle cartelle di estensione di Visual Studio a Xamarin (tutte le versioni, incluse entrambe **Program Files** e **programmi (x86)**):

    `C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin`

3.  Controllare inoltre alla directory "VirtualStore" per vedere se potrebbero essere presenti eventuali copie "sovrapposta" di nessuna delle directory di estensione:

    `%LOCALAPPDATA%\VirtualStore`

4.  Aprire l'editor del Registro di sistema (regedit).

5.  Cerca la chiave:

    _HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls_

6.  Individuare ed eliminare tutte le voci che corrispondono a questo modello:

    _C:\Program Files\*\Microsoft Visual Studio 1\*.0\Common7\IDE\Extensions\Xamarin_

7.  Cerca la chiave:

    `HKEY\_CURRENT\_USER\Software\Microsoft\VisualStudio\1\*.0\ExtensionManager\PendingDeletions`

8.  Eliminare tutte le voci che apparentemente potrebbe essere correlati a Xamarin. Ad esempio, ecco che per utilizzare causare problemi nelle versioni precedenti di Xamarin:

    _Mono.VisualStudio.Shell,1.0_

9.  Riavviare il computer.

10.  Reinstallare la versione stabile corrente di Xamarin dal [visualstudio.com](https://visualstudio.com/xamarin).

## <a name="possible-fix-4-repair-visual-studio-installation"></a>Possibile correzione 4: Installazione di ripristinare Visual Studio

1.  Aprire le finestre **programmi e funzionalità** Pannello di controllo.

2.  Fare clic con il pulsante destro la voce rilevante di Microsoft Visual Studio e selezionare **modifica**

3.  Fare clic sui **correzione** pulsante nella finestra di dialogo di Visual Studio che consente di aprire.
