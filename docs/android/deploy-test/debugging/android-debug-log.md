---
title: Log di debug Android
description: Informazioni su come usare il log di debug per eseguire il debug delle applicazioni Xamarin.Android.
ms.prod: xamarin
ms.assetid: 01A715FE-9E9D-9B85-8A59-6568D8A09CA5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 39ee1039104052b8799debf19f09439f57a1791d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028091"
---
# <a name="android-debug-log"></a>Log di debug Android

Un espediente piuttosto comune a cui ricorrono gli sviluppatori per il debug delle proprie applicazioni sono le chiamate a `Console.WriteLine`. In una piattaforma per dispositivi mobili come Android, tuttavia, la console non è disponibile. I dispositivi Android specificano un log che è possibile usare durante la scrittura delle app. Questo log viene a volte indicato come _logcat_ a causa del comando digitato per recuperarlo. Usare la strumento **Log di debug** per visualizzare i dati registrati.

## <a name="android-debug-log-overview"></a>Panoramica del log di debug Android

Lo strumento **Log di debug** fornisce un modo per visualizzare l'output del log durante il debug di un'app tramite Visual Studio. Il log di debug supporta i dispositivi seguenti:

- Telefoni, tablet e dispositivi indossabili Android fisici.
- Un dispositivo virtuale Android in esecuzione nell'emulatore Android. 

> [!NOTE]
> Lo strumento **Log di debug** non funziona con Xamarin Live Player.

**Log di Debug** non visualizza i messaggi di log generati durante l'esecuzione dell'app autonoma nel dispositivo, ovvero mentre è disconnessa da Visual Studio.

## <a name="accessing-the-debug-log-from-visual-studio"></a>Accesso al log di debug da Visual Studio

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per aprire lo strumento **Log del dispositivo** fare clic sull'icona **Log del dispositivo (logcat)** sulla barra degli strumenti:

[![Percorso dello strumento Log del dispositivo sulla barra degli strumenti](android-debug-log-images/vswin-01-logcat-sml.png)](android-debug-log-images/vswin-01-logcat.png#lightbox)

In alternativa, avviare lo strumento **Log del dispositivo** da uno dei comandi di menu seguenti:

- **Visualizza -> Altre finestre -> Log del dispositivo**
- **Strumenti -> Android -> Log del dispositivo**

Lo screenshot seguente mostra le varie parti della finestra dello **strumento di debug**:

[![Parti della finestra dello strumento di debug](android-debug-log-images/vswin-03-features-sml.png)](android-debug-log-images/vswin-03-features.png#lightbox)

- **Selettore del dispositivo** &ndash; Consente di selezionare il dispositivo fisico o l'emulatore in esecuzione da monitorare.

- **Voci del log** &ndash; Tabella dei messaggi di log da logcat.

- **Cancella voci del log** &ndash; Cancella tutte le voci di log correnti dalla tabella.

- **Riproduci/Pausa** &ndash; Alterna l'aggiornamento o la sospensione della visualizzazione delle nuove voci del log.

- **Arresta** &ndash; Interrompe la visualizzazione delle nuove voci di log.

- **Casella di ricerca** &ndash; Consente di immettere stringhe di ricerca per filtrare le voci di log e visualizzarne un subset.

Quando la finestra dello strumento **Log di debug** è visualizzata, usare il menu a discesa dei dispositivi per scegliere il dispositivo Android da monitorare:

[![Posizione del selettore di dispositivo](android-debug-log-images/vswin-02-devices-combo-sml.png)](android-debug-log-images/vswin-02-devices-combo.png#lightbox)

Dopo aver selezionato il dispositivo, lo strumento **Log del dispositivo** aggiunge automaticamente le voci di log da un'app &ndash; in esecuzione. Queste voci di log vengono visualizzate nella tabella delle voci di log. Il passaggio da un dispositivo all'altro arresta e avvia la registrazione per il dispositivo. Si noti che è necessario caricare un progetto Android prima che vengano visualizzati tutti i dispositivi nel selettore di dispositivo. Se il dispositivo non compare nel selettore di dispositivo, verificare che sia disponibile nel menu a discesa dei dispositivi di Visual Studio accanto al pulsante **Start**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per aprire il **Log del dispositivo** fare clic su **Visualizza > Riquadri > Log del dispositivo**:

[![Percorso della voce di menu Log del dispositivo](android-debug-log-images/vsmac-01-logcat-sml.png)](android-debug-log-images/vsmac-01-logcat.png#lightbox)

Lo screenshot seguente mostra le varie parti della finestra dello **strumento di debug**:

[![Funzionalità della finestra dello strumento di debug](android-debug-log-images/vsmac-03-features-sml.png)](android-debug-log-images/vsmac-03-features.png#lightbox)

- **Selettore del dispositivo** &ndash; Consente di selezionare il dispositivo fisico o l'emulatore in esecuzione da monitorare.

- **Voci del log** &ndash; Tabella dei messaggi di log da logcat.

- **Cancella voci del log** &ndash; Cancella tutte le voci di log correnti dalla tabella.

- **Casella di ricerca** &ndash; Consente di immettere stringhe di ricerca per filtrare le voci di log e visualizzarne un subset.

- **Mostra messaggi** &ndash; Attiva o disattiva la visualizzazione dei messaggi informativi.

- **Mostra avvisi** &ndash; Attiva o disattiva la visualizzazione di messaggi di avviso (i messaggi di avviso vengono mostrati in giallo).

- **Mostra errori** &ndash; Attiva o disattiva la visualizzazione dei messaggi di errore (i messaggi di errore vengono mostrati in rosso).

- **Riconnetti** &ndash; Ristabilisce la connessione al dispositivo e aggiorna la visualizzazione delle voci di log.

- **Aggiungi marcatore** &ndash; Inserisce un messaggio marcatore (ad esempio `--- Marker N ---`) dopo l'ultima voce di log, dove _N_ è un contatore che inizia da 1 e aumenta di 1 man mano che vengono aggiunti nuovi marcatori.

Quando la finestra dello strumento Log di debug è visualizzata, usare il menu a discesa dei dispositivi per scegliere il dispositivo Android da monitorare:

[![Posizione del selettore di dispositivo](android-debug-log-images/vsmac-02-devices-combo-sml.png)](android-debug-log-images/vsmac-02-devices-combo.png#lightbox)

Dopo aver selezionato il dispositivo, lo strumento **Log del dispositivo** aggiunge automaticamente le voci di log da un'app &ndash; in esecuzione. Queste voci di log vengono visualizzate nella tabella delle voci di log. Il passaggio da un dispositivo all'altro arresta e avvia la registrazione per il dispositivo. Si noti che è necessario caricare un progetto Android prima che vengano visualizzati tutti i dispositivi nel selettore di dispositivo. Se il dispositivo non compare nel selettore di dispositivo, verificare che sia disponibile nel menu a discesa dei dispositivi di Visual Studio accanto al pulsante **Start**.

-----

## <a name="accessing-from-the-command-line"></a>Accesso dalla riga di comando

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Un'altra opzione per visualizzare il log di debug è tramite la riga di comando. Aprire una finestra del prompt dei comandi e passare alla cartella platform-tools di Android SDK (in genere, la cartella platform-tools dell'SDK si trova in **C:\\Programmi (x86)\\Android\\android-sdk\\platform-tools**).

Se è collegato solo un singolo dispositivo (dispositivo fisico o emulatore), è possibile visualizzare il log immettendo il comando seguente:

```shell
$ adb logcat
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Un'altra opzione per visualizzare il log di debug è tramite la riga di comando. Aprire una finestra di Terminale e passare alla cartella platform-tools di Android SDK (in genere, la cartella platform-tools dell'SDK si trova in **/Users/nomeutente/Library/Developer/Xamarin/android-sdk-macosx/platform-tools**).

Se è collegato solo un singolo dispositivo (dispositivo fisico o emulatore), è possibile visualizzare il log immettendo il comando seguente:

```shell
$ ./adb logcat
```

-----

Se sono collegati più dispositivi, è necessario identificare in modo esplicito il dispositivo. **adb -d logcat**, ad esempio, consente di visualizzare il log dell'unico dispositivo fisico connesso, mentre **adb -e logcat** mostra il log dell'unico emulatore in esecuzione.

Per informazioni sugli altri comandi disponibili, immettere **adb** e leggere i messaggi della Guida.

## <a name="writing-to-the-debug-log"></a>Scrittura nel log di debug

È possibile scrivere messaggi nel **log di debug** usando i metodi della classe [Android.Util.Log](xref:Android.Util.Log).
Esempio: 

```csharp
string tag = "myapp";

Log.Info (tag, "this is an info message");
Log.Warn (tag, "this is a warning message");
Log.Error (tag, "this is an error message");
```

Questo codice produce un output simile al seguente:

```shell
I/myapp   (11103): this is an info message
W/myapp   (11103): this is a warning message
E/myapp   (11103): this is an error message
```

È anche possibile usare `Console.WriteLine` per la scrittura nel **log di debug**. Questi messaggi vengono visualizzati in logcat con un formato di output leggermente diverso (questa tecnica è particolarmente utile durante il debug delle app Xamarin.Forms su Android):

```csharp
System.Console.WriteLine ("DEBUG - Button Clicked!");
```

Questo codice produce un output simile al seguente in logcat:

```
Info (19543) / mono-stdout: DEBUG - Button Clicked!
```

## <a name="interesting-messages"></a>Messaggi interessanti

Durante la lettura del log (e in particolare quando si forniscono frammenti del log ad altri), analizzare il file di log nella sua interezza risulta spesso troppo oneroso.
Per rendere più semplice gli spostamenti tra i messaggi dl log, iniziare cercando una voce di log simile alla seguente:

```shell
I/ActivityManager(12944): Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10200000 cmp=GcTest.GcTest/gctest.Activity1 } from pid 24175
```

In particolare, individuare la riga corrispondente all'espressione regolare che contiene anche il nome del pacchetto dell'applicazione:

```shell
^I.*ActivityManager.*Starting: Intent
```

Questa è la riga che corrisponde all'inizio di un'attività e la *maggior parte* dei messaggi successivi (ma non tutti) deve essere correlata all'applicazione.

Si noti che ogni messaggio contiene l'identificatore di processo (PID) del processo che genera il messaggio. Nel messaggio `ActivityManager` sopra riportato, il processo `12944` ha generato il messaggio. Per individuare il processo dell'applicazione in fase di debug, cercare il messaggio **mono.MonoRuntimeProvider**: 

```shell
I/ActivityThread(  602): Pub TouchTest.TouchTest.__mono_init__: mono.MonoRuntimeProvider
```

Questo messaggio proviene dal processo che è stato avviato. Tutti i messaggi successivi che contengono questo PID provengono dallo stesso processo.
