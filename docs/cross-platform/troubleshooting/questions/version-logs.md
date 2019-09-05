---
title: Dove si possono trovare informazioni sulla versione e i log?
description: Questo documento descrive dove trovare i log e le informazioni sulla versione di Novell. Queste informazioni sono utili per la diagnosi dei problemi, l'invio di bug o il supporto.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: f2d9921795d2a788a6646aad36712a0691c07d50
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291167"
---
# <a name="where-can-i-find-my-version-information-and-logs"></a>Dove si possono trovare informazioni sulla versione e i log?

## <a name="outline"></a>Contorno

- [Informazioni sulla versione](#version-information)
  - Informazioni sulla versione di Windows
  - Informazioni sulla versione Mac
  - Android SDK Tools, strumenti di piattaforma, strumenti di compilazione
- [IDE e log del programma di installazione](#ide-and-installer-logs)
  - [Log di Windows](#windows-logs)
    - Xamarin Studio
    - Xamarin for Visual Studio
    - Programma di installazione universale Novell
    - Programmi `.msi` di installazione singoli, log dettagliati
    - Avvio di Visual Studio, log dettagliati
  - [Log Mac](#mac-logs)
    - Host di compilazione
  - Visual Studio per Mac
    - Xamarin Studio
    - Programma di installazione di Novell
- [Output di compilazione dettagliato](#verbose-build-output-logs)
- [Log di debug per le app Novell. Android e Novell. iOS](#debug-logs-for-xamarin-apps)
  - Log `adb` di Android logcat
  - log del simulatore iOS (su Mac)
  - log del dispositivo iOS (su Mac)

## <a name="a-idversion-information-nameversion-information-version-information"></a><a id="version-information" name="version-information" />Informazioni sulla versione

È in genere consigliabile restituire tutte le informazioni dai pulsanti **copia informazioni** . In caso contrario, spesso è necessario richiedere informazioni aggiuntive. Ad esempio, le versioni del sistema operativo, la versione di Xcode, i livelli di API Android installate e la versione di .NET possono essere tutti importanti quando si aiutano a risolvere un problema.

### <a name="a-idwindows-version-information-namewindows-version-information-windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />Informazioni sulla versione di Windows

#### <a name="xamarin-studio"></a>Xamarin Studio

**> Della guida su > visualizzare i dettagli > le informazioni di copia [pulsante]**

#### <a name="visual-studio"></a>Visual Studio

**> Della guida sulle informazioni sulla copia di Microsoft Visual Studio > [pulsante]**

### <a name="a-idmac-version-information-namemac-version-information-mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />Informazioni sulla versione Mac

#### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

**Visual Studio > informazioni su Visual Studio > visualizzare i dettagli > le informazioni di copia [pulsante]**

### <a name="a-idandroid-sdk-tools-versions-nameandroid-sdk-tools-versions-android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Android SDK Tools, strumenti di piattaforma, strumenti di compilazione

Aprire gestione Android SDK ed eseguire una schermata della sezione degli **strumenti** principale.

#### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

**Strumenti > Open Android SDK Manager**

#### <a name="visual-studio"></a>Visual Studio

**Strumenti > Android > aprire Android SDK Manager...**

## <a name="a-idide-and-installer-logs-nameide-and-installer-logs-ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />IDE e log del programma di installazione

Per ogni posizione di log, assicurarsi di eseguire il comprimere e alleghi l'intera cartella dei log.

### <a name="a-idwindows-logs-namewindows-logs-windows-logs"></a><a id="windows-logs" name="windows-logs" />Log di Windows

#### <a name="a-idwindows-logs-xamarin-vs-namewindows-logs-xamarin-vs--visual-studio-tools-for-xamarin"></a><a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" />Strumenti di Visual Studio per Novell

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="a-idvs-2017-namevs-2017--visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> Visual Studio 2017

[Come ottenere i log di installazione di Visual Studio](https://docs.microsoft.com/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="a-idvs-2015-namevs-2015--visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio 2015

#### <a name="a-idwindows-universal-installer-namewindows-universal-installer--xamarin-universal-installer"></a><a id="windows-universal-installer" name="windows-universal-installer" />Programma di installazione di Novell "Universal"

`%LOCALAPPDATA%\Xamarin\Universal`

Si tratta dei log del `XamarinInstaller.exe` programma di installazione.

#### <a name="a-idindividual-msi-installers-verbose-logs-nameindividual-msi-installers-verbose-logs-individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />Programmi `.msi` di installazione singoli, log dettagliati

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

Riferimento [Opzioni della riga di comando](https://msdn.microsoft.com/library/aa367988.aspx)

#### <a name="a-idvisual-studio-startup-verbose-logs-namevisual-studio-startup-verbose-logs-visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />Avvio di Visual Studio, log dettagliati

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

Riferimento: [/log (devenv. exe)](https://msdn.microsoft.com/library/ms241272.aspx)

### <a name="a-idmac-logs-namemac-logs-mac-logs"></a><a id="mac-logs" name="mac-logs" />Log Mac

È possibile selezionare la voce di menu **vai > Vai alla cartella** in Finder, quindi copiare e incollare uno di questi percorsi nella finestra di dialogo.

#### <a name="a-idmac-logs-visual-studio-namemac-logs-visual-studio-visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio per Mac

`~/Library/Logs/VisualStudio/7.0`(questo numero può variare a seconda della versione in uso)

Questa cartella può essere aperta anche tramite "Help-> Apri directory log".

#### <a name="a-idmac-logs-xamarin-studio-namemac-logs-xamarin-studio-xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0`(questo numero può variare a seconda della versione in uso)

Questa cartella può essere aperta anche tramite "Help-> Apri directory log".

#### <a name="a-idmac-universal-installer-namemac-universal-installer-xamarin-universal-installer"></a><a id="mac-universal-installer" name="mac-universal-installer" />Programma di installazione di Novell "Universal"

`~/Library/Logs/XamarinInstaller/Universal`

Si tratta dei log del `XamarinInstaller.dmg` programma di installazione.

#### <a name="a-idmac-build-host-namemac-build-host-xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />Host di compilazione Novell

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="a-idverbose-build-output-logs-nameverbose-build-output-logs-verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />Output di compilazione dettagliato

1. Abilitare l' [output di MSBuild di diagnostica](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).

2. Per le app iOS, abilitare anche l' **output mTouch dettagliato** aggiungendo `-v -v -v -v` **le proprietà del progetto > compilazione iOS > generale (scheda) > altre opzioni > argomenti aggiuntivi di mTouch**.

3. Pulire e ricompilare il progetto.

4. Copiare e incollare l'output di compilazione dall'IDE in un file di testo.
     - Visual Studio (Windows): **Visualizzare > output > visualizzare l'output di: Compilazione**
     - Visual Studio per Mac: **Visualizza > rilievi > errori > output di compilazione (tabulazione)**

## <a name="a-iddebug-logs-for-xamarin-apps-namedebug-logs-for-xamarin-apps-debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />Log di debug per le app Novell. Android e Novell. iOS

### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

**Visualizza > rilievi > output dell'applicazione**

Si noti che questa voce di menu verrà visualizzata solo dopo l'avvio dell'app.

### <a name="visual-studio"></a>Visual Studio

**Visualizzare > output > visualizzare l'output di: Debug**

### <a name="a-idadb-logcat-nameadb-logcat-android-adbhttpsdeveloperandroidcomtoolshelpadbhtml-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />Log [`adb`](https://developer.android.com/tools/help/adb.html) di Android logcat

Dopo aver eseguito `adb` il comando, alleghi il file **android_logcat. txt** dal desktop. Queste istruzioni presuppongono che sia collegato un solo dispositivo.

Vedere anche la pagina del [log di debug Android](~/android/deploy-test/debugging/android-debug-log.md) .

#### <a name="visual-studio"></a>Visual Studio

1. **Strumenti > Android > avviare il prompt dei comandi ADB di Android**
2. Pulire il registro:`adb logcat -c`
3. Riprodurre il problema.
4. Eseguire l'output del log:`adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1. **Strumenti > aprire il prompt dei comandi Android SDK**
2. Pulire il registro:`adb logcat -c`
3. Riprodurre il problema.
4. Eseguire l'output del log:`adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="a-idios-simulator-logs-nameios-simulator-logs-ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />log del simulatore iOS (su Mac)

- Per accedere al registro di sistema, selezionare **Debug > Apri Registro di sistema** nell'app del simulatore iOS.

- Per visualizzare le segnalazioni di arresti anomali dal simulatore, aprire Console `~/Library/Logs > DiagnosticReports`. app e passare a.

### <a name="a-idios-device-logs-nameios-device-logs-ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />log del dispositivo iOS (su Mac)

#### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

**Visualizza > rilievi > log del dispositivo iOS**

#### <a name="xcode"></a>Xcode

**Dispositivi > finestra > $ {DeviceName}**

Le segnalazioni di arresti anomali sono disponibili nel pulsante **Visualizza log del dispositivo** . Il registro di sistema per il dispositivo viene visualizzato nella parte inferiore della finestra sotto la freccia di divulgazione.

#### <a name="xcode-5"></a>Xcode 5

**Finestra > Libreria > dispositivi (scheda) > $ {DeviceName}**
