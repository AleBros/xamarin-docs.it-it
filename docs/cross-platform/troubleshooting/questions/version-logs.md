---
title: Dove posso trovare le informazioni sulla versione e i registri?
description: Questo documento descrive la posizione in cui cercare i motivi per trovare i log e le informazioni sulla versione di Xamarin. Queste informazioni sono utili quando si diagnosticano problemi, invio di bug, o per ottenere supporto.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 0164c5b5cad972b2d8854aefd4403e287a50a6e0
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782050"
---
# <a name="where-can-i-find-my-version-information-and-logs"></a>Dove posso trovare le informazioni sulla versione e i registri?

## <a name="outline"></a>Struttura

- [Informazioni sulla versione](#version-information)
    - Informazioni sulla versione di Windows
    - Informazioni sulla versione Mac
    - Strumenti di compilazione di Android SDK Platform-tools, strumenti
- [Registri IDE e del programma di installazione](#ide-and-installer-logs)
    - [Registri di Windows](#windows-logs)
        - Xamarin Studio
        - Xamarin for Visual Studio
        - Programma di installazione di Xamarin Universal
        - Singoli `.msi` programmi di installazione, i log dettagliati
        - Avvio di Visual Studio, i log dettagliati
    - [Registri Mac](#mac-logs)
        - Host di compilazione
    - Visual Studio per Mac
        - Xamarin Studio
        - Programma di installazione di Xamarin
- [Output di compilazione dettagliato](#verbose-build-output-logs)
- [Eseguire il debug di log per le app xamarin e xamarin](#debug-logs-for-xamarin-apps)
    - Android `adb` i log di logcat
    - simulatore iOS accede (Mac)
    - dispositivo iOS accede (Mac)

## <a name="a-idversion-information-nameversion-information-version-information"></a><a id="version-information" name="version-information" />Informazioni sulla versione

È in genere migliore per inviare tutte le informazioni da eseguire il backup di **copia informazioni** pulsanti. In caso contrario è spesso necessario richiedere informazioni aggiuntive. Ad esempio, versioni del sistema operativo, versione Xcode, installato livelli API Android e versione di .NET può tutti essere importanti quando alla risoluzione dei problemi di un problema.

### <a name="a-idwindows-version-information-namewindows-version-information-windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />Informazioni sulla versione di Windows

#### <a name="xamarin-studio"></a>Xamarin Studio

**Guida > su > Mostra dettagli > Copia informazioni [button]**

#### <a name="visual-studio"></a>Visual Studio

**Guida > informazioni su Microsoft Visual Studio > Copia informazioni [button]**

### <a name="a-idmac-version-information-namemac-version-information-mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />Informazioni sulla versione Mac

#### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

**Visual Studio > informazioni su Visual Studio > Mostra dettagli > Copia informazioni [button]**

### <a name="a-idandroid-sdk-tools-versions-nameandroid-sdk-tools-versions-android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Strumenti di compilazione di Android SDK Platform-tools, strumenti

Aprire Gestione SDK Android e acquisire una schermata dei primi **strumenti** sezione.

![](https://kb.xamarin.com/customer/portal/attachments/337323 "Schermata di Android SDK Manager > cartella degli strumenti")

#### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

**Strumenti > aprire Gestione SDK Android**

#### <a name="visual-studio"></a>Visual Studio

Selezionare l'icona della barra degli strumenti SDK Manager:

![](https://kb.xamarin.com/customer/portal/attachments/420238 "Avviare l'icona della barra degli strumenti di Android SDK Manager in Visual Studio")

## <a name="a-idide-and-installer-logs-nameide-and-installer-logs-ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />Registri IDE e del programma di installazione

Per ogni log, assicurarsi di comprimere e collegare la cartella dell'intero log.

### <a name="a-idwindows-logs-namewindows-logs-windows-logs"></a><a id="windows-logs" name="windows-logs" />Registri di Windows

#### <a name="a-idwindows-logs-xamarin-vs-namewindows-logs-xamarin-vs--visual-studio-tools-for-xamarin"></a><a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" /> Visual Studio Tools per Xamarin

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="a-idvs-2017-namevs-2017--visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> Visual Studio 2017

[Come ottenere i registri di installazione di Visual Studio](https://docs.microsoft.com/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="a-idvs-2015-namevs-2015--visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio 2015

#### <a name="a-idwindows-universal-installer-namewindows-universal-installer--xamarin-universal-installer"></a><a id="windows-universal-installer" name="windows-universal-installer" /> Programma di installazione di Xamarin "Universale"

`%LOCALAPPDATA%\Xamarin\Universal`

Questi sono i registri di `XamarinInstaller.exe` programma di installazione.

#### <a name="a-idindividual-msi-installers-verbose-logs-nameindividual-msi-installers-verbose-logs-individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />Singoli `.msi` programmi di installazione, i log dettagliati

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

Guida di riferimento: [opzioni della riga di comando](http://msdn.microsoft.com/library/aa367988.aspx)

#### <a name="a-idvisual-studio-startup-verbose-logs-namevisual-studio-startup-verbose-logs-visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />Avvio di Visual Studio, i log dettagliati

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

Guida di riferimento: [/Log (devenv.exe)](http://msdn.microsoft.com/library/ms241272.aspx)

### <a name="a-idmac-logs-namemac-logs-mac-logs"></a><a id="mac-logs" name="mac-logs" />Registri Mac

È possibile selezionare il **passare > passare alla cartella** menu elemento in Finder e quindi copiare e incollare uno di questi percorsi nella finestra di dialogo.

#### <a name="a-idmac-logs-visual-studio-namemac-logs-visual-studio-visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio per Mac

`~/Library/Logs/VisualStudio/7.0` (questo numero può variare a seconda della versione in uso)

Questa cartella può essere aperta anche tramite "Help -> Apri Directory di Log".

#### <a name="a-idmac-logs-xamarin-studio-namemac-logs-xamarin-studio-xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0` (questo numero può variare a seconda della versione in uso)

Questa cartella può essere aperta anche tramite "Help -> Apri Directory di Log".

#### <a name="a-idmac-universal-installer-namemac-universal-installer-xamarin-universal-installer"></a><a id="mac-universal-installer" name="mac-universal-installer" />Programma di installazione di Xamarin "Universale"

`~/Library/Logs/XamarinInstaller/Universal`

Questi sono i registri di `XamarinInstaller.dmg` programma di installazione.

#### <a name="a-idmac-build-host-namemac-build-host-xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />Host di compilazione Xamarin

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="a-idverbose-build-output-logs-nameverbose-build-output-logs-verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />Output di compilazione dettagliato

1.  Abilitare [diagnostica output MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).

2.  Per le app iOS, abilitare anche **output dettagliato mtouch** aggiungendo `-v -v -v -v` in **le proprietà del progetto > compilazione iOS > Generale [tab] > Opzioni > mtouch ulteriori argomenti**.

    **Visual Studio (Windows)**

    [![](https://kb.xamarin.com/customer/portal/attachments/337319 "Immettere il trattino quattro v nel campo di input mtouch ulteriori argomenti")](https://kb.xamarin.com/customer/portal/attachments/337319)

    **Visual Studio per Mac**

    [![](https://kb.xamarin.com/customer/portal/attachments/337316 "Immettere il trattino quattro v nel campo di input mtouch ulteriori argomenti")](https://kb.xamarin.com/customer/portal/attachments/337316)

3.  Pulire e ricompilare il progetto.

4.  Copiare e incollare l'output di compilazione dall'IDE in un file di testo.
     - Visual Studio (Windows): **Vista > Output > Mostra output di: compilazione**
     - Visual Studio per Mac: **Vista > Pad > errori > Output di compilazione [tab]**

## <a name="a-iddebug-logs-for-xamarin-apps-namedebug-logs-for-xamarin-apps-debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />Eseguire il debug di log per le app xamarin e xamarin

### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

**Vista > Pad > Output dell'applicazione**

![](https://kb.xamarin.com/customer/portal/attachments/337290 "Icona di riempimento di Output dell'applicazione in Visual Studio per Mac")


Si noti che questa voce di menu verrà visualizzato solo dopo l'applicazione è stata avviata.

### <a name="visual-studio"></a>Visual Studio

**Vista > Output > Mostra output di: eseguire il Debug**

![](https://kb.xamarin.com/customer/portal/attachments/337292 "Riquadro di output con l'opzione di Debug in Visual Studio")

### <a name="a-idadb-logcat-nameadb-logcat-android-adbhttpdeveloperandroidcomtoolshelpadbhtml-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />Android [ `adb` ](http://developer.android.com/tools/help/adb.html) i log di logcat

Dopo aver eseguito la `adb` comando, il collegamento back il **android_logcat.txt** file dal Desktop. Queste istruzioni si supponga un solo dispositivo collegato.

Vedere anche il [Log di Debug Android](~/android/deploy-test/debugging/android-debug-log.md) pagina.

#### <a name="visual-studio"></a>Visual Studio

1.  **Strumenti > Android > avviare prompt dei comandi Adb Android**
2.  Eseguire la pulizia del log: `adb logcat -c`
3.  Riprodurre il problema.
4.  Il log di output: `adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1.  **Strumenti > aprire il prompt dei comandi SDK Android**
2.  Eseguire la pulizia del log: `adb logcat -c`
3.  Riprodurre il problema.
4.  Il log di output: `adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="a-idios-simulator-logs-nameios-simulator-logs-ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />simulatore iOS accede (Mac)

*   Per accedere al Registro di sistema, selezionare **Debug > Apri Registro di sistema...**  nell'app simulatore iOS.

    ![](https://kb.xamarin.com/customer/portal/attachments/382617 "Con l'opzione del Registro di sistema Apri menu Debug")

*   Per visualizzare i report di arresto anomalo del sistema dal simulatore, aprire Console.app e passare a `~/Library/Logs > DiagnosticReports`.

### <a name="a-idios-device-logs-nameios-device-logs-ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />dispositivo iOS accede (Mac)

#### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

**Vista > Pad > Log dispositivo iOS**

#### <a name="xcode"></a>Xcode 

**Finestra > dispositivi > ${DeviceName}**

Segnalazioni di arresti anomali sono disponibili sotto il **Visualizza registri dispositivo** pulsante. Il Registro di sistema per il dispositivo viene visualizzato nella parte inferiore della finestra sotto la freccia di diffusione <img alt="Disclosure arrow" src="https://kb.xamarin.com/customer/portal/attachments/382618" style="width: 15px; height: 12px;" />.

#### <a name="xcode-5"></a>Xcode 5

**Finestra > Libreria > dispositivi [tab] > ${DeviceName}**
