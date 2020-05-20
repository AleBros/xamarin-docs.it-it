---
title: Modalità di Blend SkiaSharp
description: Utilizzare le modalità di Blend per definire cosa accade quando gli oggetti grafici sono sovrapposti tra loro.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CE1B222E-A2D0-4016-A532-EC1E59EE3D6B
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 829d764f03dd77c6126c2f4bced750ae570a3bc6
ms.sourcegitcommit: bc0c1740aa0708459729c0e671ab3ff7de3e2eee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/15/2020
ms.locfileid: "83425689"
---
# <a name="skiasharp-blend-modes"></a>Modalità di Blend SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Questi articoli sono incentrati sulla [`BlendMode`](xref:SkiaSharp.SKPaint.BlendMode) proprietà di [`SKPaint`](xref:SkiaSharp.SKPaint) . La `BlendMode` proprietà è di tipo [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) , un'enumerazione con 29 membri.

La `BlendMode` proprietà determina cosa accade quando viene eseguito il rendering di un oggetto grafico (spesso chiamato _origine_) sugli oggetti grafici esistenti (chiamato _destinazione_). In genere, si prevede che il nuovo oggetto grafico nasconda gli oggetti sottostanti. Questa situazione si verifica solo perché la modalità di Blend predefinita è `SKBlendMode.SrcOver` , il che significa che l'origine viene disegnata _sulla_ destinazione. Gli altri 28 membri di `SKBlendMode` generano altri effetti. Nella programmazione grafica la tecnica di combinazione di oggetti grafici in vari modi è nota come _composizione_.

## <a name="the-skblendmodes-enumeration"></a>Enumerazione SKBlendModes

Le modalità SkiaSharp Blend corrispondono a quelle descritte nella specifica W3C [**compositing and blending Level 1**](https://www.w3.org/TR/compositing-1/) . La Panoramica di Skia [**SkBlendMode**](https://skia.org/user/api/SkBlendMode_Overview) fornisce anche informazioni di base utili. Per un'introduzione generale alle modalità di Blend, l'articolo sulle [**modalità di Blend**](https://en.wikipedia.org/wiki/Blend_modes) in Wikipedia è un valido punto di partenza. Le modalità di Blend sono supportate in Adobe Photoshop, quindi sono disponibili altre informazioni online sulle modalità di Blend in tale contesto.

I 29 membri dell' `SKBlendMode` enumerazione possono essere divisi in tre categorie:

| Porter-Duff | Separabili    | Non separabile |
| ----------- | ------------ | ------------- |
| `Clear`     | `Modulate`   | `Hue`         |
| `Src`       | `Screen`     | `Saturation`  |
| `Dst`       | `Overlay`    | `Color`       |
| `SrcOver`   | `Darken`     | `Luminosity`  |
| `DstOver`   | `Lighten`    |               |
| `SrcIn`     | `ColorDodge` |               |
| `DstIn`     | `ColorBurn`  |               |
| `SrcOut`    | `HardLight`  |               |
| `DstOut`    | `SoftLight`  |               |
| `SrcATop`   | `Difference` |               |
| `DstATop`   | `Exclusion`  |               |
| `Xor`       | `Multiply`   |               |
| `Plus`      |              |               |

I nomi di queste tre categorie verranno rilevati con maggiore significato nelle discussioni che seguono. L'ordine in cui sono elencati i membri corrisponde a quello della definizione dell' `SKBlendMode` enumerazione. Ai 13 membri dell'enumerazione nella prima colonna sono associati i valori integer da 0 a 12. La seconda colonna è costituita dai membri dell'enumerazione che corrispondono ai numeri interi da 13 a 24 e i membri della terza colonna hanno valori compresi tra 25 e 28.

Queste modalità di Blend sono illustrate _approssimativamente_ nello stesso ordine del documento W3C **compositing and blending Level 1** , ma vi sono alcune differenze: la `Src` modalità viene chiamata _Copy_ nel documento W3C ed `Plus` è denominata _Lighter_. Il documento W3C definisce una modalità di Blend _normale_ che non è inclusa in `SKBlendModes` perché sarebbe uguale a `SrcOver` . La `Modulate` modalità Blend (nella parte superiore della prima colonna) non è inclusa nel documento W3C e la descrizione della `Multiply` modalità precede `Screen` .

Poiché la `Modulate` modalità Blend è univoca per Skia, verrà discussa come modalità di Porter-Duff aggiuntiva e come modalità separabile.

## <a name="the-importance-of-transparency"></a>Importanza della trasparenza

In passato, la composizione è stata sviluppata insieme al concetto di _canale alfa_. In una superficie di visualizzazione, ad esempio l' `SKCanvas` oggetto e una bitmap a colori completi, ogni pixel è costituito da 4 byte: 1 byte per i componenti rosso, verde e blu e un byte aggiuntivo per la trasparenza. Questo componente alfa è 0 per la trasparenza completa e 0xFF per l'opacità completa, con diversi livelli di trasparenza tra questi valori.

Molte delle modalità di Blend si basano sulla trasparenza. In genere, quando un `SKCanvas` viene ottenuto per la prima volta in un `PaintSurface` gestore o quando `SKCanvas` viene creato un oggetto da creare su una bitmap, il primo passaggio è la chiamata:

```csharp
canvas.Clear();
```

Questo metodo sostituisce tutti i pixel dell'area di disegno con i pixel neri trasparenti, equivalenti a `new SKColor(0, 0, 0, 0)` o all'integer 0x00000000. Tutti i byte di tutti i pixel vengono inizializzati su zero.

La superficie di disegno di un oggetto `SKCanvas` ottenuto in un `PaintSurface` gestore potrebbe avere uno sfondo bianco, ma è solo perché ha uno sfondo `SKCanvasView` trasparente e la pagina presenta uno sfondo bianco. È possibile dimostrare questo fatto impostando la proprietà Novell. Forms `BackgroundColor` di `SKCanvasView` su un colore Novell. Forms:

```csharp
canvasView.BackgroundColor = Color.Red;
```

In alternativa, in una classe che deriva da `ContentPage` , è possibile impostare il colore di sfondo della pagina:

```csharp
BackgroundColor = Color.Red;
```

Si vedrà questo sfondo rosso dietro la grafica di SkiaSharp perché l'area di disegno SkiaSharp è trasparente.

L'articolo [**SkiaSharp Transparency**](../../basics/transparency.md) ha illustrato alcune tecniche di base per l'uso della trasparenza per disporre più elementi grafici in un'immagine composita. Le modalità di Blend vanno oltre tale, ma la trasparenza rimane fondamentale per le modalità di Blend.

## <a name="skiasharp-porter-duff-blend-modes"></a>[Modalità di Blend di SkiaSharp Porter-Duff](porter-duff.md)

Usare le modalità di Blend di Porter-Duff per comporre scene basate su immagini di origine e di destinazione.

## <a name="skiasharp-separable-blend-modes"></a>[Modalità di Blend separabili SkiaSharp](separable.md)

Utilizzare le modalità di Blend separabili per modificare i colori rosso, verde e blu.

## <a name="skiasharp-non-separable-blend-modes"></a>[Modalità di Blend non separabili SkiaSharp](non-separable.md)

Usare le modalità di Blend non separabili per modificare tonalità, saturazione o luminosità.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
