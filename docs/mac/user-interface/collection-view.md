---
title: Viste di raccolta in Xamarin.Mac
description: Questo articolo descrive l'uso delle visualizzazioni di raccolta in un'app Xamarin.Mac. Viene illustrata la creazione e la gestione delle visualizzazioni di raccolta in Xcode e Interface Builder e l'utilizzo di tali viste a livello di codice.
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 05/24/2017
ms.openlocfilehash: 565441762bc7d9dcf7f73b42a34e3feb0bff86f1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025828"
---
# <a name="collection-views-in-xamarinmac"></a>Viste di raccolta in Xamarin.Mac

_Questo articolo descrive l'uso delle visualizzazioni di raccolta in un'app Xamarin.Mac. Viene illustrata la creazione e la gestione delle visualizzazioni di raccolta in Xcode e Interface Builder e l'utilizzo di tali viste a livello di codice._

Quando si lavora C# con e .NET in un'app Xamarin.Mac, lo sviluppatore ha accesso agli stessi controlli di visualizzazione raccolta AppKit che uno sviluppatore lavora in *Objective-C* e *Xcode* . Poiché Xamarin.Mac si integra direttamente con Xcode, lo sviluppatore usa _Interface Builder_ di Xcode per creare e gestire le visualizzazioni di raccolta.

In un `NSCollectionView` viene visualizzata una griglia di sottoviste organizzate con un `NSCollectionViewLayout`. Ogni sottovista della griglia viene rappresentata da un `NSCollectionViewItem` che gestisce il caricamento del contenuto della visualizzazione da un file di `.xib`.

[![un'esecuzione di app di esempio](collection-view-images/intro01.png)](collection-view-images/intro01.png#lightbox)

Questo articolo illustra le nozioni di base sull'uso delle visualizzazioni di raccolta in un'app Xamarin.Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti e le tecniche principali usati in questo articolo.

Si consiglia di esaminare la sezione [esporre C# classi/metodi in Objective-c](~/mac/internals/how-it-works.md) del documento [interno di Xamarin.Mac](~/mac/internals/how-it-works.md) , spiegando i comandi`Register`e`Export`usati per collegare le C# classi a Objective-c. oggetti ed elementi dell'interfaccia utente.

<a name="About_Collection_Views"/>

## <a name="about-collection-views"></a>Informazioni sulle visualizzazioni di raccolta

L'obiettivo principale di una visualizzazione di raccolta (`NSCollectionView`) consiste nel disporre visivamente di un gruppo di oggetti in modo organizzato utilizzando un layout di visualizzazione raccolta (`NSCollectionViewLayout`), con ogni singolo oggetto (`NSCollectionViewItem`) che recupera una propria visualizzazione nella raccolta più grande. Le visualizzazioni di raccolta funzionano tramite il data binding e le tecniche di codifica chiave-valore e, di conseguenza, è consigliabile leggere il [Data Binding e](~/mac/app-fundamentals/databinding.md) la documentazione di codifica del valore chiave prima di continuare con questo articolo.

La visualizzazione raccolta non contiene elementi standard e predefiniti della visualizzazione raccolta (ad esempio, un contorno o una vista tabella), quindi lo sviluppatore è responsabile della progettazione e dell'implementazione di una _visualizzazione prototipo_ usando altri controlli AppKit, ad esempio campi immagine, campi di testo, etichette, Via. Questa vista prototipo verrà utilizzata per visualizzare e utilizzare ogni elemento gestito dalla visualizzazione raccolta e viene archiviato in un file di `.xib`.

Poiché lo sviluppatore è responsabile dell'aspetto di un elemento della visualizzazione raccolta, la visualizzazione raccolta non dispone di alcun supporto incorporato per evidenziare un elemento selezionato nella griglia. L'implementazione di questa funzionalità verrà illustrata in questo articolo.

<a name="Defining_your_Data_Model"/>

## <a name="defining-the-data-model"></a>Definizione del modello di dati

Prima di associare i dati a una visualizzazione di raccolta in Interface Builder, è necessario definire una classe conforme a KVC (Key-Value Coding)/Key-Value Osservating (KVO) nell'app Xamarin.Mac in modo che funga da _modello di dati_ per l'associazione. Il modello di dati fornisce tutti i dati che verranno visualizzati nella raccolta e riceverà tutte le modifiche ai dati che l'utente effettua nell'interfaccia utente durante l'esecuzione dell'applicazione.

Prendere l'esempio di un'app che gestisce un gruppo di dipendenti, è possibile usare la classe seguente per definire il modello di dati:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon
        {
            get
            {
                if (isManager)
                {
                    return NSImage.ImageNamed("IconGroup");
                }
                else
                {
                    return NSImage.ImageNamed("IconUser");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

Il modello di dati `PersonModel` verrà usato nel resto di questo articolo.

<a name="Working_with_a_Collection_View"/>

## <a name="working-with-a-collection-view"></a>Utilizzo di una visualizzazione di raccolta

Il data binding con una visualizzazione di raccolta è molto simile al binding con una visualizzazione di tabella, in quanto `NSCollectionViewDataSource` viene usato per fornire i dati per la raccolta. Poiché la visualizzazione raccolta non ha un formato di visualizzazione preimpostato, è necessario più lavoro per fornire commenti e suggerimenti sull'interazione dell'utente e per tenere traccia della selezione dell'utente.

<a name="Creating-the-Cell-Prototype"/>

### <a name="creating-the-cell-prototype"></a>Creazione del prototipo di cella

Poiché la visualizzazione raccolta non include un prototipo di cella predefinito, lo sviluppatore dovrà aggiungere uno o più file di `.xib` all'app Xamarin.Mac per definire il layout e il contenuto delle singole celle.

Procedere come descritto di seguito:

1. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Aggiungi** > **nuovo file...**
2. Selezionare **Mac** > **View Controller**, assegnargli un nome, ad esempio `EmployeeItem` in questo esempio, e fare clic sul pulsante **nuovo** per creare: 

    ![Aggiunta di un nuovo controller di visualizzazione](collection-view-images/proto01.png)

    Verrà aggiunto un file `EmployeeItem.cs`, `EmployeeItemController.cs` e `EmployeeItemController.xib` alla soluzione del progetto.
3. Fare doppio clic sul file `EmployeeItemController.xib` per aprirlo per la modifica nella Interface Builder di Xcode.
4. Aggiungere una `NSBox`, `NSImageView` e due controlli `NSLabel` alla visualizzazione e disporli come indicato di seguito:

    ![Progettazione del layout del prototipo di cella](collection-view-images/proto02.png)
5. Aprire l' **editor di assistente** e creare un **Outlet** per la `NSBox` in modo che possa essere usato per indicare lo stato di selezione di una cella:

    ![Esposizione di NSBox in un Outlet](collection-view-images/proto03.png)
6. Tornare all' **editor standard** e selezionare la visualizzazione immagine.
7. Nel **controllo Binding**selezionare **associa a** > proprietario del **file** e immettere il percorso della **chiave del modello** `self.Person.Icon`:

    ![Associazione dell'icona](collection-view-images/proto04.png)
8. Selezionare la prima etichetta e nel **controllo Binding**selezionare **associa a** > proprietario del **file** e immettere il percorso della **chiave del modello** `self.Person.Name`:

    ![Associazione del nome](collection-view-images/proto05.png)
9. Selezionare la seconda etichetta e nel **controllo Binding**selezionare **associa a** > proprietario del **file** e immettere il percorso della **chiave del modello** `self.Person.Occupation`:

    ![Associazione dell'occupazione](collection-view-images/proto06.png)
10. Salvare le modifiche apportate al file `.xib` e tornare a Visual Studio per sincronizzare le modifiche.

Modificare il file di `EmployeeItemController.cs` e renderlo simile al seguente:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// The Employee item controller handles the display of the individual items that will
    /// be displayed in the collection view as defined in the associated .XIB file.
    /// </summary>
    public partial class EmployeeItemController : NSCollectionViewItem
    {
        #region Private Variables
        /// <summary>
        /// The person that will be displayed.
        /// </summary>
        private PersonModel _person;
        #endregion

        #region Computed Properties
        // strongly typed view accessor
        public new EmployeeItem View
        {
            get
            {
                return (EmployeeItem)base.View;
            }
        }

        /// <summary>
        /// Gets or sets the person.
        /// </summary>
        /// <value>The person that this item belongs to.</value>
        [Export("Person")]
        public PersonModel Person
        {
            get { return _person; }
            set
            {
                WillChangeValue("Person");
                _person = value;
                DidChangeValue("Person");
            }
        }

        /// <summary>
        /// Gets or sets the color of the background for the item.
        /// </summary>
        /// <value>The color of the background.</value>
        public NSColor BackgroundColor {
            get { return Background.FillColor; }
            set { Background.FillColor = value; }
        }

        /// <summary>
        /// Gets or sets a value indicating whether this <see cref="T:MacCollectionNew.EmployeeItemController"/> is selected.
        /// </summary>
        /// <value><c>true</c> if selected; otherwise, <c>false</c>.</value>
        /// <remarks>This also changes the background color based on the selected state
        /// of the item.</remarks>
        public override bool Selected
        {
            get
            {
                return base.Selected;
            }
            set
            {
                base.Selected = value;

                // Set background color based on the selection state
                if (value) {
                    BackgroundColor = NSColor.DarkGray;
                } else {
                    BackgroundColor = NSColor.LightGray;
                }
            }
        }
        #endregion

        #region Constructors
        // Called when created from unmanaged code
        public EmployeeItemController(IntPtr handle) : base(handle)
        {
            Initialize();
        }

        // Called when created directly from a XIB file
        [Export("initWithCoder:")]
        public EmployeeItemController(NSCoder coder) : base(coder)
        {
            Initialize();
        }

        // Call to load from the XIB/NIB file
        public EmployeeItemController() : base("EmployeeItem", NSBundle.MainBundle)
        {
            Initialize();
        }

        // Added to support loading from XIB/NIB
        public EmployeeItemController(string nibName, NSBundle nibBundle) : base(nibName, nibBundle) {

            Initialize();
        }

        // Shared initialization code
        void Initialize()
        {
        }
        #endregion
    }
}
```

Esaminando il codice in dettaglio, la classe eredita da `NSCollectionViewItem` quindi può fungere da prototipo per una cella di visualizzazione raccolta. La proprietà `Person` espone la classe utilizzata per l'associazione dati alla visualizzazione immagine e alle etichette in Xcode. Si tratta di un'istanza del `PersonModel` creato in precedenza.

La proprietà `BackgroundColor` è un collegamento al `FillColor` del controllo `NSBox` che verrà utilizzato per visualizzare lo stato di selezione di una cella. Eseguendo l'override della proprietà `Selected` della `NSCollectionViewItem`, il codice seguente imposta o cancella questo stato di selezione:

```csharp
public override bool Selected
{
    get
    {
        return base.Selected;
    }
    set
    {
        base.Selected = value;

        // Set background color based on the selection state
        if (value) {
            BackgroundColor = NSColor.DarkGray;
        } else {
            BackgroundColor = NSColor.LightGray;
        }
    }
}
```

<a name="Creating-the-Collection-View-Data-Source"/>

### <a name="creating-the-collection-view-data-source"></a>Creazione dell'origine dati della visualizzazione raccolta

Un'origine dati di visualizzazione raccolta (`NSCollectionViewDataSource`) fornisce tutti i dati per una visualizzazione raccolta e crea e popola una cella di visualizzazione raccolta (usando il prototipo di `.xib`) come richiesto per ogni elemento nella raccolta.

Aggiungere una nuova classe al progetto, chiamarla `CollectionViewDataSource` e renderla simile alla seguente:

```csharp
using System;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view data source provides the data for the collection view.
    /// </summary>
    public class CollectionViewDataSource : NSCollectionViewDataSource
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent collection view.
        /// </summary>
        /// <value>The parent collection view.</value>
        public NSCollectionView ParentCollectionView { get; set; }

        /// <summary>
        /// Gets or sets the data that will be displayed in the collection.
        /// </summary>
        /// <value>A collection of PersonModel objects.</value>
        public List<PersonModel> Data { get; set; } = new List<PersonModel>();
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDataSource"/> class.
        /// </summary>
        /// <param name="parent">The parent collection that this datasource will provide data for.</param>
        public CollectionViewDataSource(NSCollectionView parent)
        {
            // Initialize
            ParentCollectionView = parent;

            // Attach to collection view
            parent.DataSource = this;

        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Gets the number of sections.
        /// </summary>
        /// <returns>The number of sections.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        public override nint GetNumberOfSections(NSCollectionView collectionView)
        {
            // There is only one section in this view
            return 1;
        }

        /// <summary>
        /// Gets the number of items in the given section.
        /// </summary>
        /// <returns>The number of items.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="section">The Section number to count items for.</param>
        public override nint GetNumberofItems(NSCollectionView collectionView, nint section)
        {
            // Return the number of items
            return Data.Count;
        }

        /// <summary>
        /// Gets the item for the give section and item index.
        /// </summary>
        /// <returns>The item.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPath">Index path specifying the section and index.</param>
        public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
        {
            var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
            item.Person = Data[(int)indexPath.Item];

            return item;
        }
        #endregion
    }
}
```

Esaminando il codice in dettaglio, la classe eredita da `NSCollectionViewDataSource` ed espone un elenco di istanze di `PersonModel` tramite la relativa proprietà `Data`.

Poiché questa raccolta include solo una sezione, il codice esegue l'override del metodo `GetNumberOfSections` e restituisce sempre `1`. Inoltre, viene eseguito l'override del metodo `GetNumberofItems` in quanto restituisce il numero di elementi nell'elenco di proprietà `Data`.

Il metodo `GetItem` viene chiamato ogni volta che è richiesta una nuova cella e presenta un aspetto simile al seguente:

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

Il metodo `MakeItem` della visualizzazione raccolta viene chiamato per creare o restituire un'istanza riutilizzabile del `EmployeeItemController` e la relativa proprietà `Person` è impostata su elemento visualizzato nella cella richiesta. 

Il `EmployeeItemController` deve essere registrato con il controller di visualizzazione raccolta in anticipo usando il codice seguente:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

L' **identificatore** (`EmployeeCell`) utilizzato nella chiamata `MakeItem` _deve_ corrispondere al nome del controller di visualizzazione registrato con la visualizzazione della raccolta. Questo passaggio verrà analizzato in dettaglio di seguito.

<a name="Handling-Item-Selection"/>

### <a name="handling-item-selection"></a>Gestione selezione elementi

Per gestire la selezione e la deselezione degli elementi nella raccolta, sarà necessario un `NSCollectionViewDelegate`. Poiché in questo esempio verrà utilizzato il tipo di layout incorporato `NSCollectionViewFlowLayout`, sarà necessaria una versione specifica `NSCollectionViewDelegateFlowLayout` di questo delegato.

Aggiungere una nuova classe al progetto, chiamarla `CollectionViewDelegate` e renderla simile alla seguente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view delegate handles user interaction with the elements of the 
    /// collection view for the Flow-Based layout type.
    /// </summary>
    public class CollectionViewDelegate : NSCollectionViewDelegateFlowLayout
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent view controller.
        /// </summary>
        /// <value>The parent view controller.</value>
        public ViewController ParentViewController { get; set; }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDelegate"/> class.
        /// </summary>
        /// <param name="parentViewController">Parent view controller.</param>
        public CollectionViewDelegate(ViewController parentViewController)
        {
            // Initialize
            ParentViewController = parentViewController;
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Handles one or more items being selected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being selected.</param>
        public override void ItemsSelected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = (int)paths[0].Item;

            // Save the selected item
            ParentViewController.PersonSelected = ParentViewController.Datasource.Data[index];

        }

        /// <summary>
        /// Handles one or more items being deselected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being deselected.</param>
        public override void ItemsDeselected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = paths[0].Item;

            // Clear selection
            ParentViewController.PersonSelected = null;
        }
        #endregion
    }
}
``` 

I metodi `ItemsSelected` e `ItemsDeselected` vengono sottoposti a override e utilizzati per impostare o deselezionare la proprietà `PersonSelected` del controller di visualizzazione che gestisce la visualizzazione della raccolta quando l'utente seleziona o deseleziona un elemento. Questa operazione verrà illustrata in dettaglio di seguito.

<a name="Creating-the-Collection-View-in-Interface-Builder"/>

### <a name="creating-the-collection-view-in-interface-builder"></a>Creazione della visualizzazione raccolta in Interface Builder

Con tutti i componenti di supporto richiesti, lo storyboard principale può essere modificato e viene aggiunta una visualizzazione di raccolta.

Procedere come descritto di seguito:

1. Fare doppio clic sul file `Main.Storyboard` nel **Esplora soluzioni** per aprirlo per la modifica nella Interface Builder di Xcode.
2. Trascinare una visualizzazione raccolta nella visualizzazione principale e ridimensionarla per riempire la visualizzazione:

    ![Aggiunta di una visualizzazione raccolta al layout](collection-view-images/collection01.png)
3. Con la visualizzazione raccolta selezionata, usare l'editor di vincoli per aggiungerlo alla visualizzazione quando viene ridimensionato:

    ![Aggiunta di vincoli](collection-view-images/collection02.png)
4. Verificare che nella **area di progettazione** sia selezionata la visualizzazione raccolta (e non la visualizzazione a **scorrimento con bordi** o la **visualizzazione clip** che lo contiene), passare all' **Editor Assistente** e creare un' **uscita** per la visualizzazione raccolta:

    ![Aggiunta di vincoli](collection-view-images/collection03.png)
5. Salvare le modifiche e tornare a Visual Studio per la sincronizzazione.

<a name="Bringing-it-all-Together"/>

## <a name="bringing-it-all-together"></a>Riunendoli

Tutte le parti di supporto sono state inserite con una classe per fungere da modello di dati (`PersonModel`), è stata aggiunta una `NSCollectionViewDataSource` per fornire dati, è stato creato un `NSCollectionViewDelegateFlowLayout` per gestire la selezione degli elementi ed è stato aggiunto un `NSCollectionView` allo Storyboard principale e esposto come uscita (`EmployeeCollection`).

Il passaggio finale consiste nel modificare il controller di visualizzazione che contiene la visualizzazione raccolta e riunire tutti i componenti per popolare la raccolta e gestire la selezione degli elementi.

Modificare il file di `ViewController.cs` e renderlo simile al seguente:

```csharp
using System;
using AppKit;
using Foundation;
using CoreGraphics;

namespace MacCollectionNew
{
    /// <summary>
    /// The View controller controls the main view that houses the Collection View.
    /// </summary>
    public partial class ViewController : NSViewController
    {
        #region Private Variables
        private PersonModel _personSelected;
        private bool shouldEdit = true;
        #endregion

        #region Computed Properties
        /// <summary>
        /// Gets or sets the datasource that provides the data to display in the 
        /// Collection View.
        /// </summary>
        /// <value>The datasource.</value>
        public CollectionViewDataSource Datasource { get; set; }

        /// <summary>
        /// Gets or sets the person currently selected in the collection view.
        /// </summary>
        /// <value>The person selected or <c>null</c> if no person is selected.</value>
        [Export("PersonSelected")]
        public PersonModel PersonSelected
        {
            get { return _personSelected; }
            set
            {
                WillChangeValue("PersonSelected");
                _personSelected = value;
                DidChangeValue("PersonSelected");
                RaiseSelectionChanged();
            }
        }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.ViewController"/> class.
        /// </summary>
        /// <param name="handle">Handle.</param>
        public ViewController(IntPtr handle) : base(handle)
        {
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Called after the view has finished loading from the Storyboard to allow it to
        /// be configured before displaying to the user.
        /// </summary>
        public override void ViewDidLoad()
        {
            base.ViewDidLoad();

            // Initialize Collection View
            ConfigureCollectionView();
            PopulateWithData();
        }
        #endregion

        #region Private Methods
        /// <summary>
        /// Configures the collection view.
        /// </summary>
        private void ConfigureCollectionView()
        {
            EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");

            // Create a flow layout
            var flowLayout = new NSCollectionViewFlowLayout()
            {
                ItemSize = new CGSize(150, 150),
                SectionInset = new NSEdgeInsets(10, 10, 10, 20),
                MinimumInteritemSpacing = 10,
                MinimumLineSpacing = 10
            };
            EmployeeCollection.WantsLayer = true;

            // Setup collection view
            EmployeeCollection.CollectionViewLayout = flowLayout;
            EmployeeCollection.Delegate = new CollectionViewDelegate(this);

        }

        /// <summary>
        /// Populates the Datasource with data and attaches it to the collection view.
        /// </summary>
        private void PopulateWithData()
        {
            // Make datasource
            Datasource = new CollectionViewDataSource(EmployeeCollection);

            // Build list of employees
            Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
            Datasource.Data.Add(new PersonModel("Amy Burns", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Joel Martinez", "Web & Infrastructure"));
            Datasource.Data.Add(new PersonModel("Kevin Mullins", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Mark McLemore", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Tom Opgenorth", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Larry O'Brien", "API Docs Manager", true));
            Datasource.Data.Add(new PersonModel("Mike Norman", "API Documentor"));

            // Populate collection view
            EmployeeCollection.ReloadData();
        }
        #endregion

        #region Events
        /// <summary>
        /// Selection changed delegate.
        /// </summary>
        public delegate void SelectionChangedDelegate();

        /// <summary>
        /// Occurs when selection changed.
        /// </summary>
        public event SelectionChangedDelegate SelectionChanged;

        /// <summary>
        /// Raises the selection changed event.
        /// </summary>
        internal void RaiseSelectionChanged() {
            // Inform caller
            if (this.SelectionChanged != null) SelectionChanged();
        }
        #endregion
    }
}
```

Esaminando il codice in dettaglio, viene definita una proprietà `Datasource` per mantenere un'istanza del `CollectionViewDataSource` che fornirà i dati per la visualizzazione della raccolta. Viene definita una proprietà `PersonSelected` per conservare l'`PersonModel` che rappresenta l'elemento attualmente selezionato nella visualizzazione della raccolta. Questa proprietà genera anche l'evento `SelectionChanged` quando la selezione viene modificata.

La classe `ConfigureCollectionView` viene utilizzata per registrare il controller di visualizzazione che funge da prototipo di cella con la visualizzazione raccolta utilizzando la riga seguente:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

Si noti che l' **identificatore** (`EmployeeCell`) usato per registrare il prototipo corrisponde a quello chiamato nel metodo `GetItem` del `CollectionViewDataSource` definito in precedenza:

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

Inoltre, il tipo del controller di visualizzazione **deve** corrispondere al nome del file `.xib` che definisce **esattamente**il prototipo. Nel caso di questo esempio, `EmployeeItemController` e `EmployeeItemController.xib`.

Il layout effettivo degli elementi nella visualizzazione raccolta è controllato da una classe di layout della visualizzazione raccolta e può essere modificato dinamicamente in fase di esecuzione assegnando una nuova istanza alla proprietà `CollectionViewLayout`. Se si modifica questa proprietà, l'aspetto della visualizzazione raccolta verrà aggiornato senza animare la modifica.

Apple fornisce due tipi di layout predefiniti con la visualizzazione raccolta che gestirà la maggior parte degli usi tipici: `NSCollectionViewFlowLayout` e `NSCollectionViewGridLayout`. Se lo sviluppatore ha richiesto un formato personalizzato, ad esempio disponendone gli elementi in un cerchio, è possibile creare un'istanza personalizzata di `NSCollectionViewLayout` ed eseguire l'override dei metodi richiesti per ottenere l'effetto desiderato.

In questo esempio viene usato il layout del flusso predefinito in modo da creare un'istanza della classe `NSCollectionViewFlowLayout` e configurarla come indicato di seguito:

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

La proprietà `ItemSize` definisce le dimensioni di ogni singola cella della raccolta. La proprietà `SectionInset` definisce i set di impostazioni dal bordo della raccolta in cui verranno definite le celle. `MinimumInteritemSpacing` definisce la spaziatura minima tra gli elementi e `MinimumLineSpacing` definisce la spaziatura minima tra le righe della raccolta.

Il layout viene assegnato alla visualizzazione della raccolta e un'istanza del `CollectionViewDelegate` è collegata alla selezione dell'elemento di gestione:

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

Il metodo `PopulateWithData` crea una nuova istanza della `CollectionViewDataSource`, la popola con i dati, la associa alla visualizzazione della raccolta e chiama il metodo `ReloadData` per visualizzare gli elementi:

```csharp
private void PopulateWithData()
{
    // Make datasource
    Datasource = new CollectionViewDataSource(EmployeeCollection);

    // Build list of employees
    Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
    ...

    // Populate collection view
    EmployeeCollection.ReloadData();
}
```

Viene eseguito l'override del metodo `ViewDidLoad` e vengono chiamati i metodi `ConfigureCollectionView` e `PopulateWithData` per visualizzare la visualizzazione della raccolta finale per l'utente:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();

    // Initialize Collection View
    ConfigureCollectionView();
    PopulateWithData();
}
```

<a name="Summary"/>

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato in dettaglio l'uso delle visualizzazioni di raccolta in un'applicazione Xamarin.Mac. In primo luogo, è stata esaminata C# l'esposizione di una classe a Objective-C utilizzando la codifica chiave-valore (KVC) e l'osservazione chiave-valore (KVO). A questo punto, è stato illustrato come usare una classe conforme a KVO e i dati vengono associati a visualizzazioni di raccolta nel Interface Builder di Xcode. Infine, è stato illustrato come interagire con le visualizzazioni di C# raccolta nel codice.

## <a name="related-links"></a>Collegamenti correlati

- [MacCollectionNew (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccollectionnew)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
