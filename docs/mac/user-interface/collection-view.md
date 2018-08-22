---
title: Visualizzazioni raccolta in xamarin. Mac
description: Questo articolo descrive l'utilizzo con le visualizzazioni di raccolta in un'app xamarin. Mac. Viene descritto come creare e gestire le visualizzazioni raccolta in Xcode e Interface Builder e usare le loro a livello di codice.
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/24/2017
ms.openlocfilehash: c8b4b5ff8bebf5fbbded410ae84d1aefcca2d6cc
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251110"
---
# <a name="collection-views-in-xamarinmac"></a>Visualizzazioni raccolta in xamarin. Mac

_Questo articolo descrive l'utilizzo con le visualizzazioni di raccolta in un'app xamarin. Mac. Viene descritto come creare e gestire le visualizzazioni raccolta in Xcode e Interface Builder e usare le loro a livello di codice._

Quando si lavora con c# e .NET di un'app xamarin. Mac, lo sviluppatore ha accesso alla stessa visualizzazione di raccolta di AppKit controlla che uno sviluppatore che lavora in *Objective-C* e *Xcode* viene. Poiché xamarin. Mac si integra direttamente con Xcode, lo sviluppatore Usa Xcode _Interface Builder_ per creare e gestire le visualizzazioni di raccolta.

Oggetto `NSCollectionView` viene visualizzata una griglia delle visualizzazioni secondarie organizzate mediante un `NSCollectionViewLayout`. Ogni visualizzazione secondaria nella griglia è rappresentato da un `NSCollectionViewItem` che gestisce il caricamento del contenuto della visualizzazione da un `.xib` file.

[![Eseguire un'app di esempio](collection-view-images/intro01.png)](collection-view-images/intro01.png#lightbox)

Questo articolo illustra le nozioni di base dell'utilizzo delle visualizzazioni raccolta in un'app xamarin. Mac. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare le [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) le sezioni, perché illustra i concetti chiave e le tecniche utilizzate in questo articolo.

È possibile ottenere un quadro il [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) del documento, viene spiegato il `Register` e `Export` comandi utilizzato per wireup classi c# per gli oggetti di Objective-C e gli elementi dell'interfaccia utente.

<a name="About_Collection_Views"/>

## <a name="about-collection-views"></a>Informazioni sulle viste di raccolta

L'obiettivo principale di una visualizzazione di raccolta (`NSCollectionView`) consiste nell'organizzare visivamente un gruppo di oggetti in modo organizzato utilizzando un Layout di visualizzazione di raccolta (`NSCollectionViewLayout`), con ogni singolo oggetto (`NSCollectionViewItem`) getting la propria visualizzazione nella raccolta di dimensioni maggiori. Usare le visualizzazioni di raccolta tramite tecniche di Data Binding e codifica di chiave-valore e di conseguenza, è consigliabile leggere il [Data Binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md) documentazione prima di continuare con questo articolo.

La vista raccolta non dispone di alcun standard e incorporati visualizzazione elemento della raccolta (ad esempio, una struttura o una tabella vista non), in modo che lo sviluppatore è responsabile della progettazione e implementazione di un _prototipo visualizzazione_ usando altri controlli AppKit, ad esempio campi immagine , I campi di testo, le etichette e così via. In questa vista di prototipo verranno usata per visualizzare e lavorare con ogni elemento viene gestito mediante la visualizzazione di raccolta e viene archiviata in un `.xib` file.

Perché lo sviluppatore è responsabile per l'aspetto di un elemento di visualizzazione di raccolta, la vista raccolta non dispone di alcun supporto predefinito per l'evidenziazione di un elemento selezionato nella griglia. Implementazione di questa funzionalità verrà trattato in questo articolo.

<a name="Defining_your_Data_Model"/>

## <a name="defining-the-data-model"></a>La definizione del modello di dati

Prima di Data Binding di una visualizzazione di raccolta in Interface Builder, una chiave-valore di codifica (i KVM) / classe conforme chiave-valore osservando (KVO) deve essere definito nell'app per xamarin. Mac per agire come le _modello di dati_ per l'associazione. Il modello di dati fornisce tutti i dati che verranno visualizzati nella raccolta e riceve le modifiche ai dati apportate dall'utente nell'interfaccia utente durante l'esecuzione dell'applicazione.

Eseguire l'esempio di un'app che gestisce un gruppo di dipendenti, la classe seguente può essere usata per definire il modello di dati:

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

Il `PersonModel` verrà utilizzato il modello di dati nella parte restante di questo articolo.

<a name="Working_with_a_Collection_View"/>

## <a name="working-with-a-collection-view"></a>Uso di una visualizzazione di raccolta

Data Binding con una visualizzazione di raccolta è molto simile associazione like con una visualizzazione tabella, come `NSCollectionViewDataSource` viene utilizzato per fornire dati per la raccolta. Poiché la visualizzazione di raccolta non ha un formato di visualizzazione di set di impostazioni, è necessario fornire commenti e suggerimenti l'interazione dell'utente e di tenere traccia di selezione dell'utente più lavoro.

<a name="Creating-the-Cell-Prototype"/>

### <a name="creating-the-cell-prototype"></a>Creazione del prototipo di cella

Poiché la visualizzazione di raccolta non contiene un prototipo di cella predefinito, lo sviluppatore dovrà aggiungere uno o più `.xib` file all'app xamarin. Mac per definire il layout e il contenuto delle celle singole.

Seguire questa procedura:

1. Nel **Esplora soluzioni**, fare doppio clic sul nome del progetto e selezionare **Add** > **nuovo File...**
2. Selezionare **Mac** > **View Controller**, assegnargli un nome (ad esempio `EmployeeItem` in questo esempio) e fare clic sul **New** pulsante per la creazione: 

    ![Aggiunge un nuovo controller di visualizzazione](collection-view-images/proto01.png)

    Verrà aggiunta un' `EmployeeItem.cs`, `EmployeeItemController.cs` e `EmployeeItemController.xib` file alla soluzione del progetto.
3. Fare doppio clic il `EmployeeItemController.xib` file per aprirlo e modificarlo in Interface Builder di Xcode.
4. Aggiungere un `NSBox`, `NSImageView` e due `NSLabel` controlli alla visualizzazione e disporli nel modo seguente:

    ![Progettazione del layout del prototipo della cella](collection-view-images/proto02.png)
5. Aprire il **Assistente dell'Editor** e creare un **Outlet** per il `NSBox` in modo che può essere utilizzato per indicare lo stato di selezione di una cella:

    ![Esposizione NSBox in un Outlet](collection-view-images/proto03.png)
6. Tornare al **Editor Standard** e selezionare la visualizzazione di immagini.
7. Nel **Binding Inspector**, selezionare **associa a** > **proprietario del File** e immettere un **percorso della chiave del modello** di `self.Person.Icon`:

    ![L'icona di associazione](collection-view-images/proto04.png)
8. Selezionare la prima etichetta e nel **Binding Inspector**, selezionare **associa a** > **proprietario del File** e immettere un **percorso della chiave del modello**di `self.Person.Name`:

    ![Il nome dell'associazione](collection-view-images/proto05.png)
9. Selezionare la seconda etichetta e nel **Binding Inspector**, selezionare **associa a** > **proprietario del File** e immettere un **percorso della chiave del modello**di `self.Person.Occupation`:

    ![L'occupazione di associazione](collection-view-images/proto06.png)
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

Esaminando questo codice in modo dettagliato, la classe eredita da `NSCollectionViewItem` in modo che possa fungere da un prototipo per una cella di visualizzazione raccolta. Il `Person` proprietà espone la classe usata per associare i dati per la visualizzazione di immagini e le etichette in Xcode. Si tratta di un'istanza di `PersonModel` creato in precedenza.

Il `BackgroundColor` proprietà è un collegamento per il `NSBox` del controllo `FillColor` che verrà utilizzato per visualizzare lo stato di selezione di una cella. Eseguendo l'override di `Selected` proprietà del `NSCollectionViewItem`, il codice seguente imposta o Cancella lo stato di selezione:

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

### <a name="creating-the-collection-view-data-source"></a>Creazione dell'origine dati di visualizzazione raccolta

Un'origine dati di visualizzazione raccolta (`NSCollectionViewDataSource`) fornisce tutti i dati per una visualizzazione di raccolta e crea e popola una cella di visualizzazione di raccolta (usando il `.xib` prototipo) come richiesto per ogni elemento nella raccolta.

Aggiungere una nuova classe il progetto, denominarlo `CollectionViewDataSource` e renderlo simile al seguente:

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

Esaminando questo codice in modo dettagliato, la classe eredita da `NSCollectionViewDataSource` ed espone un elenco delle `PersonModel` istanze tramite relativo `Data` proprietà.

Poiché questa raccolta ha solo una sezione, il codice esegue l'override di `GetNumberOfSections` metodo e restituisce sempre `1`. Inoltre, il `GetNumberofItems` è sottoposto a override in restituisce il numero di elementi nel `Data` elenco delle proprietà.

Il `GetItem` metodo viene chiamato ogni qualvolta una nuova cella è obbligatorio e ha un aspetto simile al seguente:

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

Il `MakeItem` della visualizzazione di raccolta viene chiamato per creare o restituire un'istanza riutilizzabile del `EmployeeItemController` e il relativo `Person` proprietà è impostata su elemento viene visualizzato nella cella richiesta. 

Il `EmployeeItemController` deve essere registrato con il Controller di visualizzazione di raccolta in anticipo usando il codice seguente:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

Il **Identifier** (`EmployeeCell`) usati nel `MakeItem` chiamare _deve_ corrisponde al nome del Controller di visualizzazione che è stato registrato con la visualizzazione di raccolta. Questo passaggio verrà trattato in dettaglio di seguito.

<a name="Handling-Item-Selection"/>

### <a name="handling-item-selection"></a>Selezione di elementi di gestione

Per gestire la selezione e la deselezione di elementi nella raccolta, un `NSCollectionViewDelegate` sarà necessaria. Poiché in questo esempio sarà necessario usare incorporato in `NSCollectionViewFlowLayout` tipo di layout, un `NSCollectionViewDelegateFlowLayout` sarà necessaria la versione specifica di questo delegato.

Aggiungere una nuova classe al progetto, denominarlo `CollectionViewDelegate` e renderlo simile al seguente:

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

Il `ItemsSelected` e `ItemsDeselected` metodi sono sottoposto a override e utilizzati per impostare o cancellare il `PersonSelected` proprietà del Controller di visualizzazione che gestisce la visualizzazione di raccolta quando l'utente seleziona o deseleziona un elemento. Questa informazione verrà visualizzata in dettaglio di seguito.

<a name="Creating-the-Collection-View-in-Interface-Builder"/>

### <a name="creating-the-collection-view-in-interface-builder"></a>Creazione della visualizzazione di raccolta in Interface Builder

Con tutte le parti di supporto necessarie nella posizione, lo storyboard principale può essere modificato e aggiungervi una visualizzazione di raccolta.

Seguire questa procedura:

1. Fare doppio clic il `Main.Storyboard` del file nei **Esplora soluzioni** aprirlo e modificarlo in Xcode Interface Builder di.
2. Trascinare una visualizzazione di raccolta nella vista principale e ridimensionarla per riempire la vista:

    ![Aggiunta di una vista di raccolta per il layout](collection-view-images/collection01.png)
3. Con la visualizzazione di raccolta selezionato, usare l'Editor di vincolo per aggiungerla alla vista quando viene ridimensionato:

    ![Aggiunta di vincoli](collection-view-images/collection02.png)
4. Verificare che sia selezionata la visualizzazione di raccolta nel **nell'area di progettazione** (e non il **bordo visualizzazione a scorrimento** o **visualizzazione Clip** che lo contiene), passare al  **Assistente dell'Editor** e creare un' **Outlet** per la visualizzazione di raccolta:

    ![Aggiunta di vincoli](collection-view-images/collection03.png)
5. Salvare le modifiche e tornare a Visual Studio per la sincronizzazione.

<a name="Bringing-it-all-Together"/>

## <a name="bringing-it-all-together"></a>Importarli tutti insieme

Tutti i componenti di supporti ora inseriti nella posizione corretta con una classe da usare come modello di dati (`PersonModel`), una `NSCollectionViewDataSource` è stato aggiunto per fornire i dati, un `NSCollectionViewDelegateFlowLayout` è stato creato per gestire la selezione di elementi e un `NSCollectionView` è stato aggiunto allo Storyboard principale ed esposte come un Outlet (`EmployeeCollection`).

Il passaggio finale consiste per modificare il Controller di visualizzazione che contiene la visualizzazione di raccolta e visualizzare tutte le parti interagiscono per popolare la raccolta e gestire la selezione di elementi.

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

Osservare il codice in modo dettagliato, un `Datasource` proprietà viene definita per contenere un'istanza del `CollectionViewDataSource` che fornirà i dati per la visualizzazione di raccolta. Oggetto `PersonSelected` proprietà viene definita per contenere il `PersonModel` che rappresenta l'elemento attualmente selezionato nella visualizzazione raccolta. Questa proprietà genera inoltre il `SelectionChanged` evento quando cambia la selezione.

Il `ConfigureCollectionView` classe viene utilizzata per registrare il Controller di visualizzazione che agisce come il prototipo di cella con la visualizzazione di raccolta usando la riga seguente:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

Si noti che il **identificatore** (`EmployeeCell`) consente di registrare le corrispondenze di prototipo quello chiamato nel `GetItem` metodo del `CollectionViewDataSource` definiti in precedenza:

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

Inoltre, il tipo di Controller di visualizzazione **devono** corrispondere al nome del `.xib` file che definisce il prototipo **esattamente**. In questo esempio `EmployeeItemController` e `EmployeeItemController.xib`.

Il layout effettivo degli elementi nella visualizzazione dell'insieme è controllato da una classe di Layout di visualizzazione di raccolta e possono essere modificato dinamicamente in fase di esecuzione tramite l'assegnazione di una nuova istanza per il `CollectionViewLayout` proprietà. Se si modifica questa proprietà Aggiorna l'aspetto della visualizzazione di raccolta senza l'animazione della modifica.

Apple include due tipi di layout predefinite con la visualizzazione di raccolta che gestirà impieghi più comuni: `NSCollectionViewFlowLayout` e `NSCollectionViewGridLayout`. Se lo sviluppatore è richiesto un formato personalizzato, ad esempio, che stabilisce gli elementi in un cerchio, possono creare un'istanza personalizzata di `NSCollectionViewLayout` ed eseguire l'override di metodi necessari per ottenere l'effetto desiderato.

Questo esempio viene usato il layout di flusso predefinito crea un'istanza di `NSCollectionViewFlowLayout` classe e lo configura come indicato di seguito:

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

Il `ItemSize` proprietà definisce la dimensione delle singole celle nell'insieme. Il `SectionInset` proprietà definisce gli elementi interni tra il bordo della raccolta che verranno disposte le celle in. `MinimumInteritemSpacing` definisce la spaziatura minima tra gli elementi e `MinimumLineSpacing` definisce la spaziatura minima tra le righe nella raccolta.

Il layout viene assegnato a un'istanza e la visualizzazione di raccolta di `CollectionViewDelegate` collegata per gestire la selezione di elementi:

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

Il `PopulateWithData` metodo crea una nuova istanza della `CollectionViewDataSource`, lo popola con i dati, lo collega alla visualizzazione di raccolta e chiama il `ReloadData` metodo per visualizzare gli elementi:

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

Il `ViewDidLoad` metodo viene sottoposto a override e chiama le `ConfigureCollectionView` e `PopulateWithData` metodi per visualizzare la visualizzazione di raccolta finale per l'utente:

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

Questo articolo ha fatto un quadro dettagliato di utilizzo delle visualizzazioni raccolta in un'applicazione xamarin. Mac. In primo luogo, esaminata l'esposizione di una classe c# Objective-c con dati XML di codifica (i, chiave-valore KVM) e chiave-valore osservando (KVO). Successivamente, è stato illustrato come usare una classe conforme KVO e dati associarlo alle visualizzazioni di raccolta in Interface Builder di Xcode. Infine, è stato illustrato come interagire con le visualizzazioni di raccolta nel codice c#.

## <a name="related-links"></a>Collegamenti correlati

- [MacCollectionNew (esempio)](https://developer.xamarin.com/samples/mac/MacCollectionNew/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
