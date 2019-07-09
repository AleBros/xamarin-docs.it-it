---
title: Introduzione ad archiviazione di dati nelle App xamarin. IOS
description: Questo documento descrive vari meccanismi di archiviazione dei dati in un'applicazione xamarin. IOS e fornisce informazioni specifiche sui vantaggi di SQLite.
ms.prod: xamarin
ms.assetid: B1994468-FD06-4FD9-96B3-FCEBB13A972A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: 5df001926512b182c19ccfcf896057e88f5a597a
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650237"
---
# <a name="introduction-to-data-storage-in-xamarinios-apps"></a>Introduzione ad archiviazione di dati nelle App xamarin. IOS

## <a name="when-to-use-a-database"></a>Quando usare un Database

Mentre le funzionalità di elaborazione e archiviazione dei dispositivi mobili aumentano, telefoni e Tablet ancora ritardo delle loro desktop &amp; controparti portatile. Per questo motivo, vale la pena mettendo un po' per pianificare l'architettura di archiviazione dei dati per l'app, anziché solo supponendo che la soluzione giusta tutto il tempo è un database. Esistono una serie di opzioni diverse che soddisfare requisiti diversi, ad esempio:

-  **Preferenze** – iOS offre un meccanismo predefinito per l'archiviazione di coppie chiave-valore semplice dei dati. Se si archiviano le impostazioni utente semplice o piccole porzioni di dati (ad esempio le informazioni sulla personalizzazione) usare le funzionalità native della piattaforma per l'archiviazione di questo tipo di informazioni. Per iOS è anche possibile sfruttare i vantaggi di iCloud sincronizzazione per questi dati, sia per il backup e sincronizzazione per gli utenti con più dispositivi.
-  **File di testo** – input dell'utente o le cache del contenuto (ad esempio scaricato HTML) possono essere archiviati direttamente nel file system. Usare una convenzione di denominazione dei file appropriata per organizzare i file e trovare i dati.
-  **I file di dati serializzati** : possono essere resi persistenti gli oggetti in formato XML o JSON nel file system. .NET framework include le librerie che semplificano la serializzazione e deserializzazione degli oggetti. Usare i nomi appropriati per organizzare i file di dati.
-  **Database** – di SQLite e motore di database disponibili iOS, è utile per archiviare dati strutturati che è necessario eseguire una query, ordinamento o modificare in altro modo. Archiviazione del database è adatta agli elenchi di dati con molte proprietà.
-  **File di immagine** : sebbene sia possibile archiviare i dati binari nel database in un dispositivo mobile, è consigliabile archiviarli direttamente nel file system. Se necessario è possibile archiviare i nomi di file in un database da associare l'immagine di altri dati. Quando si usano immagini di grandi dimensioni, o un numero elevato di immagini, è consigliabile pianificare una strategia di memorizzazione nella cache che elimina i file che non è più necessario per evitare un utilizzo spazio di archiviazione dell'utente.


Se un database è il meccanismo di archiviazione corretto per l'app, il resto di questo documento viene illustrato come utilizzare SQLite nella piattaforma Xamarin.

## <a name="advantages-of-using-a-database"></a>Vantaggi dell'uso di un Database

Esistono numerosi vantaggi rispetto all'utilizzo di un database SQL in app per dispositivi mobili:

-  Database SQL consentono di archiviare dati strutturati in modo efficiente.
-  I dati specifici possono essere estratti con query complesse.
-  È possibile ordinare i risultati della query.
-  È possibile aggregare i risultati della query.
-  Gli sviluppatori con competenze sui database esistenti possono usare le proprie conoscenze per progettare il codice di accesso ai database e dei dati.
-  Il modello di dati dal componente server di un'applicazione connessa possa essere utilizzato nuovamente (in toto o in parte) nell'applicazione per dispositivi mobili.


## <a name="sqlite-database-engine"></a>Motore di Database SQLite

SQLite è un motore di database open source che è stato adottato da Apple per le rispettive piattaforme per dispositivi mobili. Il motore di database SQLite è integrato per iOS in modo che non vi è alcuna attività aggiuntiva per gli sviluppatori a sfruttare i vantaggi di esso. SQLite è particolarmente adatto per lo sviluppo per dispositivi mobili multipiattaforma in quanto:

-  Il motore di database è piccolo, veloce e facilmente trasferibili.
-  Un database viene archiviato in un singolo file, è facile da gestire nei dispositivi mobili.
-  Il formato del file è facile da usare per più piattaforme: se 32 o 64 bit e i sistemi big - endian o little-endian.
-  Implementa la maggior parte di standard di SQL92.


Poiché SQLite è progettato per essere piccoli e veloci, esistono alcune limitazioni sul relativo uso:

-  Parte della sintassi di OUTER join non è supportato.
-  Solo la RIDENOMINAZIONE di tabella e ADDCOLUMN sono supportati. È possibile eseguire altre modifiche allo schema.
-  Le viste sono di sola lettura.


Altre informazioni su SQLite nel sito Web - [SQLite.org](http://SQLite.org) : tuttavia, tutte le informazioni necessarie per l'uso di SQLite con Xamarin sono contenute in questo documento e associati gli esempi. Il motore di database SQLite è incorporato per tutte le versioni di iOS.
Anche se non trattate in questo capitolo, SQLite è anche disponibile per l'utilizzo in applicazioni di Windows e Windows Phone.

## <a name="windows-and-windows-phone"></a>Windows e Windows Phone

SQLite è anche utilizzabile nelle piattaforme Windows, anche se queste piattaforme non incluse in questo documento.
Altre informazioni, vedere la [Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) e [Tasky Pro](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/case_study%3A_tasky) case study e rivedere [blog di Tim Heuer](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx).



## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzate (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS recipe di dati](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
