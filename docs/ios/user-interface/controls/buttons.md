---
title: Pulsanti in Xamarin.iOS
description: La classe UIButton viene usata per rappresentare diversi stili di pulsante nelle schermate iOS. Questa guida presenta le diverse opzioni per l'uso dei pulsanti in iOS.
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/11/2018
ms.openlocfilehash: a8dfd267fe9f5f838927fc216d53c2475398ed16
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022118"
---
# <a name="buttons-in-xamarinios"></a>Pulsanti in Xamarin.iOS

In iOS la classe `UIButton` rappresenta un controllo Button.

Le proprietà di un pulsante possono essere modificate a livello di codice o con il **riquadro delle proprietà** della finestra di progettazione iOS:

![riquadro delle proprietà della finestra di progettazione iOS](buttons-images/properties.png "riquadro delle proprietà della finestra di progettazione iOS")

## <a name="creating-a-button-programmatically"></a>Creazione di un pulsante a livello di codice

È possibile creare un `UIButton` solo con poche righe di codice.

- Creare un'istanza di un pulsante e specificarne il tipo:

  ```csharp
  UIButton myButton = new UIButton(UIButtonType.System);
  ```

  Il tipo del pulsante è specificato da un `UIButtonType`:

  - `UIButtonType.System`-un pulsante per utilizzo generico
  - `UIButtonType.DetailDisclosure`: indica la disponibilità di informazioni dettagliate, in genere relative a un elemento specifico di una tabella
  - `UIButtonType.InfoDark`: indica la disponibilità delle informazioni di configurazione; colorato
  - `UIButtonType.InfoLight`: indica la disponibilità delle informazioni di configurazione; con colore chiaro
  - `UIButtonType..AddContact`: indica che è possibile aggiungere un contatto
  - pulsante personalizzabile `UIButtonType.Custom`

  Per ulteriori informazioni sui diversi tipi di pulsanti, vedere:
  
  - La sezione [tipi di pulsante personalizzati](#custom-button-types) di questo documento
  - La ricetta relativa ai [tipi di pulsante](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/create_different_types_of_buttons)
  - [Linee guida per l'interfaccia umana iOS](https://developer.apple.com/design/human-interface-guidelines/ios/controls/buttons/)di Apple.

- Definire le dimensioni e la posizione del pulsante:

  ```csharp
  myButton.Frame = new CGRect(25, 25, 300, 150);
  ```

- Imposta il testo del pulsante. Usare il metodo `SetTitle`, che richiede il testo e un valore `UIControlState`:

  ```csharp
  myButton.SetTitle("Hello, World!", UIControlState.Normal);
  ```

  Per ulteriori informazioni sullo stile di un pulsante e sull'impostazione del relativo testo, vedere:

  - Sezione relativa allo [stile di un pulsante](#styling-a-button) di questo documento
  - La ricetta del [testo del pulsante Imposta](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/set_button_text) .

## <a name="handling-a-button-tap"></a>Gestione del tocco di un pulsante

Per rispondere al tocco di un pulsante, fornire un gestore per l'evento `TouchUpInside` del pulsante:

```csharp
button.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

> [!NOTE]
> `TouchUpInside` non è l'unico evento pulsante disponibile. `UIButton` è una classe figlio di `UIControl`, che definisce [molti eventi diversi](xref:UIKit.UIControlEvent).

### <a name="using-the-ios-designer-to-specify-button-event-handlers"></a>Uso di iOS designer per specificare i gestori eventi dei pulsanti

Utilizzare la scheda **eventi** della **riquadro delle proprietà** per specificare i gestori eventi per i vari eventi di un pulsante.

Per l'evento appropriato, digitare il nome di un nuovo gestore eventi o selezionarne uno dall'elenco. In questo modo verrà creato un gestore eventi nel codice per il controller di visualizzazione del pulsante.

![Scheda eventi della riquadro delle proprietà](buttons-images/image1.png "Scheda eventi della riquadro delle proprietà")

## <a name="styling-a-button"></a>Applicare uno stile a un pulsante

i controlli `UIButton` possono esistere in diversi Stati, ciascuno specificato da un valore di `UIControlState`, ovvero `Normal`, `Disabled`, `Focused`, `Highlighted`e così via. A ogni stato può essere assegnato uno stile univoco, specificato a livello di codice o con iOS designer.

> [!NOTE]
> Per un elenco completo di tutti i valori `UIControlState`, vedere l' [`UIKit.UIControlState enumeration`](xref:UIKit.UIControlState)
> documentazione.

Ad esempio, per impostare il colore del titolo e il colore dell'ombreggiatura per `UIControlState.Normal`:

```csharp
button.SetTitleColor(UIColor.White, UIControlState.Normal);
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

Il codice seguente imposta il titolo del pulsante su una stringa con attributi (stilizzata) per `UIControlState.Normal` e `UIControlState.Highlighted`:

```csharp
var normalAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle(normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle(highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>Tipi di pulsante personalizzati

I pulsanti con `UIButtonType` di `Custom` non hanno stili predefiniti. Tuttavia, è possibile configurare l'aspetto del pulsante impostando un'immagine per i diversi stati:

```csharp
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```

A seconda che l'utente tocchi o meno il pulsante, verrà eseguito il rendering come una delle immagini seguenti (rispettivamente`UIControlState.Normal`, `UIControlState.Highlighted` e `UIControlState.Selected` Stati):

![UIControlState. Normal](buttons-images/image22.png "UIControlState. Normal")
![UIControlState. evidenziato](buttons-images/image23.png "UIControlState. evidenziato")
![UIControlState. Selected](buttons-images/image24.png "UIControlState. Selected")

Per ulteriori informazioni sull'utilizzo dei pulsanti personalizzati, vedere la ricetta [utilizzare un'immagine per un pulsante](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/use_an_image_for_a_button) .
