---
title: Xamarin Profiler risoluzione dei problemi
description: Questo documento vengono fornite informazioni sulla risoluzione dei problemi correlate al Xamarin Profiler. Vengono descritti i problemi relativi alla registrazione e diagnostica, l'IDE e altri argomenti.
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: topgenorth
ms.author: toopge
ms.date: 10/27/2017
ms.openlocfilehash: 71faf79ef9b783480dbb6ff4674859a9148abca3
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066910"
---
# <a name="xamarin-profiler-troubleshooting"></a>Xamarin Profiler risoluzione dei problemi

## <a name="logging-and-diagnostics"></a>Registrazione e diagnostica

Il team di Xamarin consente di tenere traccia dei problemi se fornire informazioni, tra cui:

- Screencast il problema, arresto anomalo del sistema, o problemi e il flusso di lavoro conducono a esso.
- Output di log (vedere sotto).
- Il **.mlpd** in corso la generazione per la sessione di profilatura (vedere sotto).

### <a name="getting-log-outputs"></a>Recupero di output di Log

In Mac vengono salvati i log `~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`.

In Windows queste vengono salvate in `%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log` includere il log più recente quando si invia un problema.

Stiamo aggiungendo ulteriori registrazione man mano che si procede, in modo che l'output dovrebbe aumentare e diventare più utile nel tempo.

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>Generazione di file .mlpd

Un' **.mlpd** file è l'output del profiler runtime mono compresso. L'interfaccia utente grafica Profiler Xamarin legge i dati da un **.mlpd** e lo visualizza per l'utente. **.mlpd** i file sono utili gli strumenti di debug per Xamarin poiché consentire i tecnici diagnosticare i problemi che il Profiler può verificare con i dati.

Il **.mlpd** per la sessione corrente verrà salvata automaticamente in ambiente Mac `/tmp` directory e può essere identificato tramite il timestamp. Se attiva la registrazione, il primo output sarà il percorso per il **.mlpd** file. Il **.mlpd** normalmente file verrà salvato nella directory di avvio ~/var o le cartelle...

Il **.mlpd** per una sessione di corrente può essere salvata anche scegliendo **File > Salva con nome...** dal menu del Profiler:

**Visual Studio per Mac**:

![](troubleshooting-images/image17.png "Salvataggio file .mlpd in Visual Studio per Mac")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "Salvataggio file .mlpd in Visual Studio")

È importante notare che **.mlpd** contengono molte informazioni e le dimensioni del file sarà elevata.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Nell'elenco seguente mostra i comuni problemi individuati, soluzioni alternative e suggerimenti utili per l'utilizzo del Profiler.

> [!NOTE]
> **Nota**: È necessario essere un Visual Studio **Enterprise** sottoscrittore per sbloccare questa funzionalità in entrambi Visual Studio Enterprise in Windows o di Visual Studio per Mac.

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>Non è possibile visualizzare l'opzione di profiler iOS o è grigio [Visual Studio e Visual Studio per Mac]

Controllare le impostazioni seguenti per risolvere questo problema:

- Assicurarsi che si utilizza la configurazione di Debug
- Verificare che si sta utilizzando SGen Garbage collector.
- Verificare che la piattaforma [supportato](~/tools/profiler/index.md#Profiler_Support).
- Verificare di avere la licenza a destra.
- Assicurarsi di aver effettuato in e configurata correttamente autenticato.
- [Visual Studio] È necessario utilizzare [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/enterprise/) e dispone di una licenza Enterprise valida.

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>Viene visualizzato un errore quando si tenta di avviare il profiler

Se si esegue in questa finestra di errore quando si usa il profiler in Visual Studio:

![](troubleshooting-images/error.png "Finestra di errore quando si usa il profiler in Visual Studio")

È in genere a causa dell'impossibilità di avvio per il simulatore o dell'emulatore. Provare a eseguire normalmente l'app e risolvere i problemi che offre e quindi provare a utilizzare nuovamente il Profiler.

#### <a name="to-watch-a-specific-thread"></a>Per guardare un thread specifico

Se si dispone di un thread che si desidera controllare in modo specifico, sarebbe ideale per denominare il primo thread molto inizio della creazione in modo da ottenere get `ThreadName` invece di `0x0`. Ad esempio per impostare il nome del thread di interfaccia utente, è possibile utilizzare il codice seguente:

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>Collegamenti correlati

- [Procedura dettagliata - tramite il Xamarin Profiler](~/tools/profiler/index.md)
- [Procedure consigliate per la memoria e prestazioni](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Note sulla versione](https://developer.xamarin.com/releases/profiler/preview/)
