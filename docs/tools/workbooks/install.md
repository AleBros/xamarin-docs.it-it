---
title: Requisiti di installazione e
ms.topic: article
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: dffea94b309b3c945a37a116668486f404f544b3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="installation-and-requirements"></a>Requisiti di installazione e

<script> var inspectorOnLoad = funzione () {var primaryTextBase = "Xamarin le cartelle di lavoro per;" var secondaryTextBase = "o scaricare per"; var inspectorDownloadUrlMac = "https://dl.xamarin.com/interactive/XamarinInteractive.pkg"; var inspectorDownloadUrlWin = " https://DL.xamarin.com/Interactive/XamarinInteractive.msi";

  var aPrimary = document.getElementById("inspector-download-primary"); var aSecondary = document.getElementById("inspector-download-secondary");

  var aMac = aPrimary; var aWin = aSecondary; var macTextBase = primaryTextBase; var winTextBase = secondaryTextBase;

  if (/win/i.test(navigator.platform.toLowerCase())) { aMac = aSecondary; aWin = aPrimary; macTextBase = secondaryTextBase; winTextBase = primaryTextBase; }

  aMac.href = inspectorDownloadUrlMac; aMac.text = macTextBase + " Mac"; aWin.href = inspectorDownloadUrlWin; aWin.text = winTextBase + " Windows"; };

document.addEventListener("DOMContentLoaded", inspectorOnLoad);
</script>

<a name="install" />

## <a name="download-and-install"></a>Scaricare e installare

<ol>
  <li>Controllare il <a href="#Requirements"> requisiti</a> sotto.</li>
  <li>Scaricare e installare <a href="https://dl.xamarin.com/interactive/XamarinInteractive.pkg" id="inspector-download-primary">Xamarin le cartelle di lavoro per Mac</a> (<a href="https://dl.xamarin.com/interactive/XamarinInteractive.msi" id="inspector-download-secondary">o di download per Windows</a>).
  </li>
  <li>Avviare <a href="~/tools/workbooks/workbook.md"> essersi esercitati</a> con cartelle di lavoro o provare il <a href="https://developer.xamarin.com/workbooks/">esempi</a>.
    </li>
</ol>

## <a name="requirements"></a>Requisiti

#### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 o versione successiva
- **Windows** -Windows 7 o versione successiva (con Internet Explorer 11 o versione successiva e .NET 4.6.1 o versione successiva)

#### <a name="supported-app-platforms"></a>Piattaforme di App supportate

<table>
<thead>
  <tr>
    <th>App della piattaforma</th>
    <th>Supporto del sistema operativo</th>
    <th>Note</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>Mac (unificata)</td>
    <td>Supportato solo su Mac</td>
    <td/>
  </tr>
  <tr>
    <td>iOS (unificato)</td>
    <td>Supportato in Mac e Windows</td>
    <td>
      <ul>
        <li>Xamarin. IOS 11.0 e Xcode 9.0 o versione successiva devono essere installati su Mac.</li>
        <li>L'esecuzione di cartelle di lavoro di iOS in Windows richiede un host di compilazione Mac che esegue tutte le precedenti e <a href="~/tools/ios-simulator.md">simulatore iOS remoto</a> installato in Windows.</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>Android</td>
    <td>Supportato in Mac e Windows</td>
    <td>Deve usare l'emulatore di Google, Visual Studio o Xamarin Android, con un dispositivo virtuale > = 5.0</td>
  </tr>
  <tr>
    <td>WPF</td>
    <td>Supportato solo in Windows</td>
    <td/>
  </tr>
  <tr>
    <td>Console (.NET Framework)</td>
    <td>Supportato in Mac e Windows</td>
    <td/>
  </tr>
  <tr>
    <td>Console (.NET Core)</td>
    <td>Supportato in Mac e Windows</td>
    <td/>
  </tr>
</tbody>
</table>

## <a name="reporting-bugs"></a>Segnalazione dei bug

. [Segnala un problema in GitHub][bugs]e includere tutte le informazioni seguenti:

### <a name="log-files"></a>File di log

Collegare sempre il file di log di cartelle di lavoro client:

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4.x offre inoltre la possibilità di selezionare il file di log in Finder (macOS) o Esplora risorse (Windows) direttamente dal menu principale:

- **File di Log Mostra Guida →**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>Percorsi di log per le cartelle di lavoro 1.3 e versioni precedenti:

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>Informazioni sulla versione della piattaforma

È molto utile conoscere i dettagli sul sistema operativo e prodotti Xamarin installati.

Nel menu principale nelle cartelle di lavoro:

* **Le informazioni di versione copia →**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>Istruzioni per le cartelle di lavoro 1.3 e versioni precedenti:

Visual Studio per Mac

- **Visual Studio → su Visual Studio → Mostra dettagli → copia informazioni**
- Incollare in report di bug

Visual Studio

- **Guida in linea → su Visual Studio → copia informazioni**
- Per comunicarci la versione del sistema operativo e che sia in esecuzione Windows a 32 bit o 64 bit.

### <a name="samples"></a>Esempi

Se è possibile collegare o collegare il `.workbooks` file si verificano problemi con, che potrebbe risolvere il bug più rapidamente.

### <a name="devices"></a>Dispositivi

Se si verificano problemi di connessione iOS o Android cartella di lavoro e sono già controllati [la pagina sulla risoluzione dei problemi](~/tools/workbooks/troubleshooting/index.md), è necessario conoscere:

- Nome del dispositivo che si sta tentando di connettersi a
- Versione del sistema operativo del dispositivo
- Android: Verificare che si sta utilizzando un x86 emulatore
- Android: Quale piattaforma emulatore si sta utilizzando? Google emulatore?
  Emulatore Android di Visual Studio? Xamarin Android Player?
- iOS in Windows: la versione del simulatore iOS Xamarin remoto è installato (controllare `Add/Remove Programs` in `Control Panel`)?
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

> **Impostazioni → avvio → sistema → app & funzionalità**

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

L'identificatore bundle per `/Applications/Xamarin Workbooks.app` modificato da `com.xamarin.Inspector` per `com.xamarin.Workbooks` nella versione 1.4 per facilitare un futuro la suddivisione dei programmi di installazione di Xamarin le cartelle di lavoro e controllo.

A causa di un bug nei programmi di installazione precedente, non è possibile effettuare il downgrade versioni 1.4 o versione successive utilizzando il 1.3.2 o programmi di installazione precedente.

Effettuare il downgrade dalla 1.4 o versione successiva a 1.3.2 o precedente:

1. [Disinstallare manualmente le cartelle di lavoro e controllo](#macOS)
2. Eseguire il 1.3.2 precedente o successiva `.pkg` programma di installazione