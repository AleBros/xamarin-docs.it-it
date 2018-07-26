---
title: Etichette in xamarin. IOS
description: Questo documento illustra come usare le etichette in xamarin. IOS. Viene descritto come creare le etichette a livello di codice e con iOS Designer.
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: b52bdbd41eaafbc5e6c78e1f8514b701fd78bd6b
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241913"
---
# <a name="labels-in-xamarinios"></a>Etichette in xamarin. IOS

Il `UILabel` controllo viene usato per la visualizzazione singola e a più righe, testo di sola lettura. 

## <a name="implementing-a-label"></a>Implementazione di un'etichetta

Viene creata una nuova etichetta creando un [ `UILabel` ](https://developer.xamarin.com/api/type/UIKit.UILabel/):

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>Le etichette e storyboard

È anche possibile aggiungere un'etichetta per l'interfaccia utente quando si usa iOS Designer. Cercare **Label** nel **della casella degli strumenti** e trascinarla per la visualizzazione:

![Assegnare un'etichetta nella casella degli strumenti](labels-images/image3.png)

Il riquadro delle proprietà è possibile regolare le proprietà seguenti:

![Pannello proprietà etichetta](labels-images/image2.png)

- **Contesto testo** : normale o con attributi. Testo normale consente di impostare il [attributi di formattazione](#Formatting_Text_and_Label) sull'intera stringa. Testi con attributi consente di impostare diversi caratteri o parole nella stringa di formattazione.
- **Colore, tipo di carattere, allineamento** : gli attributi di formattazione che possono essere applicati all'etichetta.
- **Righe** : imposta il numero di righe che è possibile estendere l'etichetta. Impostare questa proprietà su 0 per consentire l'etichetta da utilizzare come numero di righe in base alle esigenze.
- **Comportamento** – può essere impostato su Enabled o evidenziata. È abilitato per impostazione predefinita, il testo disabilitato verrà visualizzato in un colore grigio più chiaro. Evidenziato è disattivata per impostazione predefinita e consente l'etichetta ridisegno con uno stato evidenziato quando viene selezionato da un utente.
- **Interruzione di riga e Baselane** : 
    - Della linea di base determina come il testo verrà posizionato se le dimensioni dei caratteri è diversa da quella specificata.
    - Le interruzioni di riga determinano come verrà eseguito il wrapping o troncata se risulta più di una singola riga di una stringa.
- **Compattazione automatica** : determina come il tipo di carattere dimensionata verrà ridotta a icona all'interno di un'etichetta, se necessario.
- **Evidenziato, ombreggiatura, Offset** : consente di impostare il colore di ombreggiatura e facciamo e offset dell'ombreggiatura.

## <a name="truncating-and-wrapping"></a>Il troncamento e ritorno a capo

Per informazioni sull'uso di riga si interrompe in iOS, vedere la [Tronca e testo a capo](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text) recipe.

<a name="Formatting_Text_and_Label"/>

## <a name="formatting-text-and-label"></a>Etichetta e la formattazione del testo

Per formattare la stringa utilizzata in un'etichetta è possibile impostare attributi per l'intera stringa di formattazione o è possibile usare le stringhe con Attribute. Negli esempi seguenti viene illustrato come implementare questi elementi:

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

Per altre informazioni sull'uso di testo lo stile `NSAttributedString` fanno riferimento al [stile testo](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/text_field/style_text) recipe.

Per impostazione predefinita hanno etichette di `Enabled` impostato su true, ma è possibile impostarlo su disabilitato per consentire all'utente un suggerimento che viene disabilitato un determinato controllo:

```csharp
label.Enabled = false;
```

Questo imposta l'etichetta con un colore grigio chiaro, come illustrato nell'immagine di esempio seguenti dello schermo, le restrizioni in iOS:

![Pulsante disabilitato in iOS](labels-images/image1.png)

È anche possibile impostare i colori del testo di luci e ombre al testo dell'etichetta per gli effetti aggiuntivi:

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

Che consente di visualizzare il testo simile al seguente:

![Luci e ombre impostata sul testo](labels-images/image4.png)

Per altre informazioni su come modificare il tipo di carattere di un UILabel, consultare il [modifica il carattere](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/change_the_font) recipe.





