---
title: Xamarin Profiler risoluzione dei problemi
description: Questo documento vengono fornite informazioni sulla risoluzione dei problemi correlate al Xamarin Profiler. Vengono descritti i problemi relativi alla registrazione e diagnostica, l'IDE e altri argomenti.
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: topgenorth
ms.author: toopge
ms.date: 10/27/2017
ms.openlocfilehash: 247a18cf7f645ea90d100cb3f4900f30ac7754cc
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793855"
---
# <a name="xamarin-profiler-troubleshooting"></a>Xamarin Profiler risoluzione dei problemi

## <a name="logging-and-diagnostics"></a>Registrazione e diagnostica

Il team di Xamarin consente di tenere traccia dei problemi, se fornire informazioni, tra cui:

- Screencast il problema, l'arresto anomalo del sistema, o problemi e il flusso di lavoro conducono a esso.
- Output di log (vedere sotto).
- Il **.mlpd** generate per la sessione di profilatura (vedere sotto).

### <a name="getting-log-outputs"></a>Ottenere l'output di Log

In Mac vengono salvati i log `~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`.

In Windows salvarli su `%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log` Includi log più recente quando si invia un problema.

Stiamo aggiungendo ulteriori registrazione man mano che si procede, pertanto l'output dovrebbe aumentare e diventare più utile nel tempo.

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>Generazione di file .mlpd

Un **.mlpd** file è l'output del profiler runtime mono compresso. L'interfaccia utente grafica Profiler Xamarin legge i dati da un **.mlpd** e lo visualizza per l'utente. **.mlpd** i file sono utili gli strumenti di debug per Xamarin poiché consentono i tecnici di diagnosticare i problemi riscontrati con i dati di Profiler.

Il **.mlpd** per la sessione corrente verrà salvata automaticamente in ambiente Mac `/tmp` directory e possono essere identificati dal timestamp. Se si abilita la registrazione, il primo output sarà il percorso di **.mlpd** file. Il **.mlpd** normalmente file verrà salvato nella directory ~/var o le cartelle di avvio...

Il **.mlpd** per una sessione di corrente può essere salvata anche scegliendo **File > Salva con nome...** dal menu del Profiler:

**Visual Studio per Mac**:

![](troubleshooting-images/image17.png "Salvataggio di file .mlpd in Visual Studio per Mac")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "Salvataggio file .mlpd in Visual Studio")

È importante notare che **.mlpd** contengono molte informazioni e le dimensioni del file sarà elevata.

## <a name="troubleshooting"></a>Risoluzione dei problemi

L'elenco seguente mostra i comuni problemi, soluzioni alternative e suggerimenti utili per l'utilizzo del Profiler.

> [!NOTE]
> **Nota**: È necessario essere un Visual Studio **Enterprise** sottoscrittore per sbloccare questa funzionalità in entrambi Visual Studio Enterprise in Windows o di Visual Studio per Mac.

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>Non è possibile visualizzare l'opzione di profiler iOS o è disattivata [Visual Studio e Visual Studio per Mac]

Controllare le impostazioni seguenti per risolvere questo problema:

- Verificare che si sta utilizzando la configurazione di Debug
- Verificare che si sta utilizzando SGen Garbage collector.
- Verificare che la piattaforma è [supportati](~/tools/profiler/index.md#Profiler_Support).
- Assicurarsi di disporre della licenza di destra.
- Assicurarsi di essere connessi in modo semplice e configurata correttamente autenticato.
- [Visual Studio] È necessario utilizzare [Visual Studio Enterprise](https://www.visualstudio.com/vs/enterprise/) e avere una licenza Enterprise.

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>Viene visualizzato un errore quando si tenta di avviare il profiler

Se si esegue in questa finestra di errore quando si usa il profiler di Visual Studio:

![](troubleshooting-images/error.png "Finestra di errore quando si usa il profiler di Visual Studio")

È in genere a causa dell'impossibilità di avvio per il simulatore o dell'emulatore. Provare a eseguire normalmente l'app e risolvere i problemi che offre e quindi provare a utilizzare nuovamente il Profiler.

#### <a name="to-watch-a-specific-thread"></a>Per guardare un thread specifico

Se si dispone di un thread che si desidera controllare in modo specifico, sarebbe ideale per denominare il primo thread molto inizio della sua creazione in modo da ottenere get `ThreadName` anziché `0x0`. Ad esempio per impostare il nome del thread di interfaccia utente, è possibile utilizzare il codice seguente:

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>Collegamenti correlati

- [Procedura dettagliata: utilizzo del Xamarin Profiler](~/tools/profiler/index.md)
- [Procedure consigliate per la memoria e prestazioni](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Note sulla versione](https://developer.xamarin.com/releases/profiler/preview/)
