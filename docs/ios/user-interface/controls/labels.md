---
title: Etichette in Xamarin.iOS
description: Questo documento illustra come usare le etichette in Xamarin.iOS. Viene descritto come creare etichette a livello di codice e con iOS designer.
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/11/2017
ms.openlocfilehash: 04d33d986d76daf29fc7392206c62f77d34dd969
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022015"
---
# <a name="labels-in-xamarinios"></a>Etichette in Xamarin.iOS

Il controllo `UILabel` viene utilizzato per la visualizzazione di testo di sola lettura e a più righe.

## <a name="implementing-a-label"></a>Implementazione di un'etichetta

Viene creata una nuova etichetta creando un'istanza di un [`UILabel`](xref:UIKit.UILabel):

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>Etichette e storyboard

È anche possibile aggiungere un'etichetta all'interfaccia utente quando si usa iOS designer. Cercare **Label** nella **casella degli strumenti** e trascinarlo nella visualizzazione:

![Etichetta nella casella degli strumenti](labels-images/image3.png)

Nel riquadro delle proprietà è possibile modificare le proprietà seguenti:

![Pannello Proprietà etichetta](labels-images/image2.png)

- **Contesto del testo** : semplice o con attributi. Testo normale consente di impostare gli [attributi di formattazione](#Formatting_Text_and_Label) dell'intera stringa. I testi con attributi consentono di impostare la formattazione su caratteri o parole diversi nella stringa.
- Attributi **color, font, Alignment** -Formatting che possono essere applicati all'etichetta.
- **Righe** : imposta il numero di righe che l'etichetta può estendere. Impostare su 0 per consentire all'etichetta di utilizzare il numero di righe desiderato.
- **Comportamento** : può essere impostato su abilitato o evidenziato. Enabled è impostato per impostazione predefinita, il testo disabilitato verrà visualizzato in un colore grigio più chiaro. Evidenziato è disabilitato per impostazione predefinita e consente di ricreare l'etichetta con uno stato evidenziato quando viene selezionato da un utente.
- **Baselane e interruzioni di riga** :
  - Linea determina il modo in cui il testo verrà posizionato se le dimensioni del carattere sono diverse da quelle specificate.
  - Le interruzioni di riga determinano il modo in cui una stringa verrà racchiusa o troncata se è più lunga di una singola riga.
- **AutoShrink** : determina il modo in cui la dimensione del carattere verrà ridotta a icona all'interno di un'etichetta, se necessario.
- **Evidenziato, ombreggiato, offset** : consente di impostare il noterete e il colore dell'ombreggiatura e l'offset dell'ombreggiatura.

## <a name="truncating-and-wrapping"></a>Troncamento e wrapping

Per informazioni sull'uso delle interruzioni di riga in iOS, vedere la ricetta di [troncamento e wrapping del testo](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text) .

<a name="Formatting_Text_and_Label"/>

## <a name="formatting-text-and-label"></a>Formattazione di testo e etichetta

Per formattare la stringa usata in un'etichetta, è possibile impostare gli attributi di formattazione sull'intera stringa oppure è possibile usare stringhe con attributi. Gli esempi seguenti illustrano come implementarli:

```csharp
label = new UILabel(){
                Text = "Hello, this is a string",
                Font = UIFont.FromName("Papyrus", 20f),
                TextColor = UIColor.Magenta,
                TextAlignment = UITextAlignment.Center
            };
```

```csharp
label.AttributedText = new NSAttributedString(
                "This is some formatted text",
                font: UIFont.FromName("GillSans", 16.0f),
                foregroundColor: UIColor.Blue,
                backgroundColor: UIColor.White
            );
```

Per ulteriori informazioni sullo stile del testo utilizzando `NSAttributedString` fare riferimento alla ricetta del [testo di stile](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/text_field/style_text) .

Per impostazione predefinita, le etichette hanno il `Enabled` impostato su true, ma è possibile impostarlo su Disabled per fornire all'utente un suggerimento che un determinato controllo è disabilitato:

```csharp
label.Enabled = false;
```

In questo modo l'etichetta viene impostata su un colore grigio chiaro, come illustrato nell'immagine di esempio seguente della schermata restrizioni in iOS:

![Pulsante disabilitato in iOS](labels-images/image1.png)

È anche possibile impostare i colori di evidenziazione e di testo ombreggiatura sul testo dell'etichetta per ulteriori effetti:

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

Che Visualizza il testo simile al seguente:

![Evidenziare e ombreggiatura sul testo](labels-images/image4.png)

Per ulteriori informazioni sulla modifica del tipo di carattere di un UILabel, vedere la ricetta relativa alla [modifica del tipo di carattere](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/change_the_font) .
