---
title: Introduzione a fastlane per iOS
description: Questa guida illustra i vari strumenti fastlane che possono essere usati per firmare il codice delle applicazioni iOS.
ms.prod: xamarin
ms.assetid: 8202C57D-22FF-4224-A5B1-AAEF12B7C106
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 68c252edecc4ebffb764c0de328ab605975471c4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-fastlane-for-ios"></a>Introduzione a fastlane per iOS

_Questa guida illustra i vari strumenti fastlane che possono essere usati per firmare il codice delle applicazioni iOS_

fastlane è un progetto open source creato per semplificare il processo, confuso e spesso noioso, di rilascio delle app iOS e Android. È costituito da diverse utilità, ognuna delle quali gestisce un aspetto specifico del rilascio delle app:

- [deliver](https://github.com/fastlane/fastlane/tree/master/deliver#readme) gestisce e carica screenshot, metadati e bundle di applicazioni su iTunes Connect.
- [produce](https://github.com/fastlane/fastlane/tree/master/produce#readme) crea un ID app in iTunes Connect e sul portale per sviluppatori. Include anche il supporto per gruppi di app e per i servizi per le applicazioni.
- [pem](https://github.com/fastlane/fastlane/tree/master/pem#readme)crea e gestisce i profili di provisioning delle notifiche push.
- [gym](https://github.com/fastlane/fastlane/tree/master/gym#readme) può essere usato per compilare e firmare un'applicazione iOS (le app Xamarin usano già MSBuild per compilare, firmare e archiviare le app).
- [cert](https://github.com/fastlane/fastlane/tree/master/cert#readme) crea e gestisce i certificati di firma del codice. 
- [sigh](https://github.com/fastlane/fastlane/tree/master/sigh#readme) crea e gestisce i profili di provisioning.
- [match](https://github.com/fastlane/fastlane/tree/master/match#readme) crea e gestisce i certificati e i profili e li archivia in un repository GIT in modo che possano essere sincronizzati in un team di sviluppo.

fastlane può essere usato in diversi modi: tramite i comandi di Terminale, tramite mezzi basati su file o mediante variabili di ambiente per compilazioni con integrazione continua. 

Questa guida illustra nello specifico la configurazione di un dispositivo per lo sviluppo con le app iOS e descrive in particolare le utilità **cert**, **sigh** e **match**. 

I contenuti forniti possono essere usati come aiuto per la distribuzione di app, inclusa l'automazione completa del processo in un server di integrazione continua. Tuttavia, è importante sottolineare che fastlane è un produttore di terze parti che sviluppa strumenti per il supporto di progetti Xcode, pertanto alcuni strumenti o comandi come `fastlane init` potrebbero non funzionare nel modo previsto con i file csproj. Per altre informazioni sull'uso di fastlane, su strumenti aggiuntivi o sul rilascio di app per Android con fastlane, visitare [https://fastlane.tools/](https://fastlane.tools/)

<a name="Installation" />

## <a name="installation"></a>Installazione

1. Verificare che gli strumenti della riga di comando Xcode siano installati nel computer macOS. Per installare gli strumenti, usare il comando `xcode-select --install` in Terminale. Se sono già installati, verrà visualizzato l'errore seguente:

    ```bash
    error: command line tools are already installed, use "Software Update" to install updates
    ```

2. Scaricare gli strumenti di fastlane da [https://download.fastlane.tools](https://download.fastlane.tools). 

    > [!NOTE]
    > È possibile installare gli strumenti fastlane da Homebrew usando `brew cask install fastlane` o tramite Rubygems (2.0 o versione successiva) usando `sudo gem install fastlane –NV`. Tuttavia l'uso del programma di installazione assicura che siano disponibili le dipendenze corrette. 

3. Installare fastlane decomprimendo il file, quindi fare doppio clic sul file eseguibile `install`. Se viene visualizzato un messaggio di errore che informa che non è possibile aprire il file perché proviene da uno sviluppatore non identificato, scegliere OK e seguire questa procedura:
    - Premere CTRL e fare clic sul file eseguibile `install`. Verrà visualizzata la finestra di dialogo seguente:

      ![](images/fastlane-image12.png "Finestra di dialogo di installazione")
    
    - Scegliere OK per avviare l'installazione degli strumenti fastlane.

4. Verrà visualizzato il prompt di Terminale seguente. Premere `y`:

  ![](images/fastlane-image13.png "Prompt di Terminale")
 
4. Eseguire `which fastlane` prima di usare fastlane per la prima volta. Il percorso dovrebbe essere simile al seguente: 

    ```bash
    /Users/[user]/.fastlane/bin
    ```

5. Se il percorso corrisponde a quello riportato sopra, si è pronti per iniziare.

     In caso contrario, in macOS aprire `.bash_profile`, che è un file di testo non crittografato nascosto nella home directory, con il comando seguente:

    ```bash
    open ~/.bash_profile
    ```

6. Aggiungere la variabile di ambiente PATH seguente e salvarla: 

    ```bash
    export PATH="$HOME/.fastlane/bin:$PATH"
    ```

7.  Eseguire di nuovo `which fastlane` per verificare che il percorso sia analogo a `/Users/[user]/.fastlane/bin`


## <a name="updating-fastlane"></a>Aggiornamento di fastlane

fastlane è un progetto open source molto attivo che rilascia regolarmente nuove versioni. Quando è disponibile una nuova versione di fastlane, si viene avvisati quando si esegue un qualsiasi comando fastlane:

[![](images/fastlane-image0.png "Richiesta di aggiornamento di fastlane")](images/fastlane-image0.png#lightbox)


Per eseguire l'aggiornamento a una nuova versione di fastlane, scaricare il pacchetto più recente da [qui](https://download.fastlane.tools) e fare doppio clic sul pacchetto di installazione per eseguirlo:

[![](images/fastlane-image0a.png "Esecuzione del pacchetto di installazione")](images/fastlane-image0a.png#lightbox)


## <a name="contents"></a>Sommario

Questa serie di guide presenta alcuni degli strumenti usati da fastlane per firmare il codice dell'app iOS in preparazione allo sviluppo o alla distribuzione. Gli strumenti attualmente interessati sono:

- [cert](~/ios/deploy-test/provisioning/fastlane/cert.md)
- [sigh](~/ios/deploy-test/provisioning/fastlane/sigh.md)
- [match](~/ios/deploy-test/provisioning/fastlane/match.md)

cert e sigh creano e gestiscono i certificati di firma e i profili di provisioning in un computer locale. match porta questo processo un passo avanti. Crea e gestisce i certificati e i profili di provisioning e li archivia in un repository GIT, in modo che siano accessibili a tutti i membri di un team di sviluppo. Leggere ogni sezione per informazioni sul funzionamento di questi strumenti e su come usarli.

## <a name="using-fastlane-tools-with-xamarin"></a>Uso degli strumenti fastlane con Xamarin

Dopo aver creato e firmato i profili di identità e provisioning con fastlane, l'impostazione delle opzioni di firma del bundle in Visual Studio per Mac dovrebbe essere semplice, purché i certificati e le chiavi private nel keychain di macOS e i profili di provisioning siano nella cartella `~/Library/MobileDevice/Provisioning Profiles`.

Per impostare le opzioni di firma del codice per un'applicazione Xamarin.iOS, fare clic con il pulsante destro del mouse sul nome del progetto, scegliere **Opzioni progetto > Compilazione > Firma del bundle iOS**, quindi impostare l'identità di firma e il profilo di provisioning in modo esplicito, come illustrato di seguito:

[![](images/fastlane-image11.png "Impostare l'identità di firma e il profilo di provisioning in modo esplicito")](images/fastlane-image11.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione su fastlane](https://fastlane.tools/)
- [Documentazione sulla firma del codice fastlane](https://docs.fastlane.tools/codesigning/getting-started/)
- [Code Signing guide](https://codesigning.guide/) (Guida alla firma del codice)
