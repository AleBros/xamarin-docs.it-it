---
title: Accesso ai dati di xamarin. IOS
description: Questo documento include collegamenti alle guide che descrivono come usare i database locali in un'applicazione xamarin. IOS. Contenuto collegato illustra SQLite.NET, ADO.NET e altro ancora.
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: 017118a74528718ea99fe218f443b8df83d7c52e
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241279"
---
# <a name="xamarinios-data-access"></a>Accesso ai dati di xamarin. IOS

Xamarin. IOS supporta l'accesso al database, API, ad esempio:

-  Framework di ADO.NET.
-  Libreria di terze parti 3rd SQLite-NET.

Questa guida fornisce una panoramica generale del database in genere prima di descrivere come configurare SQLite.NET e ADO.NET per accedere ai database SQLite nelle applicazioni xamarin. IOS. 

La maggior parte del codice in questo documento è completamente lo sviluppo multipiattaforma e verrà eseguite senza modifica su iOS o Android. Esistono due app di esempio illustrata:

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) : operazioni sui dati semplice scrive i risultati in un testo vengono visualizzati controllo.
-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) – integra le operazioni sui dati in una piccola applicazione funzionante che elenca e modifica di una struttura di dati semplici.

Entrambe le soluzioni di esempio contengono progetti di applicazione di esempio di Android e iOS.

Per le applicazioni xamarin. Forms, leggere [funziona con i database](~/xamarin-forms/app-fundamentals/databases.md) che illustra come operare con SQLite in una libreria di classi Portabile con xamarin. Forms.

## <a name="sections"></a>Sezioni

-  [Introduzione](introduction.md)
-  [Configurazione](configuration.md)
-  [Uso di SQLite.NET ORM](using-sqlite-orm.md)
-  [Uso di ADO.NET](using-adonet.md)
-  [Uso dei dati in un'app](using-data-in-an-app.md)

## <a name="summary"></a>Riepilogo

In questo capitolo è illustrato l'accesso ai dati in xamarin. IOS con SQLite come il motore di database. Il database è accessibile "direttamente" usando la sintassi ADO.NET oppure è possibile includere SQLite.NET ORM ed eseguire operazioni sui dati nel linguaggio c#.

Abbiamo esaminato due esempi: uno che contiene codice di accesso ai dati molto semplice che gli output in un campo di testo e un'applicazione semplice che include creazione, lettura, aggiornamento ed eliminazione di funzionalità. Abbiamo anche discusso di threading e su come effettuare il seeding dell'applicazione con un database SQLite prepopolato.

Per altri esempi di accesso ai dati di multi-piattaforma, vedere la [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) case study.

## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzate (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS recipe di dati](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
