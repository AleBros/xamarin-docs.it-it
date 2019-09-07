---
title: Accesso ai dati di Novell. Android
description: Per la maggior parte delle applicazioni è necessario salvare i dati sul dispositivo localmente. A meno che la quantità di dati non sia leggermente ridotta, questo in genere richiede un database e un livello dati nell'applicazione per gestire l'accesso al database.  Android dispone del motore di database SQLite "built in" e l'accesso per archiviare e recuperare i dati è semplificato dalla piattaforma Novell. Questo documento illustra come accedere a un database SQLite in modo multipiattaforma.
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 2343603199661ea39b1f0af172ce0ccf48a2cd66
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754579"
---
# <a name="xamarinandroid-data-access"></a>Accesso ai dati di Novell. Android

_Per la maggior parte delle applicazioni è necessario salvare i dati sul dispositivo localmente. A meno che la quantità di dati non sia leggermente ridotta, questo in genere richiede un database e un livello dati nell'applicazione per gestire l'accesso al database.  Android dispone del motore di database SQLite "built in" e l'accesso per archiviare e recuperare i dati è semplificato dalla piattaforma Novell. Questo documento illustra come accedere a un database SQLite in modo multipiattaforma._

## <a name="data-access-overview"></a>Panoramica dell'accesso ai dati

Per la maggior parte delle applicazioni è necessario salvare i dati sul dispositivo localmente. A meno che la quantità di dati non sia leggermente ridotta, questo in genere richiede un database e un livello dati nell'applicazione per gestire l'accesso al database. Android dispone del motore di database SQLite "built in" e l'accesso ai dati viene semplificato dalla piattaforma Novell, che viene fornita con la provider di dati SQLite.

Novell. Android supporta le API di accesso al database, ad esempio:

- ADO.NET Framework.
- Libreria di terze parti SQLite-NET.

La maggior parte del codice in questa sezione è completamente multipiattaforma e verrà eseguita in iOS o Android senza modifiche. Sono illustrate due app di esempio:

- [**DataAccess_Basic**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) &ndash; Operazioni dati semplici scrive i risultati in un controllo di visualizzazione del testo;

- [**DataAccess_Advanced**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) &ndash; Integra le operazioni dati in una piccola applicazione funzionante che elenca e modifica una struttura di dati semplice.

Entrambe le soluzioni di esempio contengono progetti di applicazioni di esempio iOS e Android.

Per le applicazioni Novell. Forms, vedere [uso dei database](~/xamarin-forms/data-cloud/data/databases.md) che spiega come usare SQLite in una libreria PCL con Novell. Forms.

Gli argomenti di questa sezione illustrano l'accesso ai dati in Novell. Android usando SQLite come motore di database. È possibile accedere direttamente al database usando la sintassi ADO.NET oppure è possibile includere l'ORM SQLite.NET ed eseguire operazioni sui dati in C#.

Vengono esaminati due esempi: uno contenente codice di accesso ai dati molto semplice che restituisce un campo di testo e una semplice applicazione che include funzionalità di creazione, lettura, aggiornamento ed eliminazione. Viene anche illustrato il threading e come inizializzare l'applicazione con un database SQLite pre-popolato.

Per altri esempi di accesso ai dati multipiattaforma, vedere la pagina relativa a Microsoft [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) case study.

## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Ricette per i dati Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Accesso ai dati di Novell. Forms](~/xamarin-forms/data-cloud/data/databases.md)
