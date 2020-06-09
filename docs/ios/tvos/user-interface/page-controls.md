---
title: Uso dei controlli pagina di tvOS in Novell
description: Questo documento descrive come usare i controlli pagina di tvOS in un'app compilata con Novell. Viene fornita una descrizione di alto livello dei controlli pagina, viene illustrato come impostarli negli storyboard ed esamina come rispondere agli eventi di modifica della pagina.
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 9a45e87165101a8e8afcfc51d15d085982b6499e
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84569933"
---
# <a name="working-with-tvos-page-controls-in-xamarin"></a>Uso dei controlli pagina di tvOS in Novell

In alcuni casi potrebbe essere necessario visualizzare una serie di pagine o immagini nell'app Novell. tvOS. Un controllo pagina è stato progettato per indicare in modo chiaro quale pagina di un utente si trova al di fuori del numero massimo di pagine. Un controllo pagina consente di visualizzare una serie di punti su uno sfondo scuro a forma ovale. Nella pagina corrente viene visualizzato un punto pieno, tutte le altre pagine vengono visualizzate come punti vuoti. Il controllo pagina Ritaglia i punti più esterni se sono presenti troppi per adattarsi all'area di sfondo.

[![](page-controls-images/page01.png "Sample Page control")](page-controls-images/page01.png#lightbox)

Controllo pagina in un elemento non interattivo progettato per fornire commenti e suggerimenti all'utente. Sarà necessario aggiungere altri controlli per modificare il numero di pagina corrente, ad esempio movimenti o pulsanti.

Quando si usa un controllo pagina, Apple presenta i suggerimenti seguenti:

- **Usa solo in raccolte complete** : i controlli pagina funzionano meglio in un ambiente a schermo intero per visualizzare più pagine presenti in una singola raccolta.
- **Limitare il numero di** pagine-i controlli di pagina sono ideali per dieci (10) o un numero inferiore di pagine e un massimo di 20 (20) pagine. Per più di venti pagine, è consigliabile utilizzare una [visualizzazione di raccolta](~/ios/tvos/user-interface/collection-views.md) e visualizzare le pagine in una griglia.

<a name="Page-Controls-and-Storyboards"></a>

## <a name="page-controls-and-storyboards"></a>Controlli e storyboard della pagina

Il modo più semplice per usare i controlli pagina in un'app Novell. tvOS consiste nell'aggiungerli all'interfaccia utente dell'app tramite iOS designer.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

1. Nella **riquadro della soluzione**fare doppio clic sul `Main.storyboard` file e aprirlo per la modifica.
1. Trascinare un **controllo pagina** dalla **casella degli strumenti** e rilasciarlo nella visualizzazione:

    [![](page-controls-images/page02.png "A Page Control")](page-controls-images/page02.png#lightbox)
1. Nella **scheda Widget** del **riquadro delle proprietà**è possibile modificare diverse proprietà del controllo pagina, ad esempio la **pagina corrente** e il **numero di pagine**:

    [![](page-controls-images/page03.png "The Widget Tab")](page-controls-images/page03.png#lightbox)
1. Successivamente, aggiungere controlli o movimenti alla visualizzazione per spostarsi avanti e indietro nella raccolta di pagine.
1. Infine, assegnare i **nomi** ai controlli in modo che sia possibile rispondere a tali controlli nel codice C#. Ad esempio:

    [![](page-controls-images/page04.png "Name the control")](page-controls-images/page04.png#lightbox)
1. Salvare le modifiche.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Nella **Esplora soluzioni**fare doppio clic sul `Main.storyboard` file e aprirlo per la modifica.
1. Trascinare un **controllo pagina** dalla **casella degli strumenti** e rilasciarlo nella visualizzazione:

    [![](page-controls-images/page02-vs.png "A Page Control")](page-controls-images/page02-vs.png#lightbox)
1. Nella **scheda Widget** di **Esplora proprietà**è possibile modificare diverse proprietà del controllo pagina, ad esempio la **pagina corrente** e il **numero di pagine**:

    [![](page-controls-images/page03-vs.png "The Widget tab")](page-controls-images/page03-vs.png#lightbox)
1. Successivamente, aggiungere controlli o movimenti alla visualizzazione per spostarsi avanti e indietro nella raccolta di pagine.
1. Infine, assegnare i **nomi** ai controlli in modo che sia possibile rispondere a tali controlli nel codice C#. Ad esempio:

    [![](page-controls-images/page04-vs.png "Name the control")](page-controls-images/page04-vs.png#lightbox)
1. Salvare le modifiche.

-----

> [!IMPORTANT]
> Sebbene sia possibile assegnare eventi come `TouchUpInside` a un elemento dell'interfaccia utente (ad esempio UIButton) in iOS designer, questo non verrà mai chiamato perché Apple TV non ha un touchscreen o supporta eventi di tocco. È consigliabile usare sempre l' `Primary Action` evento quando si creano gestori eventi per gli elementi dell'interfaccia utente tvOS.

Modificare il file del controller di visualizzazione (esempio `ViewController.cs` ) e aggiungere il codice per gestire le pagine modificate. Ad esempio:

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

Verranno ora esaminate in dettaglio due proprietà del controllo pagina. In primo luogo, per specificare il numero massimo di pagine, utilizzare quanto segue:

```csharp
PageView.Pages = 6;
```

Per modificare il numero di pagina corrente, usare il codice seguente:

```csharp
PageView.CurrentPage = PageNumber;
```

La `CurrentPage` proprietà è zero (0) in base a, quindi la prima pagina sarà zero e l'ultimo sarà uno meno il numero massimo di pagine.

Per ulteriori informazioni sull'utilizzo degli storyboard, vedere la [Guida introduttiva Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Summary"></a>

## <a name="summary"></a>Summary

Questo articolo ha trattato la progettazione e l'uso del controllo pagina all'interno di un'app Novell. tvOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
