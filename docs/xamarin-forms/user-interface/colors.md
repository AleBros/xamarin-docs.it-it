---
title: Colori in Xamarin.Forms
description: Xamarin.Forms fornisce una classe di colore multipiattaforma flessibile. Questo articolo illustra le funzionalità fornite dalla classe colore e come usarlo.
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2019
ms.openlocfilehash: 2a17b037803d1ca6e54000ea7ba3f05c8ce6034f
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888465"
---
# <a name="colors-in-xamarinforms"></a>Colori in Xamarin.Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)

_Xamarin.Forms fornisce una classe di colore multipiattaforma flessibile._

Questo articolo presenta i vari modi in [`Color`](xref:Xamarin.Forms.Color) cui la classe può essere usata in Xamarin.Forms.

La [`Color`](xref:Xamarin.Forms.Color) classe fornisce diversi metodi per compilare un'istanza del colore:

- **Colori denominati** -una raccolta di denominato-colori comuni, tra cui `Red`, `Green`, e `Blue`.
- **FromHex** -valore stringa simile alla sintassi utilizzata in HTML, ad esempio "00FF00". È possibile specificare facoltativamente Alpha come prima coppia di caratteri ("CC00FF00").
- **FromHsla** -tonalità, saturazione e luminosità `double` valori, con valore alfa facoltativo (0,0-1,0).
- **FromRgb** -rosso, verde e blu `int` valori (0-255).
- **FromRgba** -rosso, verde, blu e alfa `int` valori (0-255).
- **FromUint** -impostare un'unica `double` valore che rappresenta **argb**.

Ecco alcuni colori di esempio, assegnati al `BackgroundColor` di alcune etichette usando diverse varianti della sintassi consentita:

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

Tali colori vengono visualizzati in ogni piattaforma riportato di seguito. Si noti che il colore finale - `Accent` -è un colore blue-ish per iOS e Android; questo valore viene definito da Xamarin.Forms.

 [![Demo di colore](colors-images/colors-sml.png "colore Demo")](colors-images/colors.png#lightbox "Demo di colore")

## <a name="colordefault"></a>Color.Default

Usare il `Default` per impostare un valore di colore sul valore predefinito della piattaforma (comprensione che rappresenta un colore sottostante diverso in ogni piattaforma per ogni proprietà) (o imposta nuovamente).

Gli sviluppatori possono usare questo valore per impostare una `Color` proprietà ma deve **non** eseguire query su questa istanza per i valori RGB del componente (che è tutto pronto per -1).

## <a name="colortransparent"></a>Transparent

Impostare il colore da cancellare.

## <a name="coloraccent"></a>Color.Accent

In iOS e Android questa istanza è impostata su un colore a contrasto è visibile in background predefinita, ma non è lo stesso come il colore del testo predefinito.

## <a name="additional-methods"></a>Metodi aggiuntivi

[`Color`](xref:Xamarin.Forms.Color)le istanze includono i metodi aggiuntivi seguenti:

- **AddLuminosity** : restituisce un `Color` oggetto modificando la luminosità dal Delta fornito.
- **MultiplyAlpha** : restituisce un `Color` oggetto modificando l'alfa, moltiplicando il valore per il valore alfa fornito.
- **ToHex** : restituisce una rappresentazione `string` esadecimale di `Color`un oggetto.
- **WithHue** : restituisce un `Color`oggetto, sostituendo la tonalità con il valore fornito.
- **WithLuminosity** : restituisce un `Color`oggetto, sostituendo la luminosità con il valore fornito.
- **WithSaturation** : restituisce un `Color`oggetto, sostituendo la saturazione con il valore fornito.

## <a name="implicit-conversions"></a>Conversioni implicite

Conversione implicita tra i `Xamarin.Forms.Color` e `System.Drawing.Color` tipi possono essere eseguiti:

```csharp
Xamarin.Forms.Color xfColor = Xamarin.Forms.Color.FromRgb(0, 72, 255);
System.Drawing.Color sdColor = System.Drawing.Color.FromArgb(38, 127, 0);

// Implicity convert from a Xamarin.Forms.Color to a System.Drawing.Color
System.Drawing.Color sdColor2 = xfColor;

// Implicitly convert from a System.Drawing.Color to a Xamarin.Forms.Color
Xamarin.Forms.Color xfColor2 = sdColor;
```

## <a name="deviceruntimeplatform"></a>Device.RuntimePlatform

Questo frammento di codice Usa il `Device.RuntimePlatform` proprietà da impostare in modo selettivo il colore di un `ActivityIndicator`:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="using-from-xaml"></a>Uso di XAML

È anche possibile fare riferimento ai colori in XAML usando i nomi di colore definiti o le rappresentazioni esadecimali illustrate di seguito:

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

> [!NOTE]
> Quando si usa la compilazione XAML, i nomi di colore sono maiuscole e minuscole e pertanto possono essere scritti in lettere minuscole. Per altre informazioni sulla compilazione XAML, vedere [Compilazione XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="related-links"></a>Collegamenti correlati

- [ColorsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)
- [Selezione associabile (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
