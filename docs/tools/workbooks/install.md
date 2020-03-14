---
title: Installazione e requisiti per le cartelle di lavoro
description: In questo documento viene descritto come scaricare e installare Xamarin Workbooks, illustrando le piattaforme supportate e i requisiti di sistema.
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
author: davidortinau
ms.author: daortin
ms.date: 06/19/2018
ms.openlocfilehash: 92b08a9cfd725c1114b71fad4f29e6f28cd828b3
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305045"
---
# <a name="workbooks-installation-and-requirements"></a>Installazione e requisiti per le cartelle di lavoro

<a name="install" />

## <a name="download-and-install"></a>Scaricare e installare

<!-- markdownlint-disable MD001 -->

# <a name="windows"></a>[Windows](#tab/windows)

1. Verificare i [requisiti](#requirements) indicati di seguito.
2. Scaricare e installare [Xamarin Workbooks per Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi).
3. Inizia a [giocare](~/tools/workbooks/workbook.md) con le cartelle di lavoro.

# <a name="macos"></a>[macOS](#tab/macos)

1. Verificare i [requisiti](#requirements) indicati di seguito.
2. Scaricare e installare [Xamarin Workbooks per Mac](https://dl.xamarin.com/interactive/XamarinInteractive.pkg).
3. Inizia a [giocare](~/tools/workbooks/workbook.md).

-----

## <a name="requirements"></a>Requisiti

#### <a name="supported-operating-systems"></a>Sistemi operativi supportati

- **Mac** -OS X 10,11 o versione successiva
- **Windows** -Windows 7 o versione successiva (con Internet Explorer 11 o versione successiva e .NET 4.6.1 o versione successiva)

#### <a name="supported-app-platforms"></a>Piattaforme per le app supportate

|Piattaforma dell'app|Supporto del sistema operativo|Note|
|--- |--- |--- |
|Mac|Supportato solo in Mac|
|iOS|Supportato in Mac e Windows|È necessario installare Novell. iOS 11,0 e Xcode 9,0 o versione successiva in Mac. L'esecuzione di cartelle di lavoro di iOS in Windows richiede un host di compilazione Mac che esegue tutti i precedenti e il [simulatore iOS remoto](~/tools/ios-simulator/index.md) installato in Windows.|
|Android|Supportato in Mac e Windows|È necessario usare l'emulatore Android di Google, Visual Studio o Novell con un dispositivo virtuale > = 5,0|
|WPF|Supportato solo in Windows|
|Console (.NET Framework)|Supportato in Mac e Windows|
|Console (.NET Core)|Supportato in Mac e Windows|

## <a name="reporting-bugs"></a>Segnalazione di bug

[Segnala i problemi in GitHub][bugs]e Includi tutte le informazioni seguenti:

### <a name="log-files"></a>File di log

Collega sempre i file di log del client di cartelle di lavoro:

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4. x offre inoltre la possibilità di selezionare il file di log in Finder (macOS) o in Esplora risorse (Windows) direttamente dal menu principale:

- **Guida > rivelare il file di log**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>Percorsi dei log per le cartelle di lavoro 1,3 e versioni precedenti:

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>Informazioni sulla versione della piattaforma

È molto utile conoscere i dettagli relativi al sistema operativo e ai prodotti Novell installati.

Dal menu principale nelle cartelle di lavoro di:

- **Guida > copiare le informazioni sulla versione**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>Istruzioni per le cartelle di lavoro 1,3 e versioni precedenti:

Visual Studio per Mac

- **Visual Studio > informazioni su Visual Studio > visualizzare i dettagli > copiare le informazioni**
- Incolla nel report dei bug

Visual Studio

- **> Della guida su Visual Studio > informazioni sulla copia**
- Indicare la versione del sistema operativo e se si eseguono Windows a 32 bit o a 64 bit.

### <a name="samples"></a>Samples

Se è possibile collegare o creare un collegamento al file con **estensione Workbooks** in cui si verificano problemi, questo potrebbe contribuire a risolvere il bug più rapidamente.

### <a name="devices"></a>Dispositivi

Se si verificano problemi durante la connessione della cartella di lavoro di iOS o Android e si è già verificata [la pagina di risoluzione dei problemi](~/tools/workbooks/troubleshooting/index.md), è necessario essere a conoscenza di:

- Nome del dispositivo a cui si sta provando a connettersi
- Versione del sistema operativo del dispositivo
- Android: verificare di usare un emulatore x86
- Android: quale piattaforma emulatore sta usando? Emulatore Google?
  Emulatore Android di Visual Studio? Xamarin Android Player?
- iOS in Windows: quale versione del simulatore iOS remoto di Novell è installato (selezionare **Installazione applicazioni** nel **Pannello di controllo**)?
- iOS in Windows: fornire anche le informazioni sulla versione della piattaforma per l'host di compilazione Mac
- Il dispositivo dispone di connettività di rete (controllare tramite Web browser)?

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>Disinstallare

### <a name="windows"></a>WINDOWS

A seconda del modo in cui sono state acquisite le cartelle di lavoro, potrebbe essere necessario eseguire due procedure di disinstallazione. Controllare entrambi per disinstallare completamente il software.

#### <a name="visual-studio-installer"></a>Programma di installazione di Visual Studio

Se si dispone di Visual Studio 2017, aprire **programma di installazione di Visual Studio**ed esaminare i **singoli componenti** per **Xamarin Workbooks**. Se è selezionata, deselezionarla e quindi fare clic su **modifica** per disinstallare.

#### <a name="system-uninstall"></a>Disinstallazione del sistema

Se le cartelle di lavoro sono state installate manualmente con un programma di installazione scaricato, sarà necessario disinstallarle tramite la pagina **app & funzionalità** impostazioni di sistema in Windows 10 o **installazione** applicazioni nel pannello di controllo nelle versioni precedenti di Windows.

> **Avviare > Impostazioni > le app di > di sistema & funzionalità**

![](install-images/windows-remove.png "Xamarin Workbooks as listed in &quot;Apps &amp; features&quot;")

**È comunque necessario seguire la procedura per la Programma di installazione di Visual Studio per assicurarsi che le cartelle di lavoro non vengano reinstallate senza alcuna conoscenza.**

<a name="uninstall-macos" />

### <a name="macos"></a>macOS

A partire da [1.2.2](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/interactive/interactive-1.2.md), Xamarin Workbooks possibile disinstallare da un terminale eseguendo:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

Il disinstallatore definirà in dettaglio i file e le directory che verrà rimosso e chiederà conferma prima di procedere.

Passare l'argomento `-help` allo script `uninstall` per scenari più avanzati.

Per le versioni precedenti, è necessario rimuovere manualmente quanto segue:

1. Eliminare l'app Workbooks all'indirizzo `"/Applications/Xamarin Workbooks.app"`
2. Eliminare l'app Inspector `"Applications/Xamarin Inspector.app"`
3. Eliminare i componenti aggiuntivi: `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` e `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
4. Eliminare Inspector e i file di supporto qui: `/Library/Frameworks/Xamarin.Interactive.framework` e `/Library/Frameworks/Xamarin.Inspector.framework`

## <a name="downgrading"></a>Declassamento

L'identificatore del bundle per le **cartelle di lavoro di/Applications/Xamarin. app** è stato modificato da `com.xamarin.Inspector` a `com.xamarin.Workbooks` nella versione 1,4, perché le cartelle di lavoro e il controllo ora sono completamente divisi.

A causa di un bug nei programmi di installazione precedenti, non è possibile effettuare il downgrade di 1,4 o versioni successive usando i programmi di installazione 1.3.2 o precedenti.

Per eseguire il downgrade da 1,4 o più recente a 1.3.2 o versioni precedenti:

1. [Disinstalla manualmente le cartelle di lavoro & Inspector](#uninstall-macos)
2. Eseguire il programma di installazione di `.pkg` 1.3.2 o versioni precedenti
