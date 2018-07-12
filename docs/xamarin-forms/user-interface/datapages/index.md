---
title: Xamarin. Forms DataPages
description: Questo articolo presenta xamarin. Forms DataPages, che forniscono un'API per rapidamente e facilmente associare un'origine dati per le viste predefinite.
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 2a74b636a41a72b26776157a774f0a33ef45a075
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815887"
---
# <a name="xamarinforms-datapages"></a>Xamarin. Forms DataPages

![](~/media/shared/preview.png "Questa API è attualmente in anteprima")

> [!IMPORTANT]
> DataPages richiede un [tema di xamarin. Forms](~/xamarin-forms/user-interface/themes/index.md) riferimento per il rendering.

Xamarin. Forms DataPages vengono annunciati in occasione di variare delle 2016 e sono disponibili come anteprima ai clienti di provare e inviare commenti e suggerimenti.

DataPages forniscono un'API per rapidamente e facilmente associare un'origine dati per le viste predefinite. Gli elementi dell'elenco e pagine dettagli verranno automaticamente eseguito il rendering dei dati e possono essere personalizzate usando i temi.

Per vedere come funziona la demo di //build variare delle, consultare il [Guida introduttiva](get-started.md).

[![](images/demo-sml.png "Applicazione di esempio DataPages")](images/demo.png#lightbox "applicazione di esempio DataPages")

## <a name="introduction"></a>Introduzione

Origini dati e le pagine di dati associati consentono agli sviluppatori di semplice e rapido utilizzare un'origine dati supportata ed eseguire il rendering tramite predefinite dell'interfaccia utente lo scaffolding che può essere personalizzati con i temi.

DataPages vengono aggiunti a un'applicazione xamarin. Forms, includendo il **Xamarin.Forms.Pages** pacchetto Nuget.

### <a name="data-sources"></a>Data Sources

L'anteprima presenta alcune origini dati predefinite disponibili per l'uso:

* **JsonDataSource**
* **AzureDataSource** (separare Nuget)
* **AzureEasyTableDataSource** (separare Nuget)

Vedere le [Guida introduttiva](get-started.md) per indicazioni su come usare un `JsonDataSource`.


### <a name="pages--controls"></a>Le pagine e controlli

Le seguenti pagine e controlli sono inclusi per consentire l'associazione semplice per le origini di dati specificato:

* **ListDataPage** : vedere la [introduttiva esempio](get-started.md).
* **DirectoryPage** : un elenco con raggruppamento abilitato.
* **PersonDetailPage** : un singolo dato elemento visualizzazione personalizzata per un tipo di oggetto specifico (una voce relativa al contatto).
* **DataView** – una vista per esporre i dati dall'origine in modo generico.
* **Widget CardView** : nello stile di visualizzazione che contiene un'immagine, testo del titolo e testo della descrizione.
* **HeroImage** – una visualizzazione di rendering delle immagini.
* **ListItem** : un pre-compilato visualizzazione con un layout simile per gli elementi dell'elenco di Android e iOS nativo.

Vedere le [riferimento per i controlli DataPages](controls.md) per alcuni esempi.



### <a name="under-the-hood"></a>Dietro le quinte

Un'origine dati di xamarin. Forms rispetti il `IDataSource` interfaccia.

L'infrastruttura di xamarin. Forms interagisce con un'origine dati mediante le proprietà seguenti:

* `Data` -un elenco di sola lettura degli elementi di dati che possono essere visualizzati.
* `IsLoading` : valore booleano che indica se i dati sono caricati e disponibili per il rendering.
* `[key]` -un indicizzatore per recuperare gli elementi.

Esistono due metodi `MaskKey` e `UnmaskKey` che può essere utilizzato per nascondere proprietà elemento dati (ad esempio, (o mostrare) impedire loro il rendering).
La chiave corrisponde a di una proprietà denominata per l'oggetto di elemento di dati.
