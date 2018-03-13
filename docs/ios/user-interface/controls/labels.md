---
title: Etichette
ms.topic: article
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: 0fdeecc4465aa5709b452ef0b591ec4e5c262e3d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="labels"></a>Etichette

Il `UILabel` controllo viene utilizzato per visualizzare uno o più righe, testo di sola lettura. 

## <a name="implementing-a-label"></a>Implementazione di un'etichetta

Viene creata una nuova etichetta creando un [ `UILabel` ](https://developer.xamarin.com/api/type/UIKit.UILabel/):

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>Le etichette e gli storyboard

È anche possibile aggiungere un'etichetta all'interfaccia utente quando si utilizza la finestra di progettazione iOS. Cercare **etichetta** nel **della casella degli strumenti** e trascinarlo nella propria visualizzazione:

![Assegnare l'etichetta nella casella degli strumenti](labels-images/image3.png)

Nel riquadro proprietà consente di effettuare le seguenti proprietà:

![Pannello proprietà etichetta](labels-images/image2.png)

- **Contesto di testo** : normale o con attributi. Testo normale consente di impostare il [gli attributi di formattazione](#Formatting_Text_and_Label) sulla stringa intera. Testi con attributi consente di impostare diversi caratteri o parole nella stringa di formattazione.
- **Allineamento di colore, tipo di carattere,** : gli attributi di formattazione che può essere applicato all'etichetta.
- **Righe** : imposta il numero di righe che è possibile estendere l'etichetta. Impostare questo valore su 0 per consentire l'etichetta da utilizzare come numero di righe in base alle esigenze.
- **Comportamento** : può essere impostato su abilitato o evidenziato. È abilitata l'impostazione predefinita, il testo disabilitato verrà visualizzato in un colore grigio più chiaro. Evidenziato è disattivata per impostazione predefinita e consente l'etichetta da ricreare con uno stato evidenziato quando viene selezionato da un utente.
- **Interruzione di riga e di Baselane** : 
    - Della linea di base determina come il testo verrà posizionato se le dimensioni dei caratteri è diverso da quello specificato.
    - Le interruzioni di riga determinano come verrà eseguito il wrapping o troncata se risulta più di una sola riga di una stringa.
- **Compattazione automatica** : determina come il tipo di carattere dimensione verrà ridotta a icona all'interno di un'etichetta, se necessario.
- **Evidenziato, ombreggiatura, Offset** : consente di impostare il colore di ombreggiatura e di evidenziati e offset dell'ombreggiatura.

## <a name="truncating-and-wrapping"></a>Il troncamento e ritorno a capo

Per informazioni sull'utilizzo della riga si interrompe in iOS, vedere il [Tronca e testo a capo](https://developer.xamarin.com/recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text/) recipe.

<a name="Formatting_Text_and_Label"/>

## <a name="formatting-text-and-label"></a>Etichetta e la formattazione del testo

Per formattare la stringa utilizzata in un'etichetta è possibile impostare attributi per l'intera stringa di formattazione o è possibile utilizzare stringhe con Attribute. Nell'esempio seguente viene illustrato come implementare questi elementi:

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

Per ulteriori informazioni sull'utilizzo di testo stile `NSAttributedString` fare riferimento al [testo stile](https://developer.xamarin.com/recipes/ios/standard_controls/text_field/style_text/) recipe.

Per impostazione predefinita, le etichette devono il `Enabled` impostato su true, ma è possibile impostarlo su disabilitato per consentire all'utente un suggerimento che un controllo è stato disabilitato:

```csharp
label.Enabled = false;
```

Questo imposta l'etichetta con un colore grigio chiaro, come illustrato nell'immagine seguente esempio dello schermo restrizioni in iOS:

![Pulsante disabilitato in iOS](labels-images/image1.png)

Inoltre, è possibile impostare i colori di testo evidenziato e Shadow per il testo dell'etichetta per gli effetti aggiuntivi:

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

Che visualizza il testo simile al seguente:

![Evidenziazione e Shadow impostate nel testo](labels-images/image4.png)

Per ulteriori informazioni sulla modifica del carattere di un UILabel, consultare il [modifica il carattere](https://developer.xamarin.com/recipes/ios/standard_controls/labels/change_the_font/) recipe.





