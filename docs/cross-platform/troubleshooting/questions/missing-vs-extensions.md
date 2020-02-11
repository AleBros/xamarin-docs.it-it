---
title: Estensioni di Visual Studio mancanti dopo l'installazione
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 066d36a3-e553-48d6-8769-c972274d7641
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 6cd5acb529d617c72d8c9e1bd642a831f90b2e8c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013737"
---
# <a name="missing-visual-studio-extensions-after-installation"></a>Estensioni di Visual Studio mancanti dopo l'installazione

## <a name="error-message-this-project-is-incompatible-with-the-current-edition-of-visual-studio"></a>Messaggio di errore: questo progetto non è compatibile con l'edizione corrente di Visual Studio

Assicurarsi che sia installato Visual Studio 2017 (community, Professional o Enterprise) o versione successiva.

Vedere anche i [requisiti di Windows](~/cross-platform/get-started/requirements.md#windows-requirements).

## <a name="possible-fix-1-change-the-installation-to-make-sure-the-visual-studio-extensions-are-installed"></a>Possibile correzione 1: modificare l'installazione per assicurarsi che le estensioni di Visual Studio siano installate

In alcune situazioni, il programma di installazione di Novell potrebbe deselezionare automaticamente le opzioni di installazione per le estensioni di Visual Studio. Se questa è la causa del problema, installare le estensioni di Visual Studio mancanti usando il comando **Change** del programma di installazione. Ad esempio, per installare le estensioni per Visual Studio 2013:

1. Aprire il pannello **di controllo programmi e funzionalità di** Windows.

2. Fare clic con il pulsante destro del mouse sulla voce **Novell** , quindi scegliere **Cambia**.

3. Fare clic su **Avanti**, quindi su **Cambia**.

4. Verificare che l'opzione **Novell per Visual Studio 2013** sia impostata per l'installazione:

    ![](missing-vs-extensions-images/installer.png "Enable Xamarin for Visual Studio 2013 installation option")

5. Continuare con il resto della procedura guidata del programma di installazione.

## <a name="possible-fix-2-ask-visual-studio-to-set-up-the-extensions-again"></a>Correzione possibile 2: richiedere a Visual Studio di configurare di nuovo le estensioni

1. Controllare se le estensioni Novell sono state copiate nella cartella estensioni di Visual Studio:

    `C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\Extensions\Xamarin\Xamarin\3.1.228.0`

    Se le estensioni sono installate correttamente (per la versione 3.1.228), nella cartella saranno presenti 60 elementi:

    ![](missing-vs-extensions-images/folder.png "List of 'Xamarin\3.1.228.0' folder contents in Explorer")

2. Dopo aver verificato che la cartella sia corretta, indicare a Visual Studio di provare a configurare di nuovo le estensioni:

    `"C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\devenv.exe" /setup`

## <a name="possible-fix-3-try-a-fresh-reinstall-of-xamarin"></a>Possibile correzione 3: provare una nuova reinstallazione di Novell

1. Nel pannello di controllo di Windows disinstallare uno dei seguenti elementi presenti:

    * Xamarin

    * Xamarin per Windows

    * Xamarin.Android

    * Xamarin.iOS

    * Xamarin for Visual Studio

2. In Esplora, eliminare tutti i file rimanenti dalle cartelle delle estensioni di Visual Studio Novell (tutte le versioni, inclusi i file di **programma** e **i file di programma (x86)** ):

    `C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin`

3. Controllare anche la directory "VirtualStore" per verificare se sono presenti copie "sovrapposte" di qualsiasi directory di estensione:

    `%LOCALAPPDATA%\VirtualStore`

4. Aprire l'editor del registro di sistema (regedit).

5. Cercare questa chiave:

    _HKEY\_\_locale MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls_

6. Individuare ed eliminare eventuali voci che corrispondono a questo modello:

    _C:\Programmi\*\Microsoft Visual Studio 1\*. 0 \ Common7\IDE\Extensions\Xamarin_

7. Cercare questa chiave:

    `HKEY\_CURRENT\_USER\Software\Microsoft\VisualStudio\1\*.0\ExtensionManager\PendingDeletions`

8. Eliminare tutte le voci che apparentemente possono essere correlate a Xamarin. Ad esempio, di seguito è riportato un esempio che consente di causare problemi nelle versioni precedenti di Novell:

    _Mono. VisualStudio. Shell, 1.0_

9. Riavviare il computer.

10. Reinstallare la versione stabile corrente di Novell da [VisualStudio.com](https://visualstudio.com/xamarin).

## <a name="possible-fix-4-repair-visual-studio-installation"></a>Possibile correzione 4: ripristinare l'installazione di Visual Studio

1. Aprire il pannello **di controllo programmi e funzionalità di** Windows.

2. Fare clic con il pulsante destro del mouse sulla voce Microsoft Visual Studio pertinente e selezionare **Cambia** .

3. Fare clic sul pulsante **Ripristina** nella finestra di dialogo di Visual Studio che viene visualizzata.
