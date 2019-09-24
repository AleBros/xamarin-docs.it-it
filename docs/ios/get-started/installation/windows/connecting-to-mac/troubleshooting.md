---
title: Risoluzione dei problemi di connessione per un host di compilazione Xamarin.iOS
description: Questa guida presenta una procedura di risoluzione dei problemi che possono verificarsi durante l'uso del nuovo strumento di gestione connessione, inclusi i problemi di connettività e di SSH.
ms.prod: xamarin
ms.assetid: A1508A15-1997-4562-B537-E4A9F3DD1F06
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: ae8095ed0ef0ba8d0bfaf8295832bbef006d1627
ms.sourcegitcommit: 76f930ce63b193ca3f7f85f768b031e59cb342ec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71198537"
---
# <a name="connection-troubleshooting-for-a-xamarinios-build-host"></a>Risoluzione dei problemi di connessione per un host di compilazione Xamarin.iOS

_Questa guida presenta una procedura di risoluzione dei problemi che possono verificarsi durante l'uso del nuovo strumento di gestione connessione, inclusi i problemi di connettività e di SSH._

## <a name="log-file-location"></a>Percorso del file di log

- **Mac**: ~/Library/Logs/Xamarin-[MAJOR.MINOR]
- **Windows**: %LOCALAPPDATA%\Xamarin\Logs

È possibile individuare i file di log passando a **? &gt; Xamarin &gt; Comprimi log** in Visual Studio.

## <a name="wheres-the-xamarin-build-host-app"></a>Dov'è l'app host di compilazione Xamarin?

L'host di compilazione Xamarin delle versioni precedenti di Xamarin.iOS non è più necessario. Visual Studio ora distribuisce automaticamente l'agente tramite Login remoto e lo esegue in background. Non ci sono app aggiuntive da eseguire nei computer Mac o Windows.

## <a name="troubleshooting-remote-login"></a>Risoluzione dei problemi di Login remoto

> [!IMPORTANT]
> Questa procedura di risoluzione dei problemi è destinata principalmente ai problemi che si verificano durante la configurazione iniziale in un nuovo sistema.  Se una connessione che in precedenza funzionava correttamente in un ambiente specifico smette di funzionare improvvisamente o funziona in modo intermittente, è possibile nella maggior parte dei casi passare direttamente al controllo se una delle seguenti procedure è utile: 
>
> - Terminare i processi rimanenti come descritto di seguito in [Errori dovuti a processi host di compilazione esistenti nel Mac](#errors). 
> - Cancellare gli agenti, come descritto in [Cancellazione degli agenti Broker, IDB, di compilazione e Designer](#clearing) e quindi con una connessione Internet cablata connettersi direttamente tramite l'indirizzo IP come descritto in [Non è stato possibile connettersi a MacBuildHost.local. Riprovare.](#tryagain).  
> Se nessuna di queste opzioni consente di risolvere il problema, seguire le istruzioni al [passaggio 9](#stepnine) per inviare un nuovo report sui bug.

1. Controllare che le versioni di Xamarin.iOS installate nel Mac siano compatibili. Per eseguire questa operazione con Visual Studio 2017, assicurarsi di trovarsi nel canale di distribuzione **Stabile** in Visual Studio per Mac. In Visual Studio 2015 e versioni precedenti assicurarsi di trovarsi nello stesso canale di distribuzione in entrambi gli ambienti IDE.
    - In Visual Studio per Mac, passare a **Visual Studio per Mac > Controlla la disponibilità di aggiornamenti...** per visualizzare o modificare il **canale di aggiornamento**.
    - In Visual Studio 2015 e versioni precedenti, controllare il canale di distribuzione in **Strumenti > Opzioni > Xamarin > Altro**.

2. Assicurarsi che **Login remoto** sia abilitato nel Mac. Impostare l'accesso su **Solo questi utenti**e assicurarsi che l'utente Mac sia incluso nell'elenco o nel gruppo:

    [![](troubleshooting-images/troubleshooting-image1.png "Impostare l'accesso su Solo questi utenti")](troubleshooting-images/troubleshooting-image1.png#lightbox)

3. Verificare che il firewall consenta le connessioni in ingresso attraverso la porta 22, la porta predefinita per SSH:

    [![](troubleshooting-images/troubleshooting-image2.png "Verificare che il firewall consenta le connessioni in ingresso attraverso la porta 22")](troubleshooting-images/troubleshooting-image2.png#lightbox)

    Se l'opzione **Automatically allow signed software to receive incoming connections** (Consenti automaticamente al software firmato di ricevere connessioni in ingresso) è stata disabilitata, durante il processo di associazione OS X visualizza una finestra di dialogo con la richiesta di consentire a `mono-sgen` o a `mono-sgen32` di ricevere connessioni in ingresso. Assicurarsi di fare clic su **Consenti** in questa finestra di dialogo:

    [![](troubleshooting-images/troubleshooting-image4a.png "Fare clic su Consenti in questa finestra di dialogo")](troubleshooting-images/troubleshooting-image4a.png#lightbox)

4. Verificare di aver effettuato la connessione con l'account utente del Mac e che sia attiva una sessione dell'interfaccia utente grafica.

5. Assicurarsi di connettersi al Mac con il _nome utente_ anziché con _nome e cognome_. In questo modo si evita una limitazione nota dell'uso di nome e cognome, relativa ai caratteri accentati.

    Per trovare il proprio _nome utente_ è possibile eseguire il comando `whoami` in **Terminal.app**.

    Usando come esempio lo screenshot riportato di seguito, il nome dell'account è **amyb** e non **Amy Burns**:

    [![](troubleshooting-images/troubleshooting-image5a.png "Ottenere il nome dell'account dall'app terminale")](troubleshooting-images/troubleshooting-image5a.png#lightbox)

6. Controllare che l'indirizzo IP in uso per il Mac sia corretto. È possibile trovare l'indirizzo IP in **Preferenze di Sistema > Condivisione > Login remoto** nel Mac.

    [![](troubleshooting-images/troubleshooting-image17.png "Indirizzo IP nell'app Preferenze di Sistema")](troubleshooting-images/troubleshooting-image17.png#lightbox)

7. Dopo aver verificato l'indirizzo IP del Mac, eseguire il comando `ping` per tale indirizzo in `cmd.exe` in Windows:

    ```
    ping 10.1.8.95
    ```
    
    Se il ping ha esito negativo, il Mac non è _instradabile_ dal computer Windows. Questo problema deve essere risolto a livello di configurazione della rete locale tra i due computer. Assicurarsi che entrambi i computer siano all'interno della stessa rete locale.

8. Verificare quindi se il client `ssh` da OpenSSH può connettersi correttamente al Mac da Windows. Un modo per installare questo programma è tramite l'installazione di [Git per Windows](https://git-for-windows.github.io/). È quindi possibile avviare un prompt dei comandi **Git Bash** e tentare di usare `ssh` nel Mac tramite nome utente e indirizzo IP:

    ```bash
    ssh amyb@10.1.8.95
    ```

    <a name="stepnine" />

9. Se **il passaggio 8 ha esito positivo**, è possibile provare a eseguire un comando semplice, ad esempio `ls`, attraverso la connessione:

    ```bash
    ssh amyb@10.1.8.95 'ls'
    ```
    
    Questo comando dovrebbe elencare il contenuto della directory home del Mac. Se il comando `ls` funziona correttamente, ma la connessione di Visual Studio non funziona ancora, è possibile vedere la sezione [Problemi e limitazioni noti](#knownissues) relativa a complicazioni specifiche di Xamarin. Se nessuna di queste soluzioni risolve il problema, inviare un nuovo report sui bug nella Community degli sviluppatori, selezionando **Guida >Commenti e suggerimenti > Segnala un problema** in Visual Studio e collegando i log descritti in [Check the Verbose Log Files](#verboselogs) (Controlla i file di log Verbose).

10. Se **il passaggio 8 ha esito negativo**, è possibile eseguire il comando seguente nel terminale del Mac per vedere se il server SSH accetti _alcuna_ connessione:

    ```bash
    ssh localhost
    ```
    
11. Se il passaggio 8 ha esito negativo ma **il passaggio 10 ha esito positivo**, molto probabilmente il problema è che la porta 22 nell'host di compilazione Mac non è accessibile da Windows a causa della configurazione di rete. Ecco alcuni problemi di configurazione possibili:

    - Le impostazioni del firewall di OS X non consentono la connessione. Ricontrollare il passaggio 3.

        In alcuni casi può anche succedere che lo stato della configurazione per app del firewall di OS X non sia valido e che le impostazioni di Preferenze di Sistema non riflettano il comportamento effettivo. Per ripristinare il comportamento predefinito può essere utile eliminare il file di configurazione ( **/Library/Preferences/com.apple.alf.plist**) e riavviare il computer. Un modo per eliminare il file è immettere **/Library/Preferences** in **Vai &gt; Vai alla cartella** in Finder e quindi spostare il file **com.apple.alf.plist** nel Cestino.

    - Le impostazioni del firewall di uno dei router tra il Mac e il computer Windows bloccano la connessione.

    - Anche Windows non consente le connessioni in uscita per la porta remota 22. Questo è insolito. È possibile configurare Windows Firewall in modo da non consentire le connessioni in uscita, ma l'impostazione predefinita consente tutte le connessioni in uscita.

    - L'host di compilazione del Mac non consente l'accesso alla porta 22 da tutti gli host esterni tramite una regola `pfctl`. Questo è improbabile, a meno di non avere la sicurezza di aver configurato `pfctl` in passato.

12. Se il passaggio 8 ha esito negativo e  **anche il passaggio 10 ha esito negativo**, è probabile che il problema risieda nel fatto che il processo del server SSH nel Mac non è in esecuzione o non è configurato per consentire l'accesso all'utente corrente. In questo caso, ricontrollare le impostazioni di Login remoto dal passaggio 2 prima di prendere in esame possibilità più complesse.

<a name="knownissues" />

### <a name="known-issues-and-limitations"></a>Problemi e limitazioni noti

> [!NOTE]
> Il contenuto di questa sezione è valido solo se è già stata effettuata la connessione all'host di compilazione Mac con il nome utente Mac e la password tramite il client SSH OpenSSH, come descritto nei passaggi 8 e 9 precedenti.

#### <a name="invalid-credentials-please-try-again"></a>"Credenziali non valide. Riprovare."

Cause note:

- **Limitazione**: questo errore può essere visualizzato quando si tenta di accedere all'host di compilazione tramite _nome e cognome_, se il nome include un carattere accentato. Si tratta di una limitazione della [libreria SSH.NET](https://sshnet.codeplex.com/) usata da Xamarin per la connessione SSH. **Soluzione temporanea**: vedere il passaggio 5 descritto in precedenza.

#### <a name="unable-to-authenticate-with-ssh-keys-please-try-to-log-in-with-credentials-first"></a>"Non è possibile eseguire l'autenticazione con le chiavi SSH. Provare prima ad accedere con le credenziali"

Causa nota:

- **Restrizione di sicurezza SSH**: questo messaggio nella maggior parte dei casi indica che per uno dei file o delle directory nel percorso completo di **$HOME/.ssh/authorized\_keys** nel Mac sono abilitate le autorizzazioni di scrittura per membri di tipo _other_ o _group_. **Correzione comune**: eseguire `chmod og-w "$HOME"` al prompt dei comandi del terminale nel Mac. Per informazioni dettagliate sul file o sulla directory specifica che causa il problema, eseguire `grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"` nel terminale e quindi aprire il file **sshd.log** dal desktop e cercare "Authentication refused: bad ownership or modes" (Autenticazione rifiutata: proprietà o modalità non valide).

#### <a name="trying-to-connect-never-completes"></a>"Tentativo di connessione..." non giunge mai a completamento

- **Bug[ n. 52264](https://bugzilla.xamarin.com/show_bug.cgi?id=52264)** : questo problema può verificarsi in Xamarin 4.1 se la **shell di accesso** nel menu di scelta rapida **Opzioni avanzate** per l'utente Mac in **Preferenze di Sistema &gt; Utenti &amp; Gruppi** è impostata su un valore diverso da **/bin/bash**. A partire da Xamarin 4.2, questo scenario comporta invece la visualizzazione del messaggio di errore "Non è stato possibile connettersi". **Soluzione temporanea**: modificare la **shell di accesso** impostando di nuovo il valore predefinito originale **/bin/bash**.

<a name="tryagain" />

#### <a name="couldnt-connect-to-macbuildhostlocal-please-try-again"></a>"Non è stato possibile connettersi a MacBuildHost.local. Riprovare."

Cause segnalate:

- **Bug**: per alcuni utenti questo messaggio di errore è stato visualizzato insieme a un errore più dettagliato nei file di log: "Si è verificato un errore imprevisto durante la configurazione di SSH per l'utente... Session operation has timed out"(Funzionamento della sessione scaduto) durante il tentativo di accedere all'host di compilazione tramite un account utente di un dominio di Active Directory o di un altro servizio directory. **Soluzione alternativa:** accedere all'host di compilazione tramite un account utente locale.

- **Bug**: questo errore è stato visualizzato in alcuni casi quando l'utente tenta di connettersi all'host di compilazione facendo doppio clic sul nome del Mac nella finestra di dialogo di connessione. **Possibile soluzione alternativa**: [aggiungere manualmente il Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#manually-add-a-mac) usando l'indirizzo IP.

- **Bug [n. 35971](https://bugzilla.xamarin.com/show_bug.cgi?id=35971)** : questo errore si verifica in alcuni casi quando l'utente usa una connessione di rete wireless tra l'host di compilazione Mac e Windows. **Possibile soluzione alternativa**: spostare entrambi i computer in una connessione di rete cablata.

- **Bug [n. 36642](https://bugzilla.xamarin.com/show_bug.cgi?id=36642)** : questo messaggio viene visualizzato in Xamarin 4.0 ogni volta che il file **$HOME/.bashrc** nel Mac contiene un errore. A partire da Xamarin 4.1, gli errori del file con estensione **bashrc** non influiscono più sul processo di connessione. **Soluzione alternativa**: spostare il file con estensione **bashrc** in un percorso di backup o eliminare il file se non è necessario.

- **Bug [n. 52264](https://bugzilla.xamarin.com/show_bug.cgi?id=52264)** : questo errore può essere visualizzato se la **shell di accesso** nel menu di scelta rapida **Opzioni avanzate** per l'utente Mac in **Preferenze di Sistema > Utenti e Gruppi** è impostata su un valore diverso da **/bin/bash**. **Soluzione temporanea**: modificare la **shell di accesso** impostando di nuovo il valore predefinito originale **/bin/bash**.

- **Limitazione**: l'errore può verificarsi se l'host di compilazione Mac è connesso a un router che non ha accesso a Internet o se il Mac usa un server DNS nel quale si verifica un timeout a seguito di una ricerca di DNS inverso del computer Windows. Visual Studio impiega circa 30 secondi per recuperare l'impronta digitale SSH e infine non riesce a connettersi.

    **Possibile soluzione alternativa**: aggiungere "UseDNS no" al file **sshd\_config**. Assicurarsi di leggere le informazioni su questa impostazione SSH prima di modificarla. Vedere ad esempio [unix.stackexchange.com/questions/56941/what-is-the-point-of-sshd-usedns-option](http://unix.stackexchange.com/questions/56941/what-is-the-point-of-sshd-usedns-option).

    I passaggi seguenti descrivono un modo per modificare l'impostazione. Per completare la procedura, è necessario avere effettuato l'accesso a un account amministratore nel Mac.

    1. Verificare il percorso del file **sshd\_config** eseguendo `ls /etc/ssh/sshd_config` e `ls /etc/sshd_config` al prompt dei comandi del terminale. Per tutti i passaggi rimanenti, assicurarsi di usare il percorso che _non_ restituisce "Impossibile trovare il file o la directory".

        [![](troubleshooting-images/troubleshooting-image18.png "Esecuzione di `ls /etc/ssh/sshd_config` e `ls /etc/sshd_config` nel terminale")](troubleshooting-images/troubleshooting-image18.png#lightbox)

    2. Eseguire `cp /etc/ssh/sshd_config "$HOME/Desktop/"` nel terminale per copiare il file sul desktop.

    3. Aprire il file dal desktop in un editor di testo. È ad esempio possibile eseguire `open -a TextEdit "$HOME/Desktop/sshd_config"` nel terminale.

    4. Aggiungere la riga seguente nella parte inferiore del file:

        ```
        UseDNS no
        ```

    5. Rimuovere tutte le righe `UseDNS yes` per assicurarsi che la nuova impostazione sia effettiva.

    6. Salvare il file.

    7. Eseguire `sudo cp "$HOME/Desktop/sshd_config" /etc/ssh/sshd_config` nel terminale per copiare il file modificato nella posizione originaria. Immettere la password, se richiesto.

    8. Disabilitare e riabilitare **Login remoto** in **Preferenze di Sistema &gt; Condivisione &gt; Login remoto** per riavviare il server SSH.

<a name="clearing" />

#### <a name="clearing-the-broker-idb-build-and-designer-agents-on-the-mac"></a>Cancellazione degli agenti Broker, IDB, di compilazione e Designer dal Mac

Se i file di log indicano un problema durante il passaggio di installazione, caricamento o avvio per uno degli agenti Mac, è possibile provare a eliminare la cartella di cache **XMA** per forzare un nuovo caricamento degli agenti da parte di Visual Studio.

1. Eseguire il comando seguente nel terminale del Mac:

    ```bash
    open "$HOME/Library/Caches/Xamarin"
    ```
    
2. Premere CTRL+clic sulla cartella **XMA** e selezionare **Sposta nel Cestino**:

    [![](troubleshooting-images/troubleshooting-image8.png "Spostare la cartella XMA nel Cestino")](troubleshooting-images/troubleshooting-image8.png#lightbox)

3. Anche in Windows è presente una cache che può essere utile cancellare. In Windows aprire il prompt dei comandi come amministratore:

    ```
    del %localappdata%\Temp\Xamarin\XMA
    ```
    
### <a name="warning-messages"></a>Messaggi di avviso

Questa sezione illustra alcuni messaggi che possono essere visualizzati nella finestra di output e nei log e che in genere è possibile ignorare.

#### <a name="there-is-a-mismatch-between-the-installed-xamarinios--and-the-local-xamarinios"></a>"There is a mismatch between the installed Xamarin.iOS ... and the local Xamarin.iOS" (Mancata corrispondenza tra Xamarin.iOS installato... e Xamarin.iOS locale)

È possibile ignorare questo avviso, dopo aver verificato che sia Mac che Windows siano aggiornati allo stesso canale di distribuzione di Xamarin.

#### <a name="failed-to-execute-ls-usrbinmono-exitstatus1"></a>"Non è stato possibile eseguire 'ls /usr/bin/mono': Stato uscita=1"

È possibile ignorare questo messaggio, a condizione che il Mac esegua OS X 10.11 (El Capitan) o versione successiva. Questo messaggio non rappresenta un problema in OS X 10.11, perché Xamarin controlla anche **/usr/local/bin/mono**, ovvero il percorso previsto corretto per `mono` in OS X 10.11.

#### <a name="bonjour-service-macbuildhost-did-not-respond-with-its-ip-address"></a>"Il servizio Bonjour 'MacBuildHost' non ha risposto con il relativo indirizzo IP."

È possibile ignorare questo messaggio, a meno che non si noti che la finestra di dialogo di connessione non visualizza l'indirizzo IP dell'host di compilazione Mac. Se l'indirizzo IP _manca_ in tale finestra di dialogo, è comunque possibile [aggiungere manualmente il Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#manually-add-a-mac).

#### <a name="invalid-user-a-from-101895-and-input_userauth_request-invalid-user-a-preauth"></a>"Invalid user a from 10.1.8.95" (Utente a non valido da 10.1.8.95) e "input\_userauth\_request: invalid user a [preauth]" (richiesta userauth di input: utente a non valido [preauth])

Si può notare questo messaggio esaminando il file **sshd.log**. Questi messaggi fanno parte del processo di connessione normale. Vengono visualizzati perché Xamarin usa il nome utente **a** temporaneamente durante il recupero dell'_impronta digitale SSH_.

### <a name="output-window-and-log-files"></a>Finestra di output e file di log

Se Visual Studio rileva un errore durante la connessione all'host di compilazione, è possibile controllare la presenza di altri messaggi in due posizioni: la finestra di output e i file di log.

#### <a name="output-window"></a>Finestra di output

La finestra di output è il punto di partenza migliore. Questa finestra visualizza i messaggi relativi ai passaggi principali e agli errori di connessione. Per visualizzare i messaggi di Xamarin nella finestra di output:

1. Selezionare **Visualizzazione > Output** dai menu o fare clic sulla scheda **Output**.
2. Fare clic sul menu a discesa **Mostra output di**.
3. Selezionare **Xamarin**.

[![](troubleshooting-images/troubleshooting-image11.png "Selezionare Xamarin nella scheda Output")](troubleshooting-images/troubleshooting-image11.png#lightbox)

#### <a name="log-files"></a>File di registro

Se la finestra di output non include informazioni sufficienti per diagnosticare il problema, i file di log rappresentano gli elementi successivi da esaminare. I file di log contengono messaggi di diagnostica aggiuntivi che non vengono visualizzati nella finestra di output. Per visualizzare i file di log:

1. Avviare Visual Studio.

    > [!IMPORTANT]
    > Si noti che i file con estensione **svclogs** non sono abilitati per impostazione predefinita. Per accedervi è necessario avviare Visual Studio con log dettagliati, come descritto nella guida [corrispondente](~/cross-platform/troubleshooting/questions/version-logs.md#visual-studio-startup-verbose-logs). Per altre informazioni, vedere il blog [Troubleshooting Extensions with the Activity Log](https://blogs.msdn.microsoft.com/visualstudio/2010/02/24/troubleshooting-extensions-with-the-activity-log/) (Risoluzione dei problemi delle estensioni con il log attività).

2. Tentare di connettersi all'host di compilazione.

3. Dopo che Visual Studio ha rilevato l'errore di connessione, raccogliere i log da **? > Xamarin > Comprimi log**:

    [![](troubleshooting-images/troubleshooting-image12.png "Raccogliere i log da ? > Xamarin > Comprimi log")](troubleshooting-images/troubleshooting-image12.png#lightbox)

4. Quando si apre il file con estensione zip, viene visualizzato un elenco di file simile all'esempio seguente. Per gli errori di connessione, i file più importanti sono  **\*Ide.log** e  **\*Ide.svclog**. Questi file contengono gli stessi messaggi in due formati leggermente diversi. Il file con estensione **svclog**, in formato XML, è utile se si vogliono esaminare i messaggi. Il file con estensione **log**, in testo normale, è utile se si vogliono filtrare i messaggi tramite strumenti da riga di comando.

    Per esaminare tutti i messaggi, selezionare e aprire il file con estensione **svclog**:

    [![](troubleshooting-images/troubleshooting-image13.png "Selezionare il file con estensione svclog")](troubleshooting-images/troubleshooting-image13.png#lightbox)

5. Il file con estensione **svclog** viene aperto nel **visualizzatore di tracce dei servizi Microsoft**. È possibile esaminare i messaggi per thread per visualizzare gruppi di messaggi correlati. Per esaminare i messaggi per thread, prima selezionare la scheda **Grafico** e quindi fare clic sul menu a discesa **Modalità layout** e selezionare **Thread**:

    [![](troubleshooting-images/troubleshooting-image14.png "Fare clic sul menu a discesa Modalità layout e selezionare Thread")](troubleshooting-images/troubleshooting-image14.png#lightbox)

<a name="verboselogs" />

#### <a name="verbose-log-files"></a>File di log dettagliati

Se i file di log normali non offrono comunque informazioni sufficienti per diagnosticare il problema, un'ultima tecnica da tentare consiste nell'abilitare la registrazione dettagliata. I log dettagliati sono preferibili anche per i report sui bug.

1. Uscire da Visual Studio.

2. Avviare il [**prompt dei comandi per gli sviluppatori**](https://msdn.microsoft.com/library/ms229859(v=vs.110).aspx).

3. Per avviare Visual Studio con la registrazione dettagliata, al prompt dei comandi eseguire il comando seguente:

    ```bash
    devenv /log
    ```

4. Tentare di connettersi all'host di compilazione da Visual Studio.

5. Dopo che Visual Studio ha rilevato l'errore di connessione, raccogliere i log da **? > Xamarin > Comprimi log**.

6. Per copiare eventuali messaggi di log recenti dal server SSH in un file sul desktop, eseguire il comando seguente dal terminale nel Mac:

    ```bash
    grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"
   ```

Se questi file di log dettagliati non forniscono indicazioni sufficienti per risolvere il problema direttamente, [inviare un nuovo report sui bug](https://bugzilla.xamarin.com/newbug) allegando sia il file con estensione zip del passaggio 5 che il file con estensione log del passaggio 6.

## <a name="troubleshooting-automatic-mac-provisioning"></a>Risoluzione dei problemi di provisioning automatico del Mac

### <a name="ide-log-files"></a>File di log dell'IDE

Se si verificano problemi con il [provisioning automatico del Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning), esaminare i log dell'IDE di Visual Studio 2017, archiviati in **%LOCALAPPDATA%\Xamarin\Logs\15.0**.

## <a name="troubleshooting-build-and-deployment-errors"></a>Risoluzione degli errori di compilazione e distribuzione

Questa sezione tratta alcuni problemi che possono verificarsi dopo la connessione di Visual Studio all'host di compilazione.

### <a name="unable-to-connect-to-address1921681222-with-usermacuser"></a>"Unable to connect to Address='192.168.1.2:22' with User='macuser'" (Impossibile connettersi all'indirizzo='192.168.1.2:22' con l'utente 'macuser')

Cause note:

- **Funzionalità di sicurezza di Xamarin 4.1**: questo errore _si verifica_ se si esegue il downgrade alla versione 4.0 di Xamarin dopo aver usato Xamarin 4.1 o versione successiva. In questo caso l'errore è associato all'avviso "Private key is encrypted but passphrase is empty" (La chiave privata è crittografata ma la passphrase è vuota). Si tratta di una modifica _intenzionale_ dovuta a una nuova funzionalità di sicurezza in Xamarin 4.1. **Correzione consigliata**: eliminare **id\_rsa** e **id\_rsa.pub** da **%LOCALAPPDATA%\Xamarin\MonoTouch** e quindi riconnettersi all'host di compilazione Mac.

- **Restrizione di sicurezza SSH**: quando è accompagnato dall'avviso "Could not authenticate the user using the existing ssh keys" (Non è possibile autenticare l'utente con le chiavi SSH esistenti), questo messaggio nella maggior parte dei casi indica che per uno dei file o delle directory nel percorso completo di **$HOME/.ssh/authorized\_keys** nel Mac sono abilitate le autorizzazioni di scrittura per membri di tipo _other_ o _group_. **Correzione comune**: eseguire `chmod og-w "$HOME"` al prompt dei comandi del terminale nel Mac. Per informazioni dettagliate sul file o sulla directory specifica che causa il problema, eseguire `grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"` nel terminale e quindi aprire il file **sshd.log** dal desktop e cercare "Authentication refused: bad ownership or modes" (Autenticazione rifiutata: proprietà o modalità non valide).

### <a name="solutions-cannot-be-loaded-from-a-network-share"></a>Non è possibile caricare le soluzioni da una condivisione di rete

Le soluzioni vengono compilate solo se si trovano nel file system di Windows locale o in un'unità mappata.

Le soluzioni salvate in una condivisione di rete possono generare errori o non consentire la compilazione. Tutti i file con estensione **sln** usati in Visual Studio devono essere salvati nel file system di Windows locale.

A causa di questo problema viene generato l'errore seguente:

```bash
error : Building from a network share path is not supported at the moment. Please map a network drive to '\\SharedSources\HelloWorld\HelloWorld' or copy the source to a local directory.
```

Bug correlato: [n. 36195](https://bugzilla.xamarin.com/show_bug.cgi?id=36195)

### <a name="missing-provisioning-profiles-or-failed-to-create-the-a-fat-library-error"></a>Profili di provisioning mancanti o errore "Failed to create the a fat library" (Non è stato possibile creare la libreria FAT)

Avviare Xcode nel Mac e verificare che l'account sviluppatore Apple personale sia connesso e che il profilo di sviluppo iOS personale sia stato scaricato:

[![](troubleshooting-images/troubleshooting-image7.png "Verifica della connessione dell'account sviluppatore Apple personale e dell'avvenuto download del profilo di sviluppo iOS personale")](troubleshooting-images/troubleshooting-image7.png#lightbox)

### <a name="a-socket-operation-was-attempted-to-an-unreachable-network"></a>"Tentativo di operazione socket verso una rete non raggiungibile"

Cause segnalate:

- **Miglioramento [n. 36118](https://bugzilla.xamarin.com/show_bug.cgi?id=36118)** : questo errore può impedire la riuscita della compilazione se Visual Studio si connette all'host di compilazione tramite un indirizzo IPv6. Per la connessione all'host di compilazione gli indirizzi IPv6 non sono ancora supportati.

### <a name="xamarinios-visual-studio-plugin-fails-to-load-after-reinstallation-of-betaalpha-channel"></a>Non è possibile caricare il plug-in per Xamarin.iOS di Visual Studio dopo la reinstallazione del canale beta/alfa

Bug pertinente [n. 40781](https://bugzilla.xamarin.com/show_bug.cgi?id=40781).

Questo problema può presentarsi quando Visual Studio non riesce ad aggiornare la cache del componente MEF. In tal caso, può essere utile installare questa estensione di Visual Studio: [https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd](https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd)

Questa operazione cancellerà la cache del componente MEF di Visual Studio, risolvendo i problemi dovuti al danneggiamento della cache.

<a name="errors" />

### <a name="errors-due-to-existing-build-host-processes-on-the-mac"></a>Errori dovuti a processi host di compilazione esistenti nel Mac

I processi di connessioni all'host di compilazione precedenti possono talvolta interferire con il comportamento della connessione attiva corrente. Per verificare la presenza di eventuali processi esistenti, chiudere Visual Studio e quindi eseguire i comandi seguenti nel terminale nel Mac:

```bash
ps -A | grep mono
```

[![](troubleshooting-images/troubleshooting-image10.png "Esecuzione di comandi nel terminale nel Mac")](troubleshooting-images/troubleshooting-image10.png#lightbox)

Per terminare i processi esistenti usare il comando seguente:

```bash
killall mono
```

### <a name="clearing-the-mac-build-cache"></a>Cancellazione della cache di compilazione del Mac

Se durante la risoluzione di un problema di compilazione ci si vuole assicurare che il comportamento non sia correlato ad alcuno dei file di compilazione temporanei archiviati nel Mac, è possibile eliminare la cartella della cache di compilazione.

1. Eseguire il comando seguente nel terminale del Mac:

    ```bash
    open "$HOME/Library/Caches/Xamarin"
    ```

2. Premere CTRL+clic sulla cartella **mtbs** e selezionare **Sposta nel Cestino**:

    [![](troubleshooting-images/troubleshooting-image9.png "Spostare la cartella mtbs nel Cestino")](troubleshooting-images/troubleshooting-image9.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [Associazione al Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- [Video dell'agente di compilazione Mac Xamarin](https://www.youtube.com/watch?v=MBAPBtxkjFQ)
