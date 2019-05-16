---
title: Associa a Mac per lo sviluppo di Xamarin.iOS
description: Questa guida descrive come usare Associa a Mac per connettere Visual Studio 2019 a un host di compilazione Mac. Illustra come abilitare l'accesso remoto nel Mac, connettersi al Mac da Visual Studio 2019, aggiungere manualmente un host di compilazione Mac al computer Windows e molto altro.
ms.prod: xamarin
ms.assetid: 39DD7B3F-3E69-4E2A-B743-4C26AF613025
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/29/2018
ms.openlocfilehash: e77a5d7863428d743c536b5aa7b2bb88d12cffc0
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978331"
---
# <a name="pair-to-mac-for-xamarinios-development"></a>Associa a Mac per lo sviluppo di Xamarin.iOS

_Questa guida descrive come usare Associa a Mac per connettere Visual Studio 2019 a un host di compilazione Mac. Le stesse istruzioni sono valide per Visual Studio 2017._

## <a name="overview"></a>Panoramica

La compilazione di applicazioni iOS native richiede l'accesso agli strumenti di compilazione di Apple, che vengono eseguiti solo su Mac. Per questo motivo Visual Studio 2019 deve connettersi a un Mac accessibile dalla rete per compilare le applicazioni Xamarin.iOS.

La funzionalità Associa a Mac di Visual Studio 2019 consente di trovare, connettersi, eseguire l'autenticazione e memorizzare gli host di compilazione Mac, in modo che gli sviluppatori iOS che lavorano in Windows possano operare in modo produttivo.

Associa a Mac consente il flusso di lavoro di sviluppo seguente:

- Gli sviluppatori possono scrivere codice Xamarin.iOS in Visual Studio 2019.

- Visual Studio 2019 apre una connessione di rete con un host di compilazione Mac e usa gli strumenti di compilazione su tale macchina per compilare e firmare l'app iOS.

- Non è necessario eseguire un'applicazione distinta nel Mac: Visual Studio 2017 richiama in modo sicuro le compilazioni Mac tramite SSH.

- Visual Studio 2019 riceve una notifica non appena si verificano cambiamenti. Ad esempio, quando un dispositivo iOS viene collegato al Mac o diventa disponibile in rete, la barra degli strumenti iOS si aggiorna immediatamente.

- È supportata la connessione simultanea al Mac di più istanze di Visual Studio 2019.

- È possibile usare la riga di comando di Windows per compilare le applicazioni iOS.

> [!NOTE]
> 
> Prima di seguire le istruzioni riportate in questa guida, completare i passaggi seguenti:
> 
> - In un computer Windows [installare Visual Studio 2019](~/get-started/installation/windows.md)
> - In un Mac [installare Xcode](https://itunes.apple.com/us/app/xcode/id497799835?mt=12) e [Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/installation)
>    - _È necessario aprire manualmente Xcode dopo l'installazione_ per poter aggiungere componenti aggiuntivi.
>
> Se si preferisce non installare Visual Studio per Mac, Visual Studio 2019 può configurare automaticamente l'host di compilazione Mac con Xamarin.iOS e Mono.
> È comunque necessario installare ed eseguire Xcode.
> Per altre informazioni, vedere [Provisioning automatico del Mac](#automatic-mac-provisioning).

## <a name="enable-remote-login-on-the-mac"></a>Abilitare l'accesso remoto nel Mac

Per configurare l'host di compilazione Mac, abilitare per prima cosa l'accesso remoto:

1. Nel Mac aprire Preferenze di Sistema e andare al riquadro **Condivisione**.

2. Selezionare **Login remoto** nell'elenco **Servizio**.

    ![Abilitazione dell'accesso remoto](images/sharing.png "Abilitazione dell'accesso remoto")

    Assicurarsi che l'opzione sia configurata in modo da consentire l'accesso a **Tutti gli utenti** oppure che il nome utente o il gruppo Mac sia incluso nell'elenco di utenti consentiti sulla destra.

3. Se richiesto, configurare il firewall macOS.

    Se il firewall macOS è impostato per bloccare le connessioni in entrata, potrebbe essere necessario consentire a `mono-sgen` di ricevere le connessioni in entrata. In tal caso verrà visualizzato un avviso.

4. Se si trova nella stessa rete del computer Windows, a questo punto il Mac sarà individuabile da Visual Studio 2019. Se il Mac non è ancora individuabile, provare ad [aggiungere manualmente un Mac](#manually-add-a-mac) o rivedere la [guida alla risoluzione dei problemi](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md).

## <a name="connect-to-the-mac-from-visual-studio-2019"></a>Connettersi al Mac da Visual Studio 2019

Ora che l'accesso remoto è abilitato, connettere Visual Studio 2019 al Mac.

1. In Visual Studio 2019 aprire un progetto iOS esistente o crearne uno nuovo scegliendo **File > Nuovo > Progetto** e quindi selezionando un modello di progetto iOS.

2. Aprire la finestra di dialogo **Associa a Mac**. 

    - Usare il pulsante **Associa a Mac** nella barra degli strumenti iOS:

        ![Barra degli strumenti iOS, con il pulsante Associa a Mac evidenziato](images/ios-toolbar.png "Barra degli strumenti iOS, con il pulsante Associa a Mac evidenziato")

    - In alternativa, selezionare **Strumenti > iOS > Associa a Mac**.

    - La finestra di dialogo **Associa a Mac** visualizza un elenco di tutti gli host di compilazione Mac connessi in precedenza e attualmente disponibili:

        ![Finestra di dialogo Associa a Mac](images/pairtomac.png "Finestra di dialogo Associa a Mac")

3. Selezionare un Mac dall'elenco. Fare clic su **Connetti**. 

4. Immettere il nome utente e la password.

    - La prima volta che ci si connette a uno specifico Mac, viene chiesto di immettere il nome utente e la password per tale macchina:

        ![Immissione di nome utente e password per il Mac](images/auth.png "Immissione di nome utente e password per il Mac")

        > [!TIP]
        > Durante l'accesso, usare il nome utente di sistema anziché nome e cognome.

    - Associa a Mac userà queste credenziali per creare una nuova connessione SSH al Mac. Se l'operazione ha esito positivo, viene aggiunta una chiave al file **authorized_keys** nel Mac. L'accesso per le successive connessioni allo stesso Mac sarà automatico.

5. Associa a Mac configura automaticamente il Mac.

    [A partire da Visual Studio 2019 versione 15.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning), Visual Studio 2019 installa oppure aggiorna Mono e Xamarin.iOS in un host di compilazione Mac connesso in base alle esigenze (si noti che Xcode deve comunque essere installato manualmente). Vedere [Provisioning automatico del Mac](#automatic-mac-provisioning) per maggiori dettagli.

6. Controllare l'icona dello stato della connessione.

    - Quando Visual Studio 2019 è connesso a un Mac, la voce relativa al Mac nella finestra di dialogo **Associa al Mac** è contrassegnata da un'icona indicante che il Mac è attualmente connesso:

        ![Mac connesso](images/connected.png "Mac connesso")

      È possibile connettere un solo Mac alla volta.

      > [!TIP]
      > Facendo clic con il pulsante destro del mouse su un Mac qualsiasi nell'elenco **Associa a Mac**, viene visualizzato un menu di scelta rapida in cui è possibile scegliere **Connetti...**, **Rimuovi questo Mac** oppure **Disconnetti**:
      >
      > ![Menu di scelta rapida di Associa a Mac](images/contextmenu.png "Menu di scelta rapida di Associa a Mac")
      >
      > Se si sceglie **Rimuovi questo Mac**, le credenziali per il Mac selezionato verranno rimosse. Per riconnettersi a tale Mac, sarà necessario immettere di nuovo il nome utente e la password.

Se l'associazione all'host di compilazione Mac è stata eseguita correttamente, si è pronti per compilare app Xamarin.iOS in Visual Studio 2019. Rivedere la guida [Introduzione a Xamarin.iOS per Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md).

Se l'associazione al Mac non è riuscita, provare ad [aggiungere manualmente un Mac](#manually-add-a-mac) o rivedere la [guida alla risoluzione dei problemi](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md).

## <a name="manually-add-a-mac"></a>Aggiungere manualmente un Mac

Se nella finestra di dialogo **Associa a Mac** non viene visualizzato un particolare Mac, è possibile aggiungerlo manualmente:

1. Individuare l'indirizzo IP del Mac. 

    - Aprire **Preferenze di Sistema > Condivisione > Login remoto** nel Mac:

        [![Indirizzo IP del Mac in Preferenze di Sistema > Condivisione](images/sharing-ipaddress.png "Indirizzo IP del Mac in Preferenze di Sistema > Condivisione")](images/sharing.png#lightbox)

    - In alternativa, usare la riga di comando. Nel terminale eseguire questo comando: 

        ```bash
        $ ipconfig getifaddr en0
        196.168.1.8
        ```

      A seconda della configurazione di rete, potrebbe essere necessario usare un nome di interfaccia diverso da `en0`, ad esempio `en1`, `en2` e così via.

2. Nella finestra di dialogo **Associa a Mac** di Visual Studio 2019 selezionare **Aggiungi Mac**:

    [![Pulsante Aggiungi Mac nella finestra di dialogo Associa a Mac](images/addtomac.png "Pulsante Aggiungi Mac nella finestra di dialogo Associa a Mac")](images/addtomac-large.png#lightbox)

3. Immettere l'indirizzo IP del Mac e fare clic su **Aggiungi**:

    ![Immissione dell'indirizzo IP del Mac](images/enteripaddress.png "Immissione dell'indirizzo IP del Mac")

4. Immettere il nome utente e la password per il Mac:

    ![Immissione di nome utente e password](images/auth.png "Immissione di nome utente e password")

   > [!TIP]
   > Durante l'accesso, usare il nome utente di sistema anziché nome e cognome.

5. Fare clic su **Accedi** per connettere Visual Studio 2019 al Mac tramite SSH e aggiungerlo all'elenco dei computer noti.

## <a name="automatic-mac-provisioning"></a>Provisioning automatico del Mac

A partire da [Visual Studio 2019 versione 15.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning), Associa a Mac esegue automaticamente il provisioning in un computer Mac del software necessario per la compilazione di applicazioni Xamarin.iOS: Mono, Xamarin.iOS (il framework del software, non l'IDE di Visual Studio per Mac) e i vari strumenti relativi a Xcode (ma non l'ambiente Xcode).

> [!IMPORTANT]
> - Associa a Mac non può installare Xcode; è necessario installarlo manualmente nell'host di compilazione Mac perché è richiesto per lo sviluppo con Xamarin.iOS.
> - Per il provisioning automatico del Mac è necessario che l'accesso remoto sia abilitato nel Mac e il Mac deve essere accessibile al computer Windows tramite la rete. Vedere [Abilitare l'accesso remoto nel Mac](#enable-remote-login-on-the-mac) per maggiori dettagli.
> - Il provisioning automatico del Mac richiede 3 GB di spazio libero nel Mac per l'installazione di Xamarin.iOS.

Associa a Mac esegue le installazioni e gli aggiornamenti software necessari durante la [connessione al Mac](#connect-to-the-mac-from-visual-studio-2019) di Visual Studio 2017.

### <a name="mono"></a>Mono

Associa a Mac verificherà che Mono sia installato. Se non è installato, Associa a Mac scaricherà e installerà l'ultima versione stabile di Mono nel Mac. 

Lo stato di avanzamento è indicato da vari prompt, come illustrato nelle schermate seguenti (fare clic per ingrandire):

||Verifica dell'installazione|Download|Installazione di
|---|---|---|---|
|Mono|[![Installazione di Mono mancante](images/mono-missing.png "Installazione di Mono mancante")](images/mono-missing-large.png#lightbox)|[![Download di Mono](images/mono-downloading.png "Download di Mono")](images/mono-downloading-large.png#lightbox)|[![Installazione di Mono](images/mono-installing.png "Installazione di Mono")](images/mono-installing-large.png#lightbox)|

### <a name="xamarinios"></a>Xamarin.iOS

Associa a Mac aggiorna Xamarin.iOS nel Mac in modo che corrisponda alla versione installata nel computer Windows.

> [!IMPORTANT]
> Associa a Mac non effettuerà il downgrade di Xamarin.iOS nel Mac dalla versione alfa/beta alla versione stabile. Se Visual Studio per Mac è installato, impostare il [canale della versione](https://docs.microsoft.com/visualstudio/mac/update) come indicato di seguito:
> - Se si usa Visual Studio 2019, selezionare il canale aggiornamenti **Stabile** in Visual Studio per Mac.
> - Se si usa Visual Studio 2019 Preview, selezionare il canale aggiornamenti **Alfa** in Visual Studio per Mac.

Lo stato di avanzamento è indicato da vari prompt, come illustrato nelle schermate seguenti (fare clic per ingrandire):

||Verifica dell'installazione|Download|Installazione di
|---|---|---|---|
|Xamarin.iOS|[![Installazione di Xamarin.iOS mancante](images/xamios-missing.png "Installazione di Xamarin.iOS mancante")](images/xamios-missing-large.png#lightbox)|[![Download di Xamarin.iOS](images/xamios-downloading.png "Download di Xamarin.iOS")](images/xamios-downloading-large.png#lightbox)|[![Installazione di Xamarin.iOS](images/xamios-installing.png "Installazione di Xamarin.iOS")](images/xamios-installing-large.png#lightbox)|

### <a name="xcode-tools-and-license"></a>Strumenti e licenza Xcode

Associa a Mac verificherà inoltre se Xcode è stato installato e la relativa licenza accettata. Benché Associa a Mac non installi Xcode, richiede che la licenza venga accettata, come illustrato nelle schermate seguenti (fare clic per ingrandire):

||Verifica dell'installazione|Accettazione della licenza|
|---|---|---|
|Xcode|[![Installazione di Xcode mancante](images/xcode-missing.png "Installazione di Xcode mancante")](images/xcode-missing-large.png#lightbox)|[![Licenza Xcode](images/xcode-license.png "Licenza Xcode")](images/xcode-license-large.png#lightbox)|

Inoltre, Associa a Mac installerà o aggiornerà i diversi pacchetti distribuiti con Xcode. Ad esempio:

- **MobileDeviceDevelopment.pkg**
- **XcodeExtensionSupport.pkg**
- **MobileDevice.pkg**
- **XcodeSystemResources.pkg**

L'installazione di questi pacchetti avviene rapidamente e senza un prompt.

> [!NOTE]
> Questi strumenti sono distinti dagli strumenti della riga di comando Xcode, che a partire da macOS 10.9 vengono [installati con Xcode](https://developer.apple.com/library/content/technotes/tn2339/_index.html).

### <a name="troubleshooting-automatic-mac-provisioning"></a>Risoluzione dei problemi di provisioning automatico del Mac

In caso di problemi con il provisioning automatico del Mac esaminare i log dell'IDE di Visual Studio 2019, archiviati in **%LOCALAPPDATA%\Xamarin\Logs\15.0**. Questi log possono contenere messaggi di errore che consentono di diagnosticare meglio l'errore o di richiedere assistenza.

## <a name="build-ios-apps-from-the-windows-command-line"></a>Compilare app iOS dalla riga di comando di Windows

Associa a Mac supporta la compilazione di applicazioni Xamarin.iOS dalla riga di comando. Ad esempio:

```bash
C:\samples\App1>msbuild App1.sln /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhoneSimulator /p:ServerPassword=mypassword
```

I parametri passati a `msbuild` nell'esempio precedente sono:

- `ServerAddress`: indirizzo IP dell'host di compilazione Mac.
- `ServerUser`: nome utente da usare durante l'accesso all'host di compilazione Mac.
  Usare il nome utente di sistema anziché nome e cognome.
- `ServerPassword`: password da usare durante l'accesso all'host di compilazione Mac.

> [!NOTE]
> Visual Studio 2019 archivia `msbuild` nella directory seguente: **C:\Programmi (x86)\Microsoft Visual Studio\2017\\&lt;Version&gt;\MSBuild\15.0\Bin**

La prima volta che Associa a Mac accede a uno specifico host di compilazione Mac da Visual Studio 2019 o dalla riga di comando, imposta le chiavi SSH. Con queste chiavi, gli accessi futuri non richiederanno nome utente o password. Le chiavi appena create vengono archiviate in **%LOCALAPPDATA%\Xamarin\MonoTouch**.

Se il parametro `ServerPassword` viene omesso da una chiamata di compilazione dalla riga di comando, Associa a Mac tenta di accedere all'host di compilazione Mac usando le chiavi SSH salvate.

## <a name="summary"></a>Riepilogo

Questo articolo descrive come usare Associa a Mac per la connessione di Visual Studio 2019 a un host di compilazione Mac, consentendo agli sviluppatori di Visual Studio 2019 di compilare applicazioni iOS native con Xamarin.iOS.

## <a name="next-steps"></a>Passaggi successivi

- [Risoluzione dei problemi di connessione](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)
- [Video dell'agente di compilazione Mac Xamarin](https://www.youtube.com/watch?v=MBAPBtxkjFQ)
- [Introduzione a Xamarin.iOS per Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)
- [Simulatore iOS remoto per Windows](~/tools/ios-simulator/index.md)
- [Distribuzione wireless](~/ios/deploy-test/wireless-deployment.md)
