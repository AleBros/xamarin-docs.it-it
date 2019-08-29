---
title: Viste di raccolta in Novell. Mac
description: Questo articolo descrive l'uso delle visualizzazioni di raccolta in un'app Novell. Mac. Viene illustrata la creazione e la gestione delle visualizzazioni di raccolta in Xcode e Interface Builder e l'utilizzo di tali viste a livello di codice.
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 05/24/2017
ms.openlocfilehash: ee1b3043033ea1d6e024619e6a176947733e9f24
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065478"
---
# <a name="collection-views-in-xamarinmac"></a>Viste di raccolta in Novell. Mac

_Questo articolo descrive l'uso delle visualizzazioni di raccolta in un'app Novell. Mac. Viene illustrata la creazione e la gestione delle visualizzazioni di raccolta in Xcode e Interface Builder e l'utilizzo di tali viste a livello di codice._

Quando si lavora C# con e .NET in un'app Novell. Mac, lo sviluppatore ha accesso agli stessi controlli di visualizzazione raccolta AppKit che uno sviluppatore lavora in *Objective-C* e *Xcode* . Poiché Novell. Mac si integra direttamente con Xcode, lo sviluppatore usa _Interface Builder_ di Xcode per creare e gestire le visualizzazioni di raccolta.

Un `NSCollectionView` oggetto consente di visualizzare una griglia di sottoviste organizzate utilizzando un oggetto `NSCollectionViewLayout`. Ogni sottovista della griglia viene rappresentata da un `NSCollectionViewItem` oggetto che gestisce il caricamento del contenuto della visualizzazione da un `.xib` file.

[![Esecuzione di un'app di esempio](collection-view-images/intro01.png)](collection-view-images/intro01.png#lightbox)

Questo articolo illustra le nozioni di base sull'uso delle visualizzazioni di raccolta in un'app Novell. Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti e le tecniche principali usati in questo articolo.

Si consiglia di esaminare la sezione [ C# esporre classi/metodi in Objective-C](~/mac/internals/how-it-works.md) del documento [interno di Novell. Mac](~/mac/internals/how-it-works.md) , spiegando `Register` i comandi e `Export` usati per collegare le C# classi a. Oggetti Objective-C ed elementi dell'interfaccia utente.

<a name="About_Collection_Views"/>

## <a name="about-collection-views"></a>Informazioni sulle visualizzazioni di raccolta

L'obiettivo principale di una visualizzazione di raccolta`NSCollectionView`() consiste nel disporre visivamente di un gruppo di oggetti in modo organizzato utilizzando un layout di visualizzazione`NSCollectionViewLayout`raccolta (), con ogni singolo`NSCollectionViewItem`oggetto () che recupera una propria visualizzazione nella raccolta più grande. Le visualizzazioni di raccolta funzionano tramite il data binding e le tecniche di codifica chiave-valore e, di conseguenza, è consigliabile leggere il [Data Binding e](~/mac/app-fundamentals/databinding.md) la documentazione di codifica del valore chiave prima di continuare con questo articolo.

La visualizzazione raccolta non contiene elementi standard e predefiniti della visualizzazione raccolta (ad esempio, un contorno o una vista tabella), quindi lo sviluppatore è responsabile della progettazione e dell'implementazione di una _visualizzazione prototipo_ usando altri controlli AppKit, ad esempio campi immagine, campi di testo, etichette, Via. Questa vista prototipo verrà utilizzata per visualizzare e utilizzare ogni elemento gestito dalla visualizzazione raccolta e viene archiviato in un `.xib` file.

Poiché lo sviluppatore è responsabile dell'aspetto di un elemento della visualizzazione raccolta, la visualizzazione raccolta non dispone di alcun supporto incorporato per evidenziare un elemento selezionato nella griglia. L'implementazione di questa funzionalità verrà illustrata in questo articolo.

<a name="Defining_your_Data_Model"/>

## <a name="defining-the-data-model"></a>Definizione del modello di dati

Prima di associare i dati a una visualizzazione di raccolta in Interface Builder, è necessario definire una classe conforme a KVC (Key-Value Coding)/Key-Value Osservating (KVO) nell'app Novell. Mac in modo che funga da _modello di dati_ per l'associazione. Il modello di dati fornisce tutti i dati che verranno visualizzati nella raccolta e riceverà tutte le modifiche ai dati che l'utente effettua nell'interfaccia utente durante l'esecuzione dell'applicazione.

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

Il `PersonModel` modello di dati verrà usato nel resto di questo articolo.

<a name="Working_with_a_Collection_View"/>

## <a name="working-with-a-collection-view"></a>Utilizzo di una visualizzazione di raccolta

Il data binding con una visualizzazione di raccolta è molto simile al binding con una visualizzazione tabella `NSCollectionViewDataSource` , così come viene usato per fornire i dati per la raccolta. Poiché la visualizzazione raccolta non ha un formato di visualizzazione preimpostato, è necessario più lavoro per fornire commenti e suggerimenti sull'interazione dell'utente e per tenere traccia della selezione dell'utente.

<a name="Creating-the-Cell-Prototype"/>

### <a name="creating-the-cell-prototype"></a>Creazione del prototipo di cella

Poiché la visualizzazione raccolta non include un prototipo di cella predefinito, lo sviluppatore dovrà aggiungere uno o più `.xib` file all'app Novell. Mac per definire il layout e il contenuto delle singole celle.

Seguire questa procedura:

1. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Aggiungi** > **nuovo file...**
2. Selezionare **Mac** > **View Controller**, assegnargli un nome, ad `EmployeeItem` esempio in questo esempio, e fare clic sul pulsante **nuovo** per creare: 

    ![Aggiunta di un nuovo controller di visualizzazione](collection-view-images/proto01.png)

    Verrà aggiunto un `EmployeeItem.cs`file, `EmployeeItemController.cs` e `EmployeeItemController.xib` alla soluzione del progetto.
3. Fare doppio clic sul `EmployeeItemController.xib` file per aprirlo per la modifica nella Interface Builder di Xcode.
4. Aggiungere un `NSBox`oggetto `NSImageView` e due `NSLabel` controlli alla visualizzazione e disporli come indicato di seguito:

    ![Progettazione del layout del prototipo di cella](collection-view-images/proto02.png)
5. Aprire l' **editor di assistente** e creare un **Outlet** per `NSBox` in modo da poterlo usare per indicare lo stato di selezione di una cella:

    ![Esposizione di NSBox in un Outlet](collection-view-images/proto03.png)
6. Tornare all' **editor standard** e selezionare la visualizzazione immagine.
7. Nel **controllo Binding**selezionare **associa al** > **proprietario del file** e immettere il percorso della **chiave** del `self.Person.Icon`modello:

    ![Associazione dell'icona](collection-view-images/proto04.png)
8. Selezionare la prima etichetta e in **controllo Binding**selezionare **associa al** > **proprietario del file** e immettere il percorso di una chiave del `self.Person.Name`modello:

    ![Associazione del nome](collection-view-images/proto05.png)
9. Selezionare la seconda etichetta e in **controllo Binding**selezionare **associa al** > **proprietario del file** e immettere il percorso della **chiave** del `self.Person.Occupation`modello:

    ![Associazione dell'occupazione](collection-view-images/proto06.png)
10. Salvare le modifiche apportate al `.xib` file e tornare a Visual Studio per sincronizzare le modifiche.

Modificare il `EmployeeItemController.cs` file e renderlo simile al seguente:

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

Esaminando il codice in dettaglio, la classe eredita da `NSCollectionViewItem` , quindi può fungere da prototipo per una cella di visualizzazione raccolta. La `Person` proprietà espone la classe utilizzata per l'associazione dati alla visualizzazione immagine e alle etichette in Xcode. Si tratta di un'istanza del `PersonModel` creato in precedenza.

La `BackgroundColor` proprietà è un collegamento all'oggetto `NSBox` del controllo `FillColor` che verrà usato per mostrare lo stato di selezione di una cella. Eseguendo l'override della `Selected` proprietà `NSCollectionViewItem`di, il codice seguente imposta o cancella questo stato di selezione:

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

Un'origine dati di visualizzazione raccolta`NSCollectionViewDataSource`() fornisce tutti i dati per una visualizzazione raccolta e crea e popola una cella di visualizzazione raccolta (usando il `.xib` prototipo) come richiesto per ogni elemento nella raccolta.

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

Esaminando il codice in dettaglio, la classe eredita da `NSCollectionViewDataSource` ed espone un elenco di `PersonModel` istanze tramite la `Data` relativa proprietà.

Poiché questa raccolta include solo una sezione, il codice esegue l' `GetNumberOfSections` override del metodo e `1`restituisce sempre. Inoltre, viene `GetNumberofItems` eseguito l'override del metodo in modo che restituisca il numero di `Data` elementi nell'elenco delle proprietà.

Il `GetItem` metodo viene chiamato ogni volta che è richiesta una nuova cella e ha un aspetto simile al seguente:

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

Il `MakeItem` metodo della visualizzazione raccolta viene chiamato per creare o restituire un'istanza riutilizzabile `EmployeeItemController` di e la relativa `Person` proprietà è impostata sull'elemento visualizzato nella cella richiesta. 

Il `EmployeeItemController` deve essere registrato con il controller di visualizzazione raccolta in anticipo usando il codice seguente:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

L' **identificatore** (`EmployeeCell` `MakeItem` ) utilizzato nella chiamata _deve_ corrispondere al nome del controller di visualizzazione registrato con la visualizzazione della raccolta. Questo passaggio verrà analizzato in dettaglio di seguito.

<a name="Handling-Item-Selection"/>

### <a name="handling-item-selection"></a>Gestione selezione elementi

Per gestire la selezione e la deselezione degli elementi nella raccolta, sarà necessario `NSCollectionViewDelegate` un. Poiché in questo esempio verrà usato il tipo di `NSCollectionViewFlowLayout` layout predefinito, sarà `NSCollectionViewDelegateFlowLayout` necessaria una versione specifica di questo delegato.

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

I `ItemsSelected` metodi `ItemsDeselected` e vengono sottoposti a override e usati per impostare o `PersonSelected` deselezionare la proprietà del controller di visualizzazione che gestisce la visualizzazione della raccolta quando l'utente seleziona o deseleziona un elemento. Questa operazione verrà illustrata in dettaglio di seguito.

<a name="Creating-the-Collection-View-in-Interface-Builder"/>

### <a name="creating-the-collection-view-in-interface-builder"></a>Creazione della visualizzazione raccolta in Interface Builder

Con tutti i componenti di supporto richiesti, lo storyboard principale può essere modificato e viene aggiunta una visualizzazione di raccolta.

Seguire questa procedura:

1. Fare doppio clic sul `Main.Storyboard` file nella **Esplora soluzioni** per aprirlo per la modifica nella Interface Builder di Xcode.
2. Trascinare una visualizzazione raccolta nella visualizzazione principale e ridimensionarla per riempire la visualizzazione:

    ![Aggiunta di una visualizzazione raccolta al layout](collection-view-images/collection01.png)
3. Con la visualizzazione raccolta selezionata, usare l'editor di vincoli per aggiungerlo alla visualizzazione quando viene ridimensionato:

    ![Aggiunta di vincoli](collection-view-images/collection02.png)
4. Verificare che nella **area di progettazione** sia selezionata la visualizzazione raccolta (e non la visualizzazione a **scorrimento con bordi** o la **visualizzazione clip** che lo contiene), passare all' **Editor Assistente** e creare un' **uscita** per la visualizzazione raccolta:

    ![Aggiunta di vincoli](collection-view-images/collection03.png)
5. Salvare le modifiche e tornare a Visual Studio per la sincronizzazione.

<a name="Bringing-it-all-Together"/>

## <a name="bringing-it-all-together"></a>Riunendoli

Tutte le parti di supporto sono state ora inserite con una`PersonModel`classe per fungere da modello di dati (), è stato aggiunto un `NSCollectionViewDataSource` oggetto per fornire i dati, `NSCollectionViewDelegateFlowLayout` è stato creato un oggetto per gestire la `NSCollectionView` selezione dell'elemento e un è stato aggiunto allo Storyboard principale ed esposti come un Outlet (`EmployeeCollection`).

Il passaggio finale consiste nel modificare il controller di visualizzazione che contiene la visualizzazione raccolta e riunire tutti i componenti per popolare la raccolta e gestire la selezione degli elementi.

Modificare il `ViewController.cs` file e renderlo simile al seguente:

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

Esaminando il codice in dettaglio, viene definita una `Datasource` proprietà `CollectionViewDataSource` che conterrà un'istanza del che fornirà i dati per la visualizzazione della raccolta. Viene `PersonSelected` definita una proprietà che `PersonModel` rappresenta l'elemento attualmente selezionato nella visualizzazione dell'insieme. Questa proprietà genera anche l' `SelectionChanged` evento quando la selezione viene modificata.

La `ConfigureCollectionView` classe viene utilizzata per registrare il controller di visualizzazione che funge da prototipo di cella con la visualizzazione raccolta utilizzando la riga seguente:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

Si noti che l'identificatore`EmployeeCell`() usato per registrare il prototipo corrisponde a quello chiamato nel `CollectionViewDataSource`metododi definito in precedenza: `GetItem`

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

Inoltre, il tipo del controller di visualizzazione **deve** corrispondere al nome del `.xib` file che definisce **esattamente**il prototipo. Nel caso di questo esempio, `EmployeeItemController` e. `EmployeeItemController.xib`

Il layout effettivo degli elementi nella visualizzazione raccolta è controllato da una classe di layout della visualizzazione raccolta e può essere modificato dinamicamente in fase di esecuzione assegnando una nuova istanza alla `CollectionViewLayout` proprietà. Se si modifica questa proprietà, l'aspetto della visualizzazione raccolta verrà aggiornato senza animare la modifica.

Apple fornisce due tipi di layout predefiniti con la visualizzazione raccolta che gestirà la maggior parte degli usi `NSCollectionViewFlowLayout` tipici `NSCollectionViewGridLayout`: e. Se lo sviluppatore ha richiesto un formato personalizzato, ad esempio disponendone gli elementi in un cerchio, è possibile creare un'istanza `NSCollectionViewLayout` personalizzata di ed eseguire l'override dei metodi richiesti per ottenere l'effetto desiderato.

In questo esempio viene usato il layout del flusso predefinito in modo da creare `NSCollectionViewFlowLayout` un'istanza della classe e configurarla come indicato di seguito:

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

La `ItemSize` proprietà definisce le dimensioni di ogni singola cella della raccolta. La `SectionInset` proprietà definisce i set di impostazioni dal bordo della raccolta in cui verranno definite le celle. `MinimumInteritemSpacing`definisce la spaziatura minima tra gli `MinimumLineSpacing` elementi e definisce la spaziatura minima tra le righe della raccolta.

Il layout viene assegnato alla visualizzazione della raccolta e un'istanza del `CollectionViewDelegate` è associata alla selezione dell'elemento di gestione:

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

Il `PopulateWithData` metodo crea una nuova istanza `CollectionViewDataSource`di, la popola con i dati, la collega alla visualizzazione della raccolta e chiama il `ReloadData` metodo per visualizzare gli elementi:

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

Viene `ViewDidLoad` eseguito l'override del metodo e vengono `ConfigureCollectionView` chiamati `PopulateWithData` i metodi e per visualizzare la visualizzazione della raccolta finale all'utente:

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

Questo articolo ha esaminato in dettaglio l'uso delle visualizzazioni di raccolta in un'applicazione Novell. Mac. In primo luogo, è stata esaminata C# l'esposizione di una classe a Objective-C utilizzando la codifica chiave-valore (KVC) e l'osservazione chiave-valore (KVO). A questo punto, è stato illustrato come usare una classe conforme a KVO e i dati vengono associati a visualizzazioni di raccolta nel Interface Builder di Xcode. Infine, è stato illustrato come interagire con le visualizzazioni di C# raccolta nel codice.

## <a name="related-links"></a>Collegamenti correlati

- [MacCollectionNew (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccollectionnew)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
