---
title: Riepilogo del capitolo 3. Dettagli della gestione del testo
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 3. Dettagli della gestione del testo'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5423a9f716f384eca107003bdeca69615f8b459f
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136903"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Riepilogo del capitolo 3. Dettagli della gestione del testo

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

Questo capitolo esamina la [`Label`](xref:Xamarin.Forms.Label) visualizzazione in modo più approfondito, inclusi il colore, i tipi di carattere e la formattazione.

## <a name="wrapping-paragraphs"></a>Wrapping di paragrafi

Quando la [`Text`](xref:Xamarin.Forms.Label.Text) proprietà di `Label` contiene testo lungo, `Label` ne esegue automaticamente il wrapping su più righe, come illustrato nell'esempio [**Baskerville**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) . È possibile incorporare i codici Unicode, ad esempio ' \u2014', per i caratteri em-dash o C# come ' s'per interrompere una nuova riga.

Quando le [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà e di un oggetto `Label` sono impostate su `LayoutOptions.Fill` , la dimensione complessiva di `Label` è regolata dallo spazio reso disponibile dal relativo contenitore. `Label`Viene definito *vincolato*. La dimensione di `Label` è la dimensione del relativo contenitore.

Quando le `HorizontalOptions` `VerticalOptions` proprietà e sono impostate su valori diversi da `LayoutOptions.Fill` , le dimensioni di sono `Label` regolate dallo spazio necessario per il rendering del testo, fino alla dimensione che il relativo contenitore rende disponibile per `Label` . `Label`Viene definito non *vincolato* e ne determina la dimensione.

Nota: è possibile che i termini *limitati* e non *vincolati* siano intuitivi, perché una visualizzazione non vincolata è generalmente inferiore a una vista vincolata. Inoltre, questi termini non vengono usati in modo coerente nei primi capitoli del libro.

Una vista, ad esempio, `Label` può essere vincolata in una dimensione e non vincolata nell'altra. Un oggetto `Label` esegue il wrapping del testo su più righe se è vincolato orizzontalmente.

Se un oggetto `Label` è vincolato, potrebbe occupare molto più spazio di quanto richiesto per il testo. Il testo può essere posizionato all'interno dell'area complessiva della `Label` . Impostare la [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) proprietà su un membro dell' [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) enumerazione ( [`Start`](xref:Xamarin.Forms.TextAlignment.Start) , [`Center`](xref:Xamarin.Forms.TextAlignment.Center) o [`End`](xref:Xamarin.Forms.TextAlignment.Center) ) per controllare l'allineamento di tutte le righe del paragrafo. Il valore predefinito è `Start` e Allinea a sinistra il testo.

Impostare la [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) proprietà su un membro dell' `TextAlignment` enumerazione per posizionare il testo nella parte superiore, centrale o inferiore dell'area occupata dall'oggetto `Label` .

Impostare la [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) proprietà su un membro dell' [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) enumerazione ( [`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap) , [`CharacterWrap`](xref:Xamarin.Forms.LineBreakMode.CharacterWrap) , [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) , [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode.HeadTruncation) , [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation) o [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode.TailTruncation) ) per controllare la modalità di interruzione o di troncamento di più righe in un paragrafo.

## <a name="text-and-background-colors"></a>Colori di sfondo e testo

Impostare le [`TextColor`](xref:Xamarin.Forms.Label.TextColor) [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) proprietà e di `Label` su [`Color`](xref:Xamarin.Forms.Color) Values per controllare il colore del testo e dello sfondo.

`BackgroundColor`Si applica allo sfondo dell'intera area occupata da `Label` . A seconda delle `HorizontalOptions` proprietà e `VerticalOptions` , le dimensioni potrebbero essere notevolmente maggiori rispetto all'area richiesta per visualizzare il testo. È possibile utilizzare il colore per sperimentare i diversi valori di `HorizontalOptions` , `VerticalOptions` , `HorizontalExeAlignment` e `VerticalTextAlignment` per vedere come influiscono sulle dimensioni e la posizione dell'oggetto `Label` , nonché le dimensioni e la posizione del testo all'interno di `Label` .

## <a name="the-color-structure"></a>Struttura dei colori

La [`Color`](xref:Xamarin.Forms.Color) struttura consente di specificare i colori come valori rosso-verde-blu (RGB) o i valori di Hue-Saturation-luminosità (HSL) o con un nome di colore. È disponibile anche un canale alfa per indicare la trasparenza.

Usare un `Color` costruttore per specificare:

- [ombreggiatura grigia](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- un [valore RGB](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- un [valore RGB con trasparenza](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

Gli argomenti sono `double` valori compresi tra 0 e 1.

È anche possibile usare diversi metodi statici per creare `Color` valori:

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double))per `double` i valori RGB da 0 a 1
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32))per valori RGB integer compresi tra 0 e 255
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double))per `double` i valori RGB con trasparenza
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32))per valori RGB integer con trasparenza
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))per `double` i valori HSL con trasparenza
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32))per un `uint` valore calcolato come (B + 256 \* (G + 256 \* (R + 256 \* a)))
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String))per un `string` formato di cifre esadecimali nel formato "#AARRGGBB" o "#RRGGBB" oppure "#ARGB" o "#RGB", dove ogni lettera corrisponde a una cifra esadecimale per i canali alfa, rosso, verde e blu. Questo metodo è primario usato per le conversioni di colori XAML, come illustrato nel [capitolo 7, XAML e codice](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

Una volta creato, un `Color` valore non è modificabile. Le caratteristiche del colore possono essere ottenute dalle proprietà seguenti:

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

Tutti i `double` valori compresi tra 0 e 1.

`Color`definisce inoltre i campi di sola lettura statici pubblici 240 per i colori comuni. Al momento della scrittura del libro, erano disponibili solo 17 colori comuni.

Un altro campo statico pubblico di sola lettura definisce un colore con tutti i canali di colore impostati su zero:

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

Diversi metodi di istanza consentono di modificare un colore esistente per creare un nuovo colore:

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

Infine, due proprietà statiche di sola lettura definiscono un valore di colore speciale:

- [`Color.Default`](xref:Xamarin.Forms.Color.Default), tutti i canali impostati su &ndash; 1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default`ha lo scopo di applicare la combinazione di colori della piattaforma e, di conseguenza, ha un significato diverso in contesti diversi su piattaforme diverse. Per impostazione predefinita, le combinazioni di colori della piattaforma sono:

- iOS: testo scuro in uno sfondo chiaro
- Android: testo chiaro su uno sfondo scuro (nel libro) o testo scuro su uno sfondo chiaro (per la progettazione di materiali tramite AppCompat nel ramo **Master** del repository di codice di esempio)
- UWP: testo scuro in uno sfondo chiaro

Il `Color.Accent` valore genera un colore specifico per la piattaforma (e talvolta selezionabile dall'utente) visibile in uno sfondo scuro o chiaro.

## <a name="changing-the-application-color-scheme"></a>Modifica della combinazione di colori dell'applicazione

Le varie piattaforme hanno una combinazione di colori predefinita, come illustrato nell'elenco precedente.

Quando si fa riferimento a Android, è possibile passare a uno schema scuro specificando un tema chiaro nel file Android.Manifest.xml o [aggiungendo AppCompat e progettazione materiale](~/xamarin-forms/platform/android/appcompat-material-design.md).

Per le piattaforme Windows, il tema colori viene in genere selezionato dall'utente, ma è possibile aggiungere un `RequestedTheme` attributo impostato su `Light` o `Dark` nel file app. XAML della piattaforma. Per impostazione predefinita, il file app. XAML nel progetto UWP contiene un `RequestedTheme` attributo impostato su `Light` .

## <a name="font-sizes-and-attributes"></a>Dimensioni e attributi dei caratteri

Impostare la [`FontFamily`](xref:Xamarin.Forms.Label.FontFamily) proprietà di `Label` su una stringa, ad esempio "Times Roman", per selezionare una famiglia di caratteri. Tuttavia, è necessario specificare una famiglia di caratteri supportata nella piattaforma specifica e le piattaforme sono incoerenti in questo senso.

Impostare la [`FontSize`](xref:Xamarin.Forms.Label.FontSize) proprietà di `Label` su un oggetto `double` per specificare l'altezza approssimativa del tipo di carattere. Per informazioni dettagliate sulla scelta intelligente delle dimensioni dei caratteri, vedere il [capitolo 5 relativo alla gestione delle dimensioni](chapter05.md).

In alternativa, è possibile ottenere una delle diverse dimensioni predefinite dei tipi di carattere dipendenti dalla piattaforma. Oggetto statico [ `Device.GetNamedSize` ] (xrif: Xamarin.Forms . Device. GetNamedSize ( Xamarin.Forms . Metodo NamedSize, System. Type)) e [Overload] (xrif: Xamarin.Forms . Device. GetNamedSize ( Xamarin.Forms . NamedSize, Xamarin.Forms . Elemento)) entrambi restituiscono un `double` valore di dimensione del carattere appropriato per la piattaforma in base ai membri dell' [`NamedSize`](xref:Xamarin.Forms.NamedSize) enumerazione ( [`Default`](xref:Xamarin.Forms.NamedSize.Default) ,, [`Micro`](xref:Xamarin.Forms.NamedSize.Micro) [`Small`](xref:Xamarin.Forms.NamedSize.Small) , [`Medium`](xref:Xamarin.Forms.NamedSize.Medium) e [`Large`](xref:Xamarin.Forms.NamedSize.Large) ). Il valore restituito dal `Medium` membro non è necessariamente uguale a `Default` . L'esempio [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) Visualizza il testo con queste dimensioni denominate.

Impostare la [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) proprietà di `Label` su un membro di questa [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) enumerazione, [`Bold`](xref:Xamarin.Forms.FontAttributes.Bold) , [`Italic`](xref:Xamarin.Forms.FontAttributes.Italic) o [`None`](xref:Xamarin.Forms.FontAttributes.None) . È possibile combinare i `Bold` `Italic` membri e con l'operatore OR bit per bit C#.

## <a name="formatted-text"></a>Testo formattato

In tutti gli esempi fino a questo punto, l'intero testo visualizzato da `Label` è stato formattato in modo uniforme. Per variare la formattazione all'interno di una stringa di testo, non impostare la `Text` proprietà di `Label` . In alternativa, impostare la [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) proprietà su un oggetto di tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString) .

`FormattedString`dispone [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) di una proprietà che è una raccolta di [`Span`](xref:Xamarin.Forms.Span) oggetti. Ogni `Span` oggetto dispone di proprietà,,,, [`Text`](xref:Xamarin.Forms.Span.Text) [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) [`FontSize`](xref:Xamarin.Forms.Span.FontSize) [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) e [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) .

Nell'esempio [**VariableFormattedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) viene illustrato l'utilizzo della `FormattedText` proprietà per una singola riga di testo e [**VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) illustra la tecnica per un intero paragrafo, come illustrato di seguito:

[![Schermata tripla del paragrafo formattato variabile](images/ch03fg06-small.png "Testo etichetta formattato variabile")](images/ch03fg06-large.png#lightbox "Testo etichetta formattato variabile")

Il programma [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) usa un singolo `Label` oggetto e un `FormattedString` oggetto per visualizzare tutte le dimensioni del carattere denominate per ogni piattaforma.

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 3 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Esempi del capitolo 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Esempi del capitolo 3 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Etichetta](~/xamarin-forms/user-interface/text/label.md)
- [Utilizzo dei colori](~/xamarin-forms/user-interface/colors.md)
