---
title: DataPages
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: e83efcd71eb9427f0b63749e8885a991a206febe
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="datapages"></a>DataPages

![](~/media/shared/preview.png "Questa API è attualmente in anteprima")

> [!IMPORTANT]
> DataPages richiede un [xamarin. Forms tema](~/xamarin-forms/user-interface/themes/index.md) riferimento per il rendering.

Xamarin. Forms DataPages sono stati annunciati in 2016 Evolve e sono disponibili come anteprima ai clienti di provare e fornire commenti e suggerimenti.

DataPages forniscono un'API per rapidamente e facilmente associare un'origine dati per le visualizzazioni predefinite. Gli elementi dell'elenco e pagine dettagli verranno automaticamente eseguito il rendering dei dati e possono essere personalizzate tramite i temi.

Per visualizzarne il funzionamento la demo del discorso Evolve, estrarre il [Guida introduttiva](get-started.md).

[![](images/demo-sml.png "Applicazione di esempio DataPages")](images/demo.png#lightbox "applicazione di esempio DataPages")

## <a name="introduction"></a>Introduzione

Origini dati e le pagine di dati associati consentono agli sviluppatori di semplice e rapido utilizzare un'origine dati supportata ed eseguire il rendering di usando predefinite dell'interfaccia utente di supporto temporaneo che può essere personalizzato con temi.

DataPages vengono aggiunti a un'applicazione di xamarin. Forms includendo il **Xamarin.Forms.Pages** pacchetto Nuget.

### <a name="data-sources"></a>Data Sources

L'anteprima presenta alcune origini dati predefinite disponibili per l'utilizzo:

* **JsonDataSource**
* **AzureDataSource** (separare Nuget)
* **AzureEasyTableDataSource** (separare Nuget)

Vedere il [Guida introduttiva](get-started.md) per un esempio di utilizzo un `JsonDataSource`.


### <a name="pages--controls"></a>Pagine e controlli

Le pagine e controlli seguenti sono inclusi per consentire l'associazione semplice per le origini di dati fornito:

* **ListDataPage** – vedere la [introduzione esempio](get-started.md).
* **DirectoryPage** : un elenco con raggruppamento abilitato.
* **PersonDetailPage** : un elemento di dati singoli visualizzazione personalizzata per un tipo di oggetto specifico (una voce di contatto).
* **DataView** – una vista per esporre i dati dall'origine in modo generico.
* **CardView** : un stile di visualizzazione che contiene un'immagine, il testo del titolo e testo della descrizione.
* **HeroImage** – una vista di rendering delle immagini.
* **ListItem** : una vista con un layout simile native per iOS e Android elementi incorporati.

Vedere il [controlli fanno riferimento a DataPages](controls.md) per gli esempi.



### <a name="under-the-hood"></a>Dietro le quinte

Un'origine dati di xamarin. Forms rispetti la `IDataSource` interfaccia.

L'infrastruttura di xamarin. Forms interagisce con un'origine dati mediante le proprietà seguenti:

* `Data` -un elenco di sola lettura di elementi di dati che possono essere visualizzati.
* `IsLoading` : valore booleano che indica se i dati sono caricati e disponibile per il rendering.
* `[key]` -un indicizzatore per recuperare gli elementi.

Esistono due metodi `MaskKey` e `UnmaskKey` che può essere utilizzato per nascondere le proprietà dell'elemento dati (ovvero (o visualizzare) impedire il rendering).
La chiave corrisponde a di una proprietà denominata per l'oggetto elemento di dati.

