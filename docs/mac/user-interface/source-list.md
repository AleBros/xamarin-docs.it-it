---
title: Elenchi di origine in xamarin. Mac
description: Questo articolo descrive come usare gli elenchi di origine in un'applicazione xamarin. Mac. Descrive la creazione e la gestione di elenchi di origine in Xcode e Interface Builder e sull'interazione con essi nel codice c#.
ms.prod: xamarin
ms.assetid: 651A3649-5AA8-4133-94D6-4873D99F7FCC
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 82e4dfb9add7002fd7d3568d0ec946ea38dfd530
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526408"
---
# <a name="source-lists-in-xamarinmac"></a>Elenchi di origine in xamarin. Mac

_Questo articolo descrive come usare gli elenchi di origine in un'applicazione xamarin. Mac. Descrive la creazione e la gestione di elenchi di origine in Xcode e Interface Builder e sull'interazione con essi nel codice c#._

Quando si lavora con C# e .NET in un'applicazione xamarin. Mac, è possibile utilizzare la stessa origine che elenca gli sviluppatori che lavorano *Objective-C* e *Xcode* viene. Poiché xamarin. Mac si integra direttamente con Xcode, è possibile usare Xcode _Interface Builder_ per creare e gestire gli elenchi di origine (oppure crearle direttamente nel codice c#).

Un elenco di origine è un tipo speciale della visualizzazione struttura consente di visualizzare l'origine di un'azione, ad esempio la barra laterale in Finder o iTunes.

[![](source-list-images/source05.png "Un elenco di origine di esempio")](source-list-images/source05.png#lightbox)

In questo articolo, si affronterà le nozioni di base dell'uso degli elenchi di origine in un'applicazione xamarin. Mac. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare le [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) le sezioni, perché illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile ottenere un quadro il [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) del documento, viene spiegato il `Register` e `Export` comandi utilizzato per wireup classi c# per gli oggetti di Objective-C e gli elementi dell'interfaccia utente.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-source-lists"></a>Introduzione agli elenchi di origine

Come indicato in precedenza, un elenco di origine è che un tipo speciale della visualizzazione struttura consente di visualizzare l'origine di un'azione, ad esempio la barra laterale in Finder o iTunes. Un elenco di origine è un tipo di tabella che consente all'utente di espandere o comprimere le righe di dati gerarchici. A differenza di una visualizzazione tabella, gli elementi in un elenco di origine non sono in un elenco semplice, sono organizzati in una gerarchia, ad esempio file e cartelle su un disco rigido. Se un elemento in un elenco di origine contiene altri elementi, possono essere espansi o compressi dall'utente.

L'elenco di origine è una visualizzazione in stile in modo speciale (`NSOutlineView`), che a sua volta è una sottoclasse della visualizzazione della tabella (`NSTableView`) e di conseguenza, gran parte del suo comportamento eredita dalla classe padre. Di conseguenza, molte operazioni supportate da una visualizzazione struttura, sono supportati anche da un elenco di origine. Un'applicazione xamarin. Mac ha il controllo di queste funzionalità ed è possibile configurare i parametri dell'elenco di origine (sia nel codice o Interface Builder) per consentire o impedire determinate operazioni.

Un elenco di origine non archivia i propri dati, ma si basa su un'origine dati (`NSOutlineViewDataSource`) per fornire sia le righe e colonne necessari, su una base alle esigenze.

Comportamento dell'elenco di origine può essere personalizzato specificando una sottoclasse del delegato di visualizzazione di struttura (`NSOutlineViewDelegate`) per supportare il tipo di struttura per selezionare funzionalità, elemento di selezione e modifica, rilevamento personalizzati e visualizzazioni personalizzate per i singoli elementi.

Poiché un elenco di origine condivida molte funzionalità e il suo comportamento con una visualizzazione tabella e una visualizzazione della struttura, è possibile passare attraverso il [viste delle tabelle](~/mac/user-interface/table-view.md) e [la visualizzazione della struttura](~/mac/user-interface/outline-view.md) documentazione prima di continuare In questo articolo.

<a name="Working_with_Source_Lists" />

## <a name="working-with-source-lists"></a>Utilizzo degli elenchi di origine

Un elenco di origine è un tipo speciale della visualizzazione struttura consente di visualizzare l'origine di un'azione, ad esempio la barra laterale in Finder o iTunes. A differenza delle visualizzazioni della struttura, prima si definisce l'elenco di origine in Interface Builder, è possibile creare le classi di supporto in xamarin. Mac.

In primo luogo, è possibile creare un nuovo `SourceListItem` classe per contenere i dati per l'elenco di origine. Nel **Esplora soluzioni**, fare clic sul progetto e selezionare **Add** > **nuovo File...** Selezionare **generali** > **classe vuota**, immettere `SourceListItem` per il **nome** e fare clic su di **New** pulsante:

[![](source-list-images/source01.png "Aggiunta di una classe vuota")](source-list-images/source01.png#lightbox)

Rendere il `SourceListItem.cs` file aspetto simile al seguente: 

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

Nel **Esplora soluzioni**, fare clic sul progetto e selezionare **Add** > **nuovo File...** Selezionare **generali** > **classe vuota**, immettere `SourceListDataSource` per il **nome** e fare clic su di **New** pulsante. Rendere il `SourceListDataSource.cs` file aspetto simile al seguente:

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

Questo fornirà i dati per l'elenco di origine.

Nel **Esplora soluzioni**, fare clic sul progetto e selezionare **Add** > **nuovo File...** Selezionare **generali** > **classe vuota**, immettere `SourceListDelegate` per il **nome** e fare clic su di **New** pulsante. Rendere il `SourceListDelegate.cs` file aspetto simile al seguente:

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

Ciò fornirà il comportamento del nostro elenco di origine.

Infine, nella **Esplora soluzioni**, fare clic sul progetto e selezionare **Add** > **nuovo File...** Selezionare **generali** > **classe vuota**, immettere `SourceListView` per il **nome** e fare clic su di **New** pulsante. Rendere il `SourceListView.cs` file aspetto simile al seguente:

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

Verrà creata una sottoclasse riutilizzabile e personalizzata di `NSOutlineView` (`SourceListView`) che è possibile usare per gestire l'elenco di origine in qualsiasi applicazione xamarin. Mac che commettiamo.

<a name="Creating_and_Maintaining_Source_Lists_in_Xcode" />

## <a name="creating-and-maintaining-source-lists-in-xcode"></a>Creare e gestire gli elenchi di origine in Xcode

A questo punto, è possibile progettare l'elenco di origine in Interface Builder. Fare doppio clic il `Main.storyboard` file per aprirlo e modificarlo in Interface Builder e trascinare una doppia visualizzazione dal **Library Inspector**, aggiungerlo al Controller di visualizzazione e impostarlo per ridimensionare con la visualizzazione nel **Editor vincoli di** :

[![](source-list-images/source00.png "Modifica di vincoli")](source-list-images/source00.png#lightbox)

Trascinare quindi un elenco di origine dal **Library Inspector**, aggiungerlo al lato sinistro della visualizzazione suddivisa e impostarlo per ridimensionare con la visualizzazione nel **Editor vincoli di**:

[![](source-list-images/source02.png "Modifica di vincoli")](source-list-images/source02.png#lightbox)

Successivamente, passare al **visualizzazione di identità**, selezionare l'elenco di origine e modificarlo del **classe** a `SourceListView`:

[![](source-list-images/source03.png "Impostazione del nome di classe")](source-list-images/source03.png#lightbox)

Infine, creare un **Outlet** per l'elenco di origine denominata `SourceList` nel `ViewController.h` file:

[![](source-list-images/source04.png "Configurazione di un Outlet")](source-list-images/source04.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

<a name="Populating the Source List" />

## <a name="populating-the-source-list"></a>Popolamento dell'elenco di origine

È possibile modificare il `RotationWindow.cs` del file in Visual Studio per Mac e renderlo del `AwakeFromNib` metodo aspetto simile al seguente:

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

Il `Initialize ()` metodo deve essere chiamato per l'elenco di origine **Outlet** _prima_ tutti gli elementi vengono aggiunti a esso. Per ogni gruppo di elementi, verrà creato un elemento padre e quindi aggiungere gli elementi secondari a tale elemento di gruppo. Ogni gruppo viene quindi aggiunto alla raccolta dell'elenco origine `SourceList.AddItem (...)`. Le ultime due righe caricare i dati per l'elenco di origine e si espande tutti i gruppi:

```csharp
// Display side list
SourceList.ReloadData ();
SourceList.ExpandItem (null, true);
```

Infine, modificare il `AppDelegate.cs` file e apportare le `DidFinishLaunching` metodo aspetto simile al seguente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    var rotation = new RotationWindowController ();
    rotation.Window.MakeKeyAndOrderFront (this);
}
```

Se eseguiamo l'applicazione, di seguito verrà visualizzato:

[![](source-list-images/source05.png "Eseguire un'app di esempio")](source-list-images/source05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato di utilizzo degli elenchi di origine in un'applicazione xamarin. Mac. Abbiamo visto come creare e gestire gli elenchi di origine in Interface Builder di Xcode e su come usare gli elenchi di origine nel codice c#.

## <a name="related-links"></a>Collegamenti correlati

- [MacOutlines (esempio)](https://developer.xamarin.com/samples/mac/MacOutlines/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Table Views](~/mac/user-interface/table-view.md) (Visualizzazioni tabelle)
- [Outline Views](~/mac/user-interface/outline-view.md) (Visualizzazioni struttura)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a visualizzazioni della struttura](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [Elemento NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
