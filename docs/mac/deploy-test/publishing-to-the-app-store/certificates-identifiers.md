---
title: Certificati e identificatori in Xamarin.Mac
description: Questa guida descrive come creare i certificati e gli identificatori necessari per pubblicare un'app Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 393d0066-7f6f-4ac3-a48d-4b5db65bc4cd
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: b86f731009380db7e9e8980114272ddd38d2f0da
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792875"
---
# <a name="certificates-and-identifiers-in-xamarinmac"></a>Certificati e identificatori in Xamarin.Mac

_Questa guida descrive come creare i certificati e gli identificatori necessari per pubblicare un'app Xamarin.Mac._

## <a name="certificates-and-identifiers"></a>Certificati e identificatori

Visitare [Apple Developer Member Center](http://developer.apple.com) per configurare il Mac per lo sviluppo. Il menu principale è riportato di seguito:

[![Apple Developer Member Center](certificates-identifiers-images/devcenter01.png "Apple Developer Member Center")](certificates-identifiers-images/devcenter01-large.png#lightbox)

Fare clic sul collegamento **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili):

[![Selezione di certificati, identificatori e profili](certificates-identifiers-images/devcenter02.png "Selezione di certificati, identificatori e profili")](certificates-identifiers-images/devcenter02-large.png#lightbox)

Quindi fare clic sul **collegamento Certificates** (Certificati) nella sezione **Mac Apps** (App Mac):

[![Selezione del collegamento Certificates (Certificati)](certificates-identifiers-images/devcenter03.png "Selezione del collegamento Certificates (Certificati)")](certificates-identifiers-images/devcenter03-large.png#lightbox)

Fare clic sul collegamento **All** (Tutti) e fare clic sul pulsante **+**:

[![Selezione di All (Tutti) e aggiunta di un nuovo elemento](certificates-identifiers-images/certif01.png "Selezione di All (Tutti) e aggiunta di un nuovo elemento")](certificates-identifiers-images/certif01-large.png#lightbox)

Da qui scaricare i **certificati intermedi** (Worldwide Developer Relations Certificate Authority e Developer ID Certificate Authority), se necessario. Tali certificati dovrebbero comunque essere automaticamente configurati per lo sviluppatore da Xcode.

Nella parte restante di questa sezione vengono illustrati i quattro passaggi necessari per completare la configurazione di un account Mac Developer.

-   **Registrare l'ID app Mac**: lo sviluppatore dovrà seguire la procedura seguente per ogni applicazione scritta.
-   **Registrare sistemi macOS**: questo passaggio è richiesto solo per aggiungere computer con cui eseguire i test.
-   **Creare certificati**: questo passaggio è richiesto solo una prima volta per la configurazione dei certificati e successivamente per il rinnovo.
-   **Creare un profilo di provisioning**: lo sviluppatore dovrà seguire questa procedura per ogni nuova applicazione scritta e in caso di aggiunta di nuovi sistemi.

Fare clic sul collegamento **Overview** (Panoramica) in alto a sinistra nella pagina per tornare a questo menu in qualsiasi momento.

### <a name="register-mac-app-id"></a>Registrare l'ID app Mac

Lo sviluppatore deve registrare un ID app per ogni applicazione scritta. Usare la procedura seguente per creare una voce per un'app di esempio di base chiamata "MacWriter".

1. Immettere una descrizione in **App ID Description** (Descrizione ID app) e selezionare gli eventuali servizi richiesti dall'applicazione in **App Services** (Servizi app): 

    [![Immissione della descrizione e selezione dei servizi app](certificates-identifiers-images/devcenter04.png "Immissione della descrizione e selezione dei servizi app")](certificates-identifiers-images/devcenter04-large.png#lightbox)
2. Immettere un **ID bundle** per l'app e fare clic sul pulsante **Continue** (Continua): 

    [![Immissione di un ID bundle](certificates-identifiers-images/devcenter05.png "Immissione di un ID bundle")](certificates-identifiers-images/devcenter05-large.png#lightbox)
3. Verificare le informazioni e fare clic sul pulsante **Submit** (Invia): 

    [![Verifica delle informazioni](certificates-identifiers-images/devcenter06.png "Verifica delle informazioni")](certificates-identifiers-images/devcenter06-large.png#lightbox)

Alcuni **servizi app** potrebbero richiedere un'ulteriore configurazione (ad esempio, iCloud). In questo caso, selezionare il nuovo ID app appena creato e fare clic sul pulsante **Edit** (Modifica):

[![Modifica del nuovo ID app](certificates-identifiers-images/devcenter07.png "Modifica del nuovo ID app")](certificates-identifiers-images/devcenter07-large.png#lightbox)

Per configurare i servizi iCloud, fare clic sul pulsante **Edit** (Modifica):

[![Configurazione dei servizi iCloud](certificates-identifiers-images/devcenter08.png "Configurazione dei servizi iCloud")](certificates-identifiers-images/devcenter08-large.png#lightbox)

Da qui lo sviluppatore può configurare i database che intende usare:

[![Configurazione dei database](certificates-identifiers-images/devcenter09.png "Configurazione dei database")](certificates-identifiers-images/devcenter09-large.png#lightbox)

### <a name="register-macos-systems"></a>Registrare sistemi macOS

Per creare un profilo di provisioning per il test, lo sviluppatore dovrà disporre di computer Mac registrati. È possibile registrare un massimo di 100 computer per testare le app Mac.

In Mac Developer Center selezionare **All** (Tutti) nella sezione **Devices** (Dispositivi) e fare clic sul pulsante **+**:

[![Aggiunta di un nuovo computer](certificates-identifiers-images/devcenter10.png "Aggiunta di un nuovo computer")](certificates-identifiers-images/devcenter10-large.png#lightbox)

Immettere un **nome** e l'**UUID** del computer da aggiungere e quindi fare clic sul pulsante **Continue** (Continua). Rivedere le informazioni e fare clic sul pulsante **Register** (Registra):

[![Immissione delle informazioni sul nuovo computer](certificates-identifiers-images/devcenter11.png "Immissione delle informazioni sul nuovo computer")](certificates-identifiers-images/devcenter11-large.png#lightbox)

### <a name="create-certificates"></a>Creare certificati

Usare la sezione Certificates (Certificati) per creare diversi tipi di certificati che saranno usati per firmare le applicazioni Mac:

[![Creazione di un nuovo certificato](certificates-identifiers-images/certif01.png "Creazione di un nuovo certificato")](certificates-identifiers-images/certif01-large.png#lightbox)

Esistono tre tipi principali di certificati:

-   **Certificato di sviluppo Mac**: facoltativo per lo sviluppo di app generali, ma obbligatorio se lo sviluppatore prevede di usare funzionalità quali iCloud o notifiche push. Lo sviluppatore avrà bisogno di un certificato di sviluppo prima di poter creare profili di provisioning per l'accesso a tali funzionalità.
-   **Mac App Store**: lo sviluppatore dovrà disporre di un certificato per l'app e di un altro certificato per il programma di installazione.
-   **ID sviluppatore**: certificati per l'app e il programma di installazione se si sceglie di eseguire la distribuzione all'esterno di Mac App Store.

Le sezioni seguenti forniscono esempi della creazione di ognuno di questi tipi di certificati.

#### <a name="mac-development-certificate"></a>Certificato di sviluppo Mac

Come accennato in precedenza, il certificato di sviluppo dell'app Mac è richiesto solo se vengono usate funzionalità di macOS come iCloud o le notifiche push.

Eseguire le operazioni seguenti per creare un nuovo certificato di sviluppo:

1. Selezionare il pulsante di opzione **Mac Development** (Sviluppo Mac) e fare clic su **Continue** (Continua): 

     [![Aggiunta di un certificato di sviluppo](certificates-identifiers-images/certif02.png "Aggiunta di un certificato di sviluppo")](certificates-identifiers-images/certif02-large.png#lightbox)
2. Nella schermata successiva viene spiegato come usare l'accesso keychain per creare un file di richiesta di firma del certificato da caricare: 

    [![Schermata di caricamento dell'accesso keychain](certificates-identifiers-images/certif03.png "Schermata di caricamento dell'accesso keychain")](certificates-identifiers-images/certif03-large.png#lightbox)
3. Scegliere un nome comune significativo per il certificato, in modo che sia facilmente riconoscibile in un secondo momento quando verrà creato il certificato finale. Prendere nota della posizione in cui è stato salvato il file, in modo da ritrovarlo nel passaggio successivo: 

     ![Esportazione di un certificato](certificates-identifiers-images/image12.png "Esportazione di un certificato")
4. Un file di richiesta del certificato (con estensione `.certSigningRequest`) verrà salvato localmente sul Mac. Prendere nota della posizione in cui è stato salvato (la posizione predefinita è il Desktop) perché dovrà essere selezionato nel passaggio successivo: 

     [![Caricamento del file del certificato](certificates-identifiers-images/image13.png "Caricamento del file del certificato")](certificates-identifiers-images/image13-large.png#lightbox)
5. Fare clic su **Download** per ottenere il certificato e fare doppio clic per installarlo nel **keychain**: 

     [![Download di un certificato di sviluppo](certificates-identifiers-images/image15.png "Download di un certificato di sviluppo")](certificates-identifiers-images/image15-large.png#lightbox)
6. Fare clic su **Download** per ottenere il certificato e fare doppio clic per installarlo nel **keychain**. L'**utilità del certificato dello sviluppatore** visualizzerà i certificati in maniera analoga alla seguente: 

     [![Utilità del certificato dello sviluppatore](certificates-identifiers-images/image16.png "Utilità del certificato dello sviluppatore")](certificates-identifiers-images/image16-large.png#lightbox)
7. Il certificato sarà visualizzato anche nel **keychain** con l'aspetto seguente: 

     ![Certificato nell'accesso keychain](certificates-identifiers-images/image17.png "Certificato nell'accesso keychain")

Come accennato in precedenza, il certificato dello sviluppatore non è sempre richiesto, a meno che lo sviluppatore non stia implementando funzionalità macOS come iCloud e le notifiche push. È inoltre necessario per creare un **profilo di provisioning di sviluppo**, che sarà richiesto per testare le app di Mac App Store.

#### <a name="mac-app-store-certificates"></a>Certificati Mac App Store

Per rilasciare un'app in App Store, dovranno essere creati il certificato **Mac App Store** che verrà usato per firmare l'applicazione e il pacchetto del programma di installazione Mac.

1. Selezionare **Mac App Store** come tipo di certificato e fare clic sul pulsante **Continue** (Continua): 

    [![Creazione di un certificato App Store](certificates-identifiers-images/certif04.png "Creazione di un certificato App Store")](certificates-identifiers-images/certif04-large.png#lightbox)
2. Selezionare il tipo di certificato da creare (ne sarà richiesto uno per ogni tipo da rilasciare in App Store): 

    [![Selezione del tipo di certificato](certificates-identifiers-images/certif05.png "Selezione del tipo di certificato")](certificates-identifiers-images/certif05-large.png#lightbox)
3. La pagina successiva illustra come usare l'**accesso keychain** per generare un file di richiesta del certificato. Seguire le istruzioni: 

     [![Generazione della richiesta di accesso keychain](certificates-identifiers-images/certif06.png "Generazione della richiesta di accesso keychain")](certificates-identifiers-images/certif06-large.png#lightbox)
4. Scegliere un **nome comune** descrittivo, ad esempio usare il testo "Applicazione App Store" nel nome: 

     ![Assegnazione di un nome descrittivo](certificates-identifiers-images/image20.png "Assegnazione di un nome descrittivo")
5. Un file di richiesta del certificato (con estensione `.certSigningRequest`) verrà salvato localmente sul Mac. Prendere nota della posizione in cui è stato salvato (la posizione predefinita è il Desktop): 

     [![Salvataggio del certificato](certificates-identifiers-images/image21.png "Salvataggio del certificato")](certificates-identifiers-images/image21-large.png#lightbox)
6. Fare clic su **Download** per ottenere il certificato e fare doppio clic per installarlo nel **keychain**: 

      [![Download del certificato di App Store](certificates-identifiers-images/image23.png "Download del certificato di App Store")](certificates-identifiers-images/image23-large.png#lightbox)
7. Fare clic su **Continue** (Continua) e seguire esattamente la stessa procedura per scaricare un altro certificato, questa volta per il *programma di installazione*: 

     [![Selezione del programma di installazione](certificates-identifiers-images/image24.png "Selezione del programma di installazione")](certificates-identifiers-images/image24-large.png#lightbox)
8. Scegliere un **nome comune** descrittivo, ad esempio usare il testo "Programma di installazione App Store" nel nome: 

     ![Impostazione del nome del certificato](certificates-identifiers-images/image25.png "Impostazione del nome del certificato")
9. Un file di richiesta del certificato (con estensione `.certSigningRequest`) verrà salvato localmente sul Mac. Prendere nota della posizione in cui è stato salvato (la posizione predefinita è il Desktop): 

     [![Caricamento del certificato](certificates-identifiers-images/image26.png "Caricamento del certificato")](certificates-identifiers-images/image26-large.png#lightbox) 

     [![Download del certificato di distribuzione](certificates-identifiers-images/image28.png "Download del certificato di distribuzione")](certificates-identifiers-images/image28-large.png#lightbox)
10. Fare clic su **Download** per ottenere il certificato e fare doppio clic per installarlo nel **keychain**. L'utilità del certificato dello sviluppatore visualizzerà i certificati in maniera analoga alla seguente: 

     [![Utilità del certificato dello sviluppatore](certificates-identifiers-images/image29.png "Utilità del certificato dello sviluppatore")](certificates-identifiers-images/image29-large.png#lightbox)
11. I due nuovi certificati saranno ora visibili nel **keychain**: 

     [![Certificato nell'accesso keychain](certificates-identifiers-images/image30.png "Certificato nell'accesso keychain")](certificates-identifiers-images/image30-large.png#lightbox)

#### <a name="developer-id-certificates"></a>Certificati ID sviluppatore

Per rilasciare autonomamente un'applicazione Xamarin.Mac (e non tramite App Store di Apple), lo sviluppatore avrà bisogno di un certificato ID sviluppatore per firmare l'app per il rilascio e l'installazione.

Seguire questa procedura:

1. Nella sezione **Certificates** (Certificati) iniziare facendo clic sul pulsante **+**, quindi selezionare il pulsante di opzione **Developer ID** (ID sviluppatore): 

    [![Aggiunta di un ID sviluppatore](certificates-identifiers-images/certif07.png "Aggiunta di un ID sviluppatore")](certificates-identifiers-images/certif07-large.png#lightbox)
2. Fare clic sul pulsante **Continue** (Continua) e selezionare il tipo di ID sviluppatore da creare: 

    [![Selezione del tipo di ID sviluppatore](certificates-identifiers-images/certif08.png "Selezione del tipo di ID sviluppatore")](certificates-identifiers-images/certif08-large.png#lightbox)
3. Ne saranno richiesti due, uno per firmare l'applicazione stessa e uno per firmare il programma di installazione dell'applicazione. Prestare attenzione quando si assegna un nome alle richieste di certificati per queste chiavi: usare nomi descrittivi che includano il testo `Application` e `Installer`, in modo che possano essere distinti in un secondo momento.
4. La schermata successiva fornirà indicazioni dettagliate su come creare il certificato; fare clic sul pulsante **Continue** (Continua): 

    [![Come creare il certificato](certificates-identifiers-images/certif09.png "Come creare il certificato")](certificates-identifiers-images/certif09-large.png#lightbox)
5. Scegliere un **nome comune** descrittivo, ad esempio usare il testo "Applicazione ID sviluppatore" nel nome: 

     ![Assegnazione di un nome al certificato](certificates-identifiers-images/image33.png "Assegnazione di un nome al certificato")
6. Un file di richiesta del certificato (con estensione `.certSigningRequest`) verrà salvato localmente sul Mac. Prendere nota della posizione in cui è stato salvato (la posizione predefinita è il Desktop): 

     [![Caricamento del certificato](certificates-identifiers-images/certif10.png "Caricamento del certificato")](certificates-identifiers-images/certif10-large.png#lightbox) 

     [![Download dell'ID sviluppatore](certificates-identifiers-images/certif11.png "Download dell'ID sviluppatore")](certificates-identifiers-images/certif11-large.png#lightbox)
7. Fare clic su **Download** per ottenere il certificato e fare doppio clic per installarlo nel **keychain**.
8. Fare clic su **Continue** (Continua) e seguire esattamente la stessa procedura per scaricare un altro certificato, questa volta per il *programma di installazione*.
9. Scegliere un **nome comune** descrittivo, ad esempio usare il testo "Programma di installazione ID sviluppatore" nel nome: 

     ![Assegnazione di un nome comune](certificates-identifiers-images/image38.png "Assegnazione di un nome comune")
10. Un file di richiesta del certificato (con estensione `.certSigningRequest`) verrà salvato localmente sul Mac. Prendere nota della posizione in cui è stato salvato (la posizione predefinita è il Desktop): 

     [![Caricamento di un certificato](certificates-identifiers-images/certif10.png "Caricamento di un certificato")](certificates-identifiers-images/certif10-large.png#lightbox)
11. Il certificato è quindi disponibile per il download, fare clic sul pulsante **Download** prima di scegliere **Done** (Fatto): 

     [![Download di un certificato](certificates-identifiers-images/certif11.png "Download di un certificato")](certificates-identifiers-images/certif11-large.png#lightbox)
12. Fare clic su **Download** per ottenere il certificato e fare doppio clic per installarlo nel **keychain**. L'**utilità del certificato dello sviluppatore** visualizzerà i certificati in maniera analoga alla seguente: 

     [![Utilità del certificato dello sviluppatore](certificates-identifiers-images/certif12.png "Utilità del certificato dello sviluppatore")](certificates-identifiers-images/certif12-large.png#lightbox)
13. Gli elementi seguenti sono visibili nel **keychain**: 

     [![Certificato nell'accesso keychain](certificates-identifiers-images/image43.png "Certificato nell'accesso keychain")](certificates-identifiers-images/image43-large.png#lightbox)


## <a name="related-links"></a>Collegamenti correlati

- [Installazione](/visualstudio/mac/installation/)
- [Esempio Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuire le app in Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID sviluppatore e GateKeeper](https://developer.apple.com/resources/developer-id/)
