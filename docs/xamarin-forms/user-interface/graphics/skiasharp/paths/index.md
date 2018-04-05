---
title: I percorsi e le righe SkiaSharp
description: Utilizzare SkiaSharp per disegnare linee e immagini percorsi
ms.prod: xamarin
ms.assetid: 316A15FE-383D-4D06-8641-BAC7EE7474CA
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 2f9941305f165ec04e5fc80e3c41e3150a21a9b7
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2018
---
# <a name="skiasharp-lines-and-paths"></a>I percorsi e le righe SkiaSharp

_Utilizzare SkiaSharp per disegnare linee e immagini percorsi_

Il [precedente sezione](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md) ha dimostrato che la SkiaSharp `SKCanvas` include diversi metodi per disegnare rettangoli, ellissi e rettangoli con angoli arrotondati. Questa sezione e nelle sezioni successive illustrano le varie classi connesse con la creazione e rendering *percorsi grafici*.

Il percorso grafico è l'approccio più generalizzato di disegno di linee e curve in SkiaSharp. In questa sezione viene illustrato l'utilizzo un `SKPath` oggetto per tracciare linee rette e usare una raccolta di linee rette piccoli (chiamato un *polilinea*) per disegnare curve che è possibile definire matematicamente. Una sezione successiva verrà vengono illustrati i vari tipi di curve supportati da `SKPath`.

Tutti i programmi di esempio in questa sezione appaiono sotto l'intestazione **linee e i percorsi** nella home page del [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programma e il [ **Percorsi** ](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths) cartella della soluzione.

## <a name="lines-and-stroke-capslinesmd"></a>[Linee ed estremità dei tratti](lines.md)

Informazioni su come utilizzare SkiaSharp per disegnare linee con estremità diversi.

## <a name="path-basicspathsmd"></a>[Nozioni di base sui tracciati](paths.md)

Esplorare l'oggetto SkiaSharp SKPath per la combinazione di linee e curve.

## <a name="the-path-fill-typesfill-typesmd"></a>[Tipi di riempimento dei tracciati](fill-types.md)

Individuare gli effetti di diversi possibili con tipi di riempimento SkiaSharp percorso.

## <a name="polylines-and-parametric-equationspolylinesmd"></a>[Polilinee ed equazioni parametriche](polylines.md)

Utilizzare SkiaSharp per il rendering di qualsiasi riga che è possibile definire con equazioni parametriche.

## <a name="dots-and-dashesdotsmd"></a>[Punti e trattini](dots.md)

Master gli aspetti complessi relativi SkiaSharp di disegno di linee punteggiate e tratteggiate.

## <a name="finger-paintingfinger-paintmd"></a>[Disegno con le dita](finger-paint.md)

Utilizzare le dita per disegnare nell'area di disegno.


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
