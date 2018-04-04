---
title: Requisiti e l'installazione di controllo
description: Come scaricare, installare e utilizzare il controllo di Xamarin.
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/29/2017
ms.openlocfilehash: 93f42eef8d76074f6b672bc5a663e8f8f1d2ef80
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="inspector-installation-and-requirements"></a>Requisiti e l'installazione di controllo

## <a name="download-and-installation"></a>Download e installazione


# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Scaricare e installare [Xamarin le cartelle di lavoro e controllo per Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi).
2. [Controllare la propria app.](~/tools/inspector/inspect.md)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Scaricare e installare [Xamarin le cartelle di lavoro e controllo per Mac](https://dl.xamarin.com/interactive/XamarinInteractive.pkg).
2. [Controllare la propria app.](~/tools/inspector/inspect.md)

-----

## <a name="requirements"></a>Requisiti

### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 o versione successiva
- **Windows** -Windows 7 o versione successiva (con Internet Explorer 11 o versione successiva e .NET 4.6.1 o versione successiva)

### <a name="supported-ides"></a>IDE supportati

- Xamarin Studio 6.2 o versione successiva
- Visual Studio per Mac Preview 4 o versione successiva
- Visual Studio 2015 con Xamarin 4.3.x o versione successiva
- Visual Studio 2017 con carico di lavoro di Xamarin

L'ispezione applicazione in tempo reale è disponibile per i clienti aziendali.

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>Piattaforme di App supportate

|App della piattaforma|Supporto IDE|Note|
|--- |--- |--- |
|Mac (unificata)|Supportato solo su Mac|
|iOS (unificato)|Supportato in Visual Studio e XS|Esaminare le app iOS da Windows richiede la stessa versione di controllo per essere installato anche nell'host di compilazione Mac.|
|Android|Supportato in Visual Studio e XS|Deve avere come destinazione Android > = 4.0.3, con **fastdev** abilitato.<br />È necessario usare gli emulatori di Google, Visual Studio o Xamarin Android. Gli emulatori Android di 7 potrebbero non consentire l'ispezione in questo momento.|
|WPF|Supportato solo in Visual Studio in Windows|


<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>Segnalazione dei bug

I bug devono essere segnalati direttamente tramite Visual Studio:

- **Guida > inviare commenti e suggerimenti > segnala un problema**

Includere tutte le informazioni seguenti:

### <a name="platform-version-information"></a>Informazioni sulla versione della piattaforma

Queste informazioni sono essenziale.

Visual Studio per Mac

- **Visual Studio > su Visual Studio > Mostra dettagli > Copia informazioni**
- Incollare in report di bug

Xamarin Studio

- **Xamarin Studio > informazioni su Xamarin Studio > Mostra dettagli > Copia informazioni**
- Incollare in report di bug

Visual Studio

- **Guida > su Visual Studio > Copia informazioni**
- Per comunicarci la versione del sistema operativo e che sia in esecuzione Windows a 32 bit o 64 bit.

### <a name="log-files"></a>File di registro

Collegare sempre IDE e controllo file di log client.

Client di controllo

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4.x offre inoltre la possibilità di selezionare il file di log in Finder (macOS) o Esplora risorse (Windows) direttamente dal menu principale:

- **Guida > Visualizza File di Log**

Visual Studio per Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Xamarin Studio

- `~/Library/Logs/XamarinStudio-6.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- Il contenuto di Visual Studio **Output** riquadro può anche essere informativo.

### <a name="project-settings"></a>Impostazioni progetto

Se è possibile collegare il **csproj** per il progetto che si sta tentando di controllare, sarebbe molto utile. Questo è più semplice che richiede informazioni sulle singole impostazioni.

Verificare inoltre che si trova in una configurazione di Debug.

### <a name="selected-devices"></a>Dispositivi selezionati

Per Android e iOS, è essenziale che si sa quale dispositivo a cui si esegue il debug quando si desidera controllare. È necessario conoscere:

- Nome del dispositivo, come illustrato dell'IDE
- Versione del sistema operativo del dispositivo
- Android: Verificare che si sta utilizzando un x86 emulatore
- Android: Quale piattaforma emulatore si sta utilizzando? Google emulatore? Emulatore Android di Visual Studio? Xamarin Android Player?
- L'app a cui che si esegue il debug correttamente vengono visualizzati e funzione nel dispositivo?
- Il dispositivo dispone di connettività di rete (controllo tramite browser web)?

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>Disinstalla

### <a name="windows"></a>WINDOWS

A seconda di come è stato acquistato le cartelle di lavoro e controllo, è possibile eseguire due procedure di disinstallazione. Controllare entrambi questi per disinstallare completamente il software.

#### <a name="visual-studio-installer"></a>Programma di installazione di Visual Studio

Se si dispone di Visual Studio 2017, aprire **programma di installazione di Visual Studio**ed esaminare **singoli componenti** per **Xamarin le cartelle di lavoro**. Se è selezionata, deselezionarla e quindi fare clic su "Modifica" per disinstallare.

#### <a name="system-uninstall"></a>Disinstallazione di sistema

Se è installato cartelle di lavoro di & controllo manualmente con un programma di installazione scaricato, dovranno essere disinstallate tramite le **App e funzionalità** pagina Impostazioni di sistema in Windows 10 o tramite **Aggiungi/Rimuovi programmi**nel Pannello di controllo nelle versioni precedenti di Windows.

> **Start > Impostazioni > sistema > app & funzionalità**

![](install-images/windows-remove.png "Cartelle di lavoro di Xamarin e controllo elencato nella 'App e funzionalità'")

**È comunque necessario seguire la procedura per il programma di installazione Visual Studio rendere le cartelle di lavoro che & controllo non venga reinstallato insaputa dell'utente.**

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

