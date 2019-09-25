---
title: Eventi, protocolli e delegati in Novell. iOS
description: Questo documento descrive come usare gli eventi, i protocolli e i delegati in Novell. iOS. Questi concetti fondamentali sono universali nello sviluppo di Novell. iOS.
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/17/2017
ms.openlocfilehash: d42263733c7fa793713738be4b389eaa4850f38b
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "68649363"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>Eventi, protocolli e delegati in Novell. iOS

Novell. iOS usa i controlli per esporre gli eventi per la maggior parte delle interazioni utente.
Le applicazioni Novell. iOS utilizzano questi eventi nello stesso modo delle applicazioni .NET tradizionali. Ad esempio, la classe UIButton di Novell. iOS ha un evento denominato TouchUpInside e utilizza questo evento come se questa classe ed evento si trovasse in un'app .NET.

Oltre a questo approccio .NET, Novell. iOS espone un altro modello che può essere usato per interazioni e data binding più complesse. Questa metodologia usa le chiamate a delegati e protocolli di Apple. I delegati sono concetti simili ai C#delegati in, ma anziché definire e chiamare un singolo metodo, un delegato in Objective-C è un'intera classe conforme a un protocollo. Un protocollo è simile a un'interfaccia in C#, ad eccezione del fatto che i relativi metodi possono essere facoltativi. Quindi, ad esempio, per popolare un UITableView con i dati, è necessario creare una classe Delegate che implementi i metodi definiti nel protocollo UITableViewDataSource che il UITableView chiamerà per popolare.

In questo articolo vengono fornite informazioni su tutti questi argomenti, che offrono una solida base per la gestione degli scenari di callback in Novell. iOS, tra cui:

- **Eventi** : utilizzo di eventi .NET con controlli UIKit.
- **Protocolli** : informazioni sui protocolli e sul modo in cui vengono usati e sulla creazione di un esempio che fornisce i dati per un'annotazione della mappa.
- **Delegati** : informazioni sui delegati Objective-C estendendo l'esempio di mappa per gestire l'interazione dell'utente che include un'annotazione, quindi apprendere la differenza tra i delegati forti e deboli e quando usarli.

Per illustrare i protocolli e i delegati, verrà compilata una semplice applicazione map che aggiunge un'annotazione a una mappa, come illustrato di seguito:

[![](delegates-protocols-and-events-images/01-map-sml.png "Esempio di applicazione mappa semplice che aggiunge un'annotazione a una mappa")](delegates-protocols-and-events-images/01-map.png#lightbox)
[![]di(delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "un'annotazione di esempio aggiunta a una mappa")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Prima di affrontare questa app, è necessario iniziare esaminando gli eventi .NET in UIKit.

## <a name="net-events-with-uikit"></a>Eventi .NET con UIKit

Novell. iOS espone gli eventi .NET nei controlli UIKit. Ad esempio, UIButton ha un evento TouchUpInside, che viene gestito normalmente in .NET, come illustrato nel codice seguente che usa un' C# espressione lambda:

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

È anche possibile implementare questa operazione con C# un metodo anonimo di tipo 2,0 come questo:

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

Il codice precedente viene cablato nel `ViewDidLoad` metodo di UIViewController. La `aButton` variabile fa riferimento a un pulsante, che può essere aggiunto in iOS designer o con il codice. La figura seguente mostra un pulsante che è stato aggiunto in iOS designer:

[![](delegates-protocols-and-events-images/02-interface-builder-outlet-sml.png "Un pulsante aggiunto in iOS designer")](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

Novell. iOS supporta anche lo stile di azione di destinazione della connessione del codice a un'interazione che si verifica con un controllo. Per creare un'azione di destinazione per il pulsante **Hello** , fare doppio clic su di essa nella finestra di progettazione iOS. Verrà visualizzato il file code-behind di UIViewController e allo sviluppatore verrà richiesto di selezionare un percorso per inserire il metodo di connessione:

[![](delegates-protocols-and-events-images/03-interface-builder-action-sml.png "File code-behind UIViewControllers")](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

Dopo aver selezionato una località, viene creato un nuovo metodo che viene cablato al controllo. Nell'esempio seguente viene scritto un messaggio nella console quando si fa clic sul pulsante:

[![](delegates-protocols-and-events-images/05-interface-builder-action-sml.png "Quando si fa clic sul pulsante, viene scritto un messaggio nella console.")](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

Per altri dettagli sul modello di azione di destinazione iOS, vedere la sezione relativa all'azione di destinazione delle [competenze di base dell'applicazione per iOS](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html) nella libreria degli sviluppatori iOS di Apple.

Per ulteriori informazioni su come utilizzare iOS designer con Novell. iOS, vedere la documentazione [introduttiva di iOS designer](~/ios/user-interface/designer/index.md) .

## <a name="events"></a>Eventi

Se si vuole intercettare gli eventi da UIControl, è possibile scegliere tra l' C# uso delle espressioni lambda e delle funzioni delegate per l'uso delle API Objective-C di basso livello.

La sezione seguente illustra come acquisire l'evento TouchDown su un pulsante, a seconda della quantità di controllo necessaria.

## <a name="c-style"></a>C#Stile

Utilizzando la sintassi del delegato:

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {
    Console.WriteLine ("Touched");
};
```

Se invece si desiderano le espressioni lambda:

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

Se si desidera che più pulsanti usino lo stesso gestore per condividere lo stesso codice:

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

## <a name="monitoring-more-than-one-kind-of-event"></a>Monitoraggio di più di un tipo di evento

Gli C# eventi per i flag UIControlEvent hanno un mapping uno-a-uno ai singoli flag. Quando si desidera che lo stesso frammento di codice gestisca due o più eventi, utilizzare `UIControl.AddTarget` il metodo:

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Uso della sintassi lambda:

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Se è necessario utilizzare le funzionalità di basso livello di Objective-C, ad esempio l'associazione a una particolare istanza dell'oggetto e il richiamo di un particolare selettore:

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

Un protocollo è una funzionalità del linguaggio Objective-C che fornisce un elenco di dichiarazioni di metodi. Ha uno scopo analogo a un'interfaccia in C#, la principale differenza consiste nel fatto che un protocollo può avere metodi facoltativi. I metodi facoltativi non vengono chiamati se la classe che adotta un protocollo non li implementa. Inoltre, una singola classe in Objective-C può implementare più protocolli, così come una C# classe può implementare più interfacce.

Apple usa protocolli in iOS per definire i contratti per le classi da adottare, astraendondo la classe di implementazione dal chiamante, operando così come C# un'interfaccia. I protocolli vengono utilizzati sia negli scenari non delegati (ad esempio `MKAnnotation` con l'esempio illustrato di seguito), sia con i delegati (come presentato più avanti in questo documento, nella sezione delegati).

### <a name="protocols-with-xamarinios"></a>Protocolli con Novell. iOS

Di seguito è riportato un esempio di utilizzo di un protocollo Objective-C di Novell. iOS. Per questo esempio, verrà usato il `MKAnnotation` protocollo, che fa parte `MapKit` del Framework. `MKAnnotation`è un protocollo che consente a qualsiasi oggetto che lo adotta di fornire informazioni su un'annotazione che può essere aggiunta a una mappa. Ad esempio, un oggetto che `MKAnnotation` implementa fornisce la posizione dell'annotazione e il titolo ad esso associato.

In questo modo, il `MKAnnotation` protocollo viene usato per fornire dati pertinenti che accompagnano un'annotazione. La visualizzazione effettiva per l'annotazione è compilata dai dati nell'oggetto che adotta il `MKAnnotation` protocollo. Ad esempio, il testo del callout visualizzato quando l'utente tocca l'annotazione, come illustrato nella schermata riportata di seguito, deriva dalla `Title` proprietà della classe che implementa il protocollo:

 [![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "Testo di esempio per il callout quando l'utente tocca l'annotazione")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Come descritto nella sezione successiva, [approfondimenti sui protocolli](#protocols-deep-dive), Novell. iOS associa i protocolli alle classi astratte. Per il `MKAnnotation` protocollo, la classe C# associata è denominata `MKAnnotation` per simulare il nome del protocollo ed è una sottoclasse di `NSObject`, la classe di base radice per CocoaTouch. Il protocollo richiede l'implementazione di un getter e un setter per la coordinata. Tuttavia, un titolo e un sottotitolo sono facoltativi. `MKAnnotation` Quindi, nella classe, la `Coordinate` proprietà è *astratta*, richiedendo che venga implementata e le `Title` proprietà e `Subtitle` sono contrassegnate come *virtuali*, rendendole facoltative, come illustrato di seguito:

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

Qualsiasi classe può fornire dati di annotazione semplicemente derivando da `MKAnnotation`, purché venga implementata almeno la `Coordinate` proprietà. Ecco, ad esempio, una classe di esempio che accetta la coordinata nel costruttore e restituisce una stringa per il titolo:

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

Tramite il protocollo a cui è associato, tutte le classi che sottoclassi `MKAnnotation` possono fornire i dati rilevanti che verranno usati dalla mappa quando crea la visualizzazione dell'annotazione. Per aggiungere un'annotazione a una mappa, è `AddAnnotation` sufficiente chiamare il `MKMapView` metodo di un'istanza, come illustrato nel codice seguente:

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
    new CLLocationCoordinate2D (42.3467512, -71.0969456); // Boston

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

La variabile map qui è un'istanza di `MKMapView`, ovvero la classe che rappresenta la mappa stessa. Utilizzerà i `Coordinate` dati derivati dall' `SampleMapAnnotation` istanza di per posizionare la visualizzazione delle annotazioni sulla mappa. `MKMapView`

Il `MKAnnotation` protocollo fornisce un set noto di funzionalità per tutti gli oggetti che lo implementano, senza che il consumer (la mappa in questo caso) debba conoscere i dettagli di implementazione. In questo modo si semplifica l'aggiunta di un'ampia gamma di possibili annotazioni a una mappa.

### <a name="protocols-deep-dive"></a>Approfondimento sui protocolli

Poiché C# le interfacce non supportano metodi facoltativi, Novell. iOS esegue il mapping di protocolli a classi astratte. L'adozione di un protocollo in Objective-C viene pertanto eseguita in Novell. iOS derivando dalla classe astratta associata al protocollo e implementando i metodi richiesti. Questi metodi verranno esposti come metodi astratti nella classe. I metodi facoltativi del protocollo verranno associati a metodi virtuali della C# classe.

Ad esempio, ecco una parte del `UITableViewDataSource` protocollo come associato in Novell. iOS:

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

Si noti che la classe è astratta. Novell. iOS rende la classe astratta per supportare i metodi facoltativi o obbligatori nei protocolli.
Tuttavia, a differenza dei protocolli Objective-C, o C# delle interfacce, C# le classi non supportano l'ereditarietà multipla. Ciò influisca sulla progettazione C# del codice che usa i protocolli e in genere conduce a classi annidate. Ulteriori informazioni su questo problema sono descritte più avanti in questo documento, nella sezione delegati.

 `GetCell(…)`è un metodo astratto, associato al *selettore*Objective-C `tableView:cellForRowAtIndexPath:`,, che è un metodo `UITableViewDataSource` obbligatorio del protocollo. Selector è il termine Objective-C per il nome del metodo. Per applicare il metodo come richiesto, Novell. iOS lo dichiara come abstract. L'altro metodo, `NumberOfSections(…)`, è associato a `numberOfSectionsInTableview:`. Questo metodo è facoltativo nel protocollo, quindi Novell. iOS lo dichiara come virtuale, rendendolo facoltativo per l'override in C#.

Novell. iOS gestisce automaticamente tutte le associazioni iOS. Tuttavia, se è sempre necessario associare un protocollo da Objective-C manualmente, è possibile farlo decorando una classe con `ExportAttribute`. Si tratta dello stesso metodo utilizzato da Novell. iOS.

Per ulteriori informazioni su come associare tipi Objective-C in Novell. iOS, vedere l'articolo [associazione di tipi Objective-c](~/ios/platform/binding-objective-c/index.md).

Tuttavia, non è possibile usare i protocolli. Vengono usati anche in iOS come base per i delegati Objective-C, che è l'argomento della sezione successiva.

## <a name="delegates"></a>Delegati

iOS usa i delegati Objective-C per implementare il modello di delega, in cui un oggetto passa il lavoro a un altro. L'oggetto che esegue il lavoro è il delegato del primo oggetto. Un oggetto indica al delegato di svolgere il proprio lavoro inviando messaggi dopo che si verificano determinate operazioni. L'invio di un messaggio come questo in Objective-C equivale dal punto di vista funzionale alla C#chiamata a un metodo in. Un delegato implementa i metodi in risposta a queste chiamate, quindi fornisce le funzionalità per l'applicazione.

I delegati consentono di estendere il comportamento delle classi senza la necessità di creare sottoclassi. Le applicazioni in iOS spesso usano delegati quando una classe esegue una nuova chiamata a un'altra in seguito a un'azione importante. Ad esempio, la `MKMapView` classe richiama il delegato quando l'utente tocca un'annotazione su una mappa, concedendo all'autore della classe delegata la possibilità di rispondere all'interno dell'applicazione. È possibile utilizzare un esempio di questo tipo di utilizzo dei delegati più avanti in questo articolo, ad esempio utilizzando un delegato con Novell. iOS.

A questo punto, è possibile chiedersi come una classe determini quali metodi chiamare sul delegato. Si tratta di un'altra posizione in cui si usano i protocolli. In genere, i metodi disponibili per un delegato provengono dai protocolli che adottano.

### <a name="how-protocols-are-used-with-delegates"></a>Modalità di utilizzo dei protocolli con i delegati

Abbiamo visto in precedenza come vengono usati i protocolli per supportare l'aggiunta di annotazioni a una mappa.
I protocolli vengono utilizzati anche per fornire un set noto di metodi per le classi da chiamare dopo che si verificano determinati eventi, ad esempio dopo che l'utente tocca un'annotazione su una mappa o seleziona una cella in una tabella. Le classi che implementano questi metodi sono note come delegati delle classi che le chiamano.

Le classi che supportano la delega eseguono questa operazione esponendo una proprietà del delegato, a cui viene assegnata una classe che implementa il delegato. I metodi implementati per il delegato dipendono dal protocollo adottato dal delegato specifico. Per il `UITableView` metodo, si implementa il `UITableViewDelegate` protocollo, per il `UIAccelerometer` metodo, si implementa `UIAccelerometerDelegate`e così via per qualsiasi altra classe in iOS per cui si vuole esporre un delegato.

La `MKMapView` classe illustrata nell'esempio precedente dispone anche di una proprietà denominata Delegate, che verrà chiamata dopo che si verificano vari eventi. Il delegato per `MKMapView` è di tipo `MKMapViewDelegate`.
Questa operazione verrà usata a breve in un esempio per rispondere all'annotazione dopo che è stata selezionata, ma per prima cosa si esaminerà la differenza tra i delegati forte e debole.

### <a name="strong-delegates-vs-weak-delegates"></a>Delegati forti rispetto a Delegati vulnerabili

I delegati che abbiamo esaminato finora sono delegati forti, ovvero sono fortemente tipizzati. Le associazioni Novell. iOS sono fornite con una classe fortemente tipizzata per ogni protocollo delegato in iOS. Tuttavia, iOS ha anche il concetto di un delegato debole. Anziché sottoclassare una classe associata al protocollo Objective-C per un determinato delegato, iOS consente inoltre di scegliere di associare i metodi di protocollo manualmente in qualsiasi classe che deriva da NSObject, decorando i metodi con ExportAttribute e quindi Specifica dei selettori appropriati.
Quando si accetta questo approccio, si assegna un'istanza della classe alla proprietà WeakDelegate anziché alla proprietà delegate. Un delegato debole offre la flessibilità necessaria per portare la classe Delegate a una gerarchia di ereditarietà diversa. Si osservi un esempio di Novell. iOS in cui vengono usati delegati sia forti che vulnerabili.

### <a name="example-using-a-delegate-with-xamarinios"></a>Esempio di utilizzo di un delegato con Novell. iOS

Per eseguire il codice in risposta all'utente che tocca l'annotazione nell'esempio, è possibile `MKMapViewDelegate` sottoscrivere una sottoclasse e `MKMapView`assegnare `Delegate` un'istanza alla proprietà di. Il `MKMapViewDelegate` protocollo contiene solo metodi facoltativi.
Pertanto, tutti i metodi sono virtuali associati a questo protocollo nella classe Novell. iOS `MKMapViewDelegate` . Quando l'utente seleziona un'annotazione `MKMapView` , l'istanza invierà il `mapView:didSelectAnnotationView:` messaggio al relativo delegato. Per gestire questa operazione in Novell. iOS, è necessario eseguire l' `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` override del metodo nella sottoclasse MKMapViewDelegate come indicato di seguito:

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

La classe SampleMapDelegate illustrata in precedenza viene implementata come classe annidata nel controller che `MKMapView` contiene l'istanza. In Objective-C si noterà spesso che il controller adotterà più protocolli direttamente all'interno della classe. Tuttavia, poiché i protocolli sono associati alle classi in Novell. iOS, le classi che implementano delegati fortemente tipizzati vengono in genere incluse come classi annidate.

Con l'implementazione della classe delegata sul posto, è sufficiente creare un'istanza del delegato nel controller e assegnarla alla `MKMapView`proprietà di `Delegate` , come illustrato di seguito:

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

Per usare un delegato debole per eseguire la stessa operazione, è necessario associare il metodo manualmente in qualsiasi classe che derivi da `NSObject` e assegnarla `WeakDelegate` alla proprietà di `MKMapView`. Poiché la `UIViewController` classe deriva infine da `NSObject` (come ogni classe Objective-C in CocoaTouch), è possibile implementare semplicemente un metodo associato a `mapView:didSelectAnnotationView:` direttamente nel controller e assegnare il controller a `MKMapView` `WeakDelegate`, evitando la necessità della classe annidata aggiuntiva. Il codice seguente illustra questo approccio:

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

Quando si esegue questo codice, l'applicazione si comporta esattamente come se eseguisse la versione del delegato fortemente tipizzata. Il vantaggio di questo codice è che il delegato debole non richiede la creazione della classe aggiuntiva che è stata creata quando è stato usato il delegato fortemente tipizzato. Tuttavia, questo problema si verifica a scapito dell'indipendenza dai tipi. Se si commette un errore nel selettore passato a `ExportAttribute`, non si scoprirebbe fino alla fase di esecuzione.

### <a name="events-and-delegates"></a>Eventi e delegati

I delegati vengono usati per i callback in iOS in modo analogo al modo in cui .NET usa gli eventi. Per rendere le API iOS e il modo in cui usano i delegati Objective-C sembrano più simili a .NET, Novell. iOS espone gli eventi .NET in molte posizioni in cui i delegati vengono usati in iOS.

Ad esempio, l'implementazione precedente in cui `MKMapViewDelegate` l'oggetto ha risposto a un'annotazione selezionata può essere implementata anche in Novell. iOS tramite un evento .NET. In tal caso, l'evento verrebbe definito in `MKMapView` e chiamato. `DidSelectAnnotationView` Avrebbe una `EventArgs` sottoclasse di tipo `MKMapViewAnnotationEventsArgs`. La `View` proprietà di `MKMapViewAnnotationEventsArgs` darebbe un riferimento alla visualizzazione dell'annotazione, da cui è possibile procedere con la stessa implementazione precedente, come illustrato di seguito:

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

Questo articolo ha illustrato come usare gli eventi, i protocolli e i delegati in Novell. iOS. Abbiamo visto come Novell. iOS espone gli eventi di stile .NET normali per i controlli.
A questo punto sono stati appresi i protocolli Objective-C, tra cui C# il modo in cui sono diversi dalle interfacce e il modo in cui vengono usati da Novell. iOS. Infine, sono stati esaminati i delegati Objective-C dal punto di vista di Novell. iOS. Abbiamo visto il modo in cui Novell. iOS supporta i delegati sia fortemente che debolmente tipizzati e come associare gli eventi .NET ai metodi delegati.

## <a name="related-links"></a>Collegamenti correlati

- [Protocolli, delegati ed eventi (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/protocols-delegates-events)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Associazione di tipi Objective-C](~/ios/platform/binding-objective-c/index.md)
- [Linguaggio di programmazione Objective-C](https://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [Progettazione di interfacce utente in Xcode 4](https://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [Competenze di base delle applicazioni per iOS](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
