---
title: Introduzione a Novell. Forms CarouselView
description: CarouselView è una vista per la presentazione dei dati in un layout simile a carosello.
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 7979f6ed362c580d9cf80f19b3bc0ea7550ca70c
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70985966"
---
# <a name="xamarinforms-carouselview-introduction"></a>Introduzione a Novell. Forms CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

[`CarouselView`](xref:Xamarin.Forms.CarouselView) è una vista per la presentazione di informazioni in un modo simile a un carosello.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) è disponibile in Novell. forms 4,3. Tuttavia, è attualmente sperimentale e può essere usata solo aggiungendo la riga di codice seguente alla classe `AppDelegate` in iOS o alla classe `MainActivity` in Android, prima di chiamare `Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

_Nota: al momento della stesura di questo articolo, CarouselView usa ancora il flag CollectionView per abilitarne la funzionalità._

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) è disponibile in iOS e Android, ma alcune funzionalità possono essere parzialmente disponibili solo nella piattaforma UWP (Universal Windows Platform).

## <a name="when-to-use-carouselview"></a>Quando usare CarouselView

Sebbene CarouselView basi la maggior parte dell'implementazione di CollectionView, il suo scopo è più mirato. Sebbene l'uso di CollectionView e CarouselView sia a propria discrezione, le giostre nelle app vengono in genere usate per evidenziare le informazioni e sono limitate nel numero totale di elementi usati.
