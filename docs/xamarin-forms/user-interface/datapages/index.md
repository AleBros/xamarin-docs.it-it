---
title: Pagine Web di Novell. Forms
description: Questo articolo presenta le pagine dei dati di Novell. Forms, che forniscono un'API per associare in modo rapido e semplice un'origine dati alle visualizzazioni predefinite.
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 1dc62066b71842e1d3b07495912fa35a549c0f1e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759676"
---
# <a name="xamarinforms-datapages"></a>Pagine Web di Novell. Forms

![](~/media/shared/preview.png "Questa API è attualmente in anteprima")

> [!IMPORTANT]
> Per il rendering delle pagine Web è necessario un riferimento al tema Novell. Forms. Ciò comporta l'installazione del pacchetto NuGet [Novell. Forms. Theme. base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) nel progetto, seguito dai pacchetti NuGet [Novell. Forms. Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) o [Novell. Forms. Theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

Le pagine Web di Novell. Forms sono state annunciate a evolve 2016 e sono disponibili come anteprima per consentire ai clienti di provare e fornire commenti e suggerimenti.

DataPages forniscono un'API per rapidamente e facilmente associare un'origine dati per le viste predefinite. Gli elementi dell'elenco e le pagine di dettaglio eseguiranno automaticamente il rendering dei dati e possono essere personalizzati mediante temi.

Per informazioni su come funziona la demo introduttiva di evolve, vedere la [Guida introduttiva](get-started.md).

[![](images/demo-sml.png "Applicazione di esempio DataPages")](images/demo.png#lightbox "applicazione di esempio DataPages")

## <a name="introduction"></a>Introduzione

Le origini dati e le pagine di dati associate consentono agli sviluppatori di utilizzare in modo rapido e semplice un'origine dati supportata e di eseguirne il rendering utilizzando il ponteggi incorporato dell'interfaccia utente che può essere personalizzato con i temi.

Le pagine Web vengono aggiunte a un'applicazione Novell. Forms includendo il pacchetto NuGet **Novell. Forms. Pages** .

### <a name="data-sources"></a>Data Sources

L'anteprima include alcune origini dati predefinite disponibili per l'uso:

* **JsonDataSource**
* **AzureDataSource** (NuGet separato)
* **AzureEasyTableDataSource** (NuGet separato)

Vedere la [Guida introduttiva](get-started.md) per un esempio di utilizzo `JsonDataSource`di.

### <a name="pages--controls"></a>Pagine & controlli

Le pagine e i controlli seguenti sono inclusi per consentire un facile binding alle origini dati fornite:

* **ListDataPage** : vedere l' [esempio](get-started.md)introduttivo.
* **DirectoryPage** : elenco con raggruppamento abilitato.
* **PersonDetailPage** : una singola visualizzazione di elementi di dati personalizzata per un tipo di oggetto specifico (voce di contatto).
* **DataView** : visualizzazione che consente di esporre i dati dall'origine in modo generico.
* **CardView** : visualizzazione con stile che contiene un'immagine, un testo del titolo e un testo della descrizione.
* **HeroImage** : visualizzazione del rendering di un'immagine.
* **ListItem** : una visualizzazione predefinita con un layout simile agli elementi di elenco nativi iOS e Android.

Per esempi, vedere la Guida di [riferimento ai controlli DataPages](controls.md) .

### <a name="under-the-hood"></a>Dietro le quinte

Un'origine dati Novell. Forms aderisce all' `IDataSource` interfaccia.

L'infrastruttura Novell. Forms interagisce con un'origine dati tramite le proprietà seguenti:

* `Data`: elenco di sola lettura degli elementi di dati che è possibile visualizzare.
* `IsLoading`: valore booleano che indica se i dati sono caricati e disponibili per il rendering.
* `[key]`: un indicizzatore per recuperare gli elementi.

Esistono due metodi `MaskKey` `UnmaskKey` che possono essere usati per nascondere o visualizzare le proprietà degli elementi di dati (ad esempio, impedire il rendering di tali elementi.
La chiave corrisponde a una proprietà denominata nell'oggetto elemento dati.
