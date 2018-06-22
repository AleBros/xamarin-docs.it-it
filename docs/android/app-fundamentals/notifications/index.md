---
title: Notifiche di xamarin
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: d321c3eb3fe1c882ef9cea6ed1aace3aa1dd953e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762363"
---
# <a name="notifications-in-xamarinandroid"></a>Notifiche di xamarin


## <a name="overview"></a>Panoramica

In questa sezione viene illustrato come implementare le notifiche in xamarin. Vengono descritti i vari elementi dell'interfaccia utente di una notifica di Android e descrive l'API del coinvolto nella creazione e visualizzazione di una notifica.


## <a name="sections"></a>Sezioni

### <a name="local-notifications-in-androidlocal-notificationsmd"></a>[Notifiche locale In Android](local-notifications.md)

In questa sezione viene illustrato come implementare le notifiche locale in xamarin. Vengono descritti i vari elementi dell'interfaccia utente di una notifica di Android e illustrano l'API del coinvolto nella creazione e visualizzazione di una notifica. 

### <a name="walkthrough---using-local-notifications-in-xamarinandroidlocal-notifications-walkthroughmd"></a>[Procedura dettagliata: utilizzo delle notifiche locale in xamarin](local-notifications-walkthrough.md)  
 
Questa procedura dettagliata illustra come usare le notifiche di locale in un'applicazione di xamarin. Vengono illustrate le nozioni di base di creazione e pubblicazione di una notifica. Quando l'utente fa clic sulla notifica nel cassetto notifica all'avvio una seconda attività. 


## <a name="for-further-reading"></a>Ulteriori informazioni

[Messaggistica Cloud firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) &ndash; Firebase Cloud Messaging (FCM) è un servizio che facilita la messaggistica tra le applicazioni per dispositivi mobili e server. Messaggistica Cloud firebase utilizzabile per implementare le notifiche remote (detto anche le notifiche push) nelle applicazioni di xamarin.

[Le notifiche](http://developer.android.com/guide/topics/ui/notifiers/notifications.html) &ndash; argomento Android Developer questa è la Guida completa per le notifiche Android. Include una progettazione sezione considerazioni che consentono di progettare le notifiche in modo che siano conformi alle linee guida dell'interfaccia utente di Android. Fornisce informazioni complementari sulla navigazione preserviing quando si avvia un'attività e viene descritto come visualizzare lo stato di avanzamento in una notifica e controllarne la riproduzione di supporto nella schermata di blocco. 

[NotificationListenerService](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/) &ndash; Android questo servizio rende possibile per l'app di ricevere (e interagire con) registrato tutte le notifiche nel dispositivo Android, non solo le notifiche dell'app registrata in ricezione. Si noti che l'utente dovrà fornire in modo esplicito l'autorizzazione per l'app per poter essere in grado di ascoltare le notifiche nel dispositivo.





## <a name="related-links"></a>Collegamenti correlati

- [Notifiche locale (esempio)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Notifiche remote (esempio)](https://developer.xamarin.com/samples/monodroid/RemoteNotifications/)
