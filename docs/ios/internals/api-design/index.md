---
title: Progettazione API Xamarin.iOS
description: Principi guida che sono stati utilizzati per progettare le API Xamarin.iOS e come questi sono correlati a Objective-C.
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: a2435b30b7d5b468fca6c55d295c87b9a0d20652
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304800"
---
# <a name="xamarinios-api-design"></a>Progettazione API Xamarin.iOS

Oltre alle librerie di classi di base di base che fanno parte di Mono, Xamarin.iOS viene fornito con associazioni per varie API iOS per consentire agli sviluppatori di creare applicazioni iOS native con Mono.In addition to the core Base Class Libraries that are part of Mono, [Xamarin.iOS](~/ios/index.yml) ships with bindings for various iOS APIs to allow developers to create native iOS applications with Mono.

Al centro di Xamarin.iOS, c'è un motore di interoperabilità che collega il mondo di C , con il mondo Objective-C, così come le associazioni per le API basate su C iOS come CoreGraphics e [OpenGL ES](#opengles).

Il runtime di basso livello per comunicare con il codice Objective-C si trova in [MonoTouch.ObjCRuntime](#objcruntime). Inoltre, vengono forniti i binding per [Foundation](#foundation), CoreFoundation e [UIKit.](#uikit)

## <a name="design-principles"></a>Principi di progettazione

Questi sono alcuni dei nostri principi di progettazione per gli attacchi Xamarin.iOS (si applicano anche a Xamarin.Mac, gli attacchi Mono per Objective-C su macOS):

- Seguire le linee guida di [progettazione frameworkFollow](https://docs.microsoft.com/dotnet/standard/design-guidelines) the Framework Design Guidelines
- Consenti agli sviluppatori di creare sottoclassi di classi Objective-C:

  - Derivare da una classe esistente
  - Chiamare il costruttore di base per concatenare
  - L'override dei metodi deve essere eseguito con il sistema di override di C
  - La creazione di sottoclassi deve funzionare con i costrutti standard di C

- Non esporre gli sviluppatori ai selettori Objective-C
- Fornire un meccanismo per chiamare librerie Objective-C arbitrarieProvide a mechanism to call arbitrary Objective-C libraries
- Rendere possibili le attività obiettivo-C comuni in modo semplice e difficile
- Esporre le proprietà Objective-C come proprietà di C
- Esporre un'API fortemente tipizzato:Expose a strongly-typed API:

  - Aumentare la sicurezza dei tipi
  - Ridurre al minimo gli errori di runtime
  - Ottenere IntelliSense IDE sui tipi restituitiGet IDE IntelliSense on return types
  - Consente la documentazione popup IDE

- Incoraggiare l'esplorazione in-IDE delle API:

  - Ad esempio, invece di esporre una matrice debolmente tipizzata come questa:For example, instead of exposing a weakly-typed array like this:

    ```objc
    NSArray *getViews
    ```

    Esporre un tipo forte, come questo:

    ```csharp
    NSView [] Views { get; set; }
    ```

    In questo modo Visual Studio per Mac la possibilità di eseguire `System.Array` il completamento automatico durante l'esplorazione dell'API, rende tutte le operazioni disponibili sul valore restituito e consente al valore restituito di partecipare a LINQ.

- Tipi nativi di C

  - [`NSString`Diventa`string`](~/ios/internals/api-design/nsstring.md)
  - Turn `int` `uint` e parametri che avrebbero dovuto essere enumerazioni in enumerazioni C , e le enumerazioni di C, con `[Flags]` gli attributi
  - Anziché gli `NSArray` oggetti indipendenti dal tipo, esporre le matrici come matrici fortemente tipizzate.
  - Per eventi e notifiche, offri agli utenti una scelta tra:

    - Una versione fortemente tipizzata per impostazione predefinita
    - Una versione debolmente tipizzata per i casi d'uso avanzati

- Supportare il modello delegato Objective-C:

  - Sistema di eventi di C
  - Esporre i delegati di C, ovvero `System.Delegate`le espressioni lambda, i metodi anonimi e , alle API Objective-C come blocchi

### <a name="assemblies"></a>Assembly

Xamarin.iOS include un numero di assembly che costituiscono il *profilo Xamarin.iOS*. La pagina [Assembly](~/cross-platform/internals/available-assemblies.md) contiene ulteriori informazioni.

### <a name="major-namespaces"></a>Spazi dei nomi principali

#### <a name="objcruntime"></a>ObjCRuntime

Lo spazio dei nomi [ObjCRuntime](xref:ObjCRuntime) consente agli sviluppatori di eseguire il bridge dei mondi tra C e Objective-C.
Si tratta di un nuovo binding, progettato specificamente per iOS, in base all'esperienza di Cocoa e Gtk.

#### <a name="foundation"></a>Foundation

Lo spazio dei nomi Foundation fornisce i tipi di dati di base progettati per interagire con il framework Objective-C Foundation che fa parte di iOS ed è la base per la programmazione orientata agli oggetti in Objective-C.The [Foundation](xref:Foundation) namespace provides the basic data types designed to interoperate with the Objective-C Foundation framework that is part of the iOS and it is the base for object oriented programming in Objective-C.

Xamarin.iOS rispecchia in C , la gerarchia di classi da Objective-C. Ad esempio, la classe di base Objective-C NSObject è utilizzabile da C , tramite [Foundation.NSObject](xref:Foundation.NSObject).

Anche se questo spazio dei nomi fornisce associazioni per i tipi di Objective-C Foundation sottostanti, in alcuni casi è stato eseguito il mapping dei tipi sottostanti ai tipi .NET. Ad esempio:

- Invece di gestire NSString e [NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html), il runtime espone questi come [stringa](xref:System.String)C e [array](xref:System.Array)fortemente tipizzato s in tutta l'API.

- Varie API helper sono esposte qui per consentire agli sviluppatori di associare le API Objective-C di terze parti, altre API iOS o API che non sono attualmente associate da Xamarin.iOS.APIs are exposed here to allow developers to bind third-c APIs, other iOS APIs or APIs that are not currently bound by Xamarin.iOS.

Per altre informazioni sulle API di associazione, vedere la sezione Generatore di associazioni Xamarin.iOS.For more details on binding APIs, see the [Xamarin.iOS Binding Generator](~/cross-platform/macios/binding/binding-types-reference.md) section.

##### <a name="nsobject"></a>Oggetto NS

Il [NSObject](xref:Foundation.NSObject) tipo è la base per tutte le associazioni Objective-C. I tipi Xamarin.iOS rispecchiano due classi di tipi dalle API iOS CocoaTouch: i tipi C (in genere denominati tipi CoreFoundation) e i tipi Objective-C (questi tutti derivano dalla classe NSObject).

Per ogni tipo che rispecchia un tipo non gestito, è possibile ottenere l'oggetto nativo tramite il [Handle](xref:Foundation.NSObject.Handle) proprietà.

Mentre Mono fornirà garbage collection per `Foundation.NSObject` tutti gli oggetti, l'implementa il [System.IDisposable](xref:System.IDisposable) interfaccia. Ciò significa che è possibile rilasciare in modo esplicito le risorse di un determinato NSObject senza dover attendere il Garbage Collector per kick-in. Questo è importante quando si utilizza pesante NSObjects, ad esempio, UIImages che potrebbero contenere puntatori a blocchi di dati di grandi dimensioni.

Se il tipo deve eseguire la finalizzazione deterministica, eseguire l'override di [NSObject.Dispose(bool) metodo](xref:Foundation.NSObject.Dispose(System.Boolean)) il parametro di Dispose è "bool disposing" e se impostato su true significa che il metodo Dispose viene chiamato perché l'utente ha chiamato in modo esplicito Dispose () sull'oggetto. Se il valore è false, significa che il metodo Dispose(bool disposing) viene chiamato dal finalizzatore nel thread del finalizzatore.

##### <a name="categories"></a>Categorie

A partire da Xamarin.iOS 8.10 è possibile creare le categorie Objective-C da C .

Questa operazione viene `Category` eseguita utilizzando l'attributo , specificando il tipo da estendere come argomento per l'attributo. L'esempio seguente, ad esempio, estenderà NSString.The following example will for instance extend NSString.

```csharp
[Category (typeof (NSString))]
```

Ogni metodo di categoria utilizza il normale meccanismo per `Export` l'esportazione dei metodi in Objective-C utilizzando l'attributo:

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

Tutti i metodi di estensione gestiti devono essere statici, ma è possibile creare metodi di istanza di Objective-C usando la sintassi standard per i metodi di estensione in C .

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

e il primo argomento del metodo di estensione sarà l'istanza su cui è stato richiamato il metodo.

Esempio completo:

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
}
```

In questo esempio verrà aggiunto un metodo di istanza native toUpper alla classe NSString, che può essere richiamata da Objective-C.

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAutoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

Uno scenario in cui ciò è utile è l'aggiunta di un metodo a `UIViewController` un intero set di classi nella codebase, ad esempio, in questo modo tutte le istanze segnalano che possono ruotare:One scenario where this is useful is adding a method to an entire set of classes in your codebase, for example, this would make all instances report that they can rotate:

```csharp
[Category (typeof (UINavigationController))]
class Rotation_IOS6 {
      [Export ("shouldAutorotate:")]
      static bool ShouldAutoRotate (this UINavigationController self)
      {
          return true;
      }
}
```

##### <a name="preserveattribute"></a>PreserveAttribute (PreserveAttribute)

PreserveAttribute è un attributo personalizzato che viene usato per indicare a mtouch, ovvero lo strumento di distribuzione Xamarin.iOS, di mantenere un tipo o un membro di un tipo durante la fase di elaborazione dell'applicazione per ridurne le dimensioni.

Ogni membro che non è collegato in modo statico dall'applicazione è soggetto alla rimozione. Di conseguenza, questo attributo viene utilizzato per contrassegnare i membri a cui non viene fatto riferimento in modo statico, ma che sono ancora necessari per l'applicazione.

Ad esempio, se si creano istanze dei tipi in modo dinamico, potrebbe essere necessario mantenere il costruttore predefinito dei tipi. Se si usa la serializzazione XML, potrebbe essere necessario mantenere le proprietà dei tipi.

È possibile applicare questo attributo in ogni membro di un tipo oppure nel tipo stesso. Se si desidera mantenere l'intero tipo, è possibile utilizzare la sintassi [Preserve (AllMembers : true)] sul tipo.

#### <a name="uikit"></a>Interfaccia utenteUIKit

Lo spazio dei nomi [UIKit](xref:UIKit) contiene un mapping uno-a-uno a tutti i componenti dell'interfaccia utente che costituiscono CocoaTouch sotto forma di classi C . L'API è stata modificata in modo da seguire le convenzioni usate nel linguaggio C.

I delegati di C, vengono forniti per le operazioni comuni. Per ulteriori informazioni, vedere la sezione [relativa ai delegati.](#delegates)

#### <a name="opengles"></a>OpenGLES

Per OpenGLES, distribuiamo una [versione modificata](xref:OpenTK) dell'API [OpenTK,](https://opentk.net/) un'associazione orientata agli oggetti a OpenGL che è stata modificata per utilizzare i tipi di dati e le strutture CoreGraphics, oltre a esporre solo le funzionalità disponibili in iOS.

La funzionalità OpenGLES 1.1 è disponibile tramite il [tipo di ES11.GL](xref:OpenTK.Graphics.ES11.GL).

La funzionalità OpenGLES 2.0 è disponibile tramite il [tipo di ES20.GL](xref:OpenTK.Graphics.ES20.GL).

La funzionalità OpenGLES 3.0 è disponibile tramite il [tipo di ES30.GL](xref:OpenTK.Graphics.ES30.GL).

### <a name="binding-design"></a>Progettazione dell'associazione

Xamarin.iOS non è semplicemente un'associazione alla piattaforma Objective-C sottostante. Estende il sistema di tipi .NET e il sistema di invio per fondere meglio C .

Proprio come P/Invoke è uno strumento utile per richiamare librerie native in Windows e Linux, o come il supporto IJW può essere utilizzato per l'interoperabilità COM in Windows, Xamarin.iOS estende il runtime per supportare l'associazione di oggetti C , a oggetti Objective-C.

La discussione nelle prossime sezioni non è necessaria per gli utenti che creano applicazioni Xamarin.iOS, ma aiuterà gli sviluppatori a capire come vengono fatte le cose e li aiuterà durante la creazione di applicazioni più complesse.

#### <a name="types"></a>Tipi

Nel punto in cui ha senso, i tipi di C , vengono esposti anziché i tipi Foundation di basso livello, per l'universo di C.  Ciò significa che [l'API utilizza il tipo "stringa" di C' invece di NSString](~/ios/internals/api-design/nsstring.md) e utilizza matrici c'è fortemente tipizzate anziché esporre NSArray.

In generale, nella progettazione Xamarin.iOS e Xamarin.Mac, l'oggetto sottostante `NSArray` non viene esposto. Al contrario, il `NSArray`runtime converte automaticamente s `NSObject` in matrici fortemente tipizzate di una classe. Pertanto, Xamarin.iOS non espone un metodo debolmente tipizzato come GetViews per restituire un NSArray:So, Xamarin.iOS does not expose a weakly-typed method like GetViews to return an NSArray:

```csharp
NSArray GetViews ();
```

Al contrario, l'associazione espone un valore restituito fortemente tipizzato, in questo modo:Instead, the binding exposes a strongly typed return value, like this:

```csharp
UIView [] GetViews ();
```

Esistono alcuni metodi esposti `NSArray`in , per i casi d'angolo in cui è possibile utilizzare direttamente, `NSArray` ma il loro utilizzo è sconsigliato nell'associazione API.

Inoltre, nell'API **classica** invece `CGRect`di `CGPoint` `CGSize` esporre , e dall'API CoreGraphics, `RectangleF` `PointF` quelli `SizeF` con le `System.Drawing` implementazioni e in quanto consentirebbero agli sviluppatori di conservare il codice OpenGL esistente che utilizza OpenTK. Quando si utilizza la nuova **API unificata**a 64 bit , è necessario utilizzare l'API CoreGraphics.

#### <a name="inheritance"></a>Ereditarietà

La progettazione dell'API Xamarin.iOS consente agli sviluppatori di estendere i tipi Objective-C nativi nello stesso modo in cui estendino un tipo di C, usando la parola chiave "override" in una classe derivata, nonché concatenandosi all'implementazione di base usando la parola chiave "base" di C.

Questa progettazione consente agli sviluppatori di evitare di gestire i selettori Objective-C come parte del processo di sviluppo, perché l'intero sistema Objective-C è già avvolto all'interno delle librerie Xamarin.iOS.

#### <a name="types-and-interface-builder"></a>Tipi e generatore di interfacce

Quando si creano classi .NET che sono istanze di tipi creati da `IntPtr` Interface Builder, è necessario fornire un costruttore che accetta un singolo parametro.
Questa operazione è necessaria per associare l'istanza dell'oggetto gestito all'oggetto non gestito.
Il codice è costituito da una singola riga, come questa:

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

#### <a name="delegates"></a>Delegati

Objective-C e C , hanno significati diversi per la parola delegato in ogni linguaggio.

Nel mondo Objective-C e nella documentazione che si trova online su CocoaTouch, un delegato è in genere un'istanza di una classe che risponderà a un set di metodi. Questo è molto simile a un'interfaccia di C , con la differenza che i metodi non sono sempre obbligatori.

Questi delegati svolgono un ruolo importante in UIKit e altre API CocoaTouch.These delegates play an important role in UIKit and other CocoaTouch APIs. Vengono utilizzati per eseguire varie attività:

- Per fornire notifiche al codice (simile al recapito degli eventi in C .
- Per implementare modelli per i controlli di visualizzazione dei dati.
- Per guidare il comportamento di un controllo.

Il modello di programmazione è stato progettato per ridurre al minimo la creazione di classi derivate per modificare il comportamento di un controllo. Questa soluzione è simile in questo spirito a quello che altri toolkit GUI hanno fatto nel corso degli anni: segnali di Gtk, slot Qt, eventi Winforms, eventi WPF/Silverlight e così via. Per evitare di avere centinaia di interfacce (una per ogni azione) o richiedere agli sviluppatori di implementare troppi metodi che non sono necessari, Objective-C supporta le definizioni di metodo facoltative. Questo è diverso dalle interfacce di C , che richiedono l'implementazione di tutti i metodi.

Nelle classi Objective-C, si noterà che le classi che `delegate`utilizzano questo modello di programmazione espongono una proprietà, in genere denominata , necessaria per implementare le parti obbligatorie dell'interfaccia e zero, o più, delle parti facoltative.

In Xamarin.iOS vengono offerti tre meccanismi che si escludono a vicenda per eseguire l'associazione a questi delegati:In Xamarin.iOS three mutually exclusive mechanisms to bind to these delegates are offered:

1. [Tramite eventi](#via-events).
2. [Fortemente tipizzato `Delegate` tramite una proprietàStrongly typed via a property](#strongly-typed-via-a-delegate-property)
3. [Liberamente digitato `WeakDelegate` tramite una proprietà](#loosely-typed-via-the-weakdelegate-property)

Si consideri, ad esempio, la classe UIWebView.For example, consider the UIWebView class. Questo invia a un UIWebViewDelegate istanza, che viene assegnato alla proprietà del delegato.

##### <a name="via-events"></a>Tramite eventi

Per molti tipi, Xamarin.iOS creerà automaticamente un `UIWebViewDelegate` delegato appropriato che invierà le chiamate su eventi C . Per `UIWebView`:

- Il metodo webViewDidStartLoad è mappato all'evento [UIWebView.LoadStarted.](xref:UIKit.UIWebView.LoadStarted)
- Il metodo webViewDidFinishLoad è mappato all'evento [UIWebView.LoadFinished.](xref:UIKit.UIWebView.LoadFinished)
- Il metodo webView:didFailLoadWithError è mappato all'evento [UIWebView.LoadError.](xref:UIKit.UIWebView.LoadError)

Ad esempio, questo semplice programma registra l'ora di inizio e di fine durante il caricamento di una visualizzazione Web:

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```

##### <a name="via-properties"></a>Via proprietà

Gli eventi sono utili quando potrebbero essere presenti più sottoscrittori dell'evento. Inoltre, gli eventi sono limitati ai casi in cui non è presente alcun valore restituito dal codice.

Per i casi in cui si prevede che il codice restituisca un valore, abbiamo optato invece per le proprietà. Ciò significa che è possibile impostare un solo metodo alla volta in un determinato oggetto.

Ad esempio, è possibile utilizzare questo meccanismo per chiudere `UITextField`la tastiera sullo schermo sul gestore per un:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

La `UITextField`proprietà `ShouldReturn` 's in questo caso accetta come argomento un delegato che restituisce un valore bool e determina se il TextField deve eseguire un'operazione con il pulsante Return premuto. Nel nostro metodo, ritorniamo *true* al chiamante, ma rimuoviamo anche la `ResignFirstResponder`tastiera dallo schermo (questo accade quando il campo di testo chiama ).

##### <a name="strongly-typed-via-a-delegate-property"></a>Fortemente tipizzato tramite una proprietà delegateStrongly Typed via a Delegate Property

Se si preferisce non utilizzare gli eventi, è possibile fornire la propria sottoclasse [UIWebViewDelegate](xref:UIKit.UIWebViewDelegate) e assegnarla alla proprietà [UIWebView.Delegate.](xref:UIKit.UIWebView.Delegate) Una volta che UIWebView.Delegate è stato assegnato, il UIWebView meccanismo di invio degli eventi non funzionerà più e il UIWebViewDelegate metodi verranno richiamati quando si verificano gli eventi corrispondenti.

Ad esempio, questo tipo semplice registra il tempo necessario per caricare una visualizzazione Web:For example, this simple type records the time it takes to load a web view:

```csharp
class Notifier : UIWebViewDelegate  {
    DateTime startTime, endTime;

    public override LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    public override LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}
```

Quanto sopra viene utilizzato nel codice come questo:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

Quanto sopra creerà un UIWebViewer e gli istruirà l'invio di messaggi a un'istanza di Notifier, una classe che abbiamo creato per rispondere ai messaggi.

Questo pattern viene utilizzato anche per controllare il comportamento di alcuni controlli, ad esempio nel caso `UIWebView` UIWebView, `UIWebView` il [UIWebView.ShouldStartLoad](xref:UIKit.UIWebView.ShouldStartLoad) proprietà consente all'istanza di controllare se l'oggetto caricherà una pagina o meno.

Il modello viene utilizzato anche per fornire i dati su richiesta per alcuni controlli. Ad esempio, il UITableView controllo è un potente controllo di rendering della tabella e sia l'aspetto che il contenuto sono guidati da un'istanza di un [UITableViewDataSourceFor](xref:UIKit.UITableViewDataSource) example, the [UITableView](xref:UIKit.UITableView) control is a powerful table-rendering control – and both the look and the contents are driven by an instance of a UITableViewDataSource

### <a name="loosely-typed-via-the-weakdelegate-property"></a>Liberamente tipizzato tramite la proprietà WeakDelegateLoosely Typed via the WeakDelegate Property

Oltre alla proprietà fortemente tipizzata, esiste anche un delegato tipizzato debole che consente allo sviluppatore di associare le cose in modo diverso se lo si desidera.
Ovunque una proprietà `Delegate` fortemente tipizzata viene esposta nell'associazione di `WeakDelegate` Xamarin.iOS, viene esposta anche una proprietà corrispondente.

Quando si `WeakDelegate`utilizza , si è responsabili della corretta decorazione della classe utilizzando l'attributo [Export](xref:Foundation.ExportAttribute) per specificare il selettore. Ad esempio:

```csharp
class Notifier : NSObject  {
    DateTime startTime, endTime;

    [Export ("webViewDidStartLoad:")]
    public void LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    [Export ("webViewDidFinishLoad:")]
    public void LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}

[...]

var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.WeakDelegate = new Notifier ();
```

Si noti `WeakDelegate` che una volta `Delegate` assegnata la proprietà, la proprietà non verrà utilizzata. Inoltre, se si implementa il metodo in una classe base ereditata che si desidera [Esportare], è necessario renderlo un metodo pubblico.

## <a name="mapping-of-the-objective-c-delegate-pattern-to-c"></a>Mapping del modello delegato Objective-C a C\#

Quando vengono visualizzati esempi Objective-C simili ai seguenti:

```objc
foo.delegate = [[SomethingDelegate] alloc] init]
```

In questo modo viene indicato al linguaggio di creare e costruire un'istanza della classe "SomethingDelegate" e assegnare il valore alla proprietà del delegato nella variabile foo. Questo meccanismo è supportato da Xamarin.iOS e C , la sintassi è:

```csharp
foo.Delegate = new SomethingDelegate ();
```

In Xamarin.iOS sono state fornite classi fortemente tipizzate che eseguono il mapping alle classi delegate Objective-C.In Xamarin.iOS we have provided strongly-typed classes that map to the Objective-C delegate classes. Per utilizzarli, si sarà sottoclasse ed eseguire l'override dei metodi definiti dall'implementazione di Xamarin.iOS.To use them, you will be subclassing and overriding the methods defined by Xamarin.iOS implementation. Per ulteriori informazioni sul loro funzionamento, vedere la sezione "Modelli" di seguito.

### <a name="mapping-delegates-to-c"></a>Mapping Delegates to C\#

UIKit in generale usa i delegati Objective-C in due form.

Il primo form fornisce un'interfaccia al modello di un componente. Ad esempio, come meccanismo per fornire dati su richiesta per una visualizzazione, ad esempio la struttura di archiviazione dei dati per una visualizzazione elenco.  In questi casi, è necessario creare sempre un'istanza della classe appropriata e assegnare la variabile.

Nell'esempio seguente viene `UIPickerView` fornita un'implementazione con un modello che usa stringhe:In the following example, we provide the with an implementation for a model that uses strings:

```csharp
public class SampleTitleModel : UIPickerViewTitleModel {

    public override string TitleForRow (UIPickerView picker, nint row, nint component)
    {
        return String.Format ("At {0} {1}", row, component);
    }
}

[...]

pickerView.Model = new MyPickerModel ();
```

Il secondo modulo consiste nel fornire una notifica per gli eventi. In questi casi, anche se esponiamo ancora l'API nel formato descritto in precedenza, vengono forniti anche gli eventi di C, che dovrebbero essere più semplici da usare per le operazioni rapide e integrati con i delegati anonimi e le espressioni lambda in C .

Ad esempio, è `UIAccelerometer` possibile sottoscrivere gli eventi:For example, you can subscribe to events:

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

Le due opzioni sono disponibili dove hanno senso, ma come programmatore è necessario scegliere uno o l'altro. Se si crea un'istanza personalizzata di un risponditore/delegato fortemente tipizzato e la si assegna, gli eventi di C' non saranno funzionali. Se si utilizzano gli eventi di C, i metodi nella classe risponditore/delegato non verranno mai chiamati.

L'esempio precedente `UIWebView` usato può essere scritto usando le espressioni lambda di C ,3.0 in questo modo:The previous example that used can be written using C'è 3.0 lambdas like this:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```

#### <a name="responding-to-events"></a>Risposta agli eventi

Nel codice Objective-C, a volte i gestori eventi per più controlli e provider di informazioni per più controlli, verranno ospitati nella stessa classe. Ciò è possibile perché le classi rispondono ai messaggi e, finché le classi rispondono ai messaggi, è possibile collegare gli oggetti tra loro.

Come descritto in precedenza, Xamarin.iOS supporta sia il modello di programmazione basato su eventi di C , sia il modello delegato Objective-C, in cui è possibile creare una nuova classe che implementa il delegato ed esegue l'override dei metodi desiderati.

È anche possibile supportare il modello di Objective-C in cui i risponditori per più operazioni diverse sono tutti ospitati nella stessa istanza di una classe. Per fare questo, però, è necessario utilizzare le funzionalità di basso livello del binding Xamarin.iOS.

Ad esempio, se si desidera che `UITextFieldDelegate.textFieldShouldClear`la classe `UIWebViewDelegate.webViewDidStartLoad`risponda sia al messaggio : che a : nella stessa istanza di una classe, è necessario utilizzare la dichiarazione dell'attributo [Export]:

```csharp
public class MyCallbacks : NSObject {
    [Export ("textFieldShouldClear:"]
    public bool should_we_clear (UITextField tf)
    {
        return true;
    }

    [Export ("webViewDidStartLoad:")]
    public void OnWebViewStart (UIWebView view)
    {
        Console.WriteLine ("Loading started");
    }
}
```

I nomi di C ' per i metodi non sono importanti; tutto ciò che conta sono le stringhe passate all'attributo [Export].

Quando si usa questo stile di programmazione, assicurarsi che i parametri di C' corrispondano ai tipi effettivi che verranno passati dal motore di runtime.

#### <a name="models"></a>Modelli

Nelle strutture di archiviazione UIKit o nei risponditori implementati tramite classi helper, questi vengono in genere indicati nel codice Objective-C come delegati e vengono implementati come protocolli.

I protocolli Objective-C sono simili alle interfacce, ma supportano metodi facoltativi, ovvero non tutti i metodi devono essere implementati affinché il protocollo funzioni.

Esistono due modi per implementare un modello. È possibile implementarlo manualmente o utilizzare le definizioni fortemente tipizzate esistenti.

Il meccanismo manuale è necessario quando si tenta di implementare una classe che non è stata vincolata da Xamarin.iOS.The manual mechanism is necessary when you try to implement a class that has not been bound by Xamarin.iOS. E 'molto facile da fare:

- Contrassegnare la classe per la registrazione con il runtime
- Applicare l'attributo [Export] con il nome effettivo del selettore su ogni metodo di cui si desidera sostituire
- Creare un'istanza della classe e passarla.

Ad esempio, i metodi seguenti implementano solo uno dei metodi facoltativi nella definizione del protocollo UIApplicationDelegate:

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

Il nome del selettore Objective-C ("applicationDidFinishLaunching:") viene dichiarato con `[Register]` l'attributo Export e la classe viene registrata con l'attributo .

Xamarin.iOS fornisce dichiarazioni fortemente tipizzate, pronte per l'uso, che non richiedono l'associazione manuale. Per supportare questo modello di programmazione, il runtime Xamarin.iOS supporta l'attributo [Model] in una dichiarazione di classe. In questo modo viene informa il runtime che non deve eseguire il cablare tutti i metodi nella classe, a meno che i metodi non vengano implementati in modo esplicito.

Ciò significa che in UIKit, le classi che rappresentano un protocollo con metodi facoltativi vengono scritte in questo modo:This means that in UIKit, the classes that represent a protocol with optional methods are written like this:

```csharp
[Model]
public class SomeViewModel : NSObject {
    [Export ("someMethod:")]
    public virtual int SomeMethod (TheView view) {
       throw new ModelNotImplementedException ();
    }
    ...
}
```

Quando si desidera implementare un modello che implementa solo alcuni dei metodi, è sufficiente eseguire l'override dei metodi a cui si è interessati e ignorare gli altri metodi. Il runtime astrerà solo i metodi sovrascritti, non i metodi originali al mondo Objective-C.

L'equivalente del campione manuale precedente è:

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

I vantaggi sono che non è necessario esaminare i file di intestazione Objective-C per trovare il selettore, i tipi di argomenti o il mapping a C , e che si ottiene intellisense da Visual Studio per Mac, insieme a tipi sicuri

#### <a name="xib-outlets-and-c"></a>Prese XIB e C\#

> [!IMPORTANT]
> In questa sezione viene illustrata l'integrazione IDE con i punti vendita quando si utilizzano file XIB. Quando si usa la finestra di progettazione Xamarin per iOS, tutto questo viene sostituito immettendo un nome in **Nome > identità** nella sezione Proprietà dell'IDE, come illustrato di seguito:When using the Xamarin Designer for iOS, this is all replaced by entering a name under Identity > Name in the Properties section of your IDE, as shown below:
>
> [![](images/designeroutlet.png "Entering an item Name in the iOS Designer")](images/designeroutlet.png#lightbox)
>
>Per altre informazioni su IOS Designer, vedere il documento Introduzione alla finestra di progettazione iOS.For more information on the iOS Designer, please review the [Introduction to the iOS Designer document.](~/ios/user-interface/designer/introduction.md#how-it-works)

Si tratta di una descrizione di basso livello di come Outlets si integrano con C , e viene fornito per gli utenti avanzati di Xamarin.iOS. Quando si usa Visual Studio per Mac, il mapping viene eseguito automaticamente dietro le quinte usando il codice generato in modalità utente.

Quando si progetta l'interfaccia utente con Interface Builder, si progetta solo l'aspetto dell'applicazione e verranno stabilite alcune connessioni predefinite. Se si desidera recuperare informazioni a livello di codice, modificare il comportamento di un controllo in fase di esecuzione o modificare il controllo in fase di esecuzione, è necessario associare alcuni controlli al codice gestito.

Questa operazione viene eseguita in pochi passaggi:This is done in a few steps:

1. Aggiungere la **dichiarazione di presa** al **proprietario del file.**
1. Collegare il controllo al **proprietario del file**.
1. Archiviare l'interfaccia utente più le connessioni nel file XIB/NIB.
1. Caricare il file NIB in fase di esecuzione.
1. Accedere alla variabile di uscita.

I passaggi da (1) a (3) sono trattati nella documentazione di Apple per la creazione di interfacce con Interface Builder.

Quando si usa Xamarin.iOS, l'applicazione dovrà creare una classe che deriva da UIViewController.When using Xamarin.iOS, your application will need to create a class that derives from UIViewController. È implementato in questo modo:

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

Quindi per caricare il ViewController da un file NIB, procedere come segue:

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

In questo modo viene caricata l'interfaccia utente dalla NIB. Ora, per accedere alle prese, è necessario informare il runtime che vogliamo accedervi. A tale scopo, la `UIViewController` sottoclasse deve dichiarare le proprietà e annotarle con l'attributo [Connect]. nel modo seguente:

```csharp
[Connect]
UITextField UserName {
    get {
        return (UITextField) GetNativeField ("UserName");
    }
    set {
        SetNativeField ("UserName", value);
    }
}
```

L'implementazione della proprietà è quella che effettivamente recupera e archivia il valore per il tipo nativo effettivo.

Non è necessario preoccuparsi di questo quando si utilizza Visual Studio per Mac e InterfaceBuilder.You do not need to worry about this when using Visual Studio for Mac and InterfaceBuilder. Visual Studio per Mac rispecchia automaticamente tutte le prese dichiarate con codice in una classe parziale compilata come parte del progetto.

#### <a name="selectors"></a>Selettori

Un concetto di base della programmazione Objective-C sono i selettori. Spesso ci si imbatte in API che richiedono di passare un selettore o si aspetta che il codice risponda a un selettore.

La creazione di nuovi selettori in C , è molto `ObjCRuntime.Selector` semplice: è sufficiente creare una nuova istanza della classe e usare il risultato in qualsiasi posizione nell'API che lo richiede. Ad esempio:

```csharp
var selector_add = new Selector ("add:plus:");
```

Per un metodo di C, rispondere a una `NSObject` chiamata del selettore, deve ereditare dal tipo `[Export]` e il metodo di C' deve essere decorato con il nome del selettore utilizzando l'attributo. Ad esempio:

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

Si noti che i nomi dei selettori **devono** corrispondere esattamente, inclusi tutti i due punti intermedi e finali (":"), se presenti.

#### <a name="nsobject-constructors"></a>Costruttori NSObject

La maggior parte delle classi in Xamarin.iOS che derivano da `NSObject` esporrà costruttori specifici per la funzionalità dell'oggetto, ma esporrà anche vari costruttori che non sono immediatamente evidenti.

I costruttori vengono utilizzati come segue:

```csharp
public Foo (IntPtr handle)
```

Questo costruttore viene utilizzato per creare un'istanza della classe quando il runtime deve eseguire il mapping della classe a una classe non gestita. Ciò si verifica quando si carica un file XIB/NIB.  A questo punto, il runtime Objective-C avrà creato un oggetto nel mondo non gestito e questo costruttore verrà chiamato per inizializzare il lato gestito.

In genere, è sufficiente chiamare il costruttore di base con il parametro handle e, nel corpo, eseguire qualsiasi inizializzazione necessaria.

```csharp
public Foo ()
```

Questo è il costruttore predefinito per una classe e nelle classi fornite da Xamarin.iOS, questo inizializza la classe Foundation.NSObject e `init` tutte le classi tra e alla fine concatena questo al metodo Objective-C nella classe.

```csharp
public Foo (NSObjectFlag x)
```

Questo costruttore viene utilizzato per inizializzare l'istanza, ma impedire al codice di chiamare il metodo "init" Objective-C alla fine. In genere si usa questa opzione quando si `[Export]` è già registrato per l'inizializzazione (quando si utilizza nel costruttore) o quando è già stata eseguita l'inizializzazione tramite un'altra media.

```csharp
public Foo (NSCoder coder)
```

Questo costruttore viene fornito per i casi in cui l'oggetto viene inizializzato da un NSCoding istanza.

#### <a name="exceptions"></a>Eccezioni

La progettazione dell'API Xamarin.iOS non genera le eccezioni Objective-C come eccezioni C. La progettazione impone che nessun garbage essere inviato al mondo Objective-C in primo luogo e che tutte le eccezioni che devono essere prodotte vengono prodotte dall'associazione stessa prima che i dati non validi vengono mai passati al mondo Objective-C.

#### <a name="notifications"></a>Notifiche

Sia in iOS che in OS X, gli sviluppatori possono sottoscrivere le notifiche trasmesse dalla piattaforma sottostante. Questa operazione viene `NSNotificationCenter.DefaultCenter.AddObserver` eseguita utilizzando il metodo . Il `AddObserver` metodo accetta due parametri; uno è la notifica che si desidera sottoscrivere; l'altro è il metodo da richiamare quando viene generata la notifica.

In Xamarin.iOS e Xamarin.Mac, i tasti per le varie notifiche sono ospitati nella classe che attiva le notifiche. Ad esempio, le notifiche `UIMenuController` generate da `static NSString` the `UIMenuController` sono ospitate come proprietà nelle classi che terminano con il nome "Notification".

### <a name="memory-management"></a>Gestione della memoria

Xamarin.iOS ha un garbage collector che si occuperà di rilasciare risorse per voi quando non sono più in uso. Oltre al Garbage Collector, tutti gli `NSObject` oggetti `System.IDisposable` che derivano da implementano l'interfaccia.

#### <a name="nsobject-and-idisposable"></a>NSObject e IDisposable

Esporre l'interfaccia `IDisposable` è un modo pratico per assistere gli sviluppatori nel rilascio di `UIImage` oggetti che potrebbero incapsulare grandi blocchi di memoria (ad esempio, un potrebbe apparire come solo un puntatore innocente, ma potrebbe puntare a un'immagine di 2 megabyte) e altre risorse importanti e finite (ad esempio un buffer di decodifica video).

NSObject implementa l'interfaccia IDisposable e anche il [modello Dispose di .NET.](https://msdn.microsoft.com/library/fs2xkftw.aspx) Ciò consente agli sviluppatori che sottoclasse NSObject eseguire l'override del comportamento Dispose e rilasciare le proprie risorse su richiesta. Ad esempio, si consideri questo controller di visualizzazione che mantiene intorno a un gruppo di immagini:For example, consider this view controller that keeps around a bunch of images:

```csharp
class MenuViewController : UIViewController {
    UIImage breakfast, lunch, dinner;
    [...]
    public override void Dispose (bool disposing)
    {
        if (disposing){
             if (breakfast != null) breakfast.Dispose (); breakfast = null;
             if (lunch != null) lunch.Dispose (); lunch = null;
             if (dinner != null) dinner.Dispose (); dinner = null;
        }
        base.Dispose (disposing)
    }
}
```

Quando un oggetto gestito viene eliminato, non è più utile. Si potrebbe ancora avere un riferimento agli oggetti, ma l'oggetto è a tutti gli effetti non valido a questo punto. Alcune API .NET garantiscono questo generando un ObjectDisposedException se si tenta di accedere a qualsiasi metodo su un oggetto eliminato, ad esempio:

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

Anche se è ancora possibile accedere alla variabile "image", è davvero un riferimento non valido e non punta più all'oggetto Objective-C che conteneva l'immagine.

Tuttavia, l'eliminazione di un oggetto in C, non significa che l'oggetto verrà necessariamente eliminato. Tutto quello che fai è rilasciare il riferimento che C ' aveva per l'oggetto. È possibile che l'ambiente Cocoa abbia mantenuto un riferimento per il proprio uso. Ad esempio, se si imposta un UIImageView Image proprietà a un'immagine e quindi si elimina l'immagine, il sottostante UIImageView aveva preso il proprio riferimento e manterrà un riferimento a questo oggetto fino al termine del utilizzo.

#### <a name="when-to-call-dispose"></a>Quando chiamare DisposeWhen to call Dispose

È necessario chiamare Dispose quando è necessario Mono per eliminare l'oggetto. Un possibile caso d'uso è quando Mono non è a conoscenza che il NSObject è effettivamente contenere un riferimento a una risorsa importante come la memoria o un pool di informazioni. In questi casi, è necessario chiamare Dispose per rilasciare immediatamente il riferimento alla memoria, anziché attendere che Mono esegua un ciclo di Garbage Collection.

Internamente, quando Mono crea [riferimenti NSString da stringhe C ,](~/ios/internals/api-design/nsstring.md)li elimina immediatamente per ridurre la quantità di lavoro che il Garbage Collector deve eseguire. Minore è il numero di oggetti da gestire, più veloce sarà l'esecuzione del catalogo globale.

#### <a name="when-to-keep-references-to-objects"></a>Quando mantenere i riferimenti agli oggettiWhen to Keep References to Objects

Un effetto collaterale che la gestione automatica della memoria ha è che il GC si libererà degli oggetti inutilizzati finché non ci sono riferimenti ad essi. Questo a volte può avere effetti collaterali sorprendenti, ad esempio, se si crea una variabile locale per contenere il controller di visualizzazione di primo livello, o la finestra di livello superiore, e quindi avere quelli svaniscono dietro la schiena.

Se non si mantiene un riferimento nelle variabili statiche o di istanza agli oggetti, Mono chiamerà volentitamente il metodo Dispose() su di essi e rilascerà il riferimento all'oggetto. Poiché questo potrebbe essere l'unico riferimento in sospeso, il runtime Objective-C distruggerà l'oggetto automaticamente.

## <a name="related-links"></a>Collegamenti correlati

- [Campi di associazione](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
