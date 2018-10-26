---
title: Firebase Cloud Messaging
description: Firebase Cloud Messaging (FCM) è un servizio che facilita la messaggistica tra App per dispositivi mobili e applicazioni server. Questo articolo offre una panoramica del funzionamento di FCM e viene spiegato come configurare i servizi di Google in modo che l'app può usare FCM.
ms.prod: xamarin
ms.assetid: E5314D7F-2AAC-40DA-BEBA-27C834F078DD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: bf0523a98c8e68691228b6e305265277e2925fa3
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116115"
---
# <a name="firebase-cloud-messaging"></a>Firebase Cloud Messaging

_Firebase Cloud Messaging (FCM) è un servizio che facilita la messaggistica tra App per dispositivi mobili e applicazioni server. Questo articolo offre una panoramica del funzionamento di FCM e viene spiegato come configurare i servizi di Google in modo che l'app può usare FCM._

[![Immagine di firebase Cloud Messaging hero](firebase-cloud-messaging-images/preview.png)](firebase-cloud-messaging-images/preview.png#lightbox)

In questo argomento fornisce una panoramica generale del modo in cui Firebase Cloud Messaging indirizza i messaggi tra le app xamarin. Android e un server di app e fornisce una procedura dettagliata per l'acquisizione delle credenziali in modo che l'app può usare servizi FCM.

## <a name="overview"></a>Panoramica

Firebase Cloud Messaging (FCM) è un servizio multipiattaforma che gestisce l'invio, il routing e Accodamento dei messaggi tra le applicazioni server e le app client per dispositivi mobili. FCM è il successore di Google Cloud Messaging (GCM), e si basa su Google Play Services.

Come illustrato nel diagramma seguente, FCM funge da intermediario tra i mittenti dei messaggi e i client. Oggetto *app client* è un'app abilitata per FCM che viene eseguito in un dispositivo. Il *server app* (fornito dall'utente o la società) è il server FCM abilitata l'app client con cui comunica attraverso FCM. A differenza di GCM, FCM rende possibile per poter inviare messaggi alle applicazioni client direttamente tramite la GUI di notifiche di Console di Firebase:

[![FCM si trova tra l'app client e un server di app](firebase-cloud-messaging-images/01-server-fcm-app-sml.png)](firebase-cloud-messaging-images/01-server-fcm-app.png#lightbox)

Usando FCM, i server di app possono inviare messaggi a un singolo dispositivo, a un gruppo di dispositivi o a un numero di dispositivi che hanno sottoscritto a un argomento. Un'app client può usare FCM per sottoscrivere i messaggi downstream da un server di app (ad esempio, per ricevere notifiche remote). Per altre informazioni sui diversi tipi di messaggi di Firebase, vedere [informazioni sui messaggi FCM](https://firebase.google.com/docs/cloud-messaging/concept-options).

## <a name="fcm-in-action"></a>Firebase Cloud Messaging in azione

Quando viene inviato un messaggio a valle per un'app client da un server di app, il server di app invia il messaggio a un *connessione server di FCM* fornito da Google; il server di connessione di FCM, a sua volta, inoltra il messaggio a un dispositivo che esegue il app client. È possibile inviare messaggi tramite HTTP o [XMPP](https://developers.google.com/cloud-messaging/ccs) (Extensible Messaging e presenza Protocol). Poiché le app client non siano sempre collegate o in esecuzione, la connessione server Accoda e archivi messaggi FCM, inviarli alle App client non appena riconnettersi e diventano disponibili. Analogamente, FCM enqueue upstream messaggi will dall'app client al server di app se il server di app è disponibile. Per altre informazioni sui server di connessione FCM, vedere [About Firebase Cloud Messaging Server](https://firebase.google.com/docs/cloud-messaging/server).

FCM utilizza le credenziali seguenti per identificare il server di app e l'app client e Usa queste credenziali per autorizzare le transazioni di messaggi tramite FCM:

-   <a name="fcm-in-action-sender-id"></a>**ID mittente** &ndash; le *ID mittente* rappresenta un valore numerico univoco assegnato quando si crea il progetto Firebase. L'ID del mittente è utilizzato per identificare il server di ogni app che può inviare messaggi all'app client. L'ID del mittente è anche il numero di progetto ottenere l'ID del mittente dalla Console Firebase quando si registra il progetto. È un esempio di un ID mittente `496915549731`.

-   <a name="fcm-in-action-api-key"></a>**Chiave API** &ndash; le *chiave API* consente l'accesso al server di app ai servizi Firebase; FCM Usa questa chiave per autenticare il server di app. Questa credenziale è detta anche il *chiave Server* o nella *chiave API Web*. Un esempio di una chiave API è `AJzbSyCTcpfRT1YRqbz-jIwp1h06YdauvewGDzk`.

-   <a name="fcm-in-action-app-id"></a>**ID app** &ndash; l'identità dell'app client (indipendente da qualsiasi dispositivo specifico) che esegue la registrazione per ricevere messaggi da FCM. Un esempio di un ID App è `1:415712510732:android:0e1eb7a661af2460`.

-   <a name="fcm-in-action-registration-token"></a>**Token di registrazione** &ndash; le *Token di registrazione* (noto anche come il *ID istanza*) è l'identità FCM dell'app client in un determinato dispositivo. Il token di registrazione viene generato in fase di esecuzione &ndash; l'app riceve un token di registrazione al momento prima della registrazione in FCM durante l'esecuzione in un dispositivo. Il token di registrazione autorizza un'istanza dell'app client (in esecuzione in quel particolare dispositivo) per ricevere messaggi da FCM.
    Un esempio di un token di registrazione è `fkBQTHxKKhs:AP91bHuEedxM4xFAUn0z ... JKZS` (una stringa molto lunga).

[Impostazione di Firebase Cloud Messaging](#setup_fcm) (più avanti in questa Guida) vengono fornite istruzioni dettagliate per la creazione di un progetto e la generazione di queste credenziali. Quando si crea un nuovo progetto nel [Console di Firebase](https://console.firebase.google.com/), un file di credenziali denominato **google-Services. JSON** viene creato &ndash; aggiungere questo file al progetto xamarin. Android come descritto in [ Notifiche remote con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

Le sezioni seguenti illustrano come queste credenziali vengono usate quando le app client di comunicare con i server di app tramite FCM.


<a name="registration" />

### <a name="registration-with-fcm"></a>Registrazione in FCM

Un'app client deve innanzitutto registrare con FCM prima di messaggistica può essere eseguite. L'app client è necessario completare i passaggi di registrazione illustrati nel diagramma seguente:

[![Diagramma di questa procedura di registrazione App](firebase-cloud-messaging-images/02-app-registration-sml.png)](firebase-cloud-messaging-images/02-app-registration.png#lightbox)

1.  L'app client contatta FCM per ottenere un token di registrazione, passando l'ID mittente, chiave API e App ID di FCM.

2.  FCM restituisce un token di registrazione per l'app client.

3.  (Facoltativamente), l'app client inoltra il token di registrazione al server di app.

Il server app memorizza nella cache il token di registrazione per ricevere altre comunicazioni successive con l'app client. Il server di app può inviare un acknowledgement tornare all'app client per indicare che è stato ricevuto il token di registrazione. Dopo l'esecuzione di questo handshake, l'app client può ricevere messaggi da (o inviare messaggi a) del server applicazioni. L'app client può ricevere un nuovo token di registrazione se il token precedente viene compromesso (vedere [notifiche Remote con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md) per un esempio di come un'app riceve gli aggiornamenti di token di registrazione).

Quando l'app client non è più vuole ricevere messaggi dal server di app, può inviare una richiesta al server di app per eliminare il token di registrazione. Se l'app client è disinstallato da un dispositivo, FCM rileva tale situazione e si riceve una notifica al server di app di eliminare il token di registrazione.



### <a name="downstream-messaging"></a>Messaggistica a valle

Il diagramma seguente illustra come Firebase Cloud Messaging memorizza e inoltra i messaggi a valle:

[![FCM Usa l'archiviazione e inoltro per la messaggistica a valle](firebase-cloud-messaging-images/03-downstream-sml.png)](firebase-cloud-messaging-images/03-downstream.png#lightbox)

Quando il server app invia un messaggio a valle per l'app client, vengono utilizzati i passaggi seguenti come enumerati nel diagramma precedente:

1.  Il server app invia il messaggio da FCM.

2.  Se il dispositivo client non è disponibile, il server FCM archivia il messaggio in una coda per una trasmissione successiva. I messaggi vengono mantenuti in archiviazione di FCM per un massimo di 4 settimane (per altre informazioni, vedere [impostando il ciclo di vita di un messaggio](https://firebase.google.com/docs/cloud-messaging/concept-options#ttl)).

3.  Quando il dispositivo client è disponibile, FCM inoltra il messaggio per l'app client in tale dispositivo.

4.  L'app client riceve il messaggio da FCM, lo elabora e lo visualizza all'utente. Ad esempio, se il messaggio è una notifica remota, viene visualizzata all'utente nell'area di notifica.

In questo scenario di messaggistica (in cui il server app invia un messaggio a un'app client singolo), i messaggi possono essere fino a 4kB di lunghezza.

Per informazioni dettagliate sulla ricezione di messaggi FCM downstream in Android, vedere [notifiche Remote con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

### <a name="topic-messaging"></a>Argomento di messaggistica

*Argomento messaggistica* rende possibile per un server di app inviare un messaggio a più dispositivi che hanno acconsentito esplicitamente a un argomento particolare. È anche possibile comporre e inviare i messaggi dell'argomento tramite la GUI di notifiche di Console di Firebase. FCM gestisce il routing e al recapito dei messaggi di argomento ai client sottoscritti. Questa funzionalità è utilizzabile per i messaggi quali avvisi meteo, le quotazioni di azioni e le notizie di intestazione.

[![Diagramma di messaggistica di argomento](firebase-cloud-messaging-images/04-topic-messaging-sml.png)](firebase-cloud-messaging-images/04-topic-messaging.png#lightbox)

Vengono usati i passaggi seguenti nell'argomento di messaggistica (dopo che l'app client ottiene un token di registrazione, come illustrato in precedenza):

1.  L'app client sottoscrive un argomento, inviare un messaggio di "Subscribe" a FCM.

2.  Il server app invia i messaggi dell'argomento a FCM per la distribuzione.

3.  FCM inoltra i messaggi dell'argomento per i client che hanno sottoscritto a tale argomento.

Per altre informazioni sull'argomento Firebase messaging, vedere di Google [argomento di messaggistica in Android](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging).


<a name="setup_fcm" />

## <a name="setting-up-firebase-cloud-messaging"></a>Configurazione di Firebase Cloud Messaging

Prima di utilizzare i servizi FCM nell'app, è necessario creare un nuovo progetto (o importare un progetto esistente) tramite il [Console di Firebase](https://console.firebase.google.com/). Usare la procedura seguente per creare un progetto Firebase Cloud Messaging per l'app:

1.  Accedi il [Console di Firebase](https://console.firebase.google.com/) con l'account di Google (ad esempio, l'indirizzo Gmail) e fare clic su **Crea nuovo progetto**:

    [![Creare il pulsante nuovo progetto](firebase-cloud-messaging-images/05-firebase-console-sml.png)](firebase-cloud-messaging-images/05-firebase-console.png#lightbox)

    Se si dispone di un progetto esistente, fare clic su **importare un progetto Google**.

2.  Nel **creare un progetto** finestra di dialogo immettere il nome del progetto e fare clic su **Crea progetto**. Nell'esempio seguente, un nuovo progetto denominato **XamarinFCM** creato:

    [![Creare una finestra di dialogo del progetto](firebase-cloud-messaging-images/06-create-a-project-sml.png)](firebase-cloud-messaging-images/06-create-a-project.png#lightbox)

3.  Nella Console di Firebase **Overview**, fare clic su **aggiungere Firebase all'App Android**:

    [![Aggiungere Firebase all'App Android](firebase-cloud-messaging-images/07-add-firebase-sml.png)](firebase-cloud-messaging-images/07-add-firebase.png#lightbox)

4.  Nella schermata successiva, immettere il nome del pacchetto dell'app. In questo esempio, è il nome del pacchetto **com.xamarin.fcmexample**. Questo valore deve corrispondere al nome del pacchetto dell'app Android. Un nome alternativo dell'app può anche essere immesso nel **soprannome App** campo:

    [![Se si immette esempio FCM come nome alternativo dell'app](firebase-cloud-messaging-images/08-package-name-sml.png)](firebase-cloud-messaging-images/08-package-name.png#lightbox)

5.  Se l'app Usa i collegamenti dinamici, inviti o Google Authentication, è necessario immettere anche il certificato di firma di debug. Per altre informazioni sull'individuazione del certificato di firma, vedere [individuazione firma SHA1 o MD5 dell'archivio chiavi](~/android/deploy-test/signing/keystore-signature.md).
    In questo esempio, il certificato di firma viene lasciato vuoto.

6.  Fare clic su **Aggiungi APP**:

    [![Fare clic sul pulsante Aggiungi App](firebase-cloud-messaging-images/09-add-app-sml.png)](firebase-cloud-messaging-images/09-add-app.png#lightbox)

    Una chiave API del Server e un ID Client vengono generati automaticamente per l'app. Queste informazioni sono compresse un **google-Services. JSON** file che viene scaricato automaticamente quando si fa clic **Aggiungi APP**.
    Assicurarsi di salvare il file in un luogo sicuro.

Per un esempio dettagliato di come aggiungere **google-Services. JSON** a un progetto di app per la ricezione dei messaggi di notifica push FCM in Android, vedere [notifiche Remote con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).



## <a name="for-further-reading"></a>Per altre informazioni, vedere

-   Google [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) fornisce una panoramica di funzionalità chiave della Firebase Cloud Messaging, una spiegazione del funzionamento e le istruzioni di installazione.

-   Google [richieste di invio Server compilare App](https://firebase.google.com/docs/cloud-messaging/send-message) illustra come inviare messaggi con il server di app.

-   [RFC 6120](https://tools.ietf.org/html/rfc6120) e [RFC 6121](https://tools.ietf.org/html/rfc6121) spiegare e definire l'Extensible Messaging and presenza protocollo XMPP ().

-   [Informazioni sui messaggi FCM](https://firebase.google.com/docs/cloud-messaging/concept-options) descrive i diversi tipi di messaggi che possono essere inviati con Firebase Cloud Messaging.

## <a name="summary"></a>Riepilogo

Questo articolo è fornita una panoramica di Firebase Cloud Messaging (FCM). Spiega le credenziali diverse che consentono di identificare e autorizzare la messaggistica tra i server di app e le app client. Illustrata la registrazione e gli scenari di messaggistica a valle e sono stati descritti i passaggi per la registrazione dell'app con FCM, usare i servizi FCM.


## <a name="related-links"></a>Collegamenti correlati

- [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/)
