---
title: Introduzione a Novell. Forms CarouselView
description: CarouselView è una vista per la presentazione dei dati in un layout scorrevole, in cui gli utenti possono scorrere rapidamente una raccolta di elementi.
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
ms.openlocfilehash: 2fe4d984f36880493a9a04d99b63876551366477
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696978"
---
# <a name="xamarinforms-carouselview-introduction"></a>Introduzione a Novell. Forms CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

[`CarouselView`](xref:Xamarin.Forms.CarouselView) è una vista per la presentazione dei dati in un layout scorrevole, in cui gli utenti possono scorrere rapidamente una raccolta di elementi. Per impostazione predefinita, `CarouselView` visualizzerà gli elementi in un orientamento orizzontale. Sullo schermo verrà visualizzato un singolo elemento, con movimenti di scorrimento che comportano la navigazione in avanti e indietro nella raccolta di elementi.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) è disponibile in Novell. forms 4,3. Tuttavia, è attualmente sperimentale e può essere usata solo aggiungendo la riga di codice seguente alla classe `AppDelegate` in iOS o alla classe `MainActivity` in Android, prima di chiamare `Forms.Init`:

```csharp
Forms.SetFlags("CarouselView_Experimental");
```

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) è disponibile in iOS e Android, ma alcune funzionalità possono essere parzialmente disponibili solo nella piattaforma UWP (Universal Windows Platform).

[`CarouselView`](xref:Xamarin.Forms.CarouselView) condivide gran parte dell'implementazione con [`CollectionView`](xref:Xamarin.Forms.CollectionView). Tuttavia, i due controlli hanno diversi casi d'uso. `CollectionView` viene in genere usato per presentare elenchi di dati di qualsiasi lunghezza, mentre `CarouselView` viene in genere usato per evidenziare le informazioni in un elenco di lunghezza limitata.
