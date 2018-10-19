---
title: Pulsanti in xamarin. IOS
description: La classe UIButton viene utilizzata per rappresentare vari diversi stili di pulsante nelle schermate di iOS. Questa guida illustra le diverse opzioni per l'uso di pulsanti in iOS.
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/11/2018
ms.openlocfilehash: 35fc743944c04dd1fdb8e035ba94ad6aeb6156ea
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "38986004"
---
# <a name="buttons-in-xamarinios"></a>Pulsanti in xamarin. IOS

In iOS, il `UIButton` classe rappresenta un controllo pulsante.

Proprietà del pulsante può essere modificata a livello di codice o con il **riquadro delle proprietà** di iOS Designer:

![Il riquadro delle proprietà di iOS Designer](buttons-images/properties.png "il riquadro delle proprietà di iOS Designer")

## <a name="creating-a-button-programmatically"></a>Creazione di un pulsante a livello di codice

Oggetto `UIButton` possono essere creati con solo poche righe di codice.

- Creare un'istanza di un pulsante e specificarne il tipo:

  ```csharp
  UIButton myButton = new UIButton(UIButtonType.System);
  ```

  Tipo del pulsante specificato da un `UIButtonType`:

  - `UIButtonType.System` -Un pulsante per utilizzo generico
  - `UIButtonType.DetailDisclosure` -Indica la disponibilità di informazioni dettagliate, in genere su un elemento specifico in una tabella
  - `UIButtonType.InfoDark` -Indica la disponibilità delle informazioni di configurazione; colore scuro
  - `UIButtonType.InfoLight` -Indica la disponibilità delle informazioni di configurazione; colore chiaro
  - `UIButtonType..AddContact` -Indica che è possibile aggiungere un contatto
  - `UIButtonType.Custom` -Pulsante personalizzabile

  Per altre informazioni sui tipi di pulsante diverso, dare un'occhiata:
  
  - Il [tipi di pulsante personalizzato](#custom-button-types) sezione di questo documento
  - Il [pulsante tipi](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/create_different_types_of_buttons) recipe
  - Apple [iOS Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/ios/controls/buttons/).

- Definire le dimensioni e la posizione del pulsante:

  ```csharp
  myButton.Frame = new CGRect(25, 25, 300, 150);
  ```

- Impostare il testo del pulsante. Usare la `SetTitle` metodo, che richiede il testo e un `UIControlState` valore:

  ```csharp
  myButton.SetTitle("Hello, World!", UIControlState.Normal);
  ```

  Per altre informazioni sull'applicazione di stili a un pulsante e l'impostazione del testo, vedere:

  - Il [Disegna un pulsante](#styling-a-button) sezione di questo documento
  - Il [impostare il testo del pulsante](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/set_button_text) recipe.

## <a name="handling-a-button-tap"></a>La gestione tocco di un pulsante

Per rispondere al tocco di un pulsante, fornire un gestore per il pulsante `TouchUpInside` evento:

```csharp
button.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

> [!NOTE]
> `TouchUpInside` non è l'evento è disponibile solo pulsante. `UIButton` è una classe figlio della `UIControl`, che definisce [molti eventi diversi](https://developer.xamarin.com/api/type/UIKit.UIControlEvent/).

### <a name="using-the-ios-designer-to-specify-button-event-handlers"></a>Uso di iOS Designer per specificare i gestori eventi dei pulsanti

Usare la **eventi** scheda della finestra di **riquadro delle proprietà** per specificare i gestori eventi per un pulsante di vari eventi.

Per l'evento appropriato, digitare il nome di un nuovo gestore o selezionarne uno dall'elenco. In questo modo verrà creato un gestore eventi nel codice per il controller di visualizzazione del pulsante.

![Scheda eventi del riquadro delle proprietà](buttons-images/image1.png "scheda eventi del riquadro delle proprietà")

## <a name="styling-a-button"></a>Disegna un pulsante

`UIButton` i controlli possono essere presenti in un numero di diversi stati, ognuno specificato da un `UIControlState` valore – `Normal`, `Disabled`, `Focused`, `Highlighted`e così via. Ogni stato è possibile assegnare uno stile univoco, specificato a livello di codice o con iOS Designer.

> [!NOTE]
> Per un elenco completo di tutte le `UIControlState` valori, esaminiamo il [`UIKit.UIControlState enumeration`](https://developer.xamarin.com/api/type/UIKit.UIControlState/)
> Documentazione.

Ad esempio, per impostare il colore del titolo e il colore dell'ombreggiatura per `UIControlState.Normal`:

```csharp
button.SetTitleColor(UIColor.White, UIControlState.Normal);
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

Il codice seguente imposta il titolo del pulsante su una stringa (stilizzato) con gli attributi per `UIControlState.Normal` e `UIControlState.Highlighted`:

```csharp
var normalAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle(normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle(highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>Tipi di pulsanti personalizzati

Pulsanti con un `UIButtonType` di `Custom` non hanno stili predefiniti. Tuttavia, è possibile configurare l'aspetto del pulsante impostando un'immagine per i diversi stati:

```csharp
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```

A seconda se l'utente tocca il pulsante o No, questo eseguirà il rendering come una delle immagini seguenti (`UIControlState.Normal`, `UIControlState.Highlighted` e `UIControlState.Selected` indica, rispettivamente):

![UIControlState.Normal](buttons-images/image22.png "UIControlState.Normal")
![UIControlState.Highlighted](buttons-images/image23.png "UIControlState.Highlighted") 
 ![UIControlState.Selected](buttons-images/image24.png "UIControlState.Selected")

Per altre informazioni sull'uso dei pulsanti personalizzati, vedere la [usare un'immagine per un pulsante](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/use_an_image_for_a_button) recipe.

