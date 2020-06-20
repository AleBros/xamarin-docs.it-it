---
title: Colori inXamarin.Forms
description: Xamarin.Formsfornisce una classe di colori flessibile multipiattaforma. Questo articolo illustra la funzionalità fornita dalla classe Color e come usarla.
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a02fe7451702367d85d322b756df4a547a009454
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137345"
---
# <a name="colors-in-xamarinforms"></a>Colori inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)

_Novell. Forms offre una classe di colori flessibile multipiattaforma._

In questo articolo vengono illustrati i vari modi [`Color`](xref:Xamarin.Forms.Color) in cui è possibile utilizzare la classe Xamarin.Forms .

La [`Color`](xref:Xamarin.Forms.Color) classe fornisce diversi metodi per compilare un' `Color` istanza:

- **Colori denominati** : una raccolta di colori denominati comuni, tra cui `Red` , `Green` e `Blue` .
- `FromHex`-valore stringa simile alla sintassi utilizzata in HTML, ad esempio "00FF00". È possibile specificare facoltativamente Alpha come prima coppia di caratteri ("CC00FF00").
- `FromHsla`-Valori di tonalità, saturazione e luminosità `double` , con valore alfa facoltativo (0,0-1,0).
- `FromHsv`: Tonalità, saturazione e valore `int` o `double` valori.
- `FromHsva`: Tonalità, saturazione e valore `int` o `double` valori.
- `FromRgb`-Valori rosso, verde e blu `int` (0-255).
- `FromRgba`-Valori rosso, verde, blu e alfa `int` (0-255).
- `FromUint`-impostare un singolo `double` valore che rappresenta **ARGB**.

Ecco alcuni colori di esempio, assegnati a `BackgroundColor` di alcune etichette usando diverse varianti della sintassi consentita:

```csharp
var red    = new Label { Text = "Red",   BackgroundColor = Color.Red };
var orange = new Label { Text = "Orange",BackgroundColor = Color.FromHex("FF6A00") };
var yellow = new Label { Text = "Yellow",BackgroundColor = Color.FromHsla(0.167, 1.0, 0.5, 1.0) };
var green  = new Label { Text = "Green", BackgroundColor = Color.FromRgb (38, 127, 0) };
var blue   = new Label { Text = "Blue",  BackgroundColor = Color.FromRgba(0, 38, 255, 255) };
var indigo = new Label { Text = "Indigo",BackgroundColor = Color.FromRgb (0, 72, 255) };
var violet = new Label { Text = "Violet",BackgroundColor = Color.FromHsla(0.82, 1, 0.25, 1) };

var transparent = new Label { Text = "Transparent",BackgroundColor = Color.Transparent };
var @default = new Label    { Text = "Default",    BackgroundColor = Color.Default };
var accent = new Label      { Text = "Accent",     BackgroundColor = Color.Accent };
```

Questi colori sono visualizzati in ogni piattaforma riportata di seguito. Si noti che il colore finale `Accent` è un colore blu-ish per iOS e Android; questo valore è definito da Xamarin.Forms .

 [![Demo colori](colors-images/colors-sml.png "Demo colori")](colors-images/colors.png#lightbox "Demo colori")

## <a name="colordefault"></a>Color. default

Utilizzare l'oggetto `Default` per impostare o reimpostare nuovamente un valore di colore sull'impostazione predefinita della piattaforma (per comprendere che questo rappresenta un colore sottostante diverso in ogni piattaforma per ogni proprietà).

Gli sviluppatori possono usare questo valore per impostare una `Color` proprietà, ma **non** devono eseguire una query su questa istanza per i relativi valori RGB dei componenti (tutti impostati su-1).

## <a name="colortransparent"></a>Color. Transparent

Impostare il colore da cancellare.

## <a name="coloraccent"></a>Colore. accento

In iOS e Android questa istanza è impostata su un colore a contrasto visibile sullo sfondo predefinito, ma non è uguale al colore del testo predefinito.

## <a name="additional-methods"></a>Metodi aggiuntivi

[`Color`](xref:Xamarin.Forms.Color)le istanze includono i metodi aggiuntivi seguenti:

- `AddLuminosity`: restituisce un oggetto `Color` modificando la luminosità dal Delta fornito.
- `MultiplyAlpha`: restituisce un oggetto `Color` modificando l'alfa, moltiplicando il valore per il valore alfa fornito.
- `ToHex`: restituisce una rappresentazione esadecimale `string` di un oggetto `Color` .
- `WithHue`: restituisce un oggetto `Color` , sostituendo la tonalità con il valore fornito.
- `WithLuminosity`: restituisce un oggetto `Color` , sostituendo la luminosità con il valore fornito.
- `WithSaturation`: restituisce un oggetto `Color` , sostituendo la saturazione con il valore fornito.

## <a name="implicit-conversions"></a>Conversioni implicite

È possibile eseguire la conversione implicita tra i `Xamarin.Forms.Color` `System.Drawing.Color` tipi e:

```csharp
Xamarin.Forms.Color xfColor = Xamarin.Forms.Color.FromRgb(0, 72, 255);
System.Drawing.Color sdColor = System.Drawing.Color.FromArgb(38, 127, 0);

// Implicity convert from a Xamarin.Forms.Color to a System.Drawing.Color
System.Drawing.Color sdColor2 = xfColor;

// Implicitly convert from a System.Drawing.Color to a Xamarin.Forms.Color
Xamarin.Forms.Color xfColor2 = sdColor;
```

## <a name="deviceruntimeplatform"></a>Device. RuntimePlatform

Questo frammento di codice usa la `Device.RuntimePlatform` proprietà per impostare selettivamente il colore di un oggetto `ActivityIndicator` :

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="use-from-xaml"></a>Usare da XAML

È anche possibile fare riferimento ai colori in XAML usando i nomi di colore definiti o le rappresentazioni esadecimali illustrate di seguito:

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

> [!NOTE]
> Quando si usa la compilazione XAML, i nomi di colore non fanno distinzione tra maiuscole e minuscole e pertanto possono essere scritti in minuscolo Per altre informazioni sulla compilazione XAML, vedere [Compilazione XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="related-links"></a>Collegamenti correlati

- [ColorsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)
- [Selezione associabile (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
