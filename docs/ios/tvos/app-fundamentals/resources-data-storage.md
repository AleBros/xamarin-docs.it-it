---
title: Archiviazione dei dati e risorse
description: In questo articolo viene descritto l'utilizzo con le risorse e l'archiviazione dei dati permanenti in un'app Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 8103dcc1c2e1e8c2375a86d51580b1ea3fcce432
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="resources-and-data-storage"></a>Archiviazione dei dati e risorse

_In questo articolo viene descritto l'utilizzo con le risorse e l'archiviazione dei dati permanenti in un'app Xamarin.tvOS._

<a name="tvOS-Resource-Limitations" />

## <a name="tvos-resource-limitations"></a>tvOS limitazioni delle risorse

A differenza dei dispositivi iOS, il nuovo Apple TV fornisce un'archiviazione persistente e locale estremamente limitata per tvOS App o di dati. Per gli elementi molto piccoli (ad esempio le preferenze dell'utente), l'applicazione di tvOS ha ancora accesso a `NSUserDefaults` con un [limite di 500 KB di dati](https://forums.developer.apple.com/message/50696#50696). Tuttavia, se l'app Xamarin.tvOS deve mantenere grandi quantità di informazioni, deve archiviare e recuperare i dati da [iCloud](#iCloud-Data-Storage).

Inoltre, tvOS limita le dimensioni di un'app di Apple TV fino a 200MB. Se l'app richiede risorse oltre a queste dimensioni, dovrà essere incluso nel pacchetto e caricati utilizzando [risorse su richiesta](#On-Demand-Resources) (fino a 2 GB aggiuntiva). Dato queste limitazioni, è fondamentale correttamente ora il download delle risorse aggiuntive per offrire la migliore esperienza per gli utenti dell'applicazione. Per ulteriori informazioni, vedere Apple [Guida risorse su richiesta](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083).

<a name="Non-Persistent-Downloads" />

## <a name="non-persistent-downloads"></a>Download non persistenti

Ogni app tvOS viene fornita una directory cache temporanea che le risorse aggiuntive e gli asset vengono scaricati. Questa directory verrà mantenuta fino a quando l'app è ancora in esecuzione. Tuttavia, quando sono necessari liberare spazio per altri App o l'utilizzo del sistema per Apple TV, questa cache può essere eliminata.

Di conseguenza, l'app è possibile basarsi su contenuto scaricato in precedenza è disponibile al successivo avvio. L'app Xamarin.tvOS sempre deve verificare l'esistenza delle risorse necessarie e scaricarli in base alle esigenze.

> [!IMPORTANT]
> Mentre si è in grado di scaricare altri asset e le risorse come richiesto, Apple avvisa utilizzato tutto lo spazio nella cache dell'applicazione, quanto può causare risultati imprevisti.




<a name="Managing-Resources" />

## <a name="managing-resources"></a>Gestione di risorse

Come descritto in precedenza, a causa di limitato, nell'archivio non permanente delle informazioni disponibili nelle App tvOS, queste restrizioni richiedono un'attenta pianificazione creare un'esperienza utente ottimale per l'app Xamarin.tvOS.

<a name="iCloud-Data-Storage" />

### <a name="icloud-data-storage"></a>Archiviazione dei dati iCloud

Poiché l'archiviazione su Apple TV è limitato, non solo è molto limitato un'archiviazione persistente e locale, l'app non è alcuna garanzia che le informazioni che scaricato in precedenza saranno disponibili alla successiva esecuzione.

Di conseguenza, l'app Xamarin.tvOS necessario archiviare tutti i dati utente in un archivio dati iCloud. Apple fornisce due opzioni di archiviazione di dati basati su iCloud per le app tvOS:

- **Archivio chiave-valore (KVS) iCloud** : per piccole quantità di informazioni (minore di 1 MB) che l'app potrebbe richiedere (ad esempio le preferenze dell'utente), è possibile utilizzare KVS archiviazione iCloud. iCloud KVS dati viene automaticamente sincronizzati al cloud e tutti i dispositivi dell'utente che esegue la stessa app. Per ulteriori informazioni, vedere il [archivio chiave-valore](~/ios/data-cloud/introduction-to-icloud.md) sezione del nostro [introduzione in iCloud](~/ios/data-cloud/introduction-to-icloud.md) documento o Apple [progettazione per i dati di chiave-valore in iCloud](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7) documentazione.
- **CloudKit** : per l'archiviazione di più parti di informazioni (maggiore di 1 MB), utilizzare il Framework di CloudKit di Apple. A differenza di iCloud KVS archiviazione, dati CloudKit possono essere condivisa tra tutti gli utenti dell'app (così come privato a un singolo utente). Per ulteriori informazioni, consultare il nostro [Introduzione a CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) documentazione o Apple [CloudKit Quick Start](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987).

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori di gestire correttamente generale Data Protection regolamento (PILR dell'Unione europea).

<a name="On-Demand-Resources" />

### <a name="on-demand-resources"></a>Risorse su richiesta

Risorse su richiesta di fornire contenuto dell'app e risorse (separati dal bundle di app), che sono ospitati in App Store e scaricati come richiesto dall'app. Fino a 2 GB aggiuntiva dei dati può essere disponibile quando si utilizza risorse su richiesta. Consentono il download iniziale app a essere più piccoli (app tvOS sono limitate a un massimo di 200MB), garantendo al tempo stesso asset avanzati come richiesto.

Quando un'app tvOS richiede risorse su richiesta, il sistema gestisce automaticamente il download e l'archiviazione del contenuto nella directory della cache dell'applicazione. L'app deve attendere per il contenuto da scaricare e reso disponibile prima di continuare.

Queste risorse possono continuare a essere memorizzati nella cache in Apple TV in più Avvia dell'app, pertanto accelerare ciclo di avvio. Tuttavia, l'app è possibile basarsi su qualsiasi contenuto scaricato in precedenza è disponibile al successivo avvio. Vedere il [Scarica Non-Persistent](#Non-Persistent-Downloads) precedente sezione per ulteriori dettagli.

Xcode è possibile per creare pacchetti di contenuto correlato (ad esempio, tutte le risorse per il gioco livello 2) associate a un Tag di risorse offrono. Specificando questo Tag delle risorse in un secondo momento l'app richiederà di risorse su richiesta. L'app deve presentare un'interfaccia utente per l'utente che informa che il contenuto durante il download. Per ulteriori informazioni, vedere Apple [Guida risorse su richiesta](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083).

> [!IMPORTANT]
> Necessario fare attenzione a raggiungere il giusto equilibrio tra il numero di volte in cui che l'app deve scaricare le risorse su richiesta e le dimensioni dei singoli download. Utente potrebbe diventare sempre con l'app in caso di interruzione costantemente la modalità di gioco per scaricare il contenuto nuovo o se un unico download impiega troppo tempo.




<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati le limitazioni di archiviazione dimensioni, risorse e i dati inserite in un'app Xamarin.tvOS dal sistema tvOS. È presentato opzioni per risolvere queste limitazioni e i suggerimenti relativi alla creazione di un'esperienza utente ottimale per l'app.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
