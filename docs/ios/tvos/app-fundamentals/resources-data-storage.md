---
title: tvOS risorse e archiviazione dei dati in Xamarin
description: Questo articolo descrive come usare risorse e l'archiviazione persistente dei dati in un'app tvOS compilata con Xamarin. Vengono illustrate le risorse di archiviazione e on demand dati iCloud.
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 8f8ecc115738fb97f71b4ea6b2cdcc5c2714372d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108184"
---
# <a name="tvos-resources-and-data-storage-in-xamarin"></a>tvOS risorse e archiviazione dei dati in Xamarin

_Questo articolo descrive l'uso delle risorse e archiviazione persistente dei dati in un'app xamarin. tvos._

<a name="tvOS-Resource-Limitations" />

## <a name="tvos-resource-limitations"></a>tvOS limitazioni delle risorse

A differenza dei dispositivi iOS, il nuovo televisore Apple fornisce estremamente limitata un'archiviazione persistente e locale per le app tvOS o dati. Per gli elementi molto piccoli (ad esempio le preferenze dell'utente), l'app tvOS ha ancora accesso ai `NSUserDefaults` con un [limite di 500 KB di dati](https://forums.developer.apple.com/message/50696#50696). Tuttavia, se l'app xamarin. tvos deve memorizzare maggiori quantità di informazioni, deve archiviare e recuperare i dati dal [iCloud](#iCloud-Data-Storage).

Inoltre, tvOS limita le dimensioni di un'app per Apple TV fino a 200MB. Se l'app richiede risorse oltre queste dimensioni, dovrà essere incluso nel pacchetto e caricati usando [risorse On Demand](#On-Demand-Resources) (fino a 2 GB aggiuntivi). Considerate queste limitazioni, è fondamentale correttamente ora il download delle risorse aggiuntive per offrire la migliore esperienza per gli utenti dell'app. Per altre informazioni, vedere di Apple [Guida per le risorse On Demand](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083).

<a name="Non-Persistent-Downloads" />

## <a name="non-persistent-downloads"></a>Download non permanente

Ogni app tvOS è disponibile una directory della cache temporanea che le risorse aggiuntive e gli asset vengono scaricati. Questa directory verrà mantenuta fino a quando l'app è ancora in esecuzione. Tuttavia, quando sono necessari per Apple TV liberare spazio ad altre App o l'utilizzo del sistema, questa cache può essere eliminata.

Di conseguenza, l'app non è possibile affidarsi contenuto scaricato in precedenza è sempre disponibile al successivo avvio. L'app xamarin. tvos sempre deve verificare l'esistenza delle risorse necessarie e scaricarli in base alle esigenze.

> [!IMPORTANT]
> Anche se si hanno la possibilità di scaricare altri asset e risorse in base alle esigenze, Apple fornisce un avviso su utilizzato tutto lo spazio nella cache dell'app, ma questa operazione può causare risultati imprevedibili.




<a name="Managing-Resources" />

## <a name="managing-resources"></a>Gestione di risorse

Come indicato in precedenza, poichè, archiviazione non permanente delle informazioni disponibili per le app tvOS, queste restrizioni richiedono un'attenta pianificazione creare un'esperienza utente eccezionale per l'app xamarin. tvos.

<a name="iCloud-Data-Storage" />

### <a name="icloud-data-storage"></a>Archiviazione dati iCloud

Poiché l'archiviazione sull'Apple TV è limitato, non solo è sono molto limitati un'archiviazione persistente e locale, l'app non dispone di alcuna garanzia che qualsiasi informazione scaricato in precedenza saranno disponibili alla successiva esecuzione.

Di conseguenza, l'app xamarin. tvos necessario archiviare tutti i dati utente in un Data Store iCloud. Apple offre due opzioni di archiviazione di dati basati su iCloud per le app tvOS:

- **Archiviazione chiave-valore (KVS) iCloud** - per piccole quantità di informazioni (inferiore a 1 MB) che l'app può richiedere (ad esempio, le preferenze dell'utente), è possibile usare KVS archiviazione iCloud. iCloud KVS dati vengono sincronizzati automaticamente con il cloud e tutti i dispositivi dell'utente che esegue l'app stessa. Per altre informazioni, vedere la [archiviazione chiave-valore](~/ios/data-cloud/introduction-to-icloud.md) sezione del nostro [Introduzione a iCloud](~/ios/data-cloud/introduction-to-icloud.md) documento o Apple [progettazione per i dati chiave-valore in iCloud](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7) documentazione.
- **CloudKit** : per l'archiviazione delle più grandi porzioni di informazioni (superiore a 1 MB), usare il Framework di CloudKit Apple. A differenza di archiviazione KVS iCloud, CloudKit dati possono essere condivisi tra tutti gli utenti dell'app (così come da privato a un singolo utente). Per ulteriori informazioni, vedere la [Introduzione a CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) documentazione o Apple [CloudKit Quick Start](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987).

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

<a name="On-Demand-Resources" />

### <a name="on-demand-resources"></a>Risorse su richiesta

Risorse on Demand offrono app contenuto e asset (separato dal bundle dell'app), che sono ospitati in Store l'App e scaricati come richiesto dall'app. Fino a 2 GB aggiuntivi di dati possono essere create mediante le risorse On Demand. Consentono il download iniziale dell'app siano di dimensioni ridotte (app tvOS sono limitate a un massimo di 200MB), garantendo comunque asset avanzate in base alle esigenze.

Quando un'app tvOS richiede risorse su richiesta, il sistema gestirà automaticamente il download e l'archiviazione del contenuto alla directory della cache dell'app. L'app deve attendere per questo contenuto devono essere scaricati e resi disponibili prima di poter continuare.

Queste risorse possono continuare a essere memorizzati nella cache in Apple TV in tutto più avvii dell'app, in questo modo accelerare del ciclo di avvio. Tuttavia, l'app non è possibile si basano su qualsiasi contenuto scaricato in precedenza è sempre disponibile al successivo avvio. Vedere le [Scarica Non-Persistent](#Non-Persistent-Downloads) sezione precedente per altri dettagli.

Si usa Xcode per creare aggregazioni di contenuto correlato (ad esempio, tutti gli asset di gioco livello 2) associate a un Tag delle risorse di assegnare. In un secondo momento l'app richiederà risorse su richiesta, specificando il Tag delle risorse. L'app deve presentare un'interfaccia utente per l'utente che informa che il contenuto viene scaricato. Per altre informazioni, vedere di Apple [Guida per le risorse On Demand](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083).

> [!IMPORTANT]
> Dovrebbe prestare attenzione a raggiungere il giusto equilibrio tra il numero di volte in cui che l'app deve scaricare risorse su richiesta e le dimensioni dei singoli download. Utente potrebbe diventare frustrato con l'app in caso di interruzione costantemente la modalità di gioco per scaricare il contenuto nuovo o se un singolo download richiede tempi eccessivamente lunghi.




<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato le limitazioni di archiviazione dimensioni, risorse e i dati inserite in un'app xamarin. tvos dal sistema tvOS. Ha presentato le opzioni per risolvere queste limitazioni e i suggerimenti per creare un'esperienza utente eccezionale per l'app.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
