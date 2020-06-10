---
title: "SkiaSharp Lines and paths" Description: "in questo articolo viene illustrato come utilizzare SkiaSharp per tracciare linee e percorsi grafici nelle Xamarin.Forms applicazioni e come illustrato nel codice di esempio".
ms. prod: Novell MS. AssetID: 316A15FE-383D-4D06-8641-BAC7EE7474CA ms. Technology: Novell-skiasharp autore: davidbritch ms. Author: dabritch ms. Date: 03/10/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="skiasharp-lines-and-paths"></a>Linee e tracciati in SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Usare SkiaSharp per tracciare linee e percorsi grafici_

Nella [sezione precedente](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md) è stato illustrato che la `SKCanvas` classe SkiaSharp include diversi metodi per creare cerchi, ovali, rettangoli, rettangoli arrotondati, testo e bitmap. In questa sezione e nelle sezioni successive vengono descritte le varie classi connesse alla creazione e al rendering dei *percorsi grafici*.

Il percorso grafico è l'approccio più generalizzato per disegnare linee e curve in SkiaSharp. Questa sezione illustra l'uso [`SKPath`](xref:SkiaSharp.SKPath) di un oggetto per creare linee rette e per usare una raccolta di linee rette semplici (detta *polilinea*) per creare curve che è possibile definire algoritmicamente. Una sezione successiva sulle [**curve e sui percorsi di SkiaSharp**](../curves/index.md) illustra i diversi tipi di curve supportate da `SKPath` .

Tutti i programmi di esempio in questa sezione vengono visualizzati sotto le **linee e i percorsi** delle intestazioni nel Home page del programma [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) e nella cartella [**percorsi**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths) della soluzione.

## <a name="lines-and-stroke-caps"></a>[Linee ed estremità dei tratti](lines.md)

Informazioni su come usare SkiaSharp per tracciare linee con diverse estremità del tratto.

## <a name="path-basics"></a>[Nozioni di base sui tracciati](paths.md)

Esplorare l' `SKPath` oggetto SkiaSharp per combinare linee e curve.

## <a name="the-path-fill-types"></a>[Tipi di riempimento dei tracciati](fill-types.md)

Individuare i diversi effetti possibili con i tipi di riempimento del percorso SkiaSharp.

## <a name="polylines-and-parametric-equations"></a>[Polilinee ed equazioni parametriche](polylines.md)

Usare SkiaSharp per eseguire il rendering di qualsiasi riga che è possibile definire con equazioni parametriche.

## <a name="dots-and-dashes"></a>[Punti e trattini](dots.md)

Padroneggiare le complessità del disegno di linee tratteggiate e tratteggiate in SkiaSharp.

## <a name="finger-painting"></a>[Disegno con le dita](finger-paint.md)

Usare le dita per disegnare nell'area di disegno.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
