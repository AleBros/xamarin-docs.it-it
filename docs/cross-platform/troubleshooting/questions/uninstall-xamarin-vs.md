---
title: Come effettuare una completa disinstallazione di Xamarin per Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: c1742239-05ea-449d-9c99-611e5e5a90e4
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: 99fde9330498ee62d3cf6b5910c2cbfae39cfdeb
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="how-do-i-perform-a-thorough-uninstall-for-xamarin-for-visual-studio"></a>Come effettuare una completa disinstallazione di Xamarin per Visual Studio?


1.  Dal Pannello di controllo di Windows, disinstallare uno qualsiasi dei seguenti elementi, che sono presenti:

    -   Xamarin
    -   Xamarin per Windows
    -   Xamarin.Android
    -   Xamarin.iOS
    -   Xamarin for Visual Studio

2.  In Esplora, eliminare eventuali file rimanenti dalle cartelle di estensione di Visual Studio a Xamarin (tutte le versioni, incluse entrambe _Program Files_ e _programmi (x86)_):

    _C:\\file di programma\*\\Microsoft Visual Studio 1\*,0\\Common7\\IDE\\estensioni\\Xamarin_

3.  Eliminare la directory della cache di Visual Studio MEF componente anche:

    _%LOCALAPPDATA%\\Microsoft\\VisualStudio\\1\*.0\\ComponentModelCache_

    Questo passaggio autonomamente in effetti, è in genere sufficiente per risolvere gli errori, ad esempio:

    -   "Il pacchetto 'XamarinShellPackage' caricamento non corretto"

    -   "Il file di progetto... non può essere aperto. Si è un sottotipo di progetto mancante"

    -   "Riferimento oggetto non è impostata su un'istanza di un oggetto.  at Xamarin.VisualStudio.IOS.XamarinIOSPackage.Initialize()"

    -   "SetSite non riuscita per il pacchetto" (in Visual Studio _XML_)

    -   "LegacySitePackage non riuscita per il pacchetto" (in Visual Studio _XML_)

    (Vedere anche il [Cancella Cache componente MEF](https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd) estensione di Visual Studio.  Vedremo [Bug 40781, 19 commento](https://bugzilla.xamarin.com/show_bug.cgi?id=40781#c19) per un po' più contesto sul problema upstream che possono causare questi errori in Visual Studio.)

4.  Archivia anche i _VirtualStore_ directory per verificare se Windows potrebbe essere archiviato qualsiasi sovrapposizione file per il _estensioni\\Xamarin_ o _ComponentModelCache_directory non esiste:

    _% LOCALAPPDATA %\\VirtualStore_

5.  Aprire l'editor del Registro di sistema (`regedit`).

6.  Cerca la chiave seguente:

    _HKEY\_locale\_macchina\\SOFTWARE\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\SharedDlls_

7.  Individuare ed eliminare eventuali voci che corrispondono a questo modello:

    _C:\\file di programma\*\\Microsoft Visual Studio 1\*,0\\Common7\\IDE\\estensioni\\Xamarin_

8.  Cercare questa chiave:

    _HKEY\_correnti\_utente\\Software\\Microsoft\\VisualStudio\\1\*,0\\ExtensionManager\\PendingDeletions_

9.  Eliminare tutte le voci che apparentemente possono essere correlate a Xamarin.  Ad esempio, ecco che per utilizzare causare problemi nelle versioni precedenti di Xamarin:

    _Mono.VisualStudio.Shell,1.0_

10. Aprire un amministratore `cmd.exe` prompt dei comandi e quindi eseguire il `devenv /setup` e `devenv /updateconfiguration` comandi per ogni versione installata di Visual Studio.  Ad esempio, per Visual Studio 2015:

    ```
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
    ```

11. Riavviare il computer.

12. Reinstallare la versione stabile corrente dell'utilizzo di Xamarin dal [visualstudio.com](https://visualstudio.com/xamarin/).

## <a name="additional-troubleshooting-steps-for-package-did-not-load-correctly"></a>Risoluzione dei problemi per "pacchetto di caricamento non corretto" aggiuntive

Nei casi in cui i passaggi precedenti non risolvono l'errore "pacchetto di caricamento non corretto", ecco alcuni altri passaggi per provare.

1.  Creare un nuovo account utente di Windows.

2.  Controllare se le estensioni di Visual Studio a Xamarin caricata senza errori per il nuovo utente.

3.  Se le estensioni caricano correttamente, il problema è probabilmente causato da alcune delle impostazioni archiviate per l'utente originale:

    -   **In Esplora** – _% LOCALAPPDATA %\\Microsoft\\VisualStudio\\1\*,0_
    -   **In regedit** – _HKEY\_corrente\_utente\\Software\\Microsoft\\VisualStudio\\1\*,0_
    -   **In regedit** – _HKEY\_corrente\_utente\\Software\\Microsoft\\VisualStudio\\1\*,0\_Config_

4.  Se tali impostazioni archiviate effettivamente sembra che il problema, è possibile provare a eseguirne il backup e quindi eliminarli.
