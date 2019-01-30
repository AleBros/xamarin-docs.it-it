---
title: Usando il framework del linguaggio naturale con xamarin. IOS
description: Questo documento descrive il Framework del linguaggio naturale. Introdotto in iOS 12, il framework del linguaggio naturale è l'API di iOS preferito da usare per il riconoscimento della lingua, identificazione di parti del discorso e riconoscimento di entità denominate.
ms.prod: xamarin
ms.assetid: 126C8764-F873-4EB9-98A3-D82AB5689111
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/20/2018
ms.openlocfilehash: 0b3fb7d467ae64e2cbfdb61644b1537bc5ae1161
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233068"
---
# <a name="using-the-natural-language-framework-with-xamarinios"></a>Usando il framework del linguaggio naturale con xamarin. IOS

Introdotto in iOS 12, il framework del linguaggio naturale consente l'elaborazione in linguaggio naturale su dispositivo. Supporta il riconoscimento della lingua, la suddivisione in token e l'assegnazione di tag. Tokenizzazione suddivide il testo in relativo componente parole, frasi o paragrafi. l'assegnazione di tag identifica le parti del discorso, persone, luoghi e organizzazioni.

Il framework del linguaggio naturale anche possibile usare modelli personalizzati Core ML per classificare ed etichettare il testo in contesti specializzati.

Il [NSLinguisticTagger](xref:Foundation.NSLinguisticTagger) classe sia ancora disponibile. Tuttavia, il framework del linguaggio naturale è il meccanismo preferito da usare per l'elaborazione in linguaggio naturale.

## <a name="sample-app-xamarinnl"></a>App di esempio: XamarinNL

Per informazioni su come usare il framework del linguaggio naturale con xamarin. IOS, esaminiamo il [app di esempio XamarinNL](https://developer.xamarin.com/samples/monotouch/iOS12/XamarinNL).
Questa app di esempio viene illustrato come usare il framework del linguaggio naturale per:

- [Riconoscere i linguaggi](#recognizing-languages).
- [Tokenizzazione del testo in parole e frasi](#tokenizing-text-into-words-sentences-and-paragraphs).
- [Tag denominato entità e parti del discorso](#tagging-named-entities-and-parts-of-speech).

## <a name="recognizing-languages"></a>Riconoscendo le lingue

Il **riconoscimento** scheda dell'app di esempio viene illustrato come utilizzare un [`NLLanguageRecognizer`](https://developer.xamarin.com/api/type/NaturalLanguage.NLLanguageRecognizer/)
per determinare la lingua per un blocco di testo.

> [!NOTE]
> Il riconoscimento della lingua è un tipo specifico di classificazione di testo. Il framework del linguaggio naturale supporta inoltre la classificazione di testo personalizzato tramite i modelli di Machine Learning Core fornito dallo sviluppatore. Per altre informazioni, esaminare i [Introduzione a Framework di linguaggio naturale](https://developer.apple.com/videos/play/wwdc2018/713/) sessione WWDC 2018.

### <a name="dominant-language"></a>Linguaggio dominante

Toccare il **linguaggio** pulsante per identificare la lingua principale nell'input dell'utente.

Il `HandleDetermineLanguageButtonTap` metodo del `LanguageRecognizerViewController` utilizza il [`GetDominantLanguage`](https://developer.xamarin.com/api/member/NaturalLanguage.NLLanguageRecognizer.GetDominantLanguage/)
metodo di un `NLLanguageRecognizer` per recuperare il [`NLLanguage`](https://developer.xamarin.com/api/type/NaturalLanguage.NLLanguage/)
per la lingua primaria trovata nel testo:

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

### <a name="language-probabilities"></a>Probabilità di linguaggio

Toccare il **probabilità Language** pulsante per recuperare un elenco di ipotesi di lingua per l'input dell'utente.

Il `HandleLanguageProbabilitiesButtonTap` metodo per il `LanguageRecognizerViewController` crea un'istanza di classe un `NLLanguageRecognizer` richiedendo [`Process`](https://developer.xamarin.com/api/member/NaturalLanguage.NLLanguageRecognizer.Process/)
testo dell'utente. Chiama quindi il riconoscimento della lingua [`GetNativeLanguageHypotheses`](https://developer.xamarin.com/api/member/NaturalLanguage.NLLanguageRecognizer.GetNativeLanguageHypotheses)
metodo che recupera un dizionario di linguaggi e le probabilità associate. Il `LanguageRecognizerTableViewController` classe quindi esegue il rendering di tali linguaggi e le probabilità.

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

Potenziali `NLLanguage` i valori includono:

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

Un elenco completo delle lingue supportate è disponibile come parte di [`NLLanguage`](https://developer.xamarin.com/api/type/NaturalLanguage.NLLanguage/)
documentazione di enum API.

## <a name="tokenizing-text-into-words-sentences-and-paragraphs"></a>Suddivisione in token il testo in parole e frasi, paragrafi

Il **Tokenizer** della scheda dell'app di esempio viene illustrato come separare un blocco di testo in parole relativo componente o frasi con un [ `NLTokenizer` ](https://developer.xamarin.com/api/type/NaturalLanguage.NLTokenizer/).

Toccare il **parole** oppure **frasi** pulsante per recuperare un elenco di token. Ogni token è associato a una parola o frase nel testo originale.

`ShowTokens` Divide l'input dell'utente in token chiamando il [`GetTokens`](https://developer.xamarin.com/api/member/NaturalLanguage.NLTokenizer.GetTokens/)
metodo di un `NLTokenizer`. Questo metodo restituisce una matrice di [`NSValue`](xref:Foundation.NSValue)
gli oggetti, ogni ritorno a capo un `NSRange` valore corrispondente a un token nel testo originale.

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

`LanguageTokenizerTableViewController` esegue il rendering di un singolo token in ogni cella della tabella. Estrae un `NSRange` da un token `NSValue`, trova la stringa corrispondente nel testo originale e imposta un'etichetta attiva la cella di visualizzazione di tabella:

```csharp
public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
    var cell = TableView.DequeueReusableCell(TokenCell);
    NSRange range = Tokens[indexPath.Row].RangeValue;
    cell.TextLabel.Text = Text.Substring((int)range.Location, (int)range.Length);
    return cell;
}
```

## <a name="tagging-named-entities-and-parts-of-speech"></a>Aggiunta di tag parti del discorso e le entità denominate

Il **Tagger** scheda dell'app di esempio XamarinNL illustra come usare il [`NLTagger`](https://developer.xamarin.com/api/type/NaturalLanguage.NLTagger/)
classe per associare le categorie con i token di una stringa di input.
Il framework del linguaggio naturale include supporto predefinito per il riconoscimento di persone, luoghi, le organizzazioni e parti del discorso.

> [!NOTE]
> Il framework del linguaggio naturale supporta anche gli schemi di assegnazione di tag personalizzati mediante i modelli di Machine Learning Core fornito dallo sviluppatore. Per altre informazioni, esaminare i [Introduzione a Framework di linguaggio naturale](https://developer.apple.com/videos/play/wwdc2018/713/) sessione WWDC 2018.

Toccare il **entità denominate** oppure **parti del discorso** pulsante da recuperare:

- Matrice di `NSValue` oggetti, ogni ritorno a capo un `NSRange` per un token nel testo originale.
- Matrice di [ `NLTag` ](https://developer.xamarin.com/api/type/NaturalLanguage.NLTag/) valori – categorie per il `NSValue` i token in corrispondenza dell'indice di matrice stessa.

Nelle `LanguageTaggerViewController`, `HandlePartsOfSpeechButtonTap` e `HandleNamedEntitiesButtonTap` ogni chiamata `ShowTags`passando lungo un [ `NLTagScheme` ](https://developer.xamarin.com/api/type/NaturalLanguage.NLTagScheme/) : entrambi `NLTagScheme.LexicalClass` (per parti del discorso) o `NLTagScheme.NameType` (per entità denominate).

`ShowTags` Crea un' `NLTagger`, crearne un'istanza con una matrice di oggetti `NLTagScheme` i tipi per cui verrà eseguite query (in questo caso, solo il passato aggiuntivo `NLTagScheme` valore). Quindi, utilizza il [`GetTags`](https://developer.xamarin.com/api/member/NaturalLanguage.NLTagger.GetTags/)
metodo su di `NLTagger` per determinare i tag rilevanti per il testo nell'input dell'utente.

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

I tag vengono quindi visualizzati in una tabella in base il `LanguageTaggerTableViewController`.

Potenziali `NLTag` i valori includono:

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

Un elenco completo di tag supportati è disponibile come parte di [`NLTag`](https://developer.xamarin.com/api/type/NaturalLanguage.NLTag/)
documentazione di enum API.

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio: XamarinNL](https://developer.xamarin.com/samples/monotouch/iOS12/XamarinNL)
- [Introduzione a Framework di linguaggio naturale](https://developer.apple.com/videos/play/wwdc2018/713/)
- [Linguaggio naturale (Apple)](https://developer.apple.com/documentation/naturallanguage?language=objc)
