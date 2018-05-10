---
title: Requisiti e l'installazione di cartelle di lavoro
description: Come scaricare, installare e utilizzare le cartelle di lavoro di Xamarin.
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 4d3217140605be8567d70e6dcf63d60a02e6b2fb
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="workbooks-installation-and-requirements"></a>Requisiti e l'installazione di cartelle di lavoro

<a name="install" />

## <a name="download-and-install"></a>Scaricare e installare

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Controllare il [requisiti](#requirements) sotto.
2. Scaricare e installare [Xamarin le cartelle di lavoro per Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi).
3. Avviare [essersi esercitati](~/tools/workbooks/workbook.md) con cartelle di lavoro o provare il [esempi](https://developer.xamarin.com/workbooks)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Controllare il [requisiti](#Requirements) sotto.
2. Scaricare e installare [Xamarin le cartelle di lavoro per Mac](https://dl.xamarin.com/interactive/XamarinInteractive.pkg).
3. Avviare [essersi esercitati](~/tools/workbooks/workbook.md) con cartelle di lavoro o provare il [esempi](https://developer.xamarin.com/workbooks)

-----

## <a name="requirements"></a>Requisiti

#### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 o versione successiva
- **Windows** -Windows 7 o versione successiva (con Internet Explorer 11 o versione successiva e .NET 4.6.1 o versione successiva)

#### <a name="supported-app-platforms"></a>Piattaforme di App supportate

|App della piattaforma|Supporto del sistema operativo|Note|
|--- |--- |--- |
|Mac (unificata)|Supportato solo su Mac|
|iOS (unificato)|Supportato in Mac e Windows|Xamarin. IOS 11.0 e Xcode 9.0 o versione successiva devono essere installati su Mac. L'esecuzione di cartelle di lavoro di iOS in Windows richiede un host di compilazione Mac che esegue tutte le precedenti e [simulatore iOS remoto](~/tools/ios-simulator.md) installato in Windows.|
|Android|Supportato in Mac e Windows|Deve usare l'emulatore di Google, Visual Studio o Xamarin Android, con un dispositivo virtuale > = 5.0|
|WPF|Supportato solo in Windows|
|Console (.NET Framework)|Supportato in Mac e Windows|
|Console (.NET Core)|Supportato in Mac e Windows|


## <a name="reporting-bugs"></a>Segnalazione dei bug

. [Segnala un problema in GitHub][bugs]e includere tutte le informazioni seguenti:

### <a name="log-files"></a>File di registro

Collegare sempre il file di log di cartelle di lavoro client:

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4.x offre inoltre la possibilità di selezionare il file di log in Finder (macOS) o Esplora risorse (Windows) direttamente dal menu principale:

- **Guida > Visualizza File di Log**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>Percorsi di log per le cartelle di lavoro 1.3 e versioni precedenti:

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>Informazioni sulla versione della piattaforma

È molto utile conoscere i dettagli sul sistema operativo e prodotti Xamarin installati.

Nel menu principale nelle cartelle di lavoro:

* **Guida > copiare le informazioni sulla versione**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>Istruzioni per le cartelle di lavoro 1.3 e versioni precedenti:

Visual Studio per Mac

- **Visual Studio > su Visual Studio > Mostra dettagli > Copia informazioni**
- Incollare in report di bug

Visual Studio

- **Guida > su Visual Studio > Copia informazioni**
- Per comunicarci la versione del sistema operativo e che sia in esecuzione Windows a 32 bit o 64 bit.

### <a name="samples"></a>Esempi

Se è possibile collegare o collegare il **.workbooks** file si verificano problemi con, che potrebbe risolvere il bug più rapidamente.

### <a name="devices"></a>Dispositivi

Se si verificano problemi di connessione iOS o Android cartella di lavoro e sono già controllati [la pagina sulla risoluzione dei problemi](~/tools/workbooks/troubleshooting/index.md), è necessario conoscere:

- Nome del dispositivo che si sta tentando di connettersi a
- Versione del sistema operativo del dispositivo
- Android: Verificare che si sta utilizzando un x86 emulatore
- Android: Quale piattaforma emulatore si sta utilizzando? Google emulatore?
  Emulatore Android di Visual Studio? Xamarin Android Player?
- iOS in Windows: la versione del simulatore iOS Xamarin remoto è installato (controllare **Aggiungi/Rimuovi programmi** in **Pannello di controllo**)?
- iOS in Windows: anche fornire informazioni sulla versione della piattaforma per l'host di compilazione Mac
- Il dispositivo dispone di connettività di rete (controllo tramite browser web)?

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>Disinstalla

### <a name="windows"></a>WINDOWS

A seconda di come è stato acquistato le cartelle di lavoro e controllo, è possibile eseguire due procedure di disinstallazione. Controllare entrambi questi per disinstallare completamente il software.

#### <a name="visual-studio-installer"></a>Programma di installazione di Visual Studio

Se si dispone di Visual Studio 2017, aprire **programma di installazione di Visual Studio**ed esaminare **singoli componenti** per **Xamarin le cartelle di lavoro**. Se è selezionata, deselezionarla e quindi fare clic su **modifica** per disinstallare.

#### <a name="system-uninstall"></a>Disinstallazione di sistema

Se è installato cartelle di lavoro di & controllo manualmente con un programma di installazione scaricato, dovranno essere disinstallate tramite le **App e funzionalità** pagina Impostazioni di sistema in Windows 10 o tramite **Aggiungi/Rimuovi programmi**nel Pannello di controllo nelle versioni precedenti di Windows.

> **Start > Impostazioni > sistema > app & funzionalità**

![](install-images/windows-remove.png "Cartelle di lavoro di Xamarin e controllo elencato nella &quot;app &amp; funzionalità&quot;")

**È comunque necessario seguire la procedura per il programma di installazione Visual Studio rendere le cartelle di lavoro che & controllo non venga reinstallato insaputa dell'utente.**

<a name="uninstall-macos" />

### <a name="macos"></a>macOS

A partire da [1.2.2](https://developer.xamarin.com/releases/interactive/interactive-1.2/), Xamarin le cartelle di lavoro e controllo possono essere disinstallati da un terminale eseguendo:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

Il programma di disinstallazione illustra in dettaglio i file e directory saranno rimosse e richiedere la conferma prima di procedere.

Passare il `-help` argomento per il `uninstall` script per scenari più avanzati.

Per le versioni precedenti, è necessario rimuovere manualmente quanto segue:

1. Eliminare l'app Workbooks all'indirizzo `"/Applications/Xamarin Workbooks.app"`
2. Eliminare l'app Inspector `"Applications/Xamarin Inspector.app"`
2. Eliminare i componenti aggiuntivi: `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` e `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
3. Eliminare Inspector e i file di supporto qui: `/Library/Frameworks/Xamarin.Interactive.framework` e `/Library/Frameworks/Xamarin.Inspector.framework`

## <a name="downgrading"></a>Downgrade

L'identificatore bundle per **applicazioni/Xamarin Workbooks.app** modificato da `com.xamarin.Inspector` per `com.xamarin.Workbooks` nella versione 1.4 per facilitare un futuro la suddivisione dei programmi di installazione di Xamarin le cartelle di lavoro e controllo.

A causa di un bug nei programmi di installazione precedente, non è possibile effettuare il downgrade versioni 1.4 o versione successive utilizzando il 1.3.2 o programmi di installazione precedente.

Effettuare il downgrade dalla 1.4 o versione successiva a 1.3.2 o precedente:

1. [Disinstallare manualmente le cartelle di lavoro e controllo](#macOS)
2. Eseguire il 1.3.2 precedente o successiva `.pkg` programma di installazione
