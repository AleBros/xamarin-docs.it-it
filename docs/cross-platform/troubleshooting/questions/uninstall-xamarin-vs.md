---
title: Come si esegue una disinstallazione completa di Xamarin per Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: c1742239-05ea-449d-9c99-611e5e5a90e4
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: ed0171c2b6bd98e5b29ec100d0235131d36acb05
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013346"
---
# <a name="how-do-i-perform-a-thorough-uninstall-for-xamarin-for-visual-studio"></a>Come si esegue una disinstallazione completa di Xamarin per Visual Studio?

1. Nel pannello di controllo di Windows disinstallare uno dei seguenti elementi presenti:

    - Xamarin
    - Xamarin per Windows
    - Xamarin.Android
    - Xamarin.iOS
    - Xamarin for Visual Studio

2. In Esplora, eliminare tutti i file rimanenti dalle cartelle delle estensioni di Visual Studio Xamarin (tutte le versioni, inclusi i file di _programma_ e _i file di programma (x86)_ ):

    _C:\\Program Files\*\\Microsoft Visual Studio 1\*. 0\\Common7\\IDE\\Extensions\\Novell_

3. Eliminare anche la directory della cache del componente MEF di Visual Studio:

    _% LOCALAPPDATA%\\Microsoft\\VisualStudio\\1\*. 0\\ComponentModelCache_

    Infatti, questo passaggio è spesso sufficiente per risolvere gli errori, ad esempio:

    - "Il pacchetto ' XamarinShellPackage ' non è stato caricato correttamente"

    - "File di progetto... non è possibile aprire. Manca un sottotipo di progetto "

    - "Riferimento oggetto non impostato su un'istanza di un oggetto.  in Xamarin.VisualStudio. IOS. XamarinIOSPackage. Initialize () "

    - "Errore del sito per il pacchetto" (in Visual Studio _ActivityLog. XML_)

    - "LegacySitePackage non riuscito per il pacchetto" (in _ActivityLog. XML_di Visual Studio)

    Vedere anche l'estensione [Clear MEF Component cache](https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd) di Visual Studio.  Vedere il [Bug 40781, commentare 19](https://bugzilla.xamarin.com/show_bug.cgi?id=40781#c19) per un po' più contesto sul problema upstream in Visual Studio che può causare questi errori.

4. Controllare anche la directory _VirtualStore_ per verificare se Windows potrebbe avere archiviato file overlay per le _estensioni\\directory Novell_ o _ComponentModelCache_ :

    _% LOCALAPPDATA%\\VirtualStore_

5. Aprire l'editor del registro di sistema (`regedit`).

6. Cercare la chiave seguente:

    _HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\SharedDlls_

7. Individuare ed eliminare eventuali voci che corrispondono a questo modello:

    _C:\\Program Files\*\\Microsoft Visual Studio 1\*. 0\\Common7\\IDE\\Extensions\\Novell_

8. Cercare questa chiave:

    _HKEY\_CURRENT\_USER\\software\\Microsoft\\VisualStudio\\1\*. 0\\ExtensionManager\\PendingDeletions_

9. Eliminare tutte le voci che apparentemente possono essere correlate a Xamarin.  Ad esempio, di seguito è riportato un esempio che consente di causare problemi nelle versioni precedenti di Novell:

    _Mono. VisualStudio. Shell, 1.0_

10. Aprire un prompt dei comandi di `cmd.exe` amministratore, quindi eseguire i comandi `devenv /setup` e `devenv /updateconfiguration` per ogni versione installata di Visual Studio.  Ad esempio, per Visual Studio 2015:

    ```
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
    ```

11. Riavviare il computer.

12. Reinstallare la versione stabile corrente di Xamarin usando da [VisualStudio.com](https://visualstudio.com/xamarin/).

## <a name="additional-troubleshooting-steps-for-package-did-not-load-correctly"></a>Procedure aggiuntive per la risoluzione dei problemi per "il pacchetto non è stato caricato correttamente"

Nei casi in cui i passaggi precedenti non risolvono l'errore "il pacchetto non è stato caricato correttamente", ecco alcuni altri passaggi da provare.

1. Creare un nuovo account utente di Windows.

2. Controllare se le estensioni di Visual Studio Xamarin vengono caricate senza errori per il nuovo utente.

3. Se le estensioni vengono caricate correttamente, il problema è probabilmente causato da alcune delle impostazioni archiviate per l'utente originale:

    - **In Esplora risorse** – _% LocalAppData%\\Microsoft\\VisualStudio\\1\*. 0_
    - **In Regedit** – _HKEY\_CURRENT\_USER\\Software\\Microsoft\\VisualStudio\\1\*. 0_
    - **In Regedit** – _HKEY\_CURRENT\_USER\\Software\\Microsoft\\VisualStudio\\1\*. 0\_config_

4. Se le impostazioni archiviate sembrano costituire il problema, è possibile provare a eseguirne il backup e quindi eliminarle.
