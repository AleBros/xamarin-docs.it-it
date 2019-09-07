---
title: Come si esegue una disinstallazione completa di Xamarin per Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: c1742239-05ea-449d-9c99-611e5e5a90e4
author: conceptdev
ms.author: crdun
ms.date: 12/02/2016
ms.openlocfilehash: 1596e7ed7b3f6d71e13f19a64d111873efb7445c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764943"
---
# <a name="how-do-i-perform-a-thorough-uninstall-for-xamarin-for-visual-studio"></a>Come si esegue una disinstallazione completa di Xamarin per Visual Studio?

1. Nel pannello di controllo di Windows disinstallare uno dei seguenti elementi presenti:

    - Xamarin
    - Xamarin per Windows
    - Xamarin.Android
    - Xamarin.iOS
    - Xamarin for Visual Studio

2. In Esplora, eliminare tutti i file rimanenti dalle cartelle delle estensioni di Visual Studio Novell (tutte le versioni, inclusi i file di _programma_ e _i file di programma (x86)_ ):

    _C:\\Program Files\*MicrosoftVisualStudio1\*. 0Common7\\IDE\\ExtensionsNovell\\\\\\_

3. Eliminare anche la directory della cache del componente MEF di Visual Studio:

    _%LOCALAPPDATA%\\Microsoft\\VisualStudio\\1\*.0\\ComponentModelCache_

    Infatti, questo passaggio è spesso sufficiente per risolvere gli errori, ad esempio:

    - "Il pacchetto ' XamarinShellPackage ' non è stato caricato correttamente"

    - "File di progetto... non è possibile aprire. Manca un sottotipo di progetto "

    - "Riferimento oggetto non impostato su un'istanza di un oggetto.  at Xamarin.VisualStudio.IOS.XamarinIOSPackage.Initialize()"

    - "Errore del sito per il pacchetto" (in Visual Studio _ActivityLog. XML_)

    - "LegacySitePackage non riuscito per il pacchetto" (in _ActivityLog. XML_di Visual Studio)

    Vedere anche l'estensione [Clear MEF Component cache](https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd) di Visual Studio.  Vedere il [Bug 40781, commentare 19](https://bugzilla.xamarin.com/show_bug.cgi?id=40781#c19) per un po' più contesto sul problema upstream in Visual Studio che può causare questi errori.

4. Controllare anche la directory _VirtualStore_ per verificare se Windows potrebbe avere archiviato file sovrapposti per le directory _Extensions\\Novell_ o _ComponentModelCache_ :

    _%LOCALAPPDATA%\\VirtualStore_

5. Aprire l'editor del registro`regedit`di sistema ().

6. Cercare la chiave seguente:

    _HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\SharedDlls_

7. Individuare ed eliminare eventuali voci che corrispondono a questo modello:

    _C:\\Program Files\*MicrosoftVisualStudio1\*. 0Common7\\IDE\\ExtensionsNovell\\\\\\_

8. Cercare questa chiave:

    _HKEY\_CURRENT\_USER\\Software\\Microsoft\\VisualStudio\\1\*.0\\ExtensionManager\\PendingDeletions_

9. Eliminare tutte le voci che apparentemente possono essere correlate a Xamarin.  Ad esempio, di seguito è riportato un esempio che consente di causare problemi nelle versioni precedenti di Novell:

    _Mono.VisualStudio.Shell,1.0_

10. Aprire un prompt `cmd.exe` dei comandi dell'amministratore, quindi eseguire `devenv /setup` i `devenv /updateconfiguration` comandi e per ogni versione installata di Visual Studio.  Ad esempio, per Visual Studio 2015:

    ```
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
    ```

11. Riavviare il computer.

12. Reinstallare la versione stabile corrente di Novell usando da [VisualStudio.com](https://visualstudio.com/xamarin/).

## <a name="additional-troubleshooting-steps-for-package-did-not-load-correctly"></a>Procedure aggiuntive per la risoluzione dei problemi per "il pacchetto non è stato caricato correttamente"

Nei casi in cui i passaggi precedenti non risolvono l'errore "il pacchetto non è stato caricato correttamente", ecco alcuni altri passaggi da provare.

1. Creare un nuovo account utente di Windows.

2. Controllare se le estensioni di Visual Studio Novell vengono caricate senza errori per il nuovo utente.

3. Se le estensioni vengono caricate correttamente, il problema è probabilmente causato da alcune delle impostazioni archiviate per l'utente originale:

    - **In Esplora risorse** - _% LocalAppData\\%\\Microsoft\\VisualStudio\*1.0_
    - **In Regedit** – _HKEY\_software\_utente\\correnteMicrosoftVisualStudio\\1.\*0\\\\_
    - **In Regedit** – _HKEY\_Current\_user\\softwareMicrosoftVisualStudio\\1.0\*config\\\\\__

4. Se le impostazioni archiviate sembrano costituire il problema, è possibile provare a eseguirne il backup e quindi eliminarle.
