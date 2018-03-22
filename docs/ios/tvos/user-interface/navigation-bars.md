---
title: Utilizzo di controller di navigazione
description: Questo articolo descrive la progettazione e l'utilizzo di barre di navigazione all'interno di un'app Xamarin.tvOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 0f59e8f5e732a45f7e6148a08de80fffc56dbb26
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2018
---
# <a name="working-with-navigation-controllers"></a>Utilizzo di controller di navigazione

_Questo articolo descrive la progettazione e l'utilizzo di barre di navigazione all'interno di un'app Xamarin.tvOS._

È possibile aggiungere barre di navigazione nella parte superiore delle visualizzazioni per visualizzare un titolo e i pulsanti della barra di spostamento facoltativo. In genere vengono utilizzati quando l'utente è passato da una pagina principale, ad esempio una tabella, raccolta o una vista Menu per una visualizzazione secondaria che mostra i dettagli dell'elemento selezionato.

[![](navigation-bars-images/navbar01.png "Barra di spostamento di esempio")](navigation-bars-images/navbar01.png#lightbox)

Inoltre al titolo (che viene visualizzato al centro), barre di navigazione può contenere uno o più pulsanti della barra di spostamento (`UIBarButtonItem`) a sinistra e a destra della barra.

> [!IMPORTANT]
> Barre di navigazione sono completamente trasparenti per impostazione predefinita. Deve prestare attenzione per garantire che il contenuto della barra di spostamento leggibile sul contenuto disponibili al suo interno. Ad esempio, quando il contenuto in una vista tabella o una raccolta scorre in essa contenute.




<a name="Navigation-Bars-and-Storyboards" />

## <a name="navigation-bars-and-storyboards"></a>Barre di navigazione e gli storyboard

Il modo più semplice per utilizzare le barre di navigazione in un'app Xamarin.tvOS è per aggiungerli all'interfaccia utente dell'applicazione utilizzando la finestra di progettazione iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)


1. Nel **soluzione riempimento**, fare doppio clic su `Main.storyboard` e aprirlo per la modifica.
1. Trascinare un **barra di spostamento** dal **della casella degli strumenti** e rilasciarla nella vista nella parte superiore dello schermo: 

    [![](navigation-bars-images/navbar02.png "Una barra di navigazione")](navigation-bars-images/navbar02.png#lightbox)
1. Fare doppio clic su di **barra di spostamento** per selezionare questa opzione per **elemento di navigazione**. Nel **Widget** scheda della finestra di **proprietà riempimento**, è possibile impostare il **titolo**: 

    [![](navigation-bars-images/navbar03.png "Impostare il titolo")](navigation-bars-images/navbar03.png#lightbox)
1. Successivamente, è possibile aggiungere uno o più **elementi pulsante della barra** a delle estremità della barra di: 

    [![](navigation-bars-images/navbar04.png "Un elemento pulsante della barra")](navigation-bars-images/navbar04.png#lightbox)
1. Infine transito per il **elementi pulsante della barra** alle azioni nel **eventi** scheda della finestra il **Esplora proprietà**: 

    [![](navigation-bars-images/navbar05.png "Un pulsante elemento azione a barre")](navigation-bars-images/navbar05.png#lightbox)
1. Salvare le modifiche.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


1. Nel **Esplora**, fare doppio clic su `Main.storyboard` e aprirlo per la modifica.
1. Trascinare un **barra di spostamento** dal **della casella degli strumenti** e rilasciarla nella vista nella parte superiore dello schermo: 

    [![](navigation-bars-images/navbar02-vs.png "Una barra di navigazione")](navigation-bars-images/navbar02-vs.png#lightbox)
1. Fare doppio clic su di **barra di spostamento** per selezionare questa opzione per **elemento di navigazione**. Nel **Widget** scheda della finestra di **Esplora proprietà**, è possibile impostare il **titolo**: 

    [![](navigation-bars-images/navbar03-vs.png "Impostare il titolo")](navigation-bars-images/navbar03-vs.png#lightbox)
1. Successivamente, è possibile aggiungere uno o più **elementi pulsante della barra** a delle estremità della barra di: 

    [![](navigation-bars-images/navbar04-vs.png "Un pulsante elementi a barre")](navigation-bars-images/navbar04-vs.png#lightbox)
1. Infine transito per il **elementi pulsante della barra** alle azioni nel **eventi** scheda della finestra il **Esplora proprietà**: 

    [![](navigation-bars-images/navbar05-vs.png "Un elemento azioni a barre")](navigation-bars-images/navbar05-vs.png#lightbox)
1. Salvare le modifiche.


-----

> [!IMPORTANT]
> Sebbene sia possibile assegnare gli eventi, ad esempio `TouchUpInside` a un elemento dell'interfaccia utente (ad esempio un UIButton) nella finestra di progettazione iOS, non verrà mai chiamato perché Apple TV non ha un tocco supportano eventi tocco o sullo schermo. È consigliabile utilizzare sempre il `Primary Action` eventi durante la creazione di gestori eventi per tvOS elementi dell'interfaccia utente.




Il codice seguente viene fornito un esempio di gestori eventi in tre BarButtonItems diversi: `ShowFirstHotel`, `ShowSecondHotel`, e `ShowThirdHotel`. Quando viene fatto clic su ogni elemento, l'immagine di sfondo `HotelImage` viene modificato. Questo viene modificato nel Controller di visualizzazione (esempio `ViewController.cs`) file:

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

Fino a quando un pulsante `Enabled` proprietà `true` e non è coperto da un altro controllo o una vista, può essere resa di Siri remoto utilizzando l'elemento di messa a fuoco.

Per ulteriori informazioni sull'uso di storyboard, consultare il nostro [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è descritta la progettazione e l'utilizzo di barre di navigazione all'interno di un'app Xamarin.tvOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
