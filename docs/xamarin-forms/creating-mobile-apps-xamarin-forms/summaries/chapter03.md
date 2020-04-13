---
title: Riassunto del capitolo 3. Dettagli della gestione del testo
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 3. Dettagli della gestione del testo'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 69415b59bbd376330454302981e3216c236a16bb
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "72980926"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Riassunto del capitolo 3. Dettagli della gestione del testo

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

Questo capitolo esplora [`Label`](xref:Xamarin.Forms.Label) la vista in modo più approfondito, inclusi colore, font e formattazione.

## <a name="wrapping-paragraphs"></a>Disposizione dei paragrafi

Quando [`Text`](xref:Xamarin.Forms.Label.Text) la `Label` proprietà contiene `Label` testo lungo, va automaticamente a capo su più righe, come illustrato nell'esempio [**Baskervilles.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) È possibile incorporare i codici Unicode, ad esempio ''u2014'' per il trattino em, o caratteri di C, ad esempio ''r' per interrompere in una nuova riga.

Quando [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) le [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà `Label` e di `LayoutOptions.Fill`un oggetto sono `Label` impostate su , la dimensione complessiva dell'oggetto è regolata dal vano disponibile dal relativo contenitore. Si `Label` dice che sia *vincolato*. La dimensione `Label` di è la dimensione del suo contenitore.

Quando `HorizontalOptions` le `VerticalOptions` proprietà e sono `LayoutOptions.Fill`impostate su `Label` valori diversi da , la dimensione dell'oggetto è regolata dallo `Label`spazio necessario per eseguire il rendering del testo, fino alle dimensioni che il contenitore rende disponibili per l'oggetto . Il `Label` si dice che non *vincolato* e determina la propria dimensione.

(Nota: i termini *vincolati* e *non vincolati* potrebbero essere controintuitivi, perché una vista non vincolata è in genere più piccola di una vista vincolata. Inoltre, questi termini non sono utilizzati in modo coerente nei primi capitoli del libro.)

Una vista come `Label` a può essere vincolata in una dimensione e non vincolata nell'altra. A `Label` avvolgimento del testo su più righe solo se è vincolato orizzontalmente.

Se `Label` un oggetto è vincolato, potrebbe occupare molto più spazio di quanto richiesto per il testo. Il testo può essere posizionato all'interno dell'area complessiva del `Label`file . Impostare [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) la proprietà su [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) un[`Start`](xref:Xamarin.Forms.TextAlignment.Start) [`Center`](xref:Xamarin.Forms.TextAlignment.Center)membro [`End`](xref:Xamarin.Forms.TextAlignment.Center)dell'enumerazione ( , , o ) per controllare l'allineamento di tutte le righe del paragrafo. L'impostazione predefinita è `Start` e allinea a sinistra il testo.

Impostare [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) la proprietà su `TextAlignment` un membro dell'enumerazione per posizionare il testo `Label`nella parte superiore, centrale o inferiore dell'area occupata da .

Impostare [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) la proprietà su [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) un[`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap) [`CharacterWrap`](xref:Xamarin.Forms.LineBreakMode.CharacterWrap)membro [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode.HeadTruncation)dell'enumerazione ( , , , , [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation)o [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode.TailTruncation)) per controllare la modalità di troncamento delle righe multiple in un'interruzione di paragrafo.

## <a name="text-and-background-colors"></a>Colori del testo e dello sfondo

Impostare [`TextColor`](xref:Xamarin.Forms.Label.TextColor) [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) le `Label` proprietà [`Color`](xref:Xamarin.Forms.Color) e di su su per controllare il colore del testo e dello sfondo.

Il `BackgroundColor` si applica allo sfondo dell'intera area occupata dal `Label`. A seconda `HorizontalOptions` `VerticalOptions` delle proprietà e , tale dimensione potrebbe essere notevolmente maggiore dell'area necessaria per visualizzare il testo. È possibile utilizzare color per `HorizontalOptions`sperimentare `VerticalOptions` `HorizontalExeAlignment`vari `VerticalTextAlignment` valori di , , e per `Label`vedere come influiscono sulle dimensioni `Label`e la posizione del , none le dimensioni e la posizione del testo all'interno del file .

## <a name="the-color-structure"></a>La struttura Color

La [`Color`](xref:Xamarin.Forms.Color) struttura consente di specificare i colori come valori rosso-verde-blu (RGB) o valori Hue-Saturation-Luminosity (HSL) o con un nome di colore. È inoltre disponibile un canale Alfa per indicare la trasparenza.

Utilizzare `Color` un costruttore per specificare:Use a constructor to specify:

- una [tonalità grigia](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- un [valore RGB](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- un [valore RGB con trasparenza](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

Gli `double` argomenti sono valori compresi tra 0 e 1.Arguments are values ranging ranging from 0 to 1.

È inoltre possibile utilizzare diversi `Color` metodi statici per creare valori:You can also use several static methods to create values:

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double))per `double` valori RGB da 0 a 1
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32))per valori RGB interi compresi tra 0 e 255
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double))per `double` i valori RGB con trasparenza
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32))per valori RGB interi con trasparenza
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))per `double` i valori HSL con trasparenza
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32))per `uint` un valore calcolato come (B : 256 \* (G - 256 \* (R - 256 \* A)))
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String))per `string` un formato di cifre esadecimali nel formato "#AARRGGBB" o "#RRGGBB" o "#ARGB" o "#RGB", dove ogni lettera corrisponde a una cifra esadecimale per i canali alfa, rosso, verde e blu. Questo metodo viene utilizzato come primario per le conversioni di colore XAML, come illustrato nel [Capitolo 7, XAML e codice .](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md)

Una volta `Color` creato, un valore non è modificabile. Le caratteristiche del colore possono essere ottenute dalle seguenti proprietà:

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

Questi sono `double` tutti valori che vanno da 0 a 1.

`Color`definisce inoltre 240 campi statici pubblici di sola lettura per i colori comuni. Al momento della scrittura del libro, erano disponibili solo 17 colori comuni.

Un altro campo statico pubblico di sola lettura definisce un colore con tutti i canali di colore impostati su zero:

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

Diversi metodi di istanza consentono di modificare un colore esistente per creare un nuovo colore:

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

Infine, due proprietà statiche di sola lettura definiscono un valore di colore speciale:Finally, two static read-only properties define special color value:

- [`Color.Default`](xref:Xamarin.Forms.Color.Default), tutti i &ndash;canali impostati su 1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default`ha lo scopo di applicare la combinazione di colori della piattaforma, e di conseguenza ha un significato diverso in contesti diversi su piattaforme diverse. Per impostazione predefinita, le combinazioni di colori della piattaforma sono:

- iOS: testo scuro su sfondo chiaro
- Android: testo chiaro su sfondo scuro (nel libro) o testo scuro su uno sfondo chiaro (per Material Design tramite AppCompat nel ramo **master** del repository di codice di esempio)
- UWP: testo scuro su sfondo chiaro

Il `Color.Accent` valore restituisce un colore specifico della piattaforma (e talvolta selezionabile dall'utente) visibile su uno sfondo scuro o chiaro.

## <a name="changing-the-application-color-scheme"></a>Modifica della combinazione di colori dell'applicazione

Le varie piattaforme hanno una combinazione di colori predefinita, come mostrato nell'elenco precedente.

Quando si mira Android, è possibile passare a uno schema di luce scura specificando un tema chiaro nel file Android.Manifest.xml o [aggiungendo AppCompat e Material Design](~/xamarin-forms/platform/android/appcompat-material-design.md).

Per le piattaforme Windows, il tema dei colori viene normalmente selezionato dall'utente, ma è possibile aggiungere un `RequestedTheme` attributo impostato su o `Light` `Dark` nel file App.xaml della piattaforma. Per impostazione predefinita, il file App.xaml `RequestedTheme` nel `Light`progetto UWP contiene un attributo impostato su .

## <a name="font-sizes-and-attributes"></a>Dimensioni e attributi dei caratteri

Impostare [`FontFamily`](xref:Xamarin.Forms.Label.FontFamily) la `Label` proprietà di su una stringa come "Times Roman" per selezionare una famiglia di caratteri. Tuttavia, è necessario specificare una famiglia di caratteri che è supportata sulla piattaforma specifica e le piattaforme sono incoerenti a questo proposito.

Impostare [`FontSize`](xref:Xamarin.Forms.Label.FontSize) la `Label` proprietà `double` di su un per specificare l'altezza approssimativa del tipo di carattere. Per ulteriori informazioni sulla scelta intelligente delle dimensioni dei caratteri, vedere [Il Capitolo 5 , Gestione delle dimensioni delle dimensioni.](chapter05.md)

In alternativa, è possibile ottenere una delle diverse dimensioni di carattere preimpostate dipendenti dalla piattaforma. Il [`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) metodo statico e `double` [`NamedSize`](xref:Xamarin.Forms.NamedSize) l'overload restituiscono entrambi un valore di[`Default`](xref:Xamarin.Forms.NamedSize.Default) [`Micro`](xref:Xamarin.Forms.NamedSize.Micro)dimensione [`Small`](xref:Xamarin.Forms.NamedSize.Small) [`Medium`](xref:Xamarin.Forms.NamedSize.Medium)del [`Large`](xref:Xamarin.Forms.NamedSize.Large)carattere appropriato alla piattaforma in base ai membri dell'enumerazione ( , , , , e ). [overload](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,Xamarin.Forms.Element)) Il valore restituito `Medium` dal membro non è `Default`necessariamente lo stesso di . Nell'esempio [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) viene visualizzato il testo con queste dimensioni denominate.

Impostare [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) la `Label` proprietà di [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) su [`Bold`](xref:Xamarin.Forms.FontAttributes.Bold)un [`Italic`](xref:Xamarin.Forms.FontAttributes.Italic)membro [`None`](xref:Xamarin.Forms.FontAttributes.None)di queste enumerazioni, , , o . È possibile `Bold` combinare `Italic` i membri e con l'operatore OR bit per bit di C.

## <a name="formatted-text"></a>Testo formattato

In tutti gli esempi finora, l'intero `Label` testo visualizzato da è stato formattato in modo uniforme. Per variare la formattazione all'interno `Text` di `Label`una stringa di testo, non impostare la proprietà di . Impostare invece la proprietà [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) [`FormattedString`](xref:Xamarin.Forms.FormattedString)su un oggetto di tipo .

`FormattedString`dispone [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) di una proprietà [`Span`](xref:Xamarin.Forms.Span) che è una raccolta di oggetti. Ogni `Span` oggetto dispone [`Text`](xref:Xamarin.Forms.Span.Text) [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily)di [`FontSize`](xref:Xamarin.Forms.Span.FontSize) [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes)proprietà [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor), [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) , , , e .

Nell'esempio [**VariableFormattedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) `FormattedText` viene illustrato l'utilizzo della proprietà per una singola riga di testo e [**VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) illustra la tecnica per un intero paragrafo, come illustrato di seguito:

[![Tripla schermata del paragrafo in formato variabile](images/ch03fg06-small.png "Testo etichetta formattata variabile")](images/ch03fg06-large.png#lightbox "Testo etichetta formattata variabile")

Il programma [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) `Label` utilizza `FormattedString` un singolo e un oggetto per visualizzare tutte le dimensioni dei caratteri denominati per ogni piattaforma.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 3 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Esempi del capitolo 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Capitolo 3 Esempi di F](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Etichetta](~/xamarin-forms/user-interface/text/label.md)
- [Utilizzo dei colori](~/xamarin-forms/user-interface/colors.md)
