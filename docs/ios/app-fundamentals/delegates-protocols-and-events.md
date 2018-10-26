---
title: Gli eventi, protocolli e delegati in xamarin. IOS
description: Questo documento descrive come usare gli eventi, protocolli e delegati in xamarin. IOS. Questi concetti di base vengono generati in fase di sviluppo di xamarin. IOS.
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/17/2017
ms.openlocfilehash: d3e5da19bacf5a6847bf6226c3fdd2b1e0e46ab2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111064"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>Gli eventi, protocolli e delegati in xamarin. IOS

Xamarin. IOS Usa i controlli per esporre gli eventi per la maggior parte delle interazioni dell'utente.
Le applicazioni xamarin. IOS usano questi eventi in modo analogo così come le applicazioni .NET tradizionali. Ad esempio, la classe UIButton xamarin. IOS è disponibile un evento denominato TouchUpInside e utilizza questo evento come se fosse questa classe e un evento in un'app .NET.

Oltre a questo approccio .NET, xamarin. IOS espone un altro modello che può essere utilizzato per l'interazione più complessa e il data binding. Questa metodologia utilizza ciò che Apple chiama i delegati e protocolli. I delegati sono concettualmente analoghi ai delegati in C#, ma invece di definizione e la chiamata a un singolo metodo, un delegato in Objective-C è un'intera classe conforme a un protocollo. Un protocollo è simile a un'interfaccia in C#, ad eccezione del fatto che i relativi metodi possono essere facoltativi. Per popolare un UITableView con i dati, ad esempio, si creerà una classe delegata che implementa i metodi definiti nel protocollo UITableViewDataSource che chiamasse il UITableView a popolarsi.

In questo articolo apprenderanno tutti questi argomenti, ti offre una base solida per la gestione di scenari di callback in xamarin. IOS, tra cui:

- **Gli eventi** : gli eventi usando .NET con controlli UIKit.
- **Protocolli** : apprendimento quali protocolli sono e come vengono usati e creazione di un esempio che fornisce i dati per un'annotazione della mappa.
- **I delegati** : apprendimento sui delegati Objective-C, estendendo l'esempio di mappa per gestire l'interazione dell'utente che include un'annotazione, quindi la differenza tra i delegati forte e vulnerabili e quando usare ognuna di queste di apprendimento.

Per illustrare i delegati e protocolli, verrà compilata un'applicazione semplice mappa che consente di aggiungere un'annotazione a una mappa, come illustrato di seguito:

[![](delegates-protocols-and-events-images/01-map-sml.png "Un esempio di un'applicazione semplice mappa che consente di aggiungere un'annotazione a una mappa")](delegates-protocols-and-events-images/01-map.png#lightbox)
[![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "un'annotazione di esempio viene aggiunta a una mappa")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Prima di affrontare questa app, è possibile iniziare esaminando gli eventi .NET sotto il UIKit.

## <a name="net-events-with-uikit"></a>Eventi di .NET con UIKit

Xamarin. IOS espone gli eventi di .NET su controlli UIKit. Ad esempio, UIButton dispone di un evento TouchUpInside, che è gestire come si farebbe normalmente in .NET, come illustrato nel codice seguente che usa un C# espressione lambda:

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

È anche possibile implementare questa operazione con un C# metodo anonimo 2.0 stile simile alla seguente:

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

Il codice precedente è collegato `ViewDidLoad` metodo il UIViewContoller. Il `aButton` variabile fa riferimento a un pulsante che è possibile aggiungere in iOS Designer o con il codice. La figura seguente mostra un pulsante che è stata aggiunta in iOS Designer:

[![](delegates-protocols-and-events-images/02-interface-builder-outlet-sml.png "Un pulsante aggiunto in iOS Designer")](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

Xamarin. IOS supporta anche lo stile delle azioni di destinazione di connessione del codice a un'interazione che si verifica con un controllo. Per creare un'azione di destinazione per il **Hello** pulsante, fare doppio clic in iOS Designer. Verrà visualizzati file code-behind del UIViewController e lo sviluppatore verrà chiesto di selezionare una posizione in cui inserire il metodo che esegue la connessione:

[![](delegates-protocols-and-events-images/03-interface-builder-action-sml.png "Il file code-behind UIViewControllers")](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

Dopo aver selezionato un percorso, un nuovo metodo di creazione e cablato fino al controllo. Nell'esempio seguente, verrà scritto un messaggio nella console quando si fa clic sul pulsante:

[![](delegates-protocols-and-events-images/05-interface-builder-action-sml.png "Un messaggio verrà scritto nella console quando si fa clic sul pulsante")](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

Per altre informazioni sul modello di azione di destinazione di iOS, vedere la sezione di azione di destinazione di [le competenze fondamentali dell'applicazione per iOS](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html) nella libreria per sviluppatori iOS di Apple.

Per altre informazioni su come usare la finestra di progettazione di iOS con xamarin. IOS, vedere la [Panoramica di progettazione iOS](~/ios/user-interface/designer/index.md) documentazione.

## <a name="events"></a>Eventi

Se si desidera intercettare gli eventi da UIControl, sono disponibili diverse opzioni: dall'uso di C# espressioni lambda e funzioni di delegato all'utilizzo di API di basso livello Objective-C.

La sezione seguente illustra come si sarebbe acquisire l'evento TouchDown su un pulsante, a seconda di quale livello di controllo è necessario.

## <a name="c-style"></a>C#Stile di visualizzazione

Usando la sintassi di delegato:

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {
    Console.WriteLine ("Touched");
};
```

Se si desidera invece che le espressioni lambda:

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

Se si desidera avere più pulsanti usano lo stesso gestore a condividere lo stesso codice:

```csharp
void handler (object sender, EventArgs args)
{
   if (sender == button1)
      Console.WriteLine ("button1");
   else
      Console.WriteLine ("some other button");
}

button1.TouchDown += handler;
button2.TouchDown += handler;
```

## <a name="monitoring-more-than-one-kind-of-event"></a>Il monitoraggio di più di un tipo di evento

Il C# gli eventi per i flag UIControlEvent dispongono di un mapping uno a uno ai singoli flag. Quando si desidera avere la stessa porzione di codice di gestire due o più eventi, usare il `UIControl.AddTarget` metodo:

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Usando la sintassi di espressione lambda:

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Se è necessario usare le funzionalità di basso livello di Objective-C, come associazione a un'istanza dell'oggetto specifico e richiamo di un selettore particolare:

```csharp
[Export ("MySelector")]
void MyObjectiveCHandler ()
{
    Console.WriteLine ("Hello!");
}

// In some other place:

button.AddTarget (this, new Selector ("MySelector"), UIControlEvent.TouchDown);
```

Si noti che, se si implementa il metodo di istanza in una classe di base ereditata, deve essere un metodo pubblico.

## <a name="protocols"></a>Protocolli

Una funzionalità del linguaggio Objective-C che fornisce un elenco di dichiarazioni di metodo è un protocollo. Ha uno scopo simile a un'interfaccia in C#, la differenza principale è che un protocollo può avere metodi facoltativi. Metodi facoltativi non vengono chiamati se la classe che adotta un protocollo non implementarli. Inoltre, una singola classe in Objective-C possa implementare più protocolli, proprio come un C# classe può implementare più interfacce.

Apple Usa protocolli di iOS per definire i contratti per le classi di adottare, mentre astrarre la classe di implementazione dal chiamante, pertanto funziona esattamente come un C# interfaccia. I protocolli vengono utilizzati sia in scenari non delegato (ad esempio con il `MKAnnotation` riportato di seguito viene indicato di seguito) e con delegati (come presentato più avanti in questo documento, nella sezione delegati).

### <a name="protocols-with-xamarinios"></a>Protocolli con xamarin. IOS

Esaminiamo un esempio di utilizzo di un protocollo Objective-C da xamarin. IOS. In questo esempio si userà il `MKAnnotation` protocollo, che fa parte di `MapKit` framework. `MKAnnotation` è un protocollo che consente a qualsiasi oggetto che adotta in modo da fornire informazioni su un'annotazione che è possibile aggiungere a una mappa. Ad esempio, un oggetto che implementa `MKAnnotation` fornisce il percorso di annotazione e il titolo è associato.

In questo modo, il `MKAnnotation` protocollo viene usato per fornire i dati pertinenti che accompagna un'annotazione. La visualizzazione reale dell'annotazione stesso è compilata dai dati nell'oggetto che adotta il `MKAnnotation` protocollo. Ad esempio, il testo per il callout in cui viene visualizzata quando l'utente tocca l'annotazione (come illustrato nello screenshot seguente) si trova il `Title` proprietà nella classe che implementa il protocollo:

 [![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "Testo di esempio per il callout quando l'utente tocca l'annotazione")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Come descritto nella sezione successiva [protocolli Deep Dive](#protocols-deep-dive), xamarin. IOS associa i protocolli di classi astratte. Per il `MKAnnotation` protocol, verrà restituito il limite C# classe è denominata `MKAnnotation` simulare il nome del protocollo che è una sottoclasse del `NSObject`, la classe base radice per cocoatouch usano. Il protocollo richiede un getter e setter per essere implementata per la coordinata; Tuttavia, un titolo e sottotitolo sono facoltativi. Pertanto, nel `MKAnnotation` (classe), il `Coordinate` proprietà è *astratto*, che ne richiede l'implementazione e la `Title` e `Subtitle` proprietà sono state contrassegnate *virtuale* , rendendoli facoltativo, come illustrato di seguito:

```csharp
[Register ("MKAnnotation"), Model ]
public abstract class MKAnnotation : NSObject
{
    public abstract CLLocationCoordinate2D Coordinate
    {
        [Export ("coordinate")]
        get;
        [Export ("setCoordinate:")]
        set;
    }

    public virtual string Title
    {
        [Export ("title")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }

    public virtual string Subtitle
    {
        [Export ("subtitle")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }
...
}
```

Qualsiasi classe può fornire i dati di annotazione semplicemente derivando da `MKAnnotation`, purché almeno le `Coordinate` proprietà viene implementata. Ecco ad esempio, una classe che accetta la coordinata nel costruttore e restituisce una stringa per il titolo di esempio:

```csharp
/// <summary>
/// Annotation class that subclasses MKAnnotation abstract class
/// MKAnnotation is bound by Xamarin.iOS to the MKAnnotation protocol
/// </summary>
public class SampleMapAnnotation : MKAnnotation
{
    string title;

    public SampleMapAnnotation (CLLocationCoordinate2D coordinate)
    {
        Coordinate = coordinate;
        title = "Sample";
    }

    public override CLLocationCoordinate2D Coordinate { get; set; }

    public override string Title {
        get {
            return title;
        }
    }
}
```

Tramite il protocollo è associato a, qualsiasi classe che rappresenti le sottoclassi `MKAnnotation` può fornire dati rilevanti che verranno utilizzati dalla mappa durante la creazione di visualizzazione dell'annotazione. Per aggiungere un'annotazione a una mappa, è sufficiente chiamare il `AddAnnotation` metodo di un `MKMapView` dell'istanza, come illustrato nel codice seguente:

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
    new CLLocationCoordinate2D (42.3467512, -71.0969456); // Boston

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

In questo caso la variabile di mappa è un'istanza di un `MKMapView`, che corrisponde alla classe che rappresenta la mappa stessa. Il `MKMapView` userà la `Coordinate` i dati derivati dal `SampleMapAnnotation` istanza per posizionare la visualizzazione di annotazione sulla mappa.

Il `MKAnnotation` protocollo fornisce un set noto di funzionalità tra gli oggetti che implementano lo, senza che il consumer (della mappa in questo caso) debba conoscere i dettagli di implementazione. In questo modo viene semplificata l'aggiunta di un'ampia gamma di possibili annotazioni a una mappa.

### <a name="protocols-deep-dive"></a>Approfondimento di protocolli

Poiché C# le interfacce non supportano metodi facoltativi, xamarin. IOS esegue il mapping di protocolli di classi astratte. Pertanto, l'adozione di un protocollo in Objective-C mediante xamarin. IOS che deriva dalla classe astratta che è associata al protocollo e implementando i metodi obbligatori. Questi metodi verranno esposti come metodi astratti della classe. Verranno associati ai metodi virtuali dei metodi facoltativi del protocollo di C# classe.

Ad esempio, ecco una parte di `UITableViewDataSource` protocollo come associata in xamarin. ios:

```csharp
public abstract class UITableViewDataSource : NSObject
{
    [Export ("tableView:cellForRowAtIndexPath:")]
    public abstract UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath);
    [Export ("numberOfSectionsInTableView:")]
    public virtual int NumberOfSections (UITableView tableView){...}
...
}
```

Si noti che la classe è astratta. Xamarin. IOS rende la classe astratta per supportare i metodi facoltativi/richiesti nei protocolli.
Tuttavia, a differenza di protocolli, Objective-C (o C# interfacce), C# le classi non supportano l'ereditarietà multipla. Questo problema riguarda la progettazione di C# il codice che usa i protocolli e in genere conduce a classi nidificate. Più informazioni su questo problema viene trattato più avanti in questo documento, nella sezione di delegati.

 `GetCell(…)` è un metodo astratto, associato a di Objective-C *selettore*, `tableView:cellForRowAtIndexPath:`, che è un metodo obbligatorio del `UITableViewDataSource` protocollo. Selettore è il termine di Objective-C per il nome del metodo. Per applicare il metodo in base alle esigenze, xamarin. IOS lo dichiara come astratto. L'altro metodo, `NumberOfSections(…)`, è associato a `numberOfSectionsInTableview:`. Questo metodo è facoltativo nel protocollo, in modo che xamarin. IOS lo dichiara come virtual, rendendo facoltativo per eseguire l'override di C#.

Xamarin. IOS si occupa di tutti i binding di iOS per l'utente. Tuttavia, se è necessario associare manualmente un protocollo di Objective-C, è possibile farlo tramite la decorazione di una classe con il `ExportAttribute`. Questo è lo stesso metodo usato da xamarin. IOS se stesso.

Per altre informazioni su come associare i tipi di Objective-C in xamarin. IOS, vedere l'articolo [Binding Objective-C tipi](~/ios/platform/binding-objective-c/index.md).

Non siamo tramite protocolli, tuttavia. Vengono usati anche in iOS come base per i delegati di Objective-C, che è l'argomento della prossima sezione.

## <a name="delegates"></a>Delegati

iOS Usa i delegati di Objective-C per implementare il modello di delega, in cui un oggetto passa lavoro a un altro. L'oggetto svolgono il lavoro è il delegato del primo oggetto. Un oggetto comunica il delegato per svolgere il lavoro mediante l'invio di che messaggi dopo che si verificano determinati eventi. Invia un messaggio simile al seguente in Objective-C è funzionalmente equivalente alla chiamata di un metodo C#. Un delegato implementa i metodi in risposta a queste chiamate e quindi fornisce funzionalità per l'applicazione.

I delegati consentono di estendere il comportamento delle classi senza la necessità di creare sottoclassi. Le applicazioni iOS utilizzano spesso i delegati quando una classe chiama nuovamente in un altro dopo che si verifica un'azione particolarmente importante. Ad esempio, il `MKMapView` classe chiamate al relativo delegato quando l'utente tocca un'annotazione su una mappa, offrendo l'autore della classe delegata la possibilità di rispondere all'interno dell'applicazione. È possibile usare un esempio di questo tipo di utilizzo di delegato più avanti in questo articolo, nell'esempio usando un delegato con xamarin. IOS.

A questo punto, si potrebbe chiedere come una classe determina quali metodi di chiamare il delegato. Si tratta di un'altra posizione in cui si usano i protocolli. In genere, i metodi disponibili per un delegato provengono i protocolli che adottano.

### <a name="how-protocols-are-used-with-delegates"></a>Uso dei protocolli con i delegati

Abbiamo visto in precedenza come protocolli vengono usati per supportare l'aggiunta di annotazioni a una mappa.
I protocolli usati anche per fornire un set noto di metodi per le classi da chiamare dopo determinati eventi si verificano, ad esempio dopo l'utente tocca un'annotazione in una mappa o seleziona una cella in una tabella. Le classi che implementano questi metodi sono noti come i delegati delle classi di chiamarle.

Le classi che supportano la delega di farlo tramite l'esposizione di una proprietà di delegato, a cui è assegnata una classe che implementa il delegato. I metodi implementati per il delegato variano a seconda di protocollo che adotta il delegato specifico. Per il `UITableView` metodo, si implementa la `UITableViewDelegate` protocollo, per il `UIAccelerometer` metodo, è necessario implementare `UIAccelerometerDelegate`, e così via per eventuali altre classi in iOS per la quale vuole espongono un delegato.

Il `MKMapView` classe visto nell'esempio precedente include anche una proprietà denominata delegato, che verrà chiamato dopo che si verificano diversi eventi. Il delegato `MKMapView` JE typu `MKMapViewDelegate`.
Si userà questo a breve in un esempio per rispondere all'annotazione dopo la selezione, ma prima è possibile discutere la differenza tra i delegati forte e vulnerabile.

### <a name="strong-delegates-vs-weak-delegates"></a>Visual Studio delegati sicuro. Delegati deboli

I delegati che abbiamo esaminato finora sono delegati sicuri, vale a dire che sono fortemente tipizzate. I binding xamarin. IOS dotato di una classe fortemente tipizzata per ogni protocollo di delegato in iOS. Tuttavia, iOS ha anche il concetto di un delegato debole. Crea una sottoclasse di una classe associata al protocollo Objective-C per un determinato delegato, invece di iOS consente inoltre di scegliere di associare i metodi del protocollo nelle classi si desidera che deriva da NSObject, associando al metodo con ExportAttribute e quindi Fornisce i selettori appropriati.
Quando si adotta questo approccio, un'istanza della classe assegnare alla proprietà WeakDelegate invece che alla proprietà del delegato. Un delegato vulnerabile offre la flessibilità necessaria per sfruttare la classe delegata verso il basso di una gerarchia di ereditarietà diversi. Esaminiamo un esempio di xamarin. IOS che usa i delegati sia forte e vulnerabili.

### <a name="example-using-a-delegate-with-xamarinios"></a>Esempio di utilizzo di un delegato con xamarin. IOS

Per eseguire codice in risposta all'utente se si tocca l'annotazione nel nostro esempio, è possibile creare una sottoclasse `MKMapViewDelegate` e assegnare un'istanza per il `MKMapView`del `Delegate` proprietà. Il `MKMapViewDelegate` protocollo contiene solo i metodi facoltativi.
Pertanto, tutti i metodi sono virtuali sono associati a questo protocollo in di xamarin. IOS `MKMapViewDelegate` classe. Quando l'utente seleziona un'annotazione, la `MKMapView` istanza invierà il `mapView:didSelectAnnotationView:` messaggio al relativo delegato. Per risolvere il problema in xamarin. IOS, è necessario eseguire l'override di `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` metodo nella sottoclasse MKMapViewDelegate simile al seguente:

```csharp
public class SampleMapDelegate : MKMapViewDelegate
{
    public override void DidSelectAnnotationView (
        MKMapView mapView, MKAnnotationView annotationView)
    {
        var sampleAnnotation =
            annotationView.Annotation as SampleMapAnnotation;

        if (sampleAnnotation != null) {

            //demo accessing the coordinate of the selected annotation to
            //zoom in on it
            mapView.Region = MKCoordinateRegion.FromDistance(
                sampleAnnotation.Coordinate, 500, 500);

            //demo accessing the title of the selected annotation
            Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
        }
    }
}
```

La classe SampleMapDelegate illustrata in precedenza viene implementata come una classe annidata nel controller che contiene il `MKMapView` istanza. In Objective-C, si noteranno spesso il controller di adottare più protocolli direttamente all'interno della classe. Tuttavia, poiché protocolli associati alle classi in xamarin. IOS, le classi che implementano i delegati fortemente tipizzati vengono in genere incluse come le classi annidate.

Con l'implementazione della classe delegato posto, è sufficiente creare un'istanza del delegato nel controller e assegnarlo al `MKMapView`del `Delegate` proprietà come illustrato di seguito:

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    SampleMapDelegate _mapDelegate;
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        //set the map's delegate
        _mapDelegate = new SampleMapDelegate ();
        map.Delegate = _mapDelegate;
        ...
    }
    class SampleMapDelegate : MKMapViewDelegate
    {
        ...
    }
}
```

Per usare un delegato debole per ottenere lo stesso risultato, è necessario associare il metodo in qualsiasi classe che deriva da `NSObject` e assegnarlo al `WeakDelegate` proprietà del `MKMapView`. Poiché il `UIViewController` classi derivano dalla `NSObject` (ad esempio, ogni classe Objective-C in CocoaTouch), possiamo implementare semplicemente un metodo associato a `mapView:didSelectAnnotationView:` direttamente nel controller e assegnare al controller di `MKMapView`del `WeakDelegate`, evitando la necessità per la classe annidata extra. Il codice seguente illustra questo approccio:

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        //assign the controller directly to the weak delegate
        map.WeakDelegate = this;
    }
    //bind to the Objective-C selector mapView:didSelectAnnotationView:
    [Export("mapView:didSelectAnnotationView:")]
    public void DidSelectAnnotationView (MKMapView mapView,
        MKAnnotationView annotationView)
    {
        ...
    }
}
```

Quando si esegue questo codice, l'applicazione si comporta esattamente come quando si esegue la versione fortemente tipizzata di delegato. Il vantaggio da questo codice è che il delegato debole non richiede la creazione della classe aggiuntiva che è stata creata quando è stato usato il delegato fortemente tipizzato. Tuttavia, ciò alle spese indipendenza dai tipi. Se si scegliessero commette un errore nel selettore di cui è stato passato al `ExportAttribute`, non si troverà fino al runtime.

### <a name="events-and-delegates"></a>Eventi e delegati

I delegati vengono usati per i callback di iOS in modo analogo al modo in cui che .NET usa gli eventi. Per rendere iOS API e il modo in cui utilizzano i delegati di Objective-C possono sembrare più, ad esempio .NET, xamarin. IOS espone gli eventi di .NET in molte posizioni in cui i delegati vengono usati in iOS.

Ad esempio, l'implementazione precedente in cui il `MKMapViewDelegate` ha risposto a un'annotazione selezionata poteva essere distribuita anche in xamarin. IOS mediante un evento .NET. In tal caso, l'evento potrebbe essere definito in `MKMapView` e chiamato `DidSelectAnnotationView`. Sarebbe stato necessario un `EventArgs` sottoclasse del tipo `MKMapViewAnnotationEventsArgs`. Il `View` proprietà di `MKMapViewAnnotationEventsArgs` rappresenterebbe un riferimento alla vista annotazione, da cui è stato possibile procedere con la stessa implementazione era in precedenza, come illustrato di seguito:

```csharp
map.DidSelectAnnotationView += (s,e) => {
    var sampleAnnotation = e.View.Annotation as SampleMapAnnotation;
    if (sampleAnnotation != null) {
        //demo accessing the coordinate of the selected annotation to
        //zoom in on it
        mapView.Region = MKCoordinateRegion.FromDistance (
            sampleAnnotation.Coordinate, 500, 500);

        //demo accessing the title of the selected annotation
        Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
    }
};
```

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare gli eventi, protocolli e delegati in xamarin. IOS. Abbiamo visto come xamarin. IOS espone eventi di stile .NET normale per i controlli.
Successivamente abbiamo appreso come protocolli di Objective-C, tra cui come sono diversi da C# le interfacce e le modalità utilizza xamarin. IOS. Infine, abbiamo esaminato i delegati di Objective-C da una prospettiva di xamarin. IOS. Abbiamo visto come xamarin. IOS supporta sia fortemente scarsamente tipizzati e delegati come associare gli eventi di .NET per delegare i metodi.

## <a name="related-links"></a>Collegamenti correlati

- [Protocolli, delegati ed eventi (esempio)](https://developer.xamarin.com/samples/Protocols_Delegates_Events/)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Tipi di binding Objective-C](~/ios/platform/binding-objective-c/index.md)
- [Il linguaggio di programmazione di Objective-C](http://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [Progettazione delle interfacce utente in Xcode 4](http://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [Competenze fondamentali dell'applicazione per iOS](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
