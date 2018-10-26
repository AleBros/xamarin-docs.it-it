---
title: Notifiche in xamarin. Android
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a0778de5dcc7e21ba011fe144be703b3b83b8247
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114041"
---
# <a name="notifications-in-xamarinandroid"></a>Notifiche in xamarin. Android


## <a name="overview"></a>Panoramica

Questa sezione viene illustrato come implementare le notifiche in xamarin. Android. Vengono descritti i vari elementi dell'interfaccia utente di una notifica di Android e descrive l'API di coinvolti con la creazione e visualizzazione di una notifica.


## <a name="sections"></a>Sezioni

### <a name="local-notifications-in-androidlocal-notificationsmd"></a>[Notifiche locali In Android](local-notifications.md)

Questa sezione viene illustrato come implementare le notifiche locali in xamarin. Android. Vengono descritti i vari elementi dell'interfaccia utente di una notifica di Android e illustrano l'API di coinvolti con la creazione e visualizzazione di una notifica. 

### <a name="walkthrough---using-local-notifications-in-xamarinandroidlocal-notifications-walkthroughmd"></a>[Procedura dettagliata - uso delle notifiche locali in xamarin. Android](local-notifications-walkthrough.md)  
 
Questa procedura dettagliata illustra come usare le notifiche locali in un'applicazione xamarin. Android. Vengono illustrate le nozioni di base di creazione e pubblicazione di una notifica. Quando l'utente fa clic sulla notifica nel menu informazioni di notifica avvia una seconda attività. 


## <a name="for-further-reading"></a>Ulteriori informazioni

[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) &ndash; Firebase Cloud Messaging (FCM) è un servizio che semplifica la messaggistica tra App per dispositivi mobili e applicazioni server. Firebase Cloud Messaging è utilizzabile per implementare le notifiche remote (detta anche le notifiche push) nelle applicazioni xamarin. Android.

[Le notifiche](http://developer.android.com/guide/topics/ui/notifiers/notifications.html) &ndash; per sviluppatori Android questo argomento è la Guida definitiva per le notifiche di Android. Include una progettazione sezione considerazioni che consente di progettare le notifiche in modo che siano conformi alle linee guida dell'interfaccia utente di Android. Fornisce informazioni complementari sulla navigazione preserviing quando si avvia un'attività e viene descritto come visualizzare lo stato di avanzamento della riproduzione multimediale una notifica e il controllo nella schermata di blocco. 

[NotificationListenerService](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/) &ndash; Android questo servizio rende possibile all'app di ascolto (e interagire con) registrate tutte le notifiche sul dispositivo Android, non solo le notifiche all'app registrata in ricezione. Si noti che l'utente in modo esplicito debba concedere l'autorizzazione per l'app per poter essere in grado di restare in ascolto per le notifiche sul dispositivo.





## <a name="related-links"></a>Collegamenti correlati

- [Notifiche locali (esempio)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Notifiche remote (esempio)](https://developer.xamarin.com/samples/monodroid/RemoteNotifications/)
