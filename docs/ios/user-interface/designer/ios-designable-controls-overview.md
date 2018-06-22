---
title: Controlli personalizzati nella finestra di progettazione di Xamarin per iOS
description: La finestra di progettazione Xamarin iOS supporta il rendering di controlli personalizzato creato nel progetto o a cui fa riferimento da origini esterne, ad esempio l'archivio di componenti di Xamarin.
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 113fab2fd0d1a055d566606885cefbafe3185529
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30782184"
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>Controlli personalizzati nella finestra di progettazione di Xamarin per iOS

_La finestra di progettazione Xamarin iOS supporta il rendering di controlli personalizzato creato nel progetto o a cui fa riferimento da origini esterne, ad esempio l'archivio di componenti di Xamarin._

La finestra di progettazione di Xamarin per iOS è un potente strumento per la visualizzazione di un'interfaccia utente e fornisce supporto per la maggior parte delle viste di iOS e controller di visualizzazione di modifica WYSIWYG. L'app può contenere anche controlli personalizzati che estendono quelle integrate di iOS. Se questi controlli personalizzati vengono scritti con alcune indicazioni presenti, è possibile renderli da iOS progettazione, fornendo un'esperienza di modifica avanzata. Questo documento illustra le linee guida.

## <a name="requirements"></a>Requisiti

Verrà eseguito il rendering di un controllo che soddisfi i requisiti seguenti nell'area di progettazione:

1.  È una sottoclasse diretta o indiretta di [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/) o [UIViewController](https://developer.xamarin.com/api/type/UIKit.UIView/Controller). Altri [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) sottoclassi verranno visualizzata un'icona nell'area di progettazione.
2.  Ha un [RegisterAttribute](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) esporlo per Objective-C.
3.  Ha [il costruttore IntPtr obbligatorio](~/ios/internals/api-design/index.md).
4.  Implementa sia il [IComponent](https://developer.xamarin.com/api/type/System.ComponentModel.IComponent/) o presenta un [DesignTimeVisibleAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.DesignTimeVisibleAttribute/) impostata su True.

Controlli definiti nel codice che soddisfano i requisiti indicati in precedenza verranno visualizzati nella finestra di progettazione quando viene compilato il progetto per il simulatore. Per impostazione predefinita, verranno inclusi tutti i controlli personalizzati di **componenti personalizzati** sezione del **della casella degli strumenti**. Tuttavia il [CategoryAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.CategoryAttribute/) può essere applicato alla classe del controllo personalizzato per specificare una sezione diversa.

La finestra di progettazione non supporta il caricamento di librerie di terze parti Objective-C.

## <a name="custom-properties"></a>Proprietà personalizzate

Una proprietà dichiarata da un controllo personalizzato verrà visualizzato nel riquadro proprietà, se vengono soddisfatte le condizioni seguenti:

1.  La proprietà ha un getter pubblico e un set.
1.  La proprietà ha un [ExportAttribute](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) , nonché un [BrowsableAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.BrowsableAttribute/) impostata su True.
1.  Il tipo della proprietà è un tipo numerico, tipo di enumerazione, string, bool, [SizeF](https://developer.xamarin.com/api/type/System.Drawing.SizeF/), [UIColor](https://developer.xamarin.com/api/type/UIKit.UIColor/), o [UIImage](https://developer.xamarin.com/api/type/UIKit.UIImage/). Questo elenco di tipi supportati può essere espanso in futuro.


La proprietà può anche essere decorata con un [DisplayNameAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.DisplayNameAttribute/) per specificare l'etichetta visualizzata nel Pannello proprietà per tale.

## <a name="initialization"></a>Inizializzazione

Per `UIViewController` sottoclassi, si consiglia di utilizzare il [ViewDidLoad](https://developer.xamarin.com/api/member/UIKit.UIViewController.ViewDidLoad/) metodo per il codice che dipende da viste è stato creato nella finestra di progettazione.

Per `UIView` e altri `NSObject` sottoclassi, il [AwakeFromNib](https://developer.xamarin.com/api/member/Foundation.NSObject.AwakeFromNib/) metodo è la posizione consigliata per eseguire l'inizializzazione del controllo personalizzato dopo essere stato caricato dal file di layout. In questo modo tutte le proprietà personalizzate impostate nel Pannello proprietà verranno impostate quando il costruttore del controllo viene eseguito, ma verranno impostati prima `AwakeFromNib` viene chiamato:


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

Se il controllo è inoltre progettato per essere creato direttamente dal codice, si desidera creare un metodo che include il codice di inizializzazione comune, simile al seguente:

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

## <a name="property-initialization-and-awakefromnib"></a>AwakeFromNib e l'inizializzazione della proprietà

Deve prestare attenzione su quando e dove inizializzare le proprietà di essere progettate in un componente personalizzato per non sovrascrivere i valori che sono stati impostati all'interno di progettazione iOS. Ad esempio, eseguire il codice seguente:

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

Il `CustomView` componente espone un `Counter` proprietà che possono essere impostate dallo sviluppatore all'interno di progettazione iOS. Tuttavia, indipendentemente dal valore impostato nella finestra di progettazione, il valore di `Counter` proprietà sarà sempre zero (0). La ragione di questo comportamento è la seguente:

-  Un'istanza di `CustomControl` viene ingrandita dal file di Storyboard.
-  Vengono impostate le proprietà modificata nella finestra di progettazione iOS (ad esempio impostando il valore della `Counter` a due (2), ad esempio).
-  Il `AwakeFromNib` metodo viene eseguito e viene eseguita una chiamata al componente `Initialize` metodo.
-  All'interno di `Initialize` il valore di `Counter` proprietà viene reimpostata su zero (0).


Per correggere la situazione precedente, inizializzare il `Counter` proprietà in un' posizione (ad esempio il costruttore del componente) o non esegue l'override di `AwakeFromNib` metodo e chiamare `Initialize` se il componente non richiede nessuna ulteriore inizializzazione di fuori di cosa è attualmente gestita dai relativi costruttori.

## <a name="design-mode"></a>Modalità di progettazione

Nell'area di progettazione, un controllo personalizzato deve rispettare alcune restrizioni:

-  Risorse bundle dell'App non sono disponibili in modalità progettazione. Le immagini sono disponibili quando caricato tramite [UIImage metodi](https://developer.xamarin.com/api/type/UIKit.UIImage/%2fM) .
-  Operazioni asincrone, ad esempio le richieste web, non devono essere eseguite in modalità progettazione. Finestra di progettazione non supporta l'animazione o altri aggiornamenti asincroni dell'interfaccia utente del controllo.


Implementazione di un controllo personalizzato [IComponent](https://developer.xamarin.com/api/type/System.ComponentModel.IComponent/) e utilizzare il [DesignMode](https://developer.xamarin.com/api/property/System.ComponentModel.ISite.DesignMode/) proprietà per controllare se è nella finestra di progettazione. In questo esempio, l'etichetta Visualizza "Modalità di progettazione" sulla superficie di progettazione e "Runtime" in fase di esecuzione:

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
        if (Site != null &amp;&amp; Site.DesignMode)
            Text = "Design Mode";
        else
            Text = "Runtime";
    }
}
```

Verificare sempre il `Site` proprietà `null` prima di tentare di accedere a tutti i relativi membri. Se `Site` è `null`, è opportuno presupporre che il controllo non è in esecuzione nella finestra di progettazione.
Nella modalità progettazione, `Site` verrà impostata dopo l'esecuzione di costruttore del controllo e prima `AwakeFromNib` viene chiamato.

## <a name="debugging"></a>Debug

Verrà visualizzato nella casella degli strumenti e visualizzato nell'area di un controllo che soddisfi i requisiti indicati in precedenza.
Se non viene eseguito un controllo, per i bug nel controllo o una delle sue dipendenze.

Nell'area di progettazione spesso può intercettare le eccezioni generate dai singoli controlli pur continuando a eseguire il rendering di altri controlli. Il controllo viene sostituito con un segnaposto di colore rosso ed è possibile visualizzare la traccia di eccezione facendo clic sull'icona di punto esclamativo:

 ![](ios-designable-controls-overview-images/exception-box.png "Un controllo non corretto come segnaposto rosso e i dettagli dell'eccezione")

Se i simboli di debug sono disponibili per il controllo, la traccia avranno nomi di file e i numeri di riga. Doppio clic su una riga nell'analisi dello stack passerà tale riga nel codice sorgente.

Se la finestra di progettazione non è possibile isolare il controllo non corretto, verrà visualizzato un messaggio di avviso nella parte superiore dell'area di progettazione:

 ![](ios-designable-controls-overview-images/info-bar.png "Un messaggio di avviso nella parte superiore dell'area di progettazione")

Quando il controllo non corretto è fissa o rimosso dall'area di progettazione, verrà ripresa completo per il rendering.

## <a name="summary"></a>Riepilogo

In questo articolo è stato introdotto la creazione e applicazione di controlli personalizzati nella finestra di progettazione iOS. Descritta requisiti che i controlli devono soddisfare per essere visualizzate nell'area di progettazione ed esporre le proprietà personalizzate nel Pannello proprietà. Quindi esaminato il code-behind - inizializzazione del controllo e la proprietà DesignMode. Infine, descritto cosa accade quando le eccezioni vengono generate e su come risolvere il problema.