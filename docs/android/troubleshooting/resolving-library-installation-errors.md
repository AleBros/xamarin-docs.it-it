---
title: Risoluzione degli errori di installazione di librerie
description: In alcuni casi, è possibile che si verifichino errori durante l'installazione delle librerie di supporto Android. Questa guida offre soluzioni alternative per alcuni errori comuni.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/14/2018
ms.openlocfilehash: e99e12aa73374cc8145fad0eb5aad7f3259e0ca2
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292838"
---
# <a name="resolving-library-installation-errors"></a>Risoluzione degli errori di installazione di librerie

_In alcuni casi, è possibile che si verifichino errori durante l'installazione delle librerie di supporto Android. Questa guida offre soluzioni alternative per alcuni errori comuni._

## <a name="overview"></a>Panoramica

Durante la compilazione di un progetto di app Novell. Android, è possibile che vengano generati errori di compilazione quando Visual Studio o Visual Studio per Mac tentano di scaricare e installare le librerie di dipendenze. Molti di questi errori sono causati da problemi di connettività di rete, danneggiamento dei file o problemi di controllo delle versioni. Questa guida descrive gli errori più comuni di installazione della libreria di supporto e fornisce i passaggi per risolvere questi problemi e ricreare il progetto dell'app.

## <a name="errors-while-downloading-m2repository"></a>Errori durante il download di m2Repository

È possibile che vengano visualizzati errori **m2repository** quando si fa riferimento a un pacchetto NuGet delle librerie di supporto Android o dei servizi Google Play. Il messaggio di errore è simile al seguente:

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

Questo esempio è per **android\_m2repository\_R16**, ma è possibile che venga visualizzato lo stesso messaggio di errore per una versione diversa, ad esempio **android\_m2repository\_r18** o **Android\_m2repository\_R25**.

### <a name="automatic-recovery-from-m2repository-errors"></a>Ripristino automatico da errori di m2repository

Spesso questo problema può essere risolto eliminando la libreria problematica e ricompilando in base a questi passaggi:

1. Passare alla directory della raccolta di supporto nel computer:

    - In Windows le librerie di supporto si trovano in **C:\\utenti\\_nomeutente_\\AppData\\locale\\Novell**.

    - In Mac OS X le librerie di supporto si trovano in **/utenti/_nomeutente_/.local/share/Xamarin**.

2. Individuare la libreria e la cartella della versione corrispondenti al messaggio di errore. Ad esempio, la libreria e la cartella della versione per il messaggio di errore precedente si trovano in **Android. support. v4\\22.2.1**:

    [percorso della cartella di esempio ![per la libreria di supporto 22.2.1](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. Eliminare il contenuto della cartella Version. Assicurarsi di rimuovere il file **zip** , nonché il **contenuto** e le sottodirectory **incorporate** in questa cartella. Per il messaggio di errore di esempio illustrato in precedenza, è necessario eliminare i file e le sottodirectory mostrati in questa schermata (**Content**, **embedded**e **android_m2repository_r16. zip**):

    [![contenuto di esempio della cartella della libreria di supporto 22.2.1](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   Si noti che è importante eliminare l' *intero* contenuto della cartella. Anche se questa cartella potrebbe contenere inizialmente il file "Missing" di **android\_m2repository\_R16. zip** , il file potrebbe essere stato scaricato parzialmente o danneggiato.

4. Ricompilare il progetto &ndash; in modo che il processo di compilazione scarichi la libreria mancante.

Nella maggior parte dei casi, questa procedura consente di risolvere l'errore di compilazione e di continuare. Se l'eliminazione di questa libreria non risolve l'errore di compilazione, è necessario scaricare e installare manualmente il file **android\_m2repository\_r_nn_. zip** , come descritto nella sezione successiva.

### <a name="manually-downloading-m2repository"></a>Download manuale di m2repository

Se si è provato a usare i passaggi di ripristino automatico precedenti e sono ancora presenti errori di compilazione, è possibile scaricare manualmente **android\_m2repository\_r_nn_ file zip** (usando un Web browser) e installarlo seguendo questa procedura. Questa procedura è utile anche se non si ha accesso a Internet nel computer di sviluppo, ma è possibile scaricare l'archivio usando un computer diverso.

1. Scaricare il file **android\_m2repository\_r_nn_. zip** che corrisponde al messaggio di errore &ndash; i collegamenti sono disponibili nell'elenco seguente (insieme all'hash MD5 corrispondente dell'URL di ogni collegamento):

    - [android\_m2repository\_R33. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    - [android\_m2repository\_R32. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    - [android\_m2repository\_R31. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    - [android\_m2repository\_R30. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    - [android\_m2repository\_R29. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC653030E7D695C41

    - [android\_m2repository\_R28. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374AA0223

    - [android\_m2repository\_R27. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    - [android\_m2repository\_R26. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    - [android\_m2repository\_R25. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    - [android\_m2repository\_R24. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    - [android\_m2repository\_R23. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    - [android\_m2repository\_R22. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    - [android\_m2repository\_R21. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    - [android\_m2repository\_R20. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    - [android\_m2repository\_R19. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE39E9460B8A5851A

    - [android\_m2repository\_R18. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    - [android\_m2repository\_R17. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    - [android\_m2repository\_R16. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    Se l'archivio **m2repository** non viene visualizzato in questa tabella, è possibile creare l'URL di download anteponendo `https://dl-ssl.google.com/android/repository/` al nome del **m2repository** da scaricare. Ad esempio, usare **https://dl-ssl.google.com/android/repository/android\_m2repository\_r10.zip** per scaricare **android\_m2repository\_R10. zip**.

2. Rinominare il file con l'hash MD5 corrispondente dell'URL di download, come illustrato nella tabella precedente. Ad esempio, se è stato scaricato **android\_m2repository\_R25. zip**, rinominarlo in **0B3F1796C97C707339FB13AE8507AF50. zip**. Se l'hash MD5 per l'URL di download del file scaricato non viene visualizzato nella tabella, è possibile usare un [Generatore MD5 online](http://www.webconfs.com/online-md5-generator.php) per convertire l'URL in una stringa di hash MD5.

3. Copiare il file nella cartella Novell **zip** :

    - In Windows questa cartella si trova in **C:\\users\\***username***\\AppData\\Local\\Novell\\zip**.

    - In Mac OS X, questa cartella si trova in **/utenti/***nomeutente***/.local/share/Xamarin/Zips**.

    Ad esempio, lo screenshot seguente illustra il risultato quando **android\_m2repository\_R16. zip** viene scaricato e rinominato nell'hash MD5 del relativo URL di download in Windows:

    [![esempio del repository R16. zip che viene rinominato in 0595E577D19D31708195A83087881EE6. zip](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)

Se questa procedura non risolve l'errore di compilazione, è necessario scaricare manualmente il file **android\_m2repository\_r_nn_. zip** , decomprimerlo e installarne il contenuto, come descritto nella sezione successiva.

### <a name="manually-downloading-and-installing-m2repository-files"></a>Download e installazione manuale dei file m2repository

Il processo completamente manuale per il ripristino da errori **m2repository** comporta il download del file **android\_m2repository\_r_nn_ file zip** (tramite un Web browser), la relativa decompressione e la copia del contenuto nella directory della libreria di supporto nel computer. Nell'esempio seguente verrà ripristinato il messaggio di errore seguente:

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

Per scaricare **m2repository** e installarne il contenuto, attenersi alla procedura seguente:

1. Elimina il contenuto della cartella della libreria corrispondente al messaggio di errore. Ad esempio, nel messaggio di errore precedente si elimina il contenuto di **C:\\utenti\\***nomeutente***\\AppData\\locale\\Novell\\Android. Support. v4\\23.1.1.0**.
    Come descritto in precedenza, è necessario eliminare l'intero contenuto della directory:

    [![l'eliminazione di contenuto, incorporati e android_m2repository cartelle dalla cartella 23.1.1.0](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2. Scaricare il file **android\_m2repository\_r_nn_. zip** da Google che corrisponde al messaggio di errore. per i collegamenti, vedere la tabella nella sezione precedente.

3. Estrarre questo archivio **zip** in qualsiasi percorso, ad esempio il desktop. Questa operazione dovrebbe creare una directory che corrisponde al nome dell'archivio **zip** . All'interno di questa directory, dovrebbe essere presente una sottodirectory denominata **m2repository**:

    [![cartella m2repository trovata nell'archivio zip Estratto](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4. Nella directory della libreria con versione eliminata nel passaggio 1, ricreare il **contenuto** e le sottodirectory **incorporate** . Ad esempio, nella schermata seguente vengono illustrati il **contenuto** e le sottodirectory **incorporate** create nella cartella **23.1.1.0** per **Android\_m2repository\_R25. zip**:

    [![creare contenuto e cartelle incorporate nella cartella 23.1.1.0](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5. Copiare **m2repository** dal file con **estensione zip** Estratto nella directory del **contenuto** creata nel passaggio precedente:

    [![screenshot di m2repository copiato nella cartella 23.1.1.0/Content](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6. Nella directory con **estensione zip** estratta passare a **m2repository\\com\\android\\support\\support-V4** e aprire la cartella corrispondente al numero di versione creato in precedenza (in questo esempio, **23.1.1**):

    [![elenco di esempio di file contenuti nella cartella Support-V4/23.1.1](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7. Copiare tutti i file di questa cartella nella directory **incorporata** creata nel passaggio 4:

    [![esempio di file copiati nella cartella 23.1.1.0/Embedded](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8. Verificare che tutti i file vengano copiati. La directory **Embedded** dovrebbe ora contenere file come **. jar**, **. AAR**e **. POM**.

9. Decomprimere il contenuto di tutti i file con **estensione AAR** estratti nella directory **incorporata** . In Windows aggiungere un'estensione **zip** al file **. AAR** , aprirla e copiare il contenuto nella directory **incorporata** .
    In macOS, decomprimere il file con **estensione AAR** usando il comando **unzip** nel terminale (ad esempio, **unzip file. AAR**).

A questo punto, sono stati installati manualmente i componenti mancanti e il progetto dovrebbe essere compilato senza errori. In caso contrario, verificare di aver scaricato la versione dell'archivio **m2repository** **. zip** che corrisponde esattamente alla versione nel messaggio di errore e verificare di aver installato il contenuto nei percorsi corretti, come descritto nei passaggi precedenti.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come eseguire il ripristino da errori comuni che possono essere generati durante il download e l'installazione automatici delle librerie di dipendenze. Viene descritto come eliminare la libreria problematica e ricompilare il progetto come un modo per scaricare nuovamente e reinstallare la libreria.
Viene descritto come scaricare la libreria e installarla nella cartella **zip** . È stata inoltre descritta una procedura più complessa per scaricare e installare manualmente i file necessari per aggirare i problemi che non possono essere risolti tramite i mezzi automatici.
