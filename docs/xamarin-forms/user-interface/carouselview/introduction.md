---
title: Xamarin.FormsIntroduzione a CarouselView
description: CarouselView è una vista per la presentazione dei dati in un layout scorrevole, in cui gli utenti possono scorrere rapidamente una raccolta di elementi.
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2e67acd0188e1147481005502ad9ccdaada645d9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84140270"
---
# <a name="xamarinforms-carouselview-introduction"></a>Xamarin.FormsIntroduzione a CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)è una visualizzazione per la presentazione dei dati in un layout scorrevole, in cui gli utenti possono scorrere rapidamente una raccolta di elementi. Per impostazione predefinita, visualizzerà `CarouselView` gli elementi in un orientamento orizzontale. Sullo schermo verrà visualizzato un singolo elemento, con movimenti di scorrimento che comportano la navigazione in avanti e indietro nella raccolta di elementi. Inoltre, è possibile visualizzare gli indicatori che rappresentano ogni elemento in `CarouselView` :

[![Screenshot di CarouselView e IndicatorView, su iOS e Android](populate-data-images/indicators.png "Cerchi IndicatorView")](populate-data-images/indicators-large.png#lightbox "Cerchi IndicatorView")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)è disponibile in Xamarin.Forms 4,3. Tuttavia, è attualmente sperimentale e può essere usata solo aggiungendo la seguente riga di codice alla `AppDelegate` classe in iOS o alla `MainActivity` classe in Android, prima di chiamare `Forms.Init` :

```csharp
Forms.SetFlags("CarouselView_Experimental");
```

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)è disponibile in iOS e Android, ma alcune funzionalità possono essere parzialmente disponibili solo in piattaforma UWP (Universal Windows Platform).

[`CarouselView`](xref:Xamarin.Forms.CarouselView)condivide gran parte dell'implementazione con [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Tuttavia, i due controlli hanno diversi casi d'uso. `CollectionView`viene in genere usato per presentare elenchi di dati di qualsiasi lunghezza, mentre `CarouselView` viene in genere usato per evidenziare le informazioni in un elenco di lunghezza limitata.
