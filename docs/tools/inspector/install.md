---
title: Requisiti di installazione e
ms.topic: article
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/29/2017
ms.openlocfilehash: a587935e35882ed1dc68817fbbe1ae3e91200f29
ms.sourcegitcommit: 0bdcd00b64d581d4c5179bc39ded4018c9374229
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="installation-and-requirements"></a>Requisiti di installazione e

<script> var inspectorOnLoad = funzione () {var primaryTextBase = "Le cartelle di lavoro di Xamarin e controllo per"; var secondaryTextBase = "o scaricare per"; var inspectorDownloadUrlMac = "https://dl.xamarin.com/interactive/XamarinInteractive.pkg"; var inspectorDownloadUrlWin = "https://dl.xamarin.com/interactive/XamarinInteractive.msi";

  var aPrimary = document.getElementById("inspector-download-primary"); var aSecondary = document.getElementById("inspector-download-secondary");

  var aMac = aPrimary; var aWin = aSecondary; var macTextBase = primaryTextBase; var winTextBase = secondaryTextBase;

  if (/win/i.test(navigator.platform.toLowerCase())) { aMac = aSecondary; aWin = aPrimary; macTextBase = secondaryTextBase; winTextBase = primaryTextBase; }

  aMac.href = inspectorDownloadUrlMac; aMac.text = macTextBase + " Mac"; aWin.href = inspectorDownloadUrlWin; aWin.text = winTextBase + " Windows"; };

document.addEventListener("DOMContentLoaded", inspectorOnLoad);
</script>

## <a name="download-and-installation"></a>Download e installazione

<ol>
  <li>Scaricare e installare <a href="https://dl.xamarin.com/interactive/XamarinInteractive.pkg" id="inspector-download-primary">Xamarin le cartelle di lavoro e controllo per Mac</a> (<a href="https://dl.xamarin.com/interactive/XamarinInteractive.msi" id="inspector-download-secondary">o di download per Windows</a>).
  </li>
  <li><a href="~/tools/inspector/inspect.md"> Controllare la propria app.</a>
    </li>
</ol>

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

<table>
<thead>
  <tr>
    <th>App della piattaforma</th>
    <th>Supporto IDE</th>
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
    <td>Supportato in Visual Studio e XS</td>
    <td>Esaminare le app iOS da Windows richiede la stessa versione di controllo per essere installato anche nell'host di compilazione Mac.</td>
  </tr>
  <tr>
    <td>Android</td>
    <td>Supportato in Visual Studio e XS</td>
    <td>
      <ul>
        <li>Deve avere come destinazione Android > = 4.0.3</li>
        <li>Deve essere abilitata fastdev</li>
        <li>È necessario usare gli emulatori di Google, Visual Studio o Xamarin Android. Gli emulatori Android di 7 potrebbero non consentire l'ispezione in questo momento.</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>WPF</td>
    <td>Supportato solo in Visual Studio in Windows</td>
    <td/>
  </tr>
</tbody>
</table>

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>Segnalazione dei bug

I bug devono essere segnalati direttamente tramite Visual Studio:

- **Guida in linea → Invia commenti e suggerimenti → segnala un problema**

Includere tutte le informazioni seguenti:

### <a name="platform-version-information"></a>Informazioni sulla versione della piattaforma

Queste informazioni sono essenziale.

Visual Studio per Mac

- **Visual Studio → su Visual Studio → Mostra dettagli → copia informazioni**
- Incollare in report di bug

Xamarin Studio

- **Xamarin Studio, → informazioni su Xamarin Studio → Mostra dettagli → copia informazioni**
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

- **File di Log Mostra Guida →**

Visual Studio per Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Xamarin Studio

- `~/Library/Logs/XamarinStudio-6.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- Il contenuto di Visual Studio `Output` riquadro può anche essere informativo.

### <a name="project-settings"></a>Impostazioni progetto

Se è possibile collegare il `.csproj` per il progetto che si sta tentando di controllare, sarebbe molto utile. Questo è più semplice che richiede informazioni sulle singole impostazioni.

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

Se si dispone di Visual Studio 2017, aprire "Programma di installazione di Visual Studio" e cercare "Xamarin le cartelle di lavoro" in "Singoli componenti". Se è selezionata, deselezionarla e quindi fare clic su "Modifica" per disinstallare.

#### <a name="system-uninstall"></a>Disinstallazione di sistema

Se è installato cartelle di lavoro di & controllo manualmente con un programma di installazione scaricato, dovranno essere disinstallate tramite le **App e funzionalità** pagina Impostazioni di sistema in Windows 10 o tramite **Aggiungi/Rimuovi programmi**nel Pannello di controllo nelle versioni precedenti di Windows.

> **Impostazioni → avvio → sistema → app & funzionalità**

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

