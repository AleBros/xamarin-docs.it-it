---
title: Xamarin Profiler risoluzione dei problemi
description: Questo documento fornisce informazioni sulla risoluzione dei problemi relativi al Profiler di Xamarin. Vengono descritti i problemi relativi a registrazione e diagnostica, l'IDE e altri argomenti.
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: lobrien
ms.author: laobri
ms.date: 10/27/2017
ms.openlocfilehash: e4a4376291ff56433c8cd9785989af2983a80c1c
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832155"
---
# <a name="xamarin-profiler-troubleshooting"></a>Xamarin Profiler risoluzione dei problemi

## <a name="logging-and-diagnostics"></a>Registrazione e diagnostica

Il team di Xamarin consente di tenere traccia dei problemi se si fornirci informazioni, tra cui:

- Screencast del problema, arresto anomalo del sistema, o un errore e il flusso di lavoro che hanno contribuito a esso.
- Output di log (vedere sotto).
- Il **.mlpd** da generare per la sessione di profilatura (vedere sotto).

### <a name="getting-log-outputs"></a>Ricezione dell'output di Log

In Mac vengono salvati i log `~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`.

In Windows queste proprietà vengono salvate per `%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log` , includere il log più recente ogni volta che si invia un problema.

Stiamo aggiungendo altre registrazione man mano che si procede, in modo che l'output dovrebbe aumentare e diventano più utile nel corso del tempo.

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>Generazione di file .mlpd

Un' **.mlpd** file è riportato l'output del profiler runtime di mono compresso. L'interfaccia utente grafica di Xamarin Profiler legge i dati da un **.mlpd** e lo visualizza per l'utente. **.mlpd** i file sono utili gli strumenti di debug per Xamarin perché aiutano i tecnici della diagnosi dei problemi di Profiler potrebbe verificarsi con i dati.

Il **.mlpd** per la sessione corrente verrà automaticamente salvata in ambiente Mac `/tmp` directory e possono essere identificati dal timestamp. Se attiva la registrazione, il primo output sarà il percorso per il **.mlpd** file. Il **.mlpd** normalmente file verrà salvato nella directory di avvio ~/var/cartelle...

Il **.mlpd** per una sessione di corrente può essere salvata anche scegliendo **File > Salva con nome...** dal menu del Profiler:

**Visual Studio per Mac**:

![](troubleshooting-images/image17.png "Salvataggio file .mlpd in Visual Studio per Mac")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "Salvataggio file .mlpd in Visual Studio")

È importante notare che **.mlpd** contengono molte informazioni e le dimensioni del file sarà elevata.

## <a name="troubleshooting"></a>risoluzione dei problemi

L'elenco seguente mostra i comuni problemi, possibili soluzioni e suggerimenti e consigli per usando il Profiler.

> [!NOTE]
> È necessario essere un Visual Studio **Enterprise** sottoscrittore per rendere disponibile questa funzionalità in entrambi Visual Studio Enterprise su Windows o Visual Studio per Mac.

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>Non è possibile visualizzare l'opzione di profiler iOS o è grigio [Visual Studio e Visual Studio per Mac]

Controllare le impostazioni seguenti per risolvere questo problema:

- Assicurarsi che si usa la configurazione di Debug
- Assicurarsi di usare SGen Garbage collector.
- Verificare che sia la piattaforma [supportato](~/tools/profiler/index.md#Profiler_Support).
- Assicurarsi di avere la licenza corretta.
- Assicurarsi di essere connessi in modo adeguato autenticato.
- [Visual Studio] È necessario usare [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/enterprise/) e ha una licenza Enterprise valida.

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>Viene visualizzato un errore quando tento di avviare il profiler

Se si esegue in questa finestra di errore quando si usa il profiler in Visual Studio:

![](troubleshooting-images/error.png "Finestra di errore quando si usa il profiler in Visual Studio")

In genere è dovuto all'impossibilità di avviare il simulatore / emulatore. Provare ed eseguire l'app in genere, risolvere i problemi che offre e quindi provare a utilizzare nuovamente il Profiler.

#### <a name="to-watch-a-specific-thread"></a>Per guardare un thread specifico

Se si dispone di un thread che si vuole controllare in particolare, l'ideale sarebbe per denominare il thread all'inizio della creazione per ottenere `ThreadName` invece di `0x0`. Ad esempio per impostare il nome del thread come `UI`, è possibile usare il codice seguente:

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>Collegamenti correlati

- [Procedura dettagliata: uso di Xamarin Profiler](~/tools/profiler/index.md)
- [Procedure consigliate per la memoria e prestazioni](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Note sulla versione](https://developer.xamarin.com/releases/profiler/preview/)
