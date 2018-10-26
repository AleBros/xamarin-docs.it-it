---
title: Utilizzo di barre di navigazione in Xamarin tvOS
description: Questo documento descrive come usare le barre di spostamento in un'app tvOS compilate con Xamarin. Illustra l'impostazione di barre di navigazione in uno storyboard e risposta agli eventi da questi pulsanti.
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 81e6cfe1e532bcfa7616e35adb28b314587bafc8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106949"
---
# <a name="working-with-tvos-navigation-bars-in-xamarin"></a>Utilizzo di barre di navigazione in Xamarin tvOS

È possibile aggiungere barre di navigazione nella parte superiore delle visualizzazioni da visualizzare un titolo e i pulsanti della barra di navigazione facoltative. In genere vengono utilizzati quando l'utente è passato da una pagina principale, ad esempio una tabella, raccolta o una vista Menu per una visualizzazione secondaria che visualizza i dettagli dell'elemento selezionato.

[![](navigation-bars-images/navbar01.png "Barra di spostamento di esempio")](navigation-bars-images/navbar01.png#lightbox)

Oltre al titolo (che viene visualizzato al centro), barre di navigazione può contenere uno o più pulsanti della barra di spostamento (`UIBarButtonItem`) a sinistra e destra della barra.

> [!IMPORTANT]
> Barre di navigazione sono completamente trasparenti per impostazione predefinita. Dovrebbe prestare attenzione per assicurarsi che il contenuto della barra di spostamento rimane leggibile il contenuto in esso contenuti. Ad esempio, quando il contenuto in una raccolta o una vista tabella scorre sotto di esso.

<a name="Navigation-Bars-and-Storyboards" />

## <a name="navigation-bars-and-storyboards"></a>Barre di spostamento e gli storyboard

Il modo più semplice per lavorare con le barre di spostamento in un'app xamarin. tvos è per aggiungerli all'interfaccia utente dell'app usando iOS Designer.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nel **riquadro della soluzione**, fare doppio clic su `Main.storyboard` file e aprirlo e modificarlo.
1. Trascinare un **barra di spostamento** dalle **della casella degli strumenti** e rilasciarlo nella visualizzazione nella parte superiore della schermata: 

    [![](navigation-bars-images/navbar02.png "Una barra di spostamento")](navigation-bars-images/navbar02.png#lightbox)
1. Fare doppio clic sulla **sulla barra di navigazione** per selezionare questa opzione per **elemento di navigazione**. Nel **Widget** scheda della finestra di **riquadro delle proprietà**, è possibile impostare il **titolo**: 

    [![](navigation-bars-images/navbar03.png "Impostare il titolo")](navigation-bars-images/navbar03.png#lightbox)
1. Successivamente, è possibile aggiungere uno o più **elementi di pulsante della barra** a un'estremità della barra di: 

    [![](navigation-bars-images/navbar04.png "Un elemento pulsante della barra")](navigation-bars-images/navbar04.png#lightbox)
1. Infine wireup il **gli elementi di pulsante della barra** alle azioni nel **eventi** scheda della finestra di **Esplora proprietà**: 

    [![](navigation-bars-images/navbar05.png "Una barra azione dell'elemento pulsante")](navigation-bars-images/navbar05.png#lightbox)
1. Salvare le modifiche.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. Nel **Esplora soluzioni**, fare doppio clic su `Main.storyboard` file e aprirlo e modificarlo.
1. Trascinare un **barra di spostamento** dalle **della casella degli strumenti** e rilasciarlo nella visualizzazione nella parte superiore della schermata: 

    [![](navigation-bars-images/navbar02-vs.png "Una barra di spostamento")](navigation-bars-images/navbar02-vs.png#lightbox)
1. Fare doppio clic sulla **sulla barra di navigazione** per selezionare questa opzione per **elemento di navigazione**. Nel **Widget** scheda della finestra di **riquadro delle proprietà**, è possibile impostare il **titolo**: 

    [![](navigation-bars-images/navbar03-vs.png "Impostare il titolo")](navigation-bars-images/navbar03-vs.png#lightbox)
1. Successivamente, è possibile aggiungere uno o più **elementi di pulsante della barra** a un'estremità della barra di: 

    [![](navigation-bars-images/navbar04-vs.png "Una barra che gli elementi di pulsante")](navigation-bars-images/navbar04-vs.png#lightbox)
1. Infine wireup il **gli elementi di pulsante della barra** alle azioni nel **eventi** scheda della finestra di **Esplora proprietà**: 

    [![](navigation-bars-images/navbar05-vs.png "Oggetto della barra delle azioni per l'elemento pulsante")](navigation-bars-images/navbar05-vs.png#lightbox)
1. Salvare le modifiche.


-----

> [!IMPORTANT]
> Sebbene sia possibile assegnare gli eventi, ad esempio `TouchUpInside` a un elemento dell'interfaccia utente (ad esempio un UIButton) in iOS Designer, non verrà mai chiamato perché non dispone di un tocco Apple TV a schermo intero o supportare gli eventi di tocco. È consigliabile usare sempre la `Primary Action` eventi durante la creazione di gestori eventi per tvOS elementi dell'interfaccia utente.

Il codice seguente illustra un esempio di gestori degli eventi su tre diversi BarButtonItems: `ShowFirstHotel`, `ShowSecondHotel`, e `ShowThirdHotel`. Quando viene selezionato ogni elemento, l'immagine di sfondo `HotelImage` viene modificato. Ciò viene modificato nel Controller di visualizzazione (esempio `ViewController.cs`) file:

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void ShowFirstHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel01.jpg");
        }

        partial void ShowSecondHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel02.jpg");
        }

        partial void ShowThirdHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel03.jpg");
        }
        #endregion
    }
}
```

Fino a quando un pulsante `Enabled` è di proprietà `true` e non è coperto da un altro controllo o una vista, è possibile renderla l'elemento messa a fuoco utilizzando il remoti per Siri.

Per altre informazioni sull'utilizzo degli storyboard, vedere la [App Tvos Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la progettazione e l'utilizzo di barre di spostamento all'interno di un'app xamarin. tvos.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
