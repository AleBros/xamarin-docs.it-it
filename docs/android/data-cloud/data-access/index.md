---
title: Accesso ai dati di xamarin. Android
description: La maggior parte delle applicazioni hanno alcuni requisiti per salvare i dati nel dispositivo in locale. A meno che la quantità di dati non sarebbero incredibilmente piccola, ciò richiede in genere un database e un livello di dati nell'applicazione per gestire l'accesso al database.  Android ha il motore di database SQLite 'predefinito' e l'accesso per archiviare e recuperare i dati è semplificato della piattaforma Xamarin. Questo documento viene illustrato come accedere a un database SQLite in modo lo sviluppo multipiattaforma.
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 08720734de73af12d8a7383fa7d523dc350c4462
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674769"
---
# <a name="xamarinandroid-data-access"></a>Accesso ai dati di xamarin. Android

_La maggior parte delle applicazioni hanno alcuni requisiti per salvare i dati nel dispositivo in locale. A meno che la quantità di dati non sarebbero incredibilmente piccola, ciò richiede in genere un database e un livello di dati nell'applicazione per gestire l'accesso al database.  Android ha il motore di database SQLite 'predefinito' e l'accesso per archiviare e recuperare i dati è semplificato della piattaforma Xamarin. Questo documento viene illustrato come accedere a un database SQLite in modo lo sviluppo multipiattaforma._

## <a name="data-access-overview"></a>Panoramica di accesso dati

La maggior parte delle applicazioni hanno alcuni requisiti per salvare i dati nel dispositivo in locale. A meno che la quantità di dati non sarebbero incredibilmente piccola, ciò richiede in genere un database e un livello di dati nell'applicazione per gestire l'accesso al database. Android entrambe con il motore di database SQLite "incorporato" e accedere ai dati è semplificato della piattaforma Xamarin, fornito con il Provider di dati di SQLite.

Xamarin. Android supporta API di accesso ai database, ad esempio:

- Framework di ADO.NET.
- Libreria di terze parti 3rd SQLite-NET.

La maggior parte del codice in questa sezione è completamente lo sviluppo multipiattaforma e verrà eseguite senza modifica su iOS o Android. Esistono due app di esempio illustrata:

- [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) &ndash; operazioni semplici dati scrive i risultati in un testo vengono visualizzati controllo.

- [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) &ndash; integra le operazioni sui dati in una piccola applicazione funzionante che elenca e modifica di una struttura di dati semplici.

Entrambe le soluzioni di esempio contengono progetti di applicazione di esempio di Android e iOS.

Per le applicazioni xamarin. Forms, leggere [funziona con i database](~/xamarin-forms/app-fundamentals/databases.md) che illustra come operare con SQLite in una libreria di classi Portabile con xamarin. Forms.

Gli argomenti in questa sezione descrivono l'accesso ai dati in xamarin. Android usando SQLite come il motore di database. Il database è accessibile "direttamente" usando la sintassi ADO.NET oppure è possibile includere SQLite.NET ORM ed eseguire operazioni sui dati nel linguaggio c#.

Vengono esaminati due esempi: uno che contiene codice di accesso ai dati molto semplice che gli output in un campo di testo e un'applicazione semplice che include creazione, lettura, aggiornamento ed eliminazione di funzionalità. Viene descritta anche threading e su come eseguire il seeding dell'applicazione con un database SQLite prepopolato.

Per altri esempi di accesso ai dati di multi-piattaforma, vedere la [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) case study.


## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzate (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Ricette dei dati Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
