---
title: Eventi, protocolli e delegati in Xamarin.iOS
description: Questo documento descrive come usare gli eventi, i protocolli e i delegati in Xamarin.iOS. Questi concetti fondamentali sono universali nello sviluppo di Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/17/2017
ms.openlocfilehash: b63d5dcd8ac1a82c1f120cc5a690985557f7e68f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004403"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>Eventi, protocolli e delegati in Xamarin.iOS

Xamarin.iOS usa i controlli per esporre gli eventi per la maggior parte delle interazioni utente.
Le applicazioni Xamarin.iOS utilizzano questi eventi nello stesso modo delle applicazioni .NET tradizionali. Ad esempio, la classe UIButton di Xamarin.iOS ha un evento denominato TouchUpInside e utilizza questo evento come se questa classe ed evento si trovasse in un'app .NET.

Oltre a questo approccio .NET, Xamarin.iOS espone un altro modello che può essere usato per interazioni e data binding più complesse. Questa metodologia usa le chiamate a delegati e protocolli di Apple. I delegati sono concetti simili ai C#delegati in, ma anziché definire e chiamare un singolo metodo, un delegato in Objective-C è un'intera classe conforme a un protocollo. Un protocollo è simile a un'interfaccia in C#, ad eccezione del fatto che i relativi metodi possono essere facoltativi. Quindi, ad esempio, per popolare un UITableView con i dati, è necessario creare una classe Delegate che implementi i metodi definiti nel protocollo UITableViewDataSource che il UITableView chiamerà per popolare.

In questo articolo vengono fornite informazioni su tutti questi argomenti, che offrono una solida base per la gestione degli scenari di callback in Xamarin.iOS, tra cui:

- **Eventi** : utilizzo di eventi .NET con controlli UIKit.
- **Protocolli** : informazioni sui protocolli e sul modo in cui vengono usati e sulla creazione di un esempio che fornisce i dati per un'annotazione della mappa.
- **Delegati** : informazioni sui delegati Objective-C estendendo l'esempio di mappa per gestire l'interazione dell'utente che include un'annotazione, quindi apprendere la differenza tra i delegati forti e deboli e quando usarli.

Per illustrare i protocolli e i delegati, verrà compilata una semplice applicazione map che aggiunge un'annotazione a una mappa, come illustrato di seguito:

[![](delegates-protocols-and-events-images/01-map-sml.png "An example of a simple map application that adds an annotation to a map")](delegates-protocols-and-events-images/01-map.png#lightbox)
[![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "An example annotation added to a map")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Prima di affrontare questa app, è necessario iniziare esaminando gli eventi .NET in UIKit.

## <a name="net-events-with-uikit"></a>Eventi .NET con UIKit

Xamarin.iOS espone gli eventi .NET nei controlli UIKit. Ad esempio, UIButton ha un evento TouchUpInside, che viene gestito normalmente in .NET, come illustrato nel codice seguente che usa un' C# espressione lambda:

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

Il codice precedente viene cablato nel `ViewDidLoad` metodo di UIViewController. La variabile `aButton` fa riferimento a un pulsante, che può essere aggiunto in iOS designer o con il codice. La figura seguente mostra un pulsante che è stato aggiunto in iOS designer:

[![](delegates-protocols-and-events-images/02-interface-builder-outlet-sml.png "A button added in iOS Designer")](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

Xamarin.iOS supporta anche lo stile di azione di destinazione della connessione del codice a un'interazione che si verifica con un controllo. Per creare un'azione di destinazione per il pulsante **Hello** , fare doppio clic su di essa nella finestra di progettazione iOS. Verrà visualizzato il file code-behind di UIViewController e allo sviluppatore verrà richiesto di selezionare un percorso per inserire il metodo di connessione:

[![](delegates-protocols-and-events-images/03-interface-builder-action-sml.png "The UIViewControllers code-behind file")](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

Dopo aver selezionato una località, viene creato un nuovo metodo che viene cablato al controllo. Nell'esempio seguente viene scritto un messaggio nella console quando si fa clic sul pulsante:

[![](delegates-protocols-and-events-images/05-interface-builder-action-sml.png "A message will be written to the console when the button is clicked")](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

Per altri dettagli sul modello di azione di destinazione iOS, vedere la sezione relativa all'azione di destinazione delle [competenze di base dell'applicazione per iOS](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html) nella libreria degli sviluppatori iOS di Apple.

Per ulteriori informazioni su come utilizzare iOS designer con Xamarin.iOS, vedere la documentazione [introduttiva di iOS designer](~/ios/user-interface/designer/index.md) .

## <a name="events"></a>eventi

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

Gli C# eventi per i flag UIControlEvent hanno un mapping uno-a-uno ai singoli flag. Quando si vuole che lo stesso frammento di codice gestisca due o più eventi, usare il metodo `UIControl.AddTarget`:

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

Apple usa protocolli in iOS per definire i contratti per le classi da adottare, astraendondo la classe di implementazione dal chiamante, operando così come C# un'interfaccia. I protocolli vengono utilizzati sia negli scenari non delegati (ad esempio con il `MKAnnotation` esempio illustrato di seguito), sia con i delegati, come illustrato più avanti in questo documento, nella sezione delegati.

### <a name="protocols-with-xamarinios"></a>Protocolli con Xamarin.iOS

Di seguito è riportato un esempio di utilizzo di un protocollo Objective-C di Xamarin.iOS. Per questo esempio verrà usato il protocollo `MKAnnotation`, che fa parte del Framework di `MapKit`. `MKAnnotation` è un protocollo che consente a qualsiasi oggetto che lo adotta di fornire informazioni su un'annotazione che può essere aggiunta a una mappa. Ad esempio, un oggetto che implementa `MKAnnotation` fornisce la posizione dell'annotazione e il titolo associato.

In questo modo, il protocollo `MKAnnotation` viene usato per fornire dati pertinenti che accompagnano un'annotazione. La visualizzazione effettiva per l'annotazione è compilata dai dati nell'oggetto che adotta il protocollo `MKAnnotation`. Ad esempio, il testo del callout visualizzato quando l'utente tocca l'annotazione, come illustrato nella schermata seguente, deriva dalla proprietà `Title` nella classe che implementa il protocollo:

 [![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "Example text for the callout when the user taps on the annotation")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Come descritto nella sezione successiva, [approfondimenti sui protocolli](#protocols-deep-dive), Xamarin.iOS associa i protocolli alle classi astratte. Per il protocollo `MKAnnotation`, la classe C# associata è denominata `MKAnnotation` per simulare il nome del protocollo ed è una sottoclasse di `NSObject`, la classe di base radice per CocoaTouch. Il protocollo richiede l'implementazione di un getter e un setter per la coordinata. Tuttavia, un titolo e un sottotitolo sono facoltativi. Quindi, nella classe `MKAnnotation` la proprietà `Coordinate` è *astratta*, richiedendo che venga implementata e le proprietà `Title` e `Subtitle` siano contrassegnate come *virtuali*, rendendole facoltative, come illustrato di seguito:

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

Qualsiasi classe può fornire dati di annotazione semplicemente derivando da `MKAnnotation`, purché venga implementata almeno la proprietà `Coordinate`. Ecco, ad esempio, una classe di esempio che accetta la coordinata nel costruttore e restituisce una stringa per il titolo:

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

Tramite il protocollo a cui è associato, tutte le classi sottoclassi `MKAnnotation` possono fornire i dati rilevanti che verranno usati dalla mappa quando crea la visualizzazione dell'annotazione. Per aggiungere un'annotazione a una mappa, è sufficiente chiamare il metodo `AddAnnotation` di un'istanza di `MKMapView`, come illustrato nel codice seguente:

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
    new CLLocationCoordinate2D (42.3467512, -71.0969456); // Boston

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

La variabile map qui è un'istanza di un `MKMapView`, ovvero la classe che rappresenta la mappa stessa. Il `MKMapView` utilizzerà i dati `Coordinate` derivati dall'istanza di `SampleMapAnnotation` per posizionare la visualizzazione delle annotazioni sulla mappa.

Il protocollo `MKAnnotation` fornisce un set noto di funzionalità per tutti gli oggetti che lo implementano, senza il consumer (in questo caso la mappa) che deve conoscere i dettagli di implementazione. In questo modo si semplifica l'aggiunta di un'ampia gamma di possibili annotazioni a una mappa.

### <a name="protocols-deep-dive"></a>Approfondimento sui protocolli

Poiché C# le interfacce non supportano metodi facoltativi, Xamarin.iOS esegue il mapping di protocolli a classi astratte. L'adozione di un protocollo in Objective-C viene pertanto eseguita in Xamarin.iOS derivando dalla classe astratta associata al protocollo e implementando i metodi richiesti. Questi metodi verranno esposti come metodi astratti nella classe. I metodi facoltativi del protocollo verranno associati a metodi virtuali della C# classe.

Ecco ad esempio una parte del protocollo `UITableViewDataSource` come associato in Xamarin.iOS:

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

Si noti che la classe è astratta. Xamarin.iOS rende la classe astratta per supportare i metodi facoltativi o obbligatori nei protocolli.
Tuttavia, a differenza dei protocolli Objective-C, o C# delle interfacce, C# le classi non supportano l'ereditarietà multipla. Ciò influisca sulla progettazione C# del codice che usa i protocolli e in genere conduce a classi annidate. Ulteriori informazioni su questo problema sono descritte più avanti in questo documento, nella sezione delegati.

 `GetCell(…)` è un metodo astratto, associato al *selettore*Objective-C, `tableView:cellForRowAtIndexPath:`, che è un metodo obbligatorio del protocollo di `UITableViewDataSource`. Selector è il termine Objective-C per il nome del metodo. Per applicare il metodo come richiesto, Xamarin.iOS lo dichiara come abstract. L'altro metodo, `NumberOfSections(…)`, è associato a `numberOfSectionsInTableview:`. Questo metodo è facoltativo nel protocollo, quindi Xamarin.iOS lo dichiara come virtuale, rendendolo facoltativo per l'override in C#.

Xamarin.iOS gestisce automaticamente tutte le associazioni iOS. Tuttavia, se è necessario associare manualmente un protocollo da Objective-C, è possibile farlo decorando una classe con la `ExportAttribute`. Si tratta dello stesso metodo utilizzato da Xamarin.iOS.

Per ulteriori informazioni su come associare tipi Objective-C in Xamarin.iOS, vedere l'articolo [associazione di tipi Objective-c](~/ios/platform/binding-objective-c/index.md).

Tuttavia, non è possibile usare i protocolli. Vengono usati anche in iOS come base per i delegati Objective-C, che è l'argomento della sezione successiva.

## <a name="delegates"></a>Delegati

iOS usa i delegati Objective-C per implementare il modello di delega, in cui un oggetto passa il lavoro a un altro. L'oggetto che esegue il lavoro è il delegato del primo oggetto. Un oggetto indica al delegato di svolgere il proprio lavoro inviando messaggi dopo che si verificano determinate operazioni. L'invio di un messaggio come questo in Objective-C equivale dal punto di vista funzionale alla C#chiamata a un metodo in. Un delegato implementa i metodi in risposta a queste chiamate, quindi fornisce le funzionalità per l'applicazione.

I delegati consentono di estendere il comportamento delle classi senza la necessità di creare sottoclassi. Le applicazioni in iOS spesso usano delegati quando una classe esegue una nuova chiamata a un'altra in seguito a un'azione importante. Ad esempio, la classe `MKMapView` chiama di nuovo al delegato quando l'utente tocca un'annotazione su una mappa, concedendo all'autore della classe Delegate la possibilità di rispondere all'interno dell'applicazione. È possibile utilizzare un esempio di questo tipo di utilizzo dei delegati più avanti in questo articolo, ad esempio utilizzando un delegato con Xamarin.iOS.

A questo punto, è possibile chiedersi come una classe determini quali metodi chiamare sul delegato. Si tratta di un'altra posizione in cui si usano i protocolli. In genere, i metodi disponibili per un delegato provengono dai protocolli che adottano.

### <a name="how-protocols-are-used-with-delegates"></a>Modalità di utilizzo dei protocolli con i delegati

Abbiamo visto in precedenza come vengono usati i protocolli per supportare l'aggiunta di annotazioni a una mappa.
I protocolli vengono utilizzati anche per fornire un set noto di metodi per le classi da chiamare dopo che si verificano determinati eventi, ad esempio dopo che l'utente tocca un'annotazione su una mappa o seleziona una cella in una tabella. Le classi che implementano questi metodi sono note come delegati delle classi che le chiamano.

Le classi che supportano la delega eseguono questa operazione esponendo una proprietà del delegato, a cui viene assegnata una classe che implementa il delegato. I metodi implementati per il delegato dipendono dal protocollo adottato dal delegato specifico. Per il metodo di `UITableView`, si implementa il protocollo di `UITableViewDelegate`, per il metodo `UIAccelerometer`, è necessario implementare `UIAccelerometerDelegate` e così via per qualsiasi altra classe in iOS per cui si vuole esporre un delegato.

La classe `MKMapView` illustrata nell'esempio precedente dispone anche di una proprietà denominata Delegate, che verrà chiamata dopo che si verificano vari eventi. Il delegato per `MKMapView` è di tipo `MKMapViewDelegate`.
Questa operazione verrà usata a breve in un esempio per rispondere all'annotazione dopo che è stata selezionata, ma per prima cosa si esaminerà la differenza tra i delegati forte e debole.

### <a name="strong-delegates-vs-weak-delegates"></a>Delegati forti rispetto a delegati deboli

I delegati che abbiamo esaminato finora sono delegati forti, ovvero sono fortemente tipizzati. Le associazioni Xamarin.iOS sono fornite con una classe fortemente tipizzata per ogni protocollo delegato in iOS. Tuttavia, iOS ha anche il concetto di un delegato debole. Anziché sottoclassare una classe associata al protocollo Objective-C per un determinato delegato, iOS consente inoltre di scegliere di associare i metodi di protocollo manualmente in qualsiasi classe che deriva da NSObject, decorando i metodi con ExportAttribute e quindi Specifica dei selettori appropriati.
Quando si accetta questo approccio, si assegna un'istanza della classe alla proprietà WeakDelegate anziché alla proprietà delegate. Un delegato debole offre la flessibilità necessaria per portare la classe Delegate a una gerarchia di ereditarietà diversa. Si osservi un esempio di Xamarin.iOS in cui vengono usati delegati sia forti che vulnerabili.

### <a name="example-using-a-delegate-with-xamarinios"></a>Esempio di utilizzo di un delegato con Xamarin.iOS

Per eseguire il codice in risposta all'utente che tocca l'annotazione nell'esempio, è possibile sottoclassare `MKMapViewDelegate` e assegnare un'istanza alla proprietà `Delegate` di `MKMapView`. Il protocollo `MKMapViewDelegate` contiene solo metodi facoltativi.
Pertanto, tutti i metodi sono virtuali associati a questo protocollo nella classe `MKMapViewDelegate` Xamarin.iOS. Quando l'utente seleziona un'annotazione, l'istanza di `MKMapView` invierà il messaggio di `mapView:didSelectAnnotationView:` al relativo delegato. Per gestire questa operazione in Xamarin.iOS, è necessario eseguire l'override del metodo `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` nella sottoclasse MKMapViewDelegate come indicato di seguito:

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

La classe SampleMapDelegate illustrata in precedenza viene implementata come classe annidata nel controller che contiene l'istanza di `MKMapView`. In Objective-C si noterà spesso che il controller adotterà più protocolli direttamente all'interno della classe. Tuttavia, poiché i protocolli sono associati alle classi in Xamarin.iOS, le classi che implementano delegati fortemente tipizzati vengono in genere incluse come classi annidate.

Con l'implementazione della classe delegata sul posto, è sufficiente creare un'istanza del delegato nel controller e assegnarla alla proprietà `Delegate` del `MKMapView` come illustrato di seguito:

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

Per usare un delegato debole per eseguire la stessa operazione, è necessario associare il metodo manualmente in qualsiasi classe che derivi da `NSObject` e assegnarla alla proprietà `WeakDelegate` dell'`MKMapView`. Poiché la classe `UIViewController` deriva infine da `NSObject` (ad esempio ogni classe Objective-C in CocoaTouch), è possibile implementare semplicemente un metodo associato a `mapView:didSelectAnnotationView:` direttamente nel controller e assegnare il controller alla `WeakDelegate` di `MKMapView`, evitando la necessità di un'ulteriore classe annidata. Il codice seguente illustra questo approccio:

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

Quando si esegue questo codice, l'applicazione si comporta esattamente come se eseguisse la versione del delegato fortemente tipizzata. Il vantaggio di questo codice è che il delegato debole non richiede la creazione della classe aggiuntiva che è stata creata quando è stato usato il delegato fortemente tipizzato. Tuttavia, questo problema si verifica a scapito dell'indipendenza dai tipi. Se si commette un errore nel selettore passato al `ExportAttribute`, non si scoprirà fino al runtime.

### <a name="events-and-delegates"></a>Eventi e delegati

I delegati vengono usati per i callback in iOS in modo analogo al modo in cui .NET usa gli eventi. Per rendere le API iOS e il modo in cui usano i delegati Objective-C sembrano più simili a .NET, Xamarin.iOS espone gli eventi .NET in molte posizioni in cui i delegati vengono usati in iOS.

Ad esempio, l'implementazione precedente in cui il `MKMapViewDelegate` ha risposto a un'annotazione selezionata può essere implementata anche in Xamarin.iOS tramite un evento .NET. In tal caso, l'evento verrebbe definito in `MKMapView` e chiamato `DidSelectAnnotationView`. Avrebbe una sottoclasse `EventArgs` di tipo `MKMapViewAnnotationEventsArgs`. La proprietà `View` di `MKMapViewAnnotationEventsArgs` darebbe un riferimento alla visualizzazione dell'annotazione, da cui è possibile procedere con la stessa implementazione precedente, come illustrato di seguito:

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

Questo articolo ha illustrato come usare gli eventi, i protocolli e i delegati in Xamarin.iOS. Abbiamo visto come Xamarin.iOS espone gli eventi di stile .NET normali per i controlli.
A questo punto sono stati appresi i protocolli Objective-C, tra cui C# il modo in cui sono diversi dalle interfacce e il modo in cui vengono usati da Xamarin.iOS. Infine, sono stati esaminati i delegati Objective-C dal punto di vista di Xamarin.iOS. Abbiamo visto il modo in cui Xamarin.iOS supporta i delegati sia fortemente che debolmente tipizzati e come associare gli eventi .NET ai metodi delegati.

## <a name="related-links"></a>Collegamenti correlati

- [Protocolli, delegati ed eventi (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/protocols-delegates-events)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Associazione di tipi Objective-C](~/ios/platform/binding-objective-c/index.md)
- [Linguaggio di programmazione Objective-C](https://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [Progettazione di interfacce utente in Xcode 4](https://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [Competenze di base delle applicazioni per iOS](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
