---
title: Servizi Google Licensing
ms.prod: xamarin
ms.assetid: E96BDCC3-454A-A797-5819-905E2BB1AC41
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 12/20/2017
ms.openlocfilehash: 934cc5124c71f631def0d534420cf75a12ee4079
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70753591"
---
# <a name="google-licensing-services"></a>Servizi Google Licensing

Prima di Google Play, le applicazioni Android si basavano sulla funzionalità legacy di protezione contro la copia offerta da Google Market per verificare che solo gli utenti autorizzati potessero eseguire le applicazioni nei propri dispositivi. A causa delle sue limitazioni, il meccanismo di protezione contro la copia era una soluzione tutt'altro che ideale per la protezione delle applicazioni.

Google Licensing sostituisce questo meccanismo legacy di protezione contro la copia.
Google Licensing è un servizio flessibile, sicuro e basato sulla rete nel quale le applicazioni Android possono eseguire query per determinare se un'applicazione dispone della licenza necessaria per essere eseguita in un determinato dispositivo.

Google Licensing è flessibile nel senso che le applicazioni Android hanno il pieno controllo sul momento in cui verificare la licenza, sulla frequenza della verifica e su come gestire la risposta del server licenze.

Google Licensing è sicuro nel senso che ogni risposta viene firmata usando una coppia di chiavi RSA condivisa esclusivamente tra il server di Google Play e l'applicazione. Google Play fornisce una chiave pubblica per gli sviluppatori che è incorporata all'interno dell'applicazione Android e viene usata per autenticare le risposte. Il server di Google Play conserva internamente la chiave privata.

Un'applicazione che ha implementato Google Licensing invia una richiesta a un servizio ospitato dall'applicazione Google Play nel dispositivo. Google Play invia quindi la richiesta al server di Google Licensing che risponde con lo stato della licenza: 

[![Diagramma del flusso di lavoro del server licenze](google-licensing-services-images/gp-licensing-service-overview.png)](google-licensing-services-images/gp-licensing-service-overview.png#lightbox)

Il diagramma precedente illustra questo flusso di lavoro: 

- L'applicazione fornisce il nome del pacchetto, un *nonce*, ovvero un autenticatore crittografico che viene usato per convalidare la risposta del server, nonché un callback in grado di gestire la risposta in modo asincrono. 

- Google Play fornisce informazioni quali l'account Google e il dispositivo stesso, ad esempio il numero IMSI. 

Il servizio Google Licensing è anche un componente chiave dei file di espansione APK, descritti più avanti in questo documento. I file di espansione APK usano i servizi Google Licensing per ottenere gli URL dei file di espansione che verranno scaricati.

## <a name="requirements"></a>Requisiti

Le applicazioni che non vengono acquistate tramite Google Play non ricevono alcun vantaggio dai servizi Google Licensing. Se Google Play non è installato in un dispositivo, le applicazioni che usano i servizi di licenza funzioneranno comunque normalmente nel dispositivo.

Per la funzionalità, Google Play richiede l'accesso a Internet. L'applicazione può memorizzare la licenza nella cache per supportare scenari in cui il dispositivo non abbia accesso ai server di Google Play Licensing.

Le applicazioni gratuite richiedono Google Licensing solo nel caso in cui usino file di espansione APK.
