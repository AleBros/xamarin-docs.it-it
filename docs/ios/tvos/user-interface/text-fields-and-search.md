---
title: Uso del testo e dei campi di ricerca di tvOS in Xamarin
description: Questo documento descrive come usare i campi di testo e di ricerca in un'app tvOS compilata con Xamarin. Viene fornita una panoramica di alto livello dei campi di testo e di ricerca e vengono illustrate le tastiere, l'integrazione con storyboard, i modelli di dati di ricerca e altro ancora.
ms.prod: xamarin
ms.assetid: 9EE63CA6-2F31-4EE0-AAE5-82E18CFAC06C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: cfe747d89b3ccbff390326c915dc1311e40a60fc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022161"
---
# <a name="working-with-tvos-text-and-search-fields-in-xamarin"></a>Uso del testo e dei campi di ricerca di tvOS in Xamarin

Quando necessario, l'app Xamarin.tvOS può richiedere piccole parti di testo dall'utente (ad esempio ID utente e password) usando un campo di testo e la tastiera sullo schermo:

[![](text-fields-and-search-images/intro01.png "Sample Search Field")](text-fields-and-search-images/intro01.png#lightbox)

Facoltativamente, è possibile fornire la funzionalità di ricerca di parole chiave del contenuto dell'app usando un campo di ricerca:

[![](text-fields-and-search-images/intro02.png "Sample Search Results")](text-fields-and-search-images/intro02.png#lightbox)

Questo documento illustra i dettagli dell'uso del testo e dei campi di ricerca in un'app Xamarin.tvOS.

<a name="About-Text-and-Search-Fields" />

## <a name="about-text-and-search-fields"></a>Informazioni sui campi testo e cerca

Come indicato in precedenza, se necessario, Xamarin.tvOS può presentare uno o più campi di testo per raccogliere piccole quantità di testo da parte dell'utente usando uno schermo o una tastiera Bluetooth facoltativa a seconda della versione di tvOS installata dall'utente.

Inoltre, se l'app presenta grandi quantità di contenuto per l'utente (ad esempio musica, filmati o raccolta immagini), potrebbe essere necessario includere un campo di ricerca che consenta all'utente di immettere una piccola quantità di testo per filtrare l'elenco di elementi disponibili.

<a name="Text-Fields" />

## <a name="text-fields"></a>Campi di testo

In tvOS, un campo di testo viene visualizzato come una casella di immissione con angolo arrotondato a altezza fissa che visualizza una tastiera sullo schermo quando l'utente fa clic su di esso:

[![](text-fields-and-search-images/text01.png "Text Fields In tvOS")](text-fields-and-search-images/text01.png#lightbox)

Quando l'utente sposta lo [stato attivo](~/ios/tvos/app-fundamentals/navigation-focus.md) su un campo di testo specificato, aumenterà le dimensioni e visualizzerà un'ombreggiatura profonda. È necessario tenere presente questo aspetto quando si progetta l'interfaccia utente, poiché i campi di testo possono sovrapporsi ad altri elementi dell'interfaccia utente quando sono in stato attivo.

Apple presenta i suggerimenti seguenti per l'utilizzo dei campi di testo:

- **Usare la voce di testo sporadicamente** , a causa della natura della tastiera su schermo, l'immissione di sezioni lunghe di testo o la compilazione di più campi di testo è noiosa per l'utente. Una soluzione migliore consiste nel limitare la quantità di voci di testo usando gli elenchi di selezione o i [pulsanti](~/ios/tvos/user-interface/buttons.md).
- **USA hint per comunicare il campo di testo di scopo** è possibile visualizzare "hint" segnaposto quando è vuoto. Se applicabile, usare hint per descrivere lo scopo del campo di testo anziché un'etichetta separata.
- **Selezionare il tipo di tastiera predefinito appropriato** -tvOS offre diversi tipi di tastiera compilati per lo scopo che è possibile specificare per il campo di testo. Ad esempio, la tastiera dell'indirizzo di posta elettronica può semplificare l'immissione consentendo all'utente di effettuare una selezione da un elenco di indirizzi immessi di recente.
- **Quando appropriato, usare i campi di testo protetti** . un campo di testo protetto presenta i caratteri immessi come punti, anziché le lettere reali. Usare sempre un campo di testo protetto quando si raccolgono informazioni riservate, ad esempio le password.

<a name="Keyboards" />

## <a name="keyboards"></a>Tastiere

Ogni volta che l'utente fa clic su un campo di testo nell'interfaccia utente, viene visualizzata una tastiera lineare sullo schermo. L'utente utilizza la superficie di tocco del [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) per selezionare singole lettere dalla tastiera e immettere le informazioni richieste:

[![](text-fields-and-search-images/keyboard01.png "The Siri Remote keyboard")](text-fields-and-search-images/keyboard01.png#lightbox)

Se è presente più di un campo di testo nella visualizzazione corrente, verrà visualizzato automaticamente un pulsante **Avanti** per portare l'utente al campo di testo successivo. Verrà visualizzato un pulsante **done** per l'ultimo campo di testo che terminerà l'immissione di testo e restituirà l'utente alla schermata precedente.

In qualsiasi momento, l'utente può anche premere il pulsante di **menu** nella voce di testo di Siri Remote to end e tornare alla schermata precedente.

Apple presenta i suggerimenti seguenti per l'utilizzo delle tastiere sullo schermo:

- **Selezionare il tipo di tastiera predefinito appropriato** -tvOS offre diversi tipi di tastiera compilati per lo scopo che è possibile specificare per il campo di testo. Ad esempio, la tastiera dell'indirizzo di posta elettronica può semplificare l'immissione consentendo all'utente di effettuare una selezione da un elenco di indirizzi immessi di recente.
- **Quando appropriato, utilizzare le visualizzazioni accessorie della tastiera** , oltre alle informazioni standard sempre visualizzate, le visualizzazioni accessorie facoltative, ad esempio immagini o etichette, possono essere aggiunte alla tastiera sullo schermo per chiarire lo scopo della voce di testo o di assistere utente che immette le informazioni necessarie.

Per altre informazioni sull'uso della tastiera su schermo, vedere la documentazione di Apple [UIKeyboardType](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextInputTraits_Protocol/index.html#//apple_ref/c/tdef/UIKeyboardType), [gestione della tastiera](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/KeyboardManagement/KeyboardManagement.html#//apple_ref/doc/uid/TP40009542-CH5-SW1), [visualizzazioni personalizzate per la](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/InputViews/InputViews.html#//apple_ref/doc/uid/TP40009542-CH12-SW1) [Guida alla programmazione di input e testo per](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html) i dati nella documentazione di iOS.

<a name="Search" />

## <a name="search"></a>Cerca

Un campo di ricerca presenta una schermata specializzata che fornisce un campo di testo e una tastiera sullo schermo che consentono all'utente di filtrare una raccolta di elementi visualizzati sotto la tastiera:

[![](text-fields-and-search-images/search01.png "Sample search results")](text-fields-and-search-images/search01.png#lightbox)

Quando l'utente immette lettere nel campo di ricerca, i risultati seguenti rifletteranno automaticamente i risultati della ricerca. In qualsiasi momento, l'utente può spostare lo stato attivo sui risultati e selezionare uno degli elementi presentati.

Apple presenta i suggerimenti seguenti per l'utilizzo dei campi di ricerca:

- **Fornire ricerche recenti** : poiché l'immissione di testo con Siri Remote può essere noiosa e gli utenti tendono a ripetere le richieste di ricerca, è consigliabile aggiungere una sezione di risultati di ricerca recenti prima dei risultati correnti sotto l'area della tastiera.
- **Quando possibile, limitare il numero di risultati** . Poiché un elenco di elementi di grandi dimensioni può essere difficile per l'analisi e la navigazione da parte dell'utente, è consigliabile limitare il numero di risultati restituiti.
- **Se appropriato, fornire i filtri dei risultati di ricerca** : se il contenuto fornito dall'app si presta a se stesso, è consigliabile aggiungere le barre dell'ambito per consentire all'utente di filtrare ulteriormente i risultati della ricerca restituiti.

Per ulteriori informazioni, vedere la pagina relativa al [riferimento alla classe UISearchController](https://developer.apple.com/library/tvos/documentation/UIKit/Reference/UISearchController/index.html)di Apple.

<a name="Working-with-Text-Fields" />

## <a name="working-with-text-fields"></a>Uso dei campi di testo

Il modo più semplice per lavorare con i campi di testo in un'app Xamarin.tvOS consiste nell'aggiungerli alla progettazione dell'interfaccia utente usando iOS designer.

Procedere come descritto di seguito:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nella **riquadro della soluzione**fare doppio clic sul file `Main.storyboard` per aprirlo per la modifica.
1. Trascinare uno o più **campi di testo** int l'area di progettazione in una visualizzazione:

    [![](text-fields-and-search-images/text02.png "A Text Field")](text-fields-and-search-images/text02.png#lightbox)
1. Selezionare i **campi di testo** e assegnare a ognuno **un nome** univoco nella scheda **widget** della **riquadro delle proprietà**:

    [![](text-fields-and-search-images/text03.png "The Widget tab of the Properties Pad")](text-fields-and-search-images/text03.png#lightbox)
1. Nella sezione **campo di testo** è possibile definire elementi quali l'hint **segnaposto** e il **valore**predefinito:

    [![](text-fields-and-search-images/text04.png "The Text Field section")](text-fields-and-search-images/text04.png#lightbox)
1. Scorrere verso il basso per definire proprietà quali il **controllo ortografico**, le **maiuscole** e il **tipo di tastiera**predefinito:

    [![](text-fields-and-search-images/text05.png "Spell Checking, Capitalization and the default Keyboard Type")](text-fields-and-search-images/text05.png#lightbox)
1. Salvare le modifiche apportate allo storyboard.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. In **Esplora soluzioni** fare doppio clic sul file `Main.storyboard` per aprirlo e modificarlo.
1. Trascinare uno o più **campi di testo** int l'area di progettazione in una visualizzazione:

    [![](text-fields-and-search-images/text02-vs.png "A Text Field")](text-fields-and-search-images/text02-vs.png#lightbox)
1. Selezionare i **campi di testo** e assegnare a ognuno **un nome** univoco nella scheda **widget** di **Esplora proprietà**:

    [![](text-fields-and-search-images/text03-vs.png "The Widget tab")](text-fields-and-search-images/text03-vs.png#lightbox)
1. Nella sezione **campo di testo** è possibile definire elementi quali l'hint **segnaposto** e il **valore**predefinito:

    [![](text-fields-and-search-images/text04-vs.png "The Text Field section")](text-fields-and-search-images/text04-vs.png#lightbox)
1. Scorrere verso il basso per definire proprietà quali il **controllo ortografico**, le **maiuscole** e il **tipo di tastiera**predefinito:

    [![](text-fields-and-search-images/text05-vs.png "Spell Checking, Capitalization and the default Keyboard Type")](text-fields-and-search-images/text05-vs.png#lightbox)
1. Salvare le modifiche apportate allo storyboard.

-----

Nel codice è possibile ottenere o impostare il valore di un campo di testo usando la relativa proprietà `Text`:

```csharp
Console.WriteLine ("User ID {0} and Password {1}", UserId.Text, Password.Text);
```

Facoltativamente, è possibile usare gli eventi `Started` e `Ended` campo di testo per rispondere alla voce di testo iniziale e finale.

<a name="Working-with-Search-Fields" />

## <a name="working-with-search-fields"></a>Uso dei campi di ricerca

Il modo più semplice per usare i campi di ricerca in un'app Xamarin.tvOS consiste nell'aggiungerli alla progettazione dell'interfaccia utente usando progettazione interfaccia.

Procedere come descritto di seguito:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nella **riquadro della soluzione**fare doppio clic sul file `Main.storyboard` per aprirlo per la modifica.
1. Trascinare un nuovo controller di visualizzazione raccolta nello storyboard per presentare i risultati della ricerca dell'utente:

    [![](text-fields-and-search-images/search02.png "A Collection View Controller")](text-fields-and-search-images/search02.png#lightbox)
1. Nella scheda **widget** della **riquadro delle proprietà**usare `SearchResultsViewController` per la **classe** e `SearchResults` per l' **ID storyboard**:

    [![](text-fields-and-search-images/search03.png "The Widget tab")](text-fields-and-search-images/search03.png#lightbox)
1. Selezionare il **prototipo di cella** nell'area di progettazione.
1. Nella scheda **widget** di **esplora proprietà**usare `SearchResultCell` per la **classe** e `ImageCell` per l' **identificatore**:

    [![](text-fields-and-search-images/search04.png "The Widget tab")](text-fields-and-search-images/search04.png#lightbox)
1. Layout della progettazione del **prototipo di cella** ed esporre ogni elemento con un **nome** univoco nella scheda **widget** di **Esplora proprietà**:

    [![](text-fields-and-search-images/search05.png "Layout the design of the Cell Prototype")](text-fields-and-search-images/search05.png#lightbox)
1. Salvare le modifiche apportate allo storyboard.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. In **Esplora soluzioni** fare doppio clic sul file `Main.storyboard` per aprirlo e modificarlo.
1. Trascinare un nuovo controller di visualizzazione raccolta nello storyboard per presentare i risultati della ricerca dell'utente:

    [![](text-fields-and-search-images/seach02-vs.png "A Collection View Controller")](text-fields-and-search-images/seach02-vs.png#lightbox)
1. Nella scheda **widget** di **esplora proprietà**usare `SearchResultsViewController` per la **classe** e `SearchResults` per l' **ID storyboard**:

    [![](text-fields-and-search-images/search03-vs.png "The Widget tab")](text-fields-and-search-images/search03-vs.png#lightbox)
1. Selezionare il **prototipo di cella** nell'area di progettazione.
1. Nella scheda **widget** di **esplora proprietà**usare `SearchResultCell` per la **classe** e `ImageCell` per l' **identificatore**:

    [![](text-fields-and-search-images/search04-vs.png "The Widget tab")](text-fields-and-search-images/search04-vs.png#lightbox)
1. Layout della progettazione del **prototipo di cella** ed esporre ogni elemento con un **nome** univoco nella scheda **widget** di **Esplora proprietà**:

    [![](text-fields-and-search-images/search05-vs.png "Layout the design of the Cell Prototype")](text-fields-and-search-images/search05-vs.png#lightbox)
1. Salvare le modifiche apportate allo storyboard.

-----

<a name="Provide-a-Data-Model" />

### <a name="provide-a-data-model"></a>Fornire un modello di dati

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Successivamente, sarà necessario fornire una classe che funga da modello di dati per i risultati cercati dall'utente. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Aggiungi** > **nuovo file...** > **generale** > **classe vuota** e specificare un **nome**:

[![](text-fields-and-search-images/search06.png "Select Empty Class and provide a Name")](text-fields-and-search-images/search06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Successivamente, sarà necessario fornire una classe che funga da modello di dati per i risultati cercati dall'utente. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Aggiungi** > **nuovo elemento...** > **Apple** > **varie** > **classe** e specificare un **nome**:

[![](text-fields-and-search-images/search06-vs.png "Select Class and provide a Name")](text-fields-and-search-images/search06-vs.png#lightbox)

-----

Ad esempio, un'app che consente all'utente di eseguire la ricerca in una raccolta di immagini in base al titolo e alla parola chiave può avere un aspetto simile al seguente:

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

### <a name="the-collection-view-cell"></a>Cella di visualizzazione raccolta

Con il modello di dati sul posto, modificare la **cella del prototipo** (`SearchResultViewCell.cs`) e fare in modo che l'aspetto sia il seguente:

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

Il metodo `UpdateUI` verrà usato per visualizzare i singoli campi degli elementi **PictureInformation** (la proprietà `PictureInfo`) negli elementi dell'interfaccia utente denominati ogni volta che la proprietà viene aggiornata. Ad esempio, l'immagine e il titolo associati all'immagine.

<a name="The-Collection-View-Controller" />

### <a name="the-collection-view-controller"></a>Controller di visualizzazione raccolta

Modificare quindi il controller di visualizzazione della raccolta dei risultati di ricerca (`SearchResultsViewController.cs`) e renderlo simile al seguente:

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

In primo luogo, l'interfaccia `IUISearchResultsUpdating` viene aggiunta alla classe per gestire il filtro del controller di ricerca aggiornato dall'utente:

```csharp
public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
```

Viene anche definita una costante per specificare l'ID della **cella del prototipo** , che corrisponde all'ID definito nella finestra di progettazione dell'interfaccia precedente, che verrà usata in un secondo momento quando il controller della raccolta richiede una nuova cella:

```csharp
public const string CellID = "ImageCell";
```

Viene creata l'archiviazione per l'elenco completo degli elementi cercati, il termine del filtro di ricerca e l'elenco degli elementi corrispondenti a tale termine:

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

Quando la `SearchFilter` viene modificata, l'elenco degli elementi corrispondenti viene aggiornato e il contenuto della visualizzazione della raccolta viene ricaricato. La routine `FindPictures` è responsabile della ricerca di elementi che corrispondono al nuovo termine di ricerca:

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

Il valore della `SearchFilter` verrà aggiornato (che aggiornerà la visualizzazione raccolta risultati) quando l'utente modifica il filtro nel controller di ricerca:

```csharp
public void UpdateSearchResultsForSearchController (UISearchController searchController)
{
    // Save the search filter and update the Collection View
    SearchFilter = searchController.SearchBar.Text ?? string.Empty;
}
```

Il metodo `PopulatePictures` popola inizialmente la raccolta di elementi disponibili:

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

Ai fini di questo esempio, tutti i dati di esempio vengono creati in memoria durante il caricamento del controller di visualizzazione raccolta. In un'app reale questi dati verrebbero probabilmente letti da un database o da un servizio Web e solo se necessario per evitare di eseguire l'overrunning della memoria limitata di Apple TV.

I metodi `NumberOfSections` e `GetItemsCount` forniscono il numero di elementi corrispondenti:

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

Il metodo `GetCell` restituisce una nuova **cella del prototipo** (basata sul `CellID` definito in precedenza nello storyboard) per ogni elemento nella visualizzazione della raccolta:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Get a new cell and return it
    var cell = collectionView.DequeueReusableCell (CellID, indexPath);
    return (UICollectionViewCell)cell;
}
```

Il metodo `WillDisplayCell` viene chiamato prima della visualizzazione della cella in modo che possa essere configurata:

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

Il metodo `DidUpdateFocus` fornisce feedback visivo all'utente durante l'evidenziazione degli elementi nella visualizzazione raccolta risultati:

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

Infine, il metodo di `ItemSelected` gestisce l'utente selezionando un elemento (facendo clic sulla superficie del tocco con Siri Remote) nella visualizzazione raccolta risultati:

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

Se il campo di ricerca è stato presentato come visualizzazione di finestra di dialogo modale (sulla parte superiore della visualizzazione che lo chiama), usare il metodo `DismissViewController` per chiudere la visualizzazione di ricerca quando l'utente seleziona un elemento. Per questo esempio, il campo di ricerca viene presentato come contenuto di una scheda di visualizzazione a schede, quindi non viene rilasciata qui.

Per ulteriori informazioni sulle visualizzazioni di raccolta, vedere la documentazione [utilizzo delle visualizzazioni di raccolta](~/ios/tvos/user-interface/collection-views.md) .

<a name="Presenting the Search Field" />

### <a name="presenting-the-search-field"></a>Presentazione del campo di ricerca

Esistono due modi principali per presentare all'utente in tvOS un campo di ricerca (e i risultati di ricerca e tastiera sullo schermo associati):

- **Visualizzazione finestra di dialogo modale** : il campo di ricerca può essere visualizzato nella visualizzazione corrente e nel controller di visualizzazione come visualizzazione della finestra di dialogo modale a schermo intero. Questa operazione viene in genere eseguita in risposta all'utente che fa clic su un pulsante o su un altro elemento dell'interfaccia utente. La finestra di dialogo viene rilasciata quando l'utente seleziona un elemento dai risultati della ricerca.
- **Visualizza contenuto** : il campo di ricerca è una parte diretta di una determinata visualizzazione. Ad esempio, come contenuto di una scheda di ricerca in un controller di visualizzazione a schede.

Per l'esempio di un elenco di immagini in cui è possibile eseguire ricerche, il campo di ricerca viene presentato come Visualizza contenuto nella scheda Cerca e il controller di visualizzazione scheda di ricerca è simile al seguente:

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

In primo luogo, viene definita una costante che corrisponde all' **identificatore dello storyboard** assegnato al controller di visualizzazione della raccolta dei risultati di ricerca nella finestra di progettazione dell'interfaccia:

```csharp
public const string SearchResultsID = "SearchResults";
```

Il metodo `ShowSearchController` crea quindi un nuovo controller di raccolta della visualizzazione di ricerca e ne Visualizza il necessario:

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

Nel metodo precedente, dopo che è stata creata un'istanza di `SearchResultsViewController` dallo storyboard, viene creata una nuova `UISearchController` per presentare all'utente il campo di ricerca e la tastiera sullo schermo. La raccolta dei risultati di ricerca (come definito dall'`SearchResultsViewController`) verrà visualizzata sotto questa tastiera.

Successivamente, la `SearchBar` viene configurata con le informazioni, ad esempio l'hint **segnaposto** . In questo modo vengono fornite all'utente informazioni sul tipo di ricerca in corso di esecuzione.

Il campo di ricerca viene quindi presentato all'utente in uno dei due modi seguenti:

- **Visualizzazione finestra di dialogo modale** : viene chiamato il metodo `PresentViewController` per presentare la ricerca nella visualizzazione esistente a schermo intero.
- **Visualizza contenuto** : viene creato un `UISearchContainerViewController` per contenere il controller di ricerca. Viene creato un `UINavigationController` per contenere il contenitore di ricerca, quindi il controller di spostamento viene aggiunto al controller di visualizzazione `AddChildViewController (navController)`e la visualizzazione presentata `View.Add (navController.View)`.

Infine, e di nuovo in base al tipo di presentazione, il metodo `ViewDidLoad` o `ViewDidAppear` chiamerà il metodo `ShowSearchController` per presentare la ricerca all'utente:

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

Quando l'app viene eseguita e la scheda Cerca selezionata dall'utente, l'elenco completo non filtrato di elementi verrà visualizzato all'utente:

[![](text-fields-and-search-images/intro02.png "Default search results")](text-fields-and-search-images/intro02.png#lightbox)

Quando l'utente inizia a immettere un termine di ricerca, l'elenco di risultati verrà filtrato in base a tale termine e aggiornato automaticamente:

[![](text-fields-and-search-images/intro03.png "Filtered search results")](text-fields-and-search-images/intro03.png#lightbox)

In qualsiasi momento, l'utente può spostare lo stato attivo su un elemento nei risultati della ricerca e fare clic sull'area di tocco del Siri remoto per selezionarlo.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha spiegato come progettare e usare i campi di testo e di ricerca all'interno di un'app Xamarin.tvOS. È stato illustrato come creare il testo e il contenuto della raccolta di ricerca in Interface Designer, mostrando due modi diversi per presentare un campo di ricerca all'utente in tvOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
