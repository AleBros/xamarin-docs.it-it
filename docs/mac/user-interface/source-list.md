---
title: Elenchi di origine in Novell. Mac
description: Questo articolo illustra l'uso degli elenchi di origine in un'applicazione Novell. Mac. Viene descritto come creare e gestire gli elenchi di origine in Xcode e Interface Builder e interagire con C# essi nel codice.
ms.prod: xamarin
ms.assetid: 651A3649-5AA8-4133-94D6-4873D99F7FCC
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 499edcb1420b311c519f1665b4d2effd9088e9e7
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065385"
---
# <a name="source-lists-in-xamarinmac"></a>Elenchi di origine in Novell. Mac

_Questo articolo illustra l'uso degli elenchi di origine in un'applicazione Novell. Mac. Viene descritto come creare e gestire gli elenchi di origine in Xcode e Interface Builder e interagire con C# essi nel codice._

Quando si lavora C# con e .NET in un'applicazione Novell. Mac, è possibile accedere agli stessi elenchi di origine che uno sviluppatore utilizza in *Objective-C* e *Xcode* . Poiché Novell. Mac si integra direttamente con Xcode, è possibile usare _Interface Builder_ di Xcode per creare e gestire gli elenchi di origine (oppure, facoltativamente, crearli direttamente nel C# codice).

Un elenco di origine è un tipo speciale di visualizzazione struttura usata per visualizzare l'origine di un'azione, ad esempio la barra laterale in Finder o iTunes.

[![](source-list-images/source05.png "Un elenco di origine di esempio")](source-list-images/source05.png#lightbox)

In questo articolo verranno illustrate le nozioni di base relative all'uso degli elenchi di origine in un'applicazione Novell. Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti chiave e le tecniche che verranno usati in Questo articolo.

Si consiglia di esaminare la sezione [ C# esporre classi/metodi in Objective-C](~/mac/internals/how-it-works.md) del documento [interno di Novell. Mac](~/mac/internals/how-it-works.md) , spiegando `Register` i comandi e `Export` usati per collegare le C# classi a. Oggetti Objective-C ed elementi dell'interfaccia utente.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-source-lists"></a>Introduzione agli elenchi di origine

Come indicato in precedenza, un elenco di origine è un tipo speciale di visualizzazione struttura usata per visualizzare l'origine di un'azione, ad esempio la barra laterale in Finder o iTunes. Un elenco di origine è un tipo di tabella che consente all'utente di espandere o comprimere righe di dati gerarchici. Diversamente da una vista tabella, gli elementi in un elenco di origine non sono in un elenco semplice, sono organizzati in una gerarchia, ad esempio file e cartelle su un disco rigido. Se un elemento in un elenco di origine contiene altri elementi, può essere espanso o compresso dall'utente.

L'elenco di origine è una visualizzazione struttura con stile speciale`NSOutlineView`(), che a sua volta è una sottoclasse della`NSTableView`visualizzazione tabella () e, di conseguenza, eredita gran parte del suo comportamento dalla relativa classe padre. Di conseguenza, molte operazioni supportate da una visualizzazione struttura sono supportate anche da un elenco di origine. Un'applicazione Novell. Mac ha il controllo di queste funzionalità e può configurare i parametri dell'elenco di origine (nel codice o in Interface Builder) per consentire o impedire determinate operazioni.

Un elenco di origine non archivia i propri dati, ma si basa su un'origine dati (`NSOutlineViewDataSource`) per fornire le righe e le colonne necessarie, in base alle esigenze.

È possibile personalizzare il comportamento di un elenco di origine fornendo una sottoclasse del delegato della visualizzazione`NSOutlineViewDelegate`struttura () per supportare il tipo di struttura per selezionare la funzionalità, la selezione e la modifica degli elementi, il rilevamento personalizzato e le visualizzazioni personalizzate per i singoli elementi.

Poiché un elenco di origine condivide gran parte del comportamento e delle funzionalità con una visualizzazione tabella e una visualizzazione struttura, è possibile esaminare la documentazione delle [visualizzazioni di tabella](~/mac/user-interface/table-view.md) e delle [visualizzazioni struttura](~/mac/user-interface/outline-view.md) prima di continuare con questo articolo.

<a name="Working_with_Source_Lists" />

## <a name="working-with-source-lists"></a>Utilizzo degli elenchi di origine

Un elenco di origine è un tipo speciale di visualizzazione struttura usata per visualizzare l'origine di un'azione, ad esempio la barra laterale in Finder o iTunes. A differenza delle visualizzazioni struttura, prima di definire l'elenco di origine in Interface Builder, è ora di creare le classi di supporto in Novell. Mac.

Prima di tutto, creare una nuova `SourceListItem` classe per conservare i dati per l'elenco di origine. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **nuovo file...** Selezionare > **classe vuota**generale, immettere `SourceListItem` come **nome** e fare clic sul pulsante **nuovo** :

[![](source-list-images/source01.png "Aggiunta di una classe vuota")](source-list-images/source01.png#lightbox)

Fare in `SourceListItem.cs` modo che il file abbia un aspetto simile al seguente: 

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListItem: NSObject, IEnumerator, IEnumerable
    {
        #region Private Properties
        private string _title;
        private NSImage _icon;
        private string _tag;
        private List<SourceListItem> _items = new List<SourceListItem> ();
        #endregion

        #region Computed Properties
        public string Title {
            get { return _title; }
            set { _title = value; }
        }

        public NSImage Icon {
            get { return _icon; }
            set { _icon = value; }
        }

        public string Tag {
            get { return _tag; }
            set { _tag=value; }
        }
        #endregion

        #region Indexer
        public SourceListItem this[int index]
        {
            get
            {
                return _items[index];
            }

            set
            {
                _items[index] = value;
            }
        }

        public int Count {
            get { return _items.Count; }
        }

        public bool HasChildren {
            get { return (Count > 0); }
        }
        #endregion

        #region Enumerable Routines
        private int _position = -1;

        public IEnumerator GetEnumerator()
        {
            _position = -1;
            return (IEnumerator)this;
        }

        public bool MoveNext()
        {
            _position++;
            return (_position < _items.Count);
        }

        public void Reset()
        {_position = -1;}

        public object Current
        {
            get 
            { 
                try 
                {
                    return _items[_position];
                }

                catch (IndexOutOfRangeException)
                {
                    throw new InvalidOperationException();
                }
            }
        }
        #endregion

        #region Constructors
        public SourceListItem ()
        {
        }

        public SourceListItem (string title)
        {
            // Initialize
            this._title = title;
        }

        public SourceListItem (string title, string icon)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
        }

        public SourceListItem (string title, string icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
        }

        public SourceListItem (string title, NSImage icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon, string tag)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
        }

        public SourceListItem (string title, NSImage icon, string tag, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
            this.Clicked = clicked;
        }
        #endregion

        #region Public Methods
        public void AddItem(SourceListItem item) {
            _items.Add (item);
        }

        public void AddItem(string title) {
            _items.Add (new SourceListItem (title));
        }

        public void AddItem(string title, string icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, string icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, NSImage icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon, string tag) {
            _items.Add (new SourceListItem (title, icon, tag));
        }

        public void AddItem(string title, NSImage icon, string tag, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, tag, clicked));
        }

        public void Insert(int n, SourceListItem item) {
            _items.Insert (n, item);
        }

        public void RemoveItem(SourceListItem item) {
            _items.Remove (item);
        }

        public void RemoveItem(int n) {
            _items.RemoveAt (n);
        }

        public void Clear() {
            _items.Clear ();
        }
        #endregion

        #region Events
        public delegate void ClickedDelegate();
        public event ClickedDelegate Clicked;

        internal void RaiseClickedEvent() {
            // Inform caller
            if (this.Clicked != null)
                this.Clicked ();
        }
        #endregion
    }
}
```

Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **nuovo file...** Selezionare > **classe vuota**generale, immettere `SourceListDataSource` come **nome** e fare clic sul pulsante **nuovo** . Fare in `SourceListDataSource.cs` modo che il file abbia un aspetto simile al seguente:

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDataSource : NSOutlineViewDataSource
    {
        #region Private Variables
        private SourceListView _controller;
        #endregion

        #region Public Variables
        public List<SourceListItem> Items = new List<SourceListItem>();
        #endregion

        #region Constructors
        public SourceListDataSource (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Properties
        public override nint GetChildrenCount (NSOutlineView outlineView, Foundation.NSObject item)
        {
            if (item == null) {
                return Items.Count;
            } else {
                return ((SourceListItem)item).Count;
            }
        }

        public override bool ItemExpandable (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, Foundation.NSObject item)
        {
            if (item == null) {
                return Items [(int)childIndex];
            } else {
                return ((SourceListItem)item) [(int)childIndex]; 
            }
        }

        public override NSObject GetObjectValue (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            return new NSString (((SourceListItem)item).Title);
        }
        #endregion

        #region Internal Methods
        internal SourceListItem ItemForRow(int row) {
            int index = 0;

            // Look at each group
            foreach (SourceListItem item in Items) {
                // Is the row inside this group?
                if (row >= index && row <= (index + item.Count)) {
                    return item [row - index - 1];
                }

                // Move index
                index += item.Count + 1;
            }

            // Not found 
            return null;
        }
        #endregion
    }
}
```

Questa operazione fornirà i dati per l'elenco di origine.

Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **nuovo file...** Selezionare > **classe vuota**generale, immettere `SourceListDelegate` come **nome** e fare clic sul pulsante **nuovo** . Fare in `SourceListDelegate.cs` modo che il file abbia un aspetto simile al seguente:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDelegate : NSOutlineViewDelegate
    {
        #region Private variables
        private SourceListView _controller;
        #endregion

        #region Constructors
        public SourceListDelegate (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Methods
        public override bool ShouldEditTableColumn (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            return false;
        }

        public override NSCell GetCell (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            nint row = outlineView.RowForItem (item);
            return tableColumn.DataCellForRow (row);
        }

        public override bool IsGroupItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            NSTableCellView view = null;

            // Is this a group item?
            if (((SourceListItem)item).HasChildren) {
                view = (NSTableCellView)outlineView.MakeView ("HeaderCell", this);
            } else {
                view = (NSTableCellView)outlineView.MakeView ("DataCell", this);
                view.ImageView.Image = ((SourceListItem)item).Icon;
            }

            // Initialize view
            view.TextField.StringValue = ((SourceListItem)item).Title;

            // Return new view
            return view;
        }

        public override bool ShouldSelectItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return (outlineView.GetParent (item) != null);
        }

        public override void SelectionDidChange (NSNotification notification)
        {
            NSIndexSet selectedIndexes = _controller.SelectedRows;

            // More than one item selected?
            if (selectedIndexes.Count > 1) {
                // Not handling this case
            } else {
                // Grab the item
                var item = _controller.Data.ItemForRow ((int)selectedIndexes.FirstIndex);

                // Was an item found?
                if (item != null) {
                    // Fire the clicked event for the item
                    item.RaiseClickedEvent ();

                    // Inform caller of selection
                    _controller.RaiseItemSelected (item);
                }
            }
        }
        #endregion
    }
}
```

In questo modo verrà fornito il comportamento dell'elenco di origine.

Infine, nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **nuovo file...** Selezionare > **classe vuota**generale, immettere `SourceListView` come **nome** e fare clic sul pulsante **nuovo** . Fare in `SourceListView.cs` modo che il file abbia un aspetto simile al seguente:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }
        
        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

In questo modo viene creata una sottoclasse personalizzata `NSOutlineView` riutilizzabile di (`SourceListView`) che è possibile usare per guidare l'elenco di origine in qualsiasi applicazione Novell. Mac.

<a name="Creating_and_Maintaining_Source_Lists_in_Xcode" />

## <a name="creating-and-maintaining-source-lists-in-xcode"></a>Creazione e gestione degli elenchi di origine in Xcode

A questo punto, progettare l'elenco di origine in Interface Builder. Fare doppio clic sul `Main.storyboard` file per aprirlo per la modifica in Interface Builder e trascinare una visualizzazione divisa da **libreria Inspector**, aggiungerla al controller di visualizzazione e impostarla in modo da ridimensionarla con la visualizzazione nell' **Editor vincoli**:

[![](source-list-images/source00.png "Modifica di vincoli")](source-list-images/source00.png#lightbox)

Trascinare quindi un elenco di origine da **libreria Inspector**, aggiungerlo al lato sinistro della visualizzazione divisa e impostarlo in modo che venga ridimensionato con la visualizzazione nell' **editor di vincoli**:

[![](source-list-images/source02.png "Modifica di vincoli")](source-list-images/source02.png#lightbox)

Passare quindi alla **visualizzazione Identity**, selezionare l'elenco di origine e modificare la **classe** in `SourceListView`:

[![](source-list-images/source03.png "Impostazione del nome della classe")](source-list-images/source03.png#lightbox)

Infine, creare un **Outlet** per l'elenco di origine `SourceList` denominato nel `ViewController.h` file:

[![](source-list-images/source04.png "Configurazione di un Outlet")](source-list-images/source04.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

<a name="Populating the Source List" />

## <a name="populating-the-source-list"></a>Popolamento dell'elenco di origine

Modificare il `RotationWindow.cs` file in Visual Studio per Mac e fare in `AwakeFromNib` modo che il metodo sia simile al seguente:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Populate source list
    SourceList.Initialize ();

    var library = new SourceListItem ("Library");
    library.AddItem ("Venues", "house.png", () => {
        Console.WriteLine("Venue Selected");
    });
    library.AddItem ("Singers", "group.png");
    library.AddItem ("Genre", "cards.png");
    library.AddItem ("Publishers", "box.png");
    library.AddItem ("Artist", "person.png");
    library.AddItem ("Music", "album.png");
    SourceList.AddItem (library);

    // Add Rotation 
    var rotation = new SourceListItem ("Rotation"); 
    rotation.AddItem ("View Rotation", "redo.png");
    SourceList.AddItem (rotation);

    // Add Kiosks
    var kiosks = new SourceListItem ("Kiosks");
    kiosks.AddItem ("Sign-in Station 1", "imac");
    kiosks.AddItem ("Sign-in Station 2", "ipad");
    SourceList.AddItem (kiosks);

    // Display side list
    SourceList.ReloadData ();
    SourceList.ExpandItem (null, true);

}
```

Il `Initialize ()` metodo deve essere chiamato sull'Outlet dell'elenco di origine _prima_ di aggiungervi elementi. Per ogni gruppo di elementi, si crea un elemento padre e quindi si aggiungono gli elementi secondari all'elemento del gruppo. Ogni gruppo viene quindi aggiunto alla raccolta `SourceList.AddItem (...)`dell'elenco di origine. Le ultime due righe caricano i dati per l'elenco di origine ed espande tutti i gruppi:

```csharp
// Display side list
SourceList.ReloadData ();
SourceList.ExpandItem (null, true);
```

Infine, modificare il `AppDelegate.cs` file e fare in `DidFinishLaunching` modo che il metodo sia simile al seguente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    var rotation = new RotationWindowController ();
    rotation.Window.MakeKeyAndOrderFront (this);
}
```

Se si esegue l'applicazione, verranno visualizzati gli elementi seguenti:

[![](source-list-images/source05.png "Esecuzione di un'app di esempio")](source-list-images/source05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato in dettaglio l'uso degli elenchi di origine in un'applicazione Novell. Mac. È stato illustrato come creare e gestire elenchi di origine in Interface Builder di Xcode e come usare gli elenchi di origine C# nel codice.

## <a name="related-links"></a>Collegamenti correlati

- [MacOutlines (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/macoutlines)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Table Views](~/mac/user-interface/table-view.md) (Visualizzazioni tabelle)
- [Outline Views](~/mac/user-interface/outline-view.md) (Visualizzazioni struttura)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione alle visualizzazioni struttura](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
