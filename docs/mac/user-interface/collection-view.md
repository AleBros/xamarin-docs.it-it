---
title: Viste di raccolta in Xamarin.Mac
description: In questo articolo descrive l'utilizzo con le visualizzazioni di raccolta in un'app Xamarin.Mac. Vengono illustrate la creazione di viste di raccolta in Xcode e il generatore di interfaccia di gestione e utilizzo a livello di codice.
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/24/2017
ms.openlocfilehash: 8e354b1ce273b10758a7d8c1361055b972839943
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792557"
---
# <a name="collection-views-in-xamarinmac"></a>Viste di raccolta in Xamarin.Mac

_In questo articolo descrive l'utilizzo con le visualizzazioni di raccolta in un'app Xamarin.Mac. Vengono illustrate la creazione di viste di raccolta in Xcode e il generatore di interfaccia di gestione e utilizzo a livello di codice._

Quando l'utilizzo di c# e .NET in un'app Xamarin.Mac, lo sviluppatore ha accesso alla stessa visualizzazione raccolta AppKit i controlli che uno sviluppatore che lavora *Objective-C* e *Xcode* does. Poiché Xamarin.Mac si integra direttamente con Xcode, lo sviluppatore utilizza del Xcode _generatore interfaccia_ per creare e gestire le visualizzazioni di raccolta.

Oggetto `NSCollectionView` viene visualizzata una griglia sottoviste organizzate mediante un `NSCollectionViewLayout`. Ogni visualizzazione secondaria nella griglia è rappresentato da un `NSCollectionViewItem` che gestisce il caricamento di contenuto della visualizzazione di un `.xib` file.

[![Eseguire un'app di esempio](collection-view-images/intro01.png)](collection-view-images/intro01.png#lightbox)

Questo articolo vengono illustrate le nozioni di base dell'utilizzo di viste di raccolta in un'app Xamarin.Mac. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) le sezioni, come illustra i concetti chiave e le tecniche utilizzate in questo articolo.

Si consiglia di esaminare il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) del documento, nonché viene descritto il `Register` e `Export` comandi utilizzato per le classi c# in transito-fino a oggetti Objective-C e gli elementi dell'interfaccia utente.

<a name="About_Collection_Views"/>

## <a name="about-collection-views"></a>Informazioni sulle visualizzazioni di raccolta

L'obiettivo principale di una visualizzazione di raccolta (`NSCollectionView`) è disporre visivamente un gruppo di oggetti in modo organizzato utilizzando un Layout di visualizzazione di raccolta (`NSCollectionViewLayout`), con ogni singolo oggetto (`NSCollectionViewItem`) il recupero di una propria vista nella raccolta di dimensioni maggiore. Viste di raccolta di lavoro tramite le tecniche di Data Binding e la generazione di codice chiave-valore e di conseguenza, si consiglia di leggere il [Data Binding e la generazione di codice chiave-valore](~/mac/app-fundamentals/databinding.md) documentazione prima di continuare con questo articolo.

La visualizzazione della raccolta non ha standard, incorporata vista elemento della raccolta (ad esempio una struttura o una vista di tabella), in modo lo sviluppatore è responsabile della progettazione e implementazione di un _prototipo visualizzazione_ di altri controlli AppKit ad esempio i campi immagine , I campi di testo, le etichette e così via. Questa vista prototipo verrà utilizzata per visualizzare e utilizzare con ogni elemento viene gestito mediante la visualizzazione della raccolta e viene archiviata un `.xib` file.

Poiché lo sviluppatore è responsabile per l'aspetto di un elemento di visualizzazione di raccolta, la visualizzazione della raccolta non ha nessun supporto incorporato per evidenziare un elemento selezionato nella griglia. Implementazione di questa funzionalità verrà descritta in questo articolo.

<a name="Defining_your_Data_Model"/>

## <a name="defining-the-data-model"></a>La definizione del modello di dati

Prima di Data Binding di una visualizzazione di raccolta nel generatore di interfaccia, una chiave-valore di codifica (i KVM) / classe conforme chiave-valore osservazione (KVO) deve essere definito nell'app Xamarin.Mac come il _modello di dati_ per l'associazione. Il modello di dati fornisce tutti i dati che verranno visualizzati nella raccolta e riceve le modifiche ai dati apportate dall'utente nell'interfaccia utente durante l'esecuzione dell'applicazione.

Nell'esempio di un'app che gestisce un gruppo di dipendenti, la classe seguente può essere usata per definire il modello di dati:

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

Il `PersonModel` verrà utilizzato il modello di dati in tutto il resto di questo articolo.

<a name="Working_with_a_Collection_View"/>

## <a name="working-with-a-collection-view"></a>Utilizzo di una visualizzazione di raccolta

Associazione dati con una visualizzazione di raccolta è molto simile associazione like con una visualizzazione tabella, come `NSCollectionViewDataSource` viene utilizzata per fornire dati per la raccolta. Poiché la visualizzazione della raccolta non ha un formato di visualizzazione predefinito, è necessario per fornire commenti e suggerimenti di interazione dell'utente e tenere traccia di selezione dell'utente più lavoro.

<a name="Creating-the-Cell-Prototype"/>

### <a name="creating-the-cell-prototype"></a>Creazione del prototipo di cella

Poiché la visualizzazione della raccolta non è incluso un prototipo di cella predefinito, lo sviluppatore dovrà aggiungere uno o più `.xib` file per l'app Xamarin.Mac per definire il layout e il contenuto delle singole celle.

Seguire questa procedura:

1. Nel **Esplora**del mouse sul nome del progetto e scegliere **Aggiungi** > **nuovo File...**
2. Selezionare **Mac** > **View Controller**, assegnargli un nome (ad esempio `EmployeeItem` in questo esempio) e fare clic su di **New** pulsante per la creazione: 

    ![Aggiunge un nuovo controller di visualizzazione](collection-view-images/proto01.png)

    Verrà aggiunta una `EmployeeItem.cs`, `EmployeeItemController.cs` e `EmployeeItemController.xib` file per la soluzione del progetto.
3. Fare doppio clic su di `EmployeeItemController.xib` file per aprirlo e modificarlo in Generatore del Xcode di interfaccia.
4. Aggiungere un `NSBox`, `NSImageView` e due `NSLabel` controlli per la visualizzazione e disporli come indicato di seguito:

    ![Progettare il layout del prototipo della cella](collection-view-images/proto02.png)
5. Aprire il **Assistente Editor** e creare un **presa** per il `NSBox` in modo che può essere utilizzato per indicare lo stato di selezione di una cella:

    ![Esposizione di NSBox in una presa di corrente](collection-view-images/proto03.png)
6. Restituito per il **Editor Standard** e selezionare la visualizzazione dell'immagine.
7. Nel **controllo associazione**selezionare **associa a** > **proprietario del File** e immettere un **il percorso della chiave del modello** di `self.Person.Icon`:

    ![L'icona di associazione](collection-view-images/proto04.png)
8. Selezionare l'etichetta del primo e il **controllo con associazione**, selezionare **associa a** > **proprietario del File** e immettere un **ilpercorsodellachiavedelmodello**di `self.Person.Name`:

    ![Il nome dell'associazione](collection-view-images/proto05.png)
9. Selezionare la seconda etichetta e il **controllo con associazione**, selezionare **associa a** > **proprietario del File** e immettere un **il percorso della chiave del modello**di `self.Person.Occupation`:

    ![La proprietà di associazione](collection-view-images/proto06.png)
10. Salvare le modifiche per il `.xib` file e tornare a Visual Studio per sincronizzare le modifiche.

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

Esaminando questo codice in modo dettagliato, la classe eredita da `NSCollectionViewItem` affinché possa fungere da un prototipo per una cella di visualizzazione della raccolta. Il `Person` proprietà espone la classe che è stato utilizzato per l'associazione dati per la visualizzazione di immagini ed etichette in Xcode. Si tratta di un'istanza di `PersonModel` creato in precedenza.

Il `BackgroundColor` proprietà è una scelta rapida per il `NSBox` del controllo `FillColor` che verrà usato per visualizzare lo stato di selezione di una cella. Eseguendo l'override di `Selected` proprietà del `NSCollectionViewItem`, il codice seguente imposta o Cancella lo stato di questa selezione:

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

### <a name="creating-the-collection-view-data-source"></a>Creazione dell'origine di dati di visualizzazione di raccolta

Un'origine di dati di visualizzazione di raccolta (`NSCollectionViewDataSource`) fornisce tutti i dati per una visualizzazione di raccolta e crea e popola una cella di visualizzazione di raccolta (utilizzando la `.xib` prototipo) come richiesto per ogni elemento nella raccolta.

Aggiungere una nuova classe il progetto, chiamarlo `CollectionViewDataSource` e renderlo simile al seguente:

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

Esaminando questo codice in modo dettagliato, la classe eredita da `NSCollectionViewDataSource` ed espone un elenco di `PersonModel` istanze tramite il relativo `Data` proprietà.

Poiché questa raccolta dispone solo di una sezione, il codice esegue l'override di `GetNumberOfSections` (metodo) e restituisce sempre `1`. Inoltre, il `GetNumberofItems` metodo sottoposto a override in restituisce il numero di elementi nel `Data` elenco di proprietà.

Il `GetItem` metodo viene chiamato ogni volta che una nuova cella è obbligatorio e simile al seguente:

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

Il `MakeItem` della visualizzazione di raccolta viene chiamato per creare o restituire un'istanza di riutilizzabili di `EmployeeItemController` e il relativo `Person` proprietà è impostata su elemento viene visualizzato nella cella richiesta. 

Il `EmployeeItemController` deve essere registrato con il Controller di visualizzazione di raccolta prima di utilizzare il codice riportato di seguito:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

Il **identificatore** (`EmployeeCell`) utilizzato nel `MakeItem` chiamare _deve_ corrisponde al nome del Controller di visualizzazione che è stato registrato con la visualizzazione della raccolta. Questo passaggio verrà descritta in dettaglio di seguito.

<a name="Handling-Item-Selection"/>

### <a name="handling-item-selection"></a>Selezione di elementi di gestione

Per gestire la selezione e la deselezione di elementi nella raccolta, un `NSCollectionViewDelegate` sarà necessaria. Poiché in questo esempio utilizzerà incorporato `NSCollectionViewFlowLayout` il tipo di layout, un `NSCollectionViewDelegateFlowLayout` sarà necessaria la versione specifica del delegato.

Aggiungere una nuova classe al progetto, chiamarlo `CollectionViewDelegate` e renderlo simile al seguente:

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

Il `ItemsSelected` e `ItemsDeselected` metodi sono sottoposto a override e utilizzati per impostare o deselezionare il `PersonSelected` proprietà del Controller di visualizzazione che gestisce la visualizzazione della raccolta quando l'utente seleziona o deseleziona un elemento. Verrà visualizzata in dettaglio di seguito.

<a name="Creating-the-Collection-View-in-Interface-Builder"/>

### <a name="creating-the-collection-view-in-interface-builder"></a>Creazione della visualizzazione di raccolta nel generatore di interfaccia

Con tutte le parti necessarie di supporto sul posto, lo storyboard principale può essere modificato e aggiunta di una vista di insieme a esso.

Seguire questa procedura:

1. Fare doppio clic su di `Main.Storyboard` file nel **Esplora** per aprirlo e modificarlo in Xcode del generatore di interfaccia.
2. Trascinare una visualizzazione di raccolta nella vista principale e ridimensionarlo per riempire la vista:

    ![Aggiunta di una vista di raccolta per il layout](collection-view-images/collection01.png)
3. Con la visualizzazione della raccolta selezionata, utilizzare l'Editor di vincolo per aggiungerlo alla visualizzazione al ridimensionamento:

    ![Aggiunta di vincoli](collection-view-images/collection02.png)
4. Verificare che la visualizzazione della raccolta sia selezionata nel **area di progettazione** (e non il **bordo visualizzazione a scorrimento** o **visualizzazione Clip** che lo contiene), passare al  **Editor Assistente** e creare un **presa** per la visualizzazione di raccolta:

    ![Aggiunta di vincoli](collection-view-images/collection03.png)
5. Salvare le modifiche e tornare a Visual Studio per la sincronizzazione.

<a name="Bringing-it-all-Together"/>

## <a name="bringing-it-all-together"></a>Importarli tutti unione

Tutti i componenti di supporto ora inseriti in una posizione con una classe di agire come modello di dati (`PersonModel`), un `NSCollectionViewDataSource` è stato aggiunto per fornire i dati, un `NSCollectionViewDelegateFlowLayout` è stato creato per gestire la selezione di elementi e un `NSCollectionView` è stato aggiunto allo Storyboard principale ed esposto come una presa di corrente (`EmployeeCollection`).

Il passaggio finale è possibile modificare il Controller di visualizzazione che contiene la vista di raccolta e visualizzare tutte le parti collaborano per popolare la raccolta e gestire la selezione di elementi.

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

Osservare il codice in dettaglio, un `Datasource` proprietà è definita per contenere un'istanza del `CollectionViewDataSource` che fornirà i dati per la visualizzazione della raccolta. Oggetto `PersonSelected` proprietà è definita per contenere il `PersonModel` che rappresenta l'elemento attualmente selezionato nella visualizzazione raccolta. Questa proprietà genera inoltre il `SelectionChanged` evento quando cambia la selezione.

La `ConfigureCollectionView` classe viene utilizzata per registrare il Controller di visualizzazione che si comporta come il prototipo della cella con la visualizzazione di raccolta utilizzando la seguente riga:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

Si noti che il **identificatore** (`EmployeeCell`) utilizzato per registrare le corrispondenze di prototipo di una chiamata nel `GetItem` metodo il `CollectionViewDataSource` definiti in precedenza:

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

Inoltre, il tipo di visualizzazione Controller **deve** corrisponde al nome del `.xib` file che definisce il prototipo **esattamente**. In questo esempio `EmployeeItemController` e `EmployeeItemController.xib`.

Il layout effettivo di elementi nella visualizzazione raccolta è controllato da una classe di visualizzazione di raccolta e possono essere modificato dinamicamente in fase di esecuzione assegnando una nuova istanza per il `CollectionViewLayout` proprietà. Modifica di questa proprietà Aggiorna l'aspetto della visualizzazione di raccolta senza la modifica di animazione.

Apple fornito due tipi di layout incorporato con la visualizzazione della raccolta che verrà gestito usi più comuni: `NSCollectionViewFlowLayout` e `NSCollectionViewGridLayout`. Se è necessario che un formato personalizzato, ad esempio di definizione del layout degli elementi in un cerchio, lo sviluppatore possono creare un'istanza personalizzata di `NSCollectionViewLayout` ed eseguire l'override dei metodi necessari per ottenere l'effetto desiderato.

Questo esempio viene utilizzato il layout di flusso predefinito crea un'istanza di `NSCollectionViewFlowLayout` classe e lo configura come indicato di seguito:

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

Il `ItemSize` proprietà definisce la dimensione delle singole celle nella raccolta. Il `SectionInset` proprietà definisce gli elementi interni tra il bordo della raccolta di celle verranno disposti in. `MinimumInteritemSpacing` definisce la spaziatura minima tra gli elementi e `MinimumLineSpacing` definisce la spaziatura minima tra le righe nella raccolta.

Il layout viene assegnato per la visualizzazione di raccolta e un'istanza di `CollectionViewDelegate` è collegato per gestire la selezione di elementi:

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

Il `PopulateWithData` metodo crea una nuova istanza di `CollectionViewDataSource`, popola con dati, lo collega alla visualizzazione di raccolta e chiama il `ReloadData` metodo per visualizzare gli elementi:

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

Il `ViewDidLoad` viene eseguito l'override di metodo e chiama il `ConfigureCollectionView` e `PopulateWithData` metodi per la visualizzazione raccolta finale per l'utente:

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

In questo articolo è stato applicato l'utilizzo delle visualizzazioni di raccolta in un'applicazione Xamarin.Mac un'analisi approfondita. Innanzitutto, esaminato l'esposizione di una classe c# per Objective-C con dati XML di codifica (i, chiave-valore KVM) e chiave-valore osservazione (KVO). Successivamente, è stato spiegato come utilizzare una classe conforme KVO e dati associarli alle visualizzazioni di raccolta in Generatore del Xcode di interfaccia. Infine, mostrato come interagire con le visualizzazioni di raccolta nel codice c#.

## <a name="related-links"></a>Collegamenti correlati

- [MacCollectionNew (esempio)](https://developer.xamarin.com/samples/mac/MacCollectionNew/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
