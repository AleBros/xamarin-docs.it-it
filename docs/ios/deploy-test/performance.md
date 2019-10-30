---
title: Prestazioni di Xamarin.iOS
description: Questo documento descrive tecniche per migliorare le prestazioni e l'uso della memoria nelle applicazioni Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 02b1f628-52d9-49de-8479-f2696546ca3f
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/29/2016
ms.openlocfilehash: bfa8c2cdcdcd6305618c0cd8e9cb69bde59b4f0b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030197"
---
# <a name="xamarinios-performance"></a>Prestazioni di Xamarin.iOS

Le prestazioni insoddisfacenti di un'applicazione si manifestano in molti modi. Può sembrare che l'applicazione non risponda, lo scorrimento diventa lento e si riduce la durata della batteria. Tuttavia, l'ottimizzazione delle prestazioni implica più della semplice implementazione di codice efficiente. Deve essere considerata anche l'esperienza dell'utente in termini di prestazioni dell'applicazione. Ad esempio, verificando che le operazioni vengano eseguite senza impedire all'utente di eseguire altre attività può contribuire a migliorare l'esperienza dell'utente.

Questo documento descrive tecniche per migliorare le prestazioni e l'uso della memoria nelle applicazioni Xamarin.iOS.

> [!NOTE]
> Prima di leggere questo articolo, è consigliabile vedere [Prestazioni multipiattaforma](~/cross-platform/deploy-test/memory-perf-best-practices.md), che illustra le tecniche non specifiche di una piattaforma che consentono di migliorare l'utilizzo della memoria e le prestazioni delle applicazioni compilate con la piattaforma Xamarin.

## <a name="avoid-strong-circular-references"></a>Evitare riferimenti circolari sicuri

In alcune situazioni è possibile creare cicli di riferimento sicuri che potrebbero impedire al Garbage Collector di recuperare la memoria degli oggetti. Si consideri, ad esempio, il caso in cui una sottoclasse derivata da [`NSObject`](xref:Foundation.NSObject), ad esempio una classe che eredita da [`UIView`](xref:UIKit.UIView), viene aggiunta a un contenitore derivato da `NSObject` e vi viene fatto riferimento in modo sicuro da Objective-C, come illustrato nell'esempio di codice seguente:

```csharp
class Container : UIView
{
    public void Poke ()
    {
    // Call this method to poke this object
    }
}

class MyView : UIView
{
    Container parent;
    public MyView (Container parent)
    {
        this.parent = parent;
    }

    void PokeParent ()
    {
        parent.Poke ();
    }
}

var container = new Container ();
container.AddSubview (new MyView (container));
```

Quando questo codice crea l'istanza di `Container`, l'oggetto C# avrà un riferimento sicuro a un oggetto Objective-C. Analogamente, anche l'istanza di `MyView` avrà un riferimento sicuro a un oggetto Objective-C.

La chiamata a `container.AddSubview` aumenterà inoltre il conteggio dei riferimenti nell'istanza non gestita di `MyView`. In questo caso, il runtime di Xamarin.iOS crea un'istanza di `GCHandle` per mantenere attivo l'oggetto `MyView` nel codice gestito, non essendoci garanzia che gli oggetti gestiti manterranno un riferimento a tale oggetto. Dal punto di vista del codice gestito, l'oggetto `MyView` verrebbe recuperato dopo la chiamata ad [`AddSubview`](xref:UIKit.UIView.AddSubview(UIKit.UIView)) se non fosse per `GCHandle`.

L'oggetto `MyView` non gestito avrà un elemento `GCHandle` che punta all'oggetto gestito, noto come *collegamento sicuro*. L'oggetto gestito conterrà un riferimento all'istanza di `Container`. L'istanza di `Container` avrà a sua volta un riferimento gestito all'oggetto `MyView`.

Nei casi in cui un oggetto contenuto mantiene un collegamento al contenitore, sono disponibili diverse opzioni per gestire il riferimento circolare:

- Interrompere manualmente il ciclo impostando il collegamento al contenitore su `null`.
- Rimuovere manualmente l'oggetto contenuto dal contenitore.
- Chiamare `Dispose` sugli oggetti.
- Evitare il riferimento circolare mantenendo un riferimento debole al contenitore. Sono disponibili altre informazioni sui riferimenti deboli.

### <a name="using-weakreferences"></a>Uso di WeakReference

Un modo per evitare un ciclo è l'uso di un riferimento debole dall'elemento figlio all'elemento padre. Ad esempio il codice riportato sopra potrebbe essere scritto nel modo seguente:

```csharp
class Container : UIView
{
    public void Poke ()
    {
        // Call this method to poke this object
    }
}

class MyView : UIView
{
    WeakReference<Container> weakParent;
    public MyView (Container parent)
    {
        this.weakParent = new WeakReference<Container> (parent);
    }

    void PokeParent ()
    {
        if (weakParent.TryGetTarget (out var parent))
            parent.Poke ();
    }
}

var container = new Container ();
container.AddSubview (new MyView (container));
```

In questo caso, l'oggetto contenuto non mantiene attivo l'elemento padre. Tuttavia l'elemento padre mantiene attivo l'elemento figlio tramite la chiamata eseguita a `container.AddSubView`.

Questo accade anche nelle API iOS che usano il modello del delegato o dell'origine dati, in cui una classe peer contiene l'implementazione. Un esempio è l'impostazione della proprietà [`Delegate`](xref:UIKit.UITableView.Delegate*)
Proprietà o metodo [`DataSource`](xref:UIKit.UITableView.DataSource*)
nella casse [`UITableView`](xref:UIKit.UITableView).

Nel caso delle classi create esclusivamente per l'implementazione di un protocollo, ad esempio [`IUITableViewDataSource`](xref:UIKit.IUITableViewDataSource), invece di creare una sottoclasse, è sufficiente implementare l'interfaccia nella classe, eseguire l'override del metodo e assegnare la proprietà `DataSource` a `this`.

#### <a name="weak-attribute"></a>Attributo Weak

In [Xamarin.iOS 11.10](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_11/xamarin.ios_11.10.md#WeakAttribute) è stato introdotto l'attributo `[Weak]`. Come `WeakReference <T>`, `[Weak]` può essere usato per interrompere [riferimenti circolari sicuri](https://docs.microsoft.com/xamarin/ios/deploy-test/performance#avoid-strong-circular-references), usando una quantità di codice ancora più ridotta.

Si consideri il codice seguente, che usa `WeakReference <T>`:

```csharp
public class MyFooDelegate : FooDelegate {
    WeakReference<MyViewController> controller;
    public MyFooDelegate (MyViewController ctrl) => controller = new WeakReference<MyViewController> (ctrl);
    public void CallDoSomething ()
    {
        MyViewController ctrl;
        if (controller.TryGetTarget (out ctrl)) {
            ctrl.DoSomething ();
        }
    }
}
```

Il codice equivalente che usa `[Weak]` è molto più conciso:

```csharp
public class MyFooDelegate : FooDelegate {
    [Weak] MyViewController controller;
    public MyFooDelegate (MyViewController ctrl) => controller = ctrl;
    public void CallDoSomething () => controller.DoSomething ();
}
```

Ecco un altro esempio d'uso di `[Weak]` nel contesto del criterio [delegation](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html):

```csharp
public class MyViewController : UIViewController
{
    WKWebView webView;

    protected MyViewController (IntPtr handle) : base (handle) { }

    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        webView = new WKWebView (View.Bounds, new WKWebViewConfiguration ());
        webView.UIDelegate = new UIDelegate (this);
        View.AddSubview (webView);
    }
}

public class UIDelegate : WKUIDelegate
{
    [Weak] MyViewController controller;

    public UIDelegate (MyViewController ctrl) => controller = ctrl;

    public override void RunJavaScriptAlertPanel (WKWebView webView, string message, WKFrameInfo frame, Action completionHandler)
    {
        var msg = $"Hello from: {controller.Title}";
        var alertController = UIAlertController.Create (null, msg, UIAlertControllerStyle.Alert);
        alertController.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
        controller.PresentViewController (alertController, true, null);
        completionHandler ();
    }
}
```

### <a name="disposing-of-objects-with-strong-references"></a>Eliminazione di oggetti con riferimenti sicuri

Se esiste un riferimento sicuro ed è difficile rimuovere la dipendenza, fare in modo che un metodo `Dispose` cancelli il puntatore padre.

Per i contenitori, eseguire l'override del metodo `Dispose` per rimuovere gli oggetti contenuti, come illustrato nell'esempio di codice seguente:

```csharp
class MyContainer : UIView
{
    public override void Dispose ()
    {
        // Brute force, remove everything
        foreach (var view in Subviews)
        {
              view.RemoveFromSuperview ();
        }
        base.Dispose ();
    }
}
```

Per un oggetto figlio che conserva il riferimento sicuro all'elemento padre, cancellare il riferimento al padre nell'implementazione `Dispose`:

```csharp
class MyChild : UIView
{
    MyContainer container;
    public MyChild (MyContainer container)
    {
        this.container = container;
    }
    public override void Dispose ()
    {
        container = null;
    }
}
```

Per altre informazioni sul rilascio di riferimenti sicuri, vedere [Release IDisposable Resources](~/cross-platform/deploy-test/memory-perf-best-practices.md#idisposable) (Rilasciare risorse IDisposable).
È anche disponibile una discussione interessante nel post di blog [Xamarin.iOS, the garbage collector and me](https://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me) (Xamarin.iOS, Garbage Collector e l'utente).

### <a name="more-information"></a>Altre informazioni

Per altre informazioni, vedere [Rules to Avoid Retain Cycles](https://www.cocoawithlove.com/2009/07/rules-to-avoid-retain-cycles.html) (Regole per evitare i cicli di conservazione) in Cocoa With Love, [Is this a bug in MonoTouch GC](https://stackoverflow.com/questions/13058521/is-this-a-bug-in-monotouch-gc) (È un bug nel Garbage Collection di MonoTouch?) in StackOverflow e [Why can't MonoTouch GC kill managed objects with refcount > 1?](https://stackoverflow.com/questions/13064669/why-cant-monotouch-gc-kill-managed-objects-with-refcount-1) (Perché il Garbage Collection di MonoTouch non riesce a terminare gli oggetti gestiti con refcount > 1?) in StackOverflow.

## <a name="optimize-table-views"></a>Ottimizzare le visualizzazioni di tabelle

Gli utenti si aspettano uno scorrimento fluido e tempi di caricamento rapidi per le istanze di [`UITableView`](xref:UIKit.UITableView). Le prestazioni dello scorrimento possono tuttavia peggiorare quando le celle contengono gerarchie di visualizzazione con molti livelli di annidamento o quando le celle contengono layout complessi. Alcune tecniche consentono tuttavia di evitare un peggioramento delle prestazioni di `UITableView`:

- Riutilizzare le celle. Per altre informazioni, vedere [Riutilizzare le celle](#reuse-cells).
- Ridurre il numero delle visualizzazioni secondarie.
- Memorizzare nella cache il contenuto delle celle recuperato da un servizio Web.
- Memorizzare nella cache l'altezza delle righe se non sono identiche.
- Rendere opache la cella ed eventuali altre visualizzazioni.
- Evitare il ridimensionamento e le sfumature delle immagini.

Nel loro insieme, queste tecniche consentono di mantenere uno scorrimento fluido per le istanze di [`UITableView`](xref:UIKit.UITableView).

### <a name="reuse-cells"></a>Riusare le celle

Se si visualizzano centinaia di righe in [`UITableView`](xref:UIKit.UITableView), sarebbe uno spreco di memoria creare centinaia di oggetti [`UITableViewCell`](xref:UIKit.UITableViewCell) quando poi ne vengono visualizzati contemporaneamente sullo schermo solo alcuni. Solo le celle visibili sullo schermo vengono invece caricate in memoria e il **contenuto** viene caricato in queste celle riutilizzate. Questo impedisce la creazione di istanze di centinaia di oggetti aggiuntivi, con conseguente risparmio di tempo e memoria.

Quando quindi una cella viene rimossa dallo schermo, la visualizzazione può essere inserita in una coda per essere riutilizzata in seguito, come illustrato nell'esempio di codice seguente:

```csharp
class MyTableSource : UITableViewSource
{
    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        // iOS will create a cell automatically if one isn't available in the reuse pool
        var cell = (MyCell) tableView.DequeueReusableCell (MyCellId, indexPath);

        // Perform required cell actions
        return cell;
    }
}
```

Mentre l'utente scorre, [`UITableView`](xref:UIKit.UITableView) chiama l'override `GetCell` per richiedere nuove visualizzazioni. Questo override chiama quindi il metodo [`DequeueReusableCell`](xref:UIKit.UITableView.DequeueReusableCell(Foundation.NSString)) e, se una cella è disponibile per essere riutilizzata, verrà restituita.

Per altre informazioni, vedere [Cell Reuse](~/ios/user-interface/controls/tables/populating-a-table-with-data.md) (Riutilizzo delle celle) in [Populating a Table with Data](~/ios/user-interface/controls/tables/populating-a-table-with-data.md) (Popolamento di una tabella con i dati).

## <a name="use-opaque-views"></a>Usare le visualizzazioni opache

Assicurarsi che la proprietà [`Opaque`](xref:UIKit.UIView.Opaque) sia impostata per tutte le visualizzazioni per cui non è definita la trasparenza. Questa opzione garantisce che il sistema di disegno esegua il rendering ottimale delle visualizzazioni. Questa funzionalità è particolarmente importante quando una visualizzazione è incorporata in [`UIScrollView`](xref:UIKit.UIScrollView) o fa parte di un'animazione complessa. In caso contrario il sistema di disegno unirà le visualizzazioni con gli altri contenuti, con il rischio di compromettere in modo significativo le prestazioni.

## <a name="avoid-fat-xibs"></a>Evitare XIB FAT

Anche se i file XIB sono stati in gran parte sostituiti dagli storyboard, in alcuni casi è possibile che vengano ancora usati. Quando un file XIB viene caricato in memoria, vengono caricati in memoria tutti i contenuti, incluse eventuali immagini. Se il file XIB contiene una visualizzazione che non viene usata immediatamente, la memoria risulta sprecata. Quando si usano i file XIB, assicurarsi quindi che sia presente un solo XIB per controller di visualizzazione e, se possibile, separare la gerarchia di visualizzazioni del controller di visualizzazione in XIB separati.

## <a name="optimize-image-resources"></a>Ottimizzare le risorse immagine

Le immagini sono tra le risorse più dispendiose usate dalle applicazioni e spesso vengono acquisite a risoluzioni elevate. Quando si visualizza un'immagine dal bundle dell'app in [`UIImageView`](xref:UIKit.UIImageView), assicurarsi che l'immagine e `UIImageView` abbiano le stesse dimensioni. Il ridimensionamento delle immagini in fase di esecuzione può essere un'operazione costosa, soprattutto se `UIImageView` è incorporato in [`UIScrollView`](xref:UIKit.UIScrollView).

Per altre informazioni, vedere [Ottimizzare le risorse immagine](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages) nella guida [Prestazioni multipiattaforma](~/cross-platform/deploy-test/memory-perf-best-practices.md).

## <a name="test-on-devices"></a>Eseguire test nei dispositivi

Iniziare la distribuzione e il test di un'applicazione su un dispositivo fisico il prima possibile. I simulatori non rispecchiano alla perfezione i comportamenti e le limitazioni dei dispositivi, quindi è importante eseguire il test in uno scenario basato su un dispositivo reale.

In particolare il simulatore non simula in alcun modo le restrizioni di memoria o CPU di un dispositivo fisico.

## <a name="synchronize-animations-with-the-display-refresh"></a>Sincronizzare le animazioni con l'aggiornamento dello schermo

I giochi hanno tendenzialmente cicli rigidi per eseguire la logica del gioco e aggiornare la schermata. Le frequenze dei fotogrammi tipiche sono comprese tra trenta e sessanta fotogrammi al secondo. Alcuni sviluppatori ritengono di dover aggiornare la schermata il maggior numero possibile di volte al secondo, combinando la simulazione del gioco con gli aggiornamenti della schermata, e potrebbero avere la tentazione di andare oltre i sessanta fotogrammi al secondo.

Il server di visualizzazione esegue tuttavia gli aggiornamenti della schermata non più di sessanta volte al secondo. Se quindi si prova ad aggiornare la schermata più velocemente, potrebbero verificarsi problemi di screen tearing e microstuttering. È meglio strutturare il codice in modo che gli aggiornamenti della schermata siano sincronizzati con l'aggiornamento della visualizzazione. A questo scopo, è possibile usare la classe [`CoreAnimation.CADisplayLink`](xref:CoreAnimation.CADisplayLink), un timer idoneo per la visualizzazione e i giochi che viene eseguito a sessanta fotogrammi al secondo.

## <a name="avoid-core-animation-transparency"></a>Evitare la trasparenza di Core Animation

Se si evita la trasparenza di Core Animation, migliorano le prestazioni di composizione delle bitmap. Se possibile, evitare in generale i livelli trasparenti e i bordi sfocati.

## <a name="avoid-code-generation"></a>Evitare la generazione di codice

La generazione dinamica di codice con `System.Reflection.Emit` o *Dynamic Language Runtime* deve essere evitata perché il kernel iOS impedisce l'esecuzione di dinamica di codice.

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto e illustrato le tecniche per incrementare le prestazioni delle applicazioni create con Xamarin.iOS. Insieme, queste tecniche possono ridurre notevolmente il carico di lavoro di una CPU e la quantità di memoria usata da un'applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Prestazioni multipiattaforma](~/cross-platform/deploy-test/memory-perf-best-practices.md)
