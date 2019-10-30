---
title: Installazione e requisiti di Inspector
description: In questo documento viene descritto come installare il Xamarin Inspector e vengono descritti il sistema operativo, gli IDE e le piattaforme app supportati.
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: davidortinau
ms.author: daortin
ms.date: 06/19/2018
ms.openlocfilehash: 19c4a15fb2490c7bace4798b0cb8e062b1379a04
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029700"
---
# <a name="inspector-installation-and-requirements"></a>Installazione e requisiti di Inspector

## <a name="download-and-installation"></a>Download e installazione

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Scaricare e installare [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/) e selezionare il carico **di lavoro sviluppo di applicazioni per dispositivi mobili con .NET** .
1. [Accedere](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio) per abilitare la sottoscrizione Enterprise.
1. [Esamina](~/tools/inspector/inspect.md) la tua app.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Scaricare e installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).
1. [Accedere](https://docs.microsoft.com/visualstudio/mac/activation) per abilitare la sottoscrizione Enterprise.
1. [Esamina](~/tools/inspector/inspect.md) la tua app.

-----

## <a name="requirements"></a>Requisiti

### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10,11 o versione successiva
- **Windows** -Windows 7 o versione successiva (con Internet Explorer 11 o versione successiva e .NET 4.6.1 o versione successiva)

### <a name="supported-ides"></a>IDE supportati

- Visual Studio per Mac
- Visual Studio 2017 con carico di lavoro **sviluppo per dispositivi mobili con .NET**

Per i clienti aziendali è disponibile l'ispezione di app in tempo reale.

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>Piattaforme per le app supportate

|Piattaforma app|Supporto IDE|Note|
|--- |--- |--- |
|Mac|Supportato solo in Visual Studio per Mac|
|iOS|Supportato in Visual Studio 2017 e Visual Studio per Mac| Il comportamento del linker deve essere impostato su **No link** (in **iOS Build** Project Options) |
|Android|Supportato in Visual Studio 2017 e Visual Studio per Mac|Deve avere come destinazione Android > = 4.0.3, con **Fastdev** abilitato.<br />È necessario usare gli emulatori Android di Google, Visual Studio o Novell. Gli emulatori Android 7 potrebbero non consentire l'ispezione al momento.|
|WPF|Supportato solo in Visual Studio 2017|

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>Segnalazione di bug

I bug devono essere segnalati direttamente tramite Visual Studio:

- **Guida > inviare commenti e suggerimenti > segnalare un problema**

Includere tutte le informazioni seguenti:

### <a name="platform-version-information"></a>Informazioni sulla versione della piattaforma

Queste informazioni sono essenziali.

Visual Studio per Mac

- **Visual Studio > informazioni su Visual Studio > visualizzare i dettagli > copiare le informazioni**
- Incolla nel report dei bug

Visual Studio

- **> Della guida su Visual Studio > informazioni sulla copia**
- Indicare la versione del sistema operativo e se si eseguono Windows a 32 bit o a 64 bit.

### <a name="log-files"></a>File di registro

Alleghi sempre i file di log del client IDE e Inspector.

Client Inspector

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4. x offre inoltre la possibilità di selezionare il file di log in Finder (macOS) o in Esplora risorse (Windows) direttamente dal menu principale:

- **Guida > rivelare il file di log**

Visual Studio per Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- Il contenuto del riquadro di **output** di Visual Studio può anche essere informativo.

### <a name="project-settings"></a>Impostazioni progetto

Se è possibile alleghi il **. csproj** per il progetto che si sta tentando di ispezionare, sarebbe estremamente utile. Questa operazione è più semplice che richiedere le singole impostazioni.

Confermare anche la configurazione di debug.

### <a name="selected-devices"></a>Dispositivi selezionati

Per Android e iOS, è fondamentale conoscere il dispositivo su cui si esegue il debug quando si desidera ispezionare. È necessario tenere presente quanto segue:

- Nome del dispositivo come visualizzato nell'IDE
- Versione del sistema operativo del dispositivo
- Android: verificare di usare un emulatore x86
- Android: quale piattaforma emulatore sta usando? Emulatore Google? Emulatore Android di Visual Studio? Xamarin Android Player?
- L'app che si sta eseguendo il debug viene visualizzata correttamente e funziona nel dispositivo?
- Il dispositivo dispone di connettività di rete (controllare tramite Web browser)?

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new
