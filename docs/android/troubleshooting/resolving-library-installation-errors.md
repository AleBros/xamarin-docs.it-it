---
title: Risoluzione degli errori di installazione di librerie
description: In alcuni casi, è possibile che si verifichino errori durante l'installazione delle librerie di supporto Android.In some cases, you may get errors while installing Android support libraries. In questa guida vengono fornite soluzioni alternative per alcuni errori comuni.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/14/2018
ms.openlocfilehash: e99e12aa73374cc8145fad0eb5aad7f3259e0ca2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292838"
---
# <a name="resolving-library-installation-errors"></a>Risoluzione degli errori di installazione di librerie

_In alcuni casi, è possibile che si verifichino errori durante l'installazione delle librerie di supporto Android.In some cases, you may get errors while installing Android support libraries. In questa guida vengono fornite soluzioni alternative per alcuni errori comuni._

## <a name="overview"></a>Panoramica

Durante la compilazione di un progetto di app Xamarin.Android, è possibile che si verifichino errori di compilazione quando Visual Studio o Visual Studio per Mac tentano di scaricare e installare librerie di dipendenze. Molti di questi errori sono causati da problemi di connettività di rete, danneggiamento dei file o problemi di controllo delle versioni. Questa guida descrive gli errori più comuni di installazione della libreria di supporto e fornisce i passaggi per risolvere questi problemi e ottenere nuovamente la compilazione del progetto dell'app.

## <a name="errors-while-downloading-m2repository"></a>Errori durante il download di m2RepositoryErrors while Downloading m2Repository

È possibile che vengano visualizzati errori **m2repository** quando si fa riferimento a un pacchetto NuGet delle librerie di supporto Android o dei servizi Google Play. Il messaggio di errore è simile al seguente:

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

Questo esempio è per **Android\_m2repository\_r16**, ma è possibile visualizzare lo stesso messaggio di errore per una versione diversa, ad esempio **\_android m2repository\_r18** o android **\_m2repository\_r25**.

### <a name="automatic-recovery-from-m2repository-errors"></a>Recupero automatico da errori m2repository

Spesso, questo problema può essere risolto eliminando la libreria problematica e ricompilando secondo questi passaggi:

1. Passare alla directory della libreria di supporto del computer:

    - In Windows, le librerie di supporto si trovano in **\\C:\\Users_username_\\AppData\\Local\\Xamarin**.

    - In Mac OS X, le librerie di supporto si trovano in **/Utenti/_nomeutente_/.local/share/Xamarin**.

2. Individuare la libreria e la cartella della versione corrispondenti al messaggio di errore. Ad esempio, la cartella della libreria e della versione per il messaggio di errore precedente si trova in **Android.Support.v4\\22.2.1**:

    [![Esempio di percorso della cartella per la libreria di supporto 22.2.1](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. Eliminare il contenuto della cartella della versione. Assicurarsi di rimuovere il file **.zip** così come il **contenuto** e le sottodirectory **incorporate** all'interno di questa cartella. Per il messaggio di errore di esempio illustrato in precedenza, i file e le sottodirectory mostrati in questa schermata (**content**, **embedded**e **android_m2repository_r16.zip**) devono essere eliminati:

    [![Contenuto di esempio della cartella della libreria di supporto 22.2.1](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   Si noti che è importante eliminare *l'intero* contenuto di questa cartella. Anche se questa cartella può inizialmente contenere il file "mancante" **\_android m2repository\_r16.zip,** questo file potrebbe essere stato parzialmente scaricato o danneggiato.

4. Ricompilare &ndash; il progetto in questo modo causerà il processo di compilazione per scaricare nuovamente la libreria mancante.

Nella maggior parte dei casi, questi passaggi risolveranno l'errore di compilazione e consentiranno di continuare. Se l'eliminazione di questa libreria non risolve l'errore di compilazione, è necessario scaricare e installare manualmente il file **android\_m2repository\_r_nn_.zip** come descritto nella sezione successiva.

### <a name="manually-downloading-m2repository"></a>Download manuale di m2repository

Se hai provato a utilizzare i passaggi di recupero automatico sopra e hai ancora errori di compilazione, puoi scaricare manualmente il file **android\_m2repository\_r_nn_.zip** (utilizzando un browser web) e installarlo secondo i seguenti passaggi. Questa procedura è utile anche se non si dispone dell'accesso a Internet sul computer di sviluppo, ma si è in grado di scaricare l'archivio utilizzando un altro computer.

1. Scaricare il file **android\_m2repository\_r_nn_.zip** &ndash; che corrisponde ai collegamenti dei messaggi di errore nell'elenco seguente (insieme all'hash MD5 corrispondente dell'URL di ogni collegamento):

    - [android\_m2repository\_r33.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    - [android\_m2repository\_r32.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    - [android\_m2repository\_r31.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    - [android\_m2repository\_r30.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    - [android\_m2repository\_r29.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC653030E7D695C41

    - [android\_m2repository\_r28.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374A0223

    - [android\_m2repository\_r27.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    - [android\_m2repository\_r26.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    - [android\_m2repository\_r25.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    - [android\_m2repository\_r24.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    - [android\_m2repository\_r23.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    - [android\_m2repository\_r22.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    - [android\_m2repository\_r21.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    - [android\_m2repository\_r20.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    - [android\_m2repository\_r19.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AE39E9460B8A5851A

    - [android\_m2repository\_r18.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    - [android\_m2repository\_r17.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    - [android\_m2repository\_r16.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    Se l'archivio **m2repository** non è visualizzato in questa tabella, è possibile creare l'URL di download anteponendo `https://dl-ssl.google.com/android/repository/` il nome del repository **m2** da scaricare. Ad esempio, **https://dl-ssl.google.com/android/repository/android\_m2repository\_r10.zip** utilizzare per scaricare **Android\_m2repository\_r10.zip**.

2. Rinominare il file con l'hash MD5 corrispondente dell'URL di download, come illustrato nella tabella precedente. Ad esempio, se hai scaricato **\_Android m2repository\_r25.zip**, rinominalo in **0B3F1796C97C707339FB13AE8507AF50.zip**. Se l'hash MD5 per l'URL di download del file scaricato non è mostrato nella tabella, è possibile utilizzare un [generatore MD5 online](http://www.webconfs.com/online-md5-generator.php) per convertire l'URL in una stringa hash MD5.

3. Copiare il file nella cartella **cerniera** Xamarin:

    - In Windows, questa cartella si trova in **C:\\\\Utenti\\nome***utente***\\AppData\\Local\\Xamarin zip**.

    - In Mac OS X, questa cartella si trova in **/Utenti/***nomeutente***/.local/share/Xamarin/zips**.

    Ad esempio, la schermata seguente illustra il risultato quando **Android\_m2repository\_r16.zip** viene scaricato e rinominato nell'hash MD5 del relativo URL di download in Windows:

    [![Esempio di reconfigurazione del repository r16.zip rinominato in 0595E577D19D31708195A83087881EE6.zip](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)

Se questa procedura non risolve l'errore di compilazione, è necessario scaricare manualmente il file **android\_m2repository\_r_nn_.zip,** decomprimerlo e installarne il contenuto come descritto nella sezione successiva.

### <a name="manually-downloading-and-installing-m2repository-files"></a>Download e installazione manuale dei file m2repository

Il processo completamente manuale per il recupero da errori **m2repository** comporta il download del file **\_android m2repository\_r_nn_.zip** (utilizzando un browser web), decomprimendolo e copiando il suo contenuto nella directory della libreria di supporto sul computer. Nell'esempio seguente verrà ripristinato da questo messaggio di errore:In the following example, we'll recover from this error message:

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

Attenersi alla seguente procedura per scaricare **m2repository** e installarne il contenuto:

1. Eliminare il contenuto della cartella della libreria corrispondente al messaggio di errore. Ad esempio, nel messaggio di errore precedente si eliminerebbe il contenuto di **C:\\\\Users nome***utente***\\AppData\\Local\\Xamarin\\Android.Support.v4\\23.1.1.0**.
    Come descritto in precedenza, è necessario eliminare l'intero contenuto di questa directory:

    [![Eliminazione di contenuti, cartelle incorporate e android_m2repository dalla cartella 23.1.1.0](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2. Scarica il file **android\_m2repository\_r_nn_.zip** da Google che corrisponde al messaggio di errore (vedi la tabella nella sezione precedente per i link).

3. Estrarre questo archivio **.zip** in qualsiasi posizione (ad esempio il Desktop). Verrà creata una directory che corrisponde al nome dell'archivio **.zip.** All'interno di questa directory, si dovrebbe trovare una sottodirectory chiamata **m2repository**:

    [![m2repository cartella trovata nell'archivio zip estratto](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4. Nella directory della libreria con controllo delle versioni eliminata nel passaggio 1, ricreare il **contenuto** e le sottodirectory **incorporate.** Ad esempio, la schermata seguente illustra il **contenuto** e le sottodirectory **incorporate** create nella cartella **23.1.1.0** per **Android\_m2repository\_r25.zip**:

    [![Creare contenuto e cartelle incorporate nella cartella 23.1.1.0](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5. Copiare **m2repository** dal **file .zip** estratto nella directory dei **contenuti** creata nel passaggio precedente:

    [![Schermata di m2repository copiata nella cartella 23.1.1.0/content](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6. Nella directory **.zip** estratta, passare a **m2repository\\com\\android\\support-v4\\** e aprire la cartella corrispondente al numero di versione creato in precedenza (in questo esempio, **23.1.1**):

    [![Esempio di elenco dei file contenuti nella cartella support-v4/23.1.1](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7. Copiare tutti i file in questa cartella nella directory **incorporata** creata nel passaggio 4:

    [![Esempio di file copiati nella cartella 23.1.1.0/embedded](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8. Verificare che tutti i file vengano copiati. La directory **incorporata** dovrebbe ora contenere file quali **JAR**, **.aar**e **.pom**.

9. Decomprimere il contenuto di tutti i file **.aar** estratti nella directory **incorporata.** In Windows, aggiungi un'estensione **.zip** al file **.aar,** aprilo e copia il contenuto nella directory **incorporata.**
    In macOS decomprimere il file **.aar** utilizzando il comando **dezip** nel terminale (ad esempio, **decomprimere file.aar**).

A questo punto, sono stati installati manualmente i componenti mancanti e il progetto deve essere compilato senza errori. In caso contrario, verificare di aver scaricato la versione dell'archivio **.zip** **m2repository** che corrisponde esattamente alla versione nel messaggio di errore e di averne installato il contenuto nelle posizioni corrette, come descritto nei passaggi precedenti.

## <a name="summary"></a>Riepilogo

In questo articolo viene spiegato come eseguire il ripristino da errori comuni che possono verificarsi durante il download automatico e l'installazione delle librerie di dipendenze. Viene descritto come eliminare la libreria problematica e ricompilare il progetto come un modo per scaricare nuovamente e reinstallare la libreria.
Viene descritto come scaricare la libreria e installarla nella cartella **zip.** Ha inoltre descritto una procedura più complessa per il download e l'installazione manuale dei file necessari come un modo per risolvere i problemi che non possono essere risolti tramite mezzi automatici.
