---
title: Utilizzo di testo e campi di ricerca
description: Questo articolo descrive la progettazione e l'utilizzo di campi di ricerca e di testo all'interno di un'app Xamarin.tvOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 9EE63CA6-2F31-4EE0-AAE5-82E18CFAC06C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 7d58c30e745e26d1076e75470e527cbe95e85eb6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-text-and-search-fields"></a>Utilizzo di testo e campi di ricerca

_Questo articolo descrive la progettazione e l'utilizzo di campi di ricerca e di testo all'interno di un'app Xamarin.tvOS._



Quando richiesto, l'app Xamarin.tvOS può richiedere piccole parti di testo da parte dell'utente (ad esempio ID utente e password) utilizzando un campo di testo e la tastiera su schermo:

[![](text-fields-and-search-images/intro01.png "Campo di ricerca di esempio")](text-fields-and-search-images/intro01.png#lightbox)

È anche possibile specificare capacità di ricerca di parola chiave del contenuto dell'app usando un campo di ricerca:

[![](text-fields-and-search-images/intro02.png "Risultati della ricerca di esempio")](text-fields-and-search-images/intro02.png#lightbox)

Questo documento illustra i dettagli di utilizzo di testo e campi di ricerca in un'app Xamarin.tvOS.

<a name="About-Text-and-Search-Fields" />

## <a name="about-text-and-search-fields"></a>Informazioni sul testo e campi di ricerca

Come descritto in precedenza, se necessario, il Xamarin.tvOS può presentare uno o più campi di testo per raccogliere piccole quantità di testo dall'utente utilizzando sullo schermo (o la tastiera bluetooth facoltative a seconda della versione di tvOS l'utente ha installato). 

Inoltre, se l'applicazione presenta all'utente (ad esempio un musica, film o una raccolta di immagini) grandi quantità di contenuto, è possibile includere un campo di ricerca che consente all'utente di immettere una piccola quantità di testo per filtrare l'elenco degli elementi disponibili.

<a name="Text-Fields" />

## <a name="text-fields"></a>Campi di testo

In tvOS, un campo di testo viene presentato come una casella di immissione ad altezza fissa, angolo arrotondato che verrà visualizzata una tastiera su schermo quando l'utente fa clic su di essa:

[![](text-fields-and-search-images/text01.png "Testo In campi tvOS")](text-fields-and-search-images/text01.png#lightbox)

Quando l'utente sposta [messa a fuoco](~/ios/tvos/app-fundamentals/navigation-focus.md) per un determinato campo di testo, verrà aumento delle dimensioni maggiore e visualizzare un'ombreggiatura completa. È necessario tenere presente questo presente quando si progetta un'interfaccia utente, come i campi di testo possono sovrapporsi altri elementi dell'interfaccia utente quando viene messa a fuoco.

Apple ha i seguenti suggerimenti per l'utilizzo di campi di testo:

- **Usare testo voce sporadicamente** : a causa della natura della tastiera su schermo, immettendo lungo sezioni di testo o la compilazione di più campi di testo risulta difficile per l'utente. Una soluzione migliore consiste nel limitare la quantità di testo utilizzando gli elenchi di selezione o [pulsanti](~/ios/tvos/user-interface/buttons.md).
- **Utilizzare i parametri a scopo di comunicare** -campo di testo è possibile visualizzare suggerimenti"segnaposto" quando è vuoto. Ove applicabile, utilizzare i parametri per descrivere lo scopo del campo di testo anziché un'etichetta separata.
- **Selezionare il tipo di tastiera predefinito appropriato** -tvOS fornisce diversi tipi di tastiera diversi, scopo compilato che è possibile specificare per il campo di testo. Ad esempio, la tastiera di indirizzo di posta elettronica facilita voce consentendo all'utente di selezionare da un elenco di indirizzi immessi recentemente.
- **Quando appropriato, utilizzare i campi di testo protetto** -campo di testo protetto A presenta ai caratteri immessi come punti (anziché le lettere reale). Se la raccolta di informazioni riservate, ad esempio password, utilizzare sempre un campo di testo protetto.

<a name="Keyboards" />

## <a name="keyboards"></a>Tastiere

Ogni volta che l'utente fa clic su un campo di testo nell'interfaccia utente, lineare sullo schermo viene visualizzata tastiera. L'utente utilizza la superficie toccare il [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) per selezionare le singole lettere sulla tastiera e immettere le informazioni richieste:

[![](text-fields-and-search-images/keyboard01.png "La tastiera Siri remoto")](text-fields-and-search-images/keyboard01.png#lightbox)

Se è presente più di un campo di testo nella visualizzazione corrente, un **Avanti** pulsante verrà automaticamente visualizzato per richiedere all'utente per il campo di testo successivo. Oggetto **eseguita** pulsante verrà visualizzato per l'ultimo campo di testo che terminerà l'immissione di testo e l'utente di tornare alla schermata precedente. 

In qualsiasi momento, l'utente è possibile anche premere il **Menu** pulsante remoto Siri per terminare l'immissione di testo e tornare nuovamente alla schermata precedente.

Apple ha i seguenti suggerimenti per lavorare con le tastiere:

- **Selezionare il tipo di tastiera predefinito appropriato** -tvOS fornisce diversi tipi di tastiera diversi, scopo compilato che è possibile specificare per il campo di testo. Ad esempio, la tastiera di indirizzo di posta elettronica facilita voce consentendo all'utente di selezionare da un elenco di indirizzi immessi recentemente.
- **Quando appropriato, utilizzare le visualizzazioni di accessori tastiera** - oltre lo standard informazioni che sono sempre visualizzata, facoltativo viste accessori (ad esempio immagini o etichette) possono essere aggiunte per la tastiera su schermo per chiarire lo scopo di immissione di testo o a assistere l'utente immette le informazioni necessarie.

Per ulteriori informazioni sull'utilizzo con la tastiera su schermo, vedere Apple [UIKeyboardType](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextInputTraits_Protocol/index.html#//apple_ref/c/tdef/UIKeyboardType), [la gestione della tastiera](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/KeyboardManagement/KeyboardManagement.html#//apple_ref/doc/uid/TP40009542-CH5-SW1), [visualizzazioni personalizzate per l'Input di dati](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/InputViews/InputViews.html#//apple_ref/doc/uid/TP40009542-CH12-SW1) e [ Guida per programmatori testo IOS](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html) documentazione.

<a name="Search" />

## <a name="search"></a>Cerca

Un campo di ricerca viene visualizzata una schermata di specializzato che fornisce un campo di testo e della tastiera su schermo che consente all'utente di filtrare una raccolta di elementi che vengono visualizzati sotto la tastiera:

[![](text-fields-and-search-images/search01.png "Risultati della ricerca di esempio")](text-fields-and-search-images/search01.png#lightbox)

Quando l'utente immette le lettere nel campo di ricerca, i seguenti risultati rifletterà automaticamente i risultati della ricerca. In qualsiasi momento, l'utente può spostare lo stato attivo per i risultati e selezionare uno degli elementi presentati.

Apple ha i seguenti suggerimenti per l'utilizzo di campi di ricerca:

- **Fornire le ricerche recenti** , perché l'immissione di testo con remoto Siri può rivelarsi noiosa e gli utenti tendono a ripetere le richieste di ricerca, è consigliabile aggiungere una sezione dei risultati della ricerca recente prima dei risultati nell'area di tastiera corrente.
- **Se possibile, limitare il numero di risultati** - perché un elenco di elementi di grandi dimensioni può essere difficile per l'utente può analizzare e passare, è consigliabile limitare il numero di risultati restituiti.
- **Se appropriato, il risultato di ricerca di fornire filtri** : se il contenuto fornito dalla tua app si presta, si consiglia di aggiungere barre di ambito per consentire all'utente filtrare ulteriormente i risultati della ricerca restituiti.

Per ulteriori informazioni, vedere Apple [riferimento alla classe UISearchController](https://developer.apple.com/library/tvos/documentation/UIKit/Reference/UISearchController/index.html).

<a name="Working-with-Text-Fields" />

## <a name="working-with-text-fields"></a>Utilizzo dei campi di testo

Il modo più semplice per utilizzare i campi di testo in un'app Xamarin.tvOS è per aggiungerli alla progettazione dell'interfaccia utente utilizzando la finestra di progettazione iOS.

Seguire questa procedura:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Nel **soluzione riempimento**, fare doppio clic su di `Main.storyboard` file per aprirlo e modificarlo.
1. Trascinare uno o più **i campi di testo** int nell'area di progettazione in una visualizzazione: 

    [![](text-fields-and-search-images/text02.png "Un campo di testo")](text-fields-and-search-images/text02.png#lightbox)
1. Selezionare il **i campi di testo** e concedere a ogni univoco **nome** nel **Widget** scheda della finestra il **proprietà riempimento**: 

    [![](text-fields-and-search-images/text03.png "La scheda Widget del riquadro proprietà")](text-fields-and-search-images/text03.png#lightbox)
1. Nel **campo di testo** sezione, è possibile definire elementi, ad esempio il **segnaposto** hint e predefinito **valore**: 

    [![](text-fields-and-search-images/text04.png "Sezione dei campi di testo")](text-fields-and-search-images/text04.png#lightbox)
1. Scorrere verso il basso per definire le proprietà, ad esempio **il controllo ortografico**, **capitalizzazione** e il valore predefinito **tipo tastiera**: 

    [![](text-fields-and-search-images/text05.png "Controllo ortografia, maiuscole/minuscole e il tipo di tastiera predefinito")](text-fields-and-search-images/text05.png#lightbox) 
1. Salvare le modifiche dello Storyboard.
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. In **Esplora soluzioni** fare doppio clic sul file `Main.storyboard` per aprirlo e modificarlo.
1. Trascinare uno o più **i campi di testo** int nell'area di progettazione in una visualizzazione: 

    [![](text-fields-and-search-images/text02-vs.png "Un campo di testo")](text-fields-and-search-images/text02-vs.png#lightbox)
1. Selezionare il **i campi di testo** e concedere a ogni univoco **nome** nel **Widget** scheda della finestra di **Esplora proprietà**: 

    [![](text-fields-and-search-images/text03-vs.png "La scheda Widget")](text-fields-and-search-images/text03-vs.png#lightbox)
1. Nel **campo di testo** sezione, è possibile definire elementi, ad esempio il **segnaposto** hint e predefinito **valore**: 

    [![](text-fields-and-search-images/text04-vs.png "Sezione dei campi di testo")](text-fields-and-search-images/text04-vs.png#lightbox)
1. Scorrere verso il basso per definire le proprietà, ad esempio **il controllo ortografico**, **capitalizzazione** e il valore predefinito **tipo tastiera**: 

    [![](text-fields-and-search-images/text05-vs.png "Controllo ortografia, maiuscole/minuscole e il tipo di tastiera predefinito")](text-fields-and-search-images/text05-vs.png#lightbox) 
1. Salvare le modifiche dello Storyboard.
    
-----

Nel codice, è possibile ottenere o impostare il valore di un campo di testo utilizzando il relativo `Text` proprietà:

```csharp
Console.WriteLine ("User ID {0} and Password {1}", UserId.Text, Password.Text);
```

È possibile utilizzare facoltativamente il `Started` e `Ended` gli eventi di campo di testo per rispondere alla voce di testo iniziale e finale.

<a name="Working-with-Search-Fields" />

## <a name="working-with-search-fields"></a>Utilizzo dei campi di ricerca

Il modo più semplice per lavorare con i campi di ricerca in un'app Xamarin.tvOS è per aggiungerli alla progettazione dell'interfaccia utente utilizzando la finestra di progettazione dell'interfaccia.

Seguire questa procedura:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)
    
1. Nel **soluzione riempimento**, fare doppio clic su di `Main.storyboard` file per aprirlo e modificarlo.
1. Trascinare un nuovo Controller di visualizzazione di raccolta allo Storyboard per presentare i risultati della ricerca dell'utente: 

    [![](text-fields-and-search-images/search02.png "Un Controller di visualizzazione di raccolta")](text-fields-and-search-images/search02.png#lightbox)
1. Nel **Widget** scheda della finestra il **proprietà riempimento**, utilizzare `SearchResultsViewController` per il **classe** e `SearchResults` per il **ID Storyboard**: 

    [![](text-fields-and-search-images/search03.png "La scheda Widget")](text-fields-and-search-images/search03.png#lightbox)
1. Selezionare il **cella prototipo** nell'area di progettazione.
1. Nel **Widget** scheda della finestra di **Esplora proprietà**, utilizzare `SearchResultCell` per il **classe** e `ImageCell` per il **identificatore**: 

    [![](text-fields-and-search-images/search04.png "La scheda Widget")](text-fields-and-search-images/search04.png#lightbox)
1. Layout di progettazione del **cella prototipo** ed esporre ogni elemento con un tipo univoco **nome** nel **Widget** scheda della finestra il **Esplora proprietà**: 

    [![](text-fields-and-search-images/search05.png "Layout di progettazione del prototipo della cella")](text-fields-and-search-images/search05.png#lightbox)
1. Salvare le modifiche dello Storyboard.
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. In **Esplora soluzioni** fare doppio clic sul file `Main.storyboard` per aprirlo e modificarlo.
1. Trascinare un nuovo Controller di visualizzazione di raccolta allo Storyboard per presentare i risultati della ricerca dell'utente: 

    [![](text-fields-and-search-images/seach02-vs.png "Un Controller di visualizzazione di raccolta")](text-fields-and-search-images/seach02-vs.png#lightbox)
1. Nel **Widget** scheda della finestra il **Esplora proprietà**, utilizzare `SearchResultsViewController` per il **classe** e `SearchResults` per il **Storyboard ID**: 

    [![](text-fields-and-search-images/search03-vs.png "La scheda Widget")](text-fields-and-search-images/search03-vs.png#lightbox)
1. Selezionare il **cella prototipo** nell'area di progettazione.
1. Nel **Widget** scheda della finestra di **Esplora proprietà**, utilizzare `SearchResultCell` per il **classe** e `ImageCell` per il **identificatore**: 

    [![](text-fields-and-search-images/search04-vs.png "La scheda Widget")](text-fields-and-search-images/search04-vs.png#lightbox)
1. Layout di progettazione del **cella prototipo** ed esporre ogni elemento con un tipo univoco **nome** nel **Widget** scheda della finestra il **Esplora proprietà**: 

    [![](text-fields-and-search-images/search05-vs.png "Layout di progettazione del prototipo della cella")](text-fields-and-search-images/search05-vs.png#lightbox)
1. Salvare le modifiche dello Storyboard.
    
-----

<a name="Provide-a-Data-Model" />

### <a name="provide-a-data-model"></a>Fornire un modello di dati

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Successivamente, è necessario fornire una classe di agire come modello di dati per i risultati verrà ricercati l'utente. Nel **Esplora**, il nome del progetto e scegliere **Aggiungi** > **nuovo File...**   >  **Generale** > **classe vuota** e fornire un **nome**: 

[![](text-fields-and-search-images/search06.png "Selezionare una classe vuota e specificare un nome")](text-fields-and-search-images/search06.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Successivamente, è necessario fornire una classe di agire come modello di dati per i risultati verrà ricercati l'utente. Nel **Esplora**, il nome del progetto e scegliere **Aggiungi** > **nuovo elemento...**   >  **Apple** > **varie** > **classe** e fornire un **nome**: 

[![](text-fields-and-search-images/search06-vs.png "Selezionare una classe e specificare un nome")](text-fields-and-search-images/search06-vs.png#lightbox)

-----

Ad esempio, un'applicazione che consente all'utente di eseguire la ricerca di una raccolta di immagini per titolo e la parola chiave potrebbe essere simile al seguente:

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

### <a name="the-collection-view-cell"></a>La cella di visualizzazione di raccolta

Con il modello di dati sul posto, modificare il **cella prototipo** (`SearchResultViewCell.cs`) e renderlo aspetto si trovano le operazioni seguenti:

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

### <a name="the-collection-view-controller"></a>Il Controller di visualizzazione di raccolta

Successivamente, modificare il Controller di visualizzazione raccolta risultati ricerca (`SearchResultsViewController.cs`) e renderlo simile al seguente:

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

Prima di tutto, il `IUISearchResultsUpdating` interfaccia viene aggiunto alla classe per gestire il filtro di ricerca Controller aggiornato dall'utente:

```csharp
public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
```

Una costante viene definita anche per specificare l'ID del **prototipo cella** (che corrisponde all'ID definito nella finestra di progettazione dell'interfaccia precedente) che verrà usato in un secondo momento quando il Controller di raccolta richiede una nuova cella:

```csharp
public const string CellID = "ImageCell";
```

Per un elenco completo degli elementi di cui eseguire la ricerca, al termine del filtro di ricerca viene creata l'archiviazione e un elenco di elementi corrispondenti a tale termine:

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

Quando il `SearchFilter` viene modificato, viene aggiornato l'elenco di oggetti corrispondenti e il contenuto della visualizzazione della raccolta viene ricaricato più volte. Il `FindPictures` routine è responsabile dell'individuazione di elementi che corrispondono al nuovo termine di ricerca:

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

Il valore di `SearchFilter` verranno aggiornati (che aggiornerà la visualizzazione di raccolta dei risultati) quando l'utente modifica il filtro nel Controller di ricerca:

```csharp
public void UpdateSearchResultsForSearchController (UISearchController searchController)
{
    // Save the search filter and update the Collection View
    SearchFilter = searchController.SearchBar.Text ?? string.Empty;
}
```

Il `PopulatePictures` metodo inizialmente popola la raccolta di elementi disponibili:

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

Ai fini di questo esempio, tutti i dati di esempio viene creata in memoria quando viene caricato il Controller di visualizzazione di raccolta. In un'applicazione reale, questi dati verranno letti probabilmente da un database o servizio web, e solo in base alle esigenze per impedire di sovraccaricare il TV Apple memoria limitata.

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

Il `DidUpdateFocus` metodo fornisce indicazioni visive per l'utente appena vengono evidenziati gli elementi nella visualizzazione di raccolta dei risultati:

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

Infine, il `ItemSelected` metodo gestisce l'utente seleziona un elemento (facendo clic nell'area di Touch con remoto Siri) nella visualizzazione di raccolta dei risultati:

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

Se il campo di ricerca è stato presentato come una visualizzazione di finestre di dialogo modali (nella parte superiore della visualizzazione chiamata), utilizzare il `DismissViewController` metodo per chiudere la visualizzazione di ricerca quando l'utente seleziona un elemento. Per questo esempio, il campo di ricerca viene visualizzato il contenuto di una scheda, scheda visualizzazione in modo che non si sta ignorando qui.

Per ulteriori informazioni sulle visualizzazioni di raccolta, consultare il nostro [utilizzo delle visualizzazioni di raccolta](~/ios/tvos/user-interface/collection-views.md) documentazione.

<a name="Presenting the Search Field" />

### <a name="presenting-the-search-field"></a>Presentazione di campo di ricerca

Esistono due modi principali che un campo di ricerca (e l'identificatore associato tastiera su schermo e i risultati della ricerca) può essere presentata all'utente in tvOS: 

- **Visualizzazione di finestra di dialogo modale** -il campo di ricerca può essere presentato tramite corrente visualizzazione e Controller di visualizzazione come una visualizzazione della finestra di dialogo modale a schermo intero. Questa operazione viene in genere eseguita in risposta all'utente facendo clic su un pulsante o altri elementi dell'interfaccia utente. La finestra di dialogo viene ignorata quando l'utente seleziona un elemento dai risultati della ricerca.
- **Visualizzare il contenuto** -il campo di ricerca è una parte di una determinata visualizzazione diretta. Ad esempio, il contenuto di una scheda di ricerca in un Controller di visualizzazione della scheda.

Per un esempio di un elenco di ricerca di immagini specificato in precedenza, il campo di ricerca viene presentato come contenuto di visualizzazione nella scheda di ricerca e il Controller di visualizzazione della scheda di ricerca è simile al seguente:

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

In primo luogo, una costante è definita la **identificatore Storyboard** che è stato assegnato al controller di visualizzazione di raccolta dei risultati di ricerca nella finestra di progettazione dell'interfaccia:

```csharp
public const string SearchResultsID = "SearchResults";
```

Successivamente, il `ShowSearchController` metodo crea un nuovo Controller di raccolta visualizzazione di ricerca e Visualizza che è necessario:

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

Nel metodo precedente, una volta un `SearchResultsViewController` istanza è stata creata dallo Storyboard, un nuovo `UISearchController` viene creato per presentare il campo di ricerca e tastiera su schermo all'utente. La raccolta di risultati della ricerca (come definito dal `SearchResultsViewController`) verranno visualizzati sotto la tastiera.

Successivamente, il `SearchBar` è configurato con informazioni quali il **segnaposto** hint. Fornisce informazioni all'utente sul tipo di ricerca viene eseguita.

Il campo di ricerca viene quindi presentato all'utente in uno dei due modi:

- **Visualizzazione di finestra di dialogo modale** - `PresentViewController` metodo viene chiamato per presentare la ricerca tramite la vista esistente, a schermo intero.
- **Visualizzare il contenuto** : `UISearchContainerViewController` viene creato per contenere il Controller di ricerca. Oggetto `UINavigationController` viene creato per contenere il contenitore di ricerca, il Controller di navigazione viene quindi aggiunto al Controller di visualizzazione `AddChildViewController (navController)`e la visualizzazione presentati `View.Add (navController.View)`.

Infine e nuovamente basato sul tipo di presentazione, sia il `ViewDidLoad` o `ViewDidAppear` metodo chiama il metodo di `ShowSearchController` metodo per la ricerca di presentare all'utente:

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

Quando si esegue l'app e la scheda di ricerca selezionate dall'utente, verrà visualizzato l'elenco completo non filtrato di elementi all'utente:

[![](text-fields-and-search-images/intro02.png "Risultati di ricerca predefinito")](text-fields-and-search-images/intro02.png#lightbox)

Come l'utente inizia a immettere un termine di ricerca, l'elenco dei risultati verrà filtrato in base a tale termine e aggiornata automaticamente:

[![](text-fields-and-search-images/intro03.png "Risultati della ricerca filtrati")](text-fields-and-search-images/intro03.png#lightbox)

In qualsiasi momento, l'utente può spostare lo stato attivo a un elemento nei risultati della ricerca e fare clic nell'area tocco di Siri remoto per selezionarlo.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è descritta la progettazione e l'utilizzo di campi di ricerca e di testo all'interno di un'app Xamarin.tvOS. È stato spiegato come creare contenuto di testo e l'insieme di ricerca nella finestra di progettazione dell'interfaccia e hanno dimostrato in due modi diversi che un campo di ricerca può essere presentato all'utente in tvOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
