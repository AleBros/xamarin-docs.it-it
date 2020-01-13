---
title: Introduzione all'archiviazione dei dati nelle app Xamarin.iOS
description: Questo documento descrive i vari metodi di archiviazione dei dati in un'applicazione Xamarin.iOS e fornisce informazioni specifiche sui vantaggi di SQLite.
ms.prod: xamarin
ms.assetid: B1994468-FD06-4FD9-96B3-FCEBB13A972A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/11/2016
ms.openlocfilehash: eefe57abd4ebf4986411a1d717aebd131ebf408f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008341"
---
# <a name="introduction-to-data-storage-in-xamarinios-apps"></a>Introduzione all'archiviazione dei dati nelle app Xamarin.iOS

## <a name="when-to-use-a-database"></a>Quando utilizzare un database

Anche se le funzionalità di archiviazione e di elaborazione dei dispositivi mobili aumentano, i telefoni e i tablet restano in ritardo dietro le controparti desktop &amp; portatile. Per questo motivo è opportuno prendere tempo per pianificare l'architettura di archiviazione dei dati per l'app piuttosto che solo supponendo che un database sia la risposta giusta sempre. Sono disponibili diverse opzioni che soddisfano requisiti diversi, ad esempio:

- **Preferenze** : iOS offre un meccanismo incorporato per l'archiviazione di semplici coppie chiave-valore di dati. Se si archiviano semplici impostazioni utente o piccole parti di dati (ad esempio informazioni sulla personalizzazione), utilizzare le funzionalità native della piattaforma per archiviare questo tipo di informazioni. Per iOS è inoltre possibile sfruttare la sincronizzazione di iCloud per questi dati, sia per il backup che per la sincronizzazione per gli utenti con più dispositivi.
- **File di testo** : input utente o cache del contenuto scaricato, ad esempio HTML) può essere archiviato direttamente nel file System. Usare una convenzione di denominazione dei file appropriata per organizzare i file e trovare i dati.
- **File di dati serializzati** : gli oggetti possono essere salvati in modo permanente come XML o JSON nel file System. In .NET Framework sono incluse librerie che semplificano la serializzazione e la deserializzazione di oggetti. Usare i nomi appropriati per organizzare i file di dati.
- **Database** : il motore di database SQLite è disponibile per iOS ed è utile per archiviare i dati strutturati necessari per eseguire query, ordinare o modificare in altro modo. L'archiviazione del database è adatta agli elenchi di dati con molte proprietà.
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

SQLite è un motore di database open source che è stato adottato da Apple per la piattaforma per dispositivi mobili. Il motore di database SQLite è integrato in iOS, quindi non sono previsti altri lavori per gli sviluppatori. SQLite è particolarmente adatto per lo sviluppo di app per dispositivi mobili multipiattaforma perché:

- Il motore di database è di dimensioni ridotte, veloci e facilmente portabili.
- Un database viene archiviato in un singolo file, che è facile da gestire nei dispositivi mobili.
- Il formato del file è facile da usare tra le piattaforme: se 32 o 64 bit e i sistemi Big o little-endian.
- Implementa la maggior parte dello standard SQL92.

Poiché SQLite è progettato per essere piccolo e veloce, esistono alcune avvertenze sull'uso:

- La sintassi di OUTER join non è supportata.
- Sono supportate solo le ridenominazioni di tabelle e ADDCOLUMN. Non è possibile apportare altre modifiche allo schema.
- Le visualizzazioni sono di sola lettura.

Per altre informazioni su SQLite, vedere il sito Web- [SQLite.org](https://SQLite.org) . Tuttavia, tutte le informazioni necessarie per usare SQLite con Novell sono contenute in questo documento ed esempi associati. Il motore di database SQLite è integrato in tutte le versioni di iOS.
Anche se non è trattato in questo capitolo, SQLite è disponibile anche per l'uso in applicazioni Windows Phone e Windows.

## <a name="windows-and-windows-phone"></a>Windows e Windows Phone

SQLite può essere usato anche nelle piattaforme Windows, anche se queste piattaforme non sono descritte in questo documento.
Per ulteriori informazioni, vedere la case study di [lavoro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) e consultare il [Blog di Tim Heuer](https://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx).

## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Ricette per i dati iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Accesso ai dati di Xamarin.Forms](~/xamarin-forms/data-cloud/data/databases.md)
