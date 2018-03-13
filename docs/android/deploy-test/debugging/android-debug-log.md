---
title: Log di debug Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 01A715FE-9E9D-9B85-8A59-6568D8A09CA5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 26ac42e4b7acbe19dee746130fc335fdf18ffc46
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="android-debug-log"></a>Log di debug Android

## <a name="android-debug-log-overview"></a>Panoramica del log di debug Android

Un espediente piuttosto comune a cui ricorrono gli sviluppatori per il debug delle proprie applicazioni sono le chiamate a `Console.WriteLine`. In una piattaforma per dispositivi mobili come Android, tuttavia, la console non è disponibile. I dispositivi Android specificano un log che è possibile usare durante la scrittura delle app. Questo log viene a volte indicato come "logcat" a causa del comando digitato per recuperalo.

## <a name="accessing-from-visual-studio"></a>Accesso da Visual Studio

In Visual Studio 2015 e versioni successive i riquadri dei log di Android e iOS sono unificati.

### <a name="visual-studio-2015--2017"></a>Visual Studio 2015 e 2017

Nella finestra del nuovo strumento Log del dispositivo per Visual Studio sono visualizzati i log per i dispositivi Android e iOS. È possibile visualizzarla eseguendo uno dei comandi seguenti: 

-   **Visualizza -> Altre finestre -> Log del dispositivo**
-   **Strumenti -> Android -> Log del dispositivo**
-   **Barra degli strumenti Android -> Log del dispositivo**

Dopo che la finestra dello strumento è stata visualizzata, il dispositivo fisico può essere selezionato nella casella combinata dei dispositivi. Quando il dispositivo è selezionato, viene automaticamente avviata l'aggiunta di voci al log da un'app in esecuzione presente nella tabella. Il passaggio da un dispositivo a un altro comporta l'arresto e l'avvio della registrazione del dispositivo. Per visualizzare i dispositivi nella casella combinata è necessario caricare un progetto Android. Se il dispositivo non viene visualizzato nella casella combinata, controllare prima se è disponibile nell'elenco a discesa Avvia debug. 

La finestra dello strumento offre una tabella di voci di log, una casella combinata per la selezione dei dispositivi, la possibilità di cancellare le voci di log, una casella di ricerca e pulsanti di riproduzione/arresto/sospensione. 



## <a name="accessing-from-the-command-line"></a>Accesso dalla riga di comando

Un'altra opzione per visualizzare il log di debug è tramite la riga di comando. Aprire una finestra della console e passare alla cartella degli strumenti della piattaforma Android SDK, ad esempio **C:\android-sdk-windows\platform-tools**. 

Se è collegato un solo dispositivo, è possibile visualizzare il log con:

```shell
$ adb logcat
```

Se sono collegati più dispositivi, sarà necessario identificare il dispositivo. `adb -d logcat`, ad esempio, consente di visualizzare il log dell'unico dispositivo fisico connesso, mentre `adb -e logcat` consente di visualizzare il log dell'unico emulatore in esecuzione. 

Altri comandi sono disponibili eseguendo semplicemente **adb**.



## <a name="writing-to-the-debug-log"></a>Scrittura nel log di debug

È possibile scrivere messaggi nel log di debug usando i metodi della classe [Android.Util.Log](https://developer.xamarin.com/api/type/Android.Util.Log/): 

```csharp
string tag = "myapp";

Log.Info (tag, "this is an info message");
Log.Warn (tag, "this is a warning message");
Log.Error (tag, "this is an error message");
```

Genera:

```shell
I/myapp   (11103): this is an info message
W/myapp   (11103): this is a warning message
E/myapp   (11103): this is an error message
```


## <a name="interesting-messages"></a>Messaggi interessanti

Durante la lettura del log e specialmente quando si forniscono frammenti di log ad altri (poiché fornire il file di log completo è (1) eccessivo e (2) non sarebbe leggibile), la riga *più importante* da cui iniziare è simile alla seguente:

```shell
I/ActivityManager(12944): Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10200000 cmp=GcTest.GcTest/gctest.Activity1 } from pid 24175
```

In particolare, una riga corrispondente all'espressione regolare:

```shell
^I.*ActivityManager.*Starting: Intent
```

e contenente il nome del pacchetto dell'applicazione. Questa è la riga che corrisponde all'inizio di un'attività e la *maggior parte* dei messaggi successivi (ma non tutti) deve essere correlata all'applicazione. 

In particolare, ogni messaggio contiene l'identificatore di processo (PID) del processo che genera il messaggio. Nel messaggio `ActivityManager` sopra riportato, il processo `12944` ha generato il messaggio. Per individuare il processo dell'applicazione in fase di debug, cercare il messaggio mono.MonoRuntimeProvider: 

```shell
I/ActivityThread(  602): Pub TouchTest.TouchTest.__mono_init__: mono.MonoRuntimeProvider
```

Questo messaggio proviene dal processo avviato e tutti i messaggi successivi che contengono lo stesso PID provengono dallo stesso processo. 
