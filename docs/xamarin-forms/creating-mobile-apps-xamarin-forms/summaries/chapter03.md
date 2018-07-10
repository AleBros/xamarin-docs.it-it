---
title: Riepilogo del capitolo 3. Dettagli della gestione del testo
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 3. Dettagli della gestione del testo'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 5843147b82837f1a8677d8be48a8e1ca92db1a75
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935416"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Riepilogo del capitolo 3. Dettagli della gestione del testo

In questo capitolo esamina le [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) vista in modo più approfondito, inclusi i tipi di carattere, colore e la formattazione.

## <a name="wrapping-paragraphs"></a>Ritorno a capo paragrafi

Quando la [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) proprietà della `Label` contiene testo lungo, `Label` automaticamente ne esegue il wrapping su più righe come dimostrato dal [ **Baskervilles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) esempio. È possibile incorporare i codici Unicode, ad esempio '\u2014' per il lineetta o c# caratteri, ad esempio '\r' per interrompere a una nuova riga.

Quando la [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) le proprietà di un `Label` sono impostate su `LayoutOptions.Fill`, alle dimensioni complessive del `Label` è regolato dallo spazio che del relativo contenitore rende disponibili. Il `Label` è detta *vincolata*. Le dimensioni del `Label` è la dimensione del relativo contenitore.

Quando il `HorizontalOptions` e `VerticalOptions` le proprietà vengono impostate sui valori diversi da `LayoutOptions.Fill`, alle dimensioni del `Label` è disciplinato dallo spazio necessario per il rendering del testo, fino alla dimensione del relativo contenitore rese disponibile per il `Label`. Il `Label` è detta *non vincolato* e determina la base alle proprie dimensioni.

(Nota: I termini *vincolata* e *non vincolato* potrebbero essere poco intuitivo, perché una vista non vincolata viene in genere inferiore a una visualizzazione vincolata. Inoltre, le presenti condizioni non vengono utilizzate in modo coerente nei primi capitoli del libro.)

Una vista, ad esempio un `Label` può essere vincolato in una dimensione e non vincolato in altro. Oggetto `Label` verranno solo testo a capo su più righe se esiste un vincolo in senso orizzontale.

Se un `Label` è vincolato, potrebbe essere occupano molto più spazio rispetto a quelle necessarie per il testo. Il testo può essere posizionato all'interno dell'area complessiva del `Label`. Impostare il [ `HorizontalTextAlignment` ](xref:Xamarin.Forms.Label.HorizontalTextAlignment) proprietà a un membro del [ `TextAlignment` ](xref:Xamarin.Forms.TextAlignment) enumerazione ([`Start`](xref:Xamarin.Forms.TextAlignment.Start), [ `Center` ](xref:Xamarin.Forms.TextAlignment.Center), oppure [ `End` ](xref:Xamarin.Forms.TextAlignment.Center)) per controllare l'allineamento di tutte le righe del paragrafo. Il valore predefinito è `Start` e Allinea a sinistra il testo.

Impostare il [ `VerticalTextAlignment` ](xref:Xamarin.Forms.Label.VerticalTextAlignment) proprietà a un membro del `TextAlignment` enumerazione per posizionare il testo all'inizio, center o nella parte inferiore dell'area occupata dal `Label`.

Impostare il [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) proprietà a un membro del [ `LineBreakMode` ](xref:Xamarin.Forms.LineBreakMode) enumerazione ([`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap), [ `CharacterWrap` ](xref:Xamarin.Forms.LineBreakMode.CharacterWrap), [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode.NoWrap), [ `HeadTruncation` ](xref:Xamarin.Forms.LineBreakMode.HeadTruncation), [ `MiddleTruncation` ](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation), oppure [ `TailTruncation` ](xref:Xamarin.Forms.LineBreakMode.TailTruncation)) per controllo modalità più righe di un'interruzione di paragrafo o vengono troncati.

## <a name="text-and-background-colors"></a>Colori di sfondo e testo

Impostare il [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/) e [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/) le proprietà di `Label` a [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) i valori per controllare il colore del testo e sfondo.

Il `BackgroundColor` si applica allo sfondo dell'intera area occupata dal `Label`. A seconda le `HorizontalOptions` e `VerticalOptions` proprietà, che potrebbero essere notevolmente più grande dell'area richiesta per visualizzare il testo delle dimensioni. È possibile usare colori per sperimentare con diversi valori di `HorizontalOptions`, `VerticalOptions`, `HorizontalExeAlignment`, e `VerticalTextAlignment` per vedere come cambia le dimensioni e posizione del `Label`e le dimensioni e posizione del testo all'interno di `Label`.

## <a name="the-color-structure"></a>La struttura di colore

Il [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) struttura consente di specificare i colori come valori rosso-verde-blu (RGB) o i valori di Tonalità-saturazione-luminosità HSL () o con un nome di colore. Un canale alfa è disponibile anche indicare la trasparenza.

Usare un `Color` costruttore per specificare:

- un [gradazione di grigio](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/)
- un [valore RGB](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/)
- un [valore RGB con trasparenza](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/System.Double/)

Gli argomenti sono `double` valori compresi tra 0 e 1.

È anche possibile usare diversi metodi statici per creare `Color` valori:

- [`Color.FromRgb`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Double/System.Double/System.Double/) per `double` valori RGB da 0 a 1
- [`Color.FromRgb`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Int32/System.Int32/System.Int32/) per i valori RGB integer compreso tra 0 e 255.
- [`Color.FromRgba`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgba/p/System.Double/System.Double/System.Double/System.Double/) per `double` valori RGB con trasparenza
- [`Color.FromRgba`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgba/p/System.Int32/System.Int32/System.Int32/System.Int32/) per i valori RGB di interi con trasparenza
- [`Color.FromHsla`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHsla/p/System.Double/System.Double/System.Double/System.Double/) per `double` HSL (valori) con la trasparenza
- [`Color.FromUint`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromUint/p/System.UInt32/) per un `uint` valore calcolato come (256 + B * (256 + G * (256 + R * A)))
- [`Color.FromHex`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHex/p/System.String/) per un `string` formato di cifre esadecimali nel formato "#AARRGGBB" o "#RRGGBB" o "#ARGB" o "#RGB", dove ogni lettera corrisponde a una cifra esadecimale per i valori alfa, rosso, verdi e blu canali. Questo metodo è primario usata per le conversioni di colore XAML come descritto nella [capitolo 7, XAML e codice](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

Una volta creato, un `Color` valore non è modificabile. È possibile ottenere le caratteristiche del colore tramite le proprietà seguenti:

- [`R`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.R/)
- [`G`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.G/)
- [`B`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.B/)
- [`A`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.A/)
- [`Hue`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Hue/)
- [`Saturation`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Saturation/)
- [`Luminosity`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Luminosity/)

Questi sono tutti `double` valori compresi tra 0 e 1.

`Color` definisce inoltre 240 campi statici pubblici sola lettura per i colori più comuni. Al momento che è stato scritto il libro, erano disponibili solo 17 colori più comuni.

Un altro campo statico pubblico ReadOnly definisce un colore con tutti i canali di colore impostati su zero:

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

Diversi metodi di istanza consentono la modifica di un colore esistente per creare un nuovo colore:

- [`AddLuminosity`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.AddLuminosity/p/System.Double/)
- [`MultiplyAlpha`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.MultiplyAlpha/p/System.Double/)
- [`WithHue`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithHue/p/System.Double/)
- [`WithLuminosity`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithLuminosity/p/System.Double/)
- [`WithSaturation`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithSaturation/p/System.Double/)

Infine, due proprietà statiche di sola lettura definire il valore di colore speciali:

- [`Color.Default`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/), tutti i canali è impostato su &ndash;1
- [`Color.Accent`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Accent/)

`Color.Default` Consente di applicare la combinazione di colori della piattaforma e, di conseguenza, ha un significato diverso in contesti diversi su piattaforme diverse. Per impostazione predefinita le combinazioni di colori della piattaforma sono:

- iOS: scuro testo sullo sfondo chiaro
- Android: Chiaro di testo su uno sfondo scuro (nel libro) o scuro su uno sfondo chiaro (per Material Design tramite AppCompat nel **master** ramo del repository di codice di esempio)
- Piattaforma UWP: Testo scuro su uno sfondo chiaro
- Windows 8.1: Chiaro su uno sfondo scuro
- Windows Phone 8.1: Chiaro su uno sfondo scuro

Il `Color.Accent` valore i risultati in un colore specifico della piattaforma (e talvolta selezionabili dall'utente) che è visibile su uno sfondo chiaro o scuro.

## <a name="changing-the-application-color-scheme"></a>Modifica della combinazione di colori dell'applicazione

Le diverse piattaforme hanno una combinazione di colori predefiniti, come mostrato nell'elenco precedente.

Quando la destinazione è Android, è possibile passare a uno schema dark-sul-light specificando un tema chiaro nel file Android.Manifest.xml o dal [aggiunta AppCompat e Material Design](~/xamarin-forms/platform/android/appcompat.md).

Per le piattaforme Windows, il tema colori viene in genere selezionato dall'utente, ma è possibile aggiungere un `RequestedTheme` attributo è impostata né `Light` o `Dark` nel file app della piattaforma. Per impostazione predefinita, il file app. XAML nel progetto UWP contiene un `RequestedTheme` attributo è impostato su `Light`.

## <a name="font-sizes-and-attributes"></a>Gli attributi e le dimensioni dei caratteri

Impostare il [ `FontFamily` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontFamily/) proprietà `Label` a una stringa, ad esempio "Times Roman" per selezionare una famiglia di caratteri. Tuttavia, è necessario specificare una famiglia di caratteri che è supportata sulla piattaforma specifica e le piattaforme non sono coerenti in questo senso.

Impostare il [ `FontSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontSize/) proprietà della `Label` a un `double` per specificare l'altezza approssimativa del tipo di carattere. Visualizzare [capitolo 5, gestione delle dimensioni](chapter05.md), per altri dettagli su come scegliere in modo intelligente le dimensioni dei caratteri.

In alternativa è possibile ottenere una delle diverse dimensioni predefinite del carattere dipendente dalla piattaforma. Statica [ `Device.GetNamedSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/System.Type/) (metodo) e [overload](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/Xamarin.Forms.Element/) restituiscono entrambi un `double` valore delle dimensioni del carattere appropriato per la piattaforma basate sui membri del [ `NamedSize` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NamedSize/)enumerazione ([`Default`](xref:Xamarin.Forms.NamedSize.Default), [ `Micro` ](xref:Xamarin.Forms.NamedSize.Micro), [ `Small` ](xref:Xamarin.Forms.NamedSize.Small), [ `Medium` ](xref:Xamarin.Forms.NamedSize.Medium),  e [ `Large` ](xref:Xamarin.Forms.NamedSize.Large)). Il valore restituito dal `Medium` membro non è necessariamente identico `Default`. Il [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) esempio visualizza il testo con questi denominato sizes.

Impostare il [ `FontAttributes` ](xref:Xamarin.Forms.Label.FontAttributes) proprietà della `Label` a un membro di questi [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes) enumerazione [ `Bold` ](xref:Xamarin.Forms.FontAttributes.Bold), [ `Italic` ](xref:Xamarin.Forms.FontAttributes.Italic), oppure [ `None` ](xref:Xamarin.Forms.FontAttributes.None). È possibile combinare le `Bold` e `Italic` membri con il linguaggio c# operatore OR.

## <a name="formatted-text"></a>Testo formattato

In tutti gli esempi finora, l'intero testo visualizzato per il `Label` è stato formattato in modo uniforme. Per variare la formattazione all'interno di una stringa di testo, non impostare il `Text` proprietà di `Label`. Al contrario, impostare il [ `FormattedText` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FormattedText/) proprietà a un oggetto di tipo [ `FormattedString` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FormattedString/).

`FormattedString` è un [ `Spans` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FormattedString.Spans/) che è una raccolta di proprietà [ `Span` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Span/) oggetti. Ciascuna `Span` oggetto dispone di propri [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.Text/), [ `FontFamily` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontFamily/), [ `FontSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontSize/), [ `FontAttributes` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontAttributes/), [ `ForegroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.ForegroundColor/), e [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.BackgroundColor/) proprietà.

Il [ **VariableFormattedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) esempio viene illustrato come utilizzare il `FormattedText` proprietà per una singola riga di testo, e [ **VariableFormattedParagraph** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) illustra la tecnica per un intero paragrafo, come illustrato di seguito:

[![Screenshot triplo della variabile formattato paragrafo](images/ch03fg06-small.png "variabile di testo dell'etichetta formattato")](images/ch03fg06-large.png#lightbox "variabile formato testo dell'etichetta")

Il [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) programma Usa una singola `Label` e un `FormattedString` oggetto per visualizzare tutte le dimensioni del tipo di carattere denominato per ogni piattaforma.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 3 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Capitolo 3 campioni](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Esempi di capitolo 3 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Etichetta](~/xamarin-forms/user-interface/text/label.md)
- [Uso dei colori](~/xamarin-forms/user-interface/colors.md)
