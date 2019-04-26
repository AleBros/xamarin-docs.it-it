---
title: Progettazione di API xamarin. IOS
description: Questo documento illustra alcuni principi guida che consentono di progettare le APIs Xamarin.iOS e come tali elementi sono correlati a Objective-C.
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 9425b26b5cc8fcd9b8a80df422d932c96d52889b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61037458"
---
# <a name="xamarinios-api-design"></a>Progettazione di API xamarin. IOS

Oltre alle librerie di classi di Base che fanno parte di Mono, il nucleo [xamarin. IOS](http://www.xamarin.com/iOS) viene fornito con le associazioni per iOS varie API per consentire agli sviluppatori di creare applicazioni iOS native con Mono.

Alla base di xamarin. IOS, vi è un motore di interoperabilità che colma il world usando c# con il mondo di Objective-C, nonché le associazioni per le API basate su C, ad esempio CoreGraphics iOS e [OpenGL ES](#OpenGLES).

Il runtime di basso livello per comunicare con il codice Objective-C è nel [MonoTouch.ObjCRuntime](#MonoTouch.ObjCRuntime). Nella parte superiore di questa operazione, le associazioni per [Foundation](#MonoTouch.Foundation), CoreFoundation, e [UIKit](#MonoTouch.UIKit) forniti.

## <a name="design-principles"></a>Principi di progettazione

Questi sono alcuni dei nostri principi di progettazione per i binding xamarin. IOS (si applicano anche a xamarin. Mac, le associazioni di Mono per Objective-C in macOS):

- Seguire il [linee guida di progettazione di Framework](https://docs.microsoft.com/dotnet/standard/design-guidelines)
- Consentire agli sviluppatori di classi di sottoclasse Objective-C:

  - Derivare da una classe esistente
  - Chiamare il costruttore di base per catena
  - L'override dei metodi devono essere eseguite con C#di eseguire l'override del sistema
  - Creazione di una sottoclasse dovrebbero funzionare con costrutti di linguaggio c# standard

- Non esporre agli sviluppatori di selettori Objective-C
- Fornire un meccanismo per chiamare arbitrarie librerie Objective-C
- Eseguono attività comuni di Objective-C semplice e disco rigido possibili attività Objective-C
- Esporre le proprietà di Objective-C come proprietà di c#
- Esporre un'API fortemente tipizzata:

  - Aumentare l'indipendenza dai tipi
  - Ridurre al minimo gli errori di runtime
  - Ottenere IntelliSense IDE nei tipi restituiti
  - Consente la documentazione dell'IDE di popup

- Incoraggiare esplorazione nell'IDE di API:

  - Ad esempio, anziché esporre una matrice con tipizzazione debole simile al seguente:
    
    ```objc
    NSArray *getViews
    ```
    Esporre un tipo sicuro, simile al seguente:
    
    ```csharp
    NSView [] Views { get; set; }
    ```
    
    Ciò fornisce Visual Studio per Mac la possibilità di eseguire operazioni di completamento automatico durante l'esplorazione dell'API, di tutte le `System.Array` operazioni disponibili per il valore restituito e consente di far parte di LINQ, il valore restituito.

- Tipi c# nativi:

  - [`NSString` diventa `string`](~/ios/internals/api-design/nsstring.md)
  - Attivare `int` e `uint` i parametri che avrebbe dovuto essere enum in c# le enumerazioni e le enumerazioni c# con `[Flags]` attributi
  - Invece di tipo indipendente dalla `NSArray` oggetti, l'esposizione di matrici come matrici fortemente tipizzato.
  - Per gli eventi e notifiche, consentire agli utenti di scegliere tra:

    - Una versione fortemente tipizzata per impostazione predefinita
    - Una versione con tipizzazione debole per i casi d'uso avanzata

- Supportano il modello di delegato di Objective-C:

    - Sistema di eventi in c#
    - Esporre delegati c# (espressioni lambda, metodi anonimi, e `System.Delegate`) per le API Objective-C come blocchi

### <a name="assemblies"></a>Assembly

Xamarin. IOS include una serie di assembly che costituiscono il *profilo di xamarin. IOS*. Il [assembly](~/cross-platform/internals/available-assemblies.md) pagina sono disponibili altre informazioni.

### <a name="major-namespaces"></a>Spazi dei nomi principale 

<a name="MonoTouch.ObjCRuntime" />

#### <a name="objcruntime"></a>ObjCRuntime

Il [ObjCRuntime](xref:ObjCRuntime) dello spazio dei nomi consente agli sviluppatori di colmare i mondi tra c# e Objective-C.
Si tratta di una nuova associazione, progettata specificamente per iOS, basati sull'esperienza di Cocoa # e Gtk #.

<a name="MonoTouch.Foundation" />

#### <a name="foundation"></a>Foundation

Il [Foundation](xref:Foundation) dello spazio dei nomi fornisce i tipi di dati di base progettato per interagire con il framework di Objective-C Foundation che fa parte di iOS ed è la base per orientate a oggetti di programmazione in Objective-C.

Xamarin. IOS rispecchia in c# la gerarchia di classi da Objective-C. Ad esempio, la classe di base di Objective-C [NSObject](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/Reference/Reference.html) può essere usata da c# tramite [Foundation.NSObject](xref:Foundation.NSObject).

Anche se questo spazio dei nomi fornisce le associazioni per i tipi di Objective-C Foundation sottostante, in alcuni casi è stato eseguito il mapping di tipi sottostanti ai tipi .NET. Ad esempio:

- Invece di occuparsi [NSString](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html) e [NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html), il runtime espone queste informazioni come c# [stringa](xref:System.String)s e fortemente tipizzata [matrice](xref:System.Array)s in tutto l'API.

- Varie API di supporto sono esposti di seguito per consentire agli sviluppatori di eseguire l'associazione di terze parti, le API Objective-C, altri iOS le API o le API che non sono attualmente associate da xamarin. IOS.

Per altre informazioni sull'associazione delle API, vedere la [generatore di Binding xamarin. IOS](~/cross-platform/macios/binding/binding-types-reference.md) sezione.


##### <a name="nsobject"></a>NSObject

Il [NSObject](xref:Foundation.NSObject) tipo costituisce la base per tutti i binding di Objective-C. Tipi di xamarin. IOS il mirroring a due classi dei tipi da iOS CocoaTouch APIs: i tipi C (in genere definiti come tipi CoreFoundation) e i tipi di Objective-C (questi derivano dalla classe NSObject tutte).

Per ogni tipo che rispecchia un tipo non gestito, è possibile ottenere l'oggetto nativo tramite il [gestire](xref:Foundation.NSObject.Handle) proprietà.

Mentre Mono fornirà la garbage collection per tutti gli oggetti, il `Foundation.NSObject` implementa la [System. IDisposable](xref:System.IDisposable) interfaccia. Ciò significa che è possibile rilasciare in modo esplicito le risorse di qualsiasi dato NSObject senza dover attendere che il Garbage Collector kick-in. Questo è importante quando si usa NSObjects pesanti, ad esempio, UIImages che potrebbe contenere puntatori a grandi blocchi di dati.

Se il tipo deve effettuare la finalizzazione deterministica, eseguire l'override di [NSObject.Dispose(bool) metodo](xref:Foundation.NSObject.Dispose(System.Boolean)) il parametro al metodo Dispose è "bool disposing", e se impostato su true, significa che viene chiamato il metodo Dispose perché l'utente chiamato in modo esplicito Dispose () sull'oggetto. Se il valore è false, significa che il metodo Dispose (bool disposing) è in corso chiamato dal finalizzatore nel thread finalizzatore. []()


##### <a name="categories"></a>Categories

A partire da xamarin. IOS 8.10, è possibile creare categorie di Objective-C da c#.

Questa operazione viene eseguita usando il `Category` attributo, che specifica il tipo da estendere come argomento di attributo. Nell'esempio seguente, ad esempio estenderà NSString.

    [Category (typeof (NSString))]

Ogni metodo categoria consiste nell'usare il normale meccanismo per l'esportazione di metodi in Objective-C tramite il `Export` attributo:

    [Export ("today")]
    public static string Today ()
    {
        return "Today";
    }

Tutti i metodi di estensione gestita devono essere statici, ma è possibile creare metodi di istanza di Objective-C utilizzando la sintassi standard per i metodi di estensione in c#:

    [Export ("toUpper")]
    public static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }

e il primo argomento al metodo di estensione sarà costituito dall'istanza in cui è stato richiamato il metodo.

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

In questo esempio verrà aggiunto un metodo di istanza toUpper nativa per la classe NSString, che può essere richiamata da Objective-C.

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

Uno scenario in cui ciò è utile è aggiunta di un metodo per un intero set di classi nella codebase, ad esempio, ciò rende tutti `UIViewController` istanze di report che è possibile ruotare:

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

PreserveAttribute è un attributo personalizzato che viene utilizzato per indicare di mtouch: lo strumento di distribuzione di xamarin. ios: per mantenere un tipo o membro di un tipo, durante la fase durante l'elaborazione dell'applicazione per ridurre le relative dimensioni.

Ogni membro che non è collegato in modo statico dall'applicazione è soggetto alla rimozione. Di conseguenza, questo attributo viene utilizzato per contrassegnare i membri che non fa in modo statico, ma che sono comunque necessari per l'applicazione.

Ad esempio, se si creano istanze dei tipi in modo dinamico, potrebbe essere necessario mantenere il costruttore predefinito dei tipi. Se si usa la serializzazione XML, potrebbe essere necessario mantenere le proprietà dei tipi.

È possibile applicare questo attributo in ogni membro di un tipo oppure nel tipo stesso. Se si desidera mantenere l'intero tipo, è possibile usare la sintassi [mantenere (AllMembers = true)] sul tipo.

<a name="MonoTouch.UIKit" />

#### <a name="uikit"></a>UIKit

Il [UIKit](xref:UIKit) dello spazio dei nomi contiene un mapping uno a uno a tutti i componenti dell'interfaccia utente che costituiscono CocoaTouch sotto forma di classi c#. L'API è stata modificata per seguire le convenzioni usate nel linguaggio c#.

Delegati c# sono disponibili per le operazioni comuni. Vedere le [delegati](#Delegates) sezione per altre informazioni.

<a name="OpenGLES" />

#### <a name="opengles"></a>OpenGLES

Per OpenGLES, si distribuisce un [modificato per versione](xref:OpenTK) delle [OpenTK](http://www.opentk.com/) API, un'associazione e orientato a OpenGL che è stato modificato per usare i tipi di dati CoreGraphics e strutture, nonché esporre solo il funzionalità disponibile in iOS.

OpenGLES 1.1 è disponibile tramite il tipo ES11.GL, documentato [qui](xref:OpenTK.Graphics.ES11.GL) tipo.

Funzionalità OpenGLES 2.0 è disponibile tramite il tipo ES20.GL, documentato [qui](xref:OpenTK.Graphics.ES20.GL) tipo.

OpenGLES 3.0 è disponibile tramite il tipo ES30.GL, documentato [qui](xref:OpenTK.Graphics.ES30.GL) tipo.


### <a name="binding-design"></a>Progettazione di associazione

Xamarin. IOS non è semplicemente un'associazione per la piattaforma sottostante di Objective-C. Estende il sistema di tipi .NET e al sistema di invio di blend migliori in c# e Objective-C.

Le stesse modalità P/Invoke è uno strumento utile per richiamare le librerie native in Windows e Linux, o come IJW supporto può essere utilizzato per l'interoperabilità COM su Windows, xamarin. IOS consente di estendere il runtime per supportare oggetti c# dell'associazione agli oggetti di Objective-C.

La discussione entro i prossimi prossime sezioni non è necessario che gli utenti che creano applicazioni xamarin. IOS, ma aiuteranno gli sviluppatori a comprendere come operazioni vengono eseguite e consentiranno loro quando si creano applicazioni più complesse.



#### <a name="types"></a>Tipi

Dove è parso logico, i tipi di c# sono esposti anziché tipi Foundation a basso livello per il linguaggio c# Universe (universo).  Ciò significa che [l'API Usa il tipo "string" c# anziché NSString](~/ios/internals/api-design/nsstring.md) e Usa matrici c# fortemente tipizzate anziché esporre NSArray.

In generale, nel progetto xamarin. IOS e xamarin. Mac, sottostante `NSArray` oggetto non viene esposto. Al contrario, il runtime viene convertito automaticamente `NSArray`s per le matrici fortemente tipizzate di alcuni `NSObject` classe. Pertanto, xamarin. IOS non espone un metodo con tipizzazione debole, ad esempio GetViews per restituire un NSArray:

```csharp
NSArray GetViews ();
```

Al contrario, l'associazione espone un valore restituito fortemente tipizzato, simile al seguente:

```csharp
UIView [] GetViews ();
```

Ci sono alcuni dei metodi esposti nel `NSArray`, per i casi limite in cui si potrebbe voler usare una `NSArray` direttamente, ma è sconsigliato l'uso nell'associazione API.

Inoltre, nel **API classica** anziché esporre `CGRect`, `CGPoint` e `CGSize` dall'API CoreGraphics, è stata sostituita con la `System.Drawing` implementazioni `RectangleF`, `PointF`e `SizeF` perché potrebbe aiutare gli sviluppatori mantenere codice OpenGL esistente che usa OpenTK. Quando si utilizzano nuovo 64 bit **API unificata**, usare l'API CoreGraphics.

<a name="Inheritance" />

#### <a name="inheritance"></a>Ereditarietà

La progettazione di API xamarin. IOS consente agli sviluppatori di estendere i tipi di Objective-C nativi nello stesso modo che si estendono un tipo c#, usando la parola chiave "override" in una classe derivata, nonché di concatenamento di per l'implementazione di base usando la "base" parola chiave c#.

Questa progettazione consente agli sviluppatori di evitare la gestione con i selettori Objective-C come parte del processo di sviluppo, perché è già eseguito il wrapping dell'intero sistema di Objective-C all'interno di librerie di xamarin. IOS.


#### <a name="types-and-interface-builder"></a>Tipi e Interface Builder

Quando si creano classi .NET che sono istanze dei tipi creati da Interface Builder, è necessario fornire un costruttore che accetta un singolo `IntPtr` parametro.
Questa è necessaria per associare l'istanza di oggetto gestito con l'oggetto non gestito.
Il codice è costituito da una singola riga, simile al seguente:

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

<a name="Delegates" />


#### <a name="delegates"></a>Delegati

Objective-C e c# hanno un significato diverso per il delegato di word in ciascuna lingua.

Nel mondo Objective-C e nella documentazione che si trovano in linea su CocoaTouch, un delegato è in genere un'istanza di una classe che risponderà a un set di metodi. Ciò è molto simile a un'interfaccia in c#, con la differenza è che i metodi non sono sempre obbligatori.

Questi delegati svolgono un ruolo importante in UIKit e altre APIs CocoaTouch. Vengono usati per eseguire varie attività:

-  Per fornire notifiche al codice (analogamente al recapito di eventi in c# o Gtk +).
-  Per implementare i modelli per i controlli di visualizzazione dati.
-  Per determinare il comportamento di un controllo.


Il modello di programmazione è stato progettato per ridurre al minimo la creazione di classi derivate di modificare il comportamento per un controllo. Questa soluzione è simile come spirito al cosa hanno fatto altri Toolkit dell'interfaccia utente grafica nel corso degli anni: GTK dei segnali slot Qt, gli eventi di Windows Form, WPF o Silverlight eventi e così via. Per evitare la necessità di centinaia di interfacce, una per ogni azione, o richiedere agli sviluppatori di implementare un numero eccessivo di metodi che non devono, Objective-C supporta le definizioni di metodo facoltativo. Ciò è diverso da quello di interfacce di c# che richiedono tutti i metodi da implementare.

Nelle classi di Objective-C, si noterà che le classi che usano questo modello di programmazione espongono una proprietà denominata in genere `delegate`, che è necessario implementare le parti obbligatorie dell'interfaccia e zero o più delle facoltativo parti.

In xamarin. IOS sono disponibili tre meccanismi si escludono a vicenda per associare a questi delegati:

1.  [Tramite gli eventi](#Via_Events).
2.  [Fortemente tipizzati tramite un `Delegate` proprietà](#StrongDelegate)
3.  [Non tipizzate tramite un `WeakDelegate` proprietà](#WeakDelegate)

Si consideri, ad esempio, il [UIWebView](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html) classe. Ciò lo invia a un [UIWebViewDelegate](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html) istanza, che viene assegnato alle [delegare](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html#//apple_ref/occ/instp/UIWebView/delegate) proprietà.

<a name="Via_Events" />

##### <a name="via-events"></a>Tramite gli eventi

Per molti tipi di xamarin. IOS creerà automaticamente un delegato appropriato che inoltrerà i `UIWebViewDelegate` chiamate in c# gli eventi. Per `UIWebView`:

-  Il [webViewDidStartLoad](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidStartLoad:) metodo viene eseguito il mapping per il [UIWebView.LoadStarted](xref:UIKit.UIWebView.LoadStarted) evento.
-  Il [webViewDidFinishLoad](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidFinishLoad:) metodo viene eseguito il mapping per il [UIWebView.LoadFinished](xref:UIKit.UIWebView.LoadFinished) evento.
-  Il [webView:didFailLoadWithError](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webView:didFailLoadWithError:) metodo viene eseguito il mapping per il [UIWebView.LoadError](xref:UIKit.UIWebView.LoadError) evento.

Ad esempio, questo semplice programma registra ora di inizio e fine durante il caricamento di un sito web di visualizzazione:

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```


##### <a name="via-properties"></a>Tramite la proprietà

Gli eventi sono utili quando potrebbero essere presenti più di un sottoscrittore all'evento. Inoltre, gli eventi sono limitati ai casi in cui è presente alcun valore restituito dal codice.

Per i casi in cui il codice deve restituire un valore, abbiamo deciso invece per le proprietà. Ciò significa che un solo metodo può essere impostato in un momento specifico in un oggetto.

Ad esempio, è possibile utilizzare questo meccanismo per chiudere la tastiera su schermo per il gestore per un `UITextField`:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

Il `UITextField`del `ShouldReturn` proprietà in questo caso accetta come argomento un delegato che restituisce un valore bool e determina se il TextField non deve eseguire un'operazione con il pressione del pulsante restituito. Nel nostro metodo, vengono restituiti *true* al chiamante, ma è possibile rimuovere anche la tastiera dalla schermata (ciò si verifica quando viene chiamato il TextField non `ResignFirstResponder`).

<a name="StrongDelegate"/>

##### <a name="strongly-typed-via-a-delegate-property"></a>Fortemente tipizzato tramite una proprietà dei delegati

Se si preferisce non usare gli eventi, è possibile fornire il proprio [UIWebViewDelegate](xref:UIKit.UIWebViewDelegate) sottoclasse e assegnarlo alle [UIWebView.Delegate](xref:UIKit.UIWebView.Delegate) proprietà. Dopo aver assegnato le UIWebView.Delegate, il meccanismo di invio evento UIWebView smette di funzionare e i metodi UIWebViewDelegate verranno richiamati quando si verificano gli eventi corrispondenti.

Questo tipo semplice, ad esempio, registra il tempo che necessario per caricare una visualizzazione web:

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

Il codice precedente viene usato nel codice simile al seguente:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

Il codice precedente creerà un UIWebViewer e indicherà che possa inviare messaggi a un'istanza del componente di notifica, una classe che è stata creata per rispondere ai messaggi.

Questo modello viene usato anche per controllare il comportamento per determinati controlli, ad esempio nel caso UIWebView, il [UIWebView.ShouldStartLoad](xref:UIKit.UIWebView.ShouldStartLoad) proprietà consente la `UIWebView` istanza al controllo se il `UIWebView` caricherà un pagina oppure No.

Il modello viene usato anche per fornire i dati su richiesta per alcuni controlli. Ad esempio, il [UITableView](xref:UIKit.UITableView) è un controllo tabella-rendering potente – e sia l'aspetto e il relativo contenuto è determinato da un'istanza di un [UITableViewDataSource](xref:UIKit.UITableViewDataSource)

<a name="WeakDelegate"/>

### <a name="loosely-typed-via-the-weakdelegate-property"></a>Non tipizzate tramite la proprietà WeakDelegate

Oltre alle proprietà fortemente tipizzata, è inoltre disponibile un delegato tipizzato debole che consente allo sviluppatore di associare in modo diverso se lo si desidera.
Everywhere fortemente tipizzata `Delegate` proprietà viene esposta nell'associazione di xamarin. IOS, una corrispondente `WeakDelegate` proprietà viene anche esposto.

Quando si usa la `WeakDelegate`, si è responsabili correttamente decorando la classe usando il [esportare](xref:Foundation.ExportAttribute) attributo per specificare il selettore. Ad esempio:

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

Si noti che una volta il `WeakDelegate` proprietà è stata assegnata, di `Delegate` proprietà non verrà utilizzata. Inoltre, se si implementa il metodo in una classe di base ereditata da [esportazione], è necessario renderlo un metodo pubblico.


## <a name="mapping-of-the-objective-c-delegate-pattern-to-c35"></a>Mapping del modello di delegato Objective-C-C&#35;

Quando si vedere esempi di Objective-C con un aspetto simile al seguente:

```csharp
foo.delegate = [[SomethingDelegate] alloc] init]
```

Ciò indica la lingua per creare e costruire un'istanza della classe "SomethingDelegate" e assegnare il valore per la proprietà dei delegati nella variabile di foo. Questo meccanismo è supportato da xamarin. IOS e la sintassi di c# è:

```csharp
foo.Delegate = new SomethingDelegate ();
```

In xamarin. IOS è possibile forniti da classi di delegati di classi fortemente tipizzate che eseguono il mapping di Objective-c. A questo scopo, si verrà sottoclassi e l'override dei metodi definiti dall'implementazione di xamarin. IOS. Per altre informazioni sul loro funzionamento, vedere il sezione "modelli" indicati di seguito.


##### <a name="mapping-delegates-to-c35"></a>Mapping di delegati a C&#35;

UIKit Usa in genere i delegati di Objective-C in due forme.

Il primo form fornisce un'interfaccia al modello del componente. Ad esempio, come un meccanismo per fornire dati su richiesta per una visualizzazione, ad esempio la struttura di archiviazione dei dati per una visualizzazione elenco.  In questi casi, è sempre deve creare un'istanza della classe appropriata e assegnare la variabile.

Nell'esempio seguente viene fornito il `UIPickerView` con un'implementazione per un modello che utilizza le stringhe:

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

Il secondo formato è alla invierà una notifica degli eventi. In questi casi, anche se è necessario comunque esporre le API nel formato descritto in precedenza, è disponibile anche in c# gli eventi, che devono essere più semplice da utilizzare per operazioni rapide e integrati con i delegati anonimi ed espressioni lambda in c#.

Ad esempio, è possibile sottoscrivere `UIAccelerometer` eventi:

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

Le due opzioni sono disponibili in cui risultino significativi, ma il programmatore è necessario scegliere uno o l'altro. Se si crea la propria istanza di un risponditore/delegato fortemente tipizzato e assegnarla, gli eventi in c# non sarà funzionanti. Se si usano gli eventi in c#, i metodi nella classe del risponditore/delegate mai essere chiamati.

Nell'esempio precedente che utilizzato `UIWebView` possono essere scritte utilizzando le espressioni lambda in c# 3.0 come segue:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```


#### <a name="responding-to-events"></a>Risposta agli eventi

Nel codice Objective-C, in alcuni casi i gestori eventi per più controlli e i provider di informazioni per più controlli, verrà ospitata nella stessa classe. Ciò è possibile perché le classi a rispondere ai messaggi e come classi consentono di rispondere ai messaggi, è possibile collegare gli oggetti.

Come precedentemente descritto in dettaglio, xamarin. IOS supporta sia il linguaggio c# basato su eventi modello di programmazione e il modello di delegato Objective-C, in cui è possibile creare una nuova classe che implementa il delegato ed esegue l'override di metodi desiderati.

È anche possibile supportare pattern di Objective-C in cui i risponditori per più operazioni diverse sono tutti ospitati nella stessa istanza di una classe. A questo scopo tuttavia, è necessario usare le funzionalità di basso livello del binding xamarin. IOS.

Ad esempio, se si desidera la classe deve rispondere a entrambe le `UITextFieldDelegate.textFieldShouldClear`: messaggio e il `UIWebViewDelegate.webViewDidStartLoad`: nella stessa istanza di una classe, è necessario usare la dichiarazione di attributo [esportazione]:

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

Il C# i nomi per i metodi non sono importanti; Ciò che conta sono le stringhe passate per l'attributo [Export].

Quando si utilizza questo stile di programmazione, assicurarsi che i parametri c# corrispondono i tipi effettivi che deve trascorrere il motore di runtime.

<a name="Models" />

#### <a name="models"></a>Modelli

In UIKit le strutture di archiviazione o in risponditori che vengono implementati usando classi helper, questi vengono in genere definiti nel codice Objective-C come delegati e vengono implementate come protocolli.

Protocolli di Objective-C sono simili a interfacce, ma supportano metodi facoltativi, vale a dire, non tutti i metodi necessari essere implementata per il protocollo da usare.

Esistono due modalità di implementazione di un modello. È possibile implementarlo manualmente o usare le definizioni esistenti fortemente tipizzate.


Il meccanismo manuale è necessario quando si prova a implementare una classe che non è stata associata da xamarin. IOS. È molto semplice:

-  Contrassegnare la classe per la registrazione con il runtime
-  Applicare l'attributo [esportazione] con il nome effettivo del selettore su ogni metodo che si desidera eseguire l'override
-  Creare un'istanza della classe e passarlo.

Ad esempio, di seguito implementa solo uno dei metodi facoltativi nella definizione del protocollo UIApplicationDelegate:

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

Xamarin. IOS offre fortemente tipizzate dichiarazioni, pronto per l'uso, che non richiedono l'associazione manuale. Per supportare questo modello di programmazione, il runtime di xamarin. IOS supporta l'attributo [modello] in una dichiarazione di classe. Informa il runtime che deve associare tutti i metodi nella classe, non a meno che non sono i metodi è implementato in modo esplicito.

Ciò significa che in UIKit, le classi che rappresentano un protocollo con metodi facoltativi vengono scritti come segue:

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

Quando si desidera implementare un modello che implementa solo alcuni dei metodi, è necessario eseguire è sufficiente eseguire l'override di metodi che si è interessati e ignora gli altri metodi. Il runtime solo si collegherà i sovrascrivere i metodi, non l'originale al mondo Objective-C.

È l'equivalente a quello precedente manuale:

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

I vantaggi sono che non è necessario approfondire i file di intestazione Objective-C per trovare il selettore, i tipi di argomenti o il mapping a c# e usufruire di intellisense in Visual Studio per Mac, insieme ai tipi sicuri


#### <a name="xib-outlets-and-c35"></a>C e XIB Outlet&#35;

> [!IMPORTANT]
> Questa sezione descrive l'integrazione di IDE con prese quando si usano file XIB. Quando si usa la finestra di progettazione di Xamarin per iOS, questo è tutto sostituito immettendo un nome in **identità > nome** nella sezione delle proprietà dell'IDE dell'utente, come illustrato di seguito:
>
> [![](images/designeroutlet.png "Immettere un nome di elemento in iOS Designer")](images/designeroutlet.png#lightbox)
>
>Per altre informazioni su iOS Designer, vedere la [Introduzione a iOS Designer](~/ios/user-interface/designer/introduction.md#how-it-works) documento.

Questa è una descrizione di basso livello della modalità di integrazione Outlet con c# e viene fornita per gli utenti esperti di xamarin. IOS. Con Visual Studio per Mac mapping quando viene eseguita automaticamente in background usando codice durante il volo generato automaticamente.

Quando si progetta l'interfaccia utente con Interface Builder, è solo possibile progettare l'aspetto dell'applicazione e stabilirà alcune connessioni predefinite. Se si desidera recuperare le informazioni, modificare il comportamento di un controllo in fase di esecuzione o modificare il controllo in fase di esecuzione a livello di codice, è necessario associare alcuni controlli a codice gestito.

Questa operazione viene eseguita in pochi passaggi:

1.  Aggiungere il **dichiarazione outlet** per il **proprietario del File**.
1.  Connettere il controllo per il **il proprietario del File**.
1.  Store l'interfaccia utente e le connessioni nel file XIB/NIB.
1.  Caricare il file NIB in fase di esecuzione.
1.  Accedere alla variabile outlet.


I passaggi (1) e (3) sono trattati nella documentazione di Apple per la creazione di interfacce con Interface Builder.

Quando si usa xamarin. IOS, sarà necessario creare una classe che deriva da UIViewController all'applicazione. L'implementazione è simile al seguente:

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

Quindi per caricare il ViewController da un file NIB, eseguire questa operazione:

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

Si carica l'interfaccia utente dal NIB. A questo punto, per accedere il Outlet, è necessario informare il runtime che intendiamo per accedervi. A tale scopo, il `UIViewController` sottoclasse deve dichiarare le proprietà e aggiungervi annotazioni con l'attributo [connessione]. analogamente a quanto segue:

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

L'implementazione della proprietà è quello che in realtà recupera e memorizza il valore per il tipo nativo effettivo.

Non occorre preoccuparsi quando si usa Visual Studio per Mac e InterfaceBuilder. Visual Studio per Mac rispecchia automaticamente tutti gli Outlet dichiarati con il codice in una classe parziale che viene compilato come parte del progetto.

#### <a name="selectors"></a>Selettori

Un concetto fondamentale della programmazione in Objective-C è selettori. Verrà usato spesso per le API che richiedono il passaggio di un selettore o prevede che il codice per rispondere a un selettore.

Creazione dei selettori di nuovo in c# è molto semplice: sufficiente creare una nuova istanza di `ObjCRuntime.Selector` classe e usare il risultato in qualsiasi posizione nell'API che lo richiede. Ad esempio:

```csharp
var selector_add = new Selector ("add:plus:");
```

Per un linguaggio c# (metodo) rispondono a una chiamata di selettore, deve ereditare dal `NSObject` tipo e il metodo c# deve essere decorate con il nome di selettore usando il `[Export]` attributo. Ad esempio:

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

Si noti il selettore nomi **necessario** corrispondere esattamente, inclusi tutti i punti intermedi e finali (":"), se presente.

#### <a name="nsobject-constructors"></a>Costruttori di NSObject

La maggior parte delle classi in xamarin. IOS che derivano da `NSObject` esporrà i costruttori specifici per la funzionalità dell'oggetto, ma verranno esposte anche diversi costruttori che non sono immediatamente evidenti.

I costruttori vengono usati come indicato di seguito:

```csharp
public Foo (IntPtr handle)
```

Questo costruttore viene utilizzato per creare un'istanza della classe quando il runtime deve eseguire il mapping della classe a una classe non gestita. Ciò si verifica quando si carica un file XIB/NIB.  A questo punto, il runtime di Objective-C verrà creato un oggetto nel mondo non gestito e verrà chiamato questo costruttore per inizializzare il lato gestito.

In genere, è sufficiente eseguire è chiamare il costruttore di base con il parametro handle e nel corpo, effettuare qualsiasi inizializzazione che è necessario.

```csharp
public Foo ()
```

Questo è il costruttore predefinito per una classe e in xamarin. IOS fornite le classi, si inizializza la classe Foundation.NSObject e tutte le classi tra e alla fine, si collega questo di Objective-C `init` metodo sulla classe.

```csharp
public Foo (NSObjectFlag x)
```

Questo costruttore viene utilizzato per inizializzare l'istanza, ma impedisce che il codice del metodo Objective-C "init" alla fine. Si userà in genere al momento è già stato registrato per l'inizializzazione (quando si usa `[Export]` nel costruttore) o quando è già stata eseguita l'inizializzazione mediante un altro valore medio.

```csharp
public Foo (NSCoder coder)
```

Questo costruttore viene fornito per i casi in cui l'oggetto viene inizializzato da un'istanza di NSCoding. Per altre informazioni, vedere Apple [archivi e Guida alla programmazione di serializzazione.](https://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/Archiving/index.html#//apple_ref/doc/uid/10000047i)

#### <a name="exceptions"></a>Eccezioni

La progettazione di API xamarin. IOS non genera eccezioni Objective-C come eccezioni di c#. La progettazione impone che nessun garbage venga inviato a tutti gli utenti di Objective-C in primo luogo e che tutte le eccezioni che devono essere prodotti vengono prodotte dall'associazione stessa prima che siano sempre di dati non valido passato a tutti gli utenti di Objective-C.

#### <a name="notifications"></a>Notifiche

In iOS e OS X, gli sviluppatori possono sottoscrivere le notifiche che vengono trasmessi dalla piattaforma sottostante. Questa operazione viene eseguita tramite il `NSNotificationCenter.DefaultCenter.AddObserver` (metodo). Il `AddObserver` metodo accetta due parametri, uno è la notifica che si desidera eseguire la sottoscrizione; l'altra è il metodo da richiamare quando viene generata la notifica.

In xamarin. IOS e xamarin. Mac, le chiavi per le diverse notifiche sono ospitate nella classe che attiva le notifiche. Ad esempio, le notifiche generati dal `UIMenuController` vengono ospitati come `static NSString` le proprietà nel `UIMenuController` classi che terminano con il nome "Notifica".

### <a name="memory-management"></a>Gestione della memoria

Xamarin. IOS include un garbage collector che si occuperà di rilascio delle risorse per l'utente quando non sono più in uso. Oltre a garbage collector, tutti gli oggetti che derivano da `NSObject` implementano il `System.IDisposable` interfaccia.

#### <a name="nsobject-and-idisposable"></a>NSObject e IDisposable

Esponendo le `IDisposable` interfaccia è un modo pratico di assistere gli sviluppatori nel rilascio di oggetti che potrebbero incapsulare grandi blocchi di memoria (ad esempio, un `UIImage` potrebbe essere simile a un puntatore di inoffensivo, ma può fare riferimento a un'immagine di 2 megabyte ) e altre risorse importanti e finiti (ad esempio, un buffer di decodifica video).

NSObject implementa l'interfaccia IDisposable e anche il [modello Dispose .NET](https://msdn.microsoft.com/library/fs2xkftw.aspx). In questo modo gli sviluppatori che sottoclasse NSObject per eseguire l'override del comportamento di Dispose e rilasciare le proprie risorse su richiesta. Si consideri, ad esempio, questo controller di visualizzazione che dispone di una serie di immagini:

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

Quando viene eliminato un oggetto gestito, non è più utile. Si potrebbe avere ancora un riferimento agli oggetti, ma l'oggetto è valido a tutti gli effetti a questo punto. Alcune API .NET garantire tale requisito generando un'eccezione ObjectDisposedException se si prova ad accedere a qualsiasi metodo su un oggetto eliminato, ad esempio:

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

Anche se è ancora possibile accedere alla variabile "image", è davvero un riferimento non valido e non sono più punti all'oggetto Objective-C che conteneva l'immagine.

Ma eliminare un oggetto nel linguaggio c# non significa che l'oggetto necessariamente viene distrutto. È sufficiente è rilasciare il riferimento che c# era necessario l'oggetto. È possibile che l'ambiente di Cocoa potrebbe avere mantenuto un riferimento intorno a proprio uso. Ad esempio, se si imposta proprietà di un UIImageView immagine su un'immagine e quindi si elimina l'immagine, il sottostante UIImageView aveva avuto il proprio riferimento e manterranno un riferimento a questo oggetto fino al termine utilizzarlo.

#### <a name="when-to-call-dispose"></a>Quando chiamare Dispose

È consigliabile chiamare Dispose quando occorre Mono eliminazione dell'oggetto. Un caso d'uso possibile è quando Mono non è a conoscenza di NSObject è effettivamente che contiene un riferimento a una risorsa importante, ad esempio memoria o un pool di informazioni. In questi casi, è consigliabile chiamare Dispose per rilasciare immediatamente il riferimento alla memoria, anziché attendere che Mono eseguire un ciclo di garbage collection.

Internamente, quando crea Mono [NSString fa riferimento da stringhe c#](~/ios/internals/api-design/nsstring.md), eliminerà immediatamente per ridurre il carico di lavoro che il garbage collector ha a che fare. Verranno eseguito i meno oggetti soluzioni alternative per gestire, più rapidamente il Garbage Collector.

#### <a name="when-to-keep-references-to-objects"></a>Casi in cui mantenere i riferimenti agli oggetti

Un effetto collaterale con gestione automatica della memoria è che il Garbage Collector verrà eliminare gli oggetti inutilizzati, purché non siano presenti riferimenti a essi. Questo talvolta può avere effetti collaterali imprevisti, ad esempio, se si crea una variabile locale per contenere il controller di visualizzazione di livello superiore o la finestra di primo livello e fare in modo che quelli scompaiono dietro a tua disposizione.

Se non si mantiene un riferimento di statico o variabili di istanza agli oggetti, Mono Fortunatamente chiamerà il metodo Dispose () su di essi e rilascerà il riferimento all'oggetto. Poiché questo potrebbe essere il riferimento solo in sospeso, il runtime di Objective-C determinerà l'eliminazione dell'oggetto per l'utente.

## <a name="related-links"></a>Collegamenti correlati

- [Campi di associazione](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
