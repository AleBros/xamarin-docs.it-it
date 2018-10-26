---
title: Uso di pulsanti di tvOS in Xamarin
description: Questo documento descrive come usare i pulsanti in un'app tvOS compilata con Xamarin. Illustra come usare i pulsanti nel codice e nei storyboard ed esamina come applicare uno stile a un pulsante.
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/07/2017
ms.openlocfilehash: 6d8fc1daaced24dccead78c4f9d0e5d0959b3755
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116342"
---
# <a name="working-with-tvos-buttons-in-xamarin"></a>Uso di pulsanti di tvOS in Xamarin

Utilizzare un'istanza di `UIButton` classe per creare un pulsante con stato attivabile, selezionabile in una finestra tvOS. Quando l'utente seleziona un pulsante, invia un messaggio di azione per l'oggetto di destinazione consentono di rispondere di app xamarin. tvos all'utente di input.

[![](buttons-images/buttons01.png "Pulsanti di esempio")](buttons-images/buttons01.png#lightbox)

Per altre informazioni sull'uso dello stato attivo e spostarsi con il remoti per Siri, vedere la [utilizzo di navigazione e lo stato attivo](~/ios/tvos/app-fundamentals/navigation-focus.md) e [controller Bluetooth e remoti per Siri](~/ios/tvos/platform/remote-bluetooth.md) documentazione.

<a name="About-Buttons" />

## <a name="about-buttons"></a>Informazioni sui pulsanti

In tvOS, i pulsanti vengono usati per le azioni specifiche dell'app e possono contenere un titolo, un'icona o entrambi. Quando l'utente si sposta tramite interfaccia utente dell'app di [remoti per Siri](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), lo stato attivo passa al pulsante specificato rendendo di modificare i colori di sfondo e testo. Un'ombreggiatura viene applicata anche al pulsante di aggiunta di un effetto 3D facendola apparire a salire sopra il resto dell'interfaccia utente.

[![](buttons-images/buttons01.png "Pulsanti di esempio")](buttons-images/buttons01.png#lightbox)

Apple ha i seguenti suggerimenti per l'uso di pulsanti:

- **Usare un titolo o un'icona** - durante l'entrambi un'icona e un titolo può essere inclusi in un pulsante, lo spazio è limitato quindi provare a non combinare entrambi.
- **Chiaramente i pulsanti distruttive Mark** : se il pulsante esegue un distruttive azione (ad esempio l'eliminazione di un file), indicarne chiaramente come tali usando testo e/o sull'icona. Azioni distruttive devono sempre presentare un' [avviso](~/ios/tvos/user-interface/alerts.md) che chiede all'utente per limitare l'azione.
- **Non usare nuovamente pulsanti** -pulsante di Menu nel sistema remoto Siri viene usato per tornare alla schermata precedente. L'unica eccezione a questa regola è per gli acquisti In-App o azioni distruttive in cui un **annullare** deve essere visualizzato il pulsante.

Per altre informazioni sull'utilizzo con lo stato attivo e l'esplorazione, vedere la [utilizzo di navigazione e lo stato attivo](~/ios/tvos/app-fundamentals/navigation-focus.md) documentazione.

<a name="Button-Icons" />

### <a name="button-icons"></a>Icone dei pulsanti

Usare immagini semplice e facilmente riconoscibile per le icone del pulsante suggerite da Apple. Icone eccessivamente complesse sono difficili da riconoscere in una schermata TV tutta la stanza in un divano, quindi provare a usare la rappresentazione in forma più semplice possibile rendere l'idea. Quando possibile, utilizzare standard, conosciuta immagini per le icone (ad esempio una lente di ingrandimento per la ricerca).

<a name="Button-Titles" />

### <a name="button-titles"></a>Pulsante titoli

Apple ha i suggerimenti seguenti quando si creano i titoli per i pulsanti:

- **Mostra testo descrittivo di sotto pulsanti delle icone** : se possibile, inserire il testo non crittografato e descrittivi sotto l'icona solo pulsanti a get ulteriormente lo scopo del pulsante tra.
- **Usare verbi o verbo frasi per il titolo** -posizionare chiaramente l'azione che verrà eseguita quando l'utente fa clic sul pulsante.
- **Uso di maiuscole/minuscole stile titolo** , fatta eccezione per gli articoli e preposizioni e congiunzioni (quattro lettere o meno), deve essere in maiuscolo ogni parola del titolo del pulsante.
- **Usare un breve titolo a punto** -usare il più breve e sono presenti possibili per descrivere l'azione del pulsante.

<a name="Buttons-and-Storyboards" />

## <a name="buttons-and-storyboards"></a>I pulsanti e gli storyboard

Il modo più semplice per lavorare con i pulsanti in un'app xamarin. tvos è per aggiungerli all'interfaccia utente dell'app usando la finestra di progettazione di Xamarin per iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)


1. Nel **Esplora soluzioni**, fare doppio clic il `Main.storyboard` file e aprirlo e modificarlo.
1. Trascinare un **sul pulsante** dal **libreria** e rilasciarla sulla vista: 

    [![](buttons-images/storyboard01.png "Un pulsante")](buttons-images/storyboard01.png#lightbox)
1. Nel **riquadro delle proprietà**, è possibile modificare diverse proprietà del pulsante, ad esempio relativi **titolo** e **colore del testo**: 

    [![](buttons-images/storyboard02.png "Proprietà dei pulsanti")](buttons-images/storyboard02.png#lightbox)
1. Successivamente, passare al **scheda eventi** wireup e un **evento** dal **pulsante** e chiamarlo `ButtonPressed`: 

    [![](buttons-images/storyboard03.png "Nella scheda eventi")](buttons-images/storyboard03.png#lightbox)
1. Viene automaticamente passato al `ViewController.cs` vista in cui è possibile inserire il nuovo elemento Action nel codice usando il **backup** e **verso il basso** tasti di direzione: 

    [![](buttons-images/storyboard04.png "L'inserimento di una nuova azione nel codice")](buttons-images/storyboard04.png#lightbox)
1. Premere il **invio** per selezionare il percorso: 

    [![](buttons-images/storyboard05.png "L'editor di codice")](buttons-images/storyboard05.png#lightbox)
1. Salvare le modifiche a tutti i file.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Nel **Esplora soluzioni**, fare doppio clic il `Main.storyboard` file e aprirlo e modificarlo.
1. Trascinare un **sul pulsante** dal **libreria** e rilasciarla sulla vista: 

    [![](buttons-images/storyboard01vs.png "Un pulsante")](buttons-images/storyboard01vs.png#lightbox)
1. Nel **riquadro delle proprietà**, è possibile modificare diverse proprietà del pulsante, ad esempio relativi **titolo** e **colore del testo**: 

    [![](buttons-images/storyboard02vs.png "Il riquadro delle proprietà")](buttons-images/storyboard02vs.png#lightbox)
1. Successivamente, passare al **scheda eventi** wireup e un **evento** dal **pulsante** e chiamarlo `ButtonPressed`: 

    [![](buttons-images/storyboard03vs.png "Nella scheda eventi")](buttons-images/storyboard03vs.png#lightbox)
1. Salvare le modifiche a tutti i file.



Modificare il Controller di visualizzazione (esempio `ViewController.cs`) file e aggiungere il codice seguente per gestire il pulsante selezionato:


```

using System;
using UIKit;

namespace tvRemote
{
    public partial class ViewController : UIViewController
    {
        ...

        partial void ButtonPressed (UIButton sender) {
            // Handle click here
            ...
        }
    }
}

```

-----

Fino a quando un pulsante `Enabled` è di proprietà `true` e non è coperto da un altro controllo o una vista, è possibile renderla l'elemento messa a fuoco utilizzando il remoti per Siri. Se l'utente seleziona il pulsante e fa clic sull'area di tocco, il `ButtonPressed` azione definito in precedenza sarebbe stato eseguito.

> [!IMPORTANT]
> Anche se è possibile assegnare le azioni, ad esempio `TouchUpInside` per un `UIButton` in iOS Designer durante la creazione di un **gestore evento**, non verrà mai chiamato perché non dispone di un tocco Apple TV a schermo intero o supportare gli eventi di tocco. È consigliabile usare sempre il valore predefinito **tipo di azione** durante la creazione **azioni** per tvOS elementi dell'interfaccia utente.




Per altre informazioni sull'utilizzo degli storyboard, vedere la [App Tvos Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md).

<a name="Buttons-and-Code" />

## <a name="buttons-and-code"></a>I pulsanti e il codice

Facoltativamente, un `UIButton` può essere creato in C# del codice e aggiunte alla vista dell'app tvOS. Ad esempio:

```csharp
var button = new UIButton(UIButtonType.System);
button.Frame = new CGRect (25, 25, 300, 150);
button.SetTitle ("Hello", UIControlState.Normal);
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
View.AddSubview (button);
```

Quando si crea una nuova `UIButton` nel codice, si specifica relativa `UIButtonType` come uno dei seguenti:

- **Sistema** -questo è il tipo di pulsante presentato da tvOS standard ed è il tipo che verrà utilizzato più spesso.
- **DetailDisclosure** -presenta un tipo di pulsante utilizzato per nascondere o mostrare informazioni dettagliate "in basso a".
- **InfoDark** -pulsante visualizzato un "i" in un cerchio informazioni dettagliate sul scuro.
- **InfoLight** -pulsante visualizzato un "i" in un cerchio informazioni dettagliate su una luce.
- **AddContact** -visualizzare il pulsante come un pulsante Aggiungi contatto.
- **Custom** -consente di personalizzare diversi aspetti del pulsante.

Successivamente, si definisce la dimensione su schermo e la posizione del pulsante. Esempio:

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

Quindi, impostare il titolo del pulsante. `UIButtons` sono diversi rispetto alla maggior parte `UIKit` controlli nel senso che hanno uno stato in modo che non è possibile semplicemente modificare il titolo, è necessario modificarlo per un determinato `UIControlState`. Ad esempio:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

Successivamente, utilizziamo il `AllEvents` evento per vedere quando l'utente ha fatto clic sul pulsante. Esempio:

```csharp
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
```

Aggiungere infine il pulsante per la vista per visualizzarlo:

```csharp
View.AddSubview (button);
```

> [!IMPORTANT]
> Sebbene sia possibile assegnare le azioni, ad esempio `TouchUpInside` a un `UIButton`, non verrà mai chiamato perché non dispone di un tocco Apple TV a schermo intero o supportare gli eventi di tocco. È consigliabile utilizzare sempre gli eventi come **AllEvents** oppure **PrimaryActionTriggered**.




<a name="Styling-a-Button" />

## <a name="styling-a-button"></a>Disegna un pulsante

tvOS fornisce diverse proprietà di un `UIButton` che può essere utilizzato per fornire il titolo e applicare lo stile con prodotti quali il colore di sfondo e immagini.

<a name="Button-Titles" />

### <a name="button-titles"></a>Pulsante titoli

Come abbiamo visto in precedenza, `UIButtons` sono diversi rispetto alla maggior parte `UIKit` controlli nel senso che hanno uno stato in modo che non è possibile semplicemente modificare il titolo, è necessario modificarlo per un determinato `UIControlState`. Ad esempio:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

È possibile impostare il colore del titolo del pulsante utilizzando la `SetTitleColor` (metodo). Ad esempio:

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

E modificare il titolo shadow usando il `SetTitleShadowColor`. Ad esempio:

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

È possibile impostare l'ombreggiatura del titolo da modificare *Engraved* al *in rilievo* quando il pulsante è evidenziato usando il codice seguente:

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

Inoltre, è possibile utilizzare testo con attributo come il titolo del pulsante. Ad esempio:

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

### <a name="button-images"></a>Icone dei pulsanti

Oggetto `UIButton` può avere un'immagine associata e può usare un'immagine come sfondo.

Per impostare l'immagine di sfondo di un pulsante per un determinato `UIControlState`, usare il codice seguente:

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Impostare il `AdjustsImageWhenHiglighted` proprietà `true` per disegnare l'immagine più chiara quando il pulsante è evidenziato (questo è il valore predefinito). Impostare il `AdjustsImageWhenDisabled` proprietà `true` per disegnare l'immagine più scura quando il pulsante è disabilitato (anche in questo caso, questo è il valore predefinito).

Per impostare l'immagine visualizzata sul pulsante, usare il codice seguente:

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Usare il `TintColor` proprietà da impostare una tonalità di colore che viene applicata il titolo e l'icona del pulsante. Per i pulsanti del `Custom` tipo, questa proprietà non ha alcun effetto, è necessario implementare il `TintColor` comportamento manualmente.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la progettazione e l'uso di pulsanti all'interno di un'app xamarin. tvos. È stato illustrato come usare i pulsanti in iOS Designer e su come creare pulsanti in C# codice. Infine, è stato illustrato come modificare il titolo del pulsante e modificarne l'aspetto e lo stile.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
