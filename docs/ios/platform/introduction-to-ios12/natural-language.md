---
title: Uso del Framework del linguaggio naturale con Novell. iOS
description: Questo documento descrive il Framework del linguaggio naturale. Introdotta in iOS 12, il Framework del linguaggio naturale è l'API iOS preferita da usare per il riconoscimento della lingua, le parti dell'identificazione vocale e il riconoscimento delle entità denominate.
ms.prod: xamarin
ms.assetid: 126C8764-F873-4EB9-98A3-D82AB5689111
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 08/20/2018
ms.openlocfilehash: 7b0d07f2153a0395146506a371631e0bcf75ebef
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292917"
---
# <a name="using-the-natural-language-framework-with-xamarinios"></a>Uso del Framework del linguaggio naturale con Novell. iOS

Introdotta in iOS 12, il Framework del linguaggio naturale consente l'elaborazione del linguaggio naturale nel dispositivo. Supporta il riconoscimento della lingua, la suddivisione in token e l'assegnazione di tag. La suddivisione in token suddivide il testo in parole, frasi o paragrafi del componente; l'assegnazione di tag identifica parti di sintesi vocale, persone, luoghi e organizzazioni.

Il Framework del linguaggio naturale può anche usare modelli di base ML personalizzati per classificare e contrassegnare il testo in contesti specializzati.

La classe [NSLinguisticTagger](xref:Foundation.NSLinguisticTagger) è ancora disponibile. Tuttavia, il Framework del linguaggio naturale è il meccanismo preferito da usare per l'elaborazione del linguaggio naturale.

## <a name="sample-app-xamarinnl"></a>App di esempio: XamarinNL

Per informazioni su come usare il Framework del linguaggio naturale con Novell. iOS, vedere l'app di [esempio XamarinNL](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-xamarinnl).
Questa app di esempio illustra come usare il Framework del linguaggio naturale per:

- [Riconosci le lingue](#recognizing-languages).
- [Tokenize testo in parole e frasi](#tokenizing-text-into-words-sentences-and-paragraphs).
- [Contrassegna le entità denominate e le parti di sintesi vocale](#tagging-named-entities-and-parts-of-speech).

## <a name="recognizing-languages"></a>Riconoscimento delle lingue

La scheda **riconoscimento** dell'app di esempio illustra come usare un[`NLLanguageRecognizer`](xref:NaturalLanguage.NLLanguageRecognizer)
per determinare la lingua di un blocco di testo.

> [!NOTE]
> Il riconoscimento della lingua è un tipo specifico di classificazione di testo. Il Framework del linguaggio naturale supporta anche la classificazione personalizzata del testo tramite i modelli di base di ML forniti dallo sviluppatore. Per ulteriori informazioni, vedere la sessione Introduzione al [Framework del linguaggio naturale](https://developer.apple.com/videos/play/wwdc2018/713/) da WWDC 2018.

### <a name="dominant-language"></a>Lingua dominante

Toccare il pulsante **lingua** per identificare la lingua dominante nell'input dell'utente.

Il `HandleDetermineLanguageButtonTap` metodo`LanguageRecognizerViewController` di usa il metodo[`GetDominantLanguage`](xref:NaturalLanguage.NLLanguageRecognizer.GetDominantLanguage*)
Metodo di un `NLLanguageRecognizer` oggetto per recuperare l'oggetto[`NLLanguage`](xref:NaturalLanguage.NLLanguage)
per la lingua principale trovata nel testo:

```csharp
partial void HandleDetermineLanguageButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        NLLanguage lang = NLLanguageRecognizer.GetDominantLanguage(UserInput.Text);
        DominantLanguageLabel.Text = lang.ToString();
    }
}
```

### <a name="language-probabilities"></a>Probabilità del linguaggio

Toccare il pulsante **probabilità lingua** per recuperare un elenco di ipotesi di lingua per l'input dell'utente.

Il `HandleLanguageProbabilitiesButtonTap` metodo `NLLanguageRecognizer` della classe crea un'istanza di un oggetto e lo richiede `LanguageRecognizerViewController`[`Process`](xref:NaturalLanguage.NLLanguageRecognizer.Process*)
testo dell'utente. Chiama quindi il riconoscimento della lingua[`GetNativeLanguageHypotheses`](xref:NaturalLanguage.NLLanguageRecognizer.GetNativeLanguageHypotheses*)
Metodo, che recupera un dizionario di linguaggi e le probabilità associate. La `LanguageRecognizerTableViewController` classe esegue quindi il rendering di questi linguaggi e probabilità.

```csharp
partial void HandleLanguageProbabilitiesButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var recognizer = new NLLanguageRecognizer();
        recognizer.Process(UserInput.Text);
        NSDictionary<NSString, NSNumber> probabilities = recognizer.GetNativeLanguageHypotheses(10);
        PerformSegue(ShowLanguageProbabilitiesSegue, this);
    }
}
```

I `NLLanguage` valori possibili includono:

- `Amharic`
- `Arabic`
- `Armenian`
- `Bengali`
- `Bulgarian`
- `Burmese`
- `Catalan`
- `Cherokee`
- `Croatian`
- `Czech`
- `Danish`
- `Dutch`
- `English`
- `Finnish`
- `French`
- `Georgian`
- `German`
- `Greek`
- `Gujarati`
- `Hebrew`
- `Hindi`
- `Hungarian`
- `Icelandic`
- `Indonesian`
- `Italian`
- `Japanese`
- `Kannada`
- `Khmer`
- `Korean`
- `Lao`
- `Malay`
- `Malayalam`
- `Marathi`
- `Mongolian`
- `Norwegian`
- `Oriya`
- `Persian`
- `Polish`
- `Portuguese`
- `Punjabi`
- `Romanian`
- `Russian`
- `SimplifiedChinese`
- `Sinhalese`
- `Slovak`
- `Spanish`
- `Swedish`
- `Tamil`
- `Telugu`
- `Thai`
- `Tibetan`
- `TraditionalChinese`
- `Turkish`
- `Ukrainian`
- `Undetermined`
- `Urdu`
- `Vietnamese`

Un elenco completo delle lingue supportate è disponibile come parte del[`NLLanguage`](xref:NaturalLanguage.NLLanguage)
documentazione dell'API enum.

## <a name="tokenizing-text-into-words-sentences-and-paragraphs"></a>Suddivisione in token testo in parole, frasi e paragrafi

La scheda **Tokenizer** dell'app di esempio illustra come separare un blocco di testo nelle parole o nelle frasi del componente con un [`NLTokenizer`](xref:NaturalLanguage.NLTokenizer).

Toccare il pulsante **parole** o **frasi** per recuperare un elenco di token. Ogni token è associato a una parola o a una frase nel testo originale.

`ShowTokens`suddivide l'input dell'utente in token chiamando il parametro[`GetTokens`](xref:NaturalLanguage.NLTokenizer.GetTokens*)
Metodo di un `NLTokenizer`oggetto. Questo metodo restituisce una matrice di[`NSValue`](xref:Foundation.NSValue)
oggetti, ognuno dei quali `NSRange` racchiude un valore corrispondente a un token nel testo originale.

```csharp
void ShowTokens(NLTokenUnit unit)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tokenizer = new NLTokenizer(unit);
        tokenizer.String = UserInput.Text;
        var range = new NSRange(0, UserInput.Text.Length);
        NSValue[] tokens = tokenizer.GetTokens(range);
        PerformSegue(ShowTokensSegue, this);
    }
}
```

`LanguageTokenizerTableViewController`esegue il rendering di un singolo token in ogni cella della tabella. Estrae un oggetto `NSRange` da un token `NSValue`, trova la stringa corrispondente nel testo originale e imposta un'etichetta nella cella di visualizzazione tabella:

```csharp
public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
    var cell = TableView.DequeueReusableCell(TokenCell);
    NSRange range = Tokens[indexPath.Row].RangeValue;
    cell.TextLabel.Text = Text.Substring((int)range.Location, (int)range.Length);
    return cell;
}
```

## <a name="tagging-named-entities-and-parts-of-speech"></a>Assegnazione di tag a entità denominate e parti del discorso

La scheda **tag** dell'app di esempio XamarinNL illustra come usare il[`NLTagger`](xref:NaturalLanguage.NLTagger)
classe per associare le categorie ai token di una stringa di input.
Il Framework del linguaggio naturale include il supporto incorporato per il riconoscimento di persone, luoghi, organizzazioni e parti del discorso.

> [!NOTE]
> Il Framework del linguaggio naturale supporta anche gli schemi di assegnazione di tag personalizzati tramite i modelli di base di ML forniti dallo sviluppatore. Per ulteriori informazioni, vedere la sessione Introduzione al [Framework del linguaggio naturale](https://developer.apple.com/videos/play/wwdc2018/713/) da WWDC 2018.

Toccare il pulsante entità o **parti del discorso** **denominate** per recuperare:

- Matrice di oggetti `NSValue` , ognuno dei quali viene `NSRange` sottoposta a wrapping per un token nel testo originale.
- Matrice di [`NLTag`](xref:NaturalLanguage.NLTag) valori: categorie per i `NSValue` token in corrispondenza dello stesso indice di matrice.

`LanguageTaggerViewController` In`HandleNamedEntitiesButtonTap` e ogni chiamata [,passando`NLTagScheme`](xref:NaturalLanguage.NLTagScheme) `NLTagScheme.NameType` un oggetto, ovvero (per le parti di sintesi vocale) o (per le entità denominate). `NLTagScheme.LexicalClass` `ShowTags` `HandlePartsOfSpeechButtonTap`

`ShowTags`Crea un `NLTagger`oggetto, creandone un'istanza con una `NLTagScheme` matrice di tipi per cui verrà eseguita una query (in questo caso, `NLTagScheme` solo il valore passato). USA quindi il[`GetTags`](xref:NaturalLanguage.NLTagger.GetTags*)
Metodo sull'oggetto `NLTagger` per determinare i tag relativi al testo nell'input dell'utente.

```csharp
void ShowTags(NLTagScheme tagScheme)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tagger = new NLTagger(new NLTagScheme[] { tagScheme });
        var range = new NSRange(0, UserInput.Text.Length);
        tagger.String = UserInput.Text;

        NLTag[] tags = tagger.GetTags(range, NLTokenUnit.Word, tagScheme, NLTaggerOptions.OmitWhitespace, out NSValue[] ranges);
        NSValue[] tokenRanges = ranges;
        detailViewTitle = tagScheme == NLTagScheme.NameType ? "Named Entities" : "Parts of Speech";

        PerformSegue(ShowEntitiesSegue, this);
    }
}
```

I tag vengono quindi visualizzati in una tabella da `LanguageTaggerTableViewController`.

I `NLTag` valori possibili includono:

- `Adjective`
- `Adverb`
- `Classifier`
- `CloseParenthesis`
- `CloseQuote`
- `Conjunction`
- `Dash`
- `Determiner`
- `Idiom`
- `Interjection`
- `Noun`
- `Number`
- `OpenParenthesis`
- `OpenQuote`
- `OrganizationName`
- `Other`
- `OtherPunctuation`
- `OtherWhitespace`
- `OtherWord`
- `ParagraphBreak`
- `Particle`
- `PersonalName`
- `PlaceName`
- `Preposition`
- `Pronoun`
- `Punctuation`
- `SentenceTerminator`
- `Verb`
- `Whitespace`
- `Word`
- `WordJoiner`

Un elenco completo dei tag supportati è disponibile come parte del[`NLTag`](xref:NaturalLanguage.NLTag)
documentazione dell'API enum.

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio-XamarinNL](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-xamarinnl)
- [Introduzione al Framework del linguaggio naturale](https://developer.apple.com/videos/play/wwdc2018/713/)
- [Linguaggio naturale (Apple)](https://developer.apple.com/documentation/naturallanguage?language=objc)
