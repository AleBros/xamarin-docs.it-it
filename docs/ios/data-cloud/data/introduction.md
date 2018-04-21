---
title: Introduzione all'archiviazione di dati in iOS
ms.prod: xamarin
ms.assetid: B1994468-FD06-4FD9-96B3-FCEBB13A972A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: 0da267502629ba30da59c26600ef0e7c605640aa
ms.sourcegitcommit: 797597d902330652195931dec9ac3e0cc00792c5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2018
---
# <a name="introduction"></a>Introduzione

## <a name="when-to-use-a-database"></a>Quando utilizzare un Database

Mentre le funzionalità di elaborazione e archiviazione dei dispositivi mobili sono in aumento, telefoni e Tablet ancora un ritardo rispetto desktop &amp; controparti portatile. Per questo motivo è importante prendere tempo per pianificare l'architettura di archiviazione di dati per l'app anziché solo se che un database è la risposta esatta costantemente. Esistono una serie di diverse opzioni che consentono di soddisfare i requisiti diversi, ad esempio:

-  **Preferenze** – iOS offre un meccanismo predefinito per l'archiviazione di coppie chiave-valore semplice dei dati. Se si desidera archiviare le impostazioni utente semplice o piccole quantità di dati (ad esempio le informazioni sulla personalizzazione) usare funzionalità nativo della piattaforma per l'archiviazione di questo tipo di informazioni. Per iOS è possibile usufruire di sincronizzazione iCloud per questi dati, sia per il backup e sincronizzazione per gli utenti con più dispositivi.
-  **File di testo** : l'input dell'utente o delle cache di scaricare contenuto (ad es. HTML) possono essere archiviate direttamente nel file system. Utilizzare una convenzione di denominazione dei file appropriata che consentono di organizzare i file e trovare i dati.
-  **File di dati serializzati** – oggetti possono essere mantenuti come XML o JSON nel file system. .NET framework include le librerie che rendono la serializzazione e deserializzazione degli oggetti. Utilizzare i nomi appropriati per organizzare i file di dati.
-  **Database** – SQLite il motore di database è disponibile iOS e risulta utile per l'archiviazione dei dati strutturati che si devono eseguire una query, ordinare o modificare in altro modo. Archiviazione del database è utile per gli elenchi di dati con molte proprietà.
-  **File di immagine** : anche se è possibile archiviare i dati binari nel database in un dispositivo mobile, è consigliabile archiviarli direttamente nel file system. Se necessario è possibile archiviare i nomi dei file in un database a cui associare l'immagine con altri dati. Quando si gestiscono le immagini di grandi dimensioni, o un numero elevato di immagini, è consigliabile pianificare una strategia di memorizzazione nella cache che elimina i file che non è più necessario per evitare un utilizzo spazio di archiviazione dell'utente.


Se un database è il meccanismo di archiviazione corretto per l'app, il resto di questo documento viene illustrato come utilizzare SQLite sulla piattaforma Xamarin.

## <a name="advantages-of-using-a-database"></a>Vantaggi dell'utilizzo di un Database

Esistono numerosi vantaggi rispetto all'utilizzo di un database SQL in app per dispositivi mobili:

-  Database SQL consentono di archiviare in modo efficiente i dati strutturati.
-  Dati specifici possono essere estratti con query complesse.
-  È possibile ordinare i risultati della query.
-  È possibile aggregare i risultati della query.
-  Gli sviluppatori con conoscenze di database possono utilizzare le proprie conoscenze per progettare il codice di accesso ai dati e il database.
-  Il modello di dati dal componente server di un'applicazione connessa può essere utilizzato nuovamente (intero o in parte) nell'applicazione per dispositivi mobili.


## <a name="sqlite-database-engine"></a>Motore di Database SQLite

SQLite è un motore di database open source che è stato adottato da Apple per la piattaforma per dispositivi mobili. Il motore di database SQLite è incorporato per iOS, pertanto non c'è alcuna attività aggiuntiva per gli sviluppatori di sfruttare i vantaggi. SQLite è particolarmente adatto per lo sviluppo mobile multipiattaforma perché:

-  Il motore di database è facilmente trasferibili, piccola e veloce.
-  Un database viene archiviato in un singolo file, è facile da gestire dispositivi mobili.
-  Il formato del file è facile da usare su piattaforme diverse: se 32 o 64 bit e i sistemi big - endian o little-endian.
-  Implementa la maggior parte del SQL92 standard.


Poiché SQLite è progettato per essere piccoli e veloci, vi sono alcune raccomandazioni sul relativo uso:

-  Parte della sintassi di OUTER join non è supportata.
-  Tabella solo RIDENOMINAZIONE e ADDCOLUMN sono supportati. È possibile eseguire altre modifiche allo schema.
-  Le visualizzazioni sono di sola lettura.


Maggiori informazioni su SQLite nel sito Web - [SQLite.org](http://SQLite.org) - tuttavia tutte le informazioni necessarie per l'uso di SQLite con Xamarin sono contenute in questo documento e associati gli esempi. Il motore di database SQLite è incorporato in tutte le versioni di iOS.
Sebbene non trattati in questo capitolo, SQLite è anche disponibile per l'utilizzo in applicazioni di Windows e Windows Phone.

## <a name="windows-and-windows-phone"></a>Windows e Windows Phone

SQLite utilizzabili nelle piattaforme Windows, anche se tali piattaforme non sono trattate in questo documento.
Altre informazioni, vedere il [Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) e [Tasky Pro](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/case_study%3A_tasky) case study ed esaminare [blog di Tim Heuer](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx).



## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzate (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS dati ricette](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
