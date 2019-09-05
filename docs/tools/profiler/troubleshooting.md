---
title: Risoluzione dei problemi di Xamarin Profiler
description: Questo documento fornisce informazioni sulla risoluzione dei problemi relativi alla Xamarin Profiler. Vengono descritti i problemi relativi alla registrazione e alla diagnostica, all'IDE e ad altri argomenti.
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: conceptdev
ms.author: crdun
ms.date: 10/27/2017
ms.openlocfilehash: c6a05e332bf0c08f8c7ea328c2793f7d0bf00fb7
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285706"
---
# <a name="xamarin-profiler-troubleshooting"></a>Risoluzione dei problemi di Xamarin Profiler

## <a name="logging-and-diagnostics"></a>Registrazione e diagnostica

Il team di Novell può aiutare a tenere traccia dei problemi se si forniscono informazioni, tra cui:

- Uno screencast del problema, dell'arresto anomalo o dell'errore e del flusso di lavoro.
- Output del log (vedere di seguito).
- Il **. MLPD** generato per la sessione di profilatura (vedere di seguito).

### <a name="getting-log-outputs"></a>Recupero degli output del log

Nei log Mac vengono salvati in `~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`.

In Windows vengono salvati in modo `%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log` da includere l'ultimo log ogni volta che si invia un problema.

Stiamo aggiungendo più registrazioni, quindi questo output dovrebbe crescere e diventare più utile nel tempo.

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>Generazione di file con estensione MLPD

Un file con **estensione MLPD** è l'output compresso del profiler del runtime di mono. Il Xamarin Profiler GUI legge i dati da un **MLPD** e li Visualizza per l'utente. i file con **estensione MLPD** sono strumenti di debug utili per Novell perché consentono ai tecnici di diagnosticare i problemi che il profiler potrebbe avere con i dati.

Il **. MLPD** per la sessione corrente viene salvato automaticamente nella `/tmp` directory del Mac e può essere identificato dal timestamp. Se si attiva la registrazione, il primo output sarà il percorso del file con **estensione MLPD** . Il file con **estensione MLPD** verrà in genere salvato nella directory a partire da ~/var/Folders...

Il file con **estensione MLPD** per una sessione corrente può essere salvato anche scegliendo **file > Salva con nome...** dal menu del profiler:

**Visual Studio per Mac**:

![](troubleshooting-images/image17.png "Salvataggio del file con estensione MLPD in Visual Studio per Mac")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "Salvataggio del file con estensione MLPD in Visual Studio")

È importante tenere presente che **. MLPD** contengono molte informazioni e le dimensioni del file saranno elevate.

## <a name="troubleshooting"></a>Risoluzione dei problemi

L'elenco seguente illustra i problemi comuni, le soluzioni alternative e suggerimenti e consigli per l'uso del profiler.

> [!NOTE]
> È necessario essere un Sottoscrittore di Visual Studio **Enterprise** per sbloccare questa funzionalità in Visual Studio Enterprise in Windows o Visual Studio per Mac.

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>L'opzione del profiler iOS non è visibile oppure è disattivata [Visual Studio e Visual Studio per Mac]

Per risolvere il problema, controllare le impostazioni seguenti:

- Assicurarsi di usare la configurazione di debug
- Assicurarsi di usare il Garbage Collector SGen.
- Verificare che la piattaforma sia [supportata](~/tools/profiler/index.md#Profiler_Support).
- Assicurarsi di disporre della licenza corretta.
- Assicurarsi di essere connessi e autenticati correttamente.
- [Visual Studio] È necessario usare [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/enterprise/) e avere una licenza Enterprise valida.

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>Si verifica un errore quando si tenta di avviare il profiler

Se si esegue questa casella di errore quando si usa il profiler in Visual Studio:

![](troubleshooting-images/error.png "Casella di errore quando si usa il profiler in Visual Studio")

In genere non è possibile avviare il simulatore o l'emulatore. Provare a eseguire l'app normalmente, risolvere i problemi che fornisce, quindi provare a usare nuovamente il profiler.

#### <a name="to-watch-a-specific-thread"></a>Per controllare un thread specifico

Se si dispone di un thread che si desidera controllare in modo specifico, sarebbe ideale assegnare un nome al thread all'inizio della creazione per ottenere `ThreadName` `0x0`anziché. Ad esempio, per impostare il nome del `UI`thread come, è possibile usare il codice seguente:

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>Collegamenti correlati

- [Procedura dettagliata: utilizzo del Xamarin Profiler](~/tools/profiler/index.md)
- [Procedure consigliate per la memoria e le prestazioni](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Note sulla versione](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/profiler/preview/index.md)
