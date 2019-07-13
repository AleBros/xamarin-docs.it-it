---
title: Controlli standard in xamarin. Mac
description: Questo articolo descrive l'uso di controlli AppKit standard, ad esempio pulsanti, etichette, campi di testo, caselle di controllo e segmentata i controlli in un'applicazione xamarin. Mac. Descrive aggiungendoli a un'interfaccia con Interface Builder e sull'interazione con essi nel codice.
ms.prod: xamarin
ms.assetid: d2593883-d255-431f-9781-75f04d8cecea
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 696432fb51060a236780eedee0b60661e3a86712
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865184"
---
# <a name="standard-controls-in-xamarinmac"></a>Controlli standard in xamarin. Mac

_Questo articolo descrive l'uso di controlli AppKit standard, ad esempio pulsanti, etichette, campi di testo, caselle di controllo e segmentata i controlli in un'applicazione xamarin. Mac. Descrive aggiungendoli a un'interfaccia con Interface Builder e sull'interazione con essi nel codice._

Quando si lavora con C# e .NET in un'applicazione xamarin. Mac, è possibile utilizzare la stessa AppKit controlla che gli sviluppatori che lavorano *Objective-C* e *Xcode* viene. Poiché xamarin. Mac si integra direttamente con Xcode, è possibile usare Xcode _Interface Builder_ per creare e gestire i controlli Appkit (oppure crearle direttamente nel codice c#).

I controlli AppKit sono gli elementi dell'interfaccia utente che vengono usati per creare l'interfaccia utente dell'applicazione xamarin. Mac. Sono costituiti da elementi, ad esempio pulsanti, etichette, campi di testo, caselle di controllo e controlli segmentati e causare azioni immediate o risultati visibili quando un utente le manipola.

[![](standard-controls-images/intro01.png "Schermata principale dell'app di esempio")](standard-controls-images/intro01.png#lightbox)

In questo articolo, si affronterà le nozioni di base dell'utilizzo di controlli AppKit in un'applicazione xamarin. Mac. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare le [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) le sezioni, perché illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile ottenere un quadro il [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) del documento, viene spiegato il `Register` e `Export` comandi utilizzato per wireup classi c# per gli oggetti di Objective-C e gli elementi dell'interfaccia utente.

<a name="Introduction_to_Controls_and_Views" />

## <a name="introduction-to-controls-and-views"></a>Introduzione ai controlli e visualizzazioni

macOS (precedentemente noto come Mac OS X) fornisce un insieme standard dei controlli dell'interfaccia utente tramite il Framework di AppKit. Sono costituiti da elementi, ad esempio pulsanti, etichette, campi di testo, caselle di controllo e controlli segmentati e causare azioni immediate o risultati visibili quando un utente le manipola.

Tutti i controlli AppKit hanno un aspetto standard e incorporato appropriate per la maggior parte dei casi, alcuni specificano un aspetto alternativo per l'uso in un'area di frame di finestra o in un _effetto Vividezza_ contesto, ad esempio in un'area dell'intestazione laterale o in un Widget di centro notifiche.

Quando si lavora con i controlli AppKit, Apple consiglia le linee guida seguenti:

- Evitare di combinare le dimensioni di controllo nella stessa visualizzazione.
- In generale, evitare il ridimensionamento verticale dei controlli.
- Usare il carattere di sistema e le dimensioni del testo corretto all'interno di un controllo.
- Utilizzare la spaziatura corretta tra i controlli.

Per ulteriori informazioni, vedere pleas il [informazioni sui controlli e visualizzazioni](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Using_Controls_in_a_Window_Frame" />

### <a name="using-controls-in-a-window-frame"></a>Utilizzo di controlli in una cornice di finestra

Esistono un subset di controlli AppKit che includono uno stile di visualizzazione che consente loro di essere includere nell'area di Frame della finestra. Per un esempio, visualizzato sulla barra degli strumenti dell'app di posta elettronica:

[![](standard-controls-images/mailapp.png "Una cornice di finestra Mac")](standard-controls-images/mailapp.png#lightbox)

- **Arrotondare pulsante trama** : una `NSButton` con uno stile di `NSTexturedRoundedBezelStyle`.
- **Trama arrotondato controllo Segmented** : una `NSSegmentedControl` con uno stile di `NSSegmentStyleTexturedRounded`.
- **Trama arrotondato controllo Segmented** : una `NSSegmentedControl` con uno stile di `NSSegmentStyleSeparated`.
- **Arrotondare trama Menu a comparsa** : una `NSPopUpButton` con uno stile di `NSTexturedRoundedBezelStyle`.
- **Arrotondare trama Menu a tendina** : una `NSPopUpButton` con uno stile di `NSTexturedRoundedBezelStyle`.
- **Barra di ricerca** : un `NSSearchField`.

Quando si lavora con i controlli AppKit in una cornice di finestra, Apple consiglia le linee guida seguenti:

- Non usare gli stili di controllo specifico di cornice della finestra nel corpo della finestra.
- Non usare i controlli finestra corpo o stili nella cornice della finestra.

Per ulteriori informazioni, vedere pleas il [informazioni sui controlli e visualizzazioni](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Creating_a_User_Interface_in_Interface_Builder" />

## <a name="creating-a-user-interface-in-interface-builder"></a>Creazione di un'interfaccia utente in Interface Builder

Quando si crea una nuova applicazione xamarin. Mac Cocoa, si ottiene una normale finestra vuota, per impostazione predefinita. Questo windows è definito in un `.storyboard` file automaticamente inclusi nel progetto. Per modificare la progettazione di windows, nelle **Esplora soluzioni**, fare doppio clic il `Main.storyboard` file:

[![](standard-controls-images/edit01.png "Selezionando lo Storyboard principale in Esplora soluzioni")](standard-controls-images/edit01.png#lightbox)

Verrà aperta la progettazione di finestra in Interface Builder di Xcode:

[![](standard-controls-images/edit02.png "Modifica lo storyboard in Xcode")](standard-controls-images/edit02.png#lightbox)

Per creare un'interfaccia utente, è possibile trascinare elementi dell'interfaccia utente (controlli AppKit) dal **Library Inspector** per il **Interface Editor** in Interface Builder. Nell'esempio seguente, un **doppia visualizzazione verticale** controllo è stato drug dal **Library Inspector** e inserito nella finestra di **Editor dell'interfaccia**:

[![](standard-controls-images/edit03.png "Selezione di una doppia visualizzazione dalla libreria")](standard-controls-images/edit03.png#lightbox)

Per altre informazioni sulla creazione di un'interfaccia utente in Interface Builder, vedere la [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) documentazione.

<a name="Sizing_and_Positioning" />

### <a name="sizing-and-positioning"></a>Ridimensionamento e posizionamento

Una volta che un controllo è stato incluso nell'interfaccia utente, usare il **editor vincoli di** per impostare la posizione e dimensioni inserendo manualmente i valori e controllare la modalità con cui il controllo viene posizionato automaticamente e ridimensionati quando la finestra padre o la vista viene ridimensionato:

[![](standard-controls-images/edit04.png "L'impostazione di vincoli")](standard-controls-images/edit04.png#lightbox)

Usare il **ai rossi** all'esterno del **Autoresizing** casella relativa alle _stilizzata_ un controllo in una posizione specificata (x, y). Ad esempio: 

[![](standard-controls-images/edit05.png "Modifica di un vincolo")](standard-controls-images/edit05.png#lightbox)

Specifica che il controllo selezionato (nelle **visualizzazione della gerarchia** & **Editor dell'interfaccia**) verranno bloccati per la posizione superiore e destra della finestra o della vista come che viene ridimensionata o spostata. 

Altri elementi delle proprietà del controllo dell'editor, come Height e Width:

[![](standard-controls-images/edit06.png "L'impostazione dell'altezza")](standard-controls-images/edit06.png#lightbox)

È anche possibile controllare l'allineamento degli elementi con i vincoli utilizzando il **allineamento Editor**:

[![](standard-controls-images/edit07.png "L'Editor di allineamento")](standard-controls-images/edit07.png#lightbox)

> [!IMPORTANT]
> A differenza di iOS in cui (0,0) è l'angolo superiore sinistro dello schermo, in macOS (0,0) è l'angolo inferiore sinistro. Si tratta poiché macOS utilizza un sistema di coordinate matematico con i valori numerici aumenta progressivamente verso l'alto e a destra. È necessario prendere in considerazione durante il posizionamento di controlli AppKit in un'interfaccia utente.

<a name="Setting_a_Custom_Class" />

### <a name="setting-a-custom-class"></a>L'impostazione di una classe personalizzata

Esistono ora di utilizzo di controlli AppKit che si sarà necessario sottoclasse e controllo esistente e creare è propria versione personalizzata di tale classe. Ad esempio, la definizione di una versione personalizzata dell'elenco di origine:

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

In cui il `[Register("SourceListView")]` istruzione espone il `SourceListView` classe Objective-c in modo che può essere utilizzata in Interface Builder. Per altre informazioni, vedere la [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) del documento, viene spiegato il `Register` e `Export` comandi usati wire backup di classi c# per gli oggetti di Objective-C e gli elementi dell'interfaccia utente.

Con il codice precedente, è possibile trascinare un controllo di AppKit, del tipo di base che si vuole estendere, nell'area di progettazione (nell'esempio seguente, un **Source List**), passare alle **Identity Inspector** e impostare il **classe personalizzata** sul nome che si espone a Objective-C (esempio `SourceListView`):

[![](standard-controls-images/edit10.png "Impostazione di una classe personalizzata in Xcode")](standard-controls-images/edit10.png#lightbox)

<a name="Exposing_Outlets_and_Actions" />

### <a name="exposing-outlets-and-actions"></a>Esposizione di Outlet e azioni

Prima di un controllo di AppKit sono accessibili nel codice c#, deve essere esposta come un **Outlet** o e **azione**. Eseguire questo selezionare il controllo specificato in uno il **gerarchia delle interfacce** o il **Editor dell'interfaccia** e passare al **visualizzazione Assistant** (assicurarsi di avere il `.h`della finestra selezionata per la modifica):

[![](standard-controls-images/edit11.png "Selezionare il file corretto da modificare")](standard-controls-images/edit11.png#lightbox)

Controllo e trascinare dal controllo AppKit sulla consentono `.h` file per iniziare a creare un' **Outlet** oppure **azione**:

[![](standard-controls-images/edit12.png "Trascinamento per la creazione di un'azione o un Outlet")](standard-controls-images/edit12.png#lightbox)

Selezionare il tipo di esposizione a creare e assegnare il **Outlet** oppure **azione** una **nome**: 

[![](standard-controls-images/edit13.png "Configurare l'azione o un Outlet")](standard-controls-images/edit13.png#lightbox)


Per altre informazioni sull'uso di **Outlet** e **azioni**, vedere il [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) sezione del nostro [Introduzione a Xcode e Interface Generatore](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) documentazione.

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>Sincronizzazione delle modifiche con Xcode

Quando si passa a Visual Studio per Mac da Xcode, tutte le modifiche apportate in Xcode verranno automaticamente sincronizzate con il progetto xamarin. Mac.

Se si seleziona il `SplitViewController.designer.cs` nella **Esplora soluzioni** sarà in grado di vedere come il **Outlet** e **azione** sono stati collegati nel codice c#:

[![](standard-controls-images/sync01.png "Sincronizzazione delle modifiche con Xcode")](standard-controls-images/sync01.png#lightbox)

Si noti come la definizione di `SplitViewController.designer.cs` file:

```csharp
[Outlet]
AppKit.NSSplitViewItem LeftController { get; set; }

[Outlet]
AppKit.NSSplitViewItem RightController { get; set; }

[Outlet]
AppKit.NSSplitView SplitView { get; set; }
```

Allineati con la definizione di `MainWindow.h` file in Xcode:

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

Come può notare, Visual Studio per Mac è in ascolto per le modifiche per il `.h` file e quindi si sincronizza automaticamente le modifiche nella rispettiva `.designer.cs` file per esporle all'applicazione. È anche possibile notare che `SplitViewController.designer.cs` è una classe parziale, in modo che Visual Studio per Mac non deve modificare `SplitViewController.cs` sovrascrivendo eventuali modifiche che sono stati apportati alla classe.

In genere non sarà mai necessario aprire le `SplitViewController.designer.cs` manualmente, è stata presentata esclusivamente per scopi didattici.

> [!IMPORTANT]
> Nella maggior parte dei casi, Visual Studio per Mac verrà automaticamente le modifiche apportate in Xcode e Sincronizza con il progetto xamarin. Mac. Se la sincronizzazione non viene eseguita automaticamente, tornare a Xcode e quindi di nuovo in Visual Studio per Mac. Questa operazione avvia solitamente un ciclo di sincronizzazione.

<a name="Working_with_Buttons" />

## <a name="working-with-buttons"></a>Uso di pulsanti

AppKit offre diversi tipi di pulsante che può essere utilizzato nella progettazione dell'interfaccia utente. Per altre informazioni, vedere la [pulsanti](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons01.png "Un esempio dei tipi di pulsante diverso")](standard-controls-images/buttons01.png#lightbox)

Se un pulsante è stato esposto tramite un **Outlet**, il codice seguente risponderà a esso pressione:

```csharp
ButtonOutlet.Activated += (sender, e) => {
        FeedbackLabel.StringValue = "Button Outlet Pressed";
};
```

Per i pulsanti che sono state esposte tramite **azioni**, un `public partial` metodo verrà automaticamente creato automaticamente con il nome scelto in Xcode. Per rispondere al **azione**, completare il metodo nella classe parziale che le **azione** era definita in. Ad esempio:

```csharp
partial void ButtonAction (Foundation.NSObject sender) {
    // Do something in response to the Action
    FeedbackLabel.StringValue = "Button Action Pressed";
}
```

Per i pulsanti che hanno uno stato (come **sul** e **Off**), lo stato può essere selezionato o impostato con il `State` proprietà in base al `NSCellStateValue` enum. Ad esempio:

```csharp
DisclosureButton.Activated += (sender, e) => {
    LorumIpsum.Hidden = (DisclosureButton.State == NSCellStateValue.On);
};
```

In cui `NSCellStateValue` può essere:

- **In** : il pulsante viene eseguito il push o il controllo è selezionato (ad esempio, un segno di spunta una casella di controllo).
- **Disattivare** : il pulsante non viene eseguito il push o il controllo non è selezionato.
- **Misto** -una combinazione di **sul** e **Off** stati.

<a name="Mark-a-Button-as-Default-and-Set-Key-Equivalent" />

### <a name="mark-a-button-as-default-and-set-key-equivalent"></a>Contrassegnare un pulsante come impostazione predefinita e impostare una chiave equivalente

Per qualsiasi pulsante che è aggiunti a una progettazione dell'interfaccia utente, è possibile contrassegnare tale pulsante come le _predefinito_ pulsante che verrà attivato quando l'utente preme il **Return/invio** tasti sulla tastiera. In macOS, questo pulsante riceverà un colore di sfondo blu per impostazione predefinita.

Per impostare un pulsante come impostazione predefinita, selezionarlo in Interface Builder di Xcode. Successivamente, nella **attributo Inspector**, selezionare la **chiave equivalente** campo e premere il **Return/invio** chiave:

[![](standard-controls-images/buttons03.png "Modifica l'equivalente di chiavi")](standard-controls-images/buttons03.png#lightbox)

Allo stesso modo, è possibile assegnare qualsiasi sequenza di tasti che può essere utilizzato per attivare il pulsante con la tastiera anziché il mouse. Ad esempio, premendo i tasti comando-C nell'immagine precedente.

Quando l'app viene eseguita e la finestra con il pulsante è chiave e lo stato attivo, se l'utente preme il tasto comando-C, verrà attivata l'azione per il pulsante (come-se l'utente ha fatto clic sul pulsante).

<a name="Working_with_Checkboxes_and_Radio_Buttons" />

## <a name="working-with-checkboxes-and-radio-buttons"></a>Uso di caselle di controllo e pulsanti di opzione

AppKit offre diversi tipi di caselle di controllo e i gruppi di pulsanti di opzione che può essere utilizzato nella progettazione dell'interfaccia utente. Per altre informazioni, vedere la [pulsanti](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons02.png "Un esempio dei tipi di casella di controllo disponibile")](standard-controls-images/buttons02.png#lightbox)


Caselle di controllo e pulsanti di opzione (esposti tramite **Outlet**) hanno uno stato (come **sul** e **Off**), lo stato può essere selezionato o impostato con il `State` proprietà in base al `NSCellStateValue` enum. Ad esempio:

```csharp
AdjustTime.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Adjust Time: {0}",AdjustTime.State == NSCellStateValue.On);
};
```

In cui `NSCellStateValue` può essere:

- **In** : il pulsante viene eseguito il push o il controllo è selezionato (ad esempio, un segno di spunta una casella di controllo).
- **Disattivare** : il pulsante non viene eseguito il push o il controllo non è selezionato.
- **Misto** -una combinazione di **sul** e **Off** stati.

Per selezionare un pulsante in un gruppo di pulsanti di opzione, il pulsante di opzione per selezionare come esporre un' **Outlet** e impostare il `State` proprietà. Di seguito è riportato un esempio:

```csharp
partial void SelectCar (Foundation.NSObject sender) {
    TransportationCar.State = NSCellStateValue.On;
    FeedbackLabel.StringValue = "Car Selected";
}
```

Per ottenere una raccolta di pulsanti di opzione di funzionare come un gruppo e gestiscono automaticamente lo stato selezionato, creare una nuova **azione** e collegare ogni pulsante del gruppo:

![](standard-controls-images/buttons04.png "Creazione di un'azione")

Quindi assegnare un valore univoco `Tag` per ogni pulsante di opzione nel **Inspector attributo**:

![](standard-controls-images/buttons05.png "Modifica di un tag di pulsante di opzione")

Salvare le modifiche e tornare a Visual Studio per Mac, aggiungere il codice per gestire il **azione** che tutti i pulsanti di opzione sono collegati a:

```csharp
partial void NumberChanged(Foundation.NSObject sender)
{
    var check = sender as NSButton;
    Console.WriteLine("Changed to {0}", check.Tag);
}
```

È possibile usare il `Tag` proprietà per visualizzare il pulsante di opzione è stato selezionato.

<a name="Working_with_Menu_Controls" />

## <a name="working-with-menu-controls"></a>Uso di controlli di Menu

AppKit offre diversi tipi di controlli di Menu che può essere utilizzato nella progettazione dell'interfaccia utente. Per altre informazioni, vedere la [dal Menu controlli](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlswithMenus.html#//apple_ref/doc/uid/20000957-CH100-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/menu01.png "Controlli menu di esempio")](standard-controls-images/menu01.png#lightbox)

<a name="Providing-Menu-Control-Data" />

### <a name="providing-menu-control-data"></a>Fornisce dati del controllo Menu

I controlli Menu disponibile per macOS può essere impostati su popolare l'elenco a discesa da un elenco interno (che può essere preventivamente definito in Interface Builder o popolato mediante codice) oppure specificando il proprio origine dati esterna e personalizzati.

<a name="Working-with-Internal-Data" />

#### <a name="working-with-internal-data"></a>Utilizzo di dati interni

Oltre a definire gli elementi in Interface Builder, i controlli Menu (ad esempio `NSComboBox`), offrono un set completo di metodi che consentono di aggiungere, modificare o eliminare gli elementi nell'elenco interno che gestiscono:

- `Add` -Aggiunge un nuovo elemento alla fine dell'elenco.
- `GetItem` : Restituisce l'elemento in corrispondenza dell'indice specificato.
- `Insert` -Inserisce un nuovo elemento nell'elenco nella posizione specificata.
- `IndexOf` : Restituisce l'indice dell'elemento specificato.
- `Remove` : Rimuove l'elemento specificato dall'elenco.
- `RemoveAll` : Rimuove tutti gli elementi dall'elenco.
- `RemoveAt` : Rimuove l'elemento in corrispondenza dell'indice specificato.
- `Count` : Restituisce il numero di elementi nell'elenco.

> [!IMPORTANT]
> Se si usa un'origine dati Extern (`UsesDataSource = true`), la chiamata a uno qualsiasi dei suddetti metodi generano un'eccezione.

<a name="Working-with-an-External-Data-Source" />

#### <a name="working-with-an-external-data-source"></a>Utilizzo di un'origine dati esterna

Anziché usare i dati interni incorporati per specificare le righe per il controllo Menu, è facoltativamente possibile usare un'origine dati esterna e fornire il proprio archivio di backup per gli elementi (ad esempio un database SQLite).

Per usare un'origine dati esterna, si creerà un'istanza dell'origine dati del controllo Menu (`NSComboBoxDataSource` , ad esempio) ed eseguire l'override di diversi metodi per fornire i dati necessari:

- `ItemCount` : Restituisce il numero di elementi nell'elenco.
- `ObjectValueForItem` : Restituisce il valore dell'elemento per un determinato indice.
- `IndexOfItem` : Restituisce l'indice per assegnare il valore dell'elemento.
- `CompletedString` : Restituisce il primo valore corrispondente elemento per il valore dell'elemento parzialmente tipizzato. Questo metodo viene chiamato solo se è stato abilitato il completamento automatico (`Completes = true`).

Vedere la [i database e ComboBox](~/mac/app-fundamentals/databases.md#Databases-and-ComboBoxes) sezione del [si lavora con database](~/mac/app-fundamentals/databases.md) documento per altri dettagli.

<a name="Adjusting-the-Lists-Appearance" />

### <a name="adjusting-the-lists-appearance"></a>Modificare l'aspetto dell'elenco

I metodi seguenti sono disponibili per regolare l'aspetto del controllo Menu:

- `HasVerticalScroller` -Se `true`, il controllo Visualizza una barra di scorrimento verticale. 
- `VisibleItems` -Modificare il numero di elementi visualizzati quando viene aperto il controllo. Il valore predefinito è cinque (5).
- `IntercellSpacing` -Regolare la quantità di spazio intorno a un determinato elemento, fornendo una `NSSize` in cui la `Width` specifica i margini sinistro e destro e `Height` specifica lo spazio prima e dopo che un elemento.
- `ItemHeight` -Specifica l'altezza di ogni elemento nell'elenco.

Per i tipi di elenco a discesa di `NSPopupButtons`, la prima voce di Menu fornisce il titolo per il controllo. Di seguito è riportato un esempio: 

[![](standard-controls-images/menu02.png "Controllo menu di esempio")](standard-controls-images/menu02.png#lightbox)

Per modificare il titolo, questo elemento come esporre un' **Outlet** e usare codice simile al seguente:

```csharp
DropDownSelected.Title = "Item 1";
```

<a name="Manipulating-the-Selected-Items" />

### <a name="manipulating-the-selected-items"></a>La modifica degli elementi selezionati

I metodi e le proprietà seguenti consentono di modificare gli elementi selezionati nell'elenco del controllo Menu:

- `SelectItem` -Consente di selezionare l'elemento in corrispondenza dell'indice specificato.
- `Select` -Selezionare il valore dell'elemento specificato.
- `DeselectItem` -Deseleziona l'elemento in corrispondenza dell'indice specificato.
- `SelectedIndex` : Restituisce l'indice dell'elemento attualmente selezionato.
- `SelectedValue` : Restituisce il valore dell'elemento attualmente selezionato.

Usare la `ScrollItemAtIndexToTop` presentare l'elemento in corrispondenza dell'indice specificato nella parte superiore dell'elenco e il `ScrollItemAtIndexToVisible` scorrere all'elenco fino a quando non è visibile l'elemento in corrispondenza dell'indice specificato.

<a name="Responding to Events" />

### <a name="responding-to-events"></a>Risposta agli eventi

I controlli menu forniscono i seguenti eventi per rispondere all'interazione dell'utente:

- `SelectionChanged` -Viene chiamato quando l'utente ha selezionato un valore dall'elenco.
- `SelectionIsChanging` -Viene chiamato prima che il nuovo elemento selezionato dall'utente diventa la selezione attiva.
- `WillPopup` -Viene chiamato prima che venga visualizzato l'elenco a discesa di elementi.
- `WillDismiss` -Viene chiamato prima che venga chiuso l'elenco a discesa di elementi.

Per `NSComboBox` controlli, includono tutti gli stessi eventi come il `NSTextField`, ad esempio il `Changed` evento che viene chiamato ogni volta che l'utente modifica il valore del testo nella casella combinata.

Facoltativamente, è possibile rispondere l'un voci di Menu dati interni definiti in Interface Builder selezionato collegando l'elemento a un **azione** e usare codice simile al seguente per rispondere alle **azione** in corso attivata dall'utente:

```csharp
partial void ItemOne (Foundation.NSObject sender) {
    DropDownSelected.Title = "Item 1";
    FeedbackLabel.StringValue = "Item One Selected";
}
```

Per altre informazioni sull'uso dei menu e controlli Menu, vedere la [menu di scelta](~/mac/user-interface/menu.md) e [popup pulsante e un elenco a discesa](~/mac/user-interface/menu.md) documentazione.

<a name="Working_with_Selection_Controls" />

## <a name="working-with-selection-controls"></a>Uso di controlli di selezione

AppKit offre diversi tipi di controlli di selezione che può essere utilizzato nella progettazione dell'interfaccia utente. Per altre informazioni, vedere la [controlli di selezione](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsSelection.html#//apple_ref/doc/uid/20000957-CH49-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/select01.png "Controlli di selezione di esempio")](standard-controls-images/select01.png#lightbox)

Esistono due modi per rilevare quando un controllo di selezione è l'interazione dell'utente, esporlo come una **azione**. Ad esempio:

```csharp
partial void SegmentButtonPressed (Foundation.NSObject sender) {
    FeedbackLabel.StringValue = string.Format("Button {0} Pressed",SegmentButtons.SelectedSegment);
}
```

O collegando un' **delegato** per il `Activated` evento. Ad esempio:

```csharp
TickedSlider.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
};
```

Per impostare o leggere il valore di un controllo di selezione, usare il `IntValue` proprietà. Ad esempio:

```csharp
FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
```

I controlli specializzati (ad esempio anche colore e immagine anche) hanno le proprietà specifiche per i relativi tipi di valore. Di seguito è riportato un esempio:

```csharp
ColorWell.Color = NSColor.Red;
ImageWell.Image = NSImage.ImageNamed ("tag.png");

```

Il `NSDatePicker` presenta le proprietà seguenti per lavorare direttamente con data e ora:

- **DateValue** -il valore di data e ora corrente come un `NSDate`.
- **Locale** -la posizione dell'utente come un `NSLocal`.
- **TimeInterval** -il valore di ora come un `Double`.
- **Fuso orario** -fuso orario dell'utente come un `NSTimeZone`.

<a name="Working_with_Indicator_Controls" />

## <a name="working-with-indicator-controls"></a>Uso di controlli indicatore

AppKit offre diversi tipi di controlli indicatore che può essere utilizzato nella progettazione dell'interfaccia utente. Per altre informazioni, vedere la [controlli indicatore](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsIndicators.html#//apple_ref/doc/uid/20000957-CH50-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/level01.png "Controlli indicatore di esempio")](standard-controls-images/level01.png#lightbox)

Esistono due modi per rilevare quando un controllo indicatore di interazione dell'utente, uno per esporlo come una **azione** o un' **Outlet** e collegamento di un **delegato** al `Activated`evento. Ad esempio:

```csharp
LevelIndicator.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Level: {0:###}",LevelIndicator.DoubleValue);
};
```

Per leggere o impostare il valore del controllo indicatore di, utilizzare il `DoubleValue` proprietà. Ad esempio:

```csharp
FeedbackLabel.StringValue = string.Format("Rating: {0:###}",Rating.DoubleValue);
```

Il Indeterminate e gli indicatori di stato asincrono deve essere animati quando visualizzata. Usare il `StartAnimation` metodo per avviare l'animazione quando vengono visualizzati. Ad esempio:

```csharp
Indeterminate.StartAnimation (this);
AsyncProgress.StartAnimation (this);
```

La chiamata di `StopAnimation` metodo arresterà l'animazione.

<a name="Working_with_Text_Controls" />

## <a name="working-with-text-controls"></a>Utilizzo di controlli testo

AppKit offre diversi tipi di controlli di testo che può essere utilizzato nella progettazione dell'interfaccia utente. Per altre informazioni, vedere la [controlli di testo](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsText.html#//apple_ref/doc/uid/20000957-CH51-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/text01.png "Controlli di testo di esempio")](standard-controls-images/text01.png#lightbox)

Per i campi di testo (`NSTextField`), i seguenti eventi possono essere usati per rilevare l'interazione dell'utente:

- **Modificato** -viene generato ogniqualvolta l'utente modifica il valore del campo. Ad esempio, per ogni carattere digitato.
- **EditingBegan** -viene generato quando l'utente seleziona il campo per la modifica.
- **EditingEnded** : quando l'utente preme il tasto INVIO nel campo o lascia il campo.

Usare il `StringValue` proprietà leggerne o impostarne il valore del campo. Ad esempio:

```csharp
FeedbackLabel.StringValue = string.Format("User ID: {0}",UserField.StringValue);
```

Per i campi di cui visualizzare o modificare i valori numerici, è possibile usare il `IntValue` proprietà. Ad esempio:

```csharp
FeedbackLabel.StringValue = string.Format("Number: {0}",NumberField.IntValue);
```

Un `NSTextView` fornisce un full-text in primo piano modifica e la visualizzazione di area con formattazione predefinita. Ad esempio un `NSTextField`, usare il `StringValue` proprietà da leggere o impostare il valore dell'area.

Per un esempio di un esempio complesso dell'uso delle visualizzazioni di testo in un'app xamarin. Mac, vedere la [App di esempio SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter è un editor di codice sorgente semplice che offre supporto per il completamento del codice e informazioni sulla sintassi.

Il codice SourceWriter include tutti i commenti e, dove possibile, sono stati indicati i collegamenti delle tecnologie e dei metodi principali alle informazioni corrispondenti nelle guide di Xamarin.Mac.

<a name="Working_with_Content_Views" />

## <a name="working-with-content-views"></a>Utilizzo delle visualizzazioni del contenuto

AppKit offre diversi tipi di visualizzazioni di contenuto che può essere utilizzato nella progettazione dell'interfaccia utente. Per altre informazioni, vedere la [viste contenuto](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

[![](standard-controls-images/content01.png "Una visualizzazione del contenuto di esempio")](standard-controls-images/content01.png#lightbox)

<a name="Popovers" />

### <a name="popovers"></a>Popovers

Nel popover è un elemento dell'interfaccia utente temporaneo che offre funzionalità che è direttamente correlata a uno specifico un controllo o un'area sullo schermo. Nel popover nella parte superiore della finestra che contiene il controllo o l'area in cui è collegato a e il bordo include una freccia per indicare il punto da cui è emerso.

Per creare nel popover, eseguire le operazioni seguenti:

1. Aprire il `.storyboard` file della finestra che si desidera aggiungere nel popover a facendo doppio clic su esso nel **Esplora soluzioni**
2. Trascinare un **visualizzare Controller** dalle **Library Inspector** nel **Editor dell'interfaccia**: 

    [![](standard-controls-images/content02.png "Quando si seleziona un Controller di visualizzazione dalla libreria")](standard-controls-images/content02.png#lightbox)
3. Definire le dimensioni e il layout dei **vista personalizzata**: 

    [![](standard-controls-images/content04.png "Modifica del layout")](standard-controls-images/content04.png#lightbox)
4. CTRL + clic e trascinare l'origine della finestra popup nel **Controller visualizzazione**: 

    [![](standard-controls-images/content05.png "Trascinamento per la creazione di un elemento segue")](standard-controls-images/content05.png#lightbox)
5. Selezionare **nel Popover** dal menu a comparsa: 

    [![](standard-controls-images/content06.png "Impostare il tipo di elemento segue")](standard-controls-images/content06.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

<a name="Tab_Views" />

### <a name="tab-views"></a>Visualizzazioni scheda

Visualizzazioni scheda è costituito da un elenco di schede (che ha un aspetto simile a un controllo Segmented) combinato con un set di viste che vengono chiamati _riquadri_. Quando l'utente seleziona una nuova scheda, verrà visualizzato il riquadro associato a esso. Ogni riquadro contiene un proprio set di controlli.

Quando si lavora con una visualizzazione scheda in Interface Builder di Xcode, usare il **Inspector attributo** per impostare il numero di schede:

[![](standard-controls-images/content08.png "Modifica il numero di schede")](standard-controls-images/content08.png#lightbox)

Selezionare in ogni scheda di **gerarchia delle interfacce** per impostare relativo **titolo** e aggiungere elementi dell'interfaccia utente per relativo **riquadro**:

[![](standard-controls-images/content09.png "Modifica le schede in Xcode")](standard-controls-images/content09.png#lightbox)

<a name="Data_Binding_AppKit_Controls" />

## <a name="data-binding-appkit-controls"></a>Controlli AppKit associazione dati

Utilizzando le tecniche di codifica di chiave-valore e un Data Binding nell'applicazione xamarin. Mac, è possibile ridurre la quantità di codice che è necessario scrivere e mantenere per popolare e lavorare con gli elementi dell'interfaccia utente. È anche il vantaggio di un'ulteriore separazione dei dati di backup (_modello di dati_) dal front end interfaccia utente (_Model-View-Controller_), sfociando in più facilmente gestibile, più flessibile dell'applicazione progettazione.

Dati XML di codifica (i, chiave-valore KVM) è un meccanismo per l'accesso a proprietà di un oggetto indirettamente, tramite chiavi (stringhe di formattazione speciale) per identificare le proprietà anziché accedervi tramite le variabili di istanza o metodi di accesso (`get/set`). Grazie all'implementazione chiave-valore di codifica compatibili con funzioni di accesso nell'applicazione xamarin. Mac, è possibile accedere ad altre funzionalità macOS come chiave-valore osservando (KVO), Data Binding, Core Data, le associazioni di Cocoa e scriptability.

Per altre informazioni, vedere la [Data Binding semplice](~/mac/app-fundamentals/databinding.md#Simple_Data_Binding) sezione del nostro [Data Binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md) documentazione.


<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato di utilizzo di controlli AppKit standard, ad esempio pulsanti, etichette, campi di testo, caselle di controllo e controlli segmentati in un'applicazione xamarin. Mac. Illustrato aggiungerli a una progettazione dell'interfaccia utente in Interface Builder di Xcode, esponendoli al codice tramite Outlet e azioni e uso di controlli AppKit nel codice c#.

## <a name="related-links"></a>Collegamenti correlati

- [MacControls (esempio)](https://developer.xamarin.com/samples/mac/MacControls/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Sui controlli e visualizzazioni](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)
