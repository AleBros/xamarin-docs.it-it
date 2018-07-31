---
title: Tecniche di elaborazione in background in iOS
description: 'Questo documento i collegamenti alle guide che descrivono diverse tecniche backgrounding in iOS: attività in background, il servizio di trasferimento in background, recupero in background e le notifiche remote.'
ms.prod: xamarin
ms.assetid: 011A8D48-1CDC-486A-A2B0-C4946118E7A9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 33c4613e3698556b41a0d01acf2a4ac359ca5dd8
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350969"
---
# <a name="ios-backgrounding-techniques"></a>Tecniche di elaborazione in background in iOS

Nelle sezioni seguenti, esamineremo le seguenti funzionalità di iOS con le opzioni backgrounding esistente:

-  [Le attività in Background opportunistici](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background_tasks_in_iOS_7) -preservare la durata della batteria eseguendo le attività in background in base a blocchi opportunistici quando il dispositivo è attivo per altri tipi di elaborazione.
-  [Servizio di trasferimento in background](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background-transfers) : in modo affidabile caricare e scaricare i file, indipendentemente dalle dimensioni dello stato o un file di rete.
-  [Recupero di sfondo](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#background_fetch) -aggiornare un'applicazione da background a intervalli determinati dal sistema.
-  [Notifiche remote](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#remote_notifications) -notifiche push utilizzare per attivare gli aggiornamenti del contenuto in background prima che l'utente apre l'applicazione, con un'opzione per notificare all'utente o l'aggiornamento invisibile all'utente.
-  **Aggiornamenti dell'interfaccia utente di sfondo** : preparare l'applicazione dell'interfaccia utente per l'utente e aggiornare snapshot dell'applicazione, tutto dallo sfondo.
