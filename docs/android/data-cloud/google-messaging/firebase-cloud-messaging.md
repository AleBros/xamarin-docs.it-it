---
title: Firebase Cloud Messaging
description: Firebase Cloud Messaging (FCM) è un servizio che facilita la messaggistica tra le app per dispositivi mobili e le applicazioni server. Questo articolo fornisce una panoramica del funzionamento di FCM e spiega come configurare i servizi Google in modo che l'app possa usare FCM.
ms.prod: xamarin
ms.assetid: E5314D7F-2AAC-40DA-BEBA-27C834F078DD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/31/2018
ms.openlocfilehash: acb0e9ea23d4043a72b9d2688501c576a3d43358
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84570212"
---
# <a name="firebase-cloud-messaging"></a>Firebase Cloud Messaging

_Firebase Cloud Messaging (FCM) è un servizio che facilita la messaggistica tra le app per dispositivi mobili e le applicazioni server. Questo articolo fornisce una panoramica del funzionamento di FCM e spiega come configurare i servizi Google in modo che l'app possa usare FCM._

[![Immagine di Firebase Cloud Messaging Hero](firebase-cloud-messaging-images/preview.png)](firebase-cloud-messaging-images/preview.png#lightbox)

Questo argomento fornisce una panoramica generale del modo in cui Firebase Cloud Messaging instrada i messaggi tra l'app Novell. Android e un server app e fornisce una procedura dettagliata per l'acquisizione delle credenziali in modo che l'app possa usare i servizi FCM.

## <a name="overview"></a>Panoramica

Firebase Cloud Messaging (FCM) è un servizio multipiattaforma che gestisce l'invio, il routing e l'accodamento dei messaggi tra applicazioni server e app client per dispositivi mobili. FCM è il successore di Google Cloud Messaging (GCM) ed è basato su Google Play Services.

Come illustrato nel diagramma seguente, FCM funge da intermediario tra i mittenti e i client dei messaggi. Un' *app client* è un'app abilitata per FCM che viene eseguita in un dispositivo. Il *Server App* , fornito dall'utente o dall'azienda, è il server abilitato per FCM con cui l'app client comunica tramite FCM. A differenza di GCM, FCM consente di inviare messaggi alle app client direttamente tramite l'interfaccia utente grafica delle notifiche della console di Firebase:

[![FCM si trova tra l'app client e un server app](firebase-cloud-messaging-images/01-server-fcm-app-sml.png)](firebase-cloud-messaging-images/01-server-fcm-app.png#lightbox)

Con FCM, i server app possono inviare messaggi a un singolo dispositivo, a un gruppo di dispositivi o a un numero di dispositivi sottoscritti a un argomento. Un'app client può usare FCM per sottoscrivere messaggi downstream da un server app (ad esempio, per ricevere notifiche remote). Per ulteriori informazioni sui diversi tipi di messaggi Firebase, vedere [About FCM messages](https://firebase.google.com/docs/cloud-messaging/concept-options).

## <a name="firebase-cloud-messaging-in-action"></a><a name="fcm-in-action"></a>Messaggistica cloud Firebase in azione

Quando un messaggio downstream viene inviato a un'app client da un server applicazioni, il server app invia il messaggio a un *server di connessione FCM* fornito da Google; il server di connessione FCM, a sua volta, trasmette il messaggio a un dispositivo che esegue l'app client. I messaggi possono essere inviati tramite HTTP o [XMPP](https://firebase.google.com/docs/cloud-messaging/xmpp-server-ref) (Extensible Messaging and Presence Protocol). Poiché le app client non sono sempre connesse o in esecuzione, il server di connessione FCM Accoda e archivia i messaggi, inviarli alle app client quando si riconnettono e diventano disponibili. Analogamente, FCM accoda i messaggi upstream dall'app client al server app se il server app non è disponibile. Per ulteriori informazioni sui server di connessione FCM, vedere [informazioni su Firebase Cloud Messaging Server](https://firebase.google.com/docs/cloud-messaging/server).

FCM usa le credenziali seguenti per identificare il server app e l'app client e usa queste credenziali per autorizzare le transazioni dei messaggi tramite FCM:

- <a name="fcm-in-action-sender-id"></a>**ID mittente** &ndash; L' *ID mittente* è un valore numerico univoco assegnato al momento della creazione del progetto Firebase. L'ID mittente viene usato per identificare ogni server app che può inviare messaggi all'app client. L'ID mittente è anche il numero di progetto. Quando si registra il progetto, si ottiene l'ID mittente dalla console di Firebase. Un esempio di ID mittente è `496915549731` .

- <a name="fcm-in-action-api-key"></a>**Chiave API** &ndash; La *chiave API* consente all'app server di accedere ai servizi di Firebase. FCM usa questa chiave per autenticare il server app. Questa credenziale viene anche definita *chiave server* o *chiave API Web*. Un esempio di chiave API è `AJzbSyCTcpfRT1YRqbz-jIwp1h06YdauvewGDzk` .

- <a name="fcm-in-action-app-id"></a>**ID app** &ndash; Identità dell'app client (indipendente da qualsiasi dispositivo specificato) che esegue la registrazione per ricevere i messaggi da FCM. Un esempio di ID app è `1:415712510732:android:0e1eb7a661af2460` .

- <a name="fcm-in-action-registration-token"></a>**Token** &ndash; di registrazione Il *token di registrazione* (noto anche come *ID istanza*) è l'identità FCM dell'app client in un determinato dispositivo. Il token di registrazione viene generato in fase di esecuzione &ndash; . l'app riceve un token di registrazione quando viene registrato per la prima volta con FCM mentre è in esecuzione in un dispositivo. Il token di registrazione autorizza un'istanza dell'app client (in esecuzione su quel particolare dispositivo) a ricevere i messaggi da FCM.
    Un esempio di token di registrazione è `fkBQTHxKKhs:AP91bHuEedxM4xFAUn0z ... JKZS` (una stringa molto lungo).

La [configurazione di Firebase Cloud Messaging](#setup_fcm) (più avanti in questa guida) fornisce istruzioni dettagliate per la creazione di un progetto e la generazione di queste credenziali. Quando si crea un nuovo progetto nella [console di Firebase](https://console.firebase.google.com/), viene creato un file di credenziali denominato **Google-Services. JSON.** &ndash; aggiungere questo file al progetto Novell. Android come descritto [in notifiche remote con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

Le sezioni seguenti illustrano come usare queste credenziali quando le app client comunicano con i server app tramite FCM.

<a name="registration"></a>

### <a name="registration-with-fcm"></a>Registrazione con FCM

Prima di eseguire la messaggistica è necessario che un'app client si registri prima con FCM. L'app client deve completare la procedura di registrazione illustrata nel diagramma seguente:

[![Diagramma passaggi di registrazione app](firebase-cloud-messaging-images/02-app-registration-sml.png)](firebase-cloud-messaging-images/02-app-registration.png#lightbox)

1. L'app client contatta FCM per ottenere un token di registrazione, passando l'ID mittente, la chiave API e l'ID app a FCM.

2. FCM restituisce un token di registrazione all'app client.

3. L'app client (facoltativamente) Invia il token di registrazione al server app.

Il server app memorizza nella cache il token di registrazione per le comunicazioni successive con l'app client. Il server app può inviare un riconoscimento all'app client per indicare che il token di registrazione è stato ricevuto. Dopo aver eseguito questo handshake, l'app client può ricevere messaggi da o inviare messaggi al server app. L'app client può ricevere un nuovo token di registrazione se il token precedente è compromesso (vedere [le notifiche remote con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md) per un esempio di come un'app riceve gli aggiornamenti del token di registrazione).

Quando l'app client non desidera più ricevere messaggi dal server app, può inviare una richiesta al server app per eliminare il token di registrazione. Se l'app client viene disinstallata da un dispositivo, FCM lo rileva e invia automaticamente una notifica al server app per eliminare il token di registrazione.

### <a name="downstream-messaging"></a>Messaggistica downstream

Il diagramma seguente illustra il modo in cui Firebase Cloud Messaging archivia e trasmette i messaggi downstream:

[![FCM usa l'archivio e l'avanzamento per la messaggistica downstream](firebase-cloud-messaging-images/03-downstream-sml.png)](firebase-cloud-messaging-images/03-downstream.png#lightbox)

Quando il server applicazioni Invia un messaggio downstream all'app client, USA i passaggi seguenti come enumerato nel diagramma precedente:

1. Il server app invia il messaggio a FCM.

2. Se il dispositivo client non è disponibile, il server FCM archivia il messaggio in una coda per la trasmissione successiva. I messaggi vengono conservati nell'archiviazione FCM per un massimo di 4 settimane. per ulteriori informazioni, vedere [impostazione della durata di un messaggio](https://firebase.google.com/docs/cloud-messaging/concept-options#ttl).

3. Quando il dispositivo client è disponibile, FCM invia il messaggio all'app client nel dispositivo.

4. L'app client riceve il messaggio da FCM, lo elabora e lo Visualizza all'utente. Se, ad esempio, il messaggio è una notifica remota, viene visualizzato all'utente nell'area di notifica.

In questo scenario di messaggistica (dove il server app invia un messaggio a una singola app client), i messaggi possono avere una lunghezza fino a 4kB.

Per informazioni dettagliate sulla ricezione di messaggi FCM downstream in Android, vedere [notifiche remote con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

### <a name="topic-messaging"></a>Messaggistica degli argomenti

La *messaggistica degli argomenti* consente a un server applicazioni di inviare un messaggio a più dispositivi che hanno scelto un argomento specifico. È anche possibile comporre e inviare messaggi di argomento tramite l'interfaccia utente grafica delle notifiche Firebase console. FCM gestisce il routing e il recapito dei messaggi di argomento ai client sottoscritti. Questa funzionalità può essere usata per messaggi quali avvisi meteorologici, quotazioni azionarie e notizie sul titolo.

[![Diagramma di messaggistica dell'argomento](firebase-cloud-messaging-images/04-topic-messaging-sml.png)](firebase-cloud-messaging-images/04-topic-messaging.png#lightbox)

I passaggi seguenti vengono usati nell'argomento messaggistica (dopo che l'app client ottiene un token di registrazione come illustrato in precedenza):

1. L'app client sottoscrive un argomento inviando un messaggio di sottoscrizione a FCM.

2. Il server app invia messaggi di argomento a FCM per la distribuzione.

3. FCM invia i messaggi di argomento ai client che hanno sottoscritto tale argomento.

Per altre informazioni sulla messaggistica degli argomenti di Firebase, vedere l' [argomento sulla messaggistica di Google in Android](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging).

<a name="setup_fcm"></a>

## <a name="setting-up-firebase-cloud-messaging"></a>Configurazione di Firebase Cloud Messaging

Prima di poter usare i servizi FCM nell'app, è necessario creare un nuovo progetto (o importare un progetto esistente) tramite la [console di Firebase](https://console.firebase.google.com/). Usare la procedura seguente per creare un progetto di messaggistica cloud Firebase per l'app:

1. Accedere alla [console di Firebase](https://console.firebase.google.com/) con l'account Google (ad esempio, l'indirizzo Gmail) e fare clic su **Crea nuovo progetto**:

    [![Pulsante Crea nuovo progetto](firebase-cloud-messaging-images/05-firebase-console-sml.png)](firebase-cloud-messaging-images/05-firebase-console.png#lightbox)

    Se si dispone di un progetto esistente, fare clic su **Importa un progetto Google**.

2. Nella finestra di dialogo **Crea un progetto** immettere il nome del progetto e fare clic su **Crea progetto**. Nell'esempio seguente viene creato un nuovo progetto denominato **XamarinFCM** :

    [![Finestra di dialogo Crea progetto](firebase-cloud-messaging-images/06-create-a-project-sml.png)](firebase-cloud-messaging-images/06-create-a-project.png#lightbox)

3. Nella **Panoramica**della console Firebase fare clic su **Aggiungi Firebase all'app Android**:

    [![Aggiungere Firebase all'app Android](firebase-cloud-messaging-images/07-add-firebase-sml.png)](firebase-cloud-messaging-images/07-add-firebase.png#lightbox)

4. Nella schermata successiva immettere il nome del pacchetto dell'app. In questo esempio, il nome del pacchetto è **com. Novell. fcmexample**. Questo valore deve corrispondere al nome del pacchetto dell'app Android. È anche possibile immettere un nome alternativo per l'app nel campo del **soprannome dell'app** :

    [![Immissione dell'esempio di FCM come nome alternativo dell'app](firebase-cloud-messaging-images/08-package-name-sml.png)](firebase-cloud-messaging-images/08-package-name.png#lightbox)

5. Se l'app usa collegamenti dinamici, inviti o Google auth, è necessario immettere anche il certificato di firma del debug. Per altre informazioni sull'individuazione del certificato di firma, vedere [ricerca della firma MD5 o SHA1 dell'archivio](~/android/deploy-test/signing/keystore-signature.md)chiavi.
    In questo esempio, il certificato di firma viene lasciato vuoto.

6. Fare clic su **Aggiungi app**:

    [![Fare clic sul pulsante Aggiungi app](firebase-cloud-messaging-images/09-add-app-sml.png)](firebase-cloud-messaging-images/09-add-app.png#lightbox)

    Una chiave API del server e un ID client vengono generati automaticamente per l'app. Queste informazioni vengono assemblate in un file **Google-Services. JSON** che viene scaricato automaticamente quando si fa clic su **Aggiungi app**.
    Assicurarsi di salvare il file in un luogo sicuro.

Per un esempio dettagliato di come aggiungere **Google-Services. JSON** a un progetto di app per ricevere i messaggi di notifica push FCM in Android, vedere [notifiche remote con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

## <a name="for-further-reading"></a>Per altre informazioni

- [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) di Google offre una panoramica delle funzionalità chiave di Firebase per la messaggistica cloud, una spiegazione del funzionamento e le istruzioni di configurazione.

- [Le richieste di invio del server applicazioni di compilazione](https://firebase.google.com/docs/cloud-messaging/send-message) di Google spiegano come inviare messaggi con il server app.

- [Rfc 6120](https://tools.ietf.org/html/rfc6120) e [RFC 6121](https://tools.ietf.org/html/rfc6121) spiegano e definiscono il protocollo XMPP (Extensible Messaging and Presence Protocol).

- [Informazioni sui messaggi FCM](https://firebase.google.com/docs/cloud-messaging/concept-options) descrive i diversi tipi di messaggi che possono essere inviati con Firebase Cloud Messaging.

## <a name="summary"></a>Summary

Questo articolo fornisce una panoramica di Firebase Cloud Messaging (FCM). Sono state illustrate le varie credenziali usate per identificare e autorizzare la messaggistica tra i server applicazioni e le app client. Vengono illustrati gli scenari di registrazione e di messaggistica downstream e vengono descritti in dettaglio i passaggi per la registrazione dell'app con FCM per l'uso dei servizi FCM.

## <a name="related-links"></a>Collegamenti correlati

- [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/)
