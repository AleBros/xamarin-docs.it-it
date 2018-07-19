---
title: Uso di SkiaSharp in xamarin. Forms
description: SkiaSharp è un sistema di grafica 2D per .NET e c# con tecnologia il motore della grafica Skia open source che viene ampiamente usato nei prodotti di Google. Questa guida illustra come usare SkiaSharp per la grafica 2D nelle applicazioni xamarin. Forms.
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 09/11/2017
ms.openlocfilehash: bdc0603c6ae406adac42533e251106ccccf2cb7c
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130946"
---
# <a name="using-skiasharp-in-xamarinforms"></a>Uso di SkiaSharp in xamarin. Forms

_Uso di SkiaSharp per la grafica 2D nelle applicazioni xamarin. Forms_

SkiaSharp è un sistema di grafica 2D per .NET e c# con tecnologia il motore della grafica Skia open source che viene ampiamente usato nei prodotti di Google. È possibile usare SkiaSharp nelle applicazioni xamarin. Forms per disegnare il testo, bitmap e la grafica vettoriale 2D. Vedere le [disegno 2D](~/graphics-games/skiasharp/index.md) Guida per informazioni più generali sulla libreria di SkiaSharp e alcune altre esercitazioni.

Questa guida si presuppone che abbia familiarità con la programmazione di xamarin. Forms.

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**Webinar: SkiaSharp per xamarin. Forms**

## <a name="skiasharp-preliminaries"></a>Operazioni preliminari di SkiaSharp

SkiaSharp per xamarin. Forms viene fornito come un pacchetto NuGet. Dopo aver creato una soluzione xamarin. Forms in Visual Studio o Visual Studio per Mac, è possibile usare Gestione pacchetti NuGet cercare il **SkiaSharp.Views.Forms** del pacchetto e aggiungerlo alla soluzione. Se si seleziona il **riferimenti** sezione di ogni progetto dopo l'aggiunta di SkiaSharp, è possibile vedere che vari **SkiaSharp** librerie sono stati aggiunti a ogni progetto nella soluzione.

Se l'applicazione xamarin. Forms è destinata a iOS, usare la pagina delle proprietà di progetto per modificare la destinazione di distribuzione minima a iOS 8.0.

In qualsiasi pagina di c# che utilizza SkiaSharp è opportuno includere una `using` direttiva per il [ `SkiaSharp` ](https://developer.xamarin.com/api/namespace/SkiaSharp/) dello spazio dei nomi, che comprenda tutti di SkiaSharp classi, strutture ed enumerazioni che verrà usato negli elementi grafici la programmazione. Sarà inoltre necessario un `using` direttiva per il [ `SkiaSharp.Views.Forms` ](https://developer.xamarin.com/api/namespace/SkiaSharp.Views.Forms/) dello spazio dei nomi per le classi specifiche di xamarin. Forms. Si tratta di una quantità minore dello spazio dei nomi, con la classe più importante in fase [ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/). Questa classe deriva da xamarin. Forms `View` classe e ospita l'output dei grafici SkiaSharp.

> [!IMPORTANT]
> Il `SkiaSharp.Views.Forms` dello spazio dei nomi contiene inoltre un' `SKGLView` classe che deriva da `View` ma Usa OpenGL per il rendering grafica. Per motivi di semplicità, questa guida si limita a `SKCanvasView`, ma tramite `SKGLView` invece è abbastanza simile.

## <a name="skiasharp-drawing-basicsbasicsindexmd"></a>[Nozioni di base sul disegno con SkiaSharp](basics/index.md)

Alcune delle figure di grafica più semplice, che è possibile disegnare con SkiaSharp sono cerchi, ovali e rettangoli. In visualizzazione di queste cifre, si apprenderà sulle coordinate SkiaSharp, dimensioni e colori.

## <a name="skiasharp-lines-and-pathspathsindexmd"></a>[Linee e tracciati in SkiaSharp](paths/index.md)

Un percorso della grafica è una serie di curve e linee rette collegate. I percorsi possono essere tracciati, riempita, o entrambi. In questo argomento comprende molti aspetti del disegno di linee, tra cui tratto terminazioni e giunzioni e linee tratteggiate e linee tratteggiate, ma si interrompe le geometrie curva.

## <a name="skiasharp-transformstransformsindexmd"></a>[Trasformazioni di SkiaSharp](transforms/index.md)

Trasformazioni consentono agli oggetti di grafica in modo uniforme convertito, ridimensionata, ruotata o asimmetrici. Questo articolo illustra inoltre come è possibile usare una matrice di trasformazione 3 per 3 standard per la creazione di trasformazioni non affini e applicando trasformazioni a percorsi.

## <a name="skiasharp-curves-and-pathscurvesindexmd"></a>[Curve e tracciati in SkiaSharp](curves/index.md)

L'esplorazione dei percorsi continua con l'aggiunta di curve agli oggetti percorso e sfruttare altre funzionalità potenti percorso. Si noterà come è possibile specificare un percorso completo nella stringa di testo conciso, come utilizzare effetti per il tracciato e come approfondire meccanismi interni di percorso.

## <a name="skiasharp-bitmapsbitmapsindexmd"></a>[Bitmap di SkiaSharp](bitmaps/index.md)

Le bitmap sono matrici rettangolari di bit corrispondente per il pixel di un dispositivo di visualizzazione. Questa serie di articoli illustra come caricare, salvare, visualizzare, creare, disegnare sul, aggiungere un'animazione e accedere i bit di SkiaSharp bitmap.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [SkiaSharp webinar su xamarin. Forms (video)](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)
