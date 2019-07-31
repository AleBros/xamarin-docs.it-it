---
title: Uso delle barre di spostamento di tvOS in Novell
description: Questo documento descrive come usare le barre di navigazione in un'app tvOS compilata con Novell. Viene illustrata la configurazione delle barre di spostamento in uno storyboard e la risposta agli eventi da questi pulsanti.
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 8bde37c907d2cad327dd2a72f26ee357a4391c43
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649050"
---
# <a name="working-with-tvos-navigation-bars-in-xamarin"></a>Uso delle barre di spostamento di tvOS in Novell

È possibile aggiungere barre di spostamento nella parte superiore delle visualizzazioni per visualizzare un titolo e i pulsanti della barra di navigazione facoltativa. Vengono in genere usati quando l'utente si è spostato da una pagina principale, ad esempio una visualizzazione tabella, una raccolta o un menu a una vista secondario che mostra i dettagli dell'elemento selezionato.

[![](navigation-bars-images/navbar01.png "Barra di spostamento di esempio")](navigation-bars-images/navbar01.png#lightbox)

Oltre al titolo (visualizzato al centro), le barre di navigazione possono contenere uno o più pulsanti della barra di spostamento (`UIBarButtonItem`) sui lati sinistro e destro della barra.

> [!IMPORTANT]
> Per impostazione predefinita, le barre di navigazione sono completamente trasparenti. È necessario prestare attenzione per garantire che il contenuto della barra di spostamento rimanga leggibile sul contenuto sottostante. Ad esempio, quando il contenuto in una vista tabella o in una raccolta scorre sotto di esso.

<a name="Navigation-Bars-and-Storyboards" />

## <a name="navigation-bars-and-storyboards"></a>Barre di spostamento e storyboard

Il modo più semplice per usare le barre di navigazione in un'app Novell. tvOS consiste nell'aggiungerle all'interfaccia utente dell'app tramite iOS designer.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nella **riquadro della soluzione**fare doppio clic su `Main.storyboard` file e aprirlo per la modifica.
1. Trascinare una **barra di spostamento** dalla **casella degli strumenti** e rilasciarla sulla visualizzazione nella parte superiore della schermata: 

    [![](navigation-bars-images/navbar02.png "Barra di spostamento")](navigation-bars-images/navbar02.png#lightbox)
1. Fare doppio clic sulla **barra di spostamento** per selezionare l' **elemento di navigazione**. Nella scheda **widget** del **riquadro delle proprietà**è possibile impostare il **titolo**: 

    [![](navigation-bars-images/navbar03.png "Imposta il titolo")](navigation-bars-images/navbar03.png#lightbox)
1. Successivamente, è possibile aggiungere uno o più **elementi del pulsante della barra** a una delle estremità della barra: 

    [![](navigation-bars-images/navbar04.png "Elemento di un pulsante A barre")](navigation-bars-images/navbar04.png#lightbox)
1. Infine, collegare gli elementi del **pulsante della barra** alle azioni nella scheda **eventi** di **Esplora proprietà**: 

    [![](navigation-bars-images/navbar05.png "Azione elemento pulsante A barre")](navigation-bars-images/navbar05.png#lightbox)
1. Salvare le modifiche.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. Nella **Esplora soluzioni**fare doppio clic su `Main.storyboard` file e aprirlo per la modifica.
1. Trascinare una **barra di spostamento** dalla **casella degli strumenti** e rilasciarla sulla visualizzazione nella parte superiore della schermata: 

    [![](navigation-bars-images/navbar02-vs.png "Barra di spostamento")](navigation-bars-images/navbar02-vs.png#lightbox)
1. Fare doppio clic sulla **barra di spostamento** per selezionare l' **elemento di navigazione**. Nella scheda **widget** di **Esplora proprietà**è possibile impostare il **titolo**: 

    [![](navigation-bars-images/navbar03-vs.png "Imposta il titolo")](navigation-bars-images/navbar03-vs.png#lightbox)
1. Successivamente, è possibile aggiungere uno o più **elementi del pulsante della barra** a una delle estremità della barra: 

    [![](navigation-bars-images/navbar04-vs.png "Elementi di un pulsante A barre")](navigation-bars-images/navbar04-vs.png#lightbox)
1. Infine, collegare gli elementi del **pulsante della barra** alle azioni nella scheda **eventi** di **Esplora proprietà**: 

    [![](navigation-bars-images/navbar05-vs.png "Azioni di elementi di un pulsante A barre")](navigation-bars-images/navbar05-vs.png#lightbox)
1. Salvare le modifiche.


-----

> [!IMPORTANT]
> Sebbene sia possibile assegnare eventi come `TouchUpInside` a un elemento dell'interfaccia utente (ad esempio UIButton) in iOS designer, questo non verrà mai chiamato perché Apple TV non ha un touchscreen o supporta eventi di tocco. È consigliabile usare sempre l' `Primary Action` evento quando si creano gestori eventi per gli elementi dell'interfaccia utente tvOS.

Il codice seguente fornisce un esempio di gestori di eventi in tre BarButtonItems diversi: `ShowFirstHotel`, `ShowSecondHotel`e `ShowThirdHotel`. Quando si fa clic su ogni elemento, l'immagine `HotelImage` di sfondo viene modificata. Questa operazione viene modificata nel file view controller ( `ViewController.cs`esempio):

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

Fino a quando la `Enabled` proprietà di un pulsante è `true` e non è coperta da un altro controllo o visualizzazione, è possibile fare in modo che l'elemento attivo usi Siri remote.

Per ulteriori informazioni sull'utilizzo degli storyboard, vedere la [Guida introduttiva Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha spiegato come progettare e usare le barre di navigazione all'interno di un'app Novell. tvOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
