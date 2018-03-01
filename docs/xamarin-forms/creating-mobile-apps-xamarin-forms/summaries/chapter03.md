---
title: "Riepilogo del capitolo 3. Più approfondita nel testo"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 9d283a4136a7cdfe39ea0b2da65273332fd47b00
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Riepilogo del capitolo 3. Più approfondita nel testo

In questo capitolo viene esaminata la [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) vista in modo più approfondito, inclusi tipi di carattere, colore e la formattazione.

## <a name="wrapping-paragraphs"></a>Ritorno a capo paragrafi

Quando il [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) proprietà di `Label` contiene testo lungo, `Label` automaticamente ne esegue il wrapping di più righe come illustrato di [ **Baskervilles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) esempio. È possibile incorporare i codici di Unicode, ad esempio '\u2014' per la lineetta o caratteri c#, ad esempio "\r" interruzione in una nuova riga.

Quando il [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) le proprietà di un `Label` sono impostate su `LayoutOptions.Fill`, la dimensione complessiva il `Label` è disciplinato dallo spazio che del relativo contenitore rende disponibili. Il `Label` è detto *vincolata*. Le dimensioni del `Label` è la dimensione del relativo contenitore.

Quando il `HorizontalOptions` e `VerticalOptions` sono impostate su valori diversi da `LayoutOptions.Fill`, la dimensione il `Label` è disciplinato dallo spazio necessario per il rendering del testo, fino alle dimensioni rese disponibile per il contenitore di `Label`. Il `Label` è detto *non vincolato* e determina le proprie dimensioni.

(Nota: le condizioni di *vincolata* e *non vincolato* potrebbe essere poco plausibile, poiché una vista è in genere inferiore a una vista limitata. Inoltre, queste condizioni non vengono utilizzate in modo coerente nei primi capitoli del libro.)

Una vista, ad esempio un `Label` può essere vincolato in una dimensione e non vincolato in altro. Oggetto `Label` verrà solo testo a capo su più righe se questo è vincolato in senso orizzontale.

Se un `Label` è vincolato, potrebbe essere occupano molto più spazio rispetto a quelle necessarie per il testo. Il testo può essere posizionato all'interno dell'area di complessiva di `Label`. Impostare il [ `HorizontalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.HorizontalTextAlignment/) proprietà a un membro del [ `TextAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextAlignment/) enumerazione ([`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Start/), [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Center/), o [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Center/)) per controllare l'allineamento di tutte le righe del paragrafo. Il valore predefinito è `Start` e il testo viene allineato a sinistra.

Impostare il [ `VerticalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.VerticalTextAlignment/) proprietà a un membro del `TextAlignment` enumerazione per posizionare il testo all'inizio, center o inferiore dell'area occupata dal `Label`.

Impostare il [ `LineBreakMode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.LineBreakMode/) proprietà a un membro del [ `LineBreakMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LineBreakMode/) enumerazione ([`WordWrap`](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.WordWrap/), [ `CharacterWrap` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.CharacterWrap/), [ `NoWrap` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.NoWrap/), [ `HeadTruncation` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.HeadTruncation/), [ `MiddleTruncation` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.MiddleTruncation/), o [ `TailTruncation` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.TailTruncation/)) per controllo modalità di più righe di un'interruzione di paragrafo o vengono troncati.

## <a name="text-and-background-colors"></a>Colori di sfondo e di testo

Impostare il [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/) e [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/) le proprietà di `Label` a [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) i valori per controllare il colore del testo di sfondo.

Il `BackgroundColor` Applica sfondo per l'intera area occupata dal `Label`. A seconda di `HorizontalOptions` e `VerticalOptions` proprietà, che dimensioni potrebbero essere molto più grande dell'area necessaria per visualizzare il testo. È possibile usare un colore a sperimentare con diversi valori di `HorizontalOptions`, `VerticalOptions`, `HorizontalExeAlignment`, e `VerticalTextAlignment` a di controllare le dimensioni e la posizione del `Label`e le dimensioni e posizione del testo all'interno di `Label`.

## <a name="the-color-structure"></a>La struttura di colore

Il [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) struttura consente di specificare i colori come Red-Green-Blue valori RGB () o i valori di Tonalità-saturazione-luminosità (HSL) o con un nome di colore. Un canale alfa è disponibile anche indicare la trasparenza.

Utilizzare un `Color` costruttore per specificare:

- un [sfumature di grigio.](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/)
- un [valore RGB](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/)
- un [valore RGB con trasparenza](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/System.Double/)

Gli argomenti sono `double` valori compresi tra 0 e 1.

È inoltre possibile utilizzare diversi metodi statici per creare `Color` valori:

- [`Color.FromRgb`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Double/System.Double/System.Double/) per `double` valori RGB da 0 a 1
- [`Color.FromRgb`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Int32/System.Int32/System.Int32/) per i valori RGB integer compreso tra 0 e 255.
- [`Color.FromRgba`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgba/p/System.Double/System.Double/System.Double/System.Double/) per `double` valori RGB con trasparenza
- [`Color.FromRgba`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgba/p/System.Int32/System.Int32/System.Int32/System.Int32/) per i valori RGB integer con trasparenza
- [`Color.FromHsla`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHsla/p/System.Double/System.Double/System.Double/System.Double/) per `double` HSL (valori) con trasparenza
- [`Color.FromUint`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromUint/p/System.UInt32/) per un `uint` valore calcolato come (B + 256 * (C + 256 * (R + 256 * A)))
- [`Color.FromHex`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHex/p/System.String/) per un `string` formato di cifre esadecimali nel formato "#AARRGGBB" o "#RRGGBB" o "#ARGB" o "#RGB", dove ogni lettera corrisponde a una cifra esadecimale per il canale alfa, rosso, verdi e blu canali. Questo metodo è primario utilizzato per le conversioni di colore XAML, come descritto in [capitolo 7, il codice XAML e codice](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

Una volta creato, un `Color` valore non è modificabile. È possibile ottenere le caratteristiche del colore tramite le proprietà seguenti:

- [`R`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.R/)
- [`G`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.G/)
- [`B`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.B/)
- [`A`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.A/)
- [`Hue`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Hue/)
- [`Saturation`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Saturation/)
- [`Luminosity`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Luminosity/)

Questi sono tutti `double` valori compresi tra 0 e 1.

`Color` definisce inoltre 240 pubblici statici campi di sola lettura per i colori più comuni. Al momento che il libro è stato scritto, sono disponibili solo 17 colori comuni.

Un altro campo statico pubblico sola lettura definisce un colore con tutti i canali di colore impostati su zero:

- [`Color.Transparent`](https://developer.xamarin.com/api/field/Xamarin.Forms.Color.Transparent/)

Diversi metodi di istanza che consentono la modifica di un colore esistente per creare un nuovo colore:

- [`AddLuminosity`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.AddLuminosity/p/System.Double/)
- [`MultiplyAlpha`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.MultiplyAlpha/p/System.Double/)
- [`WithHue`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithHue/p/System.Double/)
- [`WithLuminosity`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithLuminosity/p/System.Double/)
- [`WithSaturation`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithSaturation/p/System.Double/)

Infine, due proprietà statiche di sola lettura e definire il valore di colore speciali:

- [`Color.Default`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/), tutti i canali impostati su & #x 2013; 1
- [`Color.Accent`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Accent/)

`Color.Default` è finalizzato ad applicare la combinazione di colori della piattaforma e di conseguenza ha un significato diverso in contesti diversi su piattaforme diverse. Per impostazione predefinita le combinazioni di colori della piattaforma sono:

- iOS: testo uno sfondo scuro
- Android: Chiaro su uno sfondo scuro (in book) o il testo scuro su uno sfondo di colore (per la progettazione di materiale tramite delle applicazioni nel **master** ramo del repository di codice di esempio)
- Piattaforma UWP: Testo scuro su uno sfondo
- Windows 8.1: Chiaro su uno sfondo scuro
- Windows Phone 8.1: Chiaro su uno sfondo scuro

Il `Color.Accent` valore risultati in un colore specifico della piattaforma (e talvolta selezionabili dall'utente) che è visibile su uno sfondo chiaro o scuro.

## <a name="changing-the-application-color-scheme"></a>Modifica della combinazione di colori dell'applicazione

Diverse piattaforme hanno una combinazione di colori predefiniti, come illustrato nell'elenco precedente.

Quando la destinazione è Android, è possibile passare a uno schema in dark-light specificando un tema chiaro nel file Android.Manifest.xml o da [aggiunta delle applicazioni e la progettazione di materiale](~/xamarin-forms/platform/android/appcompat.md).

Per le piattaforme Windows, il tema colori è normalmente selezionato dall'utente, ma è possibile aggiungere un `RequestedTheme` attributo impostato su `Light` o `Dark` nel file app della piattaforma. Per impostazione predefinita, il file app. XAML nel progetto UWP contiene un `RequestedTheme` attributo impostato su `Light`.

## <a name="font-sizes-and-attributes"></a>Gli attributi e le dimensioni dei caratteri

Impostare il [ `FontFamily` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontFamily/) proprietà `Label` in una stringa, ad esempio "Times Roman" per selezionare una famiglia di caratteri. Tuttavia, è necessario specificare una famiglia di caratteri che è supportata sulla piattaforma particolare e le piattaforme non sono coerenti in proposito.

Impostare il [ `FontSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontSize/) proprietà di `Label` per un `double` per specificare l'altezza approssimativa del tipo di carattere. Vedere [capitolo 5, gestione delle dimensioni](chapter05.md), per ulteriori informazioni sulla scelta in modo intelligente le dimensioni dei caratteri.

In alternativa è possibile ottenere una delle diverse dimensioni di tipo di carattere predefinito dipendente dalla piattaforma. Statica [ `Device.GetNamedSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/System.Type/) (metodo) e [overload](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/Xamarin.Forms.Element/) restituiscono entrambi un `double` valore delle dimensioni del carattere appropriata per la piattaforma basate sui membri del [ `NamedSize` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NamedSize/)enumerazione ([`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Default/), [ `Micro` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Micro/), [ `Small` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Small/), [ `Medium` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Medium/),  e [ `Large` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Large/)). Il valore restituito dal `Medium` membro non è necessariamente identico `Default`. Il [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) esempio viene visualizzato testo con questi denominato sizes.

Impostare il [ `FontAttributes` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontAttributes/) proprietà di `Label` a un membro di questi [ `FontAttributes` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FontAttributes/) enumerazione [ `Bold` ](https://developer.xamarin.com/api/field/Xamarin.Forms.FontAttributes.Bold/), [ `Italic` ](https://developer.xamarin.com/api/field/Xamarin.Forms.FontAttributes.Italic/), o [ `None` ](https://developer.xamarin.com/api/field/Xamarin.Forms.FontAttributes.None/). È possibile combinare la `Bold` e `Italic` membri con il linguaggio c# operatore OR.

## <a name="formatted-text"></a>Testo formattato

In tutti gli esempi fino a questo punto, tutto il testo visualizzato per il `Label` è stato formattato in modo uniforme. Per variare la formattazione all'interno di una stringa di testo, non vengono impostate le `Text` proprietà `Label`. Al contrario, impostare il [ `FormattedText` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FormattedText/) proprietà a un oggetto di tipo [ `FormattedString` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FormattedString/).

`FormattedString` è un [ `Spans` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FormattedString.Spans/) proprietà che è una raccolta di [ `Span` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Span/) oggetti. Ogni `Span` oggetto dispone di una propria [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.Text/), [ `FontFamily` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontFamily/), [ `FontSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontSize/), [ `FontAttributes` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontAttributes/), [ `ForegroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.ForegroundColor/), e [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.BackgroundColor/) proprietà.

Il [ **VariableFormattedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) esempio viene illustrato l'utilizzo di `FormattedText` proprietà per una singola riga di testo, e [ **VariableFormattedParagraph** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) la tecnica per un intero paragrafo, di seguito viene illustrato come illustrato di seguito:

[![Schermata triplo della variabile formattato paragrafo](images/ch03fg06-small.png "testo variabile in formato etichetta")](images/ch03fg06-large.png "testo variabile in formato etichetta")

Il [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) programma utilizza una singola `Label` e `FormattedString` oggetto per visualizzare tutte le dimensioni del carattere denominato per ogni piattaforma.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 3 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Capitolo 3 campioni](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Esempi di capitolo 3 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Etichetta](~/xamarin-forms/user-interface/text/label.md)
- [Utilizzo dei colori](~/xamarin-forms/user-interface/colors.md)
