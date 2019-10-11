---
title: App per dispositivi mobili Microsoft Azure
description: Questo documento contiene collegamenti a guide che descrivono come creare un'app Novell connessa ad Azure. Viene illustrato come usare il componente, gli utenti e le notifiche push di Novell di Azure.
ms.prod: xamarin
ms.assetid: 7B9AA8D9-C181-4C33-8AB0-2F56E4DBFC03
author: conceptdev
ms.author: crdun
ms.date: 04/02/2017
ms.openlocfilehash: 7d39615f20cdd52bb862d9ee998e9a45bbb6834e
ms.sourcegitcommit: d116fdcad9f5f0587ce64749cd8d791eee87f8aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246533"
---
# <a name="microsoft-azure-mobile-apps"></a>App per dispositivi mobili Microsoft Azure

> [!NOTE]
> Visual Studio App Center supporta end-to-end e servizi integrati centrali per lo sviluppo di app per dispositivi mobili. Gli sviluppatori possono utilizzare i servizi di **compilazione**, **test** e **distribuzione** per configurare una pipeline di integrazione e recapito continua. Una volta distribuita l'app, gli sviluppatori possono monitorare lo stato e l'utilizzo dell'app usando i servizi di **analisi** e **diagnostica** e coinvolgere gli utenti che usano il servizio di **push** . Gli sviluppatori possono inoltre sfruttare l' **autenticazione** per autenticare gli utenti e il servizio **dati** per salvare in modo permanente e sincronizzare i dati delle app nel cloud.
> Se si intende integrare servizi cloud nell'applicazione per dispositivi mobili, iscriversi con [App Center](https://appcenter.ms/signup?utm_source=XamarinDocs&utm_medium=Azure&utm_campaign=docs) oggi.

_Esempi e download di codice per la documentazione portale di Azure._

<!--
NOTE TO AUTHORS: this page is referenced from
https://azure.microsoft.com/develop/mobile/xamarin/
as https://developer xamarin com/guides/cross-platform/data-cloud/mobile-services/
A redirect has been put in place to /mobile-apps/ HOWEVER the /Resources/ .ZIP files are still located in /mobile-services/ so that the following permalinks don't break

The ZIPs in /Resources/ are also referenced by inbound links
Getting Started http://go.microsoft.com/fwlink/p/?LinkId=331359
Get started with data http://go.microsoft.com/fwlink/p/?LinkId=331302
Get started with push http://go.microsoft.com/fwlink/p/?LinkId=331303
Get started with authentication http://go.microsoft.com/fwlink/p/?LinkId=331328
Get started with Notification Hubs http://go.microsoft.com/fwlink/p/?LinkId=331329
Validate and modify data  http://go.microsoft.com/fwlink/p/?LinkId=331330
-->

Questi collegamenti sono per la documentazione di Novell disponibile nel sito Web [app per dispositivi mobili di Azure](https://docs.microsoft.com/azure/app-service-mobile/) .
Aggiunta della funzionalità di Azure a un'app Novell scaricando il [client per dispositivi mobili di Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).

## <a name="working-with-the-xamarin-azure-component"></a>Uso del componente Novell di Azure

Documentazione generale [sull'uso della libreria client Novell (componente)](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library) per eseguire diverse attività con le app per dispositivi mobili di Azure. Questa pagina contiene molti frammenti di codice di esempio, senza la spiegazione dettagliata ed esempi disponibili in ciascuno degli articoli di procedura dettagliata elencati di seguito.

## <a name="getting-started"></a>Introduzione

In questo articolo vengono fornite istruzioni dettagliate per l'esecuzione della prima app di Azure Novell.
Viene illustrata la creazione di una nuova app per dispositivi mobili di Azure nel portale e il download e l'esecuzione dell'app preconfigurata.

- [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started/)
- [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started/)
- [Xamarin.Forms](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started)

<!--
## Validate, Modify and Augment Data in Scripts

Demonstrates how to add server-side scripts to Azure Mobile Services data tables to implement server-side validation and other functionality.

- [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
- [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-->

<!--
## Add Paging to Data

A quick example of paging large sets of data using Skip() and Take().

- [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
- [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-->

## <a name="get-started-with-users"></a>Introduzione agli utenti

Vengono fornite istruzioni complete per la configurazione e la codifica di una schermata di accesso tramite servizi mobili di Azure. I provider di autenticazione supportati includono Microsoft, Google, Facebook e Twitter.

- [iOS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-ios-get-started-users/)
- [Android](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-android-get-started-users/)

## <a name="authorize-users-in-scripts"></a>Autorizzare gli utenti negli script

Codice di esempio per i backend JavaScript

- [Todo. js](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/personal-table/tables/TodoItem.js#L38)

## <a name="get-started-with-push"></a>Introduzione a push

Completare le istruzioni per configurare le notifiche push nei siti Web Apple e Google, quindi inviare una notifica push da servizi mobili di Azure a un dispositivo.

- [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started-push)
- [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started-push)

## <a name="get-started-with-notification-hubs"></a>Introduzione ad hub di notifica

Completare le istruzioni per configurare le notifiche push nei siti Web Apple e Google, configurare l'hub di notifica di Azure e quindi generare notifiche push ai dispositivi.

- [iOS](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)
- [Android](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)

## <a name="related-links"></a>Collegamenti correlati

- [GettingStarted (esempio)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GettingStarted)
- [GetStartedWithData (esempio)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithData)
- [GetStartedWithUsers (esempio)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithUsers)
- [GetStartedWithPush (esempio)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithPush)
- [NotificationHubs (esempio)](https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs)
- [Client mobile di Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Percorso di apprendimento per app per dispositivi mobili di Azure](https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/)

<!--
- [ValidateModifyData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/ValidateModifyData)
-->
