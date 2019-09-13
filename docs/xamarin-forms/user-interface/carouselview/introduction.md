---
title: Introduzione a Novell. Forms CarouselView
description: CarouselView è una vista per la presentazione dei dati in un layout simile a carosello.
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 244582d579780a962dfcb25c1a081d768b42acd3
ms.sourcegitcommit: e83035c746f165ee6d03f2e9fd0066ee4f20a9fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70908289"
---
# <a name="xamarinforms-carouselview-introduction"></a>Introduzione a Novell. Forms CarouselView

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)è una vista per la presentazione di informazioni in un modo simile a un carosello.


[`CarouselView`](xref:Xamarin.Forms.CarouselView)è disponibile in Novell. Forms 4,3. Tuttavia, è attualmente sperimentale e può essere usata solo aggiungendo la seguente riga di codice alla `AppDelegate` classe in iOS o `MainActivity` alla classe in Android, prima di chiamare `Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

_Nota: Al momento della stesura di questo articolo, CarouselView usa ancora il flag CollectionView per abilitarne la funzionalità._

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)è disponibile in iOS e Android, ma alcune funzionalità possono essere parzialmente disponibili solo in piattaforma UWP (Universal Windows Platform).

## <a name="when-to-use-carouselview"></a>Quando usare CarouselView

Sebbene CarouselView basi la maggior parte dell'implementazione di CollectionView, il suo scopo è più mirato. Sebbene l'uso di CollectionView e CarouselView sia a propria discrezione, le giostre nelle app vengono in genere usate per evidenziare le informazioni e sono limitate nel numero totale di elementi usati.
