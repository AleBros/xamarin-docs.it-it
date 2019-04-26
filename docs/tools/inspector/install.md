---
title: Requisiti e installazione inspector
description: Questo documento viene descritto come installare Xamarin Inspector e illustra il sistema operativo supportato, ambienti di sviluppo integrato e piattaforme di app.
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 2bbf0bda42b7bce483d9d036ebf39314dcb73072
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61373469"
---
# <a name="inspector-installation-and-requirements"></a>Requisiti e installazione inspector

## <a name="download-and-installation"></a>Download e installazione

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Scaricare e installare [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/) e selezionare il **sviluppo per dispositivi mobili con .NET** carico di lavoro.
1. [Accedi](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio) per abilitare la sottoscrizione Enterprise.
1. [Esaminare](~/tools/inspector/inspect.md) la tua app!

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Scaricare e installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).
1. [Accedi](https://docs.microsoft.com/visualstudio/mac/activation) per abilitare la sottoscrizione Enterprise.
1. [Esaminare](~/tools/inspector/inspect.md) la tua app!

-----

## <a name="requirements"></a>Requisiti

### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 o versione successiva
- **Windows** -Windows 7 o versione successiva (in Internet Explorer 11 o versione successiva e .NET 4.6.1 o versioni successive)

### <a name="supported-ides"></a>IDE supportati

- Visual Studio per Mac
- Visual Studio 2017 con **sviluppo di applicazioni per dispositivi mobili con .NET** carico di lavoro

L'ispezione delle app in tempo reale è disponibile per i clienti aziendali.

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>Piattaforme di App supportati

|Piattaforma per App|Supporto IDE|Note|
|--- |--- |--- |
|Mac|Supportato solo in Visual Studio per Mac|
|iOS|Supportato in Visual Studio 2017 e Visual Studio per Mac| Comportamento del linker deve essere impostato su **non collegare** (sotto **compilazione iOS** opzioni progetto) |
|Android|Supportato in Visual Studio 2017 e Visual Studio per Mac|Deve avere come destinazione Android > = 4.0.3, con **fastdev** abilitata.<br />È necessario usare gli emulatori di Google, Visual Studio o Xamarin. Android. Emulatori Android 7 non possono consentire di ispezione in questo momento.|
|WPF|Supportato solo in Visual Studio 2017|

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>Segnalazione dei bug

I bug devono essere segnalati direttamente tramite Visual Studio:

- **Guida in linea > Invia commenti e suggerimenti > segnala un problema**

Includere tutte le informazioni seguenti:

### <a name="platform-version-information"></a>Informazioni sulla versione della piattaforma

Queste informazioni sono fondamentali.

Visual Studio per Mac

- **Visual Studio > informazioni su Visual Studio > Mostra dettagli > Copia informazioni**
- Incollare nel report sui bug

Visual Studio

- **Guida > informazioni su Visual Studio > Copia informazioni**
- Inviaci la versione del sistema operativo e che sia in esecuzione Windows a 32 o 64 bit.

### <a name="log-files"></a>File di registro

Collegare sempre i file di log client sia IDE sia Inspector.

Client di controllo

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4.x offre inoltre la possibilità di selezionare il file di log in Finder (macOS) o Esplora risorse (Windows) direttamente dal menu principale:

- **Guida > informazioni sul File di Log**

Visual Studio per Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- Il contenuto di Visual Studio **Output** riquadro potrebbe risultare informativo.

### <a name="project-settings"></a>Impostazioni progetto

Se è possibile collegare il **file con estensione csproj** per il progetto che si desidera controllare, sarebbe estremamente utile. Questo è più facile che vuole porre riguardo le singole impostazioni.

Verificare anche che si trova in configurazione di Debug.

### <a name="selected-devices"></a>Dispositivi selezionati

Per Android e iOS, è fondamentale che sappiamo quale dispositivo si esegue il debug quando si desidera esaminare. È necessario conoscere:

- Nome del dispositivo come illustrato nell'IDE
- Versione del sistema operativo del dispositivo
- Android: Verificare che si utilizza un x86 dell'emulatore
- Android: Piattaforma emulatore si sta usando? Emulatore Google? Emulatore Android di Visual Studio? Xamarin Android Player?
- L'app che si esegue il debug in modo corretto vengono visualizzati e funzionare nel dispositivo?
- Il dispositivo dispone di connettività di rete (controllo tramite web browser)?

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new
