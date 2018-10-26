---
title: Modalità di blend in SkiaSharp
description: Usare blend le modalità per definire cosa accade quando gli oggetti grafici sono impilati uno da altro.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CE1B222E-A2D0-4016-A532-EC1E59EE3D6B
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: b2083a819bc688e1742bc4cecccfd24c4d89ac39
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111665"
---
# <a name="skiasharp-blend-modes"></a>Modalità di blend in SkiaSharp

Questi articoli concentrarsi sul [ `BlendMode` ](xref:SkiaSharp.SKPaint.BlendMode) proprietà della [ `SKPaint` ](xref:SkiaSharp.SKPaint). Il `BlendMode` proprietà è di tipo [ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode), un'enumerazione con 29 membri.

Il `BlendMode` proprietà determina ciò che accade quando un oggetto grafico (spesso denominato il _origine_) viene eseguito il rendering nella parte superiore di oggetti di grafici esistenti (chiamato il _destinazione_). In genere, si prevede che il nuovo oggetto con interfaccia grafico per nascondere gli oggetti in esso contenuti. Ma in questo caso solo perché la modalità di blend predefinita è `SKBlendMode.SrcOver`, il che significa che l'origine viene disegnato _failover_ nella destinazione. Gli altri membri del 28 `SKBlendMode` causare altri effetti. Nella programmazione grafica, la tecnica della combinazione di oggetti grafici in vari modi è detta _composizione_.

## <a name="the-skblendmodes-enumeration"></a>L'enumerazione SKBlendModes

Le modalità di blend in SkiaSharp corrispondono a quelli descritti nel W3C [ **la composizione e fusione 1 livello** ](https://www.w3.org/TR/compositing-1/) specifica. Il Skia [ **riferimento SkBlendMode** ](https://skia.org/user/api/SkBlendMode_Reference) vengono fornite informazioni generali utili. Per un'introduzione generale a blend modalità, il [ **modalità di Blend** ](https://en.wikipedia.org/wiki/Blend_modes) articolo di Wikipedia è un buon punto di partenza. Modalità di blend sono supportate in Adobe Photoshop, pertanto non c'è molto online informazioni sulle modalità di blend in tale contesto.

I membri del 29 il `SKBlendMode` enumerazione può essere suddivisi in tre categorie:

| Porter Duff | Separabili    | Non separabili |
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

I nomi di queste tre categorie richiederà più significato nelle discussioni che seguono. L'ordine dei membri sono elencati di seguito è la stessa definizione del `SKBlendMode` enumerazione. I membri dell'13 enumerazione nella prima colonna sono i valori interi da 0 a 12. La seconda colonna sono membri di enumerazione che corrispondono ai numeri interi 13 per 24 e i membri nella terza colonna hanno valori pari a 25 al 28.

Questi blend modalità sono illustrate nella _circa_ stesso ordine nel W3C **la composizione e fusione 1 livello** documento, ma esistono alcune differenze: il `Src` modalità è detta _Copia_ nel documento W3C, e `Plus` viene chiamato _più chiaro_. Definisce il documento W3C una _normale_ fusione che non è incluso nella `SKBlendModes` sarebbe uguale `SrcOver`. Il `Modulate` fusione (nella parte superiore della prima colonna) non è incluso nel documento W3C e discussione del `Multiply` modalità precede `Screen`.

Poiché il `Modulate` modalità blend è univoca per Skia, verrà illustrata una modalità Porter Duff aggiuntive e come modalità separabili.

## <a name="the-importance-of-transparency"></a>L'importanza della trasparenza

In passato, la composizione è stata sviluppata in combinazione con il concetto del _canale alfa_. In una visualizzazione della superficie di attacco, ad esempio il `SKCanvas` oggetto e una bitmap a colori, ogni pixel contenuto costituito da 4 byte: 1 byte, ciascuno per i componenti rossi, verdi e blu e un byte aggiuntivo per la trasparenza. Questo componente alfa è 0 per una completa trasparenza e 0xFF dell'opacità completo, con diversi livelli di trasparenza tra tali valori.

Molte delle modalità blend si basano sulla trasparenza. In genere, quando un `SKCanvas` viene prima ottenuto una `PaintSurface` gestore, oppure quando un `SKCanvas` viene creato per disegnare in una bitmap, il primo passaggio consiste questa chiamata:

```csharp
canvas.Clear();
```

Questo metodo sostituisce tutti i pixel dell'area di disegno con trasparente pixel neri, equivalente a `new SKColor(0, 0, 0, 0)` o nell'intero 0x00000000. Tutti i byte di tutti i pixel vengono inizializzati su zero.

La superficie di disegno di un `SKCanvas` ottenuto una `PaintSurface` gestore potrebbe sembrare hanno uno sfondo bianco, ma solo perché il `SKCanvasView` stesso ha uno sfondo trasparente e la pagina presenta uno sfondo bianco. È possibile illustrare questo fatto a se stessi impostando xamarin. Forms `BackgroundColor` proprietà di `SKCanvasView` a un colore di xamarin. Forms:

```csharp
canvasView.BackgroundColor = Color.Red;
```

O, in una classe che deriva da `ContentPage`, è possibile impostare il colore di sfondo della pagina:

```csharp
BackgroundColor = Color.Red;
```

Verrà visualizzato che questo sfondo rosso per la grafica di SkiaSharp in quanto l'area di disegno di SkiaSharp stesso è trasparente.

L'articolo [ **SkiaSharp trasparenza** ](../../basics/transparency.md) illustra alcune tecniche di base usando la trasparenza per disporre di più immagini in un'immagine composita. Che superano le modalità di blend, ma la trasparenza rimane fondamentale per le modalità blend. 

## <a name="skiasharp-porter-duff-blend-modesporter-duffmd"></a>[Metodi di fusione Porter-Duff SkiaSharp](porter-duff.md)

Utilizzare i metodi di fusione Porter Duff per comporre le scene basate su immagini di origine e destinazione.

## <a name="skiasharp-separable-blend-modesseparablemd"></a>[Metodi di fusione separabili in SkiaSharp](separable.md)

Utilizzare i metodi di fusione separabili per modificare i colori rossi, verdi e blu.

## <a name="skiasharp-non-separable-blend-modesnon-separablemd"></a>[Modalità di blend non separabili in SkiaSharp](non-separable.md)

Utilizzare le modalità di blend non separabili per modificare la tonalità, saturazione e luminosità.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)