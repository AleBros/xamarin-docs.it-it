---
title: Pulsanti in xamarin. IOS
description: La classe UIButton viene utilizzata per rappresentare vari diversi stili di pulsante nelle schermate di iOS. Questa sezione vengono illustrate le diverse opzioni per l'utilizzo di pulsanti di iOS.
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: bf9a36c63e0c153ed950f4c3531e99e6baf77687
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789479"
---
# <a name="buttons-in-xamarinios"></a>Pulsanti in xamarin. IOS

_La classe UIButton viene utilizzata per rappresentare vari diversi stili di pulsante nelle schermate di iOS. Questa sezione vengono illustrate le diverse opzioni per l'utilizzo di pulsanti di iOS._

La `UIButton`classe rappresenta un controllo pulsante di iOS. 

Pulsante proprietà possono essere modificate nella `Properties Pad` della finestra di progettazione iOS:


![](buttons-images/properties.png "Il riquadro proprietà della finestra di progettazione iOS")

## <a name="creating-a-button"></a>Creazione di un pulsante

È possibile creare un UIButton in tramite solo poche righe di codice.

Innanzitutto, creare un'istanza di un nuovo pulsante e specificare il tipo di pulsante che è necessario:

```csharp
UIButton myButton = new UIButton(UIButtonType.System);
```

Il UIButtonType deve essere specificato come uno dei valori seguenti:

- **Sistema** -questo è il tipo di pulsante standard usato da iOS ed è il tipo che verrà utilizzato più frequentemente.
- **DetailDisclosure** -presenta un tipo di pulsante utilizzato per nascondere o visualizzare informazioni dettagliate di "in basso a".
- **InfoDark** -scuro dettagliate info pulsante visualizzato "i" in un cerchio.
- **InfoLight** -una luce dettagliate info pulsante visualizzato "i" in un cerchio.
- **AddContact** -visualizzare il pulsante come pulsante Aggiungi contatto.
- **Custom** -consente di personalizzare i vari tratti del pulsante.

Ulteriori informazioni sui tipi di pulsante sono reperibile nel [tipi di pulsanti](https://developer.xamarin.com/recipes/ios/standard_controls/buttons/create_different_types_of_buttons/) recipe.

Successivamente, definire la dimensione su schermo e la posizione del pulsante. Esempio:

```csharp
myButton.Frame = new CGRect (25, 25, 300, 150);
```

Per modificare il testo in un pulsante, usare il `SetTitle` proprietà sul pulsante, che richiede di impostare una stringa di testo e un `UIControlStyle`. Ad esempio:

```csharp
myButton.SetTitle("Hello, World!", UIControlState.Normal);
```

L'impostazione di proprietà diverse per ogni stato consente di comunicare informazioni all'utente (ad es. rendere il colore del testo grigio per lo stato disabilitato). È possibile passare tra ogni stato utilizzando la finestra di progettazione iOS, oppure è possibile eseguire a livello di codice. Per ulteriori informazioni sul testo del pulsante di impostazione e lo stato, vedere il [impostare testo del pulsante](https://developer.xamarin.com/recipes/ios/standard_controls/buttons/set_button_text/) recipe.

## <a name="dealing-with-user-interactions"></a>Gestione delle interazioni dell'utente


Pulsanti non sono molto utili a meno che non sono operazioni quando si fa clic. 

In iOS eventi sui pulsanti sono quasi sempre eventi tocco, l'uso interagisce con il pulsante sullo schermo da contigua a esso. Un elenco di tutti i possibili eventi UIControl sono elencati [qui](https://developer.apple.com/documentation/uikit/uicontrolevents), ma l'evento più comunemente usata in iOS è `TouchUpInside`. È quindi possibile creare un gestore eventi per eseguire un'operazione dopo che è stato premuto il pulsante:


```csharp
button.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

### <a name="adding-events-in-the-ios-designer"></a>Aggiunta di eventi nella finestra di progettazione iOS
 
Utilizzare la scheda eventi nel riquadro proprietà per aggiungere eventi ai controlli.

Selezionare l'evento e digitare il nome di un nuovo gestore eventi o selezionare uno dall'elenco. Questa operazione creerà un nuovo metodo parziale nella classe di Controller di visualizzazione.

![Scheda eventi](buttons-images/image1.png)

## <a name="styling-a-button"></a>Disegna un pulsante

UIButtons sono diverse da UIKit la maggior parte dei controlli che hanno uno stato non è appena sufficiente modificare il titolo, è necessario modificarlo per ogni `UIControlState`. Impostare il colore del titolo e il colore di ombreggiatura viene eseguita in modo analogo:

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

Inoltre, è possibile utilizzare con gli attributi di testo come titolo del pulsante. Ad esempio:

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>Tipi di pulsanti personalizzati


Quando si imposta la `Custom` tipo di pulsante, l'oggetto non ha rendering predefinito. È possibile configurare l'aspetto del pulsante impostando un'immagine per i diversi stati. Ad esempio, il codice seguente viene illustrato come aggiungere immagini differenti per il `Normal`, `Highlighted` e `Selected` stati:


```csharp
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```


A seconda se l'utente tocca il pulsante o No, verrà visualizzata come una delle seguenti immagini (`Normal`, `Highlighted` e `Selected` stati rispettivamente):


![](buttons-images/image22.png "Stato UIButton normale")
![](buttons-images/image23.png "stato UIButton evidenziato")
![](buttons-images/image24.png "UIButton stato selezionato")

Per ulteriori informazioni sull'utilizzo dei pulsanti personalizzati, vedere il [utilizza un'immagine per un pulsante](https://developer.xamarin.com/recipes/ios/standard_controls/buttons/use_an_image_for_a_button/).


## <a name="related-links"></a>Collegamenti correlati

- [Cartella di lavoro UIButton](https://developer.xamarin.com/workbooks/ios/user-interface/UIbutton/uibutton.workbook)
