---
title: Google Cloud Messaging
description: Google Cloud Messaging (GCM) è un servizio che facilita la messaggistica tra le applicazioni per dispositivi mobili e server. In questo articolo viene fornita una panoramica del funzionamento GCM e viene spiegato come configurare i servizi Google app consentono di usare GCM.
ms.prod: xamarin
ms.assetid: DF8EF401-F63D-4BA0-B2C6-B22DF8FD60CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/12/2018
ms.openlocfilehash: 29cccf414759a79a8ba74dfc35b7ba9f6a1cc5d6
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
ms.locfileid: "31044743"
---
# <a name="google-cloud-messaging"></a>Google Cloud Messaging

_Google Cloud Messaging (GCM) è un servizio che facilita la messaggistica tra le applicazioni per dispositivi mobili e server. In questo articolo viene fornita una panoramica del funzionamento GCM e viene spiegato come configurare i servizi Google app consentono di usare GCM._

[![Logo di Google Cloud Messaging](google-cloud-messaging-images/preview-sml.png)](google-cloud-messaging-images/preview.png#lightbox)

In questo argomento fornisce una panoramica di come Google Cloud Messaging indirizza i messaggi tra l'app e un server di app e fornisce una procedura dettagliata per l'acquisizione delle credenziali in modo che l'app è possibile utilizzare servizi GCM.

> [!NOTE]
> GCM è stata sostituita da [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> GCM server e le API client [sono state deprecate](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) e non sarà disponibile appena l'11 aprile 2019.

## <a name="overview"></a>Panoramica

Google Cloud Messaging (GCM) è un servizio che gestisce l'invio, routing e Accodamento dei messaggi tra applicazioni server e le applicazioni client mobili. Oggetto *app client* è un'app abilitata GCM che viene eseguito in un dispositivo. Il *server app* (fornito dall'utente o la società) è il server GCM abilitato l'app client con cui comunica attraverso GCM:

[![GCM si trova tra l'app client e il server di app](google-cloud-messaging-images/01-server-gcm-app-sml.png)](google-cloud-messaging-images/01-server-gcm-app.png#lightbox)

Con GCM, server applicazioni possono inviare messaggi a un singolo dispositivo, un gruppo di dispositivi o un numero di dispositivi che sono ha sottoscritto un argomento. L'app client può utilizzare GCM per sottoscrivere i messaggi downstream da un server di applicazione (ad esempio, per ricevere le notifiche remote). Inoltre, GCM rende possibile per le applicazioni client inviare messaggi a monte nel server di app.

Per informazioni sull'implementazione di un server di app per GCM, vedere [sui Server di connessione di GCM](https://developers.google.com/cloud-messaging/server).



## <a name="google-cloud-messaging-in-action"></a>Google Cloud Messaging in azione

Quando i messaggi a valle vengono inviati da un server di app a un'applicazione client, il server app invia il messaggio a un *server connessione GCM*; il server di connessione GCM, a sua volta, inoltra il messaggio a un dispositivo che esegue l'app client. È possibile inviare messaggi su HTTP o [XMPP](https://developers.google.com/cloud-messaging/ccs) (messaggistica estendibile e presenza Protocol). Poiché le applicazioni client non sono sempre connessi o in esecuzione, GCM connessione server Accoda e archivi di messaggi, vengono inviati al client App man mano che riconnettersi e diventano disponibili. Analogamente, GCM verrà Accodamento messaggi upstream dall'app client al server app se il server di app non è disponibile.

GCM utilizza le credenziali seguenti per identificare il server di app e l'app client e Usa queste credenziali per autorizzare le transazioni di messaggio attraverso GCM:

-   **Chiave API** &ndash; il *chiave API* consente all'app l'accesso server a servizi di Google. GCM utilizza questa chiave per autenticare il server di app.
    Prima di poter utilizzare il servizio GCM, è innanzitutto necessario ottenere una chiave API dal [Google Developer Console](https://console.developers.google.com/) creando un *progetto*. La chiave API devono essere protetti; Per ulteriori informazioni su come proteggere la chiave API, vedere [procedure consigliate per l'utilizzo in modo sicuro le chiavi API](https://support.google.com/cloud/answer/6310037?hl=en).

-   **ID mittente** &ndash; il *ID mittente* autorizza il server di app per l'app client &ndash; è un numero univoco che identifica il server di app che è autorizzato a inviare messaggi all'applicazione client.
    L'ID del mittente è anche il numero di progetto ottenere l'ID del mittente da Google Developers Console quando si registra il progetto.

-   **Token di registrazione** &ndash; il *registrazione Token* è l'identità GCM dell'app client in un determinato dispositivo. Il token di registrazione viene generato in fase di esecuzione &ndash; app riceve un token di registrazione al momento prima della registrazione con GCM durante l'esecuzione in un dispositivo. Il token di registrazione per ricevere messaggi da GCM, si autorizza un'istanza dell'applicazione client (in esecuzione nel dispositivo specifico).

-   **ID applicazione** &ndash; l'identità dell'applicazione client (indipendente da qualsiasi dispositivo specifico) che esegue la registrazione per ricevere messaggi da GCM. In Android, l'ID dell'applicazione è il nome del pacchetto registrato nel **AndroidManifest.xml**, ad esempio `com.xamarin.gcmexample`.

[Impostazione di Google Cloud Messaging](#settingup) (più avanti in questa Guida) vengono fornite istruzioni dettagliate per la creazione di un progetto e la generazione di queste credenziali.

Le sezioni seguenti illustrano come queste credenziali vengono utilizzate quando le applicazioni client di comunicare con i server app attraverso GCM.



### <a name="registration-with-gcm"></a>Registrazione con GCM

Un'app client installata in un dispositivo deve innanzitutto registrare con GCM prima di messaggistica può avvenire. L'applicazione client è necessario completare la procedura di registrazione illustrata nel diagramma seguente:

[![Procedura di registrazione di App](google-cloud-messaging-images/02-app-registration-sml.png)](google-cloud-messaging-images/02-app-registration.png#lightbox)

1.  L'app client contatta GCM per ottenere un token di registrazione, passare l'ID del mittente GCM.

2.  GCM restituisce un token di registrazione per l'applicazione client.

3.  L'app client inoltra il token di registrazione al server app.

Il server app memorizza nella cache il token di registrazione per le comunicazioni successive con l'applicazione client. Facoltativamente, il server di app può inviare un riconoscimento tornare all'app client per indicare che è stato ricevuto il token di registrazione. Dopo l'operazione di handshake viene eseguita, l'applicazione client può ricevere messaggi da (o inviare messaggi a) il server di app.

Quando l'app client non desidera ricevere messaggi dal server di app, è possibile inviare una richiesta al server di app per eliminare il token di registrazione. Se l'app client riceve i messaggi di argomento (descritto più avanti in questo articolo), è possibile annullare la sottoscrizione dell'argomento.
Se l'applicazione client viene disinstallato da un dispositivo, GCM rileva e invia automaticamente una notifica al server di app di eliminare il token di registrazione.

Google [le applicazioni Client di registrazione](https://developers.google.com/cloud-messaging/registration) viene illustrato il processo di registrazione in modo più dettagliato; viene spiegato l'annullamento della registrazione e annullamento sottoscrizione e descrive il processo di annullamento della registrazione quando si disinstalla un'applicazione client.



### <a name="downstream-messaging"></a>Messaggistica downstream

Quando il server app invia un messaggio a valle per l'app client, esegue la procedura illustrata nel diagramma seguente:

[![Archivio di messaggistica a valle e diagramma Avanti](google-cloud-messaging-images/03-downstream-sml.png)](google-cloud-messaging-images/03-downstream.png#lightbox)

1.  Il server app invia il messaggio a GCM.

2.  Se il dispositivo client non è disponibile, il server GCM archivia il messaggio in una coda per la trasmissione di versioni successive.

3.  Quando il dispositivo client è disponibile, GCM invia il messaggio all'app sul dispositivo client.

4.  L'applicazione client riceve il messaggio da GCM e gestisce di conseguenza. Ad esempio, se il messaggio è una notifica remota, viene visualizzato all'utente.

In questo scenario di messaggistica (in cui il server app invia un messaggio a un'app client singolo), i messaggi possono essere di lunghezza fino a 4kB.

Per informazioni dettagliate (inclusi gli esempi di codice) a valle GCM messaggi ricevuti su Android, vedere [notifiche remoto](~/android/data-cloud/google-messaging/remote-notifications-with-gcm.md).


#### <a name="topic-messaging"></a>Argomento di messaggistica

*Argomento messaggistica* è un tipo di messaggistica a valle in cui il server app invia un singolo messaggio a più dispositivi di app client che sottoscrivono un argomento (ad esempio le previsioni meteo). I messaggi di argomento possono essere fino a 2KB e messaggistica argomento supporta fino a un milione di sottoscrizioni per l'app. Se viene utilizzato solo per l'argomento messaggistica GCM, l'app client non è necessario inviare un token di registrazione al server app. Google [implementazione messaggistica argomento](https://developers.google.com/cloud-messaging/topic-messaging) viene illustrato come inviare messaggi da un server di app per più dispositivi che sottoscrivono un determinato argomento.



#### <a name="group-messaging"></a>La messaggistica di gruppo

*Gruppo messaggistica* è un tipo di messaggistica a valle in cui il server app invia un singolo messaggio a più dispositivi di app client che appartengono a un gruppo (ad esempio, un gruppo di dispositivi che appartengono a un singolo utente). Fino a 2KB di lunghezza per i dispositivi iOS, è possibile raggruppare i messaggi e fino a 4KB di lunghezza per i dispositivi Android. Un gruppo è limitato a un massimo di 20 membri. Google [dispositivo gruppo messaggistica](https://developers.google.com/cloud-messaging/notifications) spiega come server di app può inviare un singolo messaggio a più istanze di app client in esecuzione su dispositivi che appartengono a un gruppo.


### <a name="upstream-messaging"></a>Messaggistica upstream

Se l'app client si connette a un server che supporta [XMPP](https://developers.google.com/cloud-messaging/ccs), cui può inviare messaggi al server app, come illustrato nel diagramma seguente:

[![Diagramma di messaggistica a monte](google-cloud-messaging-images/04-upstream-sml.png)](google-cloud-messaging-images/04-upstream.png#lightbox)

1.  L'applicazione client invia un messaggio al server di connessione XMPP GCM.

2.  Se il server di app viene disconnesso, il server GCM archivia il messaggio in una coda per l'inoltro in un secondo momento.

3.  Quando il server di app viene nuovamente connesso, GCM inoltra il messaggio al server app.

4.  Il server app analizza il messaggio per verificare l'identità dell'applicazione client, quindi invia un ack"" a GCM per confermare la ricezione di messaggi.

5.  Il server app elabora il messaggio.

Google [messaggi Upstream](https://developers.google.com/cloud-messaging/ccs#upstream) viene illustrato come strutturare i messaggi con codifica JSON e inviarli al server di app che eseguono Server di connessione Cloud basato su XMPP Google.


<a name="settingup" />

## <a name="setting-up-google-cloud-messaging"></a>Impostazione di Google Cloud Messaging

Prima di utilizzare servizi GCM nell'app, è innanzitutto necessario acquisire le credenziali per l'accesso al server GCM di Google. Nelle sezioni seguenti vengono descritti i passaggi necessari per completare questo processo:



### <a name="enable-google-services-for-your-app"></a>Abilitare i servizi Google per l'App

1.  Accedere al [Google Developers Console](https://developers.google.com/mobile/add?platform=android) con il Google account (ad esempio, l'indirizzo gmail) e creare un nuovo progetto. Se si dispone di un progetto esistente, scegliere il progetto che si desidera diventare abilitato GCM. Nell'esempio seguente, un nuovo progetto denominato **XamarinGCM** viene creato:

    [![Creazione del progetto XamarinGCM](google-cloud-messaging-images/05-create-gcm-app-sml.png)](google-cloud-messaging-images/05-create-gcm-app.png#lightbox)

2.  Quindi, immettere il nome del pacchetto dell'App (in questo esempio, il nome del pacchetto è **com.xamarin.gcmexample**) e fare clic su **continua per scegliere e configurare i servizi**:

    [![Immettere il nome del pacchetto](google-cloud-messaging-images/06-package-name-sml.png)](google-cloud-messaging-images/06-package-name.png#lightbox)

    Si noti che il nome del pacchetto è anche l'ID dell'applicazione per l'app.

3.  Il **scegliere e configurare i servizi** sezione sono elencati i servizi Google che è possibile aggiungere all'applicazione. Fare clic su **Cloud messaggistica**:

    [![Scegliere Cloud Messaging](google-cloud-messaging-images/07-choose-gcm-service-sml.png)](google-cloud-messaging-images/07-choose-gcm-service.png#lightbox)

4.  Successivamente, fare clic su **abilitare GOOGLE CLOUD MESSAGING**:

    [![Abilitare Google Cloud Messaging](google-cloud-messaging-images/08-enable-gcm-sml.png)](google-cloud-messaging-images/08-enable-gcm.png#lightbox)

5.  Oggetto **chiave API Server** e **ID mittente** vengono generati per l'app. Registrare questi valori e fare clic su **Chiudi**:

    [![Chiave API del server e visualizzazione di ID mittente](google-cloud-messaging-images/09-get-api-key-and-id-sml.png)](google-cloud-messaging-images/09-get-api-key-and-id.png#lightbox)

    Proteggere la chiave API &ndash; non è progettato per uso pubblico. Se la chiave API è compromesso, è possibile pubblicare i messaggi da server non autorizzati alle applicazioni client.
    [Procedure consigliate per l'utilizzo in modo sicuro le chiavi API](https://support.google.com/cloud/answer/6310037?hl=en) fornisce linee guida utili per proteggere la chiave API.



### <a name="view-your-project-settings"></a>Visualizzare le impostazioni del progetto

È possibile visualizzare le impostazioni del progetto in qualsiasi momento accedendo con il [Google Cloud Console](https://console.cloud.google.com/) e selezionando il progetto. Ad esempio, è possibile visualizzare il **ID mittente** selezionando il progetto nel menu a discesa nella parte superiore della pagina (in questo esempio viene chiamato il progetto **XamarinGCM**). L'ID del mittente è il numero di progetto, come illustrato in questa schermata (qui l'ID del mittente **9349932736**):

[![Visualizzare l'ID mittente](google-cloud-messaging-images/10-view-server-id-sml.png)](google-cloud-messaging-images/10-view-server-id.png#lightbox)

Per visualizzare il **chiave API**, fare clic su **API Manager** e quindi fare clic su **credenziali**:

[![Visualizzare la chiave API](google-cloud-messaging-images/11-view-credentials-sml.png)](google-cloud-messaging-images/11-view-credentials.png#lightbox)



## <a name="for-further-reading"></a>Ulteriori informazioni

-   Google [le applicazioni Client di registrazione](https://developers.google.com/cloud-messaging/registration) descrive il processo di registrazione client nel modo più dettagliato, vengono fornite informazioni sulla configurazione di ripetizione automatica e la sincronizzazione dello stato di registrazione.

-   [6120 RFC](https://tools.ietf.org/html/rfc6120) e [6121 RFC](https://tools.ietf.org/html/rfc6121) spiegare e definire la messaggistica estendibile e protocollo presenza (XMPP).



## <a name="summary"></a>Riepilogo

In questo articolo viene fornita una panoramica di Google Cloud Messaging (GCM). È stato spiegato varie credenziali che consentono di identificare e autorizzare tra i server applicazioni e le applicazioni client di messaggistica. Illustrato i più comuni scenari di messaggistica e sono stati descritti i passaggi per registrare l'app con GCM per utilizzare i servizi GCM.


## <a name="related-links"></a>Collegamenti correlati

- [Cloud di messaggistica](https://developers.google.com/cloud-messaging/)
