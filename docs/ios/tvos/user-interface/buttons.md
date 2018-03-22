---
title: Utilizzo di pulsanti
description: Questo articolo descrive la progettazione e l'utilizzo di pulsanti all'interno di un'app Xamarin.tvOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/07/2017
ms.openlocfilehash: 5da834e7e77fb8ab080c124bd09653bc840be3b0
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2018
---
# <a name="working-with-buttons"></a>Utilizzo di pulsanti

_Questo articolo descrive la progettazione e l'utilizzo di pulsanti all'interno di un'app Xamarin.tvOS._


Utilizzare un'istanza di `UIButton` classe per creare un pulsante con stato attivabile, selezionabile in una finestra tvOS. Quando l'utente seleziona un pulsante, invia un messaggio di azione per l'oggetto di destinazione consentono la risposta di app Xamarin.tvOS all'utente di input.

[![](buttons-images/buttons01.png "Pulsanti di esempio")](buttons-images/buttons01.png#lightbox)

Per ulteriori informazioni sull'uso dello stato attivo e spostarsi con Siri remoto, vedere il nostro [utilizzo di navigazione e lo stato attivo](~/ios/tvos/app-fundamentals/navigation-focus.md) e [Siri remoto e i controller Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentazione.

<a name="About-Buttons" />

## <a name="about-buttons"></a>Informazioni sui pulsanti

In tvOS, i pulsanti vengono utilizzati per azioni specifiche dell'applicazione e possono contenere un titolo, un'icona o entrambi. Mentre l'utente passa tramite interfaccia utente dell'applicazione di [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), lo stato attivo passa al pulsante specificato, rendendolo di modificare i colori di sfondo e di testo. Un'ombreggiatura viene applicata anche al pulsante di aggiunta di un effetto 3D, facendola apparire a salire sopra il resto dell'interfaccia utente.

[![](buttons-images/buttons01.png "Pulsanti di esempio")](buttons-images/buttons01.png#lightbox)

Apple ha i seguenti suggerimenti per l'utilizzo di pulsanti:

- **Utilizzare un titolo o un'icona** - durante entrambi un'icona e un titolo può essere inclusi in un pulsante, lo spazio è limitato pertanto provare a non combinare entrambe le opzioni.
- **Chiaramente i pulsanti distruttiva contrassegno** : se il pulsante esegue un sistema azione (ad esempio l'eliminazione di un file), chiaramente contrassegnarlo come tale utilizzando testo e/o sull'icona. Le azioni di distruzione devono sempre presentare un [avviso](~/ios/tvos/user-interface/alerts.md) che chiede all'utente per limitare l'azione.
- **Non utilizzare nuovamente pulsanti** -pulsante di Menu in remoto Siri viene usato per tornare alla schermata precedente. È l'unica eccezione a questa regola per gli acquisti In-App o distruttive in cui un **Annulla** pulsante deve essere visualizzato.

Per ulteriori informazioni sull'utilizzo con lo stato attivo e navigazione, consultare il nostro [utilizzo di navigazione e lo stato attivo](~/ios/tvos/app-fundamentals/navigation-focus.md) documentazione.

<a name="Button-Icons" />

### <a name="button-icons"></a>Icone dei pulsanti

Apple suggerisce che è utilizzare semplici e facilmente riconoscibile immagini per le icone del pulsante. Icone eccessivamente complesse sono difficili da riconoscere sullo schermo in una chat room in un divano, pertanto provare a utilizzare la rappresentazione più semplice possibile rendere l'idea. Quando possibile, utilizzare standard, conosciuto, le immagini per le icone (ad esempio una lente di ingrandimento per la ricerca).

<a name="Button-Titles" />

### <a name="button-titles"></a>Pulsante titoli

Quando si creano i titoli per i pulsanti, Apple ha i suggerimenti seguenti:

- **Mostra testo descrittivo sotto le icone pulsanti** : se possibile, inserire il testo descrittivo, deselezionare sotto l'icona solo i pulsanti per ottenere ulteriori lo scopo del pulsante tra.
- **Usare verbi o frasi verbo per il titolo** -inserire stato chiaramente l'azione che verrà eseguita quando l'utente fa clic sul pulsante.
- **Utilizzare l'uso delle maiuscole stile titolo** , fatta eccezione per gli articoli, congiunzioni o preposizioni (quattro lettere minore o uguale), ogni parola del titolo del pulsante deve essere in maiuscolo.
- **Utilizzare un breve, il titolo al punto** -utilizzare la lettera di possibili più breve per descrivere l'azione del pulsante.

<a name="Buttons-and-Storyboards" />

## <a name="buttons-and-storyboards"></a>I pulsanti e gli storyboard

Il modo più semplice per utilizzare i pulsanti in un'app Xamarin.tvOS è per aggiungerli all'interfaccia utente dell'applicazione utilizzando la finestra di progettazione di Xamarin per iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)


1. Nel **Esplora**, fare doppio clic su di `Main.storyboard` e aprirlo per la modifica.
1. Trascinare un **pulsante** dal **libreria** e rilasciarla sulla vista: 

    [![](buttons-images/storyboard01.png "Un pulsante")](buttons-images/storyboard01.png#lightbox)
1. Nel **Esplora proprietà**, è possibile modificare diverse proprietà del pulsante, ad esempio il **titolo** e **colore del testo**: 

    [![](buttons-images/storyboard02.png "Proprietà dei pulsanti")](buttons-images/storyboard02.png#lightbox)
1. Successivamente, passare al **scheda eventi** associazione e un **evento** dal **pulsante** e chiamarlo `ButtonPressed`: 

    [![](buttons-images/storyboard03.png "Nella scheda eventi")](buttons-images/storyboard03.png#lightbox)
1. Passa automaticamente al `ViewController.cs` vista in cui è possibile inserire la nuova azione nel codice utilizzando il **backup** e **verso il basso** tasti: 

    [![](buttons-images/storyboard04.png "Inserimento di una nuova azione nel codice")](buttons-images/storyboard04.png#lightbox)
1. Premere il **invio** per selezionare il percorso: 

    [![](buttons-images/storyboard05.png "L'editor di codice")](buttons-images/storyboard05.png#lightbox)
1. Salvare le modifiche a tutti i file.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Nel **Esplora**, fare doppio clic su di `Main.storyboard` e aprirlo per la modifica.
1. Trascinare un **pulsante** dal **libreria** e rilasciarla sulla vista: 

    [![](buttons-images/storyboard01vs.png "Un pulsante")](buttons-images/storyboard01vs.png#lightbox)
1. Nel **Esplora proprietà**, è possibile modificare diverse proprietà del pulsante, ad esempio il **titolo** e **colore del testo**: 

    [![](buttons-images/storyboard02vs.png "Esplora risorse di proprietà")](buttons-images/storyboard02vs.png#lightbox)
1. Successivamente, passare al **scheda eventi** associazione e un **evento** dal **pulsante** e chiamarlo `ButtonPressed`: 

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

Fino a quando un pulsante `Enabled` proprietà `true` e non è coperto da un altro controllo o una vista, può essere resa di Siri remoto utilizzando l'elemento di messa a fuoco. Se l'utente seleziona il pulsante e fa clic sull'area di tocco, la `ButtonPressed` azione definita in precedenza sarebbe stato eseguito.

> [!IMPORTANT]
> Sebbene sia possibile assegnare le azioni, ad esempio `TouchUpInside` a un `UIButton` in iOS progettazione durante la creazione di un' **gestore dell'evento**, non verrà mai chiamato perché Apple TV non ha un tocco supportano eventi tocco o sullo schermo. È consigliabile utilizzare sempre il valore predefinito **tipo di azione** durante la creazione di **azioni** per tvOS elementi dell'interfaccia utente.




Per ulteriori informazioni sull'uso di storyboard, consultare il nostro [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md).

<a name="Buttons-and-Code" />

## <a name="buttons-and-code"></a>I pulsanti e codice

Facoltativamente, un `UIButton` può essere creato nel codice c# e aggiunte alla vista tvOS dell'app. Ad esempio:

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

Quando si crea un nuovo `UIButton` nel codice, specificare il relativo `UIButtonType` come uno dei seguenti:

- **Sistema** -questo è il tipo di pulsante presentato dal tvOS standard ed è il tipo che verrà utilizzato più frequentemente.
- **DetailDisclosure** -presenta un tipo di pulsante utilizzato per nascondere o visualizzare informazioni dettagliate di "in basso a".
- **InfoDark** -scuro dettagliate info pulsante visualizzato "i" in un cerchio.
- **InfoLight** -una luce dettagliate info pulsante visualizzato "i" in un cerchio.
- **AddContact** -visualizzare il pulsante come pulsante Aggiungi contatto.
- **Custom** -consente di personalizzare i vari tratti del pulsante.

Successivamente, è possibile definire la dimensione su schermo e la posizione del pulsante. Esempio:

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

Quindi, impostare il titolo del pulsante. `UIButtons` sono diversi rispetto alla maggior parte `UIKit` controlli che hanno uno stato non è possibile semplicemente semplicemente modificare il titolo, è necessario modificarlo per un determinato `UIControlState`. Ad esempio:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

Successivamente, utilizzare il `AllEvents` evento per verificare quando l'utente ha fatto clic sul pulsante. Esempio:

```csharp
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
```

Infine, Aggiungi il pulsante per la vista per visualizzarlo:

```csharp
View.AddSubview (button);
```

> [!IMPORTANT]
> Sebbene sia possibile assegnare le azioni, ad esempio `TouchUpInside` a un `UIButton`, non verrà mai chiamato perché Apple TV non ha un tocco supportano eventi tocco o sullo schermo. È necessario utilizzare sempre gli eventi, ad esempio **AllEvents** o **PrimaryActionTriggered**.




<a name="Styling-a-Button" />

## <a name="styling-a-button"></a>Disegna un pulsante

tvOS fornisce diverse proprietà di un `UIButton` che può essere utilizzato per fornire il titolo e applicare uno stile con elementi come colore di sfondo e immagini.

<a name="Button-Titles" />

### <a name="button-titles"></a>Pulsante titoli

Come illustrato sopra, `UIButtons` sono diversi rispetto alla maggior parte `UIKit` controlli che hanno uno stato non è possibile semplicemente semplicemente modificare il titolo, è necessario modificarlo per un determinato `UIControlState`. Ad esempio:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

È possibile impostare il colore del titolo per il pulsante utilizzando la `SetTitleColor` metodo. Ad esempio:

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

E modificare il titolo shadow usando la `SetTitleShadowColor`. Ad esempio:

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

È possibile impostare l'ombreggiatura titolo modificare da *Engraved* a *in rilievo* quando il pulsante è evidenziato utilizzando il codice seguente:

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

Inoltre, è possibile utilizzare con gli attributi di testo come titolo del pulsante. Ad esempio:

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

### <a name="button-images"></a>Immagini dei pulsanti

Oggetto `UIButton` può avere un'immagine associata e può utilizzare un'immagine come sfondo.

Per impostare l'immagine di sfondo di un pulsante per un determinato `UIControlState`, utilizzare il codice seguente:

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Impostare il `AdjustsImageWhenHiglighted` proprietà `true` per disegnare l'immagine più chiara quando il pulsante è evidenziato (questo è il valore predefinito). Impostare il `AdjustsImageWhenDisabled` proprietà `true` per disegnare l'immagine più scura quando il pulsante è disabilitato (anche in questo caso il valore predefinito).

Per impostare l'immagine visualizzata sul pulsante, usare il codice seguente:

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Utilizzare il `TintColor` proprietà per impostare una tonalità di colore che viene applicata il titolo e l'icona del pulsante. Per i pulsanti del `Custom` tipo, questa proprietà non ha alcun effetto, è necessario implementare la `TintColor` comportamento manualmente.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è descritta la progettazione e l'utilizzo di pulsanti all'interno di un'app Xamarin.tvOS. È stato illustrato come utilizzare i pulsanti nella finestra di progettazione iOS e come creare pulsanti nel codice c#. Infine, è stato spiegato come modificare il titolo di un pulsante e modificarne l'aspetto e lo stile.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
