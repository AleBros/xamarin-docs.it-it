---
title: Riepilogo del capitolo 3. Approfondimento del testo
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 3. Approfondimento del testo'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 69415b59bbd376330454302981e3216c236a16bb
ms.sourcegitcommit: 93697a20e6fc7da547a8714ac109d7953b61d63f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72980926"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Riepilogo del capitolo 3. Approfondimento del testo

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

In questo capitolo viene illustrata la visualizzazione [`Label`](xref:Xamarin.Forms.Label) in modo più approfondito, tra cui il colore, i tipi di carattere e la formattazione.

## <a name="wrapping-paragraphs"></a>Wrapping di paragrafi

Quando la proprietà [`Text`](xref:Xamarin.Forms.Label.Text) di `Label` contiene testo lungo, `Label` ne esegue automaticamente il wrapping su più righe, come illustrato nell'esempio [**Baskerville**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) . È possibile incorporare i codici Unicode, ad esempio ' \u2014', per l'em C# -Dash o i caratteri come ' s'per interrompere una nuova riga.

Quando le proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) di un `Label` sono impostate su `LayoutOptions.Fill`, le dimensioni complessive del `Label` sono regolate dallo spazio reso disponibile dal relativo contenitore. Il `Label` è detto *vincolato*. Le dimensioni del `Label` sono le dimensioni del relativo contenitore.

Quando le proprietà `HorizontalOptions` e `VerticalOptions` sono impostate su valori diversi da `LayoutOptions.Fill`, la dimensione del `Label` è regolata dallo spazio necessario per il rendering del testo, fino alla dimensione disponibile per il `Label`da parte del relativo contenitore. Il `Label` viene definito non *vincolato* e ne determina la dimensione.

Nota: è possibile che i termini *limitati* e non *vincolati* siano intuitivi, perché una visualizzazione non vincolata è generalmente inferiore a una vista vincolata. Inoltre, questi termini non vengono usati in modo coerente nei primi capitoli del libro.

Una vista come un `Label` può essere vincolata in una dimensione e non vincolata nell'altra. In una `Label` verrà eseguito il wrapping del testo su più righe se è vincolato orizzontalmente.

Se un `Label` è vincolato, potrebbe occupare molto più spazio di quanto richiesto per il testo. Il testo può essere posizionato all'interno dell'area complessiva della `Label`. Impostare la proprietà [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) su un membro dell'enumerazione [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) ([`Start`](xref:Xamarin.Forms.TextAlignment.Start), [`Center`](xref:Xamarin.Forms.TextAlignment.Center)o [`End`](xref:Xamarin.Forms.TextAlignment.Center)) per controllare l'allineamento di tutte le righe del paragrafo. Il valore predefinito è `Start` e Allinea a sinistra il testo.

Impostare la proprietà [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) su un membro dell'enumerazione `TextAlignment` per posizionare il testo nella parte superiore, centrale o inferiore dell'area occupata dal `Label`.

Impostare la proprietà [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) su un membro dell'enumerazione [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) ([`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap), [`CharacterWrap`](xref:Xamarin.Forms.LineBreakMode.CharacterWrap), [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap), [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode.HeadTruncation), [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation)o [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode.TailTruncation)) per controllare il modo in cui il multiplo righe in un'interruzione di paragrafo o troncate.

## <a name="text-and-background-colors"></a>Colori di sfondo e testo

Impostare le proprietà [`TextColor`](xref:Xamarin.Forms.Label.TextColor) e [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) di `Label` su [`Color`](xref:Xamarin.Forms.Color) valori per controllare il colore del testo e dello sfondo.

Il `BackgroundColor` si applica allo sfondo dell'intera area occupata dall'`Label`. A seconda delle proprietà `HorizontalOptions` e `VerticalOptions`, tali dimensioni potrebbero essere notevolmente maggiori rispetto all'area richiesta per visualizzare il testo. È possibile utilizzare il colore per sperimentare diversi valori di `HorizontalOptions`, `VerticalOptions`, `HorizontalExeAlignment`e `VerticalTextAlignment` per vedere come influiscono sulle dimensioni e la posizione del `Label`, nonché le dimensioni e la posizione del testo all'interno del `Label`.

## <a name="the-color-structure"></a>Struttura dei colori

La struttura di [`Color`](xref:Xamarin.Forms.Color) consente di specificare i colori come valori rosso-verde-blu (RGB) o i valori di Hue-Saturation-luminosità (HSL) o con un nome di colore. È disponibile anche un canale alfa per indicare la trasparenza.

Usare un costruttore `Color` per specificare:

- [ombreggiatura grigia](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- un [valore RGB](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- un [valore RGB con trasparenza](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

Gli argomenti sono `double` valori compresi tra 0 e 1.

È anche possibile usare diversi metodi statici per creare valori `Color`:

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) per `double` valori RGB da 0 a 1
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32)) per valori RGB Integer da 0 a 255
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double)) per `double` valori RGB con trasparenza
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) per valori RGB integer con trasparenza
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) per `double` i valori HSL con trasparenza
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32)) per un valore `uint` calcolato come (B + 256 \* (G + 256 \* (R + 256 \* a)))
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) per un formato `string` di cifre esadecimali nel formato "#AARRGGBB" o "#RRGGBB" o "#ARGB" o "#RGB", dove ogni lettera corrisponde a una cifra esadecimale per i canali alfa, rosso, verde e blu. Questo metodo è primario usato per le conversioni di colori XAML, come illustrato nel [capitolo 7, XAML e codice](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

Una volta creato, un valore `Color` non è modificabile. Le caratteristiche del colore possono essere ottenute dalle proprietà seguenti:

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

Tutti `double` valori compresi tra 0 e 1.

`Color` definisce inoltre i campi di sola lettura statici pubblici 240 per i colori comuni. Al momento della scrittura del libro, erano disponibili solo 17 colori comuni.

Un altro campo statico pubblico di sola lettura definisce un colore con tutti i canali di colore impostati su zero:

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

Diversi metodi di istanza consentono di modificare un colore esistente per creare un nuovo colore:

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

Infine, due proprietà statiche di sola lettura definiscono un valore di colore speciale:

- [`Color.Default`](xref:Xamarin.Forms.Color.Default), tutti i canali impostati su &ndash;1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default` ha lo scopo di applicare la combinazione di colori della piattaforma e, di conseguenza, ha un significato diverso in contesti diversi su piattaforme diverse. Per impostazione predefinita, le combinazioni di colori della piattaforma sono:

- iOS: testo scuro in uno sfondo chiaro
- Android: testo chiaro su uno sfondo scuro (nel libro) o testo scuro su uno sfondo chiaro (per la progettazione di materiali tramite AppCompat nel ramo **Master** del repository di codice di esempio)
- UWP: testo scuro in uno sfondo chiaro

Il valore `Color.Accent` restituisce un colore specifico della piattaforma (e talvolta selezionabile dall'utente) visibile in uno sfondo scuro o chiaro.

## <a name="changing-the-application-color-scheme"></a>Modifica della combinazione di colori dell'applicazione

Le varie piattaforme hanno una combinazione di colori predefinita, come illustrato nell'elenco precedente.

Quando si fa riferimento a Android, è possibile passare a uno schema scuro specificando un tema chiaro nel file Android. manifest. XML o [aggiungendo AppCompat e progettazione materiale](~/xamarin-forms/platform/android/appcompat-material-design.md).

Per le piattaforme Windows, il tema colori viene in genere selezionato dall'utente, ma è possibile aggiungere un attributo `RequestedTheme` impostato su `Light` o `Dark` nel file app. XAML della piattaforma. Per impostazione predefinita, il file app. XAML nel progetto UWP contiene un attributo `RequestedTheme` impostato su `Light`.

## <a name="font-sizes-and-attributes"></a>Dimensioni e attributi dei caratteri

Impostare la proprietà [`FontFamily`](xref:Xamarin.Forms.Label.FontFamily) di `Label` su una stringa, ad esempio "Times Roman", per selezionare una famiglia di caratteri. Tuttavia, è necessario specificare una famiglia di caratteri supportata nella piattaforma specifica e le piattaforme sono incoerenti in questo senso.

Impostare la proprietà [`FontSize`](xref:Xamarin.Forms.Label.FontSize) di `Label` su un `double` per specificare l'altezza approssimativa del tipo di carattere. Per informazioni dettagliate sulla scelta intelligente delle dimensioni dei caratteri, vedere il [capitolo 5 relativo alla gestione delle dimensioni](chapter05.md).

In alternativa, è possibile ottenere una delle diverse dimensioni predefinite dei tipi di carattere dipendenti dalla piattaforma. Il metodo e l' [Overload](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,Xamarin.Forms.Element)) del [`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) statico restituiscono entrambi un valore di dimensione del carattere `double` appropriato per la piattaforma in base ai membri dell'enumerazione [`NamedSize`](xref:Xamarin.Forms.NamedSize) ([`Default`](xref:Xamarin.Forms.NamedSize.Default), [`Micro`](xref:Xamarin.Forms.NamedSize.Micro), [`Small`](xref:Xamarin.Forms.NamedSize.Small), [`Medium`](xref:Xamarin.Forms.NamedSize.Medium)e [`Large`](xref:Xamarin.Forms.NamedSize.Large)). Il valore restituito dal membro `Medium` non è necessariamente uguale a quello `Default`. L'esempio [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) Visualizza il testo con queste dimensioni denominate.

Impostare la proprietà [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) di `Label` su un membro di questi [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) enumerazione, [`Bold`](xref:Xamarin.Forms.FontAttributes.Bold), [`Italic`](xref:Xamarin.Forms.FontAttributes.Italic)o [`None`](xref:Xamarin.Forms.FontAttributes.None). È possibile combinare i membri `Bold` e `Italic` con l' C# operatore OR bit per bit.

## <a name="formatted-text"></a>Testo formattato

In tutti gli esempi fino a questo punto, l'intero testo visualizzato dal `Label` è stato formattato in modo uniforme. Per variare la formattazione all'interno di una stringa di testo, non impostare la proprietà `Text` di `Label`. In alternativa, impostare la proprietà [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) su un oggetto di tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString).

`FormattedString` dispone di una proprietà [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) che è una raccolta di oggetti di [`Span`](xref:Xamarin.Forms.Span) . Ogni oggetto `Span` dispone di proprietà [`Text`](xref:Xamarin.Forms.Span.Text), [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily), [`FontSize`](xref:Xamarin.Forms.Span.FontSize), [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes), [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor)e [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) personalizzate.

Nell'esempio [**VariableFormattedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) viene illustrato l'utilizzo della proprietà `FormattedText` per una singola riga di testo e [**VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) illustra la tecnica per un intero paragrafo, come illustrato di seguito:

[![Schermata tripla del paragrafo formattato variabile](images/ch03fg06-small.png "Testo etichetta formattato variabile")](images/ch03fg06-large.png#lightbox "Testo etichetta formattato variabile")

Il programma [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) usa un singolo `Label` e un oggetto `FormattedString` per visualizzare tutte le dimensioni del carattere denominate per ogni piattaforma.

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 3 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Esempi del capitolo 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Esempi del F# capitolo 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Etichetta](~/xamarin-forms/user-interface/text/label.md)
- [Utilizzo dei colori](~/xamarin-forms/user-interface/colors.md)
