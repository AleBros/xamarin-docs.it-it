---
title: Xamarin.Android Data Access
description: La maggior parte delle applicazioni presentano un requisito per salvare i dati nel dispositivo locale. A meno che la quantità di dati sia in modo semplice di piccole dimensioni, è in genere necessario un database e un livello di dati nell'applicazione per gestire l'accesso al database.  Viene semplificato l'accesso per archiviare e recuperare dati dalla piattaforma di Xamarin Android ha il motore di database SQLite 'predefinito'. Questo documento viene illustrato come accedere a un database SQLite in modo multipiattaforma.
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 508a8f54723bfdd30b1c8ea8d4b6c1d422ae24e9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763250"
---
# <a name="xamarinandroid-data-access"></a>Xamarin.Android Data Access

_La maggior parte delle applicazioni presentano un requisito per salvare i dati nel dispositivo locale. A meno che la quantità di dati sia in modo semplice di piccole dimensioni, è in genere necessario un database e un livello di dati nell'applicazione per gestire l'accesso al database.  Viene semplificato l'accesso per archiviare e recuperare dati dalla piattaforma di Xamarin Android ha il motore di database SQLite 'predefinito'. Questo documento viene illustrato come accedere a un database SQLite in modo multipiattaforma._

## <a name="data-access-overview"></a>Panoramica di accesso ai dati

La maggior parte delle applicazioni presentano un requisito per salvare i dati nel dispositivo locale. A meno che la quantità di dati sia in modo semplice di piccole dimensioni, è in genere necessario un database e un livello di dati nell'applicazione per gestire l'accesso al database. Accesso ai dati viene semplificato dalla piattaforma di Xamarin che viene fornito con il Provider di dati di SQLite Android entrambi ha il motore di database Sqlite "predefinito".

Xamarin supporta le API di accesso a database, ad esempio:

-  Framework ADO.NET.
-  SQLite NET 3rd party libreria.

La maggior parte del codice in questa sezione è completamente multipiattaforma e verrà eseguiti su iOS o Android senza modifiche. Esistono due applicazioni di esempio illustrate:

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) &ndash; operazioni sui dati semplici scrive i risultati in un testo di visualizzano controllo.

-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) &ndash; integra le operazioni sui dati in una piccola applicazione funzionante che elenca e modifica di una struttura di dati semplice.

Entrambe le soluzioni di esempio contengono progetti di applicazione di esempio di Android e iOS.

Per le applicazioni di xamarin. Forms, leggere [si lavora con database](~/xamarin-forms/app-fundamentals/databases.md) che spiega come usare SQLite in una libreria di libreria di classi Portabile. Forms.

Negli argomenti in questa sezione viene illustrato l'accesso ai dati in xamarin utilizzando SQLite come il motore di database. Il database è accessibile "direttamente" utilizzando la sintassi ADO.NET oppure è possibile includere il ORM SQLite.NET ed eseguire operazioni sui dati in c#.

Vengono esaminati due esempi: uno che contiene codice di accesso ai dati molto semplice che di output per un campo di testo e un'applicazione semplice che include creazione, lettura, aggiornamento ed eliminazione di funzionalità. Viene descritta anche threading e la modalità di inizializzazione dell'applicazione con un database SQLite pre-popolato.

Per ulteriori esempi di accesso ai dati di più piattaforme, vedere il nostro [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) case study di.


## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzate (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recipe dati Android](https://developer.xamarin.com/recipes/android/data/)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
