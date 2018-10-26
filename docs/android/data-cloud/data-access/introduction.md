---
title: Introduzione ad archiviazione di dati in Android
ms.prod: xamarin
ms.assetid: FDAC0771-4749-4758-865A-F1BD190CA54B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 8c90e1c3013ec61cbb4641f19af3424f55b1a465
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103816"
---
# <a name="introduction"></a>Introduzione

## <a name="when-to-use-a-database"></a>Quando usare un Database

Mentre le funzionalità di elaborazione e archiviazione dei dispositivi mobili aumentano, telefoni e Tablet ancora ritardo delle controparti desktop e portatili. Per questo motivo, vale la pena mettendo un po' per pianificare l'architettura di archiviazione dei dati per l'app, anziché solo supponendo che la soluzione giusta tutto il tempo è un database. Esistono una serie di opzioni diverse che soddisfare requisiti diversi, ad esempio:

-  **Preferenze** – Android offre un meccanismo predefinito per l'archiviazione di coppie chiave-valore semplice dei dati. Se si archiviano le impostazioni utente semplice o piccole porzioni di dati (ad esempio le informazioni sulla personalizzazione) usare le funzionalità native della piattaforma per l'archiviazione di questo tipo di informazioni.
-  **File di testo** – input dell'utente o le cache del contenuto (ad esempio scaricato HTML) possono essere archiviati direttamente nel file system. Usare una convenzione di denominazione dei file appropriata per organizzare i file e trovare i dati.
-  **I file di dati serializzati** : possono essere resi persistenti gli oggetti in formato XML o JSON nel file system. .NET framework include le librerie che semplificano la serializzazione e deserializzazione degli oggetti. Usare i nomi appropriati per organizzare i file di dati.
-  **Database** : motore di database SQLite la è disponibile nella piattaforma Android e sono strutturati utili per archiviare i dati necessari per eseguire una query, ordinamento o modificare in altro modo. Archiviazione del database è adatta agli elenchi di dati con molte proprietà.
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

SQLite è un motore di database open source che è stato adottato da Google per le rispettive piattaforme per dispositivi mobili. Il motore di database SQLite è incorporato per entrambi i sistemi operativi, pertanto non presenta alcuna attività aggiuntiva per gli sviluppatori a sfruttare i vantaggi di esso. SQLite è particolarmente adatto per lo sviluppo per dispositivi mobili multipiattaforma in quanto:

-  Il motore di database è piccolo, veloce e facilmente trasferibili.
-  Un database viene archiviato in un singolo file, è facile da gestire nei dispositivi mobili.
-  Il formato del file è facile da usare per più piattaforme: se 32 o 64 bit e i sistemi big - endian o little-endian.
-  Implementa la maggior parte di standard di SQL92.


Poiché SQLite è progettato per essere piccoli e veloci, esistono alcune limitazioni sul relativo uso:

-  Parte della sintassi di OUTER join non è supportato.
-  Solo la RIDENOMINAZIONE di tabella e ADDCOLUMN sono supportati. È possibile eseguire altre modifiche allo schema.
-  Le viste sono di sola lettura.


Altre informazioni su SQLite nel sito Web - [SQLite.org](http://SQLite.org) : tuttavia, tutte le informazioni necessarie per l'uso di SQLite con Xamarin sono contenute in questo documento e associati gli esempi. Il motore di database SQLite è supportato in Android partire da Android 2.
Anche se non trattate in questo capitolo, SQLite è anche disponibile per l'utilizzo in applicazioni di Windows e Windows Phone.

## <a name="windows-and-windows-phone"></a>Windows e Windows Phone

SQLite è anche utilizzabile nelle piattaforme Windows, anche se queste piattaforme non incluse in questo documento.
Altre informazioni, vedere la [Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) e [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) case study e rivedere [blog di Tim Heuer](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx).


## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzate (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Ricette dei dati Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
