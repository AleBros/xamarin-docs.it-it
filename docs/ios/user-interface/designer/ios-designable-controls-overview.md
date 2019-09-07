---
title: Controlli personalizzati nel Xamarin Designer per iOS
description: Il Xamarin Designer per iOS supporta il rendering di controlli personalizzati creati nel progetto o a cui viene fatto riferimento da origini esterne come l'archivio componenti di Novell.
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 51afbdf79248af6f76426dd0e0c862e506a0a22f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768778"
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>Controlli personalizzati nel Xamarin Designer per iOS

_Il Xamarin Designer per iOS supporta il rendering di controlli personalizzati creati nel progetto o a cui viene fatto riferimento da origini esterne come l'archivio componenti di Novell._

Il Xamarin Designer per iOS è uno strumento potente per la visualizzazione dell'interfaccia utente di un'applicazione e fornisce il supporto per la modifica WYSIWYG per la maggior parte delle visualizzazioni iOS e dei controller di visualizzazione. L'app può anche contenere controlli personalizzati che estendono quelli incorporati in iOS. Se questi controlli personalizzati vengono scritti tenendo conto di alcune linee guida, possono anche essere sottoposti a rendering da iOS designer, offrendo un'esperienza di modifica ancora più avanzata. Questo documento esamina le linee guida.

## <a name="requirements"></a>Requisiti

Verrà eseguito il rendering di un controllo che soddisfa tutti i requisiti seguenti nell'area di progettazione:

1. Si tratta di una sottoclasse diretta o indiretta di [UIView](xref:UIKit.UIView) o [UIViewController](xref:UIKit.UIViewController). Altre sottoclassi [NSObject](xref:Foundation.NSObject) verranno visualizzate come un'icona nell'area di progettazione.
2. Dispone di un [RegisterAttribute](xref:Foundation.RegisterAttribute) per esporlo a Objective-C.
3. Dispone [del Costruttore IntPtr obbligatorio](~/ios/internals/api-design/index.md).
4. Implementa l'interfaccia [IComponent](xref:System.ComponentModel.IComponent) o ha [DesignTimeVisibleAttribute](xref:System.ComponentModel.DesignTimeVisibleAttribute) impostato su true.

I controlli definiti nel codice che soddisfano i requisiti precedenti verranno visualizzati nella finestra di progettazione quando viene compilato il progetto contenitore per il simulatore. Per impostazione predefinita, tutti i controlli personalizzati verranno visualizzati nella sezione **componenti personalizzati** della **casella degli strumenti**. Tuttavia, [CategoryAttribute](xref:System.ComponentModel.CategoryAttribute) può essere applicato alla classe del controllo personalizzato per specificare una sezione diversa.

La finestra di progettazione non supporta il caricamento di librerie Objective-C di terze parti.

## <a name="custom-properties"></a>Proprietà personalizzate

Una proprietà dichiarata da un controllo personalizzato verrà visualizzata nel pannello Proprietà se vengono soddisfatte le condizioni seguenti:

1. La proprietà ha un getter e un setter pubblici.
1. La proprietà ha un [ExportAttribute](xref:Foundation.ExportAttribute) e un [BrowsableAttribute](xref:System.ComponentModel.BrowsableAttribute) impostato su true.
1. Il tipo di proprietà è un tipo numerico, un tipo di enumerazione, String, bool, [SizeF](xref:System.Drawing.SizeF), [UIColor](xref:UIKit.UIColor)o [UIImage](xref:UIKit.UIImage). Questo elenco di tipi supportati può essere espanso in futuro.

La proprietà può anche essere decorata con un [DisplayNameAttribute](xref:System.ComponentModel.DisplayNameAttribute) per specificare l'etichetta visualizzata nel pannello Proprietà.

## <a name="initialization"></a>Inizializzazione

Per `UIViewController` le sottoclassi, è necessario usare il metodo [ViewDidLoad](xref:UIKit.UIViewController.ViewDidLoad) per il codice che dipende dalle viste create nella finestra di progettazione.

Per `UIView` e altre `NSObject` sottoclassi, il metodo [AwakeFromNib](xref:Foundation.NSObject.AwakeFromNib) è la posizione consigliata per eseguire l'inizializzazione del controllo personalizzato dopo che è stato caricato dal file di layout. Questo perché le proprietà personalizzate impostate nel pannello Proprietà non verranno impostate quando viene eseguito il costruttore del controllo, ma verranno impostate prima `AwakeFromNib` di chiamare:

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    public CustomView (IntPtr handle) : base (handle) { }

    public override void AwakeFromNib ()
    {
        // Initialize the view here.
    }
}
```

Se il controllo è anche progettato per essere creato direttamente dal codice, potrebbe essere necessario creare un metodo con codice di inizializzazione comune, simile al seguente:

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    public CustomView (IntPtr handle) : base (handle) { }

    public CustomView ()
    {
        // Called when created from code.
        Initialize ();
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        Initialize ();
    }

    void Initialize ()
    {
        // Common initialization code here.
    }
}
```

## <a name="property-initialization-and-awakefromnib"></a>Inizializzazione di proprietà e AwakeFromNib

Prestare attenzione quando e dove inizializzare le proprietà designable in un componente personalizzato in modo da non sovrascrivere i valori impostati nella finestra di progettazione di iOS. Come esempio, eseguire il codice seguente:

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    [Export ("Counter"), Browsable (true)]
    public int Counter {get; set;}

    public CustomView (IntPtr handle) : base (handle) { }

    public CustomView ()
    {
        // Called when created from code.
        Initialize ();
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        Initialize ();
    }

    void Initialize ()
    {
        // Common initialization code here.
        Counter = 0;
    }
}
```

Il `CustomView` componente espone una `Counter` proprietà che può essere impostata dallo sviluppatore all'interno di iOS designer. Tuttavia, indipendentemente dal valore impostato nella finestra di progettazione, il valore della `Counter` proprietà sarà sempre zero (0). La ragione di questo comportamento è la seguente:

- Un'istanza di `CustomControl` è inflat dal file dello storyboard.
- Tutte le proprietà modificate in iOS designer sono impostate, ad esempio impostando il valore di `Counter` su due (2).
- Il `AwakeFromNib` metodo viene eseguito e viene effettuata una chiamata al `Initialize` metodo del componente.
- All' `Initialize` interno del valore `Counter` della proprietà viene reimpostato su zero (0).

Per correggere la situazione precedente, inizializzare la `Counter` proprietà altrove (ad esempio nel costruttore del componente) o non eseguire l'override `AwakeFromNib` del metodo e `Initialize` chiamare se il componente non richiede ulteriori inizializzazioni all'esterno di è attualmente gestito dai relativi costruttori.

## <a name="design-mode"></a>Modalità progettazione

Nell'area di progettazione un controllo personalizzato deve rispettare alcune restrizioni:

- Le risorse del bundle dell'app non sono disponibili in modalità progettazione. Le immagini sono disponibili quando vengono caricati tramite i [Metodi UIImage](xref:UIKit.UIImage) .
- Le operazioni asincrone, ad esempio le richieste Web, non devono essere eseguite in modalità progettazione. L'area di progettazione non supporta l'animazione o altri aggiornamenti asincroni dell'interfaccia utente del controllo.

Un controllo personalizzato può implementare [IComponent](xref:System.ComponentModel.IComponent) e utilizzare la proprietà [DesignMode](xref:System.ComponentModel.ISite.DesignMode) per verificare se è nell'area di progettazione. In questo esempio, l'etichetta visualizzerà "modalità progettazione" nell'area di progettazione e "Runtime" in fase di esecuzione:

```csharp
[Register ("DesignerAwareLabel")]
public class DesignerAwareLabel : UILabel, IComponent {

    #region IComponent implementation

    public ISite Site { get; set; }
    public event EventHandler Disposed;

    #endregion

    public DesignerAwareLabel (IntPtr handle) : base (handle) { }

    public override void AwakeFromNib ()
    {
        if (Site != null && Site.DesignMode)
            Text = "Design Mode";
        else
            Text = "Runtime";
    }
}
```

Prima di tentare di accedere `Site` ai relativi membri, è sempre necessario controllare la proprietà. `null` Se `Site` è`null`, è possibile presupporre che il controllo non sia in esecuzione nella finestra di progettazione.
In modalità progettazione, `Site` verrà impostato dopo l'esecuzione del costruttore del controllo e prima `AwakeFromNib` della chiamata a.

## <a name="debugging"></a>Debug

Un controllo che soddisfa i requisiti precedenti verrà visualizzato nella casella degli strumenti ed eseguito il rendering sulla superficie.
Se non viene eseguito il rendering di un controllo, verificare la presenza di bug nel controllo o una delle relative dipendenze.

L'area di progettazione può spesso rilevare le eccezioni generate dai singoli controlli pur continuando a eseguire il rendering di altri controlli. Il controllo difettoso viene sostituito con un segnaposto rosso ed è possibile visualizzare la traccia delle eccezioni facendo clic sull'icona di punto esclamativo:

 ![](ios-designable-controls-overview-images/exception-box.png "Un controllo difettoso come segnaposto rosso e dettagli dell'eccezione")

Se i simboli di debug sono disponibili per il controllo, la traccia avrà i nomi file e i numeri di riga.
Se si fa doppio clic su una riga nell'analisi dello stack, si passerà a tale riga nel codice sorgente.

Se la finestra di progettazione non è in grado di isolare il controllo difettoso, verrà visualizzato un messaggio di avviso nella parte superiore dell'area di progettazione:

 ![](ios-designable-controls-overview-images/info-bar.png "Un messaggio di avviso nella parte superiore dell'area di progettazione")

Il rendering completo verrà ripreso quando il controllo difettoso viene corretto o rimosso dall'area di progettazione.

## <a name="summary"></a>Riepilogo

In questo articolo è stata introdotta la creazione e l'applicazione di controlli personalizzati in iOS designer. In primo luogo, descrive i requisiti che i controlli devono soddisfare per essere sottoposti a rendering nell'area di progettazione ed esporre proprietà personalizzate nel pannello Proprietà. Viene quindi analizzato il codice alla base dell'inizializzazione del controllo e della proprietà DesignMode. Infine, descrive cosa accade quando vengono generate eccezioni e come risolverlo.
