---
title: Utilizzo di tvOS campi Text e ricerca in Xamarin
description: Questo documento descrive come usare i campi text e ricerca in un'app tvOS compilate con Xamarin. Fornisce una panoramica generale dei campi text e ricerca e vengono illustrate le tastiere, integrazione dello storyboard, modelli di dati di ricerca e altro ancora.
ms.prod: xamarin
ms.assetid: 9EE63CA6-2F31-4EE0-AAE5-82E18CFAC06C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: f1085044f2147bec0910a87f8a6174c4648ef9b8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61162431"
---
# <a name="working-with-tvos-text-and-search-fields-in-xamarin"></a>Utilizzo di tvOS campi Text e ricerca in Xamarin

Quando richiesto, l'app xamarin. tvos può richiedere piccole parti di testo da parte dell'utente (ad esempio ID utente e password) usando un campo di testo e la tastiera su schermo:

[![](text-fields-and-search-images/intro01.png "Campo di ricerca di esempio")](text-fields-and-search-images/intro01.png#lightbox)

È anche possibile specificare capacità di ricerca di parola chiave del contenuto dell'app usando un campo di ricerca:

[![](text-fields-and-search-images/intro02.png "Risultati della ricerca di esempio")](text-fields-and-search-images/intro02.png#lightbox)

Questo documento illustra i dettagli di utilizzo di testo e campi di ricerca in un'app xamarin. tvos.

<a name="About-Text-and-Search-Fields" />

## <a name="about-text-and-search-fields"></a>Informazioni sul testo e campi di ricerca

Come indicato in precedenza, se necessario, di xamarin. tvos può presentare uno o più campi di testo per la raccolta di piccole quantità di testo dall'utente se si utilizza sullo schermo (o della tastiera bluetooth facoltativa a seconda della versione di tvOS installata dall'utente). 

Aggiunta, se l'app presenta grandi quantità di contenuto all'utente (ad esempio un musica, film o una raccolta di immagini), si potrebbe essere necessario includere un campo di ricerca che consente all'utente di immettere una piccola quantità di testo per filtrare l'elenco di elementi disponibili.

<a name="Text-Fields" />

## <a name="text-fields"></a>Campi di testo

In tvOS, un campo di testo viene presentato come una casella angolo arrotondato, altezza fissa movimento attiverà una tastiera su schermo quando l'utente fa clic su di essa:

[![](text-fields-and-search-images/text01.png "Testo In campi tvOS")](text-fields-and-search-images/text01.png#lightbox)

Quando l'utente sposta [messa a fuoco](~/ios/tvos/app-fundamentals/navigation-focus.md) per un determinato campo di testo, verrà aumento delle dimensioni più grande e visualizza un'ombra avanzata. È necessario tenere a mente durante la progettazione dell'interfaccia utente, come gli altri elementi dell'interfaccia utente quando messa a fuoco possono sovrapporsi a campi di testo.

Apple ha i seguenti suggerimenti per l'utilizzo di campi di testo:

- **Usare testo voce sporadicamente** : a causa della natura della tastiera su schermo, immettendo lungo sezioni di testo o la compilazione di più campi di testo è noioso all'utente. Una soluzione migliore consiste nel limitare la quantità di immissione di testo tramite gli elenchi di selezione oppure [pulsanti](~/ios/tvos/user-interface/buttons.md).
- **Utilizzare gli hint a scopo di comunicare** -campo di testo è possibile visualizzare suggerimenti"segnaposto" quando è vuoto. Ove applicabile, usare gli hint per descrivere lo scopo del campo di testo invece di un'etichetta separata.
- **Selezionare il tipo appropriato da tastiera predefinita** -tvOS fornisce diversi tipi di tastiera diverso, scopo compilato che è possibile specificare per il campo di testo. Ad esempio, la tastiera di indirizzo di posta elettronica facilita voce, consentendo all'utente di selezionare da un elenco di indirizzi immessi di recente.
- **Quando appropriato, usare i campi di testo protetto** -un campo di testo protetto presenta i caratteri immessi come punti (anziché le lettere reali). Usare sempre un campo di testo protetto per la raccolta di informazioni riservate, ad esempio le password.

<a name="Keyboards" />

## <a name="keyboards"></a>Tastiere

Ogni volta che l'utente fa clic su un campo di testo nell'interfaccia utente, lineari sullo schermo viene visualizzata della tastiera. L'utente usa l'area Touch il [remoti per Siri](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) per selezionare le singole lettere dalla tastiera e immettere le informazioni richieste:

[![](text-fields-and-search-images/keyboard01.png "La tastiera remoti per Siri")](text-fields-and-search-images/keyboard01.png#lightbox)

Se è presente più di un campo di testo nella visualizzazione corrente, un **successivo** pulsante verrà visualizzato automaticamente per rendere l'utente per il campo di testo successivo. Oggetto **Done** pulsante verrà visualizzato per l'ultimo campo di testo che terminerà l'immissione di testo e restituire l'utente alla schermata precedente. 

In qualsiasi momento, l'utente è possibile anche premere il **Menu** pulsante nell'elemento remoto di Siri per terminare l'immissione di testo e restituire nuovamente alla schermata precedente.

Apple ha i seguenti suggerimenti per l'utilizzo su schermo tastiere:

- **Selezionare il tipo appropriato da tastiera predefinita** -tvOS fornisce diversi tipi di tastiera diverso, scopo compilato che è possibile specificare per il campo di testo. Ad esempio, la tastiera di indirizzo di posta elettronica facilita voce, consentendo all'utente di selezionare da un elenco di indirizzi immessi di recente.
- **Quando appropriato, utilizzare le visualizzazioni accessorio tastiera** : oltre alle standard informazioni che viene sempre visualizzato facoltativo accessorio visualizzazioni (ad esempio immagini o etichette) possono essere aggiunte alla tastiera su schermo per chiarire lo scopo di immissione di testo o a assistere l'utente immette le informazioni necessarie.

Per altre informazioni sull'utilizzo con la tastiera su schermo, vedere di Apple [UIKeyboardType](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextInputTraits_Protocol/index.html#//apple_ref/c/tdef/UIKeyboardType), [la gestione della tastiera](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/KeyboardManagement/KeyboardManagement.html#//apple_ref/doc/uid/TP40009542-CH5-SW1), [visualizzazioni personalizzate per l'Input di dati](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/InputViews/InputViews.html#//apple_ref/doc/uid/TP40009542-CH12-SW1) e [ Guida per programmatori testo ai dispositivi iOS](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html) documentazione.

<a name="Search" />

## <a name="search"></a>Cerca

Un campo di ricerca viene visualizzata una schermata di specializzato che fornisce un campo di testo e della tastiera su schermo che consente all'utente filtrare una raccolta di elementi che sono visualizzate sotto la tastiera:

[![](text-fields-and-search-images/search01.png "Risultati della ricerca di esempio")](text-fields-and-search-images/search01.png#lightbox)

Quando l'utente ne immette di lettere nel campo di ricerca, i risultati riportati di seguito rifletterà automaticamente i risultati della ricerca. In qualsiasi momento, l'utente può spostare lo stato attivo per i risultati e selezionare uno degli elementi presentati.

Apple ha i seguenti suggerimenti per l'utilizzo di campi di ricerca:

- **Fornire le ricerche recenti** - perché immettendo il testo con il remoti per Siri può rivelarsi noiosa e gli utenti tendono a ripetere le richieste di ricerca, è possibile aggiungere una sezione dei risultati della ricerca recente prima i risultati correnti nell'area della tastiera.
- **Quando possibile, limitare il numero di risultati** - perché un lungo elenco di elementi può essere difficile all'utente di analizzare e passare, è consigliabile limitare il numero di risultati restituiti.
- **Se appropriato, il risultato di ricerca specificare filtri** : se il contenuto fornito dalla tua app si presta, considerare l'aggiunta di barre di ambito per consentire all'utente filtrare ulteriormente i risultati della ricerca restituiti.

Per altre informazioni, vedere di Apple [riferimento alla classe UISearchController](https://developer.apple.com/library/tvos/documentation/UIKit/Reference/UISearchController/index.html).

<a name="Working-with-Text-Fields" />

## <a name="working-with-text-fields"></a>Utilizzo dei campi di testo

Il modo più semplice per lavorare con i campi di testo in un'app xamarin. tvos è per aggiungerli alla progettazione dell'interfaccia utente usando iOS Designer.

Seguire questa procedura:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nel **riquadro della soluzione**, fare doppio clic il `Main.storyboard` file per aprirlo e modificarlo.
1. Trascinare uno o più **campi di testo** int nell'area di progettazione in una visualizzazione: 

    [![](text-fields-and-search-images/text02.png "Un campo di testo")](text-fields-and-search-images/text02.png#lightbox)
1. Selezionare il **campi di testo** e assegnare a ognuno un valore univoco **nome** nel **Widget** scheda della finestra di **riquadro delle proprietà**: 

    [![](text-fields-and-search-images/text03.png "La scheda di Widget del riquadro delle proprietà")](text-fields-and-search-images/text03.png#lightbox)
1. Nel **campo di testo** sezione, è possibile definire elementi, ad esempio le **segnaposto** hint e predefinito **valore**: 

    [![](text-fields-and-search-images/text04.png "La sezione dei campi di testo")](text-fields-and-search-images/text04.png#lightbox)
1. Scorrere verso il basso per definire le proprietà, ad esempio **ortografico**, **maiuscole/minuscole** e il valore predefinito **tastiera tipo**: 

    [![](text-fields-and-search-images/text05.png "Controllo ortografico, maiuscole/minuscole e il tipo di tastiera predefinito")](text-fields-and-search-images/text05.png#lightbox) 
1. Salvare le modifiche dello Storyboard.
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. In **Esplora soluzioni** fare doppio clic sul file `Main.storyboard` per aprirlo e modificarlo.
1. Trascinare uno o più **campi di testo** int nell'area di progettazione in una visualizzazione: 

    [![](text-fields-and-search-images/text02-vs.png "Un campo di testo")](text-fields-and-search-images/text02-vs.png#lightbox)
1. Selezionare il **campi di testo** e assegnare a ognuno un valore univoco **nome** nel **Widget** scheda della finestra di **Esplora proprietà**: 

    [![](text-fields-and-search-images/text03-vs.png "La scheda di Widget")](text-fields-and-search-images/text03-vs.png#lightbox)
1. Nel **campo di testo** sezione, è possibile definire elementi, ad esempio le **segnaposto** hint e predefinito **valore**: 

    [![](text-fields-and-search-images/text04-vs.png "La sezione dei campi di testo")](text-fields-and-search-images/text04-vs.png#lightbox)
1. Scorrere verso il basso per definire le proprietà, ad esempio **ortografico**, **maiuscole/minuscole** e il valore predefinito **tastiera tipo**: 

    [![](text-fields-and-search-images/text05-vs.png "Controllo ortografico, maiuscole/minuscole e il tipo di tastiera predefinito")](text-fields-and-search-images/text05-vs.png#lightbox) 
1. Salvare le modifiche dello Storyboard.
    
-----

Nel codice, è possibile ottenere o impostare il valore di un campo di testo utilizzando le `Text` proprietà:

```csharp
Console.WriteLine ("User ID {0} and Password {1}", UserId.Text, Password.Text);
```

È anche possibile usare la `Started` e `Ended` gli eventi di campo di testo per rispondere alla voce di testo iniziale e finale.

<a name="Working-with-Search-Fields" />

## <a name="working-with-search-fields"></a>Utilizzo dei campi di ricerca

Il modo più semplice per lavorare con i campi di ricerca in un'app xamarin. tvos è per aggiungerli alla progettazione dell'interfaccia utente usando la finestra di progettazione di interfaccia.

Seguire questa procedura:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)
    
1. Nel **riquadro della soluzione**, fare doppio clic il `Main.storyboard` file per aprirlo e modificarlo.
1. Trascinare un nuovo Controller di visualizzazione raccolta allo Storyboard per presentare i risultati della ricerca dell'utente: 

    [![](text-fields-and-search-images/search02.png "Un Controller di visualizzazione raccolta")](text-fields-and-search-images/search02.png#lightbox)
1. Nel **Widget** scheda della finestra di **riquadro delle proprietà**, usare `SearchResultsViewController` per il **classe** e `SearchResults` per il **ID Storyboard**: 

    [![](text-fields-and-search-images/search03.png "La scheda di Widget")](text-fields-and-search-images/search03.png#lightbox)
1. Selezionare il **cella prototipo** nell'area di progettazione.
1. Nel **Widget** scheda della finestra di **Esplora proprietà**, usare `SearchResultCell` per il **classe** e `ImageCell` per il **identificatore**: 

    [![](text-fields-and-search-images/search04.png "La scheda di Widget")](text-fields-and-search-images/search04.png#lightbox)
1. Layout di progettazione della **cella prototipo** ed esporre ogni elemento con un valore univoco **nome** nel **Widget** scheda della finestra di **Esplora proprietà**: 

    [![](text-fields-and-search-images/search05.png "Layout di progettazione del prototipo della cella")](text-fields-and-search-images/search05.png#lightbox)
1. Salvare le modifiche dello Storyboard.
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. In **Esplora soluzioni** fare doppio clic sul file `Main.storyboard` per aprirlo e modificarlo.
1. Trascinare un nuovo Controller di visualizzazione raccolta allo Storyboard per presentare i risultati della ricerca dell'utente: 

    [![](text-fields-and-search-images/seach02-vs.png "Un Controller di visualizzazione raccolta")](text-fields-and-search-images/seach02-vs.png#lightbox)
1. Nel **Widget** scheda della finestra di **Esplora proprietà**, usare `SearchResultsViewController` per il **classe** e `SearchResults` per il **ID Storyboard**: 

    [![](text-fields-and-search-images/search03-vs.png "La scheda di Widget")](text-fields-and-search-images/search03-vs.png#lightbox)
1. Selezionare il **cella prototipo** nell'area di progettazione.
1. Nel **Widget** scheda della finestra di **Esplora proprietà**, usare `SearchResultCell` per il **classe** e `ImageCell` per il **identificatore**: 

    [![](text-fields-and-search-images/search04-vs.png "La scheda di Widget")](text-fields-and-search-images/search04-vs.png#lightbox)
1. Layout di progettazione della **cella prototipo** ed esporre ogni elemento con un valore univoco **nome** nel **Widget** scheda della finestra di **Esplora proprietà**: 

    [![](text-fields-and-search-images/search05-vs.png "Layout di progettazione del prototipo della cella")](text-fields-and-search-images/search05-vs.png#lightbox)
1. Salvare le modifiche dello Storyboard.
    
-----

<a name="Provide-a-Data-Model" />

### <a name="provide-a-data-model"></a>Fornire un modello di dati

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Successivamente, è necessario fornire una classe da usare come modello di dati per i risultati che verrà ricercati l'utente. Nel **Esplora soluzioni**, fare clic sul nome del progetto e selezionare **Add** > **nuovo File...**   >  **Generali** > **classe vuota** e fornire un **nome**: 

[![](text-fields-and-search-images/search06.png "Selezionare classe vuota e specificare un nome")](text-fields-and-search-images/search06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Successivamente, è necessario fornire una classe da usare come modello di dati per i risultati che verrà ricercati l'utente. Nel **Esplora soluzioni**, fare clic sul nome del progetto e selezionare **Add** > **nuovo elemento...**   >  **Apple** > **varie** > **classe** e fornire un **nome**: 

[![](text-fields-and-search-images/search06-vs.png "Seleziona classe e specificare un nome")](text-fields-and-search-images/search06-vs.png#lightbox)

-----

Ad esempio, un'app che consente all'utente di eseguire la ricerca di una raccolta di immagini in base al titolo e la parola chiave potrebbe essere simile al seguente:

```csharp
using System;
using Foundation;

namespace tvText
{
    public class PictureInformation : NSObject
    {
        #region Computed Properties
        public string Title { get; set;}
        public string ImageName { get; set;}
        public string Keywords { get; set;}
        #endregion

        #region Constructors
        public PictureInformation (string title, string imageName, string keywords)
        {
            // Initialize
            this.Title = title;
            this.ImageName = imageName;
            this.Keywords = keywords;
        }
        #endregion
    }
}
```

<a name="The-Collection-View-Cell" />

### <a name="the-collection-view-cell"></a>La cella di visualizzazione raccolta

Il modello di dati nella posizione, modificare il **cella prototipo** (`SearchResultViewCell.cs`) e impostarlo come aspetto si trovano le seguenti:

```csharp
using Foundation;
using System;
using UIKit;

namespace tvText
{
    public partial class SearchResultViewCell : UICollectionViewCell
    {
        #region Private Variables
        private PictureInformation _pictureInfo = null;
        #endregion

        #region Computed Properties
        public PictureInformation PictureInfo {
            get { return _pictureInfo; }
            set {
                _pictureInfo = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            UpdateUI ();
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Anything to process?
            if (PictureInfo == null) return;

            try {
                Picture.Image = UIImage.FromBundle (PictureInfo.ImageName);
                Picture.AdjustsImageWhenAncestorFocused = true;
                Title.Text = PictureInfo.Title;
                TextColor = UIColor.LightGray;
            } catch {
                // Ignore errors if view isn't fully loaded
            }
        }
        #endregion
    }

}
```

Il `UpdateUI` metodo verrà utilizzato per visualizzare i singoli campi del **PictureInformation** elementi (la `PictureInfo` proprietà) in elementi dell'interfaccia utente denominati viene aggiornata ogni volta che la proprietà. Ad esempio, l'immagine e titolo associate a un'immagine.

<a name="The-Collection-View-Controller" />

### <a name="the-collection-view-controller"></a>Il Controller di visualizzazione raccolta

Successivamente, modificare il Controller di visualizzazione raccolta di risultati di ricerca (`SearchResultsViewController.cs`) e renderlo simile al seguente:

```csharp
using Foundation;
using System;
using UIKit;
using System.Collections.Generic;

namespace tvText
{
    public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
    {
        #region Constants
        public const string CellID = "ImageCell";
        #endregion

        #region Private Variables
        private string _searchFilter = "";
        #endregion

        #region Computed Properties
        public List<PictureInformation> AllPictures { get; set;}
        public List<PictureInformation> FoundPictures { get; set; }
        public string SearchFilter {
            get { return _searchFilter; }
            set {
                _searchFilter = value.ToLower();
                FindPictures ();
                CollectionView?.ReloadData ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultsViewController (IntPtr handle) : base (handle)
        {
            // Initialize
            this.AllPictures = new List<PictureInformation> ();
            this.FoundPictures = new List<PictureInformation> ();
            PopulatePictures ();
            FindPictures ();

        }
        #endregion

        #region Private Methods
        private void PopulatePictures ()
        {
            // Clear list
            AllPictures.Clear ();

            // Add images
            AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
            AllPictures.Add (new PictureInformation ("Cheese Plate", "CheesePlate", "cheese,plate,bread"));
            AllPictures.Add (new PictureInformation ("Coffee House", "CoffeeHouse", "coffee,people,menu,restaurant,cafe"));
            AllPictures.Add (new PictureInformation ("Computer and Expresso", "ComputerExpresso", "computer,coffee,expresso,phone,notebook"));
            AllPictures.Add (new PictureInformation ("Hamburger", "Hamburger", "meat,bread,cheese,tomato,pickle,lettus"));
            AllPictures.Add (new PictureInformation ("Lasagna Dinner", "Lasagna", "salad,bread,plate,lasagna,pasta"));
            AllPictures.Add (new PictureInformation ("Expresso Meeting", "PeopleExpresso", "people,bag,phone,expresso,coffee,table,tablet,notebook"));
            AllPictures.Add (new PictureInformation ("Soup and Sandwich", "SoupAndSandwich", "soup,sandwich,bread,meat,plate,tomato,lettus,egg"));
            AllPictures.Add (new PictureInformation ("Morning Coffee", "TabletCoffee", "tablet,person,man,coffee,magazine,table"));
            AllPictures.Add (new PictureInformation ("Evening Coffee", "TabletMagCoffee", "tablet,magazine,coffee,table"));
        }

        private void FindPictures ()
        {
            // Clear list
            FoundPictures.Clear ();

            // Scan each picture for a match
            foreach (PictureInformation picture in AllPictures) {
                if (SearchFilter == "") {
                    // If no search term, everything matches
                    FoundPictures.Add (picture);
                } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
                    // If the search term is in the title or keywords, we've found a match
                    FoundPictures.Add (picture);
                }
            }
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            // Only one section in this collection
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            // Return the number of matching pictures
            return FoundPictures.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get a new cell and return it
            var cell = collectionView.DequeueReusableCell (CellID, indexPath);
            return (UICollectionViewCell)cell;
        }

        public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
        {
            // Grab the cell
            var currentCell = cell as SearchResultViewCell;
            if (currentCell == null)
                throw new Exception ("Expected to display a `SearchResultViewCell`.");

            // Display the current picture info in the cell
            var item = FoundPictures [indexPath.Row];
            currentCell.PictureInfo = item;
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // If this Search Controller was presented as a modal view, close
            // it before continuing
            // DismissViewController (true, null);

            // Grab the picture being selected and report it
            var picture = FoundPictures [indexPath.Row];
            Console.WriteLine ("Selected: {0}", picture.Title);
        }

        public void UpdateSearchResultsForSearchController (UISearchController searchController)
        {
            // Save the search filter and update the Collection View
            SearchFilter = searchController.SearchBar.Text ?? string.Empty;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
            if (previousItem != null) {
                UIView.Animate (0.2, () => {
                    previousItem.TextColor = UIColor.LightGray;
                });
            }

            var nextItem = context.NextFocusedView as SearchResultViewCell;
            if (nextItem != null) {
                UIView.Animate (0.2, () => {
                    nextItem.TextColor = UIColor.Black;
                });
            }
        }
        #endregion
    }
}
```

Prima di tutto la `IUISearchResultsUpdating` interfaccia viene aggiunta alla classe per gestire il filtro di ricerca Controller in corso l'aggiornamento dall'utente:

```csharp
public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
```

Una costante è definita anche per specificare l'ID del **cella prototipo** (che corrisponde all'ID definito nella finestra di progettazione dell'interfaccia precedente) che verrà usato in un secondo momento quando il Controller di raccolta richiede una nuova cella:

```csharp
public const string CellID = "ImageCell";
```

Viene creata l'archiviazione per l'elenco completo degli elementi viene eseguita la ricerca, il termine di filtro di ricerca e un elenco di elementi corrispondente a tale termine:

```csharp
private string _searchFilter = "";
...

public List<PictureInformation> AllPictures { get; set;}
public List<PictureInformation> FoundPictures { get; set; }
public string SearchFilter {
    get { return _searchFilter; }
    set {
        _searchFilter = value.ToLower();
        FindPictures ();
        CollectionView?.ReloadData ();
    }
}
```

Quando il `SearchFilter` è stato modificato, viene aggiornato l'elenco di elementi corrispondenti e verrà ricaricato il contenuto della visualizzazione raccolta. Il `FindPictures` routine è responsabile per la ricerca degli elementi che corrispondono al nuovo termine di ricerca:

```csharp
private void FindPictures ()
{
    // Clear list
    FoundPictures.Clear ();

    // Scan each picture for a match
    foreach (PictureInformation picture in AllPictures) {
        if (SearchFilter == "") {
            // If no search term, everything matches
            FoundPictures.Add (picture);
        } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
            // If the search term is in the title or keywords, we've found a match
            FoundPictures.Add (picture);
        }
    }
}
```

Il valore della `SearchFilter` verrà aggiornata (che aggiornerà la visualizzazione di raccolta dei risultati) quando l'utente modifica il filtro nel Controller di ricerca:

```csharp
public void UpdateSearchResultsForSearchController (UISearchController searchController)
{
    // Save the search filter and update the Collection View
    SearchFilter = searchController.SearchBar.Text ?? string.Empty;
}
```

Il `PopulatePictures` metodo popola inizialmente la raccolta di elementi disponibile:

```csharp
private void PopulatePictures ()
{
    // Clear list
    AllPictures.Clear ();

    // Add images
    AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
    ...
}
```

Ai fini di questo esempio, tutti i dati di esempio viene creata in memoria quando viene caricato il Controller di visualizzazione raccolta. In un'app reale, questi dati verranno probabilmente letti da un database o un servizio web e soltanto quando è necessario impedire sovraccaricare il Apple TV memoria limitata.

Il `NumberOfSections` e `GetItemsCount` metodi forniscono il numero di elementi corrispondenti:

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    // Only one section in this collection
    return 1;
}

public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    // Return the number of matching pictures
    return FoundPictures.Count;
}
```

Il `GetCell` restituisce un nuovo metodo **cella prototipo** (in base il `CellID` definiti in precedenza nello Storyboard) per ogni elemento nella visualizzazione raccolta:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Get a new cell and return it
    var cell = collectionView.DequeueReusableCell (CellID, indexPath);
    return (UICollectionViewCell)cell;
}
```

Il `WillDisplayCell` metodo viene chiamato prima della cella viene visualizzata in modo che può essere configurato:

```csharp
public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
{
    // Grab the cell
    var currentCell = cell as SearchResultViewCell;
    if (currentCell == null)
        throw new Exception ("Expected to display a `SearchResultViewCell`.");

    // Display the current picture info in the cell
    var item = FoundPictures [indexPath.Row];
    currentCell.PictureInfo = item;
}
```

Il `DidUpdateFocus` metodo fornisce un feedback visivo all'utente appena vengono evidenziati gli elementi nella visualizzazione di raccolta dei risultati:

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
    if (previousItem != null) {
        UIView.Animate (0.2, () => {
            previousItem.TextColor = UIColor.LightGray;
        });
    }

    var nextItem = context.NextFocusedView as SearchResultViewCell;
    if (nextItem != null) {
        UIView.Animate (0.2, () => {
            nextItem.TextColor = UIColor.Black;
        });
    }
}
```

Infine, il `ItemSelected` metodo gestisce l'utente seleziona un elemento (facendo clic nell'area di tocco con il remoti per Siri) nella visualizzazione di raccolta dei risultati:

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // If this Search Controller was presented as a modal view, close
    // it before continuing
    // DismissViewController (true, null);

    // Grab the picture being selected and report it
    var picture = FoundPictures [indexPath.Row];
    Console.WriteLine ("Selected: {0}", picture.Title);
}
```

Se il campo di ricerca è stato presentato come una visualizzazione della finestra di dialogo modale (nella parte superiore della visualizzazione, una chiamata), usare il `DismissViewController` metodo per chiudere la visualizzazione di ricerca quando l'utente seleziona un elemento. Per questo esempio, il campo di ricerca viene presentato come il contenuto di una scheda di visualizzazione della scheda, in modo che non si sta ignorando qui.

Per altre informazioni sulle viste di raccolta, vedere la [utilizzo delle visualizzazioni raccolta](~/ios/tvos/user-interface/collection-views.md) documentazione.

<a name="Presenting the Search Field" />

### <a name="presenting-the-search-field"></a>Presentare il campo di ricerca

Esistono due modi principali che un campo di ricerca (e l'identificatore associato tastiera su schermo e i risultati della ricerca) può essere presentato all'utente in tvOS: 

- **Visualizza finestra di dialogo modale** -il campo di ricerca possono essere presentato tramite l'oggetto corrente visualizzazione e Controller di visualizzazione come una visualizzazione della finestra di dialogo modale a schermo intero. Questa operazione viene in genere eseguita in risposta all'utente facendo clic su un pulsante o un altro elemento dell'interfaccia utente. La finestra di dialogo viene ignorata quando l'utente seleziona un elemento nei risultati della ricerca.
- **Visualizzare il contenuto** -il campo di ricerca è una parte di una determinata visualizzazione diretta. Ad esempio il contenuto di una scheda di ricerca in un Controller di visualizzazione della scheda.

Per un esempio di un elenco disponibili per la ricerca di immagini indicata in precedenza, il campo di ricerca viene presentato come Visualizza contenuto della scheda di ricerca e il Controller di visualizzazione scheda ricerca simile al seguente:

```csharp
using System;
using UIKit;

namespace tvText
{
    public partial class SecondViewController : UIViewController
    {
        #region Constants
        public const string SearchResultsID = "SearchResults";
        #endregion

        #region Computed Properties
        public SearchResultsViewController ResultsController { get; set;}
        #endregion

        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        public void ShowSearchController ()
        {
            // Build an instance of the Search Results View Controller from the Storyboard
            ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
            if (ResultsController == null)
                throw new Exception ("Unable to instantiate a SearchResultsViewController.");

            // Create an initialize a new search controller
            var searchController = new UISearchController (ResultsController) {
                SearchResultsUpdater = ResultsController,
                HidesNavigationBarDuringPresentation = false
            };

            // Set any required search parameters
            searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

            // The Search Results View Controller can be presented as a modal view
            // PresentViewController (searchController, true, null);

            // Or in the case of this sample, the Search View Controller is being
            // presented as the contents of the Search Tab directly. Use either one
            // or the other method to display the Search Controller (not both).
            var container = new UISearchContainerViewController (searchController);
            var navController = new UINavigationController (container);
            AddChildViewController (navController);
            View.Add (navController.View);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // If the Search Controller is being displayed as the content
            // of the search tab, include it here.
            ShowSearchController ();
        }

        public override void ViewDidAppear (bool animated)
        {
            base.ViewDidAppear (animated);

            // If the Search Controller is being presented as a modal view,
            // call it here to display it over the contents of the Search
            // tab.
            // ShowSearchController ();
        }
        #endregion
    }
}
```

In primo luogo, viene definita una costante che corrisponde alla **Storyboard identificatore** che è stato assegnato al controller di visualizzazione di raccolta dei risultati di ricerca nella finestra di progettazione dell'interfaccia:

```csharp
public const string SearchResultsID = "SearchResults";
```

Successivamente, il `ShowSearchController` metodo crea un nuovo Controller di raccolta vista di ricerca e consente di visualizzare che è necessario:

```csharp
public void ShowSearchController ()
{
    // Build an instance of the Search Results View Controller from the Storyboard
    ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
    if (ResultsController == null)
        throw new Exception ("Unable to instantiate a SearchResultsViewController.");

    // Create an initialize a new search controller
    var searchController = new UISearchController (ResultsController) {
        SearchResultsUpdater = ResultsController,
        HidesNavigationBarDuringPresentation = false
    };

    // Set any required search parameters
    searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

    // The Search Results View Controller can be presented as a modal view
    // PresentViewController (searchController, true, null);

    // Or in the case of this sample, the Search View Controller is being
    // presented as the contents of the Search Tab directly. Use either one
    // or the other method to display the Search Controller (not both).
    var container = new UISearchContainerViewController (searchController);
    var navController = new UINavigationController (container);
    AddChildViewController (navController);
    View.Add (navController.View);
}
```

Nel metodo precedente, una volta una `SearchResultsViewController` è stata creata un'istanza dallo Storyboard, un nuovo `UISearchController` viene creato per presentare il campo di ricerca e tastiera su schermo all'utente. La raccolta di risultati della ricerca (come definito dal `SearchResultsViewController`) verranno visualizzate in questo tipo di tastiera.

Successivamente, il `SearchBar` è configurato con le informazioni, ad esempio il **segnaposto** hint. Fornisce informazioni sul tipo di ricerca viene effettuata all'utente.

Il campo di ricerca viene quindi presentato all'utente in uno dei due modi:

- **Visualizza finestra di dialogo modale** - il `PresentViewController` metodo viene chiamato per presentare la ricerca tramite la vista esistente, a schermo intero.
- **Visualizzare il contenuto** : un `UISearchContainerViewController` creato per contenere il Controller di ricerca. Oggetto `UINavigationController` viene creato per contenere il contenitore di ricerca, il Controller di spostamento viene aggiunto al Controller di visualizzazione `AddChildViewController (navController)`e la visualizzazione presentati `View.Add (navController.View)`.

Infine e nuovamente basati sul tipo di presentazione, ovvero il `ViewDidLoad` o `ViewDidAppear` metodo chiamerà il `ShowSearchController` metodo per la ricerca di presentare all'utente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // If the Search Controller is being displayed as the content
    // of the search tab, include it here.
    ShowSearchController ();
}

public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);

    // If the Search Controller is being presented as a modal view,
    // call it here to display it over the contents of the Search
    // tab.
    // ShowSearchController ();
}
```

Quando l'app viene eseguita e la scheda ricerca selezionata dall'utente, verrà visualizzato l'elenco completo non filtrata di elementi per l'utente:

[![](text-fields-and-search-images/intro02.png "Risultati di ricerca predefinito")](text-fields-and-search-images/intro02.png#lightbox)

Quando l'utente inizia a immettere un termine di ricerca, l'elenco dei risultati verrà filtrato in base a tale termine e aggiornato automaticamente:

[![](text-fields-and-search-images/intro03.png "Risultati della ricerca filtrata")](text-fields-and-search-images/intro03.png#lightbox)

In qualsiasi momento, l'utente può spostare lo stato attivo a un elemento nei risultati della ricerca e fare clic nell'area di tocco dell'elemento remoto Siri per selezionarlo.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la progettazione e utilizzo di testo e campi di ricerca all'interno di un'app xamarin. tvos. È stato illustrato come creare contenuto di testo e l'insieme di ricerca nella finestra di progettazione di interfaccia ed è stato illustrato a due diversi modi che un campo di ricerca possono essere presentato all'utente in tvOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
