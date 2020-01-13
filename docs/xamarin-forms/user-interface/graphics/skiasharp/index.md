---
title: Grafica SkiaSharp in Xamarin.Forms
description: SkiaSharp è un sistema di grafica 2D per .NET e c# con tecnologia il motore della grafica Skia open source che viene ampiamente usato nei prodotti di Google. Questa guida illustra come usare SkiaSharp per la grafica 2D nelle applicazioni Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 6b85cdcd92c4680ced9f75d7b8c5a69c9512d6c4
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656141"
---
# <a name="skiasharp-graphics-in-xamarinforms"></a>Grafica SkiaSharp in Xamarin.Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Uso di SkiaSharp per la grafica 2D nelle applicazioni Xamarin.Forms_

SkiaSharp è un sistema di grafica 2D per .NET e c# con tecnologia il motore della grafica Skia open source che viene ampiamente usato nei prodotti di Google. È possibile usare SkiaSharp nelle applicazioni Xamarin.Forms per disegnare il testo, bitmap e la grafica vettoriale 2D. Vedere le [disegno 2D](~/graphics-games/skiasharp/index.md) Guida per informazioni più generali sulla libreria di SkiaSharp e alcune altre esercitazioni.

Questa guida si presuppone che abbia familiarità con la programmazione di Xamarin.Forms.

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**Webinar SkiaSharp per Xamarin.Forms**

## <a name="skiasharp-preliminaries"></a>Operazioni preliminari di SkiaSharp

SkiaSharp per Xamarin.Forms viene fornito come un pacchetto NuGet. Dopo aver creato una soluzione Xamarin.Forms in Visual Studio o Visual Studio per Mac, è possibile usare Gestione pacchetti NuGet cercare il **SkiaSharp.Views.Forms** del pacchetto e aggiungerlo alla soluzione. Se si seleziona il **riferimenti** sezione di ogni progetto dopo l'aggiunta di SkiaSharp, è possibile vedere che vari **SkiaSharp** librerie sono stati aggiunti a ogni progetto nella soluzione.

Se l'applicazione Xamarin.Forms è destinata a iOS, usare la pagina delle proprietà di progetto per modificare la destinazione di distribuzione minima a iOS 8.0.

In qualsiasi pagina di c# che utilizza SkiaSharp è opportuno includere una `using` direttiva per il [ `SkiaSharp` ](xref:SkiaSharp) dello spazio dei nomi, che comprenda tutti di SkiaSharp classi, strutture ed enumerazioni che verrà usato negli elementi grafici la programmazione. Sarà inoltre necessario un `using` direttiva per il [ `SkiaSharp.Views.Forms` ](xref:SkiaSharp.Views.Forms) dello spazio dei nomi per le classi specifiche di Xamarin.Forms. Si tratta di una quantità minore dello spazio dei nomi, con la classe più importante in fase [ `SKCanvasView` ](xref:SkiaSharp.Views.Forms.SKCanvasView). Questa classe deriva da Xamarin.Forms `View` classe e ospita l'output dei grafici SkiaSharp.

> [!IMPORTANT]
> Il `SkiaSharp.Views.Forms` dello spazio dei nomi contiene inoltre un' `SKGLView` classe che deriva da `View` ma Usa OpenGL per il rendering grafica. Per motivi di semplicità, questa guida si limita a `SKCanvasView`, ma tramite `SKGLView` invece è abbastanza simile.

## <a name="skiasharp-drawing-basicsbasicsindexmd"></a>[Nozioni di base sul disegno con SkiaSharp](basics/index.md)

Alcune delle figure di grafica più semplice, che è possibile disegnare con SkiaSharp sono cerchi, ovali e rettangoli. In visualizzazione di queste cifre, si apprenderà sulle coordinate SkiaSharp, dimensioni e colori. La visualizzazione di testo e bitmap è più complessa, ma questi articoli introducono anche queste tecniche.

## <a name="skiasharp-lines-and-pathspathsindexmd"></a>[Linee e tracciati in SkiaSharp](paths/index.md)

Un percorso della grafica è una serie di curve e linee rette collegate. I percorsi possono essere tracciati, riempita, o entrambi. Questo articolo comprende molti aspetti del disegno di linee, tra cui tratto terminazioni e giunzioni e linee tratteggiate e linee tratteggiate, ma si interrompe le geometrie curva.

## <a name="skiasharp-transformstransformsindexmd"></a>[Trasformazioni di SkiaSharp](transforms/index.md)

Trasformazioni consentono agli oggetti di grafica in modo uniforme convertito, ridimensionata, ruotata o asimmetrici. Questo articolo illustra inoltre come è possibile usare una matrice di trasformazione 3 per 3 standard per la creazione di trasformazioni non affini e applicando trasformazioni a percorsi.

## <a name="skiasharp-curves-and-pathscurvesindexmd"></a>[Curve e tracciati in SkiaSharp](curves/index.md)

L'esplorazione dei percorsi continua con l'aggiunta di curve agli oggetti percorso e sfruttare altre funzionalità potenti percorso. Si noterà come è possibile specificare un percorso completo nella stringa di testo conciso, come utilizzare effetti per il tracciato e come approfondire meccanismi interni di percorso.

## <a name="skiasharp-bitmapsbitmapsindexmd"></a>[Bitmap SkiaSharp](bitmaps/index.md)

Le bitmap sono matrici rettangolari di bit corrispondente per il pixel di un dispositivo di visualizzazione. Questa serie di articoli illustra come caricare, salvare, visualizzare, creare, disegnare sul, aggiungere un'animazione e accedere i bit di SkiaSharp bitmap.

## <a name="skiasharp-effectseffectsindexmd"></a>[Effetti di SkiaSharp](effects/index.md)

Gli effetti sono proprietà modificare la visualizzazione di grafica, incluse le sfumature lineari e circolari normal, bitmap per l'affiancamento, blend modalità, blur e ad altri utenti.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SkiaSharp webinar su Xamarin.Forms (video)](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)
