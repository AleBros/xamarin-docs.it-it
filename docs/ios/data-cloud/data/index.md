---
title: Xamarin.iOS Data Access
description: Questo documento include collegamenti alle guide che descrivono come usare i database locali in un'applicazione xamarin. IOS. Contenuto collegato illustra SQLite.NET, ADO.NET e altro ancora.
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: 8d2513ba1c2ae2769e81659c98f3897f33d83fbf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61218038"
---
# <a name="xamarinios-data-access"></a>Xamarin.iOS Data Access

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
