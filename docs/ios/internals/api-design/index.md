---
title: Progettazione dell'API Novell. iOS
description: Principi di guida usati per progettare le API Novell. iOS e le relative correlazioni con Objective-C.
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 843aeda14ad8c47014b577bdce8004872b12865d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70753459"
---
# <a name="xamarinios-api-design"></a>Progettazione dell'API Novell. iOS

Oltre alle librerie di classi base core che fanno parte di mono, [Novell. iOS](http://www.xamarin.com/iOS) viene fornito con binding per varie API iOS per consentire agli sviluppatori di creare applicazioni iOS native con mono.

Alla base di Novell. iOS, è disponibile un motore di interoperabilità che C# colma il mondo con Objective-C World, nonché associazioni per le API basate su iOS C come CoreGraphics e [OpenGL ES](#opengles).

Il runtime di basso livello per la comunicazione con il codice Objective-C è in [MonoTouch. ObjCRuntime](#objcruntime). Al di sopra di questo, vengono forniti i binding per [Foundation](#foundation), CoreFoundation e [UIKit](#uikit) .

## <a name="design-principles"></a>Principi di progettazione

Questi sono alcuni dei principi di progettazione per i binding Novell. iOS (si applicano anche a Novell. Mac, i binding mono per Objective-C in macOS):

- Segui le [linee guida di progettazione del Framework](https://docs.microsoft.com/dotnet/standard/design-guidelines)
- Consenti agli sviluppatori di sottoclasse Objective-C classi:

  - Derivare da una classe esistente
  - Chiamare il costruttore di base per concatenare
  - L'override dei metodi deve essere eseguito C#con il sistema di override di
  - La creazione di sottoclassi dovrebbe C# funzionare con costrutti standard

- Non esporre gli sviluppatori ai selettori Objective-C
- Fornire un meccanismo per chiamare le librerie Objective-C arbitrarie
- Rendere le attività comuni di Objective-C semplici e difficili da Objective-C possibili
- Esporre le proprietà Objective-C C# come proprietà
- Esporre un'API fortemente tipizzata:

  - Aumentare l'indipendenza dai tipi
  - Ridurre al minimo gli errori di runtime
  - Ottenere IntelliSense IDE sui tipi restituiti
  - Consente la documentazione popup dell'IDE

- Incoraggiare l'esplorazione nell'IDE delle API:

  - Ad esempio, anziché esporre una matrice debolmente tipizzata come la seguente:

    ```objc
    NSArray *getViews
    ```

    Esporre un tipo sicuro, come indicato di seguito:

    ```csharp
    NSView [] Views { get; set; }
    ```

    In questo modo Visual Studio per Mac la possibilità di eseguire il completamento automatico durante l'esplorazione dell'API, rende disponibili `System.Array` tutte le operazioni sul valore restituito e consente al valore restituito di partecipare a LINQ.

- Tipi C# nativi:

  - [`NSString`diventa`string`](~/ios/internals/api-design/nsstring.md)
  - Attivare `int` e C# C# `[Flags]` disattivare i parametri che devono essere enumerati in enumerazioni ed enumerazioni con attributi `uint`
  - Anziché oggetti indipendenti `NSArray` dal tipo, esporre le matrici come matrici fortemente tipizzate.
  - Per gli eventi e le notifiche, consentire agli utenti di scegliere tra:

    - Una versione fortemente tipizzata per impostazione predefinita
    - Una versione con tipizzazione debole per i casi d'uso avanzati

- Supportare il modello di delegato Objective-C:

  - C#sistema di eventi
  - Esporre C# delegati (espressioni lambda, metodi anonimi e `System.Delegate`) alle API Objective-C come blocchi

### <a name="assemblies"></a>Assembly

Novell. iOS include diversi assembly che costituiscono il *profilo Novell. iOS*. Ulteriori informazioni sono contenute nella pagina [assembly](~/cross-platform/internals/available-assemblies.md) .

### <a name="major-namespaces"></a>Spazi dei nomi principali

#### <a name="objcruntime"></a>ObjCRuntime

Lo spazio dei nomi [ObjCRuntime](xref:ObjCRuntime) consente agli sviluppatori di colmare i mondi tra C# e Objective-C.
Si tratta di un nuovo binding, progettato specificamente per iOS, in base all'esperienza di Cocoa # e GTK #.

#### <a name="foundation"></a>Foundation

Lo spazio dei nomi [Foundation](xref:Foundation) fornisce i tipi di dati di base progettati per interagire con il Framework Objective-c Foundation che fa parte di iOS ed è la base per la programmazione orientata a oggetti in Objective-c.

Novell. iOS rispecchia C# la gerarchia delle classi di Objective-C. Ad esempio, la classe base Objective-C [NSObject](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/Reference/Reference.html) è utilizzabile C# da tramite [Foundation. NSObject](xref:Foundation.NSObject).

Sebbene questo spazio dei nomi fornisca associazioni per i tipi di base Objective-C sottostanti, in alcuni casi è stato eseguito il mapping dei tipi sottostanti ai tipi .NET. Ad esempio:

- Anziché gestire [NSString](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html) e [NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html), il runtime li espone come C# [stringa](xref:System.String)e [matrici](xref:System.Array)fortemente tipizzate nell'API.

- Qui sono esposte diverse API helper per consentire agli sviluppatori di associare API Objective-C di terze parti, altre API iOS o API che non sono attualmente associate a Novell. iOS.

Per altre informazioni sulle API di binding, vedere la sezione [Generatore di binding Novell. iOS](~/cross-platform/macios/binding/binding-types-reference.md) .

##### <a name="nsobject"></a>NSObject

Il tipo [NSObject](xref:Foundation.NSObject) è la base per tutte le associazioni Objective-C. I tipi Novell. iOS rispecchiano due classi di tipi dalle API CocoaTouch di iOS: i tipi C (in genere detti tipi CoreFoundation) e i tipi Objective-C (tutti derivano dalla classe NSObject).

Per ogni tipo che rispecchia un tipo non gestito, è possibile ottenere l'oggetto nativo tramite la proprietà [handle](xref:Foundation.NSObject.Handle) .

Mentre mono fornirà Garbage Collection per tutti gli oggetti, `Foundation.NSObject` implementa l'interfaccia [System. IDisposable](xref:System.IDisposable) . Ciò significa che è possibile rilasciare in modo esplicito le risorse di un determinato NSObject senza dover attendere l'avvio del Garbage Collector. Questo è importante quando si usano NSObjects pesanti, ad esempio UIImages, che potrebbero avere puntatori a blocchi di dati di grandi dimensioni.

Se il tipo deve eseguire una finalizzazione deterministica, eseguire l'override del [Metodo NSObject. Dispose (bool)](xref:Foundation.NSObject.Dispose(System.Boolean)) il parametro da eliminare è "bool disposing" e, se impostato su true, significa che il metodo Dispose viene chiamato perché l'utente ha chiamato in modo esplicito Dispose () sull'oggetto. Se il valore è false, significa che il metodo Dispose (bool disposing) viene chiamato dal finalizzatore nel thread del finalizzatore.

##### <a name="categories"></a>Categories

A partire da Novell. iOS 8,10 è possibile creare categorie Objective-C da C#.

Questa operazione viene eseguita utilizzando `Category` l'attributo, specificando il tipo da estendere come argomento all'attributo. Nell'esempio seguente, ad esempio, si estende NSString.

```csharp
[Category (typeof (NSString))]
```

Ogni metodo di categoria usa il meccanismo normale per esportare i metodi in Objective-C usando `Export` l'attributo:

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

Tutti i metodi di estensione gestiti devono essere statici, ma è possibile creare metodi di istanza Objective-C usando la sintassi standard per i metodi C#di estensione in:

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

il primo argomento del metodo di estensione sarà quindi l'istanza su cui è stato richiamato il metodo.

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

In questo esempio viene aggiunto un metodo di istanza ToUpper nativa alla classe NSString, che può essere richiamata da Objective-C.

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

Uno scenario in cui questa operazione è utile è l'aggiunta di un metodo a un intero set di classi nella codebase, ad esempio, per `UIViewController` fare in modo che tutte le istanze segnalino che possono ruotare:

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

PreserveAttribute è un attributo personalizzato che viene usato per indicare a mTouch, lo strumento di distribuzione Novell. iOS, di mantenere un tipo o un membro di un tipo durante la fase in cui l'applicazione viene elaborata per ridurne le dimensioni.

Ogni membro che non è collegato in modo statico dall'applicazione è soggetto alla rimozione. Questo attributo viene quindi utilizzato per contrassegnare i membri a cui non viene fatto riferimento in modo statico, ma che sono comunque necessari per l'applicazione.

Ad esempio, se si creano istanze dei tipi in modo dinamico, potrebbe essere necessario mantenere il costruttore predefinito dei tipi. Se si usa la serializzazione XML, potrebbe essere necessario mantenere le proprietà dei tipi.

È possibile applicare questo attributo in ogni membro di un tipo oppure nel tipo stesso. Se si desidera mantenere l'intero tipo, è possibile utilizzare la sintassi [Preserve (AllMembers = true)] sul tipo.

#### <a name="uikit"></a>UIKit

Lo spazio dei nomi [UIKit](xref:UIKit) contiene un mapping uno-a-uno a tutti i componenti dell'interfaccia utente che costituiscono CocoaTouch sotto forma C# di classi. L'API è stata modificata per seguire le convenzioni usate nel C# linguaggio.

C#i delegati vengono forniti per le operazioni comuni. Per ulteriori informazioni, vedere la sezione [delegati](#delegates) .

#### <a name="opengles"></a>OpenGLES

Per le OpenGL, viene distribuita una [versione modificata](xref:OpenTK) dell'API [OpenTK](http://www.opentk.com/) , un'associazione orientata a oggetti a OpenGL che è stata modificata per l'uso di strutture e tipi di dati CoreGraphics, oltre a esporre solo le funzionalità disponibili in iOS.

La funzionalità OpenGLes 1,1 è disponibile tramite il [tipo ES11.GL](xref:OpenTK.Graphics.ES11.GL).

La funzionalità OpenGLes 2,0 è disponibile tramite il [tipo ES20.GL](xref:OpenTK.Graphics.ES20.GL).

La funzionalità OpenGLes 3,0 è disponibile tramite il [tipo ES30.GL](xref:OpenTK.Graphics.ES30.GL).

### <a name="binding-design"></a>Progettazione dell'associazione

Novell. iOS non è semplicemente un binding alla piattaforma Objective-C sottostante. Estende il sistema di tipi .NET e il sistema di invio a C# Blend e Objective-C migliori.

Proprio come P/Invoke è uno strumento utile per richiamare le librerie native in Windows e Linux oppure, come è possibile usare il supporto IJW per l'interoperabilità COM in Windows, Novell. iOS estende C# il runtime per supportare l'associazione di oggetti a oggetti Objective-C.

La discussione nelle prossime sezioni non è necessaria per gli utenti che creano applicazioni Novell. iOS, ma aiuta gli sviluppatori a comprendere il modo in cui vengono eseguite le operazioni e fornirà assistenza per la creazione di applicazioni più complesse.

#### <a name="types"></a>Tipi

Laddove è opportuno, C# i C# tipi vengono esposti all'universo anziché ai tipi di base di basso livello.  Ciò significa che [l'API usa il C# tipo "String" invece di NSString](~/ios/internals/api-design/nsstring.md) e USA matrici fortemente tipizzate C# anziché esporre NSArray.

In generale, nella progettazione di Novell. iOS e Novell. Mac l'oggetto sottostante `NSArray` non viene esposto. Al contrario, il runtime converte `NSArray`automaticamente i in matrici fortemente tipizzate di `NSObject` una classe. Quindi, Novell. iOS non espone un metodo debolmente tipizzato come GetViews per restituire un NSArray:

```csharp
NSArray GetViews ();
```

Al contrario, l'associazione espone un valore restituito fortemente tipizzato, come indicato di seguito:

```csharp
UIView [] GetViews ();
```

Sono disponibili alcuni metodi esposti in, per `NSArray`i casi d'angolo in cui è possibile `NSArray` usare direttamente, ma il loro uso è sconsigliato nell'associazione API.

Inoltre, nel **API classica** `CGRect`anziché esporre `CGPoint` e `CGSize` dall' `System.Drawing` API CoreGraphics, sono stati sostituiti con le implementazioni e `RectangleF` `PointF` `SizeF`poiché aiuteranno gli sviluppatori a conservare il codice OpenGL esistente che usa OpenTK. Quando si usa la nuova **API unificata**a 64 bit, è necessario usare l'API coregraphics.

#### <a name="inheritance"></a>Ereditarietà

La progettazione dell'API Novell. iOS consente agli sviluppatori di estendere i tipi di Objective-C nativi nello stesso modo in cui C# estendono un tipo, usando la parola chiave "override" in una classe derivata, nonché concatenando fino all'implementazione di base usando la C# "base" parola chiave.

Questa progettazione consente agli sviluppatori di evitare la gestione dei selettori Objective-C come parte del processo di sviluppo, poiché l'intero sistema Objective-C è già incluso nelle librerie Novell. iOS.

#### <a name="types-and-interface-builder"></a>Tipi e Interface Builder

Quando si creano classi .NET che sono istanze di tipi creati da Interface Builder, è necessario fornire un costruttore che accetti un solo `IntPtr` parametro.
Questa operazione è necessaria per associare l'istanza dell'oggetto gestito all'oggetto non gestito.
Il codice è costituito da una sola riga, come la seguente:

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

#### <a name="delegates"></a>Delegati

Objective-C e C# hanno significati diversi per il delegato di Word in ogni lingua.

Nel mondo Objective-C e nella documentazione disponibile online su CocoaTouch, un delegato è in genere un'istanza di una classe che risponderà a un set di metodi. Questa operazione è molto simile a C# un'interfaccia, con la differenza che i metodi non sono sempre obbligatori.

Questi delegati svolgono un ruolo importante in UIKit e in altre API CocoaTouch. Vengono usati per eseguire varie attività:

- Per fornire notifiche al codice (simile al recapito di eventi C# in o GTK +).
- Per implementare i modelli per i controlli di visualizzazione dei dati.
- Per guidare il comportamento di un controllo.

Il modello di programmazione è stato progettato per ridurre al minimo la creazione di classi derivate per modificare il comportamento di un controllo. Questa soluzione è analoga a quella degli altri toolkit GUI eseguiti negli anni: Segnali di GTK, slot QT, eventi WinForms, eventi WPF/Silverlight e così via. Per evitare di avere centinaia di interfacce (una per ogni azione) o richiedere agli sviluppatori di implementare un numero eccessivo di metodi non necessari, Objective-C supporta le definizioni di metodo facoltative. Questa operazione è diversa C# rispetto alle interfacce che richiedono l'implementazione di tutti i metodi.

Nelle classi Objective-C si noterà che le classi che usano questo modello di programmazione espongono una proprietà, in `delegate`genere chiamata, che è necessaria per implementare le parti obbligatorie dell'interfaccia e zero, o più, delle parti facoltative.

In Novell. iOS sono disponibili tre meccanismi che si escludono a vicenda per l'associazione a questi delegati:

1. [Tramite eventi](#via-events).
2. [Fortemente tipizzato tramite `Delegate` una proprietà](#strongly-typed-via-a-delegate-property)
3. [Debolmente tipizzato tramite una `WeakDelegate` proprietà](#loosely-typed-via-the-weakdelegate-property)

Si consideri, ad esempio, la classe [UIWebView](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html) . Questo viene inviato a un'istanza di [UIWebViewDelegate](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html) , assegnata alla proprietà del [delegato](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html#//apple_ref/occ/instp/UIWebView/delegate) .

##### <a name="via-events"></a>Eventi via

Per molti tipi, Novell. iOS creerà automaticamente un delegato appropriato che inoltrerà le `UIWebViewDelegate` chiamate agli C# eventi. Per `UIWebView`:

- Viene eseguito il mapping del metodo [webViewDidStartLoad](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidStartLoad:) all'evento [UIWebView. LoadStarted](xref:UIKit.UIWebView.LoadStarted) .
- Viene eseguito il mapping del metodo [webViewDidFinishLoad](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidFinishLoad:) all'evento [UIWebView. LoadFinished](xref:UIKit.UIWebView.LoadFinished) .
- Il metodo [WebView: didFailLoadWithError](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webView:didFailLoadWithError:) è mappato all'evento [UIWebView. LoadError](xref:UIKit.UIWebView.LoadError) .

Questo semplice programma, ad esempio, registra l'ora di inizio e di fine durante il caricamento di una visualizzazione Web:

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```

##### <a name="via-properties"></a>Proprietà via

Gli eventi sono utili quando potrebbero esserci più sottoscrittori dell'evento. Inoltre, gli eventi sono limitati ai casi in cui non è presente alcun valore restituito dal codice.

Per i casi in cui si prevede che il codice restituisca un valore, si è optato per le proprietà. Ciò significa che è possibile impostare un solo metodo in un determinato momento in un oggetto.

Ad esempio, è possibile usare questo meccanismo per chiudere la tastiera sullo schermo sul gestore per un `UITextField`:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

La `UITextField`proprietà `ShouldReturn` di in questo caso accetta come argomento un delegato che restituisce un valore bool e determina se l'oggetto TextField deve eseguire un'operazione con il pulsante Return premuto. Nel metodo viene restituito *true* al chiamante, ma viene anche rimossa la tastiera dalla schermata (ciò si verifica quando il TextField chiama `ResignFirstResponder`).

##### <a name="strongly-typed-via-a-delegate-property"></a>Fortemente tipizzato tramite una proprietà di delegato

Se si preferisce non usare gli eventi, è possibile fornire la propria sottoclasse [UIWebViewDelegate](xref:UIKit.UIWebViewDelegate) e assegnarla alla proprietà [UIWebView. Delegate](xref:UIKit.UIWebView.Delegate) . Una volta assegnato UIWebView. Delegate, il meccanismo di invio dell'evento UIWebView non funzionerà più e i metodi UIWebViewDelegate verranno richiamati quando si verificano gli eventi corrispondenti.

Questo tipo semplice registra, ad esempio, il tempo necessario per caricare una visualizzazione Web:

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

Il codice precedente creerà un UIWebViewer e indicherà all'utente di inviare messaggi a un'istanza di Notifier, una classe creata per rispondere ai messaggi.

Questo modello viene inoltre usato per controllare il comportamento di alcuni controlli, ad esempio nel caso di UIWebView, la proprietà [UIWebView. ShouldStartLoad](xref:UIKit.UIWebView.ShouldStartLoad) consente `UIWebView` all'istanza di controllare se `UIWebView` l'oggetto caricherà o meno una pagina.

Il modello viene usato anche per fornire i dati su richiesta per alcuni controlli. Ad esempio, il controllo [UITableView](xref:UIKit.UITableView) è un potente controllo rendering tabella, che sia l'aspetto che il contenuto sono basati su un'istanza di un [UITableViewDataSource](xref:UIKit.UITableViewDataSource)

### <a name="loosely-typed-via-the-weakdelegate-property"></a>Debolmente tipizzato tramite la proprietà WeakDelegate

Oltre alla proprietà fortemente tipizzata, esiste anche un delegato debole tipizzato che consente allo sviluppatore di associare elementi in modo diverso, se necessario.
Ovunque una proprietà fortemente `Delegate` tipizzata viene esposta nell'associazione di Novell. iOS, viene `WeakDelegate` esposta anche una proprietà corrispondente.

Quando si usa `WeakDelegate`, si è responsabili della corretta decorazione della classe usando l'attributo [Export](xref:Foundation.ExportAttribute) per specificare il selettore. Ad esempio:

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

Si noti che una `WeakDelegate` volta che la proprietà è stata `Delegate` assegnata, la proprietà non verrà utilizzata. Inoltre, se si implementa il metodo in una classe di base ereditata che si desidera esportare, è necessario renderlo un metodo pubblico.

## <a name="mapping-of-the-objective-c-delegate-pattern-to-c"></a>Mapping del modello di delegato Objective-C a C\#

Quando vengono visualizzati esempi di Objective-C simili ai seguenti:

```objc
foo.delegate = [[SomethingDelegate] alloc] init]
```

Indica al linguaggio di creare e costruire un'istanza della classe "SomethingDelegate" e di assegnare il valore alla proprietà delegate sulla variabile foo. Questo meccanismo è supportato da Novell. iOS e C# la sintassi è:

```csharp
foo.Delegate = new SomethingDelegate ();
```

In Novell. iOS sono state fornite classi fortemente tipizzate con mapping alle classi delegate Objective-C. Per usarli, è necessario sottoclassare ed eseguire l'override dei metodi definiti dall'implementazione di Novell. iOS. Per ulteriori informazioni sul funzionamento, vedere la sezione "modelli" di seguito.

### <a name="mapping-delegates-to-c"></a>Mapping di delegati a C\#

UIKit in generale USA delegati Objective-C in due formati.

Il primo form fornisce un'interfaccia per il modello di un componente. Ad esempio, come meccanismo per fornire dati su richiesta per una visualizzazione, ad esempio la funzionalità di archiviazione dei dati per una visualizzazione elenco.  In questi casi, è sempre necessario creare un'istanza della classe appropriata e assegnare la variabile.

Nell'esempio seguente viene fornito l'oggetto `UIPickerView` con un'implementazione per un modello che utilizza stringhe:

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

Il secondo formato consiste nel fornire la notifica per gli eventi. In questi casi, anche se l'API è ancora esposta nel formato descritto in precedenza, vengono forniti C# anche gli eventi, che dovrebbero essere più semplici da usare per operazioni rapide e integrati con delegati anonimi C#ed espressioni lambda in.

Ad esempio, è possibile sottoscrivere `UIAccelerometer` gli eventi:

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

Le due opzioni sono disponibili laddove sono sensate, ma come programmatore è necessario selezionare una o l'altra. Se si crea un'istanza personalizzata di un risponditore/delegato fortemente tipizzato e lo si assegna C# , gli eventi non saranno funzionanti. Se si usano gli C# eventi, i metodi nella classe risponditore/delegata non verranno mai chiamati.

L'esempio precedente usato `UIWebView` può essere scritto usando C# le espressioni lambda 3,0 come segue:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```

#### <a name="responding-to-events"></a>Risposta agli eventi

Nel codice Objective-C, a volte i gestori eventi per più controlli e provider di informazioni per più controlli, verranno ospitati nella stessa classe. Questo è possibile perché le classi rispondono ai messaggi e, a condizione che le classi rispondano ai messaggi, è possibile collegare gli oggetti.

Come descritto in precedenza, Novell. iOS supporta sia C# il modello di programmazione basato su eventi che il modello di delegato Objective-C, in cui è possibile creare una nuova classe che implementa il delegato ed esegue l'override dei metodi desiderati.

È anche possibile supportare il modello di Objective-C, in cui i risponditori per più operazioni diverse sono tutti ospitati nella stessa istanza di una classe. A tale scopo, è necessario usare le funzionalità di basso livello del binding Novell. iOS.

Se ad esempio si desidera che la classe risponda sia `UITextFieldDelegate.textFieldShouldClear`al messaggio: che alla `UIWebViewDelegate.webViewDidStartLoad`: nella stessa istanza di una classe, è necessario utilizzare la dichiarazione dell'attributo [Export]:

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

I C# nomi dei metodi non sono importanti. tutte le questioni sono le stringhe passate all'attributo [Export].

Quando si usa questo stile di programmazione, assicurarsi che C# i parametri corrispondano ai tipi effettivi che il motore di runtime passerà.

#### <a name="models"></a>Modelli

Nelle funzionalità di archiviazione UIKit o nei risponditori implementati mediante le classi helper, questi vengono in genere indicati nel codice Objective-C come delegati e vengono implementati come protocolli.

I protocolli Objective-C sono simili alle interfacce, ma supportano metodi facoltativi, ovvero non tutti i metodi devono essere implementati per il funzionamento del protocollo.

Esistono due modi per implementare un modello. È possibile implementarlo manualmente o utilizzare le definizioni fortemente tipizzate esistenti.

Il meccanismo manuale è necessario quando si tenta di implementare una classe che non è stata associata da Novell. iOS. È molto semplice:

- Contrassegnare la classe per la registrazione con il runtime
- Applicare l'attributo [Export] con il nome del selettore effettivo in ogni metodo di cui si vuole eseguire l'override
- Creare un'istanza della classe e passarla.

Il codice seguente, ad esempio, implementa solo uno dei metodi facoltativi nella definizione del protocollo UIApplicationDelegate:

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

Il nome del selettore Objective-C ("applicationDidFinishLaunching:") viene dichiarato con l'attributo Export e la classe è `[Register]` registrata con l'attributo.

Novell. iOS fornisce dichiarazioni fortemente tipizzate, pronte per l'uso, che non richiedono l'associazione manuale. Per supportare questo modello di programmazione, il runtime di Novell. iOS supporta l'attributo [Model] in una dichiarazione di classe. In questo modo si informa il runtime che non dovrebbe collegare tutti i metodi della classe, a meno che i metodi non siano implementati in modo esplicito.

Ciò significa che, in UIKit, le classi che rappresentano un protocollo con metodi facoltativi vengono scritte come segue:

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

Quando si desidera implementare un modello che implementa solo alcuni dei metodi, è sufficiente eseguire l'override dei metodi a cui si è interessati e ignorare gli altri metodi. Il runtime collegherà solo i metodi sovrascritti, non i metodi originali per il mondo Objective-C.

L'equivalente dell'esempio manuale precedente è il seguente:

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

I vantaggi sono che non è necessario approfondire i file di intestazione Objective-C per trovare il selettore, i tipi degli argomenti o il mapping a C#e ottenere intellisense da Visual Studio per Mac, insieme ai tipi sicuri

#### <a name="xib-outlets-and-c"></a>XIB Outlet e C\#

> [!IMPORTANT]
> Questa sezione illustra l'integrazione dell'IDE con Outlet quando si usano file XIB. Quando si usa il Xamarin Designer per iOS, questo viene sostituito dall'immissione di un nome in **Identity > Name** nella sezione Properties dell'IDE, come illustrato di seguito:
>
> [![](images/designeroutlet.png "Immissione di un nome di elemento in iOS designer")](images/designeroutlet.png#lightbox)
>
>Per altre informazioni su iOS designer, vedere l' [Introduzione al documento di iOS designer](~/ios/user-interface/designer/introduction.md#how-it-works) .

Si tratta di una descrizione di basso livello del modo in cui C# gli outlet si integrano con ed è disponibile per gli utenti avanzati di Novell. iOS. Quando si usa Visual Studio per Mac il mapping viene eseguito automaticamente dietro le quinte, usando il codice generato per il volo.

Quando si progetta l'interfaccia utente con Interface Builder, sarà necessario solo progettare l'aspetto dell'applicazione e stabilire alcune connessioni predefinite. Se si desidera recuperare le informazioni a livello di codice, modificare il comportamento di un controllo in fase di esecuzione o modificare il controllo in fase di esecuzione, è necessario associare alcuni controlli al codice gestito.

Questa operazione viene eseguita in pochi passaggi:

1. Aggiungere la **dichiarazione di uscita** al **proprietario del file**.
1. Connettere il controllo al **proprietario del file**.
1. Archiviare l'interfaccia utente più le connessioni nel file XIB/pennini.
1. Caricare il file del PENNino in fase di esecuzione.
1. Accedere alla variabile di uscita.

I passaggi (1) fino a (3) sono trattati nella documentazione di Apple per la creazione di interfacce con Interface Builder.

Quando si usa Novell. iOS, l'applicazione deve creare una classe che deriva da UIViewController. Viene implementato come segue:

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

Quindi, per caricare il ViewController da un file del PENNino, procedere come segue:

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

Verrà caricata l'interfaccia utente dal PENNino. Ora, per accedere agli Outlet, è necessario informare il runtime a cui si vuole accedere. A tale scopo, la `UIViewController` sottoclasse deve dichiarare le proprietà e annotarle con l'attributo [Connect]. analogamente a quanto segue:

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

L'implementazione della proprietà è quella che esegue effettivamente il recupero e archivia il valore per il tipo nativo effettivo.

Quando si usano Visual Studio per Mac e InterfaceBuilder, non è necessario preoccuparsi di questa operazione. Visual Studio per Mac rispecchia automaticamente tutti gli outlet dichiarati con il codice in una classe parziale compilata come parte del progetto.

#### <a name="selectors"></a>Selettori

Un concetto di base della programmazione Objective-C è costituito dai selettori. Sono spesso disponibili API che richiedono il passaggio di un selettore o prevede che il codice risponda a un selettore.

La creazione di nuovi selettori in C# è molto semplice: è sufficiente creare una nuova `ObjCRuntime.Selector` istanza della classe e usare il risultato in qualsiasi posizione nell'API che la richiede. Ad esempio:

```csharp
var selector_add = new Selector ("add:plus:");
```

Per un C# metodo che risponde a una chiamata del selettore, deve `NSObject` ereditare dal C# tipo e il metodo deve essere decorato con il `[Export]` nome del selettore usando l'attributo. Ad esempio:

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

La maggior parte delle classi in Novell. iOS che `NSObject` derivano da esporrà i costruttori specifici della funzionalità dell'oggetto, ma esporrà anche diversi costruttori che non sono immediatamente evidenti.

I costruttori vengono usati come indicato di seguito:

```csharp
public Foo (IntPtr handle)
```

Questo costruttore viene usato per creare un'istanza della classe quando il runtime deve eseguire il mapping della classe a una classe non gestita. Questo errore si verifica quando si carica un file XIB/pennini.  A questo punto, il runtime di Objective-C avrà creato un oggetto nell'ambiente non gestito e verrà chiamato il costruttore per inizializzare il lato gestito.

In genere, è sufficiente chiamare il costruttore di base con il parametro handle e nel corpo eseguire le inizializzazioni necessarie.

```csharp
public Foo ()
```

Si tratta del costruttore predefinito per una classe e, nelle classi fornite da Novell. iOS, Inizializza la classe Foundation. NSObject e tutte le classi in between e alla fine concatena il metodo al metodo Objective-C `init` sulla classe.

```csharp
public Foo (NSObjectFlag x)
```

Questo costruttore viene utilizzato per inizializzare l'istanza di, ma impedisce al codice di chiamare il metodo Objective-C "init" alla fine. Questa operazione viene in genere usata quando è già stata eseguita la registrazione per l' `[Export]` inizializzazione (quando si usa nel costruttore) o quando è già stata eseguita l'inizializzazione tramite un'altra media.

```csharp
public Foo (NSCoder coder)
```

Questo costruttore viene fornito per i casi in cui l'oggetto viene inizializzato da un'istanza di NSCoding. Per ulteriori informazioni, vedere la guida alla programmazione per gli [archivi e la serializzazione](https://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/Archiving/index.html#//apple_ref/doc/uid/10000047i) di Apple.

#### <a name="exceptions"></a>Eccezioni

La progettazione dell'API Novell. iOS non genera eccezioni Objective-C come C# eccezioni. La progettazione impone che non venga inviata alcuna Garbage Collection al mondo Objective-C e che le eccezioni che devono essere generate vengano generate dall'associazione stessa prima che i dati non validi vengano mai passati al mondo Objective-C.

#### <a name="notifications"></a>Notifiche

In iOS e OS X gli sviluppatori possono sottoscrivere le notifiche trasmesse dalla piattaforma sottostante. Questa operazione viene eseguita tramite il `NSNotificationCenter.DefaultCenter.AddObserver` metodo. Il `AddObserver` metodo accetta due parametri: uno è la notifica che si desidera sottoscrivere. l'altro è il metodo da richiamare quando viene generata la notifica.

In Novell. iOS e Novell. Mac, le chiavi per le varie notifiche sono ospitate nella classe che attiva le notifiche. Ad esempio, le notifiche generate da `UIMenuController` sono ospitate `static NSString` come proprietà nelle `UIMenuController` classi che terminano con il nome "Notification".

### <a name="memory-management"></a>Gestione della memoria

Novell. iOS include un Garbage Collector che si occuperà di rilasciare le risorse quando non sono più in uso. Oltre all'Garbage Collector, tutti gli oggetti che derivano da `NSObject` implementano `System.IDisposable` l'interfaccia.

#### <a name="nsobject-and-idisposable"></a>NSObject e IDisposable

Esporre l' `IDisposable` interfaccia è un modo pratico per aiutare gli sviluppatori a rilasciare oggetti che potrebbero incapsulare blocchi di memoria di grandi dimensioni (ad esempio, un `UIImage` potrebbe sembrare semplicemente un puntatore innocente, ma potrebbe puntare a un'immagine di 2 megabyte) ) e altre risorse importanti e finite (ad esempio un buffer di decodifica video).

NSObject implementa l'interfaccia IDisposable e anche il [modello Dispose di .NET](https://msdn.microsoft.com/library/fs2xkftw.aspx). Ciò consente agli sviluppatori di sottoclasse NSObject di eseguire l'override del comportamento di eliminazione e rilasciare le proprie risorse su richiesta. Si consideri, ad esempio, questo controller di visualizzazione che mantiene una serie di immagini:

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

Quando un oggetto gestito viene eliminato, non è più utile. A questo punto è possibile che si disponga ancora di un riferimento agli oggetti, ma l'oggetto è per tutte le finalità e non è valido. Alcune API .NET assicurano questo problema generando un ObjectDisposedException se si tenta di accedere a qualsiasi metodo su un oggetto eliminato, ad esempio:

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

Anche se è ancora possibile accedere alla variabile "image", si tratta in realtà di un riferimento non valido e non punta più all'oggetto Objective-C che conteneva l'immagine.

Tuttavia, l'eliminazione di C# un oggetto in non significa che l'oggetto verrà necessariamente eliminato definitivamente. È sufficiente rilasciare il riferimento C# all'oggetto. È possibile che l'ambiente Cocoa possa avere mantenuto un riferimento per il proprio uso. Se, ad esempio, si imposta la proprietà Image di UIImageView su un'immagine e quindi si elimina l'immagine, il UIImageView sottostante aveva preso il proprio riferimento e manterrà un riferimento a questo oggetto fino a quando non ne verrà terminato l'utilizzo.

#### <a name="when-to-call-dispose"></a>Quando chiamare Dispose

Per eliminare l'oggetto, è necessario chiamare Dispose. Un possibile caso di utilizzo si verifica quando mono non è a conoscenza del fatto che il NSObject di lavoro contiene un riferimento a una risorsa importante, ad esempio la memoria o un pool di informazioni. In questi casi, è consigliabile chiamare Dispose per rilasciare immediatamente il riferimento alla memoria, anziché attendere che mono esegua un ciclo di Garbage Collection.

Internamente, quando mono crea [riferimenti NSString C# da stringhe](~/ios/internals/api-design/nsstring.md), li eliminerà immediatamente per ridurre la quantità di lavoro che il Garbage Collector deve eseguire. Minore è il numero di oggetti da gestire, più veloce sarà l'esecuzione del GC.

#### <a name="when-to-keep-references-to-objects"></a>Quando memorizzare i riferimenti agli oggetti

Un effetto collaterale che la gestione automatica della memoria è che il Garbage Collector eliminerà gli oggetti inutilizzati purché non vi siano riferimenti. Questa operazione può talvolta avere effetti collaterali sorprendenti, ad esempio, se si crea una variabile locale per contenere il controller di visualizzazione di primo livello o la finestra di primo livello e quindi si è rimasti indietro.

Se non si mantiene un riferimento nelle variabili statiche o di istanza agli oggetti, mono chiamerà tranquillamente il metodo Dispose () e rilascerà il riferimento all'oggetto. Poiché questo potrebbe essere l'unico riferimento in attesa, il runtime di Objective-C eliminerà l'oggetto per l'utente.

## <a name="related-links"></a>Collegamenti correlati

- [Campi di binding](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
