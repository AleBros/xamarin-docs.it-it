---
title: Google Cloud Messaging
description: Google Cloud Messaging (GCM) è un servizio che facilita la messaggistica tra App per dispositivi mobili e applicazioni server. Questo articolo offre una panoramica del funzionamento di GCM e viene spiegato come configurare i servizi di Google in modo che l'app può usare GCM.
ms.prod: xamarin
ms.assetid: DF8EF401-F63D-4BA0-B2C6-B22DF8FD60CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/12/2018
ms.openlocfilehash: c2ca567ffcb247622d1b3e8f3e0136c453723b96
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61017681"
---
# <a name="google-cloud-messaging"></a>Google Cloud Messaging

_Google Cloud Messaging (GCM) è un servizio che facilita la messaggistica tra App per dispositivi mobili e applicazioni server. Questo articolo offre una panoramica del funzionamento di GCM e viene spiegato come configurare i servizi di Google in modo che l'app può usare GCM._

[![Logo di Google Cloud Messaging](google-cloud-messaging-images/preview-sml.png)](google-cloud-messaging-images/preview.png#lightbox)

In questo argomento fornisce una panoramica generale del modo in cui Google Cloud Messaging indirizza i messaggi tra le app e un server di app e fornisce una procedura dettagliata per l'acquisizione delle credenziali in modo che l'app può usare servizi GCM.

> [!NOTE]
> GCM è stata sostituita da [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> GCM server e le API client [sono state deprecate](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) e non sarà disponibile appena 11 aprile 2019.

## <a name="overview"></a>Panoramica

Google Cloud Messaging (GCM) è un servizio che gestisce l'invio, il routing e Accodamento dei messaggi tra le applicazioni server e le app client per dispositivi mobili. Oggetto *app client* è un'app abilitata per GCM che viene eseguito in un dispositivo. Il *server app* (fornito dall'utente o la società) è il server GCM abilitata l'app client con cui comunica tramite GCM:

[![GCM si trova tra l'app client e server dell'app](google-cloud-messaging-images/01-server-gcm-app-sml.png)](google-cloud-messaging-images/01-server-gcm-app.png#lightbox)

Tramite GCM, i server di app possono inviare messaggi a un singolo dispositivo, un gruppo di dispositivi o un numero di dispositivi che hanno sottoscritto un argomento. L'app client può usare GCM per sottoscrivere i messaggi downstream da un server di app (ad esempio, per ricevere notifiche remote). Inoltre, GCM rende possibile per le app client inviare messaggi a monte nel server di app.

Per informazioni sull'implementazione di un server di app per GCM, vedere [informazioni sul Server di connessione di GCM](https://developers.google.com/cloud-messaging/server).



## <a name="google-cloud-messaging-in-action"></a>Google Cloud Messaging in azione

Quando vengono inviati messaggi downstream da un server di app per un'app client, il server app invia il messaggio a un *del server GCM connessione*; il server di connessione GCM, a sua volta, inoltra il messaggio a un dispositivo che esegue l'app client. È possibile inviare messaggi tramite HTTP o [XMPP](https://developers.google.com/cloud-messaging/ccs) (Extensible Messaging e presenza Protocol). Poiché le app client non siano sempre collegate o in esecuzione, i GCM connessione server Accoda e archivi di messaggi, inviarli alle App client non appena riconnettersi e diventano disponibili. Analogamente, GCM enqueue upstream messaggi will dall'app client al server di app se il server di app è disponibile.

GCM utilizza le credenziali seguenti per identificare il server di app e l'app client e Usa queste credenziali per autorizzare le transazioni di messaggi tramite GCM:

-   **Chiave API** &ndash; le *chiave API* consente all'app server accedere a servizi di Google. GCM Usa questa chiave per autenticare il server di app.
    È possibile usare il servizio GCM, è necessario innanzitutto ottenere una chiave API dal [Google Developer Console](https://console.developers.google.com/) mediante la creazione di un *progetto*. La chiave API devono essere protette; per altre informazioni su come proteggere la chiave API, vedere [procedure consigliate per l'uso in modo sicuro le chiavi API](https://support.google.com/cloud/answer/6310037?hl=en).

-   **ID mittente** &ndash; le *ID mittente* autorizza il server di app per l'app client &ndash; è un numero univoco che identifica il server di app che è autorizzato a inviare messaggi all'applicazione client.
    L'ID del mittente è anche il numero di progetto si ottiene l'ID del mittente da Google Developers Console quando si registra il progetto.

-   **Token di registrazione** &ndash; le *Token di registrazione* è l'identità GCM dell'app client in un determinato dispositivo. Il token di registrazione viene generato in fase di esecuzione &ndash; l'app riceve un token di registrazione al momento prima della registrazione con GCM durante l'esecuzione in un dispositivo. Il token di registrazione autorizza un'istanza dell'app client (in esecuzione in quel particolare dispositivo) per ricevere messaggi da GCM.

-   **ID applicazione** &ndash; l'identità dell'app client (indipendente da qualsiasi dispositivo specifico) che esegue la registrazione per ricevere messaggi da GCM. In Android, l'ID applicazione è il nome del pacchetto registrato nel **androidmanifest. XML**, ad esempio `com.xamarin.gcmexample`.

[Impostazione di backup di Google Cloud Messaging](#settingup) (più avanti in questa Guida) vengono fornite istruzioni dettagliate per la creazione di un progetto e la generazione di queste credenziali.

Le sezioni seguenti illustrano come queste credenziali vengono usate quando le app client di comunicare con i server di app tramite GCM.



### <a name="registration-with-gcm"></a>Registrazione con GCM

Un'app client installata in un dispositivo prima di tutto necessario registrare con GCM prima di messaggistica può essere eseguite. L'app client è necessario completare i passaggi di registrazione illustrati nel diagramma seguente:

[![Procedura di registrazione di App](google-cloud-messaging-images/02-app-registration-sml.png)](google-cloud-messaging-images/02-app-registration.png#lightbox)

1.  L'app client contatta GCM per ottenere un token di registrazione, passando l'ID del mittente per GCM.

2.  GCM restituisce un token di registrazione per l'app client.

3.  L'app client inoltra il token di registrazione al server di app.

Il server app memorizza nella cache il token di registrazione per ricevere altre comunicazioni successive con l'app client. Facoltativamente, il server di app può inviare un acknowledgement tornare all'app client per indicare che è stato ricevuto il token di registrazione. Dopo l'esecuzione di questo handshake, l'app client può ricevere messaggi da (o inviare messaggi a) del server applicazioni.

Quando l'app client non è più vuole ricevere messaggi dal server di app, può inviare una richiesta al server di app per eliminare il token di registrazione. Se l'app client riceve i messaggi dell'argomento (descritto più avanti in questo articolo), è possibile annullare la sottoscrizione dall'argomento.
Se l'app client è disinstallato da un dispositivo, GCM rileva tale situazione e si riceve una notifica al server di app di eliminare il token di registrazione.

Google [la registrazione di App Client](https://developers.google.com/cloud-messaging/registration) illustra il processo di registrazione in modo più dettagliato; viene illustrato l'annullamento della registrazione e annullamento sottoscrizione e viene descritto il processo di annullamento della registrazione quando si disinstalla un'app client.



### <a name="downstream-messaging"></a>Messaggistica a valle

Quando il server app invia un messaggio a valle per l'app client, segue la procedura illustrata nel diagramma seguente:

[![Archivio di messaggistica a valle e diagramma Avanti](google-cloud-messaging-images/03-downstream-sml.png)](google-cloud-messaging-images/03-downstream.png#lightbox)

1.  Il server app invia il messaggio a GCM.

2.  Se il dispositivo client non è disponibile, il server GCM archivia il messaggio in una coda per una trasmissione successiva.

3.  Quando il dispositivo client è disponibile, GCM invia il messaggio per l'app client in tale dispositivo.

4.  L'app client riceve il messaggio da GCM e si gestisce di conseguenza. Ad esempio, se il messaggio è una notifica remota, viene visualizzata all'utente.

In questo scenario di messaggistica (in cui il server app invia un messaggio a un'app client singolo), i messaggi possono essere fino a 4kB di lunghezza.

Per informazioni dettagliate (inclusi gli esempi di codice) a valle GCM messaggi ricevuti su Android, vedere [notifiche Remote](~/android/data-cloud/google-messaging/remote-notifications-with-gcm.md).


#### <a name="topic-messaging"></a>Argomento di messaggistica

*Argomento messaggistica* è un tipo di messaggistica a valle in cui il server di app invia un singolo messaggio a più dispositivi con app client che sottoscrivono un argomento (ad esempio una previsione meteo). I messaggi dell'argomento possono essere fino a 2KB di lunghezza e la messaggistica argomento supporta le sottoscrizioni fino a 1 milione per ogni app. Se viene utilizzato solo per argomento messaggistica GCM, l'app client non è necessario inviare un token di registrazione al server di app. Google [implementazione di messaggistica argomento](https://developers.google.com/cloud-messaging/topic-messaging) illustra come inviare messaggi da un server di app per più dispositivi che sottoscrivono un argomento particolare.



#### <a name="group-messaging"></a>Messaggistica di gruppo

*Gruppo messaggistica* è un tipo di messaggistica a valle in cui il server di app invia un singolo messaggio a più dispositivi con app client che appartengono a un gruppo (ad esempio, un gruppo di dispositivi che appartengono a un singolo utente). Raggruppare i messaggi possono essere fino a 2KB di lunghezza per i dispositivi iOS e fino a 4KB di lunghezza per i dispositivi Android. Un gruppo è limitato a un massimo di 20 membri. Google [messaggistica da dispositivo gruppo](https://developers.google.com/cloud-messaging/notifications) spiega come server app possono inviare un singolo messaggio a più istanze di app client in esecuzione su dispositivi appartenenti a un gruppo.


### <a name="upstream-messaging"></a>Messaggistica upstream

Se l'app client si connette a un server che supporta [XMPP](https://developers.google.com/cloud-messaging/ccs), può inviare messaggi al server di app come illustrato nel diagramma seguente:

[![Diagramma di messaggistica upstream](google-cloud-messaging-images/04-upstream-sml.png)](google-cloud-messaging-images/04-upstream.png#lightbox)

1.  L'app client invia un messaggio al server di connessione XMPP GCM.

2.  Se il server di app viene disconnesso, il server GCM archivia il messaggio in una coda per l'inoltro in un secondo momento.

3.  Quando il server di app è nuovamente connesso, GCM inoltra il messaggio al server di app.

4.  Il server app analizza il messaggio per verificare l'identità dell'app client, quindi invia un acknowledgement"" a GCM per confermare la ricezione di messaggi.

5.  Il server app elabora il messaggio.

Google [messaggi Upstream](https://developers.google.com/cloud-messaging/ccs#upstream) spiega come strutturare i messaggi con codifica JSON e inviarli al server di app che eseguono Server di connessione Cloud basata su XMPP di Google.


<a name="settingup" />

## <a name="setting-up-google-cloud-messaging"></a>Configurazione di Google Cloud Messaging

Prima di utilizzare i servizi GCM nell'app, è necessario acquisire le credenziali per l'accesso al server GCM di Google. Le sezioni seguenti descrivono i passaggi necessari per completare questa procedura:



### <a name="enable-google-services-for-your-app"></a>Abilitare i servizi di Google per l'App

1.  Accedi il [Google Developers Console](https://developers.google.com/mobile/add?platform=android) con di Google account (ad esempio, l'indirizzo gmail) e creare un nuovo progetto. Se si dispone di un progetto esistente, scegliere il progetto che si desidera diventare abilitato GCM. Nell'esempio seguente, un nuovo progetto denominato **XamarinGCM** creato:

    [![Creazione del progetto XamarinGCM](google-cloud-messaging-images/05-create-gcm-app-sml.png)](google-cloud-messaging-images/05-create-gcm-app.png#lightbox)

2.  Successivamente, immettere il nome del pacchetto dell'App (in questo esempio, è il nome del pacchetto **com.xamarin.gcmexample**) e fare clic su **continua per scegliere e configurare i servizi**:

    [![Immettere il nome del pacchetto](google-cloud-messaging-images/06-package-name-sml.png)](google-cloud-messaging-images/06-package-name.png#lightbox)

    Si noti che questo nome del pacchetto è anche l'ID dell'applicazione per l'app.

3.  Il **scegliere e configurare i servizi** sezione sono elencati i servizi di Google che è possibile aggiungere all'app. Fare clic su **Cloud Messaging**:

    [![Scegliere Cloud Messaging](google-cloud-messaging-images/07-choose-gcm-service-sml.png)](google-cloud-messaging-images/07-choose-gcm-service.png#lightbox)

4.  Fare quindi clic **abilitare GOOGLE CLOUD MESSAGING**:

    [![Abilitare Google Cloud Messaging](google-cloud-messaging-images/08-enable-gcm-sml.png)](google-cloud-messaging-images/08-enable-gcm.png#lightbox)

5.  Oggetto **chiave API del Server** e una **ID mittente** vengono generati per l'app. Registrare questi valori e fare clic su **Chiudi**:

    [![Chiave API del server e ID mittente visualizzato](google-cloud-messaging-images/09-get-api-key-and-id-sml.png)](google-cloud-messaging-images/09-get-api-key-and-id.png#lightbox)

    Proteggere la chiave API &ndash; non lo è per uso pubblico. Se la chiave API è compromesso, i server non autorizzati è stato possibile pubblicare i messaggi alle applicazioni client.
    [Procedure consigliate per l'uso in modo sicuro le chiavi API](https://support.google.com/cloud/answer/6310037?hl=en) fornisce linee guida utili per proteggere la chiave API.



### <a name="view-your-project-settings"></a>Visualizzare le impostazioni del progetto

È possibile visualizzare le impostazioni del progetto in qualsiasi momento effettuando l'accesso al [Google Cloud Console](https://console.cloud.google.com/) e selezionando il progetto. Ad esempio, è possibile visualizzare il **ID mittente** selezionando il progetto nel menu a discesa nella parte superiore della pagina (in questo esempio, il progetto viene chiamato **XamarinGCM**). L'ID del mittente è il numero di progetto, come illustrato in questo screenshot (in questo caso l'ID del mittente **9349932736**):

[![Visualizzando l'ID del mittente](google-cloud-messaging-images/10-view-server-id-sml.png)](google-cloud-messaging-images/10-view-server-id.png#lightbox)

Per visualizzare il **chiave API**, fare clic su **API Gestione** e quindi fare clic su **credenziali**:

[![Visualizzare la chiave API](google-cloud-messaging-images/11-view-credentials-sml.png)](google-cloud-messaging-images/11-view-credentials.png#lightbox)



## <a name="for-further-reading"></a>Ulteriori informazioni

-   Google [la registrazione di App Client](https://developers.google.com/cloud-messaging/registration) descrive il processo di registrazione client nel modo più dettagliato, e fornisce informazioni sulla configurazione di ripetizione dei tentativi automatici e mantenendo lo stato della registrazione sincronizzati.

-   [RFC 6120](https://tools.ietf.org/html/rfc6120) e [RFC 6121](https://tools.ietf.org/html/rfc6121) spiegare e definire l'Extensible Messaging and presenza protocollo XMPP ().



## <a name="summary"></a>Riepilogo

Questo articolo è fornita una panoramica di Google Cloud Messaging (GCM). Spiega le credenziali diverse che consentono di identificare e autorizzare la messaggistica tra i server di app e le app client. Illustrato i più comuni scenari di messaggistica e sono stati descritti i passaggi per la registrazione dell'app in GCM per usare i servizi GCM.


## <a name="related-links"></a>Collegamenti correlati

- [Messaggistica cloud](https://developers.google.com/cloud-messaging/)
