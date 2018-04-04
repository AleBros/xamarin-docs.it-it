---
title: Firebase Cloud Messaging
description: Messaggistica Cloud firebase (FCM) è un servizio che facilita la messaggistica tra le applicazioni per dispositivi mobili e server. In questo articolo viene fornita una panoramica del funzionamento FCM e viene spiegato come configurare i servizi Google in modo che l'applicazione può utilizzare FCM.
ms.prod: xamarin
ms.assetid: E5314D7F-2AAC-40DA-BEBA-27C834F078DD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: ef2c23d16545d03dc267054a96f8b0f8883afcf1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="firebase-cloud-messaging"></a>Firebase Cloud Messaging

_Messaggistica Cloud firebase (FCM) è un servizio che facilita la messaggistica tra le applicazioni per dispositivi mobili e server. In questo articolo viene fornita una panoramica del funzionamento FCM e viene spiegato come configurare i servizi Google in modo che l'applicazione può utilizzare FCM._

[![Immagine di hero Cloud Messaging firebase](firebase-cloud-messaging-images/preview.png)](firebase-cloud-messaging-images/preview.png#lightbox)

In questo argomento fornisce una panoramica generale del modo Firebase Cloud Messaging indirizza i messaggi tra l'app xamarin e un server di app e fornisce una procedura dettagliata per l'acquisizione delle credenziali in modo che l'app è possibile utilizzare servizi FCM.



## <a name="overview"></a>Panoramica

Messaggistica Cloud firebase (FCM) è un servizio multipiattaforma che gestisce l'invio, routing e Accodamento dei messaggi tra applicazioni server e le applicazioni client mobili. È stato progettato su Google Play Services FCM è il successore di Google Cloud Messaging (GCM).

Come illustrato nel diagramma seguente, FCM funge da intermediario tra mittenti e i client. Oggetto *app client* è un'app abilitata FCM che viene eseguito in un dispositivo. Il *server app* (fornito dall'utente o la società) è il server FCM abilitato l'app client con cui comunica attraverso FCM. Diversamente da GCM, FCM consente di inviare messaggi alle applicazioni client direttamente tramite l'interfaccia utente grafica di Firebase Console notifiche:

[![FCM si trova tra l'app client e un server di app](firebase-cloud-messaging-images/01-server-fcm-app-sml.png)](firebase-cloud-messaging-images/01-server-fcm-app.png#lightbox)

Utilizzo FCM, i server app possono inviare messaggi in un singolo dispositivo, a un gruppo di dispositivi o a un numero di dispositivi che sono ha sottoscritto un argomento. Un'app client può utilizzare FCM per sottoscrivere i messaggi downstream da un server di applicazione (ad esempio, per ricevere le notifiche remote). Per ulteriori informazioni sui diversi tipi di messaggi Firebase, vedere [informazioni sui messaggi FCM](https://firebase.google.com/docs/cloud-messaging/concept-options).



## <a name="firebase-cloud-messaging-in-action"></a>Cloud firebase messaggistica in azione

Quando un messaggio a valle viene inviato a un'applicazione client da un server di app, il server di app invia il messaggio da un *server connessione FCM* fornito da Google; il server di connessione FCM, a sua volta, inoltra il messaggio a un dispositivo che esegue il applicazione client. È possibile inviare messaggi su HTTP o [XMPP](https://developers.google.com/cloud-messaging/ccs) (messaggistica estendibile e presenza Protocol). Poiché le applicazioni client non sono sempre connessi o in esecuzione, FCM connessione server Accoda e archivi di messaggi, vengono inviati al client App man mano che riconnettersi e diventano disponibili. Analogamente, FCM verrà Accodamento messaggi upstream dall'app client al server app se il server di app non è disponibile. Per ulteriori informazioni sui server di connessione FCM, vedere [su Firebase messaggistica Server Cloud](https://firebase.google.com/docs/cloud-messaging/server).

FCM utilizza le credenziali seguenti per identificare il server di app e l'applicazione client e Usa queste credenziali per autorizzare le transazioni di messaggio attraverso FCM:

-   **ID mittente** &ndash; il *ID mittente* è un valore numerico univoco che viene assegnato quando si crea il progetto Firebase. L'ID del mittente è utilizzato per identificare ogni server di app che possono inviare messaggi all'applicazione client. L'ID del mittente è anche il numero di progetto ottenere l'ID del mittente dalla Console di Firebase quando si registra il progetto. Un esempio di un ID mittente è `496915549731`.

-   **Chiave API** &ndash; il *chiave API* fornisce l'accesso al server app ai servizi Firebase; FCM utilizza questa chiave per autenticare il server di app. Questa credenziale è detta anche il *chiave Server* o *chiave API Web*. Un esempio di una chiave API è `AJzbSyCTcpfRT1YRqbz-jIwp1h06YdauvewGDzk`.

-   **ID dell'app** &ndash; l'identità dell'applicazione client (indipendente da qualsiasi dispositivo specifico) che esegue la registrazione per ricevere messaggi da FCM. Un esempio di un ID dell'App è `1:415712510732:android:0e1eb7a661af2460`.

-   **Token di registrazione** &ndash; il *Token di registrazione* (detta anche il *ID istanza*) è l'identità FCM dell'app client in un determinato dispositivo. Il token di registrazione viene generato in fase di esecuzione &ndash; app riceve un token di registrazione al momento prima della registrazione con FCM durante l'esecuzione in un dispositivo. Il token di registrazione per ricevere messaggi da FCM, si autorizza un'istanza dell'applicazione client (in esecuzione nel dispositivo specifico).
    Un esempio di un token di registrazione è `fkBQTHxKKhs:AP91bHuEedxM4xFAUn0z ... JKZS` (una lunga stringa).

[Impostazione di Firebase Cloud Messaging](#setup_fcm) (più avanti in questa Guida) vengono fornite istruzioni dettagliate per la creazione di un progetto e la generazione di queste credenziali. Quando si crea un nuovo progetto nel [Firebase Console](https://console.firebase.google.com/), un file di credenziali denominato **google services.json** viene creato &ndash; aggiungere file al progetto xamarin, come descritto in [ Notifiche remote con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

Le sezioni seguenti illustrano come queste credenziali vengono utilizzate quando le applicazioni client di comunicare con i server app attraverso FCM.


<a name="registration" />

### <a name="registration-with-fcm"></a>Registrazione con FCM

Un'applicazione client deve innanzitutto registrare con FCM prima di messaggistica può avvenire. L'applicazione client è necessario completare la procedura di registrazione illustrata nel diagramma seguente:

[![Diagramma di passaggi di registrazione App](firebase-cloud-messaging-images/02-app-registration-sml.png)](firebase-cloud-messaging-images/02-app-registration.png#lightbox)

1.  L'app client contatta FCM per ottenere un token di registrazione, passare l'ID mittente, la chiave API e ID App FCM.

2.  FCM restituisce un token di registrazione per l'applicazione client.

3.  L'app client (facoltativamente) inoltra il token di registrazione al server app.

Il server app memorizza nella cache il token di registrazione per le comunicazioni successive con l'applicazione client. Il server di app può inviare un riconoscimento tornare all'app client per indicare che è stato ricevuto il token di registrazione. Dopo l'operazione di handshake viene eseguita, l'applicazione client può ricevere messaggi da (o inviare messaggi a) il server di app. L'applicazione client potrebbe ricevere un nuovo token di registrazione se il token precedente è compromesso (vedere [remoto notifiche con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md) per un esempio del modo in cui un'applicazione riceve gli aggiornamenti di token di registrazione).

Quando l'app client non desidera ricevere messaggi dal server di app, è possibile inviare una richiesta al server di app per eliminare il token di registrazione. Se l'applicazione client viene disinstallato da un dispositivo, FCM rileva e invia automaticamente una notifica al server di app di eliminare il token di registrazione.



### <a name="downstream-messaging"></a>Messaggistica downstream

Nel diagramma seguente viene illustrato come Firebase Cloud Messaging archivia e inoltra i messaggi downstream:

[![FCM Usa archivio e l'inoltro per la messaggistica a valle](firebase-cloud-messaging-images/03-downstream-sml.png)](firebase-cloud-messaging-images/03-downstream.png#lightbox)

Quando il server app invia un messaggio a valle per l'app client, vengono utilizzati i passaggi seguenti come enumerati nel diagramma precedente:

1.  Il server app invia il messaggio FCM.

2.  Se il dispositivo client non è disponibile, il server FCM archivia il messaggio in una coda per la trasmissione di versioni successive. I messaggi vengono mantenuti nel servizio di archiviazione per un massimo di 4 settimane FCM (per ulteriori informazioni, vedere [impostando il ciclo di vita di un messaggio](https://firebase.google.com/docs/cloud-messaging/concept-options#ttl)).

3.  Quando il dispositivo client è disponibile, FCM inoltra il messaggio all'app sul dispositivo client.

4.  L'applicazione client riceve il messaggio da FCM, elabora e visualizza all'utente. Ad esempio, se il messaggio è una notifica remota, viene visualizzato all'utente nell'area di notifica.

In questo scenario di messaggistica (in cui il server app invia un messaggio a un'app client singolo), i messaggi possono essere di lunghezza fino a 4kB.

Per informazioni dettagliate sulla ricezione dei messaggi FCM a valle in Android, vedere [remoto notifiche con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

### <a name="topic-messaging"></a>Argomento di messaggistica

*Argomento messaggistica* rende possibile per un server di applicazione inviare un messaggio a più dispositivi che hanno aderito a un determinato argomento. È anche possibile comporre e inviare i messaggi di argomento tramite l'interfaccia utente grafica di Firebase Console notifiche. FCM gestisce il routing e al recapito dei messaggi di argomento per i client sottoscritti. Questa funzionalità è utilizzabile per i messaggi quali avvisi meteo, quotazioni azionarie e notizie titolo.

[![Diagramma di messaggistica di argomento](firebase-cloud-messaging-images/04-topic-messaging-sml.png)](firebase-cloud-messaging-images/04-topic-messaging.png#lightbox)

I passaggi seguenti vengono utilizzati nella messaggistica argomento (dopo l'applicazione client riceve un token di registrazione, come spiegato in precedenza):

1.  L'app client effettua la sottoscrizione a un argomento inviando un messaggio di sottoscrizione per FCM.

2.  Il server app invia messaggi argomento FCM per la distribuzione.

3.  FCM inoltra i messaggi di argomento per i client che hanno sottoscritto tale argomento.

Per ulteriori informazioni sulla messaggistica argomento Firebase, vedere Google [argomento messaggistica in Android](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging).


<a name="setup_fcm" />

## <a name="setting-up-firebase-cloud-messaging"></a>Impostazione Firebase Cloud Messaging

Prima di utilizzare servizi FCM nell'app, è necessario creare un nuovo progetto (o importare un progetto esistente) tramite il [Firebase Console](https://console.firebase.google.com/). Utilizzare la procedura seguente per creare un progetto Cloud Firebase Messaging per l'app:

1.  Accedere al [Firebase Console](https://console.firebase.google.com/) con l'account Google (ad esempio, l'indirizzo Gmail) e fare clic su **Crea nuovo progetto**:

    [![Creare il pulsante nuovo progetto](firebase-cloud-messaging-images/05-firebase-console-sml.png)](firebase-cloud-messaging-images/05-firebase-console.png#lightbox)

    Se si dispone di un progetto esistente, fare clic su **importare un progetto di Google**.

2.  Nel **creare un progetto** finestra di dialogo, immettere il nome del progetto e fare clic su **Crea progetto**. Nell'esempio seguente, un nuovo progetto denominato **XamarinFCM** viene creato:

    [![Creare una finestra di dialogo del progetto](firebase-cloud-messaging-images/06-create-a-project-sml.png)](firebase-cloud-messaging-images/06-create-a-project.png#lightbox)

3.  Nella Console di Firebase **Panoramica**, fare clic su **Firebase Aggiungi all'App Android**:

    [![Aggiungere Firebase all'app Android](firebase-cloud-messaging-images/07-add-firebase-sml.png)](firebase-cloud-messaging-images/07-add-firebase.png#lightbox)

4.  Nella schermata successiva, immettere il nome del pacchetto dell'app. In questo esempio, il nome del pacchetto è **com.xamarin.fcmexample**. Questo valore deve corrispondere al nome di pacchetto dell'app Android. Un nome alternativo di app può anche essere immesso nel **soprannome App** campo:

    [![Immissione di esempio FCM come nome alternativo app](firebase-cloud-messaging-images/08-package-name-sml.png)](firebase-cloud-messaging-images/08-package-name.png#lightbox)

5.  Se l'app Usa collegamenti dinamici, inviti o Google Auth, è necessario immettere anche il certificato di firma di debug. Per ulteriori informazioni sull'individuazione del certificato di firma, vedere [ricerca MD5 o SHA1 firma il Keystore](~/android/deploy-test/signing/keystore-signature.md).
    In questo esempio, il certificato di firma viene lasciato vuoto.

6.  Fare clic su **Aggiungi APP**:

    [![Fare clic sul pulsante Aggiungi App](firebase-cloud-messaging-images/09-add-app-sml.png)](firebase-cloud-messaging-images/09-add-app.png#lightbox)

    Una chiave API del Server e un ID Client vengono generati automaticamente per l'app. Queste informazioni sono compresse un **google services.json** file che viene scaricato automaticamente quando si fa clic **Aggiungi APP**.
    Assicurarsi di salvare il file in un luogo sicuro.

Per un esempio dettagliato di come aggiungere **google services.json** a un progetto di app per ricevere i messaggi di notifica push FCM in Android, vedere [remoto notifiche con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).



## <a name="for-further-reading"></a>Ulteriori informazioni

-   Google [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) fornisce una panoramica delle funzionalità chiave del Firebase Cloud Messaging, una descrizione del funzionamento e le istruzioni di installazione.

-   Google [le richieste di invio Server compilare App](https://firebase.google.com/docs/cloud-messaging/send-message) viene illustrato come inviare messaggi con il server di app.

-   [6120 RFC](https://tools.ietf.org/html/rfc6120) e [6121 RFC](https://tools.ietf.org/html/rfc6121) spiegare e definire la messaggistica estendibile e protocollo presenza (XMPP).



## <a name="summary"></a>Riepilogo

In questo articolo viene fornita una panoramica di messaggistica Cloud Firebase (FCM). È stato spiegato varie credenziali che consentono di identificare e autorizzare tra i server applicazioni e le applicazioni client di messaggistica. Illustrata la registrazione e scenari di messaggistica a valle e sono stati descritti i passaggi per registrare l'app con FCM per utilizzare i servizi FCM.


## <a name="related-links"></a>Collegamenti correlati

- [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/)
