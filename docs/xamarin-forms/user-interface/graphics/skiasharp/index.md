---
title: Utilizzo di SkiaSharp in xamarin. Forms
description: Utilizzare SkiaSharp per grafici 2D nelle applicazioni xamarin. Forms
ms.topic: article
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 09/11/2017
ms.openlocfilehash: a49c442fcce31fb6b853359ddfafc9a43d0a2114
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="using-skiasharp-in-xamarinforms"></a>Utilizzo di SkiaSharp in xamarin. Forms

_Utilizzare SkiaSharp per grafici 2D nelle applicazioni xamarin. Forms_

SkiaSharp è un sistema di grafica 2D per .NET e c# supportata dal motore di grafica Skia open source che viene spesso utilizzato nei prodotti Google. È possibile utilizzare SkiaSharp nelle applicazioni xamarin. Forms per disegnare il testo, bitmap e la grafica vettoriale 2D. Vedere il [disegno 2D](~/graphics-games/skiasharp/index.md) Guida per ulteriori informazioni sulla libreria di SkiaSharp e alcune altre esercitazioni.

Questa guida presuppone che si ha familiarità con la programmazione di xamarin. Forms.

## <a name="webinar"></a>Webinar

[![](images/skiasharpwebinarscreen.png "SkiaSharp per xamarin. Forms Webinar")](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)  
[Guarda il webinar "SkiaSharp per xamarin. Forms"](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)

## <a name="skiasharp-preliminaries"></a>Operazioni preliminari SkiaSharp

SkiaSharp per xamarin. Forms viene fornito come un pacchetto NuGet. Dopo aver creato una soluzione xamarin. Forms in Visual Studio o Visual Studio per Mac, è possibile utilizzare Gestione pacchetti NuGet per la ricerca di **SkiaSharp.Views.Forms** pacchetto e aggiungerlo alla soluzione. Se si seleziona il **riferimenti** sezione di ogni progetto dopo l'aggiunta di SkiaSharp, è possibile vedere che vari **SkiaSharp** librerie sono stati aggiunti a ognuno dei progetti nella soluzione.

Se l'applicazione di xamarin. Forms è destinato a iOS, è possibile utilizzare la pagina delle proprietà di progetto per modificare la destinazione di distribuzione minima a iOS 8.0.

In qualsiasi pagina di c# che utilizza SkiaSharp è opportuno includere un `using` direttiva per il [ `SkiaSharp` ](https://developer.xamarin.com/api/namespace/SkiaSharp/) spazio dei nomi, che comprende tutte le SkiaSharp classi, strutture ed enumerazioni che verranno utilizzati negli elementi grafici programmazione. Sarà inoltre necessario un `using` direttiva per il [ `SkiaSharp.Views.Forms` ](https://developer.xamarin.com/api/namespace/SkiaSharp.Views.Forms/) dello spazio dei nomi per le classi specifiche di xamarin. Forms. Si tratta di una quantità minore dello spazio dei nomi, con la classe più importante è [ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/). Questa classe deriva da di xamarin. Forms `View` classe e ospita l'output grafico SkiaSharp.

> [!IMPORTANT]
> Il `SkiaSharp.Views.Forms` dello spazio dei nomi contiene inoltre un `SKGLView` classe che deriva da `View` ma utilizza OpenGL per il rendering della grafica. Per motivi di semplicità, in questa guida si limita a `SKCanvasView`, ma tramite `SKGLView` invece è piuttosto simile.

## <a name="skiasharp-drawing-basicsbasicsindexmd"></a>[Nozioni di base sul disegno con SkiaSharp](basics/index.md)

Alcune delle figure di grafica più semplice, che è possibile disegnare con SkiaSharp sono rettangoli, cerchi e ovali. In visualizzazione di queste cifre, si apprenderà sulle coordinate SkiaSharp, dimensioni e colori.

## <a name="skiasharp-lines-and-pathspathsindexmd"></a>[Linee e tracciati in SkiaSharp](paths/index.md)

Il percorso di un grafico è una serie di semplici linee e curve collegate. Percorsi possono essere tracciati, compilato, o entrambi. In questo argomento include molti aspetti di linee, inclusi tratto termina e join e tratteggiato e linee punteggiate, ma evita di geometrie curva.

## <a name="skiasharp-transformstransformsindexmd"></a>[Trasformazioni di SkiaSharp](transforms/index.md)

Trasformazioni consentono agli oggetti di grafica in modo uniforme convertito, ridimensionata, ruotata o asimmetrica. Questo articolo illustra inoltre come è possibile utilizzare una matrice di trasformazione di 3 per 3 standard per la creazione di trasformazioni non affini e l'applicazione di trasformazioni ai percorsi.

## <a name="skiasharp-curves-and-pathscurvesindexmd"></a>[Curve e tracciati in SkiaSharp](curves/index.md)

L'esplorazione dei percorsi continua con l'aggiunta di curve agli oggetti percorso e altre funzionalità di percorso potente che può essere sfruttata. Si noterà come è possibile specificare un percorso completo in una stringa di testo breve, come utilizzare gli effetti di percorso e come esaminare sui meccanismi interni di percorso.


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
- [SkiaSharp con xamarin. Forms Webinar (video)](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)
