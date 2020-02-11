---
title: Accesso ai dati di Xamarin.iOS
description: Questo documento contiene collegamenti a guide che descrivono come usare i database locali in un'applicazione Xamarin.iOS. Il contenuto collegato illustra SQLite.NET, ADO.NET e altro ancora.
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/11/2016
ms.openlocfilehash: 163bd88349ee55af5f518a20364bbce7fe6052b0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008429"
---
# <a name="xamarinios-data-access"></a>Accesso ai dati di Xamarin.iOS

Xamarin.iOS supporta le API di accesso al database, ad esempio:

- ADO.NET Framework.
- Libreria di terze parti SQLite-NET.

Questa guida fornisce una panoramica generale dei database in generale prima di descrivere come configurare ADO.NET e SQLite.NET per accedere ai database SQLite nelle applicazioni Xamarin.iOS. 

La maggior parte del codice in questo documento è completamente multipiattaforma e verrà eseguita in iOS o Android senza modifiche. Sono illustrate due app di esempio:

- [**DataAccess_Basic**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) : operazioni di dati semplici scrive i risultati in un controllo di visualizzazione del testo;
- [**DataAccess_Advanced**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) : integra le operazioni sui dati in una piccola applicazione funzionante che elenca e modifica una struttura di dati semplice.

Entrambe le soluzioni di esempio contengono progetti di applicazioni di esempio iOS e Android.

Per le applicazioni Xamarin.Forms, vedere [uso dei database](~/xamarin-forms/data-cloud/data/databases.md) che spiega come usare SQLite in una libreria PCL con Xamarin.Forms.

## <a name="sections"></a>Sezioni

- [Introduzione](introduction.md)
- [Configurazione](configuration.md)
- [Uso di SQLite.NET ORM](using-sqlite-orm.md)
- [Uso di ADO.NET](using-adonet.md)
- [Uso dei dati in un'app](using-data-in-an-app.md)

## <a name="summary"></a>Riepilogo

Questo capitolo ha illustrato l'accesso ai dati in Xamarin.iOS usando SQLite come motore di database. È possibile accedere direttamente al database usando la sintassi ADO.NET oppure è possibile includere l'ORM SQLite.NET ed eseguire operazioni sui dati in C#.

Sono stati esaminati due esempi: uno che contiene codice di accesso ai dati molto semplice che restituisce un campo di testo e una semplice applicazione che include funzionalità di creazione, lettura, aggiornamento ed eliminazione. È stato anche illustrato il threading e come inizializzare l'applicazione con un database SQLite pre-popolato.

Per altri esempi di accesso ai dati multipiattaforma, vedere la pagina relativa a Microsoft [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) case study.

## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Ricette per i dati iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Accesso ai dati di Xamarin.Forms](~/xamarin-forms/data-cloud/data/databases.md)
