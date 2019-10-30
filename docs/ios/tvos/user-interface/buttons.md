---
title: Uso dei pulsanti tvOS in Novell
description: Questo documento descrive come usare i pulsanti in un'app tvOS compilata con Novell. Viene illustrato come utilizzare i pulsanti nel codice e negli storyboard ed esamina come applicare uno stile a un pulsante.
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/07/2017
ms.openlocfilehash: 559944e5ae168fd3b45c4d25a86705c5032b2e04
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030482"
---
# <a name="working-with-tvos-buttons-in-xamarin"></a>Uso dei pulsanti tvOS in Novell

Utilizzare un'istanza della classe `UIButton` per creare un pulsante selezionabile attivabile in una finestra tvOS. Quando l'utente seleziona un pulsante, invia un messaggio di azione all'oggetto di destinazione Consenti all'app Novell. tvOS di rispondere all'input dell'utente.

[![](buttons-images/buttons01.png "Example buttons")](buttons-images/buttons01.png#lightbox)

Per ulteriori informazioni sull'utilizzo dello stato attivo e sulla navigazione con Siri remote, vedere la documentazione relativa all' [utilizzo di spostamento e messa a fuoco](~/ios/tvos/app-fundamentals/navigation-focus.md) e [Siri remote e Bluetooth Controllers](~/ios/tvos/platform/remote-bluetooth.md) .

<a name="About-Buttons" />

## <a name="about-buttons"></a>Informazioni sui pulsanti

In tvOS i pulsanti vengono usati per le azioni specifiche dell'app e possono contenere un titolo, un'icona o entrambi. Quando l'utente naviga nell'interfaccia utente dell'app usando il [comando Siri Remote](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), lo stato attivo passa al pulsante dato che modifica il testo e i colori di sfondo. Viene anche applicata un'ombreggiatura al pulsante che aggiunge un effetto 3D, facendo in modo che appaia sopra il resto dell'interfaccia utente.

[![](buttons-images/buttons01.png "Example buttons")](buttons-images/buttons01.png#lightbox)

Apple presenta i suggerimenti seguenti per l'utilizzo dei pulsanti:

- **Usare un titolo o un'icona** -mentre è possibile includere sia un'icona che un titolo in un pulsante, lo spazio è limitato, quindi provare a non combinare entrambi.
- **Contrassegnare chiaramente i pulsanti distruttivi** : se il pulsante esegue un'azione distruttiva (ad esempio l'eliminazione di un file), contrassegnarlo come tale usando il testo e/o l'icona. Le azioni distruttive devono sempre presentare un [avviso](~/ios/tvos/user-interface/alerts.md) per chiedere all'utente di limitare l'azione.
- **Non usare i pulsanti indietro** : il pulsante di menu in Siri Remote viene usato per tornare alla schermata precedente. L'unica eccezione a questa regola riguarda gli acquisti in-app o le azioni distruttive in cui deve essere visualizzato un pulsante **Annulla** .

Per ulteriori informazioni sull'utilizzo dello stato attivo e della navigazione, vedere la documentazione relativa all' [utilizzo di navigazione e messa a fuoco](~/ios/tvos/app-fundamentals/navigation-focus.md) .

<a name="Button-Icons" />

### <a name="button-icons"></a>Icone pulsante

Apple suggerisce di usare immagini semplici e altamente riconoscibili per le icone dei pulsanti. Le icone eccessivamente complesse sono difficili da riconoscere su uno schermo TV in una stanza su un divano, quindi provare a usare la rappresentazione più semplice possibile per ottenere l'idea. Quando possibile, usare immagini standard e note per le icone, ad esempio una lente di ingrandimento per la ricerca.

<a name="Button-Titles" />

### <a name="button-titles"></a>Titoli dei pulsanti

Apple presenta i suggerimenti seguenti durante la creazione dei titoli per i pulsanti:

- **Mostra il testo descrittivo sotto i pulsanti delle icone** : laddove possibile, inserire i pulsanti Cancella testo descrittivo sotto solo icona per ottenere ulteriormente lo scopo del pulsante.
- **Usare i verbi o le frasi dei verbi per il titolo** : indicare chiaramente l'azione che verrà eseguita quando l'utente fa clic sul pulsante.
- **Usare l'uso di maiuscole in stile titolo** : ad eccezione di articoli, congiunti o preposizioni (quattro lettere o meno), ogni parola del titolo del pulsante deve essere in maiuscolo.
- **Usare un breve titolo al punto** : usare la verbosità più breve possibile per descrivere l'azione del pulsante.

<a name="Buttons-and-Storyboards" />

## <a name="buttons-and-storyboards"></a>Pulsanti e storyboard

Il modo più semplice per usare i pulsanti in un'app Novell. tvOS consiste nell'aggiungerli all'interfaccia utente dell'app usando il Xamarin Designer per iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nella **Esplora soluzioni**fare doppio clic sul file di `Main.storyboard` e aprirlo per la modifica.
1. Trascinare un **pulsante** dalla **libreria** e rilasciarlo nella visualizzazione: 

    [![](buttons-images/storyboard01.png "A button")](buttons-images/storyboard01.png#lightbox)
1. In **Esplora proprietà**è possibile modificare diverse proprietà del pulsante, ad esempio il **titolo** e il **colore del testo**: 

    [![](buttons-images/storyboard02.png "Button properties")](buttons-images/storyboard02.png#lightbox)
1. Passare quindi alla **scheda eventi** e collegare un **evento** dal **pulsante** e chiamarlo `ButtonPressed`: 

    [![](buttons-images/storyboard03.png "The Events Tab")](buttons-images/storyboard03.png#lightbox)
1. Si passerà automaticamente alla visualizzazione `ViewController.cs` in cui è possibile inserire la nuova azione nel codice usando i tasti freccia **su** e **giù** : 

    [![](buttons-images/storyboard04.png "Placing a new Action in code")](buttons-images/storyboard04.png#lightbox)
1. Premere **invio** per selezionare il percorso: 

    [![](buttons-images/storyboard05.png "The code editor")](buttons-images/storyboard05.png#lightbox)
1. Salvare le modifiche apportate a tutti i file.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Nella **Esplora soluzioni**fare doppio clic sul file di `Main.storyboard` e aprirlo per la modifica.
1. Trascinare un **pulsante** dalla **libreria** e rilasciarlo nella visualizzazione: 

    [![](buttons-images/storyboard01vs.png "A button")](buttons-images/storyboard01vs.png#lightbox)
1. In **Esplora proprietà**è possibile modificare diverse proprietà del pulsante, ad esempio il **titolo** e il **colore del testo**: 

    [![](buttons-images/storyboard02vs.png "The Properties Explorer")](buttons-images/storyboard02vs.png#lightbox)
1. Passare quindi alla **scheda eventi** e collegare un **evento** dal **pulsante** e chiamarlo `ButtonPressed`: 

    [![](buttons-images/storyboard03vs.png "The Events Tab")](buttons-images/storyboard03vs.png#lightbox)
1. Salvare le modifiche apportate a tutti i file.

Modificare il file del controller di visualizzazione (esempio `ViewController.cs`) e aggiungere il codice seguente per gestire il pulsante selezionato:

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

Fino a quando la proprietà `Enabled` di un pulsante è `true` e non è coperta da un altro controllo o visualizzazione, è possibile impostarla utilizzando Siri remote. Se l'utente seleziona il pulsante e fa clic sull'area di tocco, viene eseguita l'azione `ButtonPressed` definita sopra.

> [!IMPORTANT]
> Sebbene sia possibile assegnare azioni come `TouchUpInside` a un `UIButton` nella finestra di progettazione di iOS durante la creazione di un **gestore eventi**, non verrà mai chiamato perché Apple TV non ha un touch screen o supporta gli eventi di tocco. Quando si creano **azioni** per gli elementi dell'interfaccia utente di tvOS, è consigliabile usare sempre il **tipo di azione** predefinito.

Per ulteriori informazioni sull'utilizzo degli storyboard, vedere la [Guida introduttiva Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Buttons-and-Code" />

## <a name="buttons-and-code"></a>Pulsanti e codice

Facoltativamente, è possibile creare un `UIButton` nel C# codice e aggiungerlo alla vista dell'app tvOS. Esempio:

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

Quando si crea un nuovo `UIButton` nel codice, è necessario specificarne il `UIButtonType` come uno dei seguenti:

- **System** -questo è il tipo standard di Button presentato da tvOS ed è il tipo che verrà usato più di frequente.
- **DetailDisclosure** : presenta un tipo di pulsante "turn down" usato per nascondere o visualizzare informazioni dettagliate.
- **InfoDark** : un pulsante di informazioni dettagliate scuro Visualizza un "i" in un cerchio.
- **InfoLight** : un pulsante di informazioni dettagliate chiaro ha visualizzato una "i" in un cerchio.
- **AddContact** -visualizzare il pulsante come pulsante Aggiungi contatto.
- **Personalizzata** : consente di personalizzare diversi tratti del pulsante.

Definire quindi le dimensioni e il percorso sullo schermo del pulsante. Esempio:

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

Quindi, impostare il titolo per il pulsante. `UIButtons` sono diversi dalla maggior parte dei controlli `UIKit` perché hanno uno stato, quindi non è possibile semplicemente modificare il titolo, è necessario modificarlo per una determinata `UIControlState`. Esempio:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

Usare quindi l'evento `AllEvents` per vedere quando l'utente ha fatto clic sul pulsante. Esempio:

```csharp
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
```

Infine, aggiungere il pulsante alla visualizzazione per visualizzarlo:

```csharp
View.AddSubview (button);
```

> [!IMPORTANT]
> Sebbene sia possibile assegnare azioni come `TouchUpInside` a un `UIButton`, non verrà mai chiamato perché Apple TV non ha un touchscreen o non supporta gli eventi di tocco. È consigliabile usare sempre eventi come **AllEvents** o **PrimaryActionTriggered**.

<a name="Styling-a-Button" />

## <a name="styling-a-button"></a>Applicare uno stile a un pulsante

tvOS fornisce diverse proprietà di un `UIButton` che possono essere usate per fornire il titolo e lo stile con elementi come il colore di sfondo e le immagini.

<a name="Button-Titles" />

### <a name="button-titles"></a>Titoli dei pulsanti

Come illustrato in precedenza, `UIButtons` sono diversi rispetto alla maggior parte dei controlli `UIKit` perché hanno uno stato, quindi non è possibile semplicemente modificare il titolo, è necessario modificarlo per una determinata `UIControlState`. Esempio:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

È possibile impostare il colore del titolo per il pulsante usando il metodo `SetTitleColor`. Esempio:

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

È possibile modificare l'ombreggiatura del titolo usando il `SetTitleShadowColor`. Esempio:

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

È possibile impostare l'ombreggiatura del titolo per passare da *incisa* a *goffrato* quando il pulsante è evidenziato usando il codice seguente:

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

Inoltre, è possibile utilizzare il testo con attributi come titolo del pulsante. Esempio:

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

### <a name="button-images"></a>Immagini di pulsanti

A un `UIButton` può essere associata un'immagine e può utilizzare un'immagine come sfondo.

Per impostare l'immagine di sfondo di un pulsante per un `UIControlState`specificato, usare il codice seguente:

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Impostare la proprietà `AdjustsImageWhenHiglighted` su `true` per creare l'immagine più chiara quando il pulsante è evidenziato (impostazione predefinita). Impostare la proprietà `AdjustsImageWhenDisabled` su `true` per creare l'immagine più scura quando il pulsante è disabilitato (anche in questo caso, si tratta dell'impostazione predefinita).

Per impostare l'immagine visualizzata sul pulsante, usare il codice seguente:

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Utilizzare la proprietà `TintColor` per impostare una tinta del colore applicata sia al titolo sia all'immagine del pulsante. Per i pulsanti del tipo di `Custom`, questa proprietà non ha alcun effetto, quindi è necessario implementare autonomamente il comportamento del `TintColor`.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha trattato la progettazione e l'uso di pulsanti all'interno di un'app Novell. tvOS. È stato illustrato come usare i pulsanti in iOS designer e come creare i pulsanti nel C# codice. Infine, ha illustrato come modificare il titolo di un pulsante e modificarne lo stile e l'aspetto.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
