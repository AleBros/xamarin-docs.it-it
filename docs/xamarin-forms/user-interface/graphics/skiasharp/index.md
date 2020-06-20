---
title: SkiaSharp grafica inXamarin.Forms
description: SkiaSharp è un sistema di grafica 2D per .NET e C# basato sul motore di grafica Skia open source, ampiamente usato nei prodotti Google. Questa guida illustra come usare SkiaSharp per la grafica 2D nelle Xamarin.Forms applicazioni.
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 855bd0d357950b019487b3ea05e379915f54b9d4
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84127634"
---
# <a name="skiasharp-graphics-in-xamarinforms"></a>SkiaSharp grafica inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Usare SkiaSharp per la grafica 2D nelle Xamarin.Forms applicazioni_

SkiaSharp è un sistema di grafica 2D per .NET e C# basato sul motore di grafica Skia open source, ampiamente usato nei prodotti Google. È possibile usare SkiaSharp nelle Xamarin.Forms applicazioni per creare grafica vettoriale 2D, bitmap e testo. Per informazioni più generali sulla libreria SkiaSharp e altre esercitazioni, vedere la Guida di [disegno 2D](~/graphics-games/skiasharp/index.md) .

In questa guida si presuppone che l'utente abbia familiarità con la Xamarin.Forms programmazione.

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**Webinar: SkiaSharp perXamarin.Forms**

## <a name="skiasharp-preliminaries"></a>SkiaSharp preliminari

SkiaSharp per Xamarin.Forms è incluso in un pacchetto NuGet. Dopo aver creato una Xamarin.Forms soluzione in Visual Studio o Visual Studio per Mac, è possibile usare Gestione pacchetti NuGet per cercare il pacchetto **SkiaSharp. views. Forms** e aggiungerlo alla soluzione. Se si seleziona la sezione **riferimenti** di ogni progetto dopo l'aggiunta di SkiaSharp, è possibile notare che sono state aggiunte varie librerie **SkiaSharp** a ognuno dei progetti nella soluzione.

Se l' Xamarin.Forms applicazione è destinata a iOS, usare la pagina delle proprietà del progetto per modificare la destinazione di distribuzione minima in ios 8,0.

In qualsiasi pagina C# che usa SkiaSharp è opportuno includere una `using` direttiva per lo [`SkiaSharp`](xref:SkiaSharp) spazio dei nomi, che include tutte le classi, le strutture e le enumerazioni di SkiaSharp che verranno usate nella programmazione grafica. Si desidera inoltre una `using` direttiva per lo [`SkiaSharp.Views.Forms`](xref:SkiaSharp.Views.Forms) spazio dei nomi per le classi specifiche di Xamarin.Forms . Si tratta di uno spazio dei nomi molto più piccolo, con la classe più importante [`SKCanvasView`](xref:SkiaSharp.Views.Forms.SKCanvasView) . Questa classe deriva dalla Xamarin.Forms `View` classe e ospita l'output della grafica SkiaSharp.

> [!IMPORTANT]
> Lo `SkiaSharp.Views.Forms` spazio dei nomi contiene anche una `SKGLView` classe che deriva da `View` ma usa OpenGL per il rendering della grafica. Per motivi di semplicità, questa guida si limita a `SKCanvasView` , ma l'uso di `SKGLView` è piuttosto simile.

## <a name="skiasharp-drawing-basics"></a>[Nozioni di base sul disegno con SkiaSharp](basics/index.md)

Alcune delle figure grafiche più semplici che è possibile creare con SkiaSharp sono cerchi, ovali e rettangoli. Nella visualizzazione di queste figure verranno fornite informazioni sulle coordinate, le dimensioni e i colori di SkiaSharp. La visualizzazione di testo e bitmap è più complessa, ma questi articoli introducono anche tali tecniche.

## <a name="skiasharp-lines-and-paths"></a>[Linee e tracciati in SkiaSharp](paths/index.md)

Un percorso grafico è una serie di linee rette e curve collegate. I percorsi possono essere tracciati, compilati o entrambi. Questo articolo comprende molti aspetti del disegno a linee, tra cui estremità di tratto e join, linee tratteggiate e tratteggiate, ma si interrompe a corto di geometrie di curva.

## <a name="skiasharp-transforms"></a>[Trasformazioni di SkiaSharp](transforms/index.md)

Le trasformazioni consentono agli oggetti grafici di essere convertiti in modo uniforme, ridimensionati, ruotati o inclinati. Questo articolo illustra anche come è possibile usare una matrice di trasformazione standard 3 per 3 per la creazione di trasformazioni non affini e l'applicazione di trasformazioni ai percorsi.

## <a name="skiasharp-curves-and-paths"></a>[Curve e tracciati in SkiaSharp](curves/index.md)

L'esplorazione dei percorsi continua con l'aggiunta di curve a oggetti Path e l'utilizzo di altre potenti funzionalità di percorso. Si vedrà come specificare un percorso intero in una stringa di testo concisa, come usare gli effetti del percorso e come esaminare gli elementi interni del percorso.

## <a name="skiasharp-bitmaps"></a>[Bitmap SkiaSharp](bitmaps/index.md)

Le bitmap sono matrici rettangolari di bit corrispondenti ai pixel di un dispositivo di visualizzazione. Questa serie di articoli illustra come caricare, salvare, visualizzare, creare, elaborare, animare e accedere ai bit di bitmap SkiaSharp.

## <a name="skiasharp-effects"></a>[Effetti SkiaSharp](effects/index.md)

Gli effetti sono proprietà che modificano la normale visualizzazione della grafica, incluse le sfumature lineari e circolari, la piastrellatura bitmap, le modalità di Blend, la sfocatura e altri.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SkiaSharp con Xamarin.Forms webinar (video)](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)
