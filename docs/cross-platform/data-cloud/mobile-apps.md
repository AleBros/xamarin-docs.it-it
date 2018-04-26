---
title: App per dispositivi mobili di Microsoft Azure
description: Download di esempi e codice per la documentazione del portale di Azure.
ms.prod: xamarin
ms.assetid: 7B9AA8D9-C181-4C33-8AB0-2F56E4DBFC03
ms.technology: xamarin-cross-platform
author: conceptdev
ms.author: crdun
ms.date: 04/02/2017
ms.openlocfilehash: 4a6211ac218d914089bf1d0dbcb9956800dde700
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2018
---
# <a name="microsoft-azure-mobile-apps"></a>App per dispositivi mobili di Microsoft Azure

_Download di esempi e codice per la documentazione del portale di Azure._

<!--
NOTE TO AUTHORS: this page is referenced from
http://azure.microsoft.com/develop/mobile/xamarin/
as https://developer.xamarin.com/guides/cross-platform/data-cloud/mobile-services/
A redirect has been put in place to /mobile-apps/ HOWEVER the /Resources/ .ZIP files are still located in /mobile-services/ so that the following permalinks don't break

The ZIPs in /Resources/ are also referenced by inbound links
Getting Started  http://go.microsoft.com/fwlink/p/?LinkId=331359
Get started with data   http://go.microsoft.com/fwlink/p/?LinkId=331302
Get started with push   http://go.microsoft.com/fwlink/p/?LinkId=331303
Get started with authentication http://go.microsoft.com/fwlink/p/?LinkId=331328
Get started with Notification Hubs  http://go.microsoft.com/fwlink/p/?LinkId=331329
Validate and modify data    http://go.microsoft.com/fwlink/p/?LinkId=331330
-->


Questi collegamenti sono per la documentazione di Xamarin disponibile il [App mobili di Azure](https://docs.microsoft.com/azure/app-service-mobile/) sito Web.
Aggiunta di funzionalità di Azure a un'app Xamarin scaricando il [Client mobili di Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).

## <a name="working-with-the-xamarin-azure-component"></a>Utilizzo del componente di Azure di Xamarin

Documentazione generale [utilizzo della libreria Client di Xamarin (componente) di](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library) eseguire varie attività con App mobili di Azure. Questa pagina contiene un numero elevato di frammenti di codice di esempio, senza le spiegazioni dettagliate e gli esempi disponibili in ciascuno degli articoli di questa procedura dettagliata elencati di seguito.

## <a name="getting-started"></a>Introduzione

In questo articolo vengono fornite istruzioni dettagliate per ottenere la prima app Xamarin Azure attivo e in esecuzione.
Vengono illustrate la creazione di una nuova App Mobile di Azure nel portale di download e l'esecuzione dell'app configurati in precedenza.

-  [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started/)
-  [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started/)
-  [Xamarin.Forms](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started)

<!--
## Validate, Modify and Augment Data in Scripts

Demonstrates how to add server-side scripts to Azure Mobile Services data tables to implement server-side validation and other functionality.

-  [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-  [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-->

<!--
## Add Paging to Data

A quick example of paging large sets of data using Skip() and Take().

-  [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-  [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-->

## <a name="get-started-with-users"></a>Introduzione agli utenti

Fornisce istruzioni dettagliate per la configurazione e la codifica di una schermata di accesso tramite servizi mobili di Azure. Provider di autenticazione supportati includono Microsoft, Google, Facebook e Twitter.

-  [iOS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-ios-get-started-users/)
-  [Android](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-android-get-started-users/)


## <a name="authorize-users-in-scripts"></a>Autorizzare gli utenti negli script

Alcuni esempi di codice di back-end Javascript

-  [TODO.js](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/personal-table/tables/TodoItem.js#L38)


## <a name="get-started-with-push"></a>Introduzione a Push

Completare le istruzioni per configurare le notifiche push nei siti Web di Apple e Google, quindi inviare una notifica push da servizi mobili di Azure a un dispositivo.

-  [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started-push)
-  [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started-push)


## <a name="get-started-with-notification-hubs"></a>Introduzione agli hub di notifica

Le istruzioni su come configurare le notifiche push nei siti Web di Apple e Google, configurare l'Hub di notifica di Azure e quindi generare le notifiche push ai dispositivi.

-  [iOS](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)
-  [Android](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)



## <a name="related-links"></a>Collegamenti correlati

- [GettingStarted (esempio)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GettingStarted)
- [GetStartedWithData (esempio)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithData)
- [GetStartedWithUsers (esempio)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithUsers)
- [GetStartedWithPush (esempio)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithPush)
- [Hub di notifica (esempio)](https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs)
- [Client per dispositivi mobili di Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Percorso di apprendimento Azure di App per dispositivi mobili](https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/)

<!--
- [ValidateModifyData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/ValidateModifyData)
-->