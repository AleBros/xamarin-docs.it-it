---
title: Notifiche in Novell. Android
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 7d153491802a74ff06b3a5cd63e585b7fd18ff03
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644323"
---
# <a name="notifications-in-xamarinandroid"></a>Notifiche in Novell. Android

Questa sezione illustra come implementare le notifiche in Novell. Android. Vengono descritti i vari elementi dell'interfaccia utente di una notifica Android e viene illustrata la creazione e la visualizzazione di una notifica da parte dell'API.

## <a name="local-notifications-in-androidlocal-notificationsmd"></a>[Notifiche locali in Android](local-notifications.md)

Questa sezione illustra come implementare le notifiche locali in Novell. Android. Vengono descritti i vari elementi dell'interfaccia utente di una notifica Android e vengono illustrate le API necessarie per la creazione e la visualizzazione di una notifica.

## <a name="walkthrough---using-local-notifications-in-xamarinandroidlocal-notifications-walkthroughmd"></a>[Procedura dettagliata: uso delle notifiche locali in Novell. Android](local-notifications-walkthrough.md)  
 
Questa procedura dettagliata illustra come usare le notifiche locali in un'applicazione Novell. Android. Vengono illustrati i concetti di base per la creazione e la pubblicazione di una notifica. Quando l'utente fa clic sulla notifica nel cassetto delle notifiche, avvia una seconda attività. 

## <a name="further-reading"></a>Ulteriori informazioni

[Messaggistica cloud Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) &ndash; Firebase Cloud Messaging (FCM) è un servizio che facilita la messaggistica tra le app per dispositivi mobili e le applicazioni server. La messaggistica cloud Firebase può essere usata per implementare notifiche remote (dette anche notifiche push) nelle applicazioni Novell. Android.

[Notifiche](https://developer.android.com/guide/topics/ui/notifiers/notifications.html) di &ndash; Questo argomento per sviluppatori Android è la guida definitiva per le notifiche Android. Include una sezione relativa alle considerazioni sulla progettazione che consente di progettare le notifiche in modo che siano conformi alle linee guida dell'interfaccia utente di Android. Vengono fornite ulteriori informazioni di base sulla navigazione preliminare all'avvio di un'attività e viene illustrato come visualizzare lo stato di avanzamento in una notifica e controllare la riproduzione dei supporti nella schermata di blocco.

[NotificationListenerService](xref:Android.Service.Notification.NotificationListenerService) &ndash; Questo servizio Android consente all'app di restare in ascolto e interagire con tutte le notifiche pubblicate sul dispositivo Android, non solo le notifiche che l'app è registrata per la ricezione.
Si noti che l'utente deve concedere in modo esplicito l'autorizzazione all'app affinché possa restare in ascolto delle notifiche sul dispositivo.

## <a name="related-links"></a>Collegamenti correlati

- [Notifiche locali (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)
- [Notifiche remote (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/remotenotifications)
