---
title: Controlli standard in Xamarin.Mac
description: Questo articolo illustra come usare i controlli AppKit standard, ad esempio pulsanti, etichette, campi di testo, caselle di controllo e controlli segmentati in un'applicazione Xamarin.Mac. Viene descritto come aggiungerli a un'interfaccia con Interface Builder e interagire con essi nel codice.
ms.prod: xamarin
ms.assetid: d2593883-d255-431f-9781-75f04d8cecea
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: fe887d837930ebc75fed0fb7c163a3f30ad83af9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008361"
---
# <a name="standard-controls-in-xamarinmac"></a>Controlli standard in Xamarin.Mac

_Questo articolo illustra come usare i controlli AppKit standard, ad esempio pulsanti, etichette, campi di testo, caselle di controllo e controlli segmentati in un'applicazione Xamarin.Mac. Viene descritto come aggiungerli a un'interfaccia con Interface Builder e interagire con essi nel codice._

Quando si lavora C# con e .NET in un'applicazione Xamarin.Mac, è possibile accedere agli stessi controlli AppKit che uno sviluppatore lavora in *Objective-C* e *Xcode* . Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile usare _Interface Builder_ di Xcode per creare e gestire i controlli AppKit (o, facoltativamente, crearli C# direttamente nel codice).

I controlli AppKit sono gli elementi dell'interfaccia utente usati per creare l'interfaccia utente dell'applicazione Xamarin.Mac. Sono costituiti da elementi quali pulsanti, etichette, campi di testo, caselle di controllo e controlli segmentati e generano azioni immediate o risultati visibili quando un utente li manipola.

[![](standard-controls-images/intro01.png "The example app main screen")](standard-controls-images/intro01.png#lightbox)

In questo articolo verranno illustrate le nozioni di base sull'uso dei controlli AppKit in un'applicazione Xamarin.Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti chiave e le tecniche che verranno usati in Questo articolo.

Si consiglia di esaminare la sezione [esporre C# classi/metodi in Objective-c](~/mac/internals/how-it-works.md) del documento [interno di Xamarin.Mac](~/mac/internals/how-it-works.md) , spiegando i comandi`Register`e`Export`usati per collegare le C# classi a Objective-c. oggetti ed elementi dell'interfaccia utente.

<a name="Introduction_to_Controls_and_Views" />

## <a name="introduction-to-controls-and-views"></a>Introduzione a controlli e visualizzazioni

macOS (precedentemente noto come Mac OS X) fornisce un set standard di controlli dell'interfaccia utente tramite il Framework AppKit. Sono costituiti da elementi quali pulsanti, etichette, campi di testo, caselle di controllo e controlli segmentati e generano azioni immediate o risultati visibili quando un utente li manipola.

Tutti i controlli AppKit hanno un aspetto standard e predefinito che sarà appropriato per la maggior parte degli usi, alcuni specificano un aspetto alternativo da usare in un'area della cornice della finestra o in un contesto di _effetto vivido_ , ad esempio in un'area dell'intestazione laterale o in un centro notifiche widget.

Apple suggerisce le linee guida seguenti quando si utilizzano i controlli AppKit:

- Evitare di combinare dimensioni dei controlli nella stessa visualizzazione.
- In generale, evitare il ridimensionamento verticale di controlli.
- Usare il tipo di carattere del sistema e le dimensioni del testo corrette all'interno di un controllo.
- Utilizzare la spaziatura corretta tra i controlli.

Per ulteriori informazioni, vedere la sezione [informazioni sui controlli e sulle visualizzazioni](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) delle [linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)di Apple.

<a name="Using_Controls_in_a_Window_Frame" />

### <a name="using-controls-in-a-window-frame"></a>Uso di controlli in una cornice di finestra

Sono presenti un subset di controlli AppKit che includono uno stile di visualizzazione che consente l'inclusione nell'area del frame di una finestra. Per un esempio, vedere la barra degli strumenti dell'app di posta elettronica:

[![](standard-controls-images/mailapp.png "A Mac Window frame")](standard-controls-images/mailapp.png#lightbox)

- **Pulsante con trama arrotondata** : `NSButton` con lo stile `NSTexturedRoundedBezelStyle`.
- **Controllo segmentato arrotondato a trama** : `NSSegmentedControl` con uno stile di `NSSegmentStyleTexturedRounded`.
- **Controllo segmentato arrotondato a trama** : `NSSegmentedControl` con uno stile di `NSSegmentStyleSeparated`.
- **Menu a comparsa con trama arrotondata** : `NSPopUpButton` con uno stile di `NSTexturedRoundedBezelStyle`.
- **Menu a discesa con trama arrotondata** : `NSPopUpButton` con uno stile di `NSTexturedRoundedBezelStyle`.
- **Barra di ricerca** : `NSSearchField`.

Apple suggerisce le linee guida seguenti quando si utilizzano i controlli AppKit in una cornice di finestra:

- Non usare stili di controllo specifici del frame della finestra nel corpo della finestra.
- Non usare i controlli o gli stili del corpo della finestra nella cornice della finestra.

Per ulteriori informazioni, vedere la sezione [informazioni sui controlli e sulle visualizzazioni](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) delle [linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)di Apple.

<a name="Creating_a_User_Interface_in_Interface_Builder" />

## <a name="creating-a-user-interface-in-interface-builder"></a>Creazione di un'interfaccia utente in Interface Builder

Quando si crea una nuova applicazione Xamarin.Mac Cocoa, per impostazione predefinita si ottiene una finestra vuota standard. Questa finestra è definita in un file di `.storyboard` incluso automaticamente nel progetto. Per modificare la progettazione di Windows, nella **Esplora soluzioni**fare doppio clic sul file `Main.storyboard`:

[![](standard-controls-images/edit01.png "Selecting the Main Storyboard in the Solution Explorer")](standard-controls-images/edit01.png#lightbox)

Verrà aperta la progettazione della finestra nella Interface Builder di Xcode:

[![](standard-controls-images/edit02.png "Editing the storyboard in Xcode")](standard-controls-images/edit02.png#lightbox)

Per creare l'interfaccia utente, trascinare gli elementi dell'interfaccia utente (controlli AppKit) dal **controllo libreria** all' **Editor interfacce** in Interface Builder. Nell'esempio riportato di seguito, un controllo di **visualizzazione divisa verticale** è stato drogato da **Inspector Library** e inserito nella finestra nell' **editor di interfacce**:

[![](standard-controls-images/edit03.png "Selecting a Split View from the Library")](standard-controls-images/edit03.png#lightbox)

Per ulteriori informazioni sulla creazione di un'interfaccia utente in Interface Builder, vedere l' [Introduzione alla documentazione di Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) .

<a name="Sizing_and_Positioning" />

### <a name="sizing-and-positioning"></a>Dimensionamento e posizionamento

Una volta che un controllo è stato incluso nell'interfaccia utente, usare l' **editor di vincoli** per impostarne la posizione e le dimensioni immettendo i valori manualmente e controllando il modo in cui il controllo viene automaticamente posizionato e ridimensionato quando la finestra o la visualizzazione padre viene ridimensionata:

[![](standard-controls-images/edit04.png "Setting the constraints")](standard-controls-images/edit04.png#lightbox)

Utilizzare i **raggi i rossi** intorno all'esterno della casella **Autoresizing** per _posizionare_ un controllo in una determinata posizione (x, y). Esempio: 

[![](standard-controls-images/edit05.png "Editing a constraint")](standard-controls-images/edit05.png#lightbox)

Specifica che il controllo selezionato (nella **visualizzazione gerarchia** & **editor di interfaccia**) verrà bloccato nella posizione superiore e destra della finestra o della vista mentre viene ridimensionata o spostata. 

Altri elementi dell'editor proprietà del controllo, ad esempio altezza e larghezza:

[![](standard-controls-images/edit06.png "Setting the height")](standard-controls-images/edit06.png#lightbox)

È anche possibile controllare l'allineamento degli elementi con vincoli usando l' **editor di allineamento**:

[![](standard-controls-images/edit07.png "The Alignment Editor")](standard-controls-images/edit07.png#lightbox)

> [!IMPORTANT]
> A differenza di iOS dove (0, 0) è l'angolo superiore sinistro della schermata, in macOS (0,0) è l'angolo in basso a sinistra. Questo perché macOS usa un sistema di coordinate matematiche con i valori numerici che aumentano di valore verso l'alto e verso destra. È necessario prendere in considerazione questo aspetto quando si posizionano i controlli AppKit in un'interfaccia utente.

<a name="Setting_a_Custom_Class" />

### <a name="setting-a-custom-class"></a>Impostazione di una classe personalizzata

In alcuni casi, quando si utilizzano i controlli AppKit, è necessario creare una sottoclasse e il controllo esistente e creare una versione personalizzata di tale classe. Ad esempio, definendo una versione personalizzata dell'elenco di origine:

```csharp
using System;
using AppKit;
using Foundation;

namespace AppKit
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

Dove l'istruzione `[Register("SourceListView")]` espone la classe `SourceListView` a Objective-C in modo che sia possibile utilizzare in Interface Builder. Per altre informazioni, vedere la sezione [esporre C# classi/metodi in Objective-c](~/mac/internals/how-it-works.md) del documento interno di [Xamarin.Mac](~/mac/internals/how-it-works.md) , spiega i comandi`Register`e`Export`usati per collegare le C# classi a Objective-c. oggetti ed elementi dell'interfaccia utente.

Con il codice precedente, è possibile trascinare un controllo AppKit del tipo di base che si sta estendendo nell'area di progettazione (nell'esempio riportato di seguito, un **elenco di origine**), passare a **Identity Inspector** e impostare la **classe personalizzata** sul nome che è stato esposto a Objective-C (ad esempio `SourceListView`):

[![](standard-controls-images/edit10.png "Setting a custom class in Xcode")](standard-controls-images/edit10.png#lightbox)

<a name="Exposing_Outlets_and_Actions" />

### <a name="exposing-outlets-and-actions"></a>Esposizione di Outlet e azioni

Prima di poter accedere a un controllo AppKit C# nel codice, è necessario che venga esposto come un **Outlet** o un' **azione**. A tale scopo, selezionare il controllo specificato nella **gerarchia dell'interfaccia** o nell' **editor di interfacce** e passare alla **visualizzazione assistente** (assicurarsi di avere `.h` della finestra selezionata per la modifica):

[![](standard-controls-images/edit11.png "Selecting the correct file to edit")](standard-controls-images/edit11.png#lightbox)

Control-trascinare dal controllo AppKit al file Give `.h` per iniziare a creare un **Outlet** o un' **azione**:

[![](standard-controls-images/edit12.png "Dragging to create an Outlet or Action")](standard-controls-images/edit12.png#lightbox)

Selezionare il tipo di esposizione da creare e assegnare un **nome**all' **Outlet** o all' **azione** : 

[![](standard-controls-images/edit13.png "Configuring the Outlet or Action")](standard-controls-images/edit13.png#lightbox)

Per altre informazioni sull'uso di **Outlet** e **azioni**, vedere la sezione [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) dell' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) documentazione.

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>Sincronizzazione delle modifiche con Xcode

Quando si torna al Visual Studio per Mac da Xcode, tutte le modifiche apportate in Xcode verranno sincronizzate automaticamente con il progetto Xamarin.Mac.

Se si seleziona il `SplitViewController.designer.cs` nel **Esplora soluzioni** , sarà possibile vedere come l' **Outlet** e l' **azione** sono stati collegati nel C# codice:

[![](standard-controls-images/sync01.png "Synchronizing Changes with Xcode")](standard-controls-images/sync01.png#lightbox)

Si noti come la definizione nel file di `SplitViewController.designer.cs`:

```csharp
[Outlet]
AppKit.NSSplitViewItem LeftController { get; set; }

[Outlet]
AppKit.NSSplitViewItem RightController { get; set; }

[Outlet]
AppKit.NSSplitView SplitView { get; set; }
```

Riga con la definizione nel file di `MainWindow.h` in Xcode:

```csharp
@interface SplitViewController : NSSplitViewController {
    NSSplitViewItem *_LeftController;
    NSSplitViewItem *_RightController;
    NSSplitView *_SplitView;
}

@property (nonatomic, retain) IBOutlet NSSplitViewItem *LeftController;

@property (nonatomic, retain) IBOutlet NSSplitViewItem *RightController;

@property (nonatomic, retain) IBOutlet NSSplitView *SplitView;
```

Come si può notare, Visual Studio per Mac resta in attesa delle modifiche apportate al file di `.h` e quindi Sincronizza automaticamente tali modifiche nel rispettivo file di `.designer.cs` per esporle all'applicazione. È anche possibile notare che `SplitViewController.designer.cs` è una classe parziale, in modo che Visual Studio per Mac non debba modificare `SplitViewController.cs` che sovrascriverebbe le modifiche apportate alla classe.

Normalmente non sarà mai necessario aprire il `SplitViewController.designer.cs`, ma è stato presentato solo a scopo didattico.

> [!IMPORTANT]
> Nella maggior parte dei casi, Visual Studio per Mac visualizzerà automaticamente tutte le modifiche apportate in Xcode e le sincronizza con il progetto Xamarin.Mac. Se la sincronizzazione non viene eseguita automaticamente, tornare a Xcode e quindi di nuovo in Visual Studio per Mac. Questa operazione avvia solitamente un ciclo di sincronizzazione.

<a name="Working_with_Buttons" />

## <a name="working-with-buttons"></a>Uso dei pulsanti

AppKit offre diversi tipi di pulsante che possono essere usati nella progettazione dell'interfaccia utente. Per altre informazioni, vedere la sezione [pulsanti](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) delle [linee guida per l'interfaccia umana di Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons01.png "An example of the different button types")](standard-controls-images/buttons01.png#lightbox)

Se un pulsante è stato esposto tramite un **Outlet**, il codice seguente risponderà alla pressione:

```csharp
ButtonOutlet.Activated += (sender, e) => {
        FeedbackLabel.StringValue = "Button Outlet Pressed";
};
```

Per i pulsanti esposti tramite **azioni**, viene creato automaticamente un metodo di `public partial` con il nome scelto in Xcode. Per rispondere all' **azione**, completare il metodo parziale nella classe in cui è stata definita l' **azione** . Esempio:

```csharp
partial void ButtonAction (Foundation.NSObject sender) {
    // Do something in response to the Action
    FeedbackLabel.StringValue = "Button Action Pressed";
}
```

Per i pulsanti con stato (ad esempio, **on** e **off**), lo stato può essere selezionato o impostato con la proprietà `State` sulla `NSCellStateValue` enum. Esempio:

```csharp
DisclosureButton.Activated += (sender, e) => {
    LorumIpsum.Hidden = (DisclosureButton.State == NSCellStateValue.On);
};
```

Dove `NSCellStateValue` può essere:

- **Il pulsante** viene inserito o il controllo è selezionato, ad esempio un controllo in una casella di controllo.
- **Off** : il pulsante non viene inserito o il controllo non è selezionato.
- **Mixed** -combinazione di stati **on** e **off** .

<a name="Mark-a-Button-as-Default-and-Set-Key-Equivalent" />

### <a name="mark-a-button-as-default-and-set-key-equivalent"></a>Contrassegna un pulsante come predefinito e imposta chiave equivalente

Per qualsiasi pulsante aggiunto a una progettazione dell'interfaccia utente, è possibile contrassegnare il pulsante come pulsante _predefinito_ che verrà attivato quando l'utente preme il tasto **invio/invio** sulla tastiera. In macOS questo pulsante riceverà un colore di sfondo blu per impostazione predefinita.

Per impostare un pulsante come predefinito, selezionarlo nella Interface Builder di Xcode. Successivamente, nel **controllo attributi**selezionare il campo **equivalente chiave** e premere il tasto **invio/invio** :

[![](standard-controls-images/buttons03.png "Editing the Key Equivalent")](standard-controls-images/buttons03.png#lightbox)

Allo stesso modo, è possibile assegnare qualsiasi sequenza di chiavi che può essere usata per attivare il pulsante usando la tastiera anziché il mouse. Ad esempio, premendo le chiavi Command-C nell'immagine precedente.

Quando l'app viene eseguita e la finestra con il pulsante è la chiave e lo stato attivo, se l'utente preme Command-C, l'azione per il pulsante verrà attivata (come se l'utente avesse fatto clic sul pulsante).

<a name="Working_with_Checkboxes_and_Radio_Buttons" />

## <a name="working-with-checkboxes-and-radio-buttons"></a>Uso delle caselle di controllo e dei pulsanti di opzione

AppKit offre diversi tipi di caselle di controllo e gruppi di pulsanti di opzione che possono essere usati nella progettazione dell'interfaccia utente. Per altre informazioni, vedere la sezione [pulsanti](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) delle [linee guida per l'interfaccia umana di Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons02.png "An example of the available checkbox types")](standard-controls-images/buttons02.png#lightbox)

Le caselle di controllo e i pulsanti di opzione (esposti via **Outlet**) hanno uno stato (ad esempio, **on** e **off**). lo stato può essere selezionato o impostato con la proprietà `State` rispetto all'enumerazione `NSCellStateValue`. Esempio:

```csharp
AdjustTime.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Adjust Time: {0}",AdjustTime.State == NSCellStateValue.On);
};
```

Dove `NSCellStateValue` può essere:

- **Il pulsante** viene inserito o il controllo è selezionato, ad esempio un controllo in una casella di controllo.
- **Off** : il pulsante non viene inserito o il controllo non è selezionato.
- **Mixed** -combinazione di stati **on** e **off** .

Per selezionare un pulsante in un gruppo di pulsanti di opzione, esporre il pulsante di opzione per selezionare come **Outlet** e impostare la relativa proprietà `State`. Di seguito è riportato un esempio:

```csharp
partial void SelectCar (Foundation.NSObject sender) {
    TransportationCar.State = NSCellStateValue.On;
    FeedbackLabel.StringValue = "Car Selected";
}
```

Per ottenere una raccolta di pulsanti di opzione che fungano da gruppo e gestiscano automaticamente lo stato selezionato, creare una nuova **azione** e allinearvi ogni pulsante del gruppo:

![](standard-controls-images/buttons04.png "Creating a new Action")

Assegnare quindi un `Tag` univoco a ogni pulsante di opzione nel **controllo attributi**:

![](standard-controls-images/buttons05.png "Editing a radio button tag")

Salvare le modifiche e tornare a Visual Studio per Mac, aggiungere il codice per gestire l' **azione** a cui sono collegati tutti i pulsanti di opzione:

```csharp
partial void NumberChanged(Foundation.NSObject sender)
{
    var check = sender as NSButton;
    Console.WriteLine("Changed to {0}", check.Tag);
}
```

È possibile usare la proprietà `Tag` per vedere quale pulsante di opzione è stato selezionato.

<a name="Working_with_Menu_Controls" />

## <a name="working-with-menu-controls"></a>Utilizzo dei controlli menu

AppKit offre diversi tipi di controlli di menu che possono essere usati nella progettazione dell'interfaccia utente. Per altre informazioni, vedere la sezione [controlli menu](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlswithMenus.html#//apple_ref/doc/uid/20000957-CH100-SW1) delle [linee guida per l'interfaccia umana di Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/menu01.png "Example menu controls")](standard-controls-images/menu01.png#lightbox)

<a name="Providing-Menu-Control-Data" />

### <a name="providing-menu-control-data"></a>Fornire dati di controllo menu

I controlli menu disponibili per macOS possono essere impostati per popolare l'elenco a discesa da un elenco interno, che può essere definito in Interface Builder o popolato tramite codice, oppure fornendo un'origine dati personalizzata esterna.

<a name="Working-with-Internal-Data" />

#### <a name="working-with-internal-data"></a>Utilizzo di dati interni

Oltre a definire gli elementi in Interface Builder, i controlli menu (ad esempio `NSComboBox`) forniscono un set completo di metodi che consentono di aggiungere, modificare o eliminare gli elementi dall'elenco interno che gestiscono:

- `Add`: aggiunge un nuovo elemento alla fine dell'elenco.
- `GetItem`: restituisce l'elemento in corrispondenza dell'indice specificato.
- `Insert`: inserisce un nuovo elemento nell'elenco nella posizione specificata.
- `IndexOf`: restituisce l'indice dell'elemento specificato.
- `Remove`: rimuove l'elemento specificato dall'elenco.
- `RemoveAll`: rimuove tutti gli elementi dall'elenco.
- `RemoveAt`: rimuove l'elemento in corrispondenza dell'indice specificato.
- `Count`: restituisce il numero di elementi nell'elenco.

> [!IMPORTANT]
> Se si usa un'origine dati extern (`UsesDataSource = true`), la chiamata di uno dei metodi precedenti genererà un'eccezione.

<a name="Working-with-an-External-Data-Source" />

#### <a name="working-with-an-external-data-source"></a>Utilizzo di un'origine dati esterna

Anziché usare i dati interni incorporati per fornire le righe per il controllo menu, è possibile usare facoltativamente un'origine dati esterna e fornire un proprio archivio di backup per gli elementi, ad esempio un database SQLite.

Per utilizzare un'origine dati esterna, è possibile creare un'istanza dell'origine dati del controllo menu (ad esempio,`NSComboBoxDataSource`) ed eseguire l'override di diversi metodi per fornire i dati necessari:

- `ItemCount`: restituisce il numero di elementi nell'elenco.
- `ObjectValueForItem`: restituisce il valore dell'elemento per un indice specificato.
- `IndexOfItem`: restituisce l'indice per il valore Give Item.
- `CompletedString`: restituisce il primo valore dell'elemento corrispondente per il valore dell'elemento parzialmente tipizzato. Questo metodo viene chiamato solo se il completamento automatico è stato abilitato (`Completes = true`).

Per altri dettagli, vedere la sezione [database e ComboBox](~/mac/app-fundamentals/databases.md#Databases-and-ComboBoxes) del documento [utilizzo dei database](~/mac/app-fundamentals/databases.md) .

<a name="Adjusting-the-Lists-Appearance" />

### <a name="adjusting-the-lists-appearance"></a>Regolazione dell'aspetto dell'elenco

Per modificare l'aspetto del controllo menu sono disponibili i metodi seguenti:

- `HasVerticalScroller`-se `true`, il controllo visualizzerà una barra di scorrimento verticale. 
- `VisibleItems`: consente di modificare il numero di elementi visualizzati quando si apre il controllo. Il valore predefinito è cinque (5).
- `IntercellSpacing`: regolare la quantità di spazio intorno a un determinato elemento fornendo un `NSSize` in cui il `Width` specifica i margini sinistro e destro e il `Height` specifica lo spazio prima e dopo un elemento.
- `ItemHeight`-specifica l'altezza di ogni elemento nell'elenco.

Per i tipi di `NSPopupButtons`a discesa, la prima voce di menu fornisce il titolo per il controllo. Di seguito è riportato un esempio: 

[![](standard-controls-images/menu02.png "An example menu control")](standard-controls-images/menu02.png#lightbox)

Per modificare il titolo, esporre questo elemento come **Outlet** e usare un codice simile al seguente:

```csharp
DropDownSelected.Title = "Item 1";
```

<a name="Manipulating-the-Selected-Items" />

### <a name="manipulating-the-selected-items"></a>Modifica degli elementi selezionati

I metodi e le proprietà seguenti consentono di modificare gli elementi selezionati nell'elenco del controllo menu:

- `SelectItem`: seleziona l'elemento in corrispondenza dell'indice specificato.
- `Select`: selezionare il valore dell'elemento specificato.
- `DeselectItem`: deseleziona l'elemento in corrispondenza dell'indice specificato.
- `SelectedIndex`: restituisce l'indice dell'elemento attualmente selezionato.
- `SelectedValue`: restituisce il valore dell'elemento attualmente selezionato.

Usare il `ScrollItemAtIndexToTop` per presentare l'elemento in corrispondenza dell'indice specificato nella parte superiore dell'elenco e il `ScrollItemAtIndexToVisible` per scorrere fino all'elenco fino a quando non è visibile l'elemento in corrispondenza dell'indice specificato.

<a name="Responding to Events" />

### <a name="responding-to-events"></a>Risposta agli eventi

I controlli menu forniscono gli eventi seguenti per rispondere all'interazione dell'utente:

- `SelectionChanged`: viene chiamato quando l'utente ha selezionato un valore dall'elenco.
- `SelectionIsChanging`: viene chiamato prima che il nuovo elemento selezionato dall'utente diventi la selezione attiva.
- `WillPopup` viene chiamato prima che venga visualizzato l'elenco a discesa degli elementi.
- `WillDismiss` viene chiamato prima che l'elenco a discesa degli elementi venga chiuso.

Per `NSComboBox` controlli, includono tutti gli stessi eventi del `NSTextField`, ad esempio l'evento `Changed` che viene chiamato ogni volta che l'utente modifica il valore del testo nella casella combinata.

Facoltativamente, è possibile rispondere alle voci di menu dati interni definite in Interface Builder essere selezionate connettendo l'elemento a un' **azione** e utilizzare codice simile al seguente per rispondere all' **azione** attivata dall'utente:

```csharp
partial void ItemOne (Foundation.NSObject sender) {
    DropDownSelected.Title = "Item 1";
    FeedbackLabel.StringValue = "Item One Selected";
}
```

Per altre informazioni sull'uso di menu e controlli di menu, vedere i [menu](~/mac/user-interface/menu.md) e il [pulsante a comparsa e](~/mac/user-interface/menu.md) la documentazione di elenco a discesa.

<a name="Working_with_Selection_Controls" />

## <a name="working-with-selection-controls"></a>Utilizzo dei controlli di selezione

AppKit offre diversi tipi di controlli di selezione che possono essere usati nella progettazione dell'interfaccia utente. Per altre informazioni, vedere la sezione [controlli di selezione](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsSelection.html#//apple_ref/doc/uid/20000957-CH49-SW1) delle [linee guida per l'interfaccia umana di Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/select01.png "Example selection controls")](standard-controls-images/select01.png#lightbox)

Esistono due modi per tenere traccia quando un controllo di selezione dispone dell'interazione dell'utente, esponendolo come **azione**. Esempio:

```csharp
partial void SegmentButtonPressed (Foundation.NSObject sender) {
    FeedbackLabel.StringValue = string.Format("Button {0} Pressed",SegmentButtons.SelectedSegment);
}
```

Oppure allegando un **delegato** all'evento `Activated`. Esempio:

```csharp
TickedSlider.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
};
```

Per impostare o leggere il valore di un controllo di selezione, utilizzare la proprietà `IntValue`. Esempio:

```csharp
FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
```

I controlli speciali, ad esempio color well e image well, presentano proprietà specifiche per i relativi tipi di valore. Di seguito è riportato un esempio:

```csharp
ColorWell.Color = NSColor.Red;
ImageWell.Image = NSImage.ImageNamed ("tag.png");

```

Il `NSDatePicker` presenta le proprietà seguenti per lavorare direttamente con data e ora:

- **DateValue** : valore di data e ora corrente come `NSDate`.
- **Local** : posizione dell'utente come `NSLocal`.
- **TimeInterval** : valore di ora come `Double`.
- **TimeZone** : fuso orario dell'utente come `NSTimeZone`.

<a name="Working_with_Indicator_Controls" />

## <a name="working-with-indicator-controls"></a>Utilizzo dei controlli indicatore

AppKit offre diversi tipi di controlli indicatore che possono essere usati nella progettazione dell'interfaccia utente. Per ulteriori informazioni, vedere la sezione [controlli indicatore](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsIndicators.html#//apple_ref/doc/uid/20000957-CH50-SW1) delle [linee guida per l'interfaccia umana di Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/level01.png "Example indicator controls")](standard-controls-images/level01.png#lightbox)

Esistono due modi per tenere traccia quando un controllo indicatore presenta un'interazione dell'utente, esponendolo come un' **azione** o un' **uscita** e allegando un **delegato** all'evento `Activated`. Esempio:

```csharp
LevelIndicator.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Level: {0:###}",LevelIndicator.DoubleValue);
};
```

Per leggere o impostare il valore del controllo indicatore, utilizzare la proprietà `DoubleValue`. Esempio:

```csharp
FeedbackLabel.StringValue = string.Format("Rating: {0:###}",Rating.DoubleValue);
```

Gli indicatori di stato indeterminato e asincrono dovrebbero essere animati quando vengono visualizzati. Usare il metodo `StartAnimation` per avviare l'animazione quando vengono visualizzate. Esempio:

```csharp
Indeterminate.StartAnimation (this);
AsyncProgress.StartAnimation (this);
```

Se si chiama il metodo `StopAnimation`, l'animazione viene arrestata.

<a name="Working_with_Text_Controls" />

## <a name="working-with-text-controls"></a>Utilizzo di controlli di testo

AppKit offre diversi tipi di controlli di testo che possono essere usati nella progettazione dell'interfaccia utente. Per altre informazioni, vedere la sezione [controlli di testo](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsText.html#//apple_ref/doc/uid/20000957-CH51-SW1) delle [linee guida per l'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)di Apple. 

[![](standard-controls-images/text01.png "Example text controls")](standard-controls-images/text01.png#lightbox)

Per i campi di testo (`NSTextField`), per tenere traccia dell'interazione dell'utente è possibile usare gli eventi seguenti:

- **Changed** -viene generato ogni volta che l'utente modifica il valore del campo. Ad esempio, su ogni carattere tipizzato.
- **EditingBegan** -viene generato quando l'utente seleziona il campo per la modifica.
- **EditingEnded** -quando l'utente preme il tasto invio nel campo o lascia il campo.

Utilizzare la proprietà `StringValue` per leggere o impostare il valore del campo. Esempio:

```csharp
FeedbackLabel.StringValue = string.Format("User ID: {0}",UserField.StringValue);
```

Per i campi che visualizzano o modificano i valori numerici, è possibile usare la proprietà `IntValue`. Esempio:

```csharp
FeedbackLabel.StringValue = string.Format("Number: {0}",NumberField.IntValue);
```

Un `NSTextView` fornisce un'area di visualizzazione e modifica di testo completa con formattazione incorporata. Analogamente a una `NSTextField`, utilizzare la proprietà `StringValue` per leggere o impostare il valore dell'area.

Per un esempio di un esempio complesso di uso delle visualizzazioni di testo in un'app Xamarin.Mac, vedere l' [app di esempio SourceWriter](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter). SourceWriter è un editor di codice sorgente semplice che offre supporto per il completamento del codice e informazioni sulla sintassi.

Il codice SourceWriter include tutti i commenti e, dove possibile, sono stati indicati i collegamenti delle tecnologie e dei metodi principali alle informazioni corrispondenti nelle guide di Xamarin.Mac.

<a name="Working_with_Content_Views" />

## <a name="working-with-content-views"></a>Utilizzo delle visualizzazioni contenuto

AppKit offre diversi tipi di visualizzazioni di contenuto che possono essere usate nella progettazione dell'interfaccia utente. Per altre informazioni, vedere la sezione [visualizzazioni contenuto](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) delle [linee guida per l'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)di Apple.

[![](standard-controls-images/content01.png "An example content view")](standard-controls-images/content01.png#lightbox)

<a name="Popovers" />

### <a name="popovers"></a>Popovers

Un oggetto è un elemento temporaneo dell'interfaccia utente che fornisce funzionalità direttamente correlate a un controllo specifico o a un'area sullo schermo. Un oggetto popopov si sposta sopra la finestra che contiene il controllo o l'area a cui è correlata e il bordo include una freccia per indicare il punto da cui è emerso.

Per creare un popopov, seguire questa procedura:

1. Aprire il file di `.storyboard` della finestra a cui si desidera aggiungere un utente facendo doppio clic su di esso nella **Esplora soluzioni**
2. Trascinare un **controller di visualizzazione** da **libreria Inspector** nell' **editor di interfaccia**: 

    [![](standard-controls-images/content02.png "Selecting a View Controller from the Library")](standard-controls-images/content02.png#lightbox)
3. Definire le dimensioni e il layout della **visualizzazione personalizzata**: 

    [![](standard-controls-images/content04.png "Editing the layout")](standard-controls-images/content04.png#lightbox)
4. CTRL: fare clic e trascinare dall'origine del popup nel controller di **visualizzazione**: 

    [![](standard-controls-images/content05.png "Dragging to create a segue")](standard-controls-images/content05.png#lightbox)
5. Selezionare **popover** dal menu di scelta rapida: 

    [![](standard-controls-images/content06.png "Setting the segue type")](standard-controls-images/content06.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

<a name="Tab_Views" />

### <a name="tab-views"></a>Visualizzazioni a schede

Le visualizzazioni a schede sono costituite da un elenco di schede (simile a un controllo segmentato) combinato con un set di viste denominate _riquadri_. Quando l'utente seleziona una nuova scheda, verrà visualizzato il riquadro collegato. Ogni riquadro contiene un proprio set di controlli.

Quando si lavora con una visualizzazione a schede nella Interface Builder di Xcode, usare il **controllo attribute** per impostare il numero di schede:

[![](standard-controls-images/content08.png "Editing the number of tabs")](standard-controls-images/content08.png#lightbox)

Selezionare ogni scheda nella **gerarchia dell'interfaccia** per impostarne il **titolo** e aggiungere elementi dell'interfaccia utente al **riquadro**:

[![](standard-controls-images/content09.png "Editing the tabs in Xcode")](standard-controls-images/content09.png#lightbox)

<a name="Data_Binding_AppKit_Controls" />

## <a name="data-binding-appkit-controls"></a>Data Binding AppKit controlli

Usando le tecniche di codifica e data binding nell'applicazione Xamarin.Mac, è possibile ridurre significativamente la quantità di codice da scrivere e gestire per popolare e usare gli elementi dell'interfaccia utente. Si ha anche il vantaggio di separare ulteriormente i dati di supporto (modello di_dati_) dall'interfaccia utente front-end (_Model-View-Controller_), in modo da semplificare la gestione e la progettazione di applicazioni più flessibili.

Il codice chiave-valore (KVC) è un meccanismo per accedere indirettamente alle proprietà di un oggetto, usando chiavi (stringhe formattate in modo particolare) per identificare le proprietà anziché accedervi tramite variabili di istanza o metodi di funzione di accesso (`get/set`). Implementando le funzioni di accesso conformi al codice chiave-valore nell'applicazione Xamarin.Mac, è possibile accedere ad altre funzionalità di macOS, ad esempio osservazione chiave-valore (KVO), data binding, dati principali, associazioni Cocoa e script.

Per altre informazioni, vedere la sezione [Data Binding semplice](~/mac/app-fundamentals/databinding.md#Simple_Data_Binding) del data binding e la documentazione relativa al [codice chiave-valore](~/mac/app-fundamentals/databinding.md) .

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato in dettaglio come usare i controlli AppKit standard, ad esempio pulsanti, etichette, campi di testo, caselle di controllo e controlli segmentati in un'applicazione Xamarin.Mac. È stato illustrato come aggiungerli a una progettazione dell'interfaccia utente in Interface Builder di Xcode, esponendoli al codice tramite Outlet e azioni e utilizzando i C# controlli AppKit nel codice.

## <a name="related-links"></a>Collegamenti correlati

- [MacControls (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccontrols)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Informazioni sui controlli e sulle visualizzazioni](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)
