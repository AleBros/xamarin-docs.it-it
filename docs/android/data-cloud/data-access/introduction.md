---
title: Introduzione all'archiviazione dei dati in Android
ms.prod: xamarin
ms.assetid: FDAC0771-4749-4758-865A-F1BD190CA54B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 69d5222bb6c50870d0c42bea6ff71236e3d1580c
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "70754559"
---
# <a name="introduction"></a>Introduzione

## <a name="when-to-use-a-database"></a>Quando utilizzare un database

Mentre le funzionalità di archiviazione e di elaborazione dei dispositivi mobili aumentano, i telefoni e i tablet restano in ritardo dietro le controparti desktop e portatili. Per questo motivo è opportuno prendere tempo per pianificare l'architettura di archiviazione dei dati per l'app piuttosto che solo supponendo che un database sia la risposta giusta sempre. Sono disponibili diverse opzioni che soddisfano requisiti diversi, ad esempio:

- **Preferenze** : Android offre un meccanismo incorporato per l'archiviazione di semplici coppie chiave-valore di dati. Se si archiviano semplici impostazioni utente o piccole parti di dati (ad esempio informazioni sulla personalizzazione), utilizzare le funzionalità native della piattaforma per archiviare questo tipo di informazioni.
- **File di testo** : input utente o cache del contenuto scaricato, ad esempio HTML) può essere archiviato direttamente nel file System. Usare una convenzione di denominazione dei file appropriata per organizzare i file e trovare i dati.
- **File di dati serializzati** : gli oggetti possono essere salvati in modo permanente come XML o JSON nel file System. In .NET Framework sono incluse librerie che semplificano la serializzazione e la deserializzazione di oggetti. Usare i nomi appropriati per organizzare i file di dati.
- **Database** : il motore di database SQLite è disponibile nella piattaforma Android ed è utile per archiviare i dati strutturati necessari per eseguire query, ordinare o modificare in altro modo. L'archiviazione del database è adatta agli elenchi di dati con molte proprietà.
- **File di immagine** : Sebbene sia possibile archiviare dati binari nel database di un dispositivo mobile, è consigliabile archiviarli direttamente nel file System. Se necessario, è possibile archiviare i nomi di file in un database per associare l'immagine ad altri dati. Quando si gestiscono immagini di grandi dimensioni o molte immagini, è consigliabile pianificare una strategia di memorizzazione nella cache che elimini i file che non sono più necessari per evitare l'utilizzo dello spazio di archiviazione dell'utente.

Se un database è il meccanismo di archiviazione corretto per l'app, nella parte restante di questo documento viene illustrato come usare SQLite nella piattaforma Novell.

## <a name="advantages-of-using-a-database"></a>Vantaggi dell'utilizzo di un database

L'uso di un database SQL nell'app per dispositivi mobili presenta diversi vantaggi:

- I database SQL consentono l'archiviazione efficiente di dati strutturati.
- È possibile estrarre dati specifici con query complesse.
- I risultati della query possono essere ordinati.
- I risultati della query possono essere aggregati.
- Gli sviluppatori con competenze di database esistenti possono utilizzare le proprie conoscenze per progettare il database e il codice di accesso ai dati.
- Il modello di dati dal componente server di un'applicazione connessa può essere riutilizzato (interamente o in parte) nell'applicazione per dispositivi mobili.

## <a name="sqlite-database-engine"></a>motore di database SQLite

SQLite è un motore di database open source che è stato adottato da Google per la piattaforma per dispositivi mobili. Il motore di database SQLite è integrato in entrambi i sistemi operativi, pertanto non è necessario alcun lavoro aggiuntivo da parte degli sviluppatori. SQLite è particolarmente adatto per lo sviluppo di app per dispositivi mobili multipiattaforma perché:

- Il motore di database è di dimensioni ridotte, veloci e facilmente portabili.
- Un database viene archiviato in un singolo file, che è facile da gestire nei dispositivi mobili.
- Il formato del file è facile da usare tra le piattaforme: se 32 o 64 bit e i sistemi Big o little-endian.
- Implementa la maggior parte dello standard SQL92.

Poiché SQLite è progettato per essere piccolo e veloce, esistono alcune avvertenze sull'uso:

- La sintassi di OUTER join non è supportata.
- Sono supportate solo le ridenominazioni di tabelle e ADDCOLUMN. Non è possibile apportare altre modifiche allo schema.
- Le visualizzazioni sono di sola lettura.

Per altre informazioni su SQLite, vedere il sito Web- [SQLite.org](http://SQLite.org) . Tuttavia, tutte le informazioni necessarie per usare SQLite con Novell sono contenute in questo documento ed esempi associati. Il motore di database SQLite è stato supportato in Android a partire da Android 2.
Anche se non è trattato in questo capitolo, SQLite è disponibile anche per l'uso in applicazioni Windows Phone e Windows.

## <a name="windows-and-windows-phone"></a>Windows e Windows Phone

SQLite può essere usato anche nelle piattaforme Windows, anche se queste piattaforme non sono descritte in questo documento.
Per altre informazioni, vedere la pagina relativa ai case study di [attività](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) e [attività professionali](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) e consultare [il Blog di Tim Heuer](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx).

## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Ricette per i dati Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Accesso ai dati di Novell. Forms](~/xamarin-forms/data-cloud/data/databases.md)
