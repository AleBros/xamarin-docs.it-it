---
title: Controlli standard
description: In questo articolo viene descritto l'utilizzo con i controlli AppKit standard, ad esempio pulsanti, etichette, campi di testo, caselle di controllo e segmentata in un'applicazione Xamarin.Mac. Viene descritto aggiungerli a un'interfaccia con il generatore di interfaccia e l'interazione con essi nel codice.
ms.topic: article
ms.prod: xamarin
ms.assetid: d2593883-d255-431f-9781-75f04d8cecea
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: e887026b4f87d2e1bf8c7647a7845765ce8b886c
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="standard-controls"></a>Controlli standard

_In questo articolo viene descritto l'utilizzo con i controlli AppKit standard, ad esempio pulsanti, etichette, campi di testo, caselle di controllo e segmentata in un'applicazione Xamarin.Mac. Viene descritto aggiungerli a un'interfaccia con il generatore di interfaccia e l'interazione con essi nel codice._

Quando si utilizza c# e .NET in un'applicazione Xamarin.Mac, è possibile accedere allo stesso AppKit i controlli che uno sviluppatore che lavora *Objective-C* e *Xcode* does. Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile utilizzare del Xcode _generatore interfaccia_ per creare e gestire i controlli Appkit (o, facoltativamente, crearli direttamente nel codice c#).

I controlli di AppKit sono gli elementi dell'interfaccia utente che consentono di creare l'interfaccia utente dell'applicazione Xamarin.Mac. Sono costituite da elementi, ad esempio pulsanti, etichette, campi di testo, caselle di controllo e controlli segmentata e causare azioni immediate o risultati visibili quando un utente manipola.

[![](standard-controls-images/intro01.png "La schermata principale di app di esempio")](standard-controls-images/intro01.png#lightbox)

In questo articolo verranno descritte le nozioni fondamentali sull'utilizzo dei controlli AppKit in un'applicazione Xamarin.Mac. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) le sezioni, come illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

Si consiglia di esaminare il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) del documento, nonché viene descritto il `Register` e `Export` comandi utilizzato per le classi c# in transito-fino a oggetti Objective-C e gli elementi dell'interfaccia utente.

<a name="Introduction_to_Controls_and_Views" />

## <a name="introduction-to-controls-and-views"></a>Introduzione ai controlli e viste

macOS (precedentemente noto come Mac OS X) fornisce un set standard di controlli dell'interfaccia utente tramite il AppKit Framework. Sono costituite da elementi, ad esempio pulsanti, etichette, campi di testo, caselle di controllo e controlli segmentata e causare azioni immediate o risultati visibili quando un utente manipola.

Tutti i controlli AppKit hanno un aspetto standard e predefinito appropriate per la maggior parte dei casi, alcuni specificare un aspetto alternativo per l'utilizzo in un'area della finestra cornice o in un _Vividezza effetto_ contesto, ad esempio un'area di intestazione laterale oppure in un Widget centro notifiche.

Quando si utilizzano controlli AppKit Apple vengono indicate le linee guida seguenti:

- Evitare di combinare le dimensioni di controlli nella stessa visualizzazione.
- In generale, evitare di ridimensionamento di controlli verticalmente.
- Utilizzare il carattere di sistema e le dimensioni del testo appropriato all'interno di un controllo.
- Usare la spaziatura tra i controlli corretta.

Per ulteriori informazioni, vedere pleas il [informazioni sui controlli e le viste](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) sezione di Apple [OS X umano linee guida sull'interfaccia](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Using_Controls_in_a_Window_Frame" />

### <a name="using-controls-in-a-window-frame"></a>Utilizzo dei controlli in una cornice di finestra

Esistono alcuni dei controlli AppKit che includono uno stile di visualizzazione che consente di includere nell'area di una finestra cornice. Per un esempio, vedere barra degli strumenti dell'applicazione di posta elettronica:

[![](standard-controls-images/mailapp.png "Una cornice di finestra Mac")](standard-controls-images/mailapp.png#lightbox)

- **Arrotondare trama pulsante** : `NSButton` con uno stile di `NSTexturedRoundedBezelStyle`.
- **Trama arrotondato segmentata controllo** : `NSSegmentedControl` con uno stile di `NSSegmentStyleTexturedRounded`.
- **Trama arrotondato segmentata controllo** : `NSSegmentedControl` con uno stile di `NSSegmentStyleSeparated`.
- **Arrotondare trama Menu a comparsa** : `NSPopUpButton` con uno stile di `NSTexturedRoundedBezelStyle`.
- **Arrotondare trama Menu a discesa** : `NSPopUpButton` con uno stile di `NSTexturedRoundedBezelStyle`.
- **Barra di ricerca** : `NSSearchField`.

Quando si utilizzano controlli AppKit in una cornice di finestra, Apple Suggerire le linee guida seguenti:

- Non utilizzare gli stili di controllo specifici di cornice della finestra nel corpo della finestra.
- Non utilizzare i controlli finestra corpo o gli stili nella cornice della finestra.

Per ulteriori informazioni, vedere pleas il [informazioni sui controlli e le viste](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) sezione di Apple [OS X umano linee guida sull'interfaccia](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Creating_a_User_Interface_in_Interface_Builder" />

## <a name="creating-a-user-interface-in-interface-builder"></a>Creazione di un'interfaccia utente in Generatore di interfaccia

Quando si crea una nuova applicazione Xamarin.Mac Cocoa, per impostazione predefinita è ottenere una finestra vuota, standard. Questo windows è definito in un `.storyboard` file automaticamente incluso nel progetto. Per modificare la progettazione di windows, nel **Esplora**, fare doppio clic il `Main.storyboard` file:

[![](standard-controls-images/edit01.png "Selezione Storyboard principale in Esplora soluzioni")](standard-controls-images/edit01.png#lightbox)

La progettazione della finestra verrà aperta in Generatore del Xcode di interfaccia:

[![](standard-controls-images/edit02.png "Modifica lo storyboard in Xcode")](standard-controls-images/edit02.png#lightbox)

Per creare un'interfaccia utente, si sarà trascinare elementi dell'interfaccia utente (AppKit controlla) il **controllo libreria** per il **Editor dell'interfaccia** in Generatore di interfaccia. Nell'esempio seguente, un **verticale doppia visualizzazione** controllo è stato somministrato il farmaco dal **libreria controllo** e posizionato sulla finestra il **Editor dell'interfaccia**:

[![](standard-controls-images/edit03.png "Selezione di una visualizzazione dalla libreria")](standard-controls-images/edit03.png#lightbox)

Per ulteriori informazioni sulla creazione di un'interfaccia utente in Generatore di interfaccia, vedere il nostro [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) documentazione.

<a name="Sizing_and_Positioning" />

### <a name="sizing-and-positioning"></a>Ridimensionamento e posizionamento

Una volta un controllo è stato incluso nell'interfaccia utente, utilizzare il **editor vincoli di** per impostare la posizione e dimensioni immettendo manualmente i valori e controllare la modalità con cui il controllo viene automaticamente posizionato e ridimensionato quando la finestra padre o la vista ridimensionamento:

[![](standard-controls-images/edit04.png "Impostazione di vincoli")](standard-controls-images/edit04.png#lightbox)

Utilizzare il **rossi si-raggi di** all'esterno del **Autoresizing** casella per _stick_ un controllo in un percorso specificato (x, y). Ad esempio: 

[![](standard-controls-images/edit05.png "Modifica di un vincolo")](standard-controls-images/edit05.png#lightbox)

Specifica che il controllo selezionato (nel **Visualizza gerarchia** & **Editor dell'interfaccia**) verrà bloccato per la posizione superiore e destra della finestra o della vista come viene ridimensionato o spostato. 

Altri elementi delle proprietà del controllo editor, ad esempio l'altezza e larghezza:

[![](standard-controls-images/edit06.png "L'impostazione dell'altezza")](standard-controls-images/edit06.png#lightbox)

È inoltre possibile controllare l'allineamento di elementi con vincoli mediante la **allineamento Editor**:

[![](standard-controls-images/edit07.png "L'Editor di allineamento")](standard-controls-images/edit07.png#lightbox)

> [!IMPORTANT]
> A differenza di iOS in cui (0,0) è l'angolo superiore sinistro dello schermo, in macOS (0,0) è l'angolo inferiore sinistro. Questo avviene perché macOS utilizza un sistema di coordinate matematico con i valori numerici, aumentando nel valore verso l'alto e a destra. È necessario tenere in considerazione quando si inseriscono controlli AppKit in un'interfaccia utente.

<a name="Setting_a_Custom_Class" />

### <a name="setting-a-custom-class"></a>L'impostazione di una classe personalizzata

Sono disponibili ora utilizzano AppKit controlli che si sarà necessario sottoclasse e controllo esistente e creare una propria versione personalizzata di tale classe. Ad esempio, la definizione di una versione personalizzata dell'elenco di origine:

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

In cui il `[Register("SourceListView")]` istruzione espone il `SourceListView` classe per Objective-C in modo che non può essere utilizzata in Generatore di interfaccia. Per ulteriori informazioni, vedere il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento, viene spiegato il `Register` e `Export` comandi utilizzati wire backup di classi c# per gli oggetti Objective-C e gli elementi dell'interfaccia utente.

Con il codice precedente, è possibile trascinare un controllo AppKit, del tipo di base che si estende, nell'area di progettazione (nell'esempio seguente, un **elenco origine**), passare il **controllo di identità** e impostare il **classe personalizzata** per il nome che esposto a Objective-C (esempio `SourceListView`):

[![](standard-controls-images/edit10.png "L'impostazione di una classe personalizzata in Xcode")](standard-controls-images/edit10.png#lightbox)

<a name="Exposing_Outlets_and_Actions" />

### <a name="exposing-outlets-and-actions"></a>Esposizione di punti vendita e le azioni

Prima di un controllo AppKit è possibile accedere nel codice c#, deve essere esposta come un **presa** o e **azione**. Per eseguire questo selezionare il controllo specificato in uno di **gerarchia delle interfacce** o **Editor dell'interfaccia** e passare al **visualizzazione Assistente** (assicurarsi di disporre di `.h`della finestra selezionata per la modifica):

[![](standard-controls-images/edit11.png "Selezionare il file corretto da modificare")](standard-controls-images/edit11.png#lightbox)

Trascinamento del controllo da di concedere il controllo AppKit `.h` file per iniziare a creare un **presa** o **azione**:

[![](standard-controls-images/edit12.png "Trascinamento per creare un'azione o uscita")](standard-controls-images/edit12.png#lightbox)

Selezionare il tipo di esposizione a creare e assegnare il **presa** o **azione** un **nome**: 

[![](standard-controls-images/edit13.png "Configurare l'azione o uscita")](standard-controls-images/edit13.png#lightbox)


Per ulteriori informazioni sull'utilizzo di **prese** e **azioni**, vedere il [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) sezione del nostro [Introduzione a Xcode e interfaccia Generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) documentazione.

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>Sincronizzazione delle modifiche con Xcode

Quando si passa a Visual Studio per Mac da Xcode, le modifiche effettuate in Xcode verranno sincronizzate automaticamente con il progetto Xamarin.Mac.

Se si seleziona il `SplitViewController.designer.cs` nel **Esplora** sarà in grado di vedere come il **presa** e **azione** raggruppati nel codice c#:

[![](standard-controls-images/sync01.png "La sincronizzazione delle modifiche con Xcode")](standard-controls-images/sync01.png#lightbox)

Si noti come la definizione di `SplitViewController.designer.cs` file:

```csharp
[Outlet]
AppKit.NSSplitViewItem LeftController { get; set; }

[Outlet]
AppKit.NSSplitViewItem RightController { get; set; }

[Outlet]
AppKit.NSSplitView SplitView { get; set; }
```

Allineate con la definizione di `MainWindow.h` file in Xcode:

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

Come si può notare, Visual Studio per Mac è in ascolto per le modifiche di `.h` file e quindi si sincronizza automaticamente le modifiche nel rispettivo `.designer.cs` file esporli all'applicazione. È inoltre possibile notare che `SplitViewController.designer.cs` è una classe parziale, in modo che Visual Studio per Mac senza dover modificare `SplitViewController.cs ` che sovrascriverà le modifiche che sono stati apportati alla classe.

È in genere, non è necessario aprire il `SplitViewController.designer.cs` manualmente, è stata presentata esclusivamente per scopi didattici.

> [!IMPORTANT]
> Nella maggior parte dei casi, Visual Studio per Mac verrà automaticamente vedere tutte le modifiche apportate in Xcode e sincronizzarli al progetto Xamarin.Mac. Se la sincronizzazione non viene eseguita automaticamente, tornare a Xcode e quindi di nuovo in Visual Studio per Mac. Questa operazione avvia solitamente un ciclo di sincronizzazione.

<a name="Working_with_Buttons" />

## <a name="working-with-buttons"></a>Utilizzo di pulsanti

AppKit fornisce diversi tipi di pulsante che può essere utilizzato nella progettazione dell'interfaccia utente. Per ulteriori informazioni, vedere il [pulsanti](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) sezione di Apple [OS X umano linee guida sull'interfaccia](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons01.png "Un esempio dei tipi diversi di pulsante")](standard-controls-images/buttons01.png#lightbox)

Se un pulsante è stato esposto tramite un **presa**, il codice seguente risponderà a esso pressione:

```csharp
ButtonOutlet.Activated += (sender, e) => {
        FeedbackLabel.StringValue = "Button Outlet Pressed";
};
```

Per i pulsanti che sono stati esposti tramite **azioni**, `public partial` metodo verrà automaticamente creato automaticamente con il nome scelto in Xcode. Per rispondere al **azione**, completare il metodo nella classe parziale che il **azione** in cui è stato definito. Ad esempio:

```csharp
partial void ButtonAction (Foundation.NSObject sender) {
    // Do something in response to the Action
    FeedbackLabel.StringValue = "Button Action Pressed";
}
```

Per i pulsanti che hanno uno stato (come **su** e **Off**), lo stato può essere archiviato o impostato con il `State` proprietà in base al `NSCellStateValue` enum. Ad esempio:

```csharp
DisclosureButton.Activated += (sender, e) => {
    LorumIpsum.Hidden = (DisclosureButton.State == NSCellStateValue.On);
};
```

Dove `NSCellStateValue` può essere:

- **In** : il pulsante è premuto o il controllo è selezionato (ad esempio un controllo in una casella di controllo).
- **Disattivare** : non viene premuto il pulsante o il controllo non è selezionato.
- **Misto** -una combinazione di **su** e **Off** stati.

<a name="Mark-a-Button-as-Default-and-Set-Key-Equivalent" />

### <a name="mark-a-button-as-default-and-set-key-equivalent"></a>Contrassegnare un pulsante predefinito e impostare una chiave equivalente

Per qualsiasi pulsante che sono stati aggiunti a una progettazione dell'interfaccia utente, è possibile contrassegnare come pulsante di _predefinito_ pulsante che verrà attivata quando l'utente preme il **invio** chiave sulla tastiera. In Mac OS, questo pulsante verrà visualizzato un colore di sfondo di colore blu per impostazione predefinita.

Per impostare un pulsante come predefinito, selezionarlo in Generatore del Xcode di interfaccia. Successivamente, nel **controllo attributo**, selezionare il **chiave equivalente** campo e premere il **invio** chiave:

[![](standard-controls-images/buttons03.png "Modifica la chiave equivalente")](standard-controls-images/buttons03.png#lightbox)

Allo stesso modo, è possibile assegnare qualsiasi sequenza di tasti che può essere utilizzata per attivare il pulsante utilizzando la tastiera anziché il mouse. Ad esempio, premendo i tasti di comando C nell'immagine precedente.

Quando si esegue l'app e la finestra con il pulsante è chiave e con stato attivo, se l'utente preme comando-C, verrà attivata l'azione per il pulsante (come-se l'utente ha fatto clic sul pulsante).

<a name="Working_with_Checkboxes_and_Radio_Buttons" />

## <a name="working-with-checkboxes-and-radio-buttons"></a>Utilizzo di caselle di controllo e pulsanti di opzione

AppKit fornisce diversi tipi di caselle di controllo e i gruppi di pulsanti di opzione che può essere utilizzato nella progettazione dell'interfaccia utente. Per ulteriori informazioni, vedere il [pulsanti](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) sezione di Apple [OS X umano linee guida sull'interfaccia](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons02.png "Un esempio dei tipi di casella di controllo disponibili")](standard-controls-images/buttons02.png#lightbox)


Caselle di controllo e pulsanti di opzione (esposti tramite **prese**) hanno uno stato (come **su** e **Off**), lo stato può essere archiviato o impostato con la `State` proprietà in base al `NSCellStateValue` enum. Ad esempio:

```csharp
AdjustTime.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Adjust Time: {0}",AdjustTime.State == NSCellStateValue.On);
};
```

Dove `NSCellStateValue` può essere:

- **In** : il pulsante è premuto o il controllo è selezionato (ad esempio un controllo in una casella di controllo).
- **Disattivare** : non viene premuto il pulsante o il controllo non è selezionato.
- **Misto** -una combinazione di **su** e **Off** stati.

Per selezionare un pulsante in un gruppo di pulsanti di opzione, esporre il pulsante di opzione per selezionare come un **presa** e impostare il relativo `State` proprietà. Di seguito è riportato un esempio:

```csharp
partial void SelectCar (Foundation.NSObject sender) {
    TransportationCar.State = NSCellStateValue.On;
    FeedbackLabel.StringValue = "Car Selected";
}
```

Per ottenere una raccolta di pulsanti di opzione di funzionare come un gruppo e gestiscono automaticamente lo stato selezionato, creare un nuovo **azione** e collegare ogni pulsante del gruppo:

![](standard-controls-images/buttons04.png "Creazione di un'azione")

Quindi, assegnare un univoco `Tag` per ogni pulsante di opzione nella **controllo attributo**:

![](standard-controls-images/buttons05.png "La modifica di un tag di pulsante di opzione")

Salvare le modifiche e tornare a Visual Studio per Mac, aggiungere il codice per gestire il **azione** che tutti i pulsanti di opzione siano collegati a:

```csharp
partial void NumberChanged(Foundation.NSObject sender)
{
    var check = sender as NSButton;
    Console.WriteLine("Changed to {0}", check.Tag);
}
```

È possibile utilizzare il `Tag` proprietà per visualizzare il pulsante di opzione selezionato.

<a name="Working_with_Menu_Controls" />

## <a name="working-with-menu-controls"></a>Utilizzo dei controlli di Menu

AppKit fornisce diversi tipi di controlli di Menu che può essere utilizzato nella progettazione dell'interfaccia utente. Per ulteriori informazioni, vedere il [i controlli Menu](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlswithMenus.html#//apple_ref/doc/uid/20000957-CH100-SW1) sezione di Apple [OS X umano linee guida sull'interfaccia](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/menu01.png "Controlli menu di esempio")](standard-controls-images/menu01.png#lightbox)

<a name="Providing-Menu-Control-Data" />

### <a name="providing-menu-control-data"></a>Fornisce dati di controllo Menu

I controlli Menu disponibili per macOS impostabili per popolare l'elenco a discesa da un elenco interno (che può essere predefinito in Generatore di interfaccia o popolato tramite codice) o fornendo una propria origine dati esterna e personalizzati.

<a name="Working-with-Internal-Data" />

#### <a name="working-with-internal-data"></a>Utilizzo di dati interni

Inoltre, per definire gli elementi in Generatore di interfaccia, i controlli Menu (ad esempio `NSComboBox`), forniscono un set completo di metodi che consentono di aggiungere, modificare o eliminare gli elementi dall'elenco interno da essi gestiti:

- `Add` -Aggiunge un nuovo elemento alla fine dell'elenco.
- `GetItem` : Restituisce l'elemento in corrispondenza dell'indice specificato.
- `Insert` -Inserisce un nuovo elemento nell'elenco in corrispondenza della posizione specificata.
- `IndexOf` : Restituisce l'indice dell'elemento specificato.
- `Remove` -Rimuove l'elemento specificato dall'elenco.
- `RemoveAll` -Rimuove tutti gli elementi dall'elenco.
- `RemoveAt` -Rimuove l'elemento in corrispondenza dell'indice specificato.
- `Count` : Restituisce il numero di elementi nell'elenco.

> [!IMPORTANT]
> Se si utilizza un'origine dati Extern (`UsesDataSource = true`), chiamare uno dei metodi sopra riportati genererà un'eccezione.

<a name="Working-with-an-External-Data-Source" />

#### <a name="working-with-an-external-data-source"></a>Utilizzo di un'origine dati esterna

Anziché utilizzare i dati interni predefiniti per fornire le righe per il controllo Menu, facoltativamente è possibile utilizzare un'origine dati esterna e fornire il proprio archivio di backup per gli elementi (ad esempio un database SQLite).

Per utilizzare un'origine dati esterna, si creerà un'istanza dell'origine dati del controllo Menu (`NSComboBoxDataSource` , ad esempio) ed eseguire l'override di metodi diversi per fornire i dati necessari:

- `ItemCount` : Restituisce il numero di elementi nell'elenco.
- `ObjectValueForItem` : Restituisce il valore dell'elemento per un indice specificato.
- `IndexOfItem` : Restituisce l'indice per assegnare il valore dell'elemento.
- `CompletedString` : Restituisce il primo valore di elemento corrispondente per il valore dell'elemento parzialmente tipizzato. Questo metodo viene chiamato solo se è stato abilitato il completamento automatico (`Completes = true`).

Vedere il [database e caselle combinate](~/mac/app-fundamentals/databases.md#Databases-and-ComboBoxes) sezione del [si lavora con database](~/mac/app-fundamentals/databases.md) documento per altri dettagli.

<a name="Adjusting-the-Lists-Appearance" />

### <a name="adjusting-the-lists-appearance"></a>Modificare l'aspetto dell'elenco

I metodi seguenti sono disponibili per modificare l'aspetto del controllo Menu:

- `HasVerticalScroller` -Se `true`, il controllo visualizzerà una barra di scorrimento verticale. 
- `VisibleItems` -Modificare il numero di elementi visualizzati quando viene aperto il controllo. Il valore predefinito è cinque (5).
- `IntercellSpacing` -Regolare la quantità di spazio intorno a un determinato elemento fornendo un `NSSize` in cui il `Width` specifica i margini sinistro e destro e `Height` specifica lo spazio prima e dopo un elemento.
- `ItemHeight` -Specifica l'altezza di ogni elemento nell'elenco.

Per i tipi di elenco a discesa di `NSPopupButtons`, la prima voce di Menu fornisce il titolo per il controllo. Di seguito è riportato un esempio: 

[![](standard-controls-images/menu02.png "Un controllo menu di esempio")](standard-controls-images/menu02.png#lightbox)

Per modificare il titolo, esporre questo elemento come un **presa** e usare codice simile al seguente:

```csharp
DropDownSelected.Title = "Item 1";
```

<a name="Manipulating-the-Selected-Items" />

### <a name="manipulating-the-selected-items"></a>La modifica degli elementi selezionati

I metodi e le proprietà seguenti consentono di modificare gli elementi selezionati nell'elenco del controllo Menu:

- `SelectItem` -Seleziona l'elemento in corrispondenza dell'indice specificato.
- `Select` -Selezionare il valore dell'elemento specificato.
- `DeselectItem` -Deseleziona l'elemento in corrispondenza dell'indice specificato.
- `SelectedIndex` : Restituisce l'indice dell'elemento attualmente selezionato.
- `SelectedValue` : Restituisce il valore dell'elemento attualmente selezionato.

Utilizzare il `ScrollItemAtIndexToTop` per presentare l'elemento in corrispondenza dell'indice specificato nella parte superiore dell'elenco e `ScrollItemAtIndexToVisible` per scorrere l'elenco fino a quando non è visibile l'elemento in corrispondenza dell'indice specificato.

<a name="Responding to Events" />

### <a name="responding-to-events"></a>Risposta agli eventi

I controlli menu forniscono i seguenti eventi per rispondere all'interazione dell'utente:

- `SelectionChanged` -Viene chiamato quando l'utente ha selezionato un valore dall'elenco.
- `SelectionIsChanging` -Viene chiamato prima il nuovo elemento selezionato dall'utente diventa la selezione attiva.
- `WillPopup` -Viene chiamato prima che venga visualizzato l'elenco a discesa di elementi.
- `WillDismiss` -Viene chiamato prima che venga chiuso l'elenco a discesa di elementi.

Per `NSComboBox` controlli, tutti gli stessi eventi includono il `NSTextField`, ad esempio il `Changed` evento che viene chiamato ogni volta che l'utente modifica il valore del testo nella casella combinata.

Facoltativamente, è possibile rispondere di una voce di Menu dati interni definiti nel generatore di interfaccia collegando l'elemento selezionato un **azione** e utilizzare il codice seguente per rispondere alle **azione** da attivato dall'utente:

```csharp
partial void ItemOne (Foundation.NSObject sender) {
    DropDownSelected.Title = "Item 1";
    FeedbackLabel.StringValue = "Item One Selected";
}
```

Per ulteriori informazioni sull'utilizzo dei menu e controlli di Menu, vedere il nostro [menu](~/mac/user-interface/menu.md) e [pulsante popup ed elenchi a discesa](~/mac/user-interface/menu.md) documentazione.

<a name="Working_with_Selection_Controls" />

## <a name="working-with-selection-controls"></a>Utilizzo dei controlli di selezione

AppKit fornisce diversi tipi di controlli di selezione che può essere utilizzato nella progettazione dell'interfaccia utente. Per ulteriori informazioni, vedere il [controlli di selezione](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsSelection.html#//apple_ref/doc/uid/20000957-CH49-SW1) sezione di Apple [OS X umano linee guida sull'interfaccia](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/select01.png "Controlli di selezione di esempio")](standard-controls-images/select01.png#lightbox)

Esistono due modi per tenere traccia di un controllo di selezione è l'interazione dell'utente, esponendo come un **azione**. Ad esempio:

```csharp
partial void SegmentButtonPressed (Foundation.NSObject sender) {
    FeedbackLabel.StringValue = string.Format("Button {0} Pressed",SegmentButtons.SelectedSegment);
}
```

O collegando un **delegato** per il `Activated` evento. Ad esempio:

```csharp
TickedSlider.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
};
```

Per impostare o leggere il valore di un controllo di selezione, usare il `IntValue` proprietà. Ad esempio:

```csharp
FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
```

I controlli speciali (ad esempio anche colore e immagine anche) dispongono di proprietà specifiche per i tipi di valore. Di seguito è riportato un esempio:

```csharp
CollorWell.Color = NSColor.Red;
ImageWell.Image = NSImage.ImageNamed ("tag.png");

```

Il `NSDatePicker` presenta le seguenti proprietà per l'utilizzo diretto di data e ora:

- **La funzione DateValue** -il valore di data e l'ora corrente come un `NSDate`.
- **Locale** -la posizione dell'utente come un `NSLocal`.
- **TimeInterval** -il valore di ora come un `Double`.
- **Fuso orario** -fuso orario dell'utente come un `NSTimeZone`.

<a name="Working_with_Indicator_Controls" />

## <a name="working-with-indicator-controls"></a>Utilizzo dei controlli di indicatore

AppKit fornisce diversi tipi di controlli indicatore che può essere utilizzato nella progettazione dell'interfaccia utente. Per ulteriori informazioni, vedere il [controlli indicatore](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsIndicators.html#//apple_ref/doc/uid/20000957-CH50-SW1) sezione di Apple [OS X umano linee guida sull'interfaccia](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/level01.png "Controlli indicatore di esempio")](standard-controls-images/level01.png#lightbox)

Esistono due modi per rilevare quando un controllo indicatore di interazione dell'utente, da esporre come un **azione** o **presa** e collegare un **delegato** per il `Activated`evento. Ad esempio:

```csharp
LevelIndicator.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Level: {0:###}",LevelIndicator.DoubleValue);
};
```

Per leggere o impostare il valore del controllo indicatore, utilizzare il `DoubleValue` proprietà. Ad esempio:

```csharp
FeedbackLabel.StringValue = string.Format("Rating: {0:###}",Rating.DoubleValue);
```

Il Indeterminate e gli indicatori di stato asincrono deve essere animati quando visualizzata. Utilizzare il `StartAnimation` metodo per iniziare l'animazione quando vengono visualizzati. Ad esempio:

```csharp
Indeterminate.StartAnimation (this);
AsyncProgress.StartAnimation (this);
```

La chiamata di `StopAnimation` del metodo verrà interrotta l'animazione.

<a name="Working_with_Text_Controls" />

## <a name="working-with-text-controls"></a>Utilizzo dei controlli di testo

AppKit fornisce diversi tipi di controlli di testo che può essere utilizzato nella progettazione dell'interfaccia utente. Per ulteriori informazioni, vedere il [controlli testo](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsText.html#//apple_ref/doc/uid/20000957-CH51-SW1) sezione di Apple [OS X umano linee guida sull'interfaccia](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/text01.png "Controlli di testo di esempio")](standard-controls-images/text01.png#lightbox)

Per i campi di testo (`NSTextField`), gli eventi seguenti consentono di tenere traccia di interazione dell'utente:

- **Modificato** -viene generato ogniqualvolta l'utente modifica il valore del campo. Ad esempio, in ogni carattere digitato.
- **EditingBegan** -viene generato quando l'utente seleziona il campo per la modifica.
- **EditingEnded** : quando l'utente preme il tasto INVIO nel campo o lascia il campo.

Utilizzare il `StringValue` per leggere o impostare il valore del campo. Ad esempio:

```csharp
FeedbackLabel.StringValue = string.Format("User ID: {0}",UserField.StringValue);
```

Per i campi che visualizzare o modificare i valori numerici, è possibile utilizzare il `IntValue` proprietà. Ad esempio:

```csharp
FeedbackLabel.StringValue = string.Format("Number: {0}",NumberField.IntValue);
```

Un `NSTextView` fornisce un full-text in primo piano modifica e visualizza l'area con la formattazione predefinita. Ad esempio un `NSTextField`, utilizzare il `StringValue` proprietà per la lettura o impostare il valore dell'area.

Per un esempio di un esempio di utilizzo di visualizzazioni di testo in un'app Xamarin.Mac complesso, vedere il [App di esempio SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter è un editor di codice sorgente semplice che offre supporto per il completamento del codice e informazioni sulla sintassi.

Il codice SourceWriter include tutti i commenti e, dove possibile, sono stati indicati i collegamenti delle tecnologie e dei metodi principali alle informazioni corrispondenti nelle guide di Xamarin.Mac.

<a name="Working_with_Content_Views" />

## <a name="working-with-content-views"></a>Utilizzo delle visualizzazioni del contenuto

AppKit fornisce diversi tipi di visualizzazione del contenuto che può essere utilizzato nella progettazione dell'interfaccia utente. Per ulteriori informazioni, vedere il [viste contenuto](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) sezione di Apple [OS X umano linee guida sull'interfaccia](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

[![](standard-controls-images/content01.png "Una visualizzazione di contenuto di esempio")](standard-controls-images/content01.png#lightbox)

<a name="Popovers" />

### <a name="popovers"></a>Elementi costituiscono

Un popover è un elemento dell'interfaccia utente temporaneo che fornisce funzionalità che è direttamente correlata a una specifica di un controllo o un'area sullo schermo. Un popover scorre sopra la finestra che contiene il controllo o l'area in cui è collegato a e il bordo include una freccia per indicare il punto da cui è emerso.

Per creare un popover, eseguire le operazioni seguenti:

1. Aprire il `.storyboard` file della finestra che si desidera aggiungere un popover per facendo doppio clic su esso nel **Esplora soluzioni**
2. Trascinare un **visualizzare Controller** dal **controllo libreria** sul **Editor dell'interfaccia**: 

    [![](standard-controls-images/content02.png "Selezione di un Controller di visualizzazione dalla libreria")](standard-controls-images/content02.png#lightbox)
4. Definire le dimensioni e il layout del **vista personalizzata**: 

    [![](standard-controls-images/content04.png "Modifica del layout")](standard-controls-images/content04.png#lightbox)
5. CTRL + clic e trascinare l'origine del popup sul **View Controller**: 

    [![](standard-controls-images/content05.png "Trascinamento per creare un segue")](standard-controls-images/content05.png#lightbox)
6. Selezionare **Popover** dal menu di scelta rapida: 

    [![](standard-controls-images/content06.png "L'impostazione del tipo segue")](standard-controls-images/content06.png#lightbox)
7. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

<a name="Tab_Views" />

### <a name="tab-views"></a>Scheda viste

Scheda viste è costituito da un elenco della scheda (che ha un aspetto simile a un controllo segmentata) combinato con un set di viste che vengono chiamati _riquadri_. Quando l'utente seleziona una nuova scheda, verrà visualizzato nel riquadro in cui è associato. Ogni riquadro contiene un proprio set di controlli.

Quando si lavora con una scheda di visualizzazione in interfaccia generatore del Xcode, utilizzare il **controllo attributo** per impostare il numero di schede:

[![](standard-controls-images/content08.png "Modifica il numero di schede")](standard-controls-images/content08.png#lightbox)

Selezionare in ogni scheda di **gerarchia delle interfacce** impostare relativo **titolo** e aggiungere elementi dell'interfaccia utente per relativo **riquadro**:

[![](standard-controls-images/content09.png "Modifica le schede in Xcode")](standard-controls-images/content09.png#lightbox)

<a name="Data_Binding_AppKit_Controls" />

## <a name="data-binding-appkit-controls"></a>Controlli AppKit associazione dati

Utilizzando le tecniche di associazione di dati e la generazione di codice chiave-valore Xamarin.Mac nell'applicazione in uso, è possibile ridurre notevolmente la quantità di codice che è necessario scrivere e mantenere per popolare e lavorare con gli elementi dell'interfaccia utente. È inoltre il vantaggio di separazione ulteriormente i dati di backup (_modello di dati_) la parte anteriore terminare l'interfaccia utente (_Model-View-Controller_), i punti iniziali per più semplici da gestire, più flessibile dell'applicazione progettazione.

Dati XML di codifica (i, chiave-valore KVM) è un meccanismo per l'accesso a proprietà di un oggetto indirettamente, utilizzando le chiavi (stringhe di formattazione speciale) per identificare le proprietà anziché accedervi tramite le variabili di istanza o metodi di accesso (`get/set`). Implementando chiave-valore di codifica compatibile con funzioni di accesso nell'applicazione Xamarin.Mac, è possibile accedere ad altre funzionalità macOS come chiave-valore osservazione (KVO), Data Binding, i dati di base, associazioni Cocoa e scriptability.

Per ulteriori informazioni, vedere il [Data Binding semplice](~/mac/app-fundamentals/databinding.md#Simple_Data_Binding) sezione del nostro [Data Binding e la generazione di codice chiave-valore](~/mac/app-fundamentals/databinding.md) documentazione.


<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato l'utilizzo con i controlli AppKit standard, ad esempio pulsanti, etichette, campi di testo, caselle di controllo e controlli segmentati in un'applicazione Xamarin.Mac un'analisi approfondita. Descritta aggiungerli a una progettazione dell'interfaccia utente in Generatore di interfaccia di Xcode, esporle al codice tramite punti vendita e le azioni e utilizzo dei controlli AppKit nel codice c#.

## <a name="related-links"></a>Collegamenti correlati

- [MacControls (esempio)](https://developer.xamarin.com/samples/mac/MacControls/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Sui controlli e viste](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)
