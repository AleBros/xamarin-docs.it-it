---
title: Colori
description: Xamarin. Forms fornisce una classe di colore flessibile e multipiattaforma.
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: 71c10e1de8b94b8d9799d144fb603c82c40ca9eb
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2018
---
# <a name="colors"></a>Colori

_Xamarin. Forms fornisce una classe di colore flessibile e multipiattaforma._

Questo articolo descrive i vari modi di `Color` classe può essere utilizzata in xamarin. Forms.

La `Color` classe fornisce numerosi metodi per creare un'istanza di colore

-  **I colori denominati** -una raccolta di denominato-colori comuni, tra cui `Red`, `Green`, e `Blue`.
-  **FromHex** -stringa di valore simili alla sintassi utilizzata in formato HTML, ad esempio "00FF00". Alfa è facoltativamente possibile specificare come la prima coppia di caratteri ("CC00FF00").
-  **FromHsla** -tonalità, saturazione e luminosità `double` valori, con valore alfa facoltativo (0.0, 1.0).
-  **FromRgb** -rosso, verde e blu `int` valori (0-255).
-  **FromRgba** -rosso, verde, blu e alfa `int` valori (0-255).
-  **FromUint** -impostare un singolo `double` valore che rappresenta **argb**.

Ecco alcuni colori di esempio, assegnati al `BackgroundColor` di alcune etichette utilizzando diverse varianti della sintassi consentita:

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

Questi colori vengono visualizzati in ogni piattaforma sottostante. Si noti il colore finale - `Accent` -un colore blue-ish per iOS e Android; questo valore è definito da xamarin. Forms.

 [![Demo di colore](colors-images/colors-sml.png "Demo colore")](colors-images/colors.png#lightbox "Demo di colore")

## <a name="colordefault"></a>Color.Default

Utilizzare il `Default` per impostare un valore di colore predefinita della piattaforma (comprensione che rappresenta un colore diverso sottostante in ogni piattaforma per ogni proprietà) (o impostare nuovamente).

Gli sviluppatori possono utilizzare questo valore per impostare un `Color` proprietà ma deve **non** eseguire query su questa istanza per il componente valori RGB (che sta tutti impostati su -1).

## <a name="colortransparent"></a>Transparent

Impostare il colore da cancellare.

## <a name="coloraccent"></a>Color.Accent

In iOS e Android questa istanza è impostata su un colore diversi che è visibile sullo sfondo predefinito ma non è lo stesso come il colore del testo predefinito.

## <a name="additional-methods"></a>Metodi aggiuntivi

`Color` le istanze includono metodi aggiuntivi che possono essere utilizzati per creare nuovi colori:

-  **AddLuminosity** -restituisce un nuovo colore cambiando la luminosità del delta fornito.
-  **WithHue** -restituisce un nuovo colore, sostituendo la tonalità con il valore specificato.
-  **WithLuminosity** -restituisce un nuovo colore, sostituendo la luminosità con il valore specificato.
-  **WithSaturation** -restituisce un nuovo colore, sostituendo la saturazione con il valore specificato.
-  **MultiplyAlpha** -restituisce un nuovo colore modificando il canale alfa, moltiplicandolo per il valore alfa.

## <a name="deviceruntimeplatform"></a>Device.RuntimePlatform

Questo frammento di codice Usa il `Device.RuntimePlatform` proprietà da impostare in modo selettivo il colore di un `ActivityIndicator`:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="using-from-xaml"></a>Utilizzo di XAML

Colori possono anche essere facilmente a cui fa riferimento in XAML tramite i nomi di colore definito o le rappresentazioni esadecimali illustrate di seguito:

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

## <a name="summary"></a>Riepilogo

Di xamarin. Forms `Color` classe viene utilizzata per creare riferimenti colore basato su piattaforma. E può essere utilizzato nel codice condiviso e XAML.


## <a name="related-links"></a>Collegamenti correlati

- [ColorsSample](https://developer.xamarin.com/samples/WorkingWithColors)
- [Selezione associabili (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindablePicker/)
