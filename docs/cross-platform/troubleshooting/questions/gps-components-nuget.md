---
title: Componenti e NuGet, unificazione Google Play Services
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: e0ba5ee9417917b834ab060a94f72d1f071b4912
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="unifying-google-play-services-components-and-nuget"></a>Componenti e NuGet, unificazione Google Play Services

### <a name="history"></a>Cronologia

Sono utilizzati da diversi componenti di Google Play Services e pacchetti NuGet:

-   Servizi Google Play (Froyo)
-   Servizi Google Play (marzapane)
-   Google Play Services (ICS)
-   Servizi Google Play (JellyBean)
-   Servizi Google Play (KitKat)

File JAR navi due effettivamente solo Google per Google Play Services:

-   `google-play-services-froyo.jar`
-   `google-play-services.jar`

La discrepanza esistenti perché il nostro strumenti correttamente diceva `aapt.exe` Qual era la risorsa di massima livello API da utilizzare per un'applicazione specificata. In questo modo, è pervenuta errori di compilazione se è tentato di utilizzare l'associazione di Google Play Services (KitKat) in un livello inferiore di API come pupazzi.

### <a name="unifying-google-play-services"></a>Unificazione Google Play Services

Nelle versioni più recenti di xamarin, è ora possibile indicare `aapt.exe` versione massima di risorse, in modo da utilizzare questo problema si risolve automaticamente.

In questo modo, non è reale di disporre di pacchetti separati per pupazzi/Condivisione connessione Internet/JellyBean/KitKat (è comunque necessario tuttavia un binding distinto per Froyo perché è un file JAR diversi completamente).

Per semplificare le operazioni per gli sviluppatori, è stato ora i componenti e unificati NuGet pacchetti in due parti:

-   Google Play Services (Froyo) (associa `google-play-services-froyo.jar`)
-   Google Play Services (associa `google-play-services.jar`)

### <a name="which-one-should-be-used"></a>Quale deve essere utilizzato?

In quasi tutti i casi, Google Play Services deve essere utilizzato. L'unico motivo per utilizzare il pacchetto (Froyo) è se la destinazione attivamente Froyo. L'unico motivo per cui che esiste il file JAR separato da Google poiché Froyo si trova su una piccola percentuale di dispositivi, vengono anch'essi è deciso di terminare il supporto, pertanto il file JAR è uno snapshot bloccato, non supportato di Google Play Services.

### <a name="note-about-gingerbread"></a>Nota sul Pupazzi

Pupazzi non ha il frammento di supporto per impostazione predefinita e per questo motivo, alcune classi di associazione non sarà utilizzabile in un'applicazione in fase di esecuzione in un dispositivo pupazzi. Le classi come `MapFragment` non funziona in pupazzi e utilizzare invece la variante di supporto `SupportMapFragment`. È attivo agli sviluppatori di sapere quale utilizzare. Questa incompatibilità è indicata da Google nella relativa documentazione di Google Play Services.

### <a name="what-happens-to-the-old-componentsnugets"></a>Cosa accade per il vecchio componenti/NuGet?

Poiché non sono più necessari, è necessario disattivato/Delisted i seguenti componenti/NuGets:

-   Servizi Google Play (marzapane)
-   Servizi Google Play (JellyBean)
-   Servizi Google Play (KitKat)

Esistente _Google Play Services (ICS)_ componente/Nuget è stato rinominato in _Google Play Services_ e verrà mantenuto aggiornato in futuro. Tutti i progetti che fanno riferimento a uno dei pacchetti disattivato/Delisted devono essere aggiornati per l'utilizzo di questa.

Componenti disabilitati ancora esistano e devono essere possibile ripristinare per i progetti che fanno ancora riferimento, per evitare di interrompere le. Allo stesso modo dei pacchetti NuGet delisted ancora presenti ed è possono ripristinare. Essi non verranno aggiornati in futuro.
