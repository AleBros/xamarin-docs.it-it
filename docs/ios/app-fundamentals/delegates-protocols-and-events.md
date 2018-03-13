---
title: Protocolli, delegati ed eventi
description: "In questo articolo presenta le tecnologie di iOS chiave utilizzate per ricevere i callback e compilare i controlli dell'interfaccia utente con i dati. Queste tecnologie sono gli eventi, protocolli e delegati. In questo articolo viene descritto ognuno di questi, e come ognuno è utilizzato da c#. Viene illustrato come xamarin utilizza i controlli di iOS per esporre familiarità eventi di .NET, nonché come xamarin fornisce il supporto per i concetti di Objective-C, quali protocolli e delegati (delegati Objective-C non devono essere confusi con i delegati in c#). Inoltre, in questo articolo fornisce esempi che illustrano come vengono utilizzati protocolli – sia come base per i delegati Objective-C e in scenari non delegato."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 69296992c503d536a4160f172022c7ce5578812f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="events-protocols-and-delegates"></a>Protocolli, delegati ed eventi

_In questo articolo presenta le tecnologie di iOS chiave utilizzate per ricevere i callback e compilare i controlli dell'interfaccia utente con i dati. Queste tecnologie sono gli eventi, protocolli e delegati. In questo articolo viene descritto ognuno di questi, e come ognuno è utilizzato da c#. Viene illustrato come xamarin utilizza i controlli di iOS per esporre familiarità eventi di .NET, nonché come xamarin fornisce il supporto per i concetti di Objective-C, quali protocolli e delegati (delegati Objective-C non devono essere confusi con i delegati in c#). Inoltre, in questo articolo fornisce esempi che illustrano come vengono utilizzati protocolli – sia come base per i delegati Objective-C e in scenari non delegato._

Xamarin utilizza i controlli per esporre gli eventi per la maggior parte delle interazioni dell'utente.
Xamarin. IOS applicazioni utilizzano questi eventi in modo analogo a quanto tradizionali applicazioni .NET. Ad esempio, la classe di UIButton xamarin è un evento denominato TouchUpInside e utilizza questo evento, come se questa classe e un evento sono stati in un'app .NET.

Oltre a questo approccio .NET, xamarin espone un altro modello che può essere utilizzato per l'interazione di più complesso e l'associazione dati. Questa metodologia utilizza cosa Apple chiama delegati e i protocolli. I delegati sono concettualmente analoghi alle delegati in c#, ma invece di definizione e la chiamata a un singolo metodo, un delegato in Objective-C è un'intera classe conforme a un protocollo. Un protocollo è simile a un'interfaccia in c#, ad eccezione del fatto che i metodi possono essere facoltativi. Per popolare un UITableView con i dati, ad esempio, si creerà una classe delegata che implementa i metodi definiti nel protocollo UITableViewDataSource per chiamare il UITableView per popolare automaticamente.

In questo articolo apprenderanno tutti questi argomenti, offrendo una solida base per la gestione di scenari di callback in xamarin. IOS, tra cui:

-  **Eventi** : gli eventi utilizzando .NET con controlli UIKit.
-  **Protocolli** : apprendimento quali protocolli sono e le modalità di utilizzo e creazione di un esempio che fornisce dati per un'annotazione di mappa.
-  **Delegati** : apprendimento su delegati Objective-C, estendendo l'esempio di mappa per gestire l'interazione dell'utente che include un'annotazione, quindi la differenza tra i delegati complesse e semplici e quando utilizzare ognuno di questi di apprendimento.


Per illustrare un protocolli e i delegati, verrà creata un'applicazione semplice mappa che aggiunge un'annotazione a una mappa, come illustrato di seguito:

 [![](delegates-protocols-and-events-images/01-map.png "Un esempio di un'applicazione semplice mappa che aggiunge un'annotazione a una mappa") ](delegates-protocols-and-events-images/01-map.png#lightbox) [ ![ ] (delegates-protocols-and-events-images/04-annotation-with-callout.png "un'annotazione di esempio aggiunta a una mappa")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Prima di affrontare questa app, è possibile iniziare subito, esaminando gli eventi .NET sotto il UIKit.

 <a name=".NET_Events_with_UIKit" />


## <a name="net-events-with-uikit"></a>Eventi .NET con UIKit

Xamarin. IOS espone gli eventi di .NET su UIKit controlli. Ad esempio, UIButton dispone di un evento di TouchUpInside, gestire in modo analogo in .NET, come illustrato nel codice seguente che utilizza un'espressione lambda in c#:

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

È anche possibile implementare questo con un tipo 2.0 metodo anonimo c# simile alla seguente:

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

Il codice precedente è collegato nel metodo ViewDidLoad il UIViewContoller. La variabile aButton fa riferimento a un pulsante, che è possibile aggiungere nella finestra di progettazione iOS o con il codice. Nella figura seguente mostra questo pulsante viene aggiunto nella iOS progettazione, tratto dall'esempio che accompagna questo articolo:

 [![](delegates-protocols-and-events-images/02-interface-builder-outlet.png "Un pulsante aggiunto nella finestra di progettazione iOS")](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

Xamarin supporta anche lo stile delle azioni di destinazione della connessione del codice a un'interazione che si verifica con un controllo. Per creare un'azione di destinazione per il pulsante di Hello, fare doppio clic nella finestra di progettazione iOS. Verrà visualizzati i file code-behind del UIViewController e lo sviluppatore verrà richiesto di selezionare un percorso in cui inserire il metodo di connessione:

 [![](delegates-protocols-and-events-images/03-interface-builder-action.png "Il file code-behind UIViewControllers")](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

Dopo aver selezionato un percorso, un nuovo metodo di creazione e cablato fino al controllo. Nell'esempio seguente, un messaggio verrà scritto nella console quando si fa clic sul pulsante:

 [![](delegates-protocols-and-events-images/05-interface-builder-action.png "Un messaggio verrà scritto nella console quando si fa clic sul pulsante")](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

Per ulteriori informazioni sul modello di azione di destinazione di iOS, vedere la sezione di azione di destinazione di " [le competenze fondamentali di applicazione per iOS](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)" nella libreria per sviluppatori iOS di Apple.

Per ulteriori informazioni su come usare la finestra di progettazione di iOS con xamarin, vedere la " [iOS Designer Overview](~/ios/user-interface/designer/index.md)" documentazione.

 <a name="Events" />


## <a name="events"></a>Eventi

Se si desidera intercettare gli eventi da UIControl, si dispone di una gamma di opzioni: dall'utilizzo delle funzioni di delegato all'utilizzo delle API di basso livello Objective-C e le espressioni lambda in c#.

Nella sezione seguente viene illustrato come acquisite l'evento di TouchDown su un pulsante, a seconda di quanto controllo necessario.

 <a name="C#_Style" />


## <a name="c-style"></a>Stile del linguaggio c#

Utilizzando la sintassi di delegato:

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {    
    Console.WriteLine ("Touched");
};
```

Se si desidera invece le espressioni lambda:

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

Se si desidera disporre di più pulsanti utilizzano lo stesso gestore per condividere lo stesso codice:

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

<a name="Monitoring_more_than_one_kind_of_Event" />


## <a name="monitoring-more-than-one-kind-of-event"></a>Il monitoraggio di più di un tipo di evento

Gli eventi in c# per i flag UIControlEvent dispongono di un mapping uno a uno per i singoli flag. Quando si desidera avere la stessa porzione di codice di gestione di due o più eventi, utilizzare il `UIControl.AddTarget` metodo:

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Utilizzando la sintassi di espressione lambda:

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Se è necessario utilizzare le funzionalità di basso livello di Objective-C, come associazione a una particolare istanza di oggetto e richiamare un selettore particolare:

```csharp
[Export ("MySelector")]
void MyObjectiveCHandler ()
{
    Console.WriteLine ("Hello!");
}

// In some other place:

button.AddTarget (this, new Selector ("MySelector"), UIControlEvent.TouchDown);
```

Nota, se si implementa il metodo di istanza in una classe base ereditata, deve essere un metodo pubblico.

 <a name="Protocols" />


## <a name="protocols"></a>Protocolli

Una funzionalità del linguaggio Objective-C che fornisce un elenco di dichiarazioni di metodo è un protocollo. Ha uno scopo simile a un'interfaccia in c#, la differenza principale che un protocollo può avere metodi facoltativi. Se non implementa la classe che adotta un protocollo, i metodi facoltativi non vengono chiamati. Inoltre, una singola classe Objective-C può implementare più protocolli, come una classe c# può implementare più interfacce.

Apple utilizza protocolli di iOS per definire contratti per le classi di adottare, mentre astrarre la classe di implementazione da parte del chiamante, funziona pertanto come un'interfaccia in c#. I protocolli vengono utilizzati sia in scenari non delegato (ad esempio con il `MKAnnotation` riportato di seguito viene illustrato nella figura seguente) e con i delegati (come illustrato più avanti in questo documento, nella sezione delegati).

 <a name="Protocols_with_Monotouch" />


### <a name="protocols-with-xamarinios"></a>Protocolli con xamarin

Esaminiamo un esempio di utilizzo di un protocollo Objective-C da xamarin. IOS. In questo esempio utilizzeremo il `MKAnnotation` protocollo, che fa parte di `MapKit` framework. `MKAnnotation` è un protocollo che consente a qualsiasi oggetto che adotta per fornire informazioni su un'annotazione che può essere aggiunto a una mappa. Ad esempio, un oggetto che implementa `MKAnnotation` fornisce il percorso di annotazione e il titolo è associato.

In questo modo, il `MKAnnotation` viene utilizzato per fornire i dati pertinenti che accompagna un'annotazione. La visualizzazione effettiva per l'annotazione stesso viene compilata dai dati dell'oggetto che adotta il `MKAnnotation` protocollo. Ad esempio, il testo per la didascalia visualizzata quando l'utente tocca sull'annotazione (come illustrato nella schermata seguente) si trova il `Title` proprietà nella classe che implementa il protocollo:

 [![](delegates-protocols-and-events-images/04-annotation-with-callout.png "Testo di esempio per il callout quando l'utente tocca sull'annotazione")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Come descritto nella sezione successiva, protocolli di approfondimento, xamarin. IOS associa protocolli a classi astratte. Per il `MKAnnotation` protocollo, la classe c# associata denominata `MKAnnotation` per simulare il nome del protocollo e è una sottoclasse di `NSObject`, la classe base principale per CocoaTouch. Il protocollo richiede un getter e setter per essere implementato per la coordinata; Tuttavia, un titolo e sottotitolo sono facoltativi. Pertanto, nel `MKAnnotation` (classe), il `Coordinate` proprietà è *astratta*, che li richiede l'implementazione e `Title` e `Subtitle` proprietà viene contrassegnata *virtuale* , rendendole opzionali, come illustrato di seguito:

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

Qualsiasi classe può fornire dati di annotazione semplicemente derivandolo dalla `MKAnnotation`, purché almeno il `Coordinate` proprietà viene implementata. Di seguito è ad esempio, una classe che accetta la coordinata nel costruttore e restituisce una stringa per il titolo di esempio:

```csharp
/// <summary>
/// Annotation class that subclasses MKAnnotation abstract class
/// MKAnnotation is bound by Xamarin.iOS to the MKAnnotation protocol
/// </summary>
public class SampleMapAnnotation : MKAnnotation
{
    string _title;

    public SampleMapAnnotation (CLLocationCoordinate2D coordinate)
    {
        Coordinate = coordinate;
        _title = "Sample";
    }

    public override CLLocationCoordinate2D Coordinate { get; set; }

    public override string Title {
        get {
            return _title;
        }
    }
}
```

Tramite il protocollo è associato a qualsiasi classe che rappresenta una sottoclasse `MKAnnotation` può fornire dati rilevanti che verranno usati dalla mappa durante la creazione di visualizzazione dell'annotazione. Per aggiungere un'annotazione a una mappa, chiamare semplicemente il `AddAnnotation` metodo di un `MKMapView` dell'istanza, come illustrato nel codice seguente:

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
new CLLocationCoordinate2D (42.3467512, -71.0969456);

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

In questo caso la variabile di mappa è un'istanza di un `MKMapView`, che è la classe che rappresenta la mappa stessa. Il `MKMapView` utilizzerà il `Coordinate` dati derivati dal `SampleMapAnnotation` istanza per posizionare la vista di annotazione sulla mappa.

Il `MKAnnotation` protocollo fornisce un set noto di funzionalità tra tutti gli oggetti che implementano il, senza il consumer (map in questo caso) la necessità di conoscere i dettagli di implementazione. Ciò semplifica l'aggiunta di un'ampia gamma di annotazioni possibili a una mappa.

 <a name="Protocols_Deep_Dive" />


### <a name="protocols-deep-dive"></a>Protocolli di approfondimento

Poiché le interfacce in c# non supportano i metodi facoltativi, xamarin esegue il mapping di protocolli per le classi astratte. Pertanto, l'adozione di un protocollo in Objective-C mediante xamarin derivanti dalla classe astratta che è associata al protocollo e implementando i metodi richiesti. Questi metodi verranno esposti come metodi astratti della classe. I metodi facoltativi del protocollo verranno associati a metodi virtuali della classe c#.

Ad esempio, è una parte di `UITableViewDataSource` protocollo come associata in xamarin. ios:

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

Si noti che la classe è astratta. Xamarin. IOS rende la classe astratta per supportare i metodi facoltativi obbligatorie o protocolli.
Tuttavia, a differenza dei protocolli Objective-C (o le interfacce in c#), classi c# non supportano l'ereditarietà multipla. Questo problema riguarda la progettazione di codice c# che utilizza i protocolli e in genere conduce a classi annidate. Più informazioni su questo problema è trattato più avanti in questo documento, nella sezione di delegati.

 `GetCell(…)` è un metodo astratto, associato a Objective-C *selettore*, `tableView:cellForRowAtIndexPath:`, che è un metodo di richiesta del `UITableViewDataSource` protocollo. Selettore è il termine Objective-C per il nome del metodo. Per applicare il metodo in base alle esigenze, xamarin. IOS dichiara come astratto. L'altro metodo, `NumberOfSections(…)`, è associato a `numberOfSectionsInTableview:`. Questo metodo è facoltativo nel protocollo, quindi xamarin dichiara come virtuali, rendendo facoltativo per eseguire l'override in c#.

Xamarin. IOS gestisce tutti i binding di iOS per l'utente. Tuttavia, se è necessario associare manualmente un protocollo Objective-C, è possibile farlo tramite la decorazione di una classe con il `ExportAttribute`. Questo è lo stesso metodo usato da xamarin stesso.

Per ulteriori informazioni su come associare i tipi di Objective-C in xamarin. IOS, vedere l'articolo [associazione tipi Objective-C](~/ios/platform/binding-objective-c/index.md).

Non siamo tramite protocolli ancora, tuttavia. È anche utilizzati in iOS come base per i delegati Objective-C, che è l'argomento della sezione successiva.

 <a name="Delegates" />


## <a name="delegates"></a>Delegati

iOS utilizza Objective-C delegati per implementare il modello di delega, in cui un oggetto passa lavoro a un altro. L'oggetto svolgono il lavoro è il delegato del primo oggetto. Un oggetto indica il delegato per eseguire le operazioni mediante l'invio di che messaggi dopo che si verificano determinati eventi. L'invio di un messaggio simile al seguente in Objective-C è funzionalmente equivalente alla chiamata a un metodo in c#. Un delegato implementa i metodi in risposta a queste chiamate e pertanto fornisce funzionalità per l'applicazione.

I delegati consentono di estendere il comportamento delle classi senza la necessità di creare sottoclassi. Quando una classe richiama a un altro dopo che si verifica un'azione importante, le applicazioni iOS utilizzano spesso i delegati. Ad esempio, la `MKMapView` classe delle chiamate del delegato quando l'utente tocca un'annotazione su una mappa, fornendo la possibilità di rispondere all'interno dell'applicazione di autore della classe delegata. Un esempio di questo tipo di delegato utilizzo più avanti in questo articolo, nell'esempio di utilizzo è possibile utilizzare un delegato con xamarin. IOS.

A questo punto, si potrebbe chiedere come una classe determina quali metodi per chiamare il delegato. Si tratta di un'altra posizione in cui si usano i protocolli. In genere, i metodi disponibili per un delegato provengono i protocolli che adottano.

 <a name="How_Protocols_are_used_with_Delegates" />


### <a name="how-protocols-are-used-with-delegates"></a>Come i protocolli vengono utilizzati con i delegati

È stato illustrato in precedenza come protocolli vengono utilizzati per supportare l'aggiunta di annotazioni a una mappa.
I protocolli utilizzati anche per fornire un set noto di metodi per le classi da chiamare dopo alcuni eventi si verificano, ad esempio dopo le scelte utente, un'annotazione in una mappa o seleziona una cella in una tabella. Le classi che implementano questi metodi sono noti come i delegati delle classi che chiamarle.

Le classi che supportano la delega consente di eseguire l'esposizione di una proprietà di delegato, a cui è assegnata una classe che implementa il delegato. I metodi implementati per il delegato dipende dal protocollo che adotta delegato specifico. Per il `UITableView` metodo, implementare il `UITableViewDelegate` protocollo, per il `UIAccelerometer` (metodo), è necessario implementare `UIAccelerometerDelegate`, e così via per tutte le altre classi in iOS per il quale si desidera espongano un delegato.

La `MKMapView` classe è stato illustrato nell'esempio precedente include inoltre una proprietà denominata delegato, che verrà chiamato dopo che si verificano vari eventi. Il delegato per `MKMapView` è di tipo `MKMapViewDelegate`.
Lo userai a breve in un esempio di rispondere all'annotazione dopo che questa opzione è selezionata, ma prima di seguito illustrano la differenza tra i delegati complesse e semplici.

 <a name="Strong_Delegates_vs._Weak_Delegates" />


### <a name="strong-delegates-vs-weak-delegates"></a>Visual Studio delegati sicuro. Delegati deboli

I delegati che abbiamo esaminato finora sono sicuri delegati, pertanto che sono fortemente tipizzati. Le associazioni di xamarin. IOS forniscono con una classe fortemente tipizzata per ogni protocollo di delegato in iOS. Tuttavia, iOS presenta inoltre il concetto di un delegato debole. Invece di creazione di sottoclassi di una classe associata al protocollo Objective-C per un determinato delegato, iOS è inoltre possibile scegliere di associare i metodi di protocollo in desiderato di qualsiasi classe che deriva da NSObject, aggiungendo i metodi con ExportAttribute e quindi Fornisce i selettori appropriati.
Quando si adotta questo approccio, assegnare un'istanza della classe alla proprietà WeakDelegate invece che per la proprietà di delegato. Un delegato vulnerabile offre la flessibilità necessaria per richiedere la classe delegata verso il basso di una gerarchia di ereditarietà diversi. Esaminiamo un esempio di xamarin. IOS che usa i delegati sia sicuri e deboli.

 <a name="Example_using_a_Delegate_with_Xamarin.iOS" />


### <a name="example-using-a-delegate-with-xamarinios"></a>Esempio di utilizzo di un delegato con xamarin

Per eseguire codice in risposta all'utente se si tocca l'annotazione nel nostro esempio, è possibile creare una sottoclasse `MKMapViewDelegate` e assegnare un'istanza per il `MKMapView`del `Delegate` proprietà. Il `MKMapViewDelegate` protocollo contiene solo i metodi facoltativi.
Pertanto, tutti i metodi sono virtuali che sono associati a questo protocollo di xamarin `MKMapViewDelegate` classe. Quando l'utente seleziona un'annotazione, il `MKMapView` istanza invierà il `mapView:didSelectAnnotationView:` messaggio per il delegato. Per risolvere il problema in xamarin. IOS, è necessario eseguire l'override di `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` metodo nella sottoclasse MKMapViewDelegate simile al seguente:

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

La classe SampleMapDelegate illustrata in precedenza viene implementata come una classe annidata nel controller che contiene il `MKMapView` istanza. In Objective-C, spesso vedrai il controller di adottare un protocolli più direttamente all'interno della classe. Tuttavia, poiché i protocolli associati alle classi in xamarin. IOS, le classi che implementano fortemente tipizzata di delegati sono in genere incluse come classi annidate.

Con l'implementazione della classe delegato sul posto, è sufficiente creare un'istanza del delegato nel controller e assegnarlo al `MKMapView`del `Delegate` proprietà come illustrato di seguito:

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

Per utilizzare un delegato vulnerabile per eseguire la stessa operazione, è necessario associare il metodo di qualsiasi classe che deriva da `NSObject` e assegnarlo al `WeakDelegate` proprietà del `MKMapView`. Poiché il `UIViewController` classe deriva in fondo da `NSObject` (ad esempio, ogni classe Objective-C in CocoaTouch), è semplicemente possibile implementare un metodo associato a `mapView:didSelectAnnotationView:` direttamente nel controller e assegnare al controller di `MKMapView`del `WeakDelegate`, evitando la necessità di una classe annidata extra. Il codice riportato di seguito viene illustrato questo approccio:

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

Quando si esegue questo codice, l'applicazione si comporta esattamente come quando si esegue la versione fortemente tipizzata di delegato. Il vantaggio da questo codice è che il delegato debole non richiede la creazione di una classe aggiuntiva che è stata creata quando è stato usato il delegato fortemente tipizzato. Tuttavia, sono associate a scapito di indipendenza dai tipi. Se decide di commette un errore nel selettore che è stato passato il `ExportAttribute`, non sarebbe scoprire fino al runtime.

 <a name="Events_and_Delegates" />


### <a name="events-and-delegates"></a>Gli eventi e delegati

I delegati vengono utilizzati per i callback di iOS in modo analogo al modo in cui che .NET vengono utilizzati gli eventi. Per rendere iOS API e il modo in cui utilizzano delegati Objective-C sembrare più .NET, xamarin. IOS espone gli eventi di .NET in numerose posizioni in cui i delegati vengono utilizzati in iOS.

Ad esempio, l'implementazione precedente in cui il `MKMapViewDelegate` ha risposto a un'annotazione selezionata potrebbe essere implementata anche in xamarin. IOS tramite un evento .NET. In tal caso, l'evento deve essere definito nel `MKMapView` e chiamato `DidSelectAnnotationView`. Ha un `EventArgs` sottoclasse del tipo `MKMapViewAnnotationEventsArgs`. Il `View` proprietà `MKMapViewAnnotationEventsArgs` fornisce un riferimento alla vista di annotazione, da cui è possibile procedere con la stessa implementazione era in precedenza, come illustrato di seguito:

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

 <a name="Summary" />


## <a name="summary"></a>Riepilogo

In questo articolo viene descritto come utilizzare gli eventi, protocolli e i delegati in xamarin. IOS. È stato illustrato come xamarin. IOS espone gli eventi di stile normale .NET per i controlli.
Quindi abbiamo appreso protocolli Objective-C, ad esempio come sono diversi da interfacce c# e come xamarin. IOS li utilizza. Infine, abbiamo esaminato delegati Objective-C da una prospettiva di xamarin. È stato illustrato come associare gli eventi di .NET per delegare i metodi e delegati tipizzati in modo debole e come xamarin supporta sia fortemente.


## <a name="related-links"></a>Collegamenti correlati

- [Protocolli, delegati ed eventi (esempio)](https://developer.xamarin.com/samples/Protocols_Delegates_Events/)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Tipi di associazione Objective-C](~/ios/platform/binding-objective-c/index.md)
- [Il linguaggio di programmazione Objective-C](http://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [Progettazione di interfacce utente in Xcode 4](http://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [Competenze fondamentali di applicazione per iOS](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
