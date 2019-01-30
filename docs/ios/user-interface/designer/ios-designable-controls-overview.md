---
title: Controlli personalizzati nella finestra di progettazione di Xamarin per iOS
description: La finestra di progettazione di Xamarin per iOS supporta il rendering dei controlli personalizzati creati nel progetto o cui viene fatto riferimento da origini esterne, ad esempio Store il componente Xamarin.
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: c409fcc018379401c1ab40573495da12a8220c5a
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233666"
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>Controlli personalizzati nella finestra di progettazione di Xamarin per iOS

_La finestra di progettazione di Xamarin per iOS supporta il rendering dei controlli personalizzati creati nel progetto o cui viene fatto riferimento da origini esterne, ad esempio Store il componente Xamarin._

La finestra di progettazione di Xamarin per iOS è uno strumento potente per la visualizzazione dell'interfaccia utente di un'applicazione e fornisce supporto per la maggior parte delle visualizzazioni di iOS e i controller di visualizzazione di modifica WYSIWYG. L'app può contenere anche controlli personalizzati che estendono quelle integrate di iOS. Se questi controlli personalizzati sono scritti con alcune indicazioni ricordare, sia possibile anche eseguirne il rendering da iOS Designer, fornendo un'esperienza di modifica ancora più dettagliata. Questo documento esamina queste linee guida.

## <a name="requirements"></a>Requisiti

Nell'area di progettazione verrà visualizzato un controllo che soddisfi i requisiti seguenti:

1.  È una sottoclasse diretta o indiretta di [UIView](xref:UIKit.UIView) oppure [UIViewController](xref:UIKit.UIViewController). Altri [NSObject](xref:Foundation.NSObject) sottoclassi verranno visualizzato come un'icona nell'area di progettazione.
2.  Ha un [RegisterAttribute](xref:Foundation.RegisterAttribute) esporlo per Objective-C.
3.  Dispone [il costruttore IntPtr obbligatorio](~/ios/internals/api-design/index.md).
4.  Ovvero implementa il [IComponent](xref:System.ComponentModel.IComponent) o presenta un [DesignTimeVisibleAttribute](xref:System.ComponentModel.DesignTimeVisibleAttribute) impostata su True.

Controlli definiti nel codice che soddisfano i requisiti precedenti saranno visualizzati nella finestra di progettazione quando il progetto contenente viene compilato per il simulatore. Per impostazione predefinita, tutti i controlli personalizzati verranno visualizzati nel **componenti personalizzati** sezione il **della casella degli strumenti**. Tuttavia il [CategoryAttribute](xref:System.ComponentModel.CategoryAttribute) possono essere applicati alla classe del controllo personalizzato per specificare un'altra sezione.

La finestra di progettazione non supporta il caricamento di librerie Objective-C di terze parti.

## <a name="custom-properties"></a>Proprietà personalizzate

Una proprietà dichiarata da un controllo personalizzato verrà visualizzato nel pannello delle proprietà se vengono soddisfatte le condizioni seguenti:

1.  La proprietà ha un metodo pubblico get e setter.
1.  La proprietà ha un [ExportAttribute](xref:Foundation.ExportAttribute) , nonché un' [BrowsableAttribute](xref:System.ComponentModel.BrowsableAttribute) impostata su True.
1.  Il tipo della proprietà è un tipo numerico, tipo di enumerazione, string, bool, [SizeF](xref:System.Drawing.SizeF), [le proprietà UIColor](xref:UIKit.UIColor), o [UIImage](xref:UIKit.UIImage). Questo elenco di tipi supportati può essere espanso in futuro.


La proprietà può anche essere decorata con un [attributo DisplayNameAttribute](xref:System.ComponentModel.DisplayNameAttribute) per specificare l'etichetta che viene visualizzato per tale nel pannello delle proprietà.

## <a name="initialization"></a>Inizializzazione

Per la `UIViewController` sottoclassi, è consigliabile usare il [ViewDidLoad](xref:UIKit.UIViewController.ViewDidLoad) metodo per il codice che dipende a viste create nella finestra di progettazione.

Per la `UIView` e altre `NSObject` sottoclassi, il [AwakeFromNib](xref:Foundation.NSObject.AwakeFromNib) metodo è la posizione consigliata per eseguire l'inizializzazione del controllo personalizzato dopo essere stato caricato dal file di layout. Infatti, qualsiasi proprietà personalizzate impostate nel pannello delle proprietà viene impostata quando viene eseguito il costruttore del controllo, ma verranno impostati prima `AwakeFromNib` viene chiamato:


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

Se il controllo è anche progettato per essere creata direttamente dal codice, è possibile creare un metodo con codice di inizializzazione comune, simile al seguente:

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

Attenzione su quando e dove inizializzare le proprietà manipolate in un componente personalizzato per non sovrascrivere i valori che sono stati impostati all'interno di iOS Designer. Ad esempio, eseguire il codice seguente:

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

Il `CustomView` esposto dal componente un `Counter` proprietà che può essere impostata dallo sviluppatore all'interno di iOS Designer. Tuttavia, indipendentemente da quale valore viene impostato all'interno di finestra di progettazione, il valore della `Counter` proprietà sarà sempre zero (0). La ragione di questo comportamento è la seguente:

-  Un'istanza di `CustomControl` viene sottoposto a inflating dal file di Storyboard.
-  Vengono impostate le proprietà modificata nella finestra di progettazione iOS (ad esempio impostando il valore della `Counter` a due (2), ad esempio).
-  Il `AwakeFromNib` metodo viene eseguito e viene eseguita una chiamata al componente `Initialize` (metodo).
-  All'interno `Initialize` il valore della `Counter` proprietà reimpostata sul valore zero (0).


Per risolvere la situazione precedente, inizializzare il `Counter` proprietà in un' posizione (ad esempio il costruttore del componente) o non esegue l'override di `AwakeFromNib` metodo e chiamare `Initialize` se nessuna ulteriore inizializzazione di fuori di ciò che è necessario il componente è attualmente gestita da propri costruttori.

## <a name="design-mode"></a>Modalità progettazione

Nell'area di progettazione, un controllo personalizzato deve rispettare alcune restrizioni:

-  Risorse di bundle dell'App non sono disponibili in modalità progettazione. Le immagini sono disponibili quando caricate tramite [UIImage metodi](xref:UIKit.UIImage) .
-  Operazioni asincrone, ad esempio richieste web, non devono essere eseguite in modalità progettazione. L'area di progettazione non supporta l'animazione o altri aggiornamenti asincroni dell'interfaccia utente del controllo.


Implementazione di un controllo personalizzato [IComponent](xref:System.ComponentModel.IComponent) e utilizzare il [DesignMode](xref:System.ComponentModel.ISite.DesignMode) proprietà per verificare se è nell'area di progettazione. In questo esempio, l'etichetta Visualizza "Modalità di progettazione" sulla superficie di progettazione e "di Runtime" in fase di esecuzione:

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

È consigliabile controllare sempre il `Site` proprietà `null` prima di tentare di accedere a uno dei relativi membri. Se `Site` è `null`, è opportuno presupporre il controllo non è in esecuzione nella finestra di progettazione.
Nella modalità progettazione `Site` verrà impostata dopo l'esecuzione di costruttore del controllo e prima `AwakeFromNib` viene chiamato.

## <a name="debugging"></a>Debug

Verrà visualizzato nella casella degli strumenti e viene eseguito il rendering nell'area di un controllo che soddisfi i requisiti precedenti.
Se non viene eseguito un controllo, cercare bug nel controllo o una delle relative dipendenze.

L'area di progettazione spesso possibile intercettare le eccezioni generate dai controlli singoli, pur continuando a eseguire il rendering di altri controlli. Il controllo non corretto viene sostituito con un segnaposto rossa e la traccia dell'eccezione è possibile visualizzare facendo clic sull'icona di punto esclamativo:

 ![](ios-designable-controls-overview-images/exception-box.png "Un controllo non corretto come segnaposto di colore rosso e i dettagli dell'eccezione")

Se i simboli di debug sono disponibili per il controllo, la traccia avrà nomi di file e i numeri di riga. Doppio clic su una riga nell'analisi dello stack passerà alla riga nel codice sorgente.

Se la finestra di progettazione non è possibile isolare il controllo non corretto, verrà visualizzato un messaggio di avviso nella parte superiore dell'area di progettazione:

 ![](ios-designable-controls-overview-images/info-bar.png "Un messaggio di avviso nella parte superiore dell'area di progettazione")

Per il rendering completo riprende quando il controllo difettoso è fissa o rimossi dall'area di progettazione.

## <a name="summary"></a>Riepilogo

Questo articolo descrive la creazione e l'applicazione di controlli personalizzati in iOS designer. Descritto prima di tutto i requisiti che i controlli devono soddisfare per essere eseguito il rendering nell'area di progettazione ed esporre proprietà personalizzate nel pannello delle proprietà. Quindi esaminato il code-behind - inizializzazione di controllo e la proprietà DesignMode. Infine è stato descritto che cosa accade quando vengono generate eccezioni e su come risolvere il problema.