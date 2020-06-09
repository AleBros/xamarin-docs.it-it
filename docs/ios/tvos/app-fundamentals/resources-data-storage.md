---
title: Risorse e archiviazione dei dati di tvOS in Novell
description: Questo articolo descrive come usare l'archiviazione di risorse e dati permanenti in un'app tvOS compilata con Novell. Illustra l'archiviazione dei dati iCloud e le risorse su richiesta.
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 8619fa73a4dbaabe1e161c634b6a794b701d5135
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84570859"
---
# <a name="tvos-resources-and-data-storage-in-xamarin"></a>Risorse e archiviazione dei dati di tvOS in Novell

_Questo articolo illustra l'uso delle risorse e dell'archiviazione dei dati permanenti in un'app Novell. tvOS._

<a name="tvOS-Resource-Limitations"></a>

## <a name="tvos-resource-limitations"></a>Limitazioni delle risorse di tvOS

A differenza dei dispositivi iOS, il nuovo Apple TV fornisce un'archiviazione locale persistente estremamente limitata per le app o i dati tvOS. Per gli elementi molto piccoli, ad esempio le preferenze dell'utente, l'app tvOS può ancora accedere a `NSUserDefaults` con un [limite di 500 KB di dati](https://forums.developer.apple.com/message/50696#50696). Tuttavia, se l'app Novell. tvOS deve salvare in modo permanente maggiori quantità di informazioni, deve archiviare e recuperare i dati da [iCloud](#iCloud-Data-Storage).

Inoltre, tvOS limita le dimensioni di un'app Apple TV a 200 MB. Se l'app richiede risorse oltre questa dimensione, è necessario che vengano inserite in un pacchetto e caricate usando [risorse su richiesta](#On-Demand-Resources) (fino a 2 GB aggiuntivi). Date queste limitazioni, è fondamentale che il tempo necessario per il download di asset aggiuntivi fornisca la migliore esperienza per gli utenti dell'app. Per altre informazioni, vedere la [Guida alle risorse su richiesta](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)di Apple.

<a name="Non-Persistent-Downloads"></a>

## <a name="non-persistent-downloads"></a>Download non permanenti

A ogni app tvOS viene fornita una directory della cache temporanea in cui vengono scaricate le risorse e gli asset aggiuntivi. Questa directory verrà mantenuta fino a quando l'app è ancora in esecuzione. Tuttavia, poiché Apple TV deve liberare spazio per altre app o l'utilizzo del sistema, questa cache può essere eliminata.

Di conseguenza, l'app non può fare affidamento sul contenuto scaricato in precedenza disponibile al successivo avvio. L'app Novell. tvOS deve sempre verificare l'esistenza delle risorse necessarie e scaricarle come richiesto.

> [!IMPORTANT]
> Sebbene sia possibile scaricare altri asset e risorse in base alle esigenze, Apple avverte di non consumare tutto lo spazio disponibile nella cache dell'app, in quanto può causare risultati imprevedibili.

<a name="Managing-Resources"></a>

## <a name="managing-resources"></a>Gestione delle risorse

Come indicato in precedenza, a causa dell'archiviazione limitata e non permanente delle informazioni disponibili per le app tvOS, queste restrizioni richiedono un'attenta pianificazione per creare un'esperienza utente ottimale per l'app Novell. tvOS.

<a name="iCloud-Data-Storage"></a>

### <a name="icloud-data-storage"></a>Archiviazione dati iCloud

Poiché l'archiviazione in Apple TV è limitata, non solo è presente una risorsa di archiviazione locale molto limitata e persistente. l'app non ha alcuna garanzia che le informazioni scaricate in precedenza saranno disponibili alla successiva esecuzione.

Di conseguenza, l'app Novell. tvOS deve archiviare tutti i dati utente in un archivio dati iCloud. Apple fornisce due opzioni di archiviazione dati basate su iCloud per le app tvOS:

- **archiviazione chiave-valore iCloud (kvs)** : per informazioni di piccole dimensioni (inferiori a 1 MB) che possono essere richieste dall'app, ad esempio le preferenze dell'utente, è possibile usare l'archiviazione kvs iCloud. i dati KVS di iCloud vengono sincronizzati automaticamente nel cloud e in tutti i dispositivi dell'utente che eseguono la stessa app. Per altre informazioni, vedere la sezione [archiviazione chiave-valore](~/ios/data-cloud/introduction-to-icloud.md) dell' [Introduzione al documento iCloud](~/ios/data-cloud/introduction-to-icloud.md) o progettazione di Apple [per i dati chiave-valore nella](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7) documentazione di iCloud.
- **CloudKit** : per l'archiviazione di informazioni di dimensioni maggiori (più di 1 MB), usare il Framework CloudKit di Apple. Diversamente dall'archiviazione di iCloud KVS, i dati di CloudKit possono essere condivisi tra tutti gli utenti dell'app, oltre a essere privati per un singolo utente. Per ulteriori informazioni, vedere la documentazione [introduttiva di CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) o CloudKit di Apple [avvio rapido](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987).

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

<a name="On-Demand-Resources"></a>

### <a name="on-demand-resources"></a>Risorse su richiesta

Le risorse su richiesta forniscono contenuto e risorse per le app (separate dal bundle dell'app), ospitate nell'App Store e scaricate come richiesto dall'app. Fino a un massimo di 2 GB di dati possono essere serviti con risorse su richiesta. Consentono di ridurre le dimensioni del download dell'app iniziale (le app tvOS sono limitate a un massimo di 200 MB), offrendo allo stesso tempo risorse avanzate come richiesto.

Quando un'app tvOS richiede risorse su richiesta, il sistema gestisce automaticamente il download e l'archiviazione del contenuto nella directory della cache dell'app. Per poter continuare, l'app deve attendere che il contenuto venga scaricato e reso disponibile.

Queste risorse possono continuare a essere memorizzate nella cache in Apple TV in diversi lanci dell'app, velocizzando così il ciclo di avvio. Tuttavia, l'app non può fare affidamento su un contenuto scaricato in precedenza disponibile al successivo avvio. Per ulteriori informazioni, vedere la sezione [download non persistente](#Non-Persistent-Downloads) precedente.

Usare Xcode per creare bundle di contenuto correlato (ad esempio, tutti gli asset per il livello di gioco 2) associati a un tag Give Resource. Successivamente, l'app richiederà una risorsa su richiesta specificando questo tag di risorsa. L'app deve presentare un'interfaccia utente all'utente che informa che è in corso il download del contenuto. Per altre informazioni, vedere la [Guida alle risorse su richiesta](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)di Apple.

> [!IMPORTANT]
> È necessario prestare attenzione per trovare il giusto equilibrio tra il numero di volte in cui l'app deve scaricare le risorse su richiesta e le dimensioni dei singoli download. L'utente potrebbe essere frustrato con l'app se il gioco viene interrotto costantemente per scaricare nuovo contenuto o se un singolo download richiede troppo tempo.

<a name="Summary"></a>

## <a name="summary"></a>Summary

Questo articolo ha trattato le limitazioni relative alle dimensioni, alle risorse e all'archiviazione dei dati inserite in un'app Novell. tvOS dal sistema tvOS. Sono state presentate opzioni per ovviare a queste limitazioni e suggerimenti per creare un'esperienza utente ottimale per l'app.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
