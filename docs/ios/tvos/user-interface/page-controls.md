---
title: Utilizzo di controllo pagina
description: Questo articolo descrive la progettazione e l'utilizzo di controllo pagina all'interno di un'app Xamarin.tvOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: d4da50dac901628b9baf10a07650d232a977a653
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-page-control"></a>Utilizzo di controllo pagina

_Questo articolo descrive la progettazione e l'utilizzo di controllo pagina all'interno di un'app Xamarin.tvOS._

In alcuni casi potrebbe essere necessario visualizzare una serie di immagini o pagine nell'app Xamarin.tvOS. Un controllo pagina è stato progettato per mostrare con chiarezza la pagina di un utente rientra nel numero massimo di pagine. Un controllo pagina consente di visualizzare una serie di punti contro scuro, oval a forma di sfondo. Nella pagina corrente verrà visualizzato un pallino, tutte le altre pagine mostrano come punti vuoti. Il controllo pagina ridurrà la maggior parte dei punti esterni se vi sono troppi per le dimensioni dell'area di sfondo.

[ ![](page-controls-images/page01.png "Esempio di controllo pagina")](page-controls-images/page01.png)

Controllo pagina in un elemento interattivo progettato per fornire commenti e suggerimenti solo all'utente. È necessario aggiungere altri controlli per modificare il numero di pagina corrente (ad esempio i movimenti o i pulsanti).

Apple ha i seguenti suggerimenti quando si utilizza un controllo pagina:

- **Utilizzare solo le raccolte completo su** -pagina controlli funzionano meglio in un ambiente a schermo intero per visualizzare più pagine esistenti in una singola raccolta.
- **Limitare il numero di pagine** -pagina controlli funzionano meglio per dieci (10) o un numero inferiore di pagine e un massimo di venti (20) pagine. Per più di venti pagine, è consigliabile usare un [visualizzazione raccolta](~/ios/tvos/user-interface/collection-views.md) e visualizzare le pagine in una griglia.

<a name="Page-Controls-and-Storyboards" />

## <a name="page-controls-and-storyboards"></a>Controlli di pagina e di storyboard

Il modo più semplice per utilizzare i controlli di pagina in un'app Xamarin.tvOS è per aggiungerli all'interfaccia utente dell'applicazione utilizzando la finestra di progettazione iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

    
1. Nel **soluzione riempimento**, fare doppio clic su di `Main.storyboard` e aprirlo per la modifica.
1. Trascinare un **controllo pagina** dal **della casella degli strumenti** e rilasciarla sulla vista: 

    [ ![](page-controls-images/page02.png "Un controllo pagina")](page-controls-images/page02.png)
1. Nel **scheda Widget** del **proprietà riempimento**, è possibile modificare diverse proprietà del controllo pagina come relativo **pagina corrente** e **# di pagine**: 

    [ ![](page-controls-images/page03.png "La scheda Widget")](page-controls-images/page03.png)
1. Successivamente, è possibile aggiungere controlli o i movimenti alla visualizzazione per spostarsi avanti e indietro attraverso la raccolta di pagine.
1. Infine, assegnare **nomi** ai controlli in modo che è possibile rispondere ad essi nel codice c#. Ad esempio: 

    [ ![](page-controls-images/page04.png "Nome del controllo")](page-controls-images/page04.png)
1. Salvare le modifiche.
    

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

    
1. Nel **Esplora**, fare doppio clic su di `Main.storyboard` e aprirlo per la modifica.
1. Trascinare un **controllo pagina** dal **della casella degli strumenti** e rilasciarla sulla vista: 

    [ ![](page-controls-images/page02-vs.png "Un controllo pagina")](page-controls-images/page02-vs.png)
1. Nel **scheda Widget** del **Esplora proprietà**, è possibile modificare diverse proprietà del controllo pagina come relativo **pagina corrente** e **# di pagine**: 

    [ ![](page-controls-images/page03-vs.png "La scheda Widget")](page-controls-images/page03-vs.png)
1. Successivamente, è possibile aggiungere controlli o i movimenti alla visualizzazione per spostarsi avanti e indietro attraverso la raccolta di pagine.
1. Infine, assegnare **nomi** ai controlli in modo che è possibile rispondere ad essi nel codice c#. Ad esempio: 

    [ ![](page-controls-images/page04-vs.png "Nome del controllo")](page-controls-images/page04-vs.png)
1. Salvare le modifiche.
    

-----

> [!IMPORTANT]
> **Nota:** mentre è possibile assegnare gli eventi, ad esempio `TouchUpInside` a un elemento dell'interfaccia utente (ad esempio un UIButton) nella finestra di progettazione iOS, non verrà mai chiamato perché non dispone di un tocco Apple TV a schermo intero o supportare eventi tocco. È consigliabile utilizzare sempre il `Primary Action` eventi durante la creazione di gestori eventi per tvOS elementi dell'interfaccia utente.




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

Diamo un'occhiata due proprietà del controllo pagina. In primo luogo, per specificare il numero massimo di pagine, utilizzare le operazioni seguenti:

```csharp
PageView.Pages = 6;
```

Per modificare il numero di pagina corrente, utilizzare il codice seguente:

```csharp
PageView.CurrentPage = PageNumber;
```

Il `CurrentPage` proprietà è zero (0) di base, in modo che la prima pagina sarà zero e l'ultimo sarà uno meno il numero massimo di pagine.

Per ulteriori informazioni sull'uso di storyboard, consultare il nostro [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è descritta la progettazione e l'utilizzo di controllo pagina all'interno di un'app Xamarin.tvOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
