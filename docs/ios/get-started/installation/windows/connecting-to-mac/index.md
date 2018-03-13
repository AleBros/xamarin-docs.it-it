---
title: Connessione al Mac
description: "Xamarin.iOS per Visual Studio consente agli sviluppatori di creare, compilare ed eseguire il debug di applicazioni iOS in un computer Windows tramite l'ambiente di sviluppo integrato (IDE) di Visual Studio. Questa guida illustra le funzionalità di Xamarin.iOS per Visual Studio e spiega come viene eseguita la connessione all'host di compilazione Mac."
ms.topic: article
ms.prod: xamarin
ms.assetid: 39DD7B3F-3E69-4E2A-B743-4C26AF613025
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: c60927593f062c8ac9694d889ffbf581c09bab82
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="connecting-to-the-mac"></a>Connessione al Mac

_Xamarin.iOS per Visual Studio consente agli sviluppatori di creare, compilare ed eseguire il debug di applicazioni iOS in un computer Windows tramite l'ambiente di sviluppo integrato (IDE) di Visual Studio. Questa guida illustra le funzionalità di Xamarin.iOS per Visual Studio e spiega come viene eseguita la connessione all'host di compilazione Mac._

Visual Studio si connette al Mac tramite SSH, che offre diversi vantaggi, tra cui:

- Visual Studio può avviare e controllare direttamente l'agente di compilazione. Non esiste più un'applicazione visibile all'utente che richiede l'avvio e l'arresto manuali.

- La nuova funzionalità Gestione connessioni in Visual Studio individua, autentica e memorizza l'host di compilazione Mac.

- Poiché tutte le comunicazioni sono incanalate attraverso un tunnel SSH sicuro, è necessaria solo una connessione alla porta 22.

- Visual Studio riceve una notifica appena si verificano cambiamenti. Ad esempio, quando un dispositivo iOS viene collegato, la barra degli strumenti si aggiorna immediatamente.

- È supportata la connessione simultanea di più istanze di Visual Studio.

- La connessione non interferirà con lo sviluppo. Verrà richiesta una connessione al Mac solo quando occorre eseguire un'operazione per cui il Mac è necessario, come l'esecuzione del debug o l'uso di iOS Designer.

La connessione al Mac è costituita da più processi per le diverse parti del suo funzionamento che sono controllate da un broker, ad esempio l'agente iOS Designer e l'agente di compilazione. Questo broker è controllato e aggiornato da Visual Studio e riavvia automaticamente gli eventuali processi indipendenti che subiscono un arresto anomalo.

Il diagramma seguente offre una semplice panoramica del flusso di lavoro di sviluppo di Xamarin.iOS:

[![Flusso di lavoro di sviluppo di iOS](images/xma2.png)](images/xma2.png#lightbox)

> [!IMPORTANT]
>  Visual Studio avvia un processo MSBuild separato per compilare i progetti. Questo processo crea una nuova connessione al Mac, quindi esistono effettivamente due connessioni SSH da Windows a Mac durante la compilazione di Visual Studio. La compilazione dalla [riga di comando](#commandline) crea un unico processo MSBuild. Per semplificare il diagramma, tutte le connessioni sono rappresentate da una sola freccia.

## <a name="requirements"></a>Requisiti

Xamarin.iOS per Visual Studio svolge una funzione straordinaria: consente agli sviluppatori di creare, compilare ed eseguire il debug di applicazioni iOS in un computer Windows tramite l'ambiente di sviluppo integrato (IDE) di Visual Studio. Ma non può fare tutto questo da solo. Le applicazioni iOS non possono infatti essere create senza il compilatore Apple e non possono essere distribuite senza i certificati e gli strumenti di firma del codice Apple. Questo significa che l'installazione di Xamarin.iOS per Visual Studio richiede una connessione a un computer Mac OS X connesso a una rete (detto *host* o *host di compilazione*) per poter eseguire queste attività automaticamente. Una volta configurati, gli strumenti Xamarin semplificheranno al massimo il processo.

### <a name="system-requirements"></a>Requisiti di sistema

I requisiti di sistema sono disponibili nella guida [Installazione di Xamarin.iOS in Windows](~/ios/get-started/installation/windows/index.md#system-requirements)


#### <a name="compatibility"></a>Compatibilità

> [!IMPORTANT]
>  Il computer Windows deve usare la stessa versione di Xamarin.iOS del Mac a cui è connesso. Per verificare che sia così:                                                    
>                                                                                                                 
> - **Visual Studio 2015 e versioni precedenti**: assicurarsi di trovarsi nello stesso [canale aggiornamenti](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/) di Visual Studio per Mac.
>                                                                                                                 
> - **Visual Studio 2017, versione di rilascio**: assicurarsi di trovarsi nel canale **stabile** di Visual Studio per Mac.
>                                                                                                                 
> - **Visual Studio 2017, versione di rilascio**: assicurarsi di trovarsi nel canale **alfa** di Visual Studio per Mac. Visual Studio non verifica che Xamarin.iOS SDK e Xcode esistano e abbiano versioni compatibili.
>   Questa verifica viene fatta dall'agente di compilazione, con conseguenti errori di compilazione, e dall'agente iOS Designer, con conseguenti errori di progettazione.

### <a name="connecting-to-the-mac"></a>Connessione al Mac

#### <a name="mac-setup"></a>Configurazione del Mac

Per configurare l'host Mac è necessario abilitare la comunicazione tra l'estensione Xamarin per Visual Studio e il Mac. A questo scopo occorre attivare **Login remoto** sul Mac seguendo questa procedura:

1. Aprire *Spotlight* (**⌘-BARRA SPAZIATRICE**), cercare *Login remoto* e selezionare il risultato *Condivisione*. Verrà aperta la finestra *Preferenze di Sistema* con il riquadro *Condivisione*:

   [![Ricerca di Login remoto in Spotlight](images/spotlight.png)](images/spotlight.png#lightbox)

2. Selezionare l'opzione *Login remoto* nell'elenco *Servizio* sulla sinistra per consentire a Xamarin per Visual Studio di connettersi al Mac:

   [![Selezionare l'opzione Login remoto nell'elenco Servizio](images/sharing.png)](images/sharing.png#lightbox)

3. Assicurarsi che *Login remoto* sia impostato per consentire l'accesso a *Tutti gli utenti* oppure che il nome utente o il gruppo Mac sia incluso nell'elenco di utenti consentiti sulla destra.

Inoltre, se il firewall di OS X è impostato in modo da bloccare le applicazioni firmate per impostazione predefinita, potrebbe essere necessario consentire a `mono-sgen` di ricevere connessioni in entrata. In tal caso verrà visualizzata una finestra di dialogo con un avviso.

A questo punto, purché nel Mac sia presente una sessione aperta, il Mac dovrebbe essere individuabile da Visual Studio se si trova nella stessa rete.

Visual Studio avvierà e arresterà l'agente nel Mac, quindi l'utente non ha bisogno di eseguire altro.

### <a name="windows-setup"></a>Configurazione di Windows

Assicurarsi di [installare](~/ios/get-started/installation/windows/index.md) gli strumenti Xamarin nel computer Windows.

### <a name="connecting-to-the-mac-build-host"></a>Connessione all'host di compilazione Mac

È possibile connettersi all'host di compilazione Mac in due modi:

Sulla barra degli strumenti iOS:

[![Barra degli strumenti iOS](images/image1.png)](images/image1.png#lightbox)

Oppure passando a **Strumenti > Opzioni** in Visual Studio, selezionando **Xamarin > Impostazioni iOS** e facendo clic sul pulsante **Trova Xamarin Mac Agent**:

[![Trova Xamarin Mac Agent](images/image2.png)](images/image2.png#lightbox)

In entrambi i modi si passerà alla finestra di dialogo **Xamarin Mac Agent**, illustrata di seguito:

[![Finestra di dialogo Xamarin Mac Agent](images/image3.png)](images/image3.png#lightbox)

La finestra di dialogo contiene un elenco di tutti i computer che sono stati precedentemente connessi e sono memorizzati come computer noti o dei computer che sono disponibili per *Login remoto*.

Selezionare un Mac facendo doppio clic su di esso per stabilire la connessione. La prima volta che ci si connette a un Mac, viene chiesto di immettere le credenziali utente del Mac per consentire la connessione remota:

[![Immettere le credenziali utente del Mac](images/image4.png)](images/image4.png#lightbox)

L'agente userà queste credenziali per creare una nuova connessione SSH al Mac. Se la connessione riesce, viene creata una chiave SSH, che viene [registrata](#commandline) nel file `authorized_keys` nel Mac. Per le connessioni successive l'agente userà il nome utente e il file della chiave per connettersi all'host di compilazione noto a cui è stata stabilita la connessione più recente.

> [!NOTE]
>  **Nota**: è necessario usare il _nome utente_ e non il _nome e cognome_ quando si immettono le credenziali.  Per trovare il proprio nome utente, usare il comando `whoami` in Terminale.  Usando come esempio lo screenshot riportato di seguito, il nome dell'account è **amyb** e non **Amy Burns**:
>
> ![Individuazione del nome utente nell'app Terminale](images/image5.png)

Quando una connessione viene stabilita correttamente, viene visualizzata nella finestra di dialogo Selezione host con un'icona **connesso** accanto, come illustrato nella figura seguente:

[![Finestra di dialogo Selezione host con un'icona connesso accanto](images/image6.png)](images/image6.png#lightbox)

È possibile connettere un solo Mac alla volta.

Facendo clic con il pulsante destro del mouse su un qualsiasi computer nell'elenco, che sia connesso o meno, viene visualizzato un menu di scelta rapida in cui è possibile scegliere **Connetti**, **Disconnetti** o **Rimuovi questo Mac** in base alle esigenze:

[![Comandi Connetti, Disconnetti e Rimuovi questo Mac del menu di scelta rapida](images/image7.png)](images/image7.png#lightbox)

Se si sceglie **Rimuovi questo Mac**, sarà necessario immettere di nuovo le credenziali per riconnettersi.

<a name="manual-add"/>

### <a name="manually-adding-a-mac"></a>Aggiunta manuale di un Mac

In alcune circostanze si può avere l'esigenza di aggiungere manualmente un Mac, se il suo nome mDNS non compare nell'elenco nella finestra di dialogo Selezione host. A tale scopo, seguire questa procedura:

1. Individuare l'indirizzo IP del Mac selezionando **Preferenze di Sistema > Condivisione > Login remoto** nel Mac:

   [![Indirizzo IP del Mac in Preferenze di Sistema](images/image8.png)](images/image8.png#lightbox)

   Oppure, se si preferisce usare la riga di comando, è possibile trovare l'indirizzo IP immettendo `ipconfig getifaddr en0` in Terminale (si noti che a seconda del tipo di connessione la variabile potrebbe essere `en1`, `en2` e così via):

   [![Indirizzo IP nell'app Terminale](images/image9.png)](images/image9.png#lightbox)

2. Tornare a Visual Studio, quindi nella finestra di dialogo Selezione host selezionare **Aggiungi Mac**:

   [![Finestra di dialogo Selezione host](images/image10.png)](images/image10.png#lightbox)

3. Immettere l'indirizzo IP del Mac nella finestra di dialogo Aggiungi Mac e fare clic su **Aggiungi**:

   [![Immettere l'indirizzo IP del Mac nella finestra di dialogo Aggiungi Mac](images/image11.png)](images/image11.png#lightbox)

4. Infine, immettere il nome utente (non il nome e cognome) dell'account amministratore del Mac e la password corrispondente:

   [![Immettere nome utente e password](images/image12.png)](images/image12.png#lightbox)

Dopo aver fatto clic su **Accedi**, Visual Studio si connette al Mac tramite SSH e lo aggiunge come computer noto.

<a name="commandline"/>

### <a name="command-line-support"></a>Supporto della riga di comando

L'agente supporta anche la compilazione di una configurazione Xamarin.iOS dalla riga di comando.  Per usarla è necessario passare i parametri obbligatori seguenti a MSBuild:

- `ServerAddress`: indirizzo IP del server Mac.

- `ServerUser`: nome utente (non nome e cognome) da usare per accedere al server Mac.

- `ServerPassword`: password usata per accedere all'host Mac (facoltativa).

Il parametro `ServerPassword` non è obbligatorio.

La prima volta che viene passata una password, usando Visual Studio o la riga di comando, per quella particolare configurazione Windows, Mac e utente, viene generata una coppia di chiavi, che viene archiviata nel computer Windows per uso futuro. Sarà disponibile in **%localappdata%\Xamarin\MonoTouch\id_rsa**.  Se non si passa il parametro `ServerPassword`, per l'autenticazione verrà usato il file di chiave `id_rsa`.

Di seguito è riportato un esempio di comando per stabilire la connessione al Mac 10.211.55.2 usando l'account **xamUser** con la password **mypassword**:

```bash
C:\samples\App1>msbuild App1.sln /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhoneSimulator /p:ServerPassword=mypassword
```

### <a name="summary"></a>Riepilogo

In questo articolo sono state presentate le interazioni tra Visual Studio e gli strumenti di compilazione e progettazione iOS nel Mac, che consentono di compilare app di Xamarin.iOS con Visual Studio.

### <a name="related-links"></a>Collegamenti correlati

- [Installazione](~/ios/get-started/installation/windows/index.md)
- [Risoluzione dei problemi di connessione](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)
- [Connessione di un Mac all'ambiente di Visual Studio in uso con XMA (video)](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)
