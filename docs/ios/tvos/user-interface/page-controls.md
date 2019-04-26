---
title: Utilizzo di controlli di pagina in Xamarin tvOS
description: Questo documento descrive come usare i controlli di pagina tvOS in un'app compilata con Xamarin. Fornisce una descrizione dettagliata dei controlli di pagina, viene illustrato come configurare gli storyboard e prende in esame come rispondere agli eventi di modifica pagina.
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 173bc7713b5b8c330d4d4c5863bef24be8bdcb52
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61179612"
---
# <a name="working-with-tvos-page-controls-in-xamarin"></a>Utilizzo di controlli di pagina in Xamarin tvOS

In alcuni casi potrebbe essere necessario visualizzare una serie di immagini o pagine nell'app xamarin. tvos. Un controllo pagina è stato progettato per illustrare chiaramente quale pagina di un utente si trova in rispetto al numero massimo di pagine. Un controllo pagina consente di visualizzare una serie di punti su scuro, oval a forma di sfondo. Nella pagina corrente verrà visualizzato un pallino, tutte le altre pagine mostrano come punti vuoti. Il controllo pagina verrà ritagliata i punti la maggior parte delle esterni se sono presenti troppi per rientrare nella relativa area di sfondo.

[![](page-controls-images/page01.png "Esempio di controllo pagina")](page-controls-images/page01.png#lightbox)

Controllo pagina in un elemento interattivo progettato per fornire commenti e suggerimenti solo all'utente. È necessario aggiungere altri controlli per modificare il numero di pagina correnti (ad esempio i movimenti o i pulsanti).

Apple ha i suggerimenti seguenti quando si usa un controllo pagina:

- **Usare solo le raccolte complete su** -controlli per le pagine funzionano meglio in un ambiente a schermo intero per visualizzare più pagine esistenti in una singola raccolta.
- **Limitare il numero di pagine** -controlli per le pagine funzionano meglio per le pagine di dieci (10) o meno e un massimo di venti (20) pagine. Per le pagine più di venti, è consigliabile usare un [visualizzazione di raccolta](~/ios/tvos/user-interface/collection-views.md) e visualizzare le pagine in una griglia.

<a name="Page-Controls-and-Storyboards" />

## <a name="page-controls-and-storyboards"></a>I controlli di pagina e gli storyboard

Il modo più semplice per usare i controlli di pagina in un'app xamarin. tvos è per aggiungerli all'interfaccia utente dell'app usando iOS Designer.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

    
1. Nel **riquadro della soluzione**, fare doppio clic il `Main.storyboard` file e aprirlo e modificarlo.
1. Trascinare un **controllo della pagina** dal **della casella degli strumenti** e rilasciarlo nella vista: 

    [![](page-controls-images/page02.png "Un controllo pagina")](page-controls-images/page02.png#lightbox)
1. Nel **scheda Widget** del **riquadro delle proprietà**, è possibile modificare diverse proprietà del controllo pagina come relativo **pagina corrente** e **& delle pagine**: 

    [![](page-controls-images/page03.png "La scheda di Widget")](page-controls-images/page03.png#lightbox)
1. Successivamente, aggiungere i movimenti o i controlli alla visualizzazione per spostarsi avanti e indietro tramite la raccolta di pagine.
1. Infine, assegnare **nomi** ai controlli in modo da poter rispondere ad essi in C# codice. Ad esempio: 

    [![](page-controls-images/page04.png "Nome del controllo")](page-controls-images/page04.png#lightbox)
1. Salvare le modifiche.
    

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    
1. Nel **Esplora soluzioni**, fare doppio clic il `Main.storyboard` file e aprirlo e modificarlo.
1. Trascinare un **controllo della pagina** dal **della casella degli strumenti** e rilasciarlo nella vista: 

    [![](page-controls-images/page02-vs.png "Un controllo pagina")](page-controls-images/page02-vs.png#lightbox)
1. Nel **scheda Widget** del **riquadro delle proprietà**, è possibile modificare diverse proprietà del controllo pagina, ad esempio relativi **pagina corrente** e **& delle pagine**: 

    [![](page-controls-images/page03-vs.png "La scheda di Widget")](page-controls-images/page03-vs.png#lightbox)
1. Successivamente, aggiungere i movimenti o i controlli alla visualizzazione per spostarsi avanti e indietro tramite la raccolta di pagine.
1. Infine, assegnare **nomi** ai controlli in modo da poter rispondere ad essi in C# codice. Ad esempio: 

    [![](page-controls-images/page04-vs.png "Nome del controllo")](page-controls-images/page04-vs.png#lightbox)
1. Salvare le modifiche.
    

-----

> [!IMPORTANT]
> Sebbene sia possibile assegnare gli eventi, ad esempio `TouchUpInside` a un elemento dell'interfaccia utente (ad esempio un UIButton) in iOS Designer, non verrà mai chiamato perché non dispone di un tocco Apple TV a schermo intero o supportare gli eventi di tocco. È consigliabile usare sempre la `Primary Action` eventi durante la creazione di gestori eventi per tvOS elementi dell'interfaccia utente.

Modificare il Controller di visualizzazione (esempio `ViewController.cs`) file e aggiungere il codice per gestire le pagine da modificare. Ad esempio:

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public nint PageNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            PageView.Pages = 6;
            ShowCat ();
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void NextCat (UIBarButtonItem sender) {

            // Display next Cat
            if (++PageNumber > 5) {
                PageNumber = 5;
            }
            ShowCat();

        }

        partial void PreviousCat (UIBarButtonItem sender) {
            // Display previous cat
            if (--PageNumber < 0) {
                PageNumber = 0;
            }
            ShowCat();
        }
        #endregion

        #region Private Methods
        private void ShowCat() {

            // Adjust UI
            PreviousButton.Enabled = (PageNumber > 0);
            NextButton.Enabled = (PageNumber < 5);
            PageView.CurrentPage = PageNumber;

            // Display new cat
            CatView.Image = UIImage.FromFile(string.Format("Cat{0:00}.jpg",PageNumber+1));
        }
        #endregion
    }
}
```

Diamo uno sguardo a due proprietà del controllo pagina. In primo luogo, per specificare il numero massimo di pagine, usare quanto segue:

```csharp
PageView.Pages = 6;
```

Per modificare il numero di pagina corrente, usare il codice seguente:

```csharp
PageView.CurrentPage = PageNumber;
```

Il `CurrentPage` proprietà è zero (0) di base, in modo che la prima pagina sarà zero e l'ultimo sarà uno meno il numero massimo di pagine.

Per altre informazioni sull'utilizzo degli storyboard, vedere la [App Tvos Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la progettazione e l'utilizzo di controllo pagina all'interno di un'app xamarin. tvos.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
