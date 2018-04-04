---
title: Risoluzione degli errori di installazione di libreria
description: In alcuni casi, potrebbero verificarsi errori durante l'installazione delle librerie di supporto Android. Questa guida vengono illustrate soluzioni alternative per alcuni errori comuni.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/14/2018
ms.openlocfilehash: 6f280a90994ff40ebd8a07d2cab49ddc2b3d6ca1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="resolving-library-installation-errors"></a>Risoluzione degli errori di installazione di libreria

_In alcuni casi, potrebbero verificarsi errori durante l'installazione delle librerie di supporto Android. Questa guida vengono illustrate soluzioni alternative per alcuni errori comuni._

## <a name="overview"></a>Panoramica

Durante la compilazione di un progetto di app xamarin, è possibile ricevere errori di compilazione quando Visual Studio o Visual Studio per Mac tenta di scaricare e installare le librerie di dipendenza. Molti di questi errori sono causati da problemi di connettività di rete, un danneggiamento di file o problemi di controllo delle versioni. Questa guida descrive gli errori di installazione libreria di supporto più comuni e fornisce i passaggi per risolvere questi problemi e ottenere il progetto di app di compilazione. 

 
 
## <a name="errors-while-downloading-m2repository"></a>Errori durante il download m2Repository

Può vedere **m2repository** errori quando si fa riferimento un pacchetto NuGet dei servizi le raccolte di supporto Android o Google Play. Il messaggio di errore è simile al seguente:

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

Questo esempio è relativo **android\_m2repository\_r16**, ma si può vedere questo stesso messaggio di errore per una versione diversa, ad esempio **android\_m2repository\_R18 durante**  o **android\_m2repository\_r25**. 



### <a name="automatic-recovery-from-m2repository-errors"></a>Recupero automatico da errori m2repository 

Spesso, questo problema può essere risolta eliminando la libreria problematica e la ricompilazione in base alla procedura seguente: 

1. Passare alla directory di libreria di supporto nel computer in uso:

    -   In Windows, librerie di supporto si trovano in **c:\\utenti\\_username_\\AppData\\locale\\Xamarin**. 

    -   In Mac OS X, librerie di supporto si trovano in **/Users/_username_/.local/share/Xamarin**. 

2. Individuare la cartella di libreria e la versione corrispondente al messaggio di errore. Ad esempio, la cartella di libreria e la versione per il messaggio di errore si trova in **Android.Support.v4\\22.2.1**:

    [![Esempio di percorso di cartella per 22.2.1 libreria di supporto](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. Eliminare il contenuto della cartella della versione. Assicurarsi di rimuovere il **zip** file, nonché **contenuto** e **incorporato** sottodirectory all'interno della cartella. Per il messaggio di errore riportato di seguito viene illustrato in precedenza, i file e sottodirectory illustrata in questa schermata (**contenuto**, **incorporato**, e **android_m2repository_r16.zip**) per eliminare:

    [![Il contenuto di esempio di 22.2.1 supporta nella cartella libreria](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   Si noti che è importante eliminare le *intero* contenuto di questa cartella. Sebbene questa cartella può contenere inizialmente il "mancante" **android\_m2repository\_r16.zip** file, questo file è stato parzialmente scaricato o danneggiato.

4. Ricompilare il progetto &ndash; in questo modo il processo di compilazione a scaricare nuovamente la libreria manca.

Nella maggior parte dei casi, questi passaggi saranno risolvere l'errore di compilazione e consentono di continuare. Se l'eliminazione di questa libreria non viene risolto l'errore di compilazione, è necessario scaricare e installare manualmente il **android\_m2repository\_r_nn_.zip** file come descritto nella sezione successiva. 



### <a name="manually-downloading-m2repository"></a>Scaricare manualmente m2repository

Se si è tentato di utilizzare la procedura di ripristino automatico precedente e ancora errori di compilazione, è possibile scaricare manualmente il **android\_m2repository\_r_nn_.zip** file (tramite un browser web) e installarlo in base le seguenti procedure. Questa procedura è utile anche se non si dispone dell'accesso a internet nel computer di sviluppo, ma si è in grado di scaricare l'archivio utilizzando un altro computer. 

1.  Scaricare il **android\_m2repository\_r_nn_.zip** file che corrisponde al messaggio di errore &ndash; nell'elenco seguente (con il valore hash MD5 corrispondente di ogni collegamento vengono forniti i collegamenti URL):

    -   [Android\_m2repository\_r33.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    -   [android\_m2repository\_r32.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    -   [Android\_m2repository\_r31.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    -   [Android\_m2repository\_r30.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    -   [Android\_m2repository\_r29.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC653030E7D695C41

    -   [android\_m2repository\_r28.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374AA0223

    -   [android\_m2repository\_r27.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    -   [android\_m2repository\_r26.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    -   [Android\_m2repository\_r25.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    -   [android\_m2repository\_r24.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    -   [Android\_m2repository\_r23.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    -   [Android\_m2repository\_r22.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    -   [android\_m2repository\_r21.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    -   [android\_m2repository\_r20.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    -   [android\_m2repository\_r19.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE39E9460B8A5851A

    -   [android\_m2repository\_r18.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    -   [android\_m2repository\_r17.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    -   [Android\_m2repository\_r16.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    Se il **m2repository** archivio non viene visualizzato in questa tabella, è possibile creare l'URL di download anteponendo **https://dl-ssl.google.com/android/repository/** al nome del **m2repository** per il download. Ad esempio, utilizzare  **https://dl-ssl.google.com/android/repository/android \_m2repository\_r10.zip** scaricare **android\_m2repository\_r10.zip**.

2.  Rinominare il file con il corrispondente hash MD5 dell'URL di download, come illustrato nella tabella precedente. Ad esempio, se è stato scaricato **android\_m2repository\_r25.zip**, rinominarlo in **0B3F1796C97C707339FB13AE8507AF50.zip**. Se l'hash MD5 per l'URL di download del file scaricato non è illustrato nella tabella, è possibile utilizzare un [online MD5 generatore](http://www.webconfs.com/online-md5-generator.php) per convertire l'URL in una stringa hash MD5. 

3.  Copiare il file di Xamarin **passa** cartella: 

    -   In Windows, questa cartella si trova in **c:\\utenti\\***username***\\AppData\\locale\\Xamarin\\passa**. 

    -   In Mac OS X, questa cartella si trova in **/Users/***username***/.local/share/Xamarin/zips**. 

    Ad esempio, nella schermata seguente viene illustrato il risultato quando **android\_m2repository\_r16.zip** viene scaricato e rinominata per l'hash MD5 del relativo URL di download in Windows:

    [![Esempio del repository r16.zip viene rinominato in 0595E577D19D31708195A83087881EE6.zip](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)


Se questa procedura non viene risolto l'errore di compilazione, è necessario scaricare manualmente il **android\_m2repository\_r_nn_.zip** file, decomprimere e installare il contenuto come descritto nella sezione successiva. 


### <a name="manually-downloading-and-installing-m2repository-files"></a>Manualmente scaricando e installando il file m2repository

Il processo manuale per il ripristino da **m2repository** errori comporta il download di **android\_m2repository\_r_nn_.zip** file (tramite un browser web), decompressione e copia il contenuto nella directory di libreria di supporto nel computer in uso. Nell'esempio seguente, è possibile recuperare da questo messaggio di errore: 

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

Utilizzare la procedura seguente per scaricare **m2repository** e installare il relativo contenuto:

1.  Eliminare il contenuto della cartella libreria corrispondente al messaggio di errore. Ad esempio, nel messaggio di errore precedente elimina il contenuto di **c:\\utenti\\***username***\\AppData\\locale\\Xamarin\\ Android.Support.v4\\23.1.1.0**. 
    Come descritto in precedenza, è necessario eliminare l'intero contenuto di questa directory:

    [![Eliminazione del contenuto, incorporato e cartelle android_m2repository il 23.1.1.0 cartella](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2.  Scaricare il **android\_m2repository\_r_nn_.zip** file da Google che corrisponde all'errore del messaggio (vedere la tabella nella sezione precedente per i collegamenti).

3.  Estrarre tale **zip** archivio in qualsiasi percorso (ad esempio il Desktop). Si deve creare una directory che corrisponde al nome del **zip** archivio. In questa directory, è necessario trovare una sottodirectory denominata **m2repository**: 

    [![cartella m2repository trovata nell'archivio zip estratti](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4.  Nella directory di libreria con controllo delle versioni che eliminati nel passaggio 1, ricreare il **contenuto** e **incorporato** nelle sottodirectory. Ad esempio, nella schermata seguente mostra **contenuto** e **incorporato** da creare nella sottodirectory di **23.1.1.0** cartella per **android \_m2repository\_r25.zip**: 

    [![Creare contenuto e delle cartelle di embedded nel 23.1.1.0 cartella](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5.  Copia **m2repository** dall'estratto **zip** nel **contenuto** directory creata nel passaggio precedente: 

    [![Schermata di m2repository copiati nella cartella 23.1.1.0/content](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6.  Nell'estratto **zip** passare alla directory **m2repository\\com\\android\\supporta\\supporto v4** e aprire la cartella corrispondente il numero di versione creato in precedenza (in questo esempio, **23.1.1**):

    [![Elenco di esempio di file contenuti nella cartella support-v4/23.1.1](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7.  Copiare tutti i file in questa cartella per il **incorporato** directory creata nel passaggio 4:

    [![Esempio di file copiati nella cartella 23.1.1.0/embedded](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8.  Verificare che tutti i file vengono copiati. Il **incorporato** directory ora deve contenere file, ad esempio **JAR**, **AAR**, e **.pom**.

9.  Decomprimere qualsiasi estratti **AAR** file. In Windows, aggiungere un **zip** estensione per il **AAR** file, pulsante destro del mouse e selezionare **Estrai tutto...** , quindi rimuovere il **zip** estensione. In macOS, decomprimere il **AAR** file utilizzando il **decomprimere** comando Terminal (ad esempio, **decomprimere file.aar**).

A questo punto, si sono stati installati manualmente i componenti mancanti e il progetto dovrebbe essere compilato senza errori. In caso contrario, verificare che sia stato scaricato il **m2repository** **zip** archiviare versione che corrisponde esattamente alla versione nel messaggio di errore e verificare di aver installato il relativo contenuto nel correggere i percorsi come descritto nei passaggi precedenti. 



## <a name="summary"></a>Riepilogo 

Questo articolo ha descritto come risolvere gli errori comuni che possono essere eseguiti durante il download automatico e l'installazione delle librerie di dipendenza. Descritto come eliminare la raccolta problematica e ricompilare il progetto come un modo per scaricare e reinstallare la libreria. Descritto come scaricare la libreria e installarlo nel **passa** cartella. Descritta anche una procedura più complessa per scaricare e installare i file necessari come un modo per risolvere i problemi che non possono essere risolto tramite automatizzato manualmente. 
