---
title: Estensioni di Visual Studio mancanti dopo l'installazione
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 066d36a3-e553-48d6-8769-c972274d7641
author: asb3993
ms.author: amburns
ms.date: 03/20/2017
ms.openlocfilehash: 7b1f96807d77d9db0a892c5e78124eb3a9890edc
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251093"
---
# <a name="missing-visual-studio-extensions-after-installation"></a>Estensioni di Visual Studio mancanti dopo l'installazione

## <a name="error-message-this-project-is-incompatible-with-the-current-edition-of-visual-studio"></a>Messaggio di errore: Questo progetto è incompatibile con la versione attuale di Visual Studio

Verificare che sia installata una versione compatibile di Visual Studio:

-   Visual Studio 2017 (Community, Professional o Enterprise)
-   Visual Studio 2015 (Community, Professional o Enterprise)

Vedere anche il [requisiti di Windows](~/cross-platform/get-started/requirements.md#windows-requirements).

## <a name="possible-fix-1-change-the-installation-to-make-sure-the-visual-studio-extensions-are-installed"></a>Possibile correzione 1: Modificare l'installazione per assicurare che siano installate le estensioni di Visual Studio

In alcuni casi, il programma di installazione di Xamarin potrebbe automaticamente deselezionare le opzioni di installazione per le estensioni di Visual Studio. Se questa è la causa del problema, installare le estensioni di Visual Studio mancante utilizzando il programma di installazione **modifica** comando. Ad esempio, per installare le estensioni per Visual Studio 2013:

1. Aprire il Windows **programmi e funzionalità** Pannello di controllo.

2. Fare clic il **Xamarin** voce, quindi selezionare **modifica**.

3. Fare clic su **successivo**, quindi **modifica**.

4. Assicurarsi che il **Xamarin per Visual Studio 2013** opzione è impostata per l'installazione:

    ![](missing-vs-extensions-images/installer.png "Abilitare Xamarin per l'opzione di installazione di Visual Studio 2013")

5. Procedere con il resto dell'installazione guidata.

## <a name="possible-fix-2-ask-visual-studio-to-set-up-the-extensions-again"></a>Possibile correzione 2: Chiedere a Visual Studio per configurare nuovamente le estensioni

1. Controllare se le estensioni di Xamarin sono state copiate nella cartella delle estensioni di Visual Studio:

    `C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\Extensions\Xamarin\Xamarin\3.1.228.0`

    Se le estensioni vengono installate in modo corretto (per la versione 3.1.228), vi sarà 60 elementi nella cartella:


    ![](missing-vs-extensions-images/folder.png "Elenco 'Xamarin\3.1.228.0' del contenuto della cartella in Esplora")

2. Dopo avere verificato che in questa cartella è corretta, indicare a Visual Studio per provare a configurare le estensioni nuovamente:

    `"C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\devenv.exe" /setup`

## <a name="possible-fix-3-try-a-fresh-reinstall-of-xamarin"></a>Correzione di possibili 3: Provare una reinstallazione aggiornata di Xamarin

1.  Dal Pannello di controllo di Windows, disinstallare uno qualsiasi dei seguenti elementi, che sono presenti:

    *   Xamarin

    *   Xamarin per Windows

    *   Xamarin.Android

    *   Xamarin.iOS

    *   Xamarin for Visual Studio

2.  In Explorer eliminare eventuali file rimanenti dalle cartelle di estensione Xamarin Visual Studio (tutte le versioni, inclusi i **Program Files** e **i file di programma (x86)**):

    `C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin`

3.  Controllare anche nella directory "VirtualStore" per vedere se è possibile che le relative copie di una delle directory estensione "sovrapposta":

    `%LOCALAPPDATA%\VirtualStore`

4.  Aprire l'editor del Registro di sistema (regedit).

5.  Cercare questa chiave:

    _HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls_

6.  Individuare ed eliminare eventuali voci che corrispondono a questo modello:

    _C:\Program Files\*\Microsoft Visual Studio 1\*.0\Common7\IDE\Extensions\Xamarin_

7.  Cercare questa chiave:

    `HKEY\_CURRENT\_USER\Software\Microsoft\VisualStudio\1\*.0\ExtensionManager\PendingDeletions`

8.  Eliminare tutte le voci che apparentemente possono essere correlate a Xamarin. Ad esempio, ecco che per utilizzare e causare problemi nelle versioni precedenti di Xamarin:

    _Mono.VisualStudio.Shell,1.0_

9.  Riavviare il computer.

10.  Reinstallare la versione stabile corrente di Xamarin da [visualstudio.com](https://visualstudio.com/xamarin).

## <a name="possible-fix-4-repair-visual-studio-installation"></a>Correzione possibili 4: Installazione ripristinare Visual Studio

1.  Aprire il Windows **programmi e funzionalità** Pannello di controllo.

2.  Fare clic con il pulsante destro l'ingresso di Microsoft Visual Studio pertinente e selezionare **modifica**

3.  Fare clic sui **Repair** pulsante nella finestra di dialogo di Visual Studio che si apre.
