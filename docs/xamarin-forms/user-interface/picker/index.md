---
title: Selezione
description: "La visualizzazione di selezione è un controllo per la selezione di un elemento di testo da un elenco di dati."
ms.topic: article
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: edc724eb73b314c0accd3e8775b9b26b6eac16d9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="picker"></a>Selezione

_La visualizzazione di selezione è un controllo per la selezione di un elemento di testo da un elenco di dati._

Oggetto [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) Visualizza un breve elenco di elementi, da cui l'utente può selezionare. Tuttavia, un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) non Mostra tutti i dati alla prima visualizzazione. Al contrario, il valore della relativa [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Title/) proprietà viene visualizzata come segnaposto nelle piattaforme Android e iOS:

[![](images/picker-initial.png "Selezione visualizzazione iniziale")](images/picker-initial-large.png "selezione visualizzazione iniziale")

Quando il [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) riceve lo stato attivo, i dati viene visualizzato e l'utente può selezionare un elemento:

[![](images/picker-selection.png "Selezione di un elemento di selezione")](images/picker-selection-large.png "selezione selezione di un elemento")

Dopo la selezione, viene visualizzato l'elemento selezionato per il [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/):

![](images/picker-after-selection.png "Selezione dopo la selezione")

Sono disponibili due tecniche per la compilazione di un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) con i dati:

- L'impostazione di [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) proprietà ai dati da visualizzare. Questa è la tecnica consigliata, che è stata introdotta in xamarin. Forms 2.3.4. Per ulteriori informazioni, vedere [impostando proprietà ItemsSource un prelievo](populating-itemssource.md).
- Aggiunta di dati da visualizzare per il [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) insieme. Questa tecnica è il processo originale per la compilazione di un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) con i dati. Per ulteriori informazioni, vedere [dati aggiunta alla raccolta di elementi di un controllo di selezione](populating-items.md).


## <a name="related-links"></a>Collegamenti correlati

- [Selezione](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
