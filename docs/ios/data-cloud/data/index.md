---
title: accesso ai dati iOS
description: "La maggior parte delle applicazioni presentano un requisito per salvare i dati nel dispositivo locale. A meno che la quantità di dati sia in modo semplice di piccole dimensioni, è in genere necessario un database e un livello di dati nell'applicazione per gestire l'accesso al database. iOS è il motore di database SQLite \"incorporato\" e viene semplificato l'accesso per archiviare e recuperare dati dalla piattaforma di Xamarin. Questo documento viene illustrato come accedere a un database SQLite."
ms.topic: article
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: 6dba862055cc266b2af1eaf87418fe7ebf5830c5
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="ios-data-access"></a>accesso ai dati iOS

_La maggior parte delle applicazioni presentano un requisito per salvare i dati nel dispositivo locale. A meno che la quantità di dati sia in modo semplice di piccole dimensioni, è in genere necessario un database e un livello di dati nell'applicazione per gestire l'accesso al database. iOS è il motore di database SQLite "incorporato" e viene semplificato l'accesso per archiviare e recuperare dati dalla piattaforma di Xamarin. Questo documento viene illustrato come accedere a un database SQLite._

Xamarin supporta le API di accesso a database, ad esempio:

-  Framework ADO.NET.
-  SQLite NET 3rd party libreria.

Questa guida fornisce una panoramica generale del database in genere prima di descrivere come configurare SQLite.NET e ADO.NET per accedere ai database SQLite nelle applicazioni di xamarin. IOS. 

La maggior parte del codice in questo documento è completamente multipiattaforma e verrà eseguiti su iOS o Android senza modifiche. Esistono due applicazioni di esempio illustrate:

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) : operazioni sui dati semplici scrive i risultati in un testo di visualizzano controllo.
-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) – integra le operazioni sui dati in una piccola applicazione funzionante che elenca e modifica di una struttura di dati semplice.

Entrambe le soluzioni di esempio contengono progetti di applicazione di esempio di Android e iOS.

Per le applicazioni di xamarin. Forms, leggere [si lavora con database](~/xamarin-forms/app-fundamentals/databases.md) che spiega come usare SQLite in una libreria di libreria di classi Portabile. Forms.

## <a name="sections"></a>Sezioni

-  [Introduzione](introduction.md)
-  [Configurazione](configuration.md)
-  [Uso di SQLite.NET ORM](using-sqlite-orm.md)
-  [Uso di ADO.NET](using-adonet.md)
-  [Uso dei dati in un'app](using-data-in-an-app.md)


## <a name="summary"></a>Riepilogo

In questo capitolo è descritto l'accesso ai dati in xamarin. IOS utilizzando SQLite come il motore di database. Il database è accessibile "direttamente" utilizzando la sintassi ADO.NET oppure è possibile includere il ORM SQLite.NET ed eseguire operazioni sui dati in c#.

È stata esaminata due esempi: uno che contiene codice di accesso ai dati molto semplice che di output per un campo di testo e un'applicazione semplice che include creazione, lettura, aggiornamento ed eliminazione di funzionalità. È stato descritto threading e la modalità di inizializzazione dell'applicazione con un database SQLite pre-popolato.

Per ulteriori esempi di accesso ai dati di più piattaforme, vedere il nostro [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) case study di.

## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzate (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS dati ricette](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
