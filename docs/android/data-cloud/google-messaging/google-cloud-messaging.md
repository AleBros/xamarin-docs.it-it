---
title: Google Cloud Messaging
description: Google Cloud Messaging (GCM) è un servizio che facilita la messaggistica tra le app per dispositivi mobili e le applicazioni server. Questo articolo fornisce una panoramica del funzionamento di GCM e spiega come configurare i servizi Google in modo che l'app possa usare GCM.
ms.prod: xamarin
ms.assetid: DF8EF401-F63D-4BA0-B2C6-B22DF8FD60CB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/02/2019
ms.openlocfilehash: 742555da24120eaeadcc4b6232b24d23f41da283
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023709"
---
# <a name="google-cloud-messaging"></a>Google Cloud Messaging

> [!WARNING]
> Google deprecato GCM a partire dal 10 aprile 2018. I documenti e i progetti di esempio seguenti potrebbero non essere più gestiti. Il server GCM di Google e le API client verranno rimossi non appena il 29 maggio 2019. Google consiglia di eseguire la migrazione di app GCM a Firebase Cloud Messaging (FCM). Per altre informazioni sulla deprecazione e la migrazione di GCM, vedere la pagina relativa alla [messaggistica cloud deprecata di Google](https://developers.google.com/cloud-messaging/).
>
> Per iniziare a usare la messaggistica cloud Firebase con Novell, vedere [Firebase Cloud Messaging](firebase-cloud-messaging.md).

_Google Cloud Messaging (GCM) è un servizio che facilita la messaggistica tra le app per dispositivi mobili e le applicazioni server. Questo articolo fornisce una panoramica del funzionamento di GCM e spiega come configurare i servizi Google in modo che l'app possa usare GCM._

[logo Google Cloud Messaging![](google-cloud-messaging-images/preview-sml.png)](google-cloud-messaging-images/preview.png#lightbox)

Questo argomento fornisce una panoramica di alto livello del modo in cui Google Cloud Messaging instrada i messaggi tra l'app e un server app e fornisce una procedura dettagliata per l'acquisizione delle credenziali in modo che l'app possa usare i servizi GCM.

## <a name="overview"></a>Panoramica

Google Cloud Messaging (GCM) è un servizio che gestisce l'invio, il routing e l'accodamento dei messaggi tra applicazioni server e app client per dispositivi mobili. Un' *app client* è un'app abilitata per GCM che viene eseguita in un dispositivo. Il *Server App* , fornito dall'utente o dall'azienda, è il server abilitato per GCM con cui comunica l'app client tramite GCM:

[![GCM risiede tra l'app client e il server app](google-cloud-messaging-images/01-server-gcm-app-sml.png)](google-cloud-messaging-images/01-server-gcm-app.png#lightbox)

Usando GCM, i server app possono inviare messaggi a un singolo dispositivo, a un gruppo di dispositivi o a un numero di dispositivi che hanno sottoscritto un argomento. L'app client può usare GCM per sottoscrivere i messaggi downstream da un server app (ad esempio, per ricevere notifiche remote). GCM consente inoltre alle app client di inviare messaggi upstream al server app.

Per informazioni sull'implementazione di un server app per GCM, vedere [informazioni sul server di connessione GCM](https://developers.google.com/cloud-messaging/server).

## <a name="google-cloud-messaging-in-action"></a>Google Cloud Messaging in azione

Quando i messaggi downstream vengono inviati da un server app a un'app client, il server app invia il messaggio a un *server di connessione di GCM*. il server di connessione GCM, a sua volta, trasmette il messaggio a un dispositivo che esegue l'app client. I messaggi possono essere inviati tramite HTTP o [XMPP](https://developers.google.com/cloud-messaging/ccs) (Extensible Messaging and Presence Protocol). Poiché le app client non sono sempre connesse o in esecuzione, il server di connessione GCM Accoda e archivia i messaggi, inviarli alle app client quando si riconnettono e diventano disponibili. Analogamente, GCM accoda i messaggi upstream dall'app client al server app se il server app non è disponibile.

GCM usa le credenziali seguenti per identificare il server app e l'app client e usa queste credenziali per autorizzare le transazioni dei messaggi tramite GCM:

- **Chiave api** &ndash; la *chiave API* consente al server app di accedere ai servizi Google. GCM usa questa chiave per autenticare il server app.
    Prima di poter usare il servizio GCM, è necessario prima ottenere una chiave API da [Google Developer Console](https://console.developers.google.com/) creando un *progetto*. La chiave API deve essere mantenuta sicura; per altre informazioni sulla protezione della chiave API, vedere [procedure consigliate per l'uso sicuro delle chiavi API](https://support.google.com/cloud/answer/6310037?hl=en).

- **ID mittente** &ndash; l' *ID mittente* autorizza il server app all'app client &ndash; è un numero univoco che identifica il server app a cui è consentito inviare messaggi all'app client.
    L'ID mittente è anche il numero di progetto. Quando si registra il progetto, si ottiene l'ID mittente da Google Developers Console.

- Il **token di registrazione** &ndash; il token di *registrazione* è l'identità GCM dell'app client in un determinato dispositivo. Il token di registrazione viene generato in fase di esecuzione &ndash; l'app riceve un token di registrazione quando si registra per la prima volta con GCM durante l'esecuzione in un dispositivo. Il token di registrazione autorizza un'istanza dell'app client (in esecuzione su quel particolare dispositivo) a ricevere i messaggi da GCM.

- **ID applicazione** &ndash; l'identità dell'app client (indipendente da qualsiasi dispositivo specificato) che esegue la registrazione per ricevere i messaggi da GCM. In Android, l'ID applicazione è il nome del pacchetto registrato in **file AndroidManifest. XML**, ad esempio `com.xamarin.gcmexample`.

La [configurazione di Google Cloud Messaging](#settingup) (più avanti in questa guida) fornisce istruzioni dettagliate per la creazione di un progetto e la generazione di queste credenziali.

Le sezioni seguenti illustrano come usare queste credenziali quando le app client comunicano con i server applicazioni tramite GCM.

### <a name="registration-with-gcm"></a>Registrazione con GCM

Prima di eseguire la messaggistica, è necessario che un'app client installata in un dispositivo si registri prima con GCM. L'app client deve completare la procedura di registrazione illustrata nel diagramma seguente:

[procedura di registrazione di![app](google-cloud-messaging-images/02-app-registration-sml.png)](google-cloud-messaging-images/02-app-registration.png#lightbox)

1. L'app client contatta GCM per ottenere un token di registrazione, passando l'ID mittente a GCM.

2. GCM restituisce un token di registrazione all'app client.

3. L'app client invia il token di registrazione al server app.

Il server app memorizza nella cache il token di registrazione per le comunicazioni successive con l'app client. Facoltativamente, il server app può inviare un riconoscimento all'app client per indicare che il token di registrazione è stato ricevuto. Dopo aver eseguito questo handshake, l'app client può ricevere messaggi da o inviare messaggi al server app.

Quando l'app client non desidera più ricevere messaggi dal server app, può inviare una richiesta al server app per eliminare il token di registrazione. Se l'app client riceve messaggi di argomento, descritti più avanti in questo articolo, è possibile annullare la sottoscrizione dell'argomento.
Se l'app client viene disinstallata da un dispositivo, GCM lo rileva e invia automaticamente una notifica al server app per eliminare il token di registrazione.

Google sta [registrando le app client](https://developers.google.com/cloud-messaging/registration) illustra il processo di registrazione in modo più dettagliato. viene illustrata l'annullamento della registrazione e l'annullamento della sottoscrizione e viene descritto il processo di annullamento della registrazione quando viene disinstallata un'app client.

### <a name="downstream-messaging"></a>Messaggistica downstream

Quando il server applicazioni Invia un messaggio downstream all'app client, segue i passaggi illustrati nel diagramma seguente:

[diagramma di archiviazione e di avanzamento della messaggistica![downstream](google-cloud-messaging-images/03-downstream-sml.png)](google-cloud-messaging-images/03-downstream.png#lightbox)

1. Il server app invia il messaggio a GCM.

2. Se il dispositivo client non è disponibile, il server GCM archivia il messaggio in una coda per la trasmissione successiva.

3. Quando il dispositivo client è disponibile, GCM invia il messaggio all'app client sul dispositivo.

4. L'app client riceve il messaggio da GCM e lo gestisce di conseguenza. Se, ad esempio, il messaggio è una notifica remota, viene visualizzato all'utente.

In questo scenario di messaggistica (dove il server app invia un messaggio a una singola app client), i messaggi possono avere una lunghezza fino a 4kB.

Per informazioni dettagliate (inclusi esempi di codice) sulla ricezione di messaggi GCM downstream in Android, vedere [notifiche remote](~/android/data-cloud/google-messaging/remote-notifications-with-gcm.md).

#### <a name="topic-messaging"></a>Messaggistica degli argomenti

La *messaggistica degli argomenti* è un tipo di messaggistica downstream in cui il server applicazioni Invia un singolo messaggio a più dispositivi dell'app client che sottoscrivono un argomento, ad esempio una previsione meteorologica. I messaggi di argomento possono avere una lunghezza fino a 2 KB e la messaggistica degli argomenti supporta fino a 1 milione sottoscrizioni per app. Se GCM viene usato solo per la messaggistica degli argomenti, l'app client non è necessaria per inviare un token di registrazione al server app. La [messaggistica dell'argomento di implementazione](https://developers.google.com/cloud-messaging/topic-messaging) di Google spiega come inviare messaggi da un server app a più dispositivi che sottoscrivono un argomento specifico.

#### <a name="group-messaging"></a>Messaggistica del gruppo

La *messaggistica del gruppo* è un tipo di messaggistica downstream in cui il server app invia un singolo messaggio a più dispositivi dell'app client che appartengono a un gruppo (ad esempio, un gruppo di dispositivi che appartengono a un singolo utente). I messaggi di gruppo possono avere una lunghezza fino a 2 KB per i dispositivi iOS e fino a 4KB di lunghezza per i dispositivi Android. Un gruppo è limitato a un massimo di 20 membri. La [messaggistica del gruppo di dispositivi](https://developers.google.com/cloud-messaging/notifications) di Google spiega in che modo i server app possono inviare un singolo messaggio a più istanze di app client in esecuzione su dispositivi appartenenti a un gruppo.

### <a name="upstream-messaging"></a>Messaggistica upstream

Se l'app client si connette a un server che supporta [XMPP](https://developers.google.com/cloud-messaging/ccs), può inviare messaggi al server app come illustrato nel diagramma seguente:

[![diagramma di messaggistica upstream](google-cloud-messaging-images/04-upstream-sml.png)](google-cloud-messaging-images/04-upstream.png#lightbox)

1. L'app client invia un messaggio al server di connessione XMPP GCM.

2. Se il server app è disconnesso, il server GCM archivia il messaggio in una coda per l'invio successivo.

3. Quando il server app viene riconnesso, GCM lo trasmette al server app.

4. Il server app analizza il messaggio per verificare l'identità dell'app client e quindi Invia un "ACK" a GCM per confermare la ricezione del messaggio.

5. Il server app elabora il messaggio.

[I messaggi upstream](https://developers.google.com/cloud-messaging/ccs#upstream) di Google spiegano come strutturare i messaggi con codifica JSON e come inviarli ai server app che eseguono il server di connessione cloud basato su XMPP di Google.

<a name="settingup" />

## <a name="setting-up-google-cloud-messaging"></a>Configurazione di Google Cloud Messaging

Prima di poter usare i servizi GCM nell'app, è prima necessario acquisire le credenziali per l'accesso ai server GCM di Google. Le sezioni seguenti descrivono i passaggi necessari per completare il processo:

### <a name="enable-google-services-for-your-app"></a>Abilitare i servizi Google per l'app

1. Accedere a [Google Developers Console](https://developers.google.com/mobile/add?platform=android) con l'account Google (ovvero l'indirizzo Gmail) e creare un nuovo progetto. Se si dispone di un progetto esistente, scegliere il progetto che si desidera sia abilitato per GCM. Nell'esempio seguente viene creato un nuovo progetto denominato **XamarinGCM** :

    [![creazione di un progetto XamarinGCM](google-cloud-messaging-images/05-create-gcm-app-sml.png)](google-cloud-messaging-images/05-create-gcm-app.png#lightbox)

2. Immettere quindi il nome del pacchetto per l'app (in questo esempio, il nome del pacchetto è **com. Xamarin.gcmexample**) e fare clic su **continua per scegliere e configurare i servizi**:

    [![immissione del nome del pacchetto](google-cloud-messaging-images/06-package-name-sml.png)](google-cloud-messaging-images/06-package-name.png#lightbox)

    Si noti che il nome del pacchetto è anche l'ID applicazione per l'app.

3. La sezione **scegliere e configurare** i servizi elenca i servizi Google che è possibile aggiungere all'app. Fare clic su **messaggistica cloud**:

    [![scegliere la messaggistica cloud](google-cloud-messaging-images/07-choose-gcm-service-sml.png)](google-cloud-messaging-images/07-choose-gcm-service.png#lightbox)

4. Fare quindi clic su **Abilita Google Cloud Messaging**:

    [![Abilita Google Cloud Messaging](google-cloud-messaging-images/08-enable-gcm-sml.png)](google-cloud-messaging-images/08-enable-gcm.png#lightbox)

5. Per l'app vengono generate una **chiave API del server** e un **ID mittente** . Registrare questi valori e fare clic su **Chiudi**:

    [![la chiave API del server e l'ID mittente visualizzati](google-cloud-messaging-images/09-get-api-key-and-id-sml.png)](google-cloud-messaging-images/09-get-api-key-and-id.png#lightbox)

    Proteggere la chiave API &ndash; non è destinata all'uso pubblico. Se la chiave API è compromessa, i server non autorizzati possono pubblicare messaggi nelle applicazioni client.
    Le [procedure consigliate per l'uso sicuro delle chiavi API](https://support.google.com/cloud/answer/6310037?hl=en) offrono linee guida utili per la protezione della chiave API.

### <a name="view-your-project-settings"></a>Visualizzare le impostazioni del progetto

È possibile visualizzare le impostazioni del progetto in qualsiasi momento accedendo a [Google Cloud console](https://console.cloud.google.com/) e selezionando il progetto. Ad esempio, è possibile visualizzare l' **ID mittente** selezionando il progetto nel menu a discesa nella parte superiore della pagina (in questo esempio, il progetto è denominato **XamarinGCM**). L'ID mittente è il numero del progetto, come illustrato in questa schermata (l'ID mittente è **9349932736**):

[![visualizzare l'ID mittente](google-cloud-messaging-images/10-view-server-id-sml.png)](google-cloud-messaging-images/10-view-server-id.png#lightbox)

Per visualizzare la **chiave API**, fare clic su **gestione API** e quindi fare clic su **credenziali**:

[![la visualizzazione della chiave API](google-cloud-messaging-images/11-view-credentials-sml.png)](google-cloud-messaging-images/11-view-credentials.png#lightbox)

## <a name="for-further-reading"></a>Ulteriori informazioni

- Google [registra le app client](https://developers.google.com/cloud-messaging/registration) descrive il processo di registrazione client in modo più dettagliato e fornisce informazioni sulla configurazione del nuovo tentativo automatico e sulla sincronizzazione dello stato di registrazione.

- [Rfc 6120](https://tools.ietf.org/html/rfc6120) e [RFC 6121](https://tools.ietf.org/html/rfc6121) spiegano e definiscono il protocollo XMPP (Extensible Messaging and Presence Protocol).

## <a name="summary"></a>Riepilogo

In questo articolo è stata fornita una panoramica di Google Cloud Messaging (GCM). Sono state illustrate le varie credenziali usate per identificare e autorizzare la messaggistica tra i server applicazioni e le app client. Vengono illustrati gli scenari di messaggistica più comuni e vengono descritti in dettaglio i passaggi per la registrazione dell'app con GCM per l'uso dei servizi di GCM.

## <a name="related-links"></a>Collegamenti correlati

- [Messaggistica cloud](https://developers.google.com/cloud-messaging/)
