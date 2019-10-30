---
title: Messaggistica di Google
description: Questa sezione contiene le guide che descrivono come implementare app Novell. Android usando i servizi di messaggistica Google.
ms.prod: xamarin
ms.assetid: 85E8DF92-D160-4763-A7D3-458B4C31635F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/12/2018
ms.openlocfilehash: e0d257cd5d443c77fd0a8132d9cd31ec572182aa
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023537"
---
# <a name="google-messaging"></a>Messaggistica di Google

_Questa sezione contiene le guide che descrivono come implementare app Novell. Android usando i servizi di messaggistica Google._

## <a name="firebase-cloud-messagingfirebase-cloud-messagingmd"></a>[Firebase Cloud Messaging](firebase-cloud-messaging.md)

Firebase Cloud Messaging (FCM) è un servizio che facilita la messaggistica tra le app per dispositivi mobili e le applicazioni server. FCM è il successore di Google per Google Cloud Messaging. Questo articolo fornisce una panoramica del funzionamento di FCM e fornisce una procedura dettagliata per l'acquisizione delle credenziali in modo che l'app possa usare i servizi FCM.

## <a name="remote-notifications-with-firebase-cloud-messagingremote-notifications-with-fcmmd"></a>[Notifiche remote con Firebase Cloud Messaging](remote-notifications-with-fcm.md)

Questa procedura dettagliata fornisce una spiegazione dettagliata dell'uso di Firebase Cloud Messaging per implementare notifiche remote (dette anche notifiche push) in un'applicazione Novell. Android. Viene illustrato come implementare le varie classi necessarie per le comunicazioni con Firebase Cloud Messaging (FCM), vengono forniti esempi di come configurare il manifesto Android per l'accesso a FCM e viene illustrata la messaggistica downstream mediante Firebase Console.

## <a name="google-cloud-messaginggoogle-cloud-messagingmd"></a>[Google Cloud Messaging](google-cloud-messaging.md)

Questa sezione fornisce una panoramica di alto livello del modo in cui Google Cloud Messaging (GCM) instrada i messaggi tra l'app e un server app e fornisce una procedura dettagliata per l'acquisizione delle credenziali in modo che l'app possa usare i servizi GCM. Si noti che GCM è stato sostituito da FCM.

> [!NOTE]
> GCM è stato sostituito da [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> Le API client e server GCM [sono state deprecate](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) e non saranno più disponibili non appena l'11 aprile 2019.

## <a name="remote-notifications-with-google-cloud-messagingremote-notifications-with-gcmmd"></a>[Notifiche remote con Google Cloud Messaging](remote-notifications-with-gcm.md)

Questa sezione fornisce una spiegazione dettagliata dell'implementazione di notifiche remote in Novell. Android con Google Cloud Messaging.
Vengono illustrati i vari componenti che è necessario sfruttare per abilitare Google Cloud Messaging in un'applicazione Android.
