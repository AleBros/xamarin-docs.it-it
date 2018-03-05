---
title: App per dispositivi mobili di Microsoft Azure
description: Download di esempi e codice per la documentazione del portale di Azure.
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B9AA8D9-C181-4C33-8AB0-2F56E4DBFC03
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: fb3c26b7d090ca42328c61192c794dec1544d1d3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="microsoft-azure-mobile-apps"></a>App per dispositivi mobili di Microsoft Azure

_Download di esempi e codice per la documentazione del portale di Azure._

<!--
NOTE TO AUTHORS: this page is referenced from
http://azure.microsoft.com/en-us/develop/mobile/xamarin/
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


Questi collegamenti sono per la documentazione di Xamarin disponibile il [App mobili di Azure](https://azure.microsoft.com/en-us/documentation/services/app-service/mobile/) sito Web.
Aggiunta di funzionalità di Azure a un Xamarin app è semplice come download di [Client mobili di Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).

## <a name="working-with-the-xamarin-azure-component"></a>Utilizzo del componente di Azure di Xamarin

Documentazione generale [utilizzo della libreria Client di Xamarin (componente) di](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-dotnet-how-to-use-client-library/) eseguire varie attività con App mobili di Azure. Questa pagina contiene un numero elevato di frammenti di codice di esempio, senza le spiegazioni dettagliate e gli esempi disponibili in ciascuno degli articoli di questa procedura dettagliata elencati di seguito.

## <a name="getting-started"></a>Introduzione

In questo articolo vengono fornite istruzioni dettagliate per ottenere la prima app Xamarin Azure attivo e in esecuzione.
Vengono illustrate la creazione di una nuova App Mobile di Azure nel portale di download e l'esecuzione dell'app configurati in precedenza.

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-ios-get-started/)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-android-get-started/)
-  [Xamarin.Forms](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-forms-get-started/)

## <a name="validate-modify-and-augment-data-in-scripts"></a>Convalidare, modificare e migliorare i dati negli script

Viene illustrato come aggiungere script sul lato server alle tabelle di dati di servizi mobili di Azure per implementare la convalida sul lato server e altre funzionalità.

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)


## <a name="add-paging-to-data"></a>Aggiungere il Paging ai dati

Un esempio semplice di paging di grandi set di dati utilizzando Skip() e Take().

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)


## <a name="get-started-with-users"></a>Introduzione agli utenti

Fornisce istruzioni dettagliate per la configurazione e la codifica di una schermata di accesso tramite servizi mobili di Azure. Provider di autenticazione supportati includono Microsoft, Google, Facebook e Twitter.

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-ios-get-started-users/)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-android-get-started-users/)


## <a name="authorize-users-in-scripts"></a>Autorizzare gli utenti negli script

Alcuni esempi di codice di back-end Javascript

-  [Todo.js](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/personal-table/tables/TodoItem.js#L38)


## <a name="get-started-with-push"></a>Introduzione a Push

Completare le istruzioni per configurare le notifiche push nei siti Web di Apple e Google, quindi inviare una notifica push da servizi mobili di Azure a un dispositivo.

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-ios-get-started-push/)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-android-get-started-push/)


## <a name="get-started-with-notification-hubs"></a>Introduzione agli hub di notifica

Le istruzioni su come configurare le notifiche push nei siti Web di Apple e Google, configurare l'Hub di notifica di Azure e quindi generare le notifiche push ai dispositivi.

-  [iOS](http://azure.microsoft.com/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/)
-  [Android](http://azure.microsoft.com/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/)



## <a name="related-links"></a>Collegamenti correlati

- [GettingStarted (esempio)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GettingStarted)
- [GetStartedWithData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithData)
- [GetStartedWithUsers (esempio)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithUsers)
- [GetStartedWithPush (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithPush)
- [ValidateModifyData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/ValidateModifyData)
- [Hub di notifica (esempio)](https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs)
- [Esempio di libreria di classi Portabile Azure (da @paulbatum) (esempio)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Client per dispositivi mobili di Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Percorso di apprendimento Azure di App per dispositivi mobili](https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/)
