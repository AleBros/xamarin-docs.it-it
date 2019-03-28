---
title: Requisiti e installazione di cartelle di lavoro
description: Questo documento viene descritto come scaricare e installare Xamarin Workbooks, discusse le piattaforme supportate e requisiti di sistema.
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: a1001163d89a9a9cda16a7ee5e644307fcc9875c
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58506980"
---
# <a name="workbooks-installation-and-requirements"></a>Requisiti e installazione di cartelle di lavoro

<a name="install" />

## <a name="download-and-install"></a>Scaricare e installare

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Verificare i [requisiti](#requirements) sotto.
2. Scaricare e installare [cartelle di lavoro di Xamarin per Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi).
3. Avviare [esercitarsi](~/tools/workbooks/workbook.md) con le cartelle di lavoro o provare il [esempi](https://developer.xamarin.com/workbooks)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Verificare i [requisiti](#requirements) sotto.
2. Scaricare e installare [Xamarin Workbooks per Mac](https://dl.xamarin.com/interactive/XamarinInteractive.pkg).
3. Avviare [esercitarsi](~/tools/workbooks/workbook.md) con le cartelle di lavoro o provare il [esempi](https://developer.xamarin.com/workbooks)

-----

## <a name="requirements"></a>Requisiti

#### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 o versione successiva
- **Windows** -Windows 7 o versione successiva (in Internet Explorer 11 o versione successiva e .NET 4.6.1 o versioni successive)

#### <a name="supported-app-platforms"></a>Piattaforme di App supportati

|Piattaforma per App|Supporto del sistema operativo|Note|
|--- |--- |--- |
|Mac|Supportato solo su Mac|
|iOS|Supportato in Mac e Windows|Xamarin. IOS 11.0 e Xcode 9.0 o versione successiva deve essere installato su computer Mac. L'esecuzione di cartelle di lavoro di iOS in Windows richiede un host di compilazione Mac che esegue tutte le funzionalità precedenti e il [simulatore iOS remoto](~/tools/ios-simulator/index.md) installato in Windows.|
|Android|Supportato in Mac e Windows|Deve usare l'emulatore di Google, Visual Studio o Xamarin. Android, con un dispositivo virtuale > = 5.0|
|WPF|Supportato solo in Windows|
|Console (.NET Framework)|Supportato in Mac e Windows|
|Console (.NET Core)|Supportato in Mac e Windows|


## <a name="reporting-bugs"></a>Segnalazione dei bug

Verificare [segnalare i problemi in GitHub][bugs]e includono tutte le informazioni seguenti:

### <a name="log-files"></a>File di registro

Collegare sempre i file di log client di cartelle di lavoro:

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4.x offre inoltre la possibilità di selezionare il file di log in Finder (macOS) o Esplora risorse (Windows) direttamente dal menu principale:

- **Guida > informazioni sul File di Log**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>Percorsi di log per le cartelle di lavoro 1.3 e versioni precedenti:

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>Informazioni sulla versione della piattaforma

È molto utile conoscere i dettagli relativi al sistema operativo e i prodotti Xamarin installati.

Dal menu principale nelle cartelle di lavoro:

* **Guida in linea > copiare le informazioni sulla versione**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>Istruzioni per le cartelle di lavoro 1.3 e versioni precedenti:

Visual Studio per Mac

- **Visual Studio > informazioni su Visual Studio > Mostra dettagli > Copia informazioni**
- Incollare nel report sui bug

Visual Studio

- **Guida > informazioni su Visual Studio > Copia informazioni**
- Inviaci la versione del sistema operativo e che sia in esecuzione Windows a 32 o 64 bit.

### <a name="samples"></a>Esempi

Se è possibile collegare o collegare il **.workbooks** file si verificano problemi con, che potrà aiutare a risolvere i bug più rapidamente.

### <a name="devices"></a>Dispositivi

Se si verificano problemi di connessione cartella di lavoro di Android o iOS e sono già verificati [la pagina sulla risoluzione dei problemi](~/tools/workbooks/troubleshooting/index.md), è necessario conoscere:

- Nome del dispositivo che si sta tentando di connettersi a
- Versione del sistema operativo del dispositivo
- Android: Verificare che si utilizza un x86 dell'emulatore
- Android: Piattaforma emulatore si sta usando? Emulatore Google?
  Emulatore Android di Visual Studio? Xamarin Android Player?
- iOS in Windows: Quale versione di Xamarin REMOTED iOS Simulator è installato (controllare **Aggiungi/Rimuovi programmi** nelle **Pannello di controllo**)?
- iOS in Windows: Specificare anche le informazioni sulla versione della piattaforma per l'host di compilazione Mac
- Il dispositivo dispone di connettività di rete (controllo tramite web browser)?

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>Disinstalla

### <a name="windows"></a>WINDOWS

A seconda del modo in cui è stato acquistato le cartelle di lavoro, potrebbe essere necessario eseguire due procedure di disinstallazione. Controllare entrambi a disinstallare completamente il software.

#### <a name="visual-studio-installer"></a>Programma di installazione di Visual Studio

Se si dispone di Visual Studio 2017, aprire **programma di installazione di Visual Studio**e cercare **singoli componenti** per **Xamarin Workbooks**. Se è selezionata, deselezionarla e quindi fare clic su **Modify** disinstallare.

#### <a name="system-uninstall"></a>Disinstallazione di sistema

Se è installato le cartelle di lavoro manualmente con un programma di installazione scaricato, dovrà essere disinstallato tramite la **App e funzionalità** pagina delle impostazioni di sistema in Windows 10 o tramite **Aggiungi/Rimuovi programmi** nel controllo Pannello nelle versioni precedenti di Windows.

> **Start > Impostazioni > sistema > App e funzionalità**

![](install-images/windows-remove.png "Xamarin Workbooks come indicato nella &quot;app &amp; funzionalità&quot;")

**È comunque deve seguire la procedura per l'installazione di Visual Studio per assicurarsi che le cartelle di lavoro non venga reinstallato insaputa dell'utente.**

<a name="uninstall-macos" />

### <a name="macos"></a>macOS

A partire [1.2.2](https://developer.xamarin.com/releases/interactive/interactive-1.2/), Xamarin Workbooks può essere disinstallato da un terminal eseguendo:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

Il programma di disinstallazione fornisce i dettagli di file e directory saranno rimosse e chiedere conferma prima di procedere.

Passare il `-help` argomento per il `uninstall` script per scenari più avanzati.

Per le versioni precedenti, è necessario rimuovere manualmente quanto segue:

1. Eliminare l'app Workbooks all'indirizzo `"/Applications/Xamarin Workbooks.app"`
2. Eliminare l'app Inspector `"Applications/Xamarin Inspector.app"`
2. Eliminare i componenti aggiuntivi: `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` e `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
3. Eliminare Inspector e i file di supporto qui: `/Library/Frameworks/Xamarin.Interactive.framework` e `/Library/Frameworks/Xamarin.Inspector.framework`

## <a name="downgrading"></a>Il downgrade

L'identificatore del bundle per **Workbooks.app applicazioni/Xamarin** modificata `com.xamarin.Inspector` a `com.xamarin.Workbooks` nella versione 1.4, come Workbooks e Inspector sono ora completamente dividere.

A causa di un bug in programmi di installazione precedente, non è possibile effettuare il downgrade di versioni 1.4 o successive usando i 1.3.2 o programmi di installazione precedenti.

Effettuare il downgrade da 1.4 o versione successiva a 1.3.2 o versioni precedenti:

1. [Disinstallare Workbooks e Inspector manualmente](#uninstall-macos)
2. Eseguire il 1.3.2 o versioni precedenti `.pkg` programma di installazione
