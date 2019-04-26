---
title: Dove si possono trovare informazioni sulla versione e i log?
description: Questo documento descrive la posizione per la ricerca per trovare i log e le informazioni sulla versione di Xamarin. Queste informazioni sono utili quando si diagnosticano problemi, invio di bug, o per ottenere supporto.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: ee4b39aed64d7339bd561cccc49a2959a6daba5c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341321"
---
# <a name="where-can-i-find-my-version-information-and-logs"></a>Dove si possono trovare informazioni sulla versione e i log?

## <a name="outline"></a>Contorno

- [Informazioni sulla versione](#version-information)
    - Informazioni sulla versione di Windows
    - Informazioni sulla versione Mac
    - Strumenti di compilazione di Android SDK Tools, strumenti della piattaforma,
- [Log dell'IDE e programma di installazione](#ide-and-installer-logs)
    - [Log di Windows](#windows-logs)
        - Xamarin Studio
        - Xamarin for Visual Studio
        - Programma di installazione di Xamarin Universal
        - Singoli `.msi` programmi di installazione, i log dettagliati
        - Avvio di Visual Studio, i log dettagliati
    - [Log a Mac](#mac-logs)
        - Host di compilazione
    - Visual Studio per Mac
        - Xamarin Studio
        - Programma di installazione di Xamarin
- [Output di compilazione dettagliati](#verbose-build-output-logs)
- [Eseguire il debug dei log per le app xamarin. Android e xamarin. IOS](#debug-logs-for-xamarin-apps)
    - Android `adb` i log di logcat
    - simulatore iOS accede (Mac)
    - Registra il dispositivo iOS (nel Mac)

## <a name="a-idversion-information-nameversion-information-version-information"></a><a id="version-information" name="version-information" />Informazioni sulla versione

Si tratta in genere migliore per inviare nuovamente tutte le informazioni dal **informazioni sulla copia** pulsanti. In caso contrario, è spesso necessario richiedere informazioni aggiuntive. Ad esempio, le versioni del sistema operativo, versione di Xcode installati i livelli API Android e versione di .NET possa tutti essere importante quando aiutano a risolvere un problema.

### <a name="a-idwindows-version-information-namewindows-version-information-windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />Informazioni sulla versione di Windows

#### <a name="xamarin-studio"></a>Xamarin Studio

**Guida in linea > su > Mostra dettagli > Copia informazioni [pulsante]**

#### <a name="visual-studio"></a>Visual Studio

**Guida > informazioni su Microsoft Visual Studio > Copia informazioni [pulsante]**

### <a name="a-idmac-version-information-namemac-version-information-mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />Informazioni sulla versione Mac

#### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

**Visual Studio > informazioni su Visual Studio > Mostra dettagli > Copia informazioni [pulsante]**

### <a name="a-idandroid-sdk-tools-versions-nameandroid-sdk-tools-versions-android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Strumenti di compilazione di Android SDK Tools, strumenti della piattaforma,

Aprire Android SDK Manager e acquisire uno screenshot della parte superiore **strumenti** sezione.

#### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

**Strumenti > Apri Android SDK Manager**

#### <a name="visual-studio"></a>Visual Studio

**Strumenti > Android > Android SDK Manager aprire...**

## <a name="a-idide-and-installer-logs-nameide-and-installer-logs-ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />Log dell'IDE e programma di installazione

Per ogni log, assicurarsi di comprimere e collegare la cartella dell'intero log.

### <a name="a-idwindows-logs-namewindows-logs-windows-logs"></a><a id="windows-logs" name="windows-logs" />Log di Windows

#### <a name="a-idwindows-logs-xamarin-vs-namewindows-logs-xamarin-vs--visual-studio-tools-for-xamarin"></a><a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" /> Visual Studio Tools per Xamarin

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="a-idvs-2017-namevs-2017--visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> Visual Studio 2017

[Come ottenere i log di installazione di Visual Studio](https://docs.microsoft.com/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="a-idvs-2015-namevs-2015--visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio 2015

#### <a name="a-idwindows-universal-installer-namewindows-universal-installer--xamarin-universal-installer"></a><a id="windows-universal-installer" name="windows-universal-installer" /> Programma di installazione di Xamarin "Universali"

`%LOCALAPPDATA%\Xamarin\Universal`

Questi sono i registri di `XamarinInstaller.exe` programma di installazione.

#### <a name="a-idindividual-msi-installers-verbose-logs-nameindividual-msi-installers-verbose-logs-individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />Singoli `.msi` programmi di installazione, i log dettagliati

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

Informazioni di riferimento: [Opzioni della riga di comando](https://msdn.microsoft.com/library/aa367988.aspx)

#### <a name="a-idvisual-studio-startup-verbose-logs-namevisual-studio-startup-verbose-logs-visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />Avvio di Visual Studio, i log dettagliati

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

Informazioni di riferimento: [/Log (devenv.exe)](https://msdn.microsoft.com/library/ms241272.aspx)

### <a name="a-idmac-logs-namemac-logs-mac-logs"></a><a id="mac-logs" name="mac-logs" />Log a Mac

È possibile selezionare il **Vai > Vai alla cartella** menu item in Finder e quindi copiare e incollare uno qualsiasi di questi percorsi nella finestra di dialogo.

#### <a name="a-idmac-logs-visual-studio-namemac-logs-visual-studio-visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio per Mac

`~/Library/Logs/VisualStudio/7.0` (questo numero può cambiare a seconda della versione in uso)

Questa cartella può essere aperta anche tramite "? -> Apri Directory Log".

#### <a name="a-idmac-logs-xamarin-studio-namemac-logs-xamarin-studio-xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0` (questo numero può cambiare a seconda della versione in uso)

Questa cartella può essere aperta anche tramite "? -> Apri Directory Log".

#### <a name="a-idmac-universal-installer-namemac-universal-installer-xamarin-universal-installer"></a><a id="mac-universal-installer" name="mac-universal-installer" />Programma di installazione di Xamarin "Universali"

`~/Library/Logs/XamarinInstaller/Universal`

Questi sono i registri di `XamarinInstaller.dmg` programma di installazione.

#### <a name="a-idmac-build-host-namemac-build-host-xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />Host di compilazione Xamarin

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="a-idverbose-build-output-logs-nameverbose-build-output-logs-verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />Output di compilazione dettagliati

1.  Abilitare [output di MSBuild di diagnostica](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).

2.  Per le app iOS, abilitare anche **output dettagliato mtouch** aggiungendo `-v -v -v -v` sotto **le proprietà del progetto > compilazione iOS > General (scheda) > Opzioni aggiuntive > argomenti aggiuntivi di mtouch**.

3.  Pulire e ricompilare il progetto.

4.  Copiare e incollare l'output di compilazione dall'IDE in un file di testo.
     - Visual Studio (Windows): **Visualizzazione > Output > Mostra output di: Compilazione**
     - Visual Studio per Mac: **Visualizzazione > riquadri > errori > Output di compilazione (scheda)**

## <a name="a-iddebug-logs-for-xamarin-apps-namedebug-logs-for-xamarin-apps-debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />Eseguire il debug dei log per le app xamarin. Android e xamarin. IOS

### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

**Visualizzazione > riquadri > Output dell'applicazione**

Si noti che questa voce di menu verrà visualizzato solo dopo che l'app è stata avviata.

### <a name="visual-studio"></a>Visual Studio

**Visualizzazione > Output > Mostra output di: Debug**

### <a name="a-idadb-logcat-nameadb-logcat-android-adbhttpsdeveloperandroidcomtoolshelpadbhtml-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />Android [ `adb` ](https://developer.android.com/tools/help/adb.html) i log di logcat

Dopo aver eseguito il `adb` comando, ricollegarli il **android_logcat.txt** file dal Desktop. Queste istruzioni presuppongono che sia un solo dispositivo collegato.

Vedere anche il [Log di Debug Android](~/android/deploy-test/debugging/android-debug-log.md) pagina.

#### <a name="visual-studio"></a>Visual Studio

1. **Strumenti > Android > avviare il prompt dei comandi Adb di Android**
2. Pulire i log: `adb logcat -c`
3. Riprodurre il problema.
4. Output del log: `adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1. **Strumenti > Apri prompt dei comandi di Android SDK**
2. Pulire i log: `adb logcat -c`
3. Riprodurre il problema.
4. Output del log: `adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="a-idios-simulator-logs-nameios-simulator-logs-ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />simulatore iOS accede (Mac)

* Per accedere al Registro di sistema, selezionare **Debug > Apri Log di sistema...**  nell'app per simulatore iOS.

* Per visualizzare i report sugli arresti anomali dal simulatore, aprire Console.app e passare a `~/Library/Logs > DiagnosticReports`.

### <a name="a-idios-device-logs-nameios-device-logs-ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />Registra il dispositivo iOS (nel Mac)

#### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

**Visualizzazione > riquadri > Log del dispositivo iOS**

#### <a name="xcode"></a>Xcode

**Window > Devices > ${DeviceName}**

Report sugli arresti anomali sono disponibili sotto il **Visualizza i log del dispositivo** pulsante. Il Registro di sistema per il dispositivo viene visualizzato nella parte inferiore della finestra sotto la freccia di divulgazione.

#### <a name="xcode-5"></a>Xcode 5

**Finestra > Organizzatore > dispositivi (scheda) > ${DeviceName}**
