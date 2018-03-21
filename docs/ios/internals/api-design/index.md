---
title: Progettazione delle API
description: Prospettiva sulla progettazione delle API di xamarin
ms.topic: article
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 8c336799a4d46359a78432837101dad43b572aea
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2018
---
# <a name="api-design"></a>Progettazione delle API

Oltre alle librerie di classi di Base che fanno parte di Mono, il nucleo [xamarin](http://www.xamarin.com/iOS) viene fornito con le associazioni per iOS diverse API per consentire agli sviluppatori di creare applicazioni native per iOS con Mono.

La base di xamarin. IOS, è un motore di interoperabilità che colma il mondo con il mondo Objective-C come c#, nonché le associazioni per iOS API basate su C come CoreGraphics e [OpenGLES](#OpenGLES).

Il runtime di basso livello per comunicare con il codice Objective-C è nel [MonoTouch.ObjCRuntime](#MonoTouch.ObjCRuntime). Nella parte superiore di questa operazione, le associazioni per [Foundation](#MonoTouch.Foundation), CoreFoundation e [UIKit](#MonoTouch.UIKit) forniti.

## <a name="design-principles"></a>Principi di progettazione

Questi sono alcuni dei nostri principi di progettazione per l'associazione di xamarin. IOS (queste sono applicabili anche a Xamarin.Mac, le associazioni Mono per Objective-C su OS X):

- Seguire le linee guida
- Consentono agli sviluppatori di classi sottoclasse Objective-C:

  - Derivare una classe esistente
  - Chiamare il costruttore base a catena
  - È necessario eseguire l'override dei metodi con il sistema di sostituzione #

- Sottoclasse dovrebbe funzionare con i costrutti di linguaggio c# standard
- Non esporre agli sviluppatori di selettori Objective-C
- Forniscono un meccanismo per chiamare arbitrarie librerie Objective-C
- Rendono le comuni attività di Objective-C semplice e disco rigido possibili attività Objective-C
- Esporre le proprietà di Objective-C come proprietà in c#
- Esporre un'API fortemente tipizzata:
- Aumentare l'indipendenza dai tipi
- Ridurre al minimo gli errori di runtime
- È possibile ottenere intellisense IDE in tipi restituiti
- Consente la documentazione di popup IDE
- Incoraggiare l'esplorazione nell'IDE di API:
- Tipi c# nativi:

    - Esempio: anziché esporre la matrice con tipizzazione debole simile al seguente:
        ```
        NSArray *getViews
        ```
        è necessario esporre li con tipi sicuri, simile al seguente:
    
        ```
        NSView [] Views { get; set; }
        ```
    
    Questo consente di Visual Studio per Mac la possibilità di eseguire il completamento automatico durante l'esplorazione di API e consente anche a tutti i `System.Array` operazioni siano disponibili nel valore restituito e consente il valore restituito deve far parte di LINQ

- [NSString diventa stringa](~/ios/internals/api-design/nsstring.md)
- Attivare i parametri di tipo int e uint che avrebbe dovuto essere enumerazioni come c# enumerazioni e le enumerazioni di c# con attributi [Flags]
- Anziché oggetti NSArray indipendente dal tipo deve esporre matrici come matrice fortemente tipizzata.
- Eventi e notifiche, consentire agli utenti di scegliere tra:

    - Il valore predefinito è la versione fortemente tipizzata
    - Versione con tipizzazione per casi di utilizzo avanzato

- Supportare il pattern di delegato Objective-C:

    - Sistema di eventi del linguaggio c#
    - Esporre c# delegati (espressioni lambda, metodi anonimi e System. Delegate) alle API Objective-C come "blocchi"

### <a name="assemblies"></a>Assembly

Xamarin include un numero di assembly che costituiscono il *xamarin profilo*. Il [assembly](~/cross-platform/internals/available-assemblies.md) pagina è disponibili altre informazioni.

### <a name="major-namespaces"></a>Spazi dei nomi principale 

<a name="MonoTouch.ObjCRuntime" />

#### <a name="objcruntime"></a>ObjCRuntime

Il [ObjCRuntime](https://developer.xamarin.com/api/namespace/ObjCRuntime/) spazio dei nomi consente agli sviluppatori di colmare il mondo tra c# e Objective-C.
Si tratta di una nuova associazione, appositamente progettata per iOS, sulla base dell'esperienza di Cocoa # e # Gtk.

<a name="MonoTouch.Foundation" />

#### <a name="foundation"></a>Foundation

Il [Foundation](https://developer.xamarin.com/api/namespace/Foundation/) spazio dei nomi fornisce i tipi di dati di base è progettato per interagire con il framework Foundation Objective-C che fa parte di iOS e costituisce la base per orientata agli oggetti di programmazione in Objective-C.

Xamarin esegue il mirroring in c# la gerarchia di classi da Objective-C. Ad esempio, la classe di base Objective-C [NSObject](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/Reference/Reference.html) è usato da c# tramite [Foundation.NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/).

Sebbene questo spazio dei nomi fornisce associazioni per i tipi di Objective-C Foundation sottostante, in alcuni casi abbiamo eseguito il mapping di tipi sottostanti ai tipi .NET. Ad esempio:

- Anziché gestire [NSString](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html) e [NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html), il runtime espone queste informazioni come c# [stringa](https://developer.xamarin.com/api/type/System.String/)s e fortemente tipizzate [matrice](https://developer.xamarin.com/api/type/System.Array/)s in tutto l'API.

- Varie API di supporto vengono esposti di seguito per consentire agli sviluppatori di eseguire l'associazione di terze parti, le API Objective-C, altri iOS API o le API che non sono attualmente associate da xamarin.

Per ulteriori informazioni sull'associazione delle API, vedere il [xamarin associazione generatore](~/cross-platform/macios/binding/binding-types-reference.md) sezione.


##### <a name="nsobject"></a>NSObject

Il [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) tipo costituisce la base per tutte le associazioni di Objective-C. Tipi di xamarin. IOS mirror due classi di tipi da iOS CocoaTouch APIs: i tipi di C (in genere indicate come tipi CoreFoundation) e i tipi di Objective-C (questi derivano tutte dalla classe NSObject).

Per ogni tipo che rispecchia un tipo non gestito, è possibile ottenere l'oggetto nativo tramite il [gestire](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) proprietà.

Mentre Mono fornirà la garbage collection per tutti gli oggetti di `Foundation.NSObject` implementa il [System. IDisposable](https://developer.xamarin.com/api/type/System.IDisposable/) interfaccia. Ciò significa che è possibile rilasciare le risorse di qualsiasi dato NSObject in modo esplicito senza dover attendere per il Garbage Collector kick-in. Questo è importante quando si utilizza NSObjects pesante, ad esempio, UIImages che potrebbe contenere puntatori a grandi blocchi di dati.

Se il tipo deve eseguire la finalizzazione deterministica, eseguire l'override di [NSObject.Dispose(bool) metodo](https://developer.xamarin.com/api/type/Foundation.NSObject/%2fM%2fDispose) il parametro al metodo Dispose è "bool disposing", e se impostato su true, significa che viene chiamato il metodo Dispose perché l'utente chiamato in modo esplicito Dispose () per l'oggetto. Se il valore è false, ciò significa che il metodo Dispose (bool disposing) viene chiamato dal finalizzatore sul thread del finalizzatore. []()


##### <a name="categories"></a>Categories

A partire da xamarin 8.10, è possibile creare categorie di Objective-C da c#.

Questa operazione viene eseguita utilizzando il `Category` attributo, che specifica il tipo da estendere come un argomento dell'attributo. Nell'esempio seguente viene estesa per l'istanza NSString.

    [Category (typeof (NSString))]

Ogni metodo categoria sta utilizzando il meccanismo normale per l'esportazione di metodi in Objective-C utilizzando la `Export` attributo:

    [Export ("today")]
    public static string Today ()
    {
        return "Today";
    }

Tutti i metodi di estensione gestita devono essere statici, ma è possibile creare metodi di istanza Objective-C utilizzando la sintassi standard per i metodi di estensione in c#:

    [Export ("toUpper")]
    public static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }

e il primo argomento al metodo di estensione sarà l'istanza in cui è stato richiamato il metodo.

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

In questo esempio aggiungerà un metodo di istanza nativo toUpper alla classe NSString, che può essere richiamata da Objective-C.

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAudoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

Uno scenario in cui è utile consiste nell'aggiungere un metodo a un intero set di classi nella codebase, ad esempio, ciò rende tutti `UIViewController` istanze di report che è possibile ruotare:

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


##### <a name="preserveattribute"></a>PreserveAttribute

PreserveAttribute è un attributo personalizzato che viene utilizzato per indicare mtouch: lo strumento di distribuzione di xamarin: per mantenere un tipo o un membro di un tipo, durante la fase di elaborazione quando l'applicazione per ridurre le dimensioni.

Ogni membro che non è collegato in modo statico dall'applicazione è soggetto alla rimozione. Di conseguenza, questo attributo viene utilizzato per contrassegnare i membri che non fa in modo statico, ma che sono ancora necessari dall'applicazione.

Ad esempio, se si creano istanze dei tipi in modo dinamico, potrebbe essere necessario mantenere il costruttore predefinito dei tipi. Se si usa la serializzazione XML, potrebbe essere necessario mantenere le proprietà dei tipi.

È possibile applicare questo attributo in ogni membro di un tipo oppure nel tipo stesso. Se si desidera mantenere il tipo intero, è possibile utilizzare la sintassi [mantenere (AllMembers = true)] sul tipo.

<a name="MonoTouch.UIKit" />

#### <a name="uikit"></a>UIKit

Il [UIKit](https://developer.xamarin.com/api/namespace/UIKit/) spazio dei nomi contiene un mapping uno a tutti i componenti dell'interfaccia utente che costituiscono CocoaTouch sotto forma di classi c#. L'API è stata modificata per seguire le convenzioni utilizzate nel linguaggio c#.

I delegati in c# sono disponibili per operazioni comuni. Vedere il [delegati](#Delegates) sezione per ulteriori informazioni.

<a name="OpenGLES" />

#### <a name="opengles"></a>OpenGLES

Per OpenGLES, si distribuisce un [modificato versione](https://developer.xamarin.com/api/namespace/OpenTK/) del [OpenTK](http://www.opentk.com/) API, un'associazione e orientato a OpenGL che è stato modificato per l'utilizzo di tipi di dati CoreGraphics e strutture, nonché esporre solo le funzionalità disponibile in iOS.

Funzionalità OpenGLES 1.1 è disponibile tramite il tipo ES11.GL, documentato [qui](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES11.GL/) tipo.

Funzionalità OpenGLES 2.0 è disponibile tramite il tipo ES20.GL, documentato [qui](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES20.GL/) tipo.

Funzionalità OpenGLES 3.0 sono disponibili tramite il tipo ES30.GL, documentato [qui](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES30.GL/) tipo.


### <a name="binding-design"></a>Progettazione di associazione

Xamarin non è semplicemente un'associazione alla piattaforma sottostante Objective-C. Estende il sistema di tipi .NET e al sistema di recapito di blend migliori in c# e Objective-C.

Come P/Invoke è uno strumento utile per richiamare le librerie native in Windows e Linux, o come IJW supporto può essere utilizzato per l'interoperabilità COM in Windows, xamarin consente di estendere il runtime per supportare c# oggetti di associazione di Objective-C.

La discussione nelle prossime sezioni non è necessario che gli utenti che creano applicazioni di xamarin, ma gli sviluppatori potranno comprendere come operazioni vengono eseguite e li semplificheranno quando si creano applicazioni più complesse.



#### <a name="types"></a>Tipi

I tipi c# in cui è eseguita senso, vengono esposte anziché tipi di basso livello Foundation, all'universo c#.  Ciò significa che [l'API utilizza il tipo "string" c# anziché NSString](~/ios/internals/api-design/nsstring.md) e viene utilizzato c# matrici fortemente tipizzate anziché esporre NSArray.

In generale, nella progettazione di xamarin. IOS e Xamarin.Mac sottostante `NSArray` oggetto non è esposto. Al contrario, il runtime converte automaticamente `NSArray`s alle matrici fortemente tipizzate di alcuni `NSObject` classe. In tal caso, xamarin. IOS non espone un metodo con tipizzazione debole come GetViews per restituire un NSArray:

```csharp
NSArray GetViews ();
```

Al contrario, l'associazione espone un valore restituito fortemente tipizzato, simile al seguente:

```csharp
UIView [] GetViews ();
```

Ci sono alcuni dei metodi esposti `NSArray`, per i casi estremi, in cui si desidera utilizzare un `NSArray` direttamente, ma è sconsigliato l'uso nell'associazione API.

Inoltre, nel **API classica** anziché esporre `CGRect`, `CGPoint` e `CGSize` dall'API CoreGraphics, pertanto abbiamo sostituito con il `System.Drawing` implementazioni `RectangleF`, `PointF`e `SizeF` come verrebbe consentono agli sviluppatori mantenere codice OpenGL esistente che utilizza OpenTK. Quando si utilizza il nuovo 64-bit **Unified API**, l'API CoreGraphics deve essere utilizzato.

<a name="Inheritance" />

#### <a name="inheritance"></a>Ereditarietà

La progettazione delle API di xamarin consente agli sviluppatori di estendere i tipi nativi Objective-C nello stesso modo che estendono un tipo in c#, utilizzando la parola chiave "override" in una classe derivata, nonché di concatenamento di per l'implementazione di base tramite la "base" parola chiave c#.

Questa progettazione consente agli sviluppatori di evitare di dover gestire con i selettori Objective-C come parte del processo di sviluppo, poiché l'intero sistema Objective-C è già incluso all'interno delle librerie di xamarin. IOS.


#### <a name="types-and-interface-builder"></a>Tipi e il generatore di interfaccia

Quando si creano classi .NET che sono istanze dei tipi creati dal generatore di interfaccia, è necessario fornire un costruttore che accetta un singolo `IntPtr` parametro.
Ciò è necessario associare l'istanza di oggetto gestito con l'oggetto non gestito.
Il codice è costituito da una singola riga, simile al seguente:

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

<a name="Delegates" />


#### <a name="delegates"></a>Delegati

Objective-C e c# hanno significati diversi per il delegato di word in ciascuna lingua.

Nel mondo Objective-C e nella documentazione disponibili in linea su CocoaTouch, un delegato è in genere un'istanza di una classe che risponda a un set di metodi. È molto simile a un'interfaccia in c#, con la differenza che i metodi non sono sempre obbligatori.

Questi delegati svolgono un ruolo importante in UIKit e altre APIs CocoaTouch. Vengono utilizzate per eseguire diverse attività:

-  Per fornire notifiche al codice (come per l'invio di eventi in c# o Gtk +).
-  Per implementare i modelli per i controlli di visualizzazione di dati.
-  Per gestire il comportamento di un controllo.


Il modello di programmazione è stato progettato per ridurre al minimo la creazione di classi derivate di modificare il comportamento per un controllo. Questa soluzione è simile a quello altri Toolkit GUI hanno eseguito nel corso degli anni bevande: Gtk segnali slot Qt, gli eventi di Windows Form, WPF o Silverlight eventi e così via. Per evitare centinaia di interfacce (uno per ogni azione) o richiedere agli sviluppatori di implementare un numero eccessivo di metodi che non richiedono, Objective-C supporta le definizioni di metodo facoltativo. Ciò è diverso da c# le interfacce che richiedono tutti i metodi da implementare.

Nelle classi Objective-C, si noterà che le classi che utilizzano il modello di programmazione espongono una proprietà denominata in genere `delegate`, che è necessario per implementare le parti obbligatorie dell'interfaccia e parti di zero o più delle facoltativo.

Tre meccanismi si escludono a vicenda per associare questi delegati disponibili in xamarin. ios:

1.  [Tramite gli eventi](#Via_Events).
2.  [Fortemente tipizzato tramite una `Delegate` proprietà](#StrongDelegate)
3.  [Con tipizzazione debole tramite un `WeakDelegate` proprietà](#WeakDelegate)

Si consideri ad esempio il [UIWebView](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html) classe. Si invia a un [UIWebViewDelegate](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html) istanza, che viene assegnato al [delegato](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html#//apple_ref/occ/instp/UIWebView/delegate) proprietà.

<a name="Via_Events" />

##### <a name="via-events"></a>Tramite gli eventi

Per molti tipi, xamarin. IOS creerà automaticamente un delegato appropriato che inoltrerà le `UIWebViewDelegate` chiamate su eventi di c#. Per `UIWebView`:

-  Il [webViewDidStartLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidStartLoad:) metodo viene eseguito il mapping per il [UIWebView.LoadStarted](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadStarted/) evento.
-  Il [webViewDidFinishLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidFinishLoad:) metodo viene eseguito il mapping per il [UIWebView.LoadFinished](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadFinished/) evento.
-  Il [webView:didFailLoadWithError](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webView:didFailLoadWithError:) metodo viene eseguito il mapping per il [UIWebView.LoadError](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadError/) evento.

Ad esempio, questo semplice programma registra l'ora di inizio e fine quando il caricamento di un sito web Visualizza:

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```


##### <a name="via-properties"></a>Tramite la proprietà

Gli eventi sono utili quando potrebbero essere presenti più di un sottoscrittore per l'evento. Inoltre, gli eventi sono limitati ai casi in cui è presente alcun valore restituito dal codice.

Per i casi in cui è previsto il codice per restituire un valore, abbiamo scelto invece per le proprietà. Ciò significa che un solo metodo può essere impostato in un momento specifico in un oggetto.

Ad esempio, è possibile utilizzare questo meccanismo per ignorare la tastiera su schermo sul gestore per un `UITextField`:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

Il `UITextField`del `ShouldReturn` proprietà in questo caso accetta come argomento un delegato che restituisce un valore bool e determina se TextField deve eseguire un'operazione con la pressione del pulsante restituito. Nel metodo di restituiamo *true* al chiamante, ma è anche di rimuovere dalla schermata della tastiera (ciò accade quando chiama textfield `ResignFirstResponder`).

<a name="StrongDelegate"/>

##### <a name="strongly-typed-via-a-delegate-property"></a>Fortemente tipizzato tramite una proprietà di delegato

Se si preferisce non utilizzare gli eventi, è possibile fornire la propria [UIWebViewDelegate](https://developer.xamarin.com/api/type/UIKit.UIWebViewDelegate/) sottoclasse e assegnarlo al [UIWebView.Delegate](https://developer.xamarin.com/api/property/UIKit.UIWebView.Delegate/) proprietà. Una volta assegnato UIWebView.Delegate, il meccanismo di invio evento UIWebView non funzionerà più e i metodi UIWebViewDelegate verranno richiamati quando si verificano gli eventi corrispondenti.

Ad esempio, questo tipo semplice registra il tempo che necessario per caricare una visualizzazione web:

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

Viene utilizzato il precedente nel codice simile al seguente:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

Il precedente creerà una UIWebViewer e indicherà per inviare messaggi a un'istanza di notifica, una classe creata per rispondere ai messaggi.

Questo modello viene usato anche per controllare il comportamento di alcuni controlli, ad esempio nel caso UIWebView, il [UIWebView.ShouldStartLoad](https://developer.xamarin.com/api/property/UIKit.UIWebView.ShouldStartLoad/) proprietà consente il `UIWebView` istanza per controllare se il `UIWebView` caricherà un pagina o non.

Il modello viene utilizzato anche per fornire i dati su richiesta per alcuni controlli. Ad esempio, il [UITableView](https://developer.xamarin.com/api/type/UIKit.UITableView/) il controllo è un controllo tabella rendering potente: e sia l'aspetto e il relativo contenuto è dovuto a un'istanza di un [UITableViewDataSource](https://developer.xamarin.com/api/type/UIKit.UITableView/DataSource)

<a name="WeakDelegate"/>

### <a name="loosely-typed-via-the-weakdelegate-property"></a>Con tipizzazione debole tramite la proprietà WeakDelegate

Oltre alle proprietà fortemente tipizzate, è inoltre disponibile un debole delegato tipizzato che consente allo sviluppatore di associare elementi in modo diverso se si desidera.
Un oggetto fortemente tipizzato ovunque `Delegate` proprietà viene esposta in xamarin. IOS associazione, un corrispondente `WeakDelegate` proprietà viene esposta anche.

Quando si utilizza il `WeakDelegate`, si è responsabili correttamente la decorazione di classe utilizzando il [esportare](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) attributo per specificare il selettore. Ad esempio:

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

Si noti che una volta il `WeakDelegate` proprietà è stata assegnata, il `Delegate` proprietà non verrà utilizzata. Inoltre, se si implementa il metodo in una classe di base ereditata da [Esporta], è necessario renderlo un metodo pubblico.


## <a name="mapping-of-the-objective-c-delegate-pattern-to-c35"></a>Mapping del modello delegato Objective-C da C&#35;

Quando viene visualizzato Objective-C esempi simili al seguente:

```csharp
foo.delegate = [[SomethingDelegate] alloc] init]
```

Questo indica la lingua per creare e costruire un'istanza della classe "SomethingDelegate" e assegnare il valore alla proprietà della variabile foo delegato. Questo meccanismo è supportato da xamarin. IOS e la sintassi di c#:

```csharp
foo.Delegate = new SomethingDelegate ();
```

In xamarin. IOS sono fornite le classi di delegare classi fortemente tipizzate che eseguono il mapping per Objective-C. A questo scopo, si verrà sottoclassi e l'override dei metodi definiti dall'implementazione di xamarin. Per ulteriori informazioni sul relativo funzionamento, vedere il sezione "modelli" riportata di seguito.


##### <a name="mapping-delegates-to-c35"></a>Mapping di delegati a C&#35;

In generale, UIKit utilizza delegati Objective-C in due formati.

Il primo form fornisce un'interfaccia per il modello di un componente. Ad esempio, un meccanismo per fornire dati su richiesta per una vista, ad esempio la funzionalità di archiviazione di dati per una visualizzazione elenco.  In questi casi, è sempre necessario creare un'istanza della classe appropriata e assegnare la variabile.

Nell'esempio seguente, si forniscono le `UIPickerView` con un'implementazione per un modello che utilizza le stringhe:

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

La seconda forma consiste nel fornire la notifica degli eventi. In questi casi, anche se è necessario esporre ancora l'API nel formato descritto in precedenza, è possibile utilizzare eventi in c# che devono essere più semplice da utilizzare per operazioni rapide e integrati con i delegati anonimi ed espressioni lambda in c#.

Ad esempio, è possibile sottoscrivere `UIAccelerometer` eventi:

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

Le due opzioni sono disponibili in cui più significativi, ma i programmatori è necessario selezionare uno o l'altro. Se si crea la propria istanza di un risponditore/delegato fortemente tipizzata e assegnarlo, gli eventi in c# non saranno funzionali. Se si utilizzano gli eventi in c#, i metodi nella classe del risponditore/delegato non verranno mai chiamati.

Nell'esempio precedente viene utilizzato `UIWebView` possono essere scritte con le espressioni lambda in c# 3.0 simile al seguente:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```


#### <a name="responding-to-events"></a>Risposta agli eventi

Nel codice Objective-C, a volte i gestori eventi per più controlli e i provider di informazioni per più controlli, verrà ospitata nella stessa classe. Ciò è possibile perché le classi di rispondere ai messaggi e come classi di rispondono ai messaggi, è possibile collegare gli oggetti.

Come descritto in precedenza, xamarin supporta sia il linguaggio c# basato su eventi modello di programmazione e il modello di delegato Objective-C, in cui è possibile creare una nuova classe che implementa il delegato e sostituisce i metodi desiderati.

È inoltre possibile supportare pattern Objective-C dove risponditori per più operazioni diverse sono tutti ospitati nella stessa istanza di una classe. A questo scopo tuttavia, è necessario utilizzare le funzionalità di basso livello dell'associazione di xamarin. IOS.

Ad esempio, se si desidera che la classe per rispondere a entrambi il `UITextFieldDelegate.textFieldShouldClear`: messaggio e `UIWebViewDelegate.webViewDidStartLoad`: nella stessa istanza di una classe, è necessario usare la dichiarazione di attributo [Esporta]:

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

I nomi in c# per i metodi non sono importanti, tutto ciò che è importante sono le stringhe passate all'attributo [Esporta].

Quando si utilizza questo stile di programmazione, assicurarsi che i parametri c# corrispondono i tipi effettivi che verrà passati il motore di runtime.

<a name="Models" />

#### <a name="models"></a>Modelli

In strutture di archiviazione UIKit o in risponditori implementate utilizzando le classi di supporto, vengono in genere definite nel codice Objective-C come delegati e vengono implementate come protocolli.

I protocolli Objective-C sono come le interfacce, ma supportano metodi facoltativi: non tutti i metodi, è necessario implementare per il protocollo da utilizzare.

Esistono due modalità di implementazione di un modello. È possibile implementarlo manualmente o utilizzare le definizioni esistenti fortemente tipizzate.


Il meccanismo manuale è necessario quando si tenta di implementare una classe che non è stata associata da xamarin. IOS. È molto semplice:

-  Contrassegnare la classe per la registrazione con il runtime
-  Applicare l'attributo [Esporta] con il nome effettivo del selettore su ogni metodo che si desidera eseguire l'override
-  Un'istanza della classe e passarlo.

Ad esempio, le operazioni seguenti implementare solo uno dei metodi facoltativi nella definizione del protocollo UIApplicationDelegate:

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

Il nome del selettore Objective-C ("applicationDidFinishLaunching:") viene dichiarato con l'attributo di esportazione e la classe è registrata con il `[Register]` attributo.

Xamarin offre fortemente tipizzate dichiarazioni, pronto per l'utilizzo, che non richiedono l'associazione manuale. Per supportare questo modello di programmazione, il runtime xamarin supporta l'attributo [modello] in una dichiarazione di classe. Informa il runtime che deve associare tutti i metodi nella classe, non a meno che non sono i metodi è implementato in modo esplicito.

Ciò significa che in UIKit, le classi che rappresentano un protocollo con metodi facoltativi vengono scritti simile al seguente:

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

Quando si desidera implementare un modello che implementa solo alcuni dei metodi, è necessario effettuare è sufficiente l'override dei metodi di interesse e ignorare gli altri metodi. Il runtime verrà associato solo i sovrascrivere i metodi, non l'originale al mondo Objective-C.

È equivalente all'esempio precedente manuale:

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

I vantaggi sono che non è necessario per esaminare i file di intestazione Objective-C per trovare il selettore, i tipi di argomenti o il mapping a c#, e di poter usare intellisense da Visual Studio per Mac, oltre ai tipi sicuri


#### <a name="xib-outlets-and-c35"></a>C e prese XI&#35;

> [!IMPORTANT]
> Questa sezione illustra l'integrazione di IDE con prese quando si utilizzano file XI. Quando si utilizza la finestra di progettazione di Xamarin per iOS, questo è tutto sostituito immettendo un nome in **identità > nome** nella sezione proprietà dell'IDE, come illustrato di seguito:
>
> [![](images/designeroutlet.png "Immettere un nome di elemento nella finestra di progettazione iOS")](images/designeroutlet.png#lightbox)
>
>Per ulteriori informazioni sulla finestra di progettazione iOS, consultare il [Introduzione a progettazione iOS](~/ios/user-interface/designer/introduction.md#how-it-works) documento.

Questa è una descrizione di basso livello della modalità di integrazione prese con c# e viene fornita per gli utenti esperti di xamarin. IOS. Con Visual Studio per Mac il mapping quando viene eseguita automaticamente in background tramite generato codice il volo.

Quando si progetta un'interfaccia utente con il generatore di interfaccia, è solo possibile progettare l'aspetto dell'applicazione e stabilirà alcune connessioni predefinito. Se si desidera recuperare le informazioni, modificare il comportamento di un controllo in fase di esecuzione o modificare il controllo in fase di esecuzione a livello di codice, è necessario associare alcuni controlli a codice gestito.

Questa operazione viene eseguita in pochi passaggi:

1.  Aggiungere il **dichiarazione presa** per il **proprietario del File**.
1.  Connettersi a un controllo di **proprietario del File**.
1.  Archiviare l'interfaccia utente e le connessioni all'interno del file XI/NIB.
1.  Caricare il file NIB in fase di esecuzione.
1.  Accedere alla variabile di uscita.


I passaggi (1) e (3) vengono trattati nella documentazione di Apple per la creazione di interfacce con il generatore di interfaccia.

Quando si usa xamarin. IOS, è necessario creare una classe che deriva da UIViewController l'applicazione. Viene implementato è simile al seguente:

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

Quindi per caricare il ViewController da un file NIB, a tale scopo:

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

L'interfaccia utente viene caricato dall'ufficio. A questo punto, per l'accesso di base, è necessario informare il runtime che si vuole accedere. A tale scopo, il `UIViewController` sottoclasse deve dichiarare le proprietà e aggiungervi annotazioni con l'attributo [connessione]. analogamente a quanto segue:

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

L'implementazione della proprietà è quella effettivamente recupera e memorizza il valore per il tipo nativo effettivo.

Non occorre preoccuparsi questo quando si utilizza Visual Studio per Mac e InterfaceBuilder. Visual Studio per Mac rispecchia automaticamente tutti i punti vendita dichiarato con il codice in una classe parziale che viene compilato come parte del progetto.

#### <a name="selectors"></a>Selettori

Un concetto fondamentale della programmazione Objective-C è selettori. Si incontreranno spesso API che richiedono il passaggio di un selettore o prevede che il codice per rispondere a un selettore.

Creazione di nuovi selettori in c# è molto semplice: sufficiente creare una nuova istanza della `ObjCRuntime.Selector` classe e utilizzare il risultato in qualsiasi punto dell'API che lo richiede. Ad esempio:

```csharp
var selector_add = new Selector ("add:plus:");
```

Per un c# metodo rispondono a una chiamata di selettore, deve ereditare dal `NSObject` tipo e del metodo c# deve essere decorate con il nome di selettore mediante il `[Export]` attributo. Ad esempio:

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

Si noti il selettore nomi **deve** corrispondere esattamente, inclusi tutti i due punti intermedi e finali (":"), se presente.

#### <a name="nsobject-constructors"></a>Costruttori NSObject

La maggior parte delle classi in xamarin. IOS che derivano da `NSObject` esporrà costruttori specifici per la funzionalità dell'oggetto, ma espongono anche vari costruttori che non sono immediatamente ovvi.

I costruttori vengono utilizzati come segue:

```csharp
public Foo (IntPtr handle)
```

Questo costruttore viene utilizzato per creare un'istanza della classe quando il runtime deve eseguire il mapping della classe a una classe non gestita. Ciò accade quando si carica un file XI/NIB.  A questo punto, il runtime Objective-C verrà creato un oggetto nel mondo non gestito e verrà chiamato il costruttore per inizializzare il lato gestito.

È in genere, è sufficiente chiamare il costruttore base con il parametro di handle e nel corpo, effettuare qualsiasi inizializzazione che è necessaria.

```csharp
public Foo ()
```

Questo è il costruttore predefinito per una classe, in xamarin. IOS fornite le classi, si e inizializza la classe Foundation.NSObject e tutte le classi tra, al termine, si collega questo Objective-C `init` metodo nella classe.

```csharp
public Foo (NSObjectFlag x)
```

Questo costruttore viene utilizzato per inizializzare l'istanza, ma impedisce che il codice del metodo Objective-C "init" alla fine. È in genere utilizzarlo quando è già stato registrato per l'inizializzazione (quando si utilizza `[Export]` nel costruttore del) o quando è già stata eseguita l'inizializzazione tramite un altro valore medio.

```csharp
public Foo (NSCoder coder)
```

Questo costruttore viene fornito per i casi in cui di inizializzazione dell'oggetto da un'istanza di NSCoding. Per ulteriori informazioni, vedere Apple [archivi e Guida alla programmazione di serializzazione.](http://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/Archiving/index.html#//apple_ref/doc/uid/10000047i)

#### <a name="exceptions"></a>Eccezioni

La progettazione delle API di xamarin. IOS non genera eccezioni Objective-C come eccezioni c#. La progettazione garantisce che nessun garbage da inviare in primo luogo al mondo Objective-C e che tutte le eccezioni che devono essere prodotti sono generate dall'associazione stesso prima che siano mai di dati non validi passati al mondo Objective-C.

#### <a name="notifications"></a>Notifiche

In iOS e OS X, gli sviluppatori possono sottoscrivere le notifiche che vengono trasmessi dalla piattaforma sottostante. Questa operazione viene eseguita tramite il `NSNotificationCenter.DefaultCenter.AddObserver` metodo. Il `AddObserver` metodo accetta due parametri, uno è la notifica che si desidera eseguire la sottoscrizione; l'altra è il metodo da richiamare quando viene generata la notifica.

In xamarin. IOS e Xamarin.Mac, le chiavi per le diverse notifiche sono ospitate nella classe che attiva le notifiche. Le notifiche, ad esempio generato dal `UIMenuController` vengono ospitati come `static NSString` proprietà il `UIMenuController` classi che terminano con il nome "Notifica".

### <a name="memory-management"></a>Gestione della memoria

Xamarin è un garbage collector che si occuperà di rilascio delle risorse per l'utente quando non sono più in uso. Oltre il garbage collector, tutti gli oggetti che derivano da `NSObject` implementare il `System.IDisposable` interfaccia.

#### <a name="nsobject-and-idisposable"></a>NSObject e IDisposable

Esposizione di `IDisposable` interfaccia è un modo pratico di assistere gli sviluppatori il rilascio di oggetti che potrebbero incapsulare grandi blocchi di memoria (ad esempio, un `UIImage` potrebbe essere simile a un puntatore di inoffensivo, ma potrebbe essere che punta a un'immagine 2 megabyte ) e altre risorse importanti e finiti (ad esempio, un buffer di decodifica video).

NSObject implementa l'interfaccia IDisposable e anche il [modello Dispose .NET](http://msdn.microsoft.com/en-us/library/fs2xkftw.aspx). Questo consente agli sviluppatori che sottoclasse NSObject per eseguire l'override del comportamento di eliminazione e rilasciare le proprie risorse su richiesta. Si consideri, ad esempio, questo controller di visualizzazione che dispone di una serie di immagini:

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

Quando viene eliminato un oggetto gestito, non è più utile. Potrebbe tuttavia essere un riferimento agli oggetti, ma l'oggetto è valido a tutti gli effetti a questo punto. Alcune API .NET verificare questo generando un'eccezione ObjectDisposedException se si tenta di accedere ai metodi su un oggetto eliminato, ad esempio:

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

Anche se è ancora possibile accedere alla variabile "image", è effettivamente un riferimento non valido e non sono più punti all'oggetto Objective-C che contengono l'immagine.

Ma l'eliminazione di un oggetto nel linguaggio c# non significa che l'oggetto verrà eliminato necessariamente. Non è di rilasciare il riferimento con c# per l'oggetto. È possibile che l'ambiente Cocoa potrebbe essere mantenuto un riferimento per il proprio uso. Ad esempio, se è impostata di una UIImageView immagine di un'immagine e quindi si elimina l'immagine, il UIImageView sottostante venga eseguita il proprio riferimento e manterrà un riferimento a questo oggetto fino al termine utilizzarlo.

#### <a name="when-to-call-dispose"></a>Quando chiamare il metodo Dispose

È consigliabile chiamare Dispose quando è necessario Mono nei eliminare l'oggetto. Un possibile utilizzo viene Mono non ha alcuna conoscenza che il NSObject effettivamente è presente un riferimento a una risorsa importante, ad esempio memoria o un pool di informazioni. In questi casi, è consigliabile chiamare Dispose per rilasciare immediatamente il riferimento alla memoria, anziché attendere Mono eseguire un ciclo di garbage collection.

Internamente, quando crea Mono [NSString fa riferimento da c# stringhe](~/ios/internals/api-design/nsstring.md), eliminerà immediatamente per ridurre la quantità di lavoro che deve eseguire il garbage collector. Verranno eseguito meno oggetti per gestire, più velocemente il Garbage Collector.

#### <a name="when-to-keep-references-to-objects"></a>Quando mantenere riferimenti a oggetti

Un effetto con gestione automatica della memoria è che il Garbage Collector verrà eliminarle oggetti inutilizzati, purché non siano presenti riferimenti a essi. Questo talvolta può avere effetti collaterali imprevisti, ad esempio, se si crea una variabile locale per contenere il controller di vista di primo livello, o la finestra di primo livello e fare in modo che quelli scompaiono dietro il back.

Se non si mantiene un riferimento nello statico o variabili di istanza agli oggetti, Mono tranquillamente chiamerà il metodo Dispose () su di essi, e rilascerà il riferimento all'oggetto. Poiché questo potrebbe essere il riferimento solo in sospeso, il runtime Objective-C eliminerà l'oggetto.

## <a name="related-links"></a>Collegamenti correlati

- [Associazione di campi](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
