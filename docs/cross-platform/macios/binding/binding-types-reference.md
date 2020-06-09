---
title: Guida di riferimento ai tipi di binding
description: Questa guida di riferimento descrive diversi attributi e concetti che è necessario conoscere quando si creano associazioni C# per le librerie Objective-C.
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: da6bf97bfc5769647c63b55c289293e63f50e5cb
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84570999"
---
# <a name="binding-types-reference-guide"></a>Guida di riferimento ai tipi di binding

Questo documento descrive l'elenco degli attributi che è possibile usare per annotare i file del contratto API per guidare l'associazione e il codice generato

I contratti API Novell. iOS e Novell. Mac sono scritti in C# principalmente come definizioni di interfaccia che definiscono il modo in cui il codice Objective-C viene esposto in C#. Il processo implica una combinazione di dichiarazioni di interfaccia, oltre ad alcune definizioni di tipo di base che il contratto API potrebbe richiedere. Per un'introduzione ai tipi di binding, vedere la Guida complementare per l' [associazione di librerie Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md).

## <a name="type-definitions"></a>Definizioni di tipo

Sintassi:

```csharp
[BaseType (typeof (BTYPE))
interface MyType : [Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

Ogni interfaccia nella definizione del contratto che dispone dell' [`[BaseType]`](#BaseTypeAttribute) attributo che dichiara il tipo di base per l'oggetto generato. Nella dichiarazione precedente `MyType` viene generato un tipo C# di classe che si associa a un tipo Objective-C denominato `MyType` .

Se si specificano tipi dopo TypeName (nell'esempio precedente `Protocol1` e `Protocol2` ) utilizzando la sintassi di ereditarietà dell'interfaccia, il contenuto di tali interfacce verrà inline come se facessero parte del contratto per `MyType` .
Il modo in cui Novell. iOS emerge che un tipo adotta un protocollo consiste nell'incorporare tutti i metodi e le proprietà dichiarati nel protocollo nel tipo stesso.

Di seguito viene illustrato come definire la dichiarazione Objective-C per `UITextField` in un contratto Novell. iOS:

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

Viene scritto come questo come un contratto API C#:

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

È possibile controllare molti altri aspetti della generazione di codice applicando altri attributi all'interfaccia e configurando l' [`[BaseType]`](#BaseTypeAttribute) attributo.

### <a name="generating-events"></a>Generazione di eventi

Una funzionalità della progettazione dell'API Novell. iOS e Novell. Mac è che vengono mappate le classi delegate Objective-C come eventi e callback in C#. Gli utenti possono scegliere in base alle singole istanze se vogliono adottare il modello di programmazione Objective-C, assegnando a proprietà come `Delegate` un'istanza di una classe che implementa i vari metodi che il runtime di Objective-c chiamerà oppure scegliendo gli eventi e le proprietà di tipo C#.

Ecco un esempio di come usare il modello Objective-C:

```csharp
bool MakeDecision ()
{
    return true;
}

void Setup ()
{
     var scrollView = new UIScrollView (myRect);
     scrollView.Delegate = new MyScrollViewDelegate ();
     ...
}

class MyScrollViewDelegate : UIScrollViewDelegate {
    public override void Scrolled (UIScrollView scrollView)
    {
        Console.WriteLine ("Scrolled");
    }

    public override bool ShouldScrollToTop (UIScrollView scrollView)
    {
        return MakeDecision ();
    }
}
```

Nell'esempio precedente, è possibile notare che si è scelto di sovrascrivere due metodi, una notifica che si è verificato un evento di scorrimento e il secondo che è un callback che deve restituire un valore booleano che indica `scrollView` se deve scorrere verso l'alto o meno.

Il modello C# consente all'utente della libreria di ascoltare le notifiche usando la sintassi dell'evento C# o la sintassi della proprietà per associare i callback che dovrebbero restituire valori.

Questo è il modo in cui il codice C# per la stessa funzionalità ha un aspetto simile all'uso delle espressioni lambda:

```csharp
void Setup ()
{
    var scrollview = new UIScrollView (myRect);
    // Event connection, use += and multiple events can be connected
    scrollView.Scrolled += (sender, eventArgs) { Console.WriteLine ("Scrolled"); }

    // Property connection, use = only a single callback can be used
    scrollView.ShouldScrollToTop = (sv) => MakeDecision ();
}
```

Poiché gli eventi non restituiscono valori, ovvero hanno un tipo restituito void, è possibile connettere più copie. `ShouldScrollToTop`Non è un evento, bensì una proprietà con il tipo con `UIScrollViewCondition` questa firma:

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

Restituisce un `bool` valore, in questo caso la sintassi lambda consente di restituire semplicemente il valore dalla `MakeDecision` funzione.

Il generatore di associazioni supporta la generazione di eventi e proprietà che collegano una classe come `UIScrollView` con il relativo `UIScrollViewDelegate` (ben chiamare queste classi del modello). questa operazione viene eseguita annotando [`[BaseType]`](#BaseTypeAttribute) la definizione con i `Events` `Delegates` parametri e (descritto di seguito). Oltre ad annotare [`[BaseType]`](#BaseTypeAttribute) con tali parametri, è necessario informare il generatore di alcuni altri componenti.

Per gli eventi che accettano più parametri (in Objective-C la convenzione è che il primo parametro in una classe delegata è l'istanza dell'oggetto mittente), è necessario specificare il nome desiderato per la `EventArgs` classe generata. Questa operazione viene eseguita con l' [`[EventArgs]`](#EventArgsAttribute) attributo nella dichiarazione di metodo nella classe del modello. Ad esempio:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

La dichiarazione precedente genererà una `UIImagePickerImagePickedEventArgs` classe che deriva da `EventArgs` e comprime entrambi i parametri, `UIImage` e `NSDictionary` . Il generatore produce quanto segue:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Espone quindi quanto segue nella `UIImagePickerController` classe:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

I metodi del modello che restituiscono un valore sono associati in modo diverso. Che richiedono sia un nome per il delegato C# generato (la firma per il metodo) che un valore predefinito da restituire se l'utente non fornisce un'implementazione. Ad esempio, la `ShouldScrollToTop` definizione è la seguente:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

Sopra verrà creato un `UIScrollViewCondition` delegato con la firma illustrata sopra e se l'utente non fornisce un'implementazione, il valore restituito sarà true.

Oltre all' [`[DefaultValue]`](#DefaultValueAttribute) attributo, è anche possibile usare l' [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute) attributo che indica al generatore di restituire il valore del parametro specificato nella chiamata o il [`[NoDefaultValue]`](#NoDefaultValueAttribute) parametro che indica al generatore che non esiste alcun valore predefinito.

<a name="BaseTypeAttribute"></a>

### <a name="basetypeattribute"></a>BaseTypeAttribute

Sintassi:

```csharp
public class BaseTypeAttribute : Attribute {
        public BaseTypeAttribute (Type t);

        // Properties
        public Type BaseType { get; set; }
        public string Name { get; set; }
        public Type [] Events { get; set; }
        public string [] Delegates { get; set; }
        public string KeepRefUntil { get; set; }
}
```

#### <a name="basetypename"></a>BaseType.Name

Usare la `Name` proprietà per controllare il nome a cui verrà associato questo tipo nel mondo Objective-C. Viene in genere usato per fornire al tipo C# un nome conforme alle linee guida di progettazione .NET Framework, ma che esegue il mapping a un nome in Objective-C che non segue tale convenzione.

Esempio, nel caso seguente viene mappato il tipo Objective-C `NSURLConnection` a `NSUrlConnection` , in quanto le linee guida per la progettazione di .NET Framework usano "URL" invece di "URL":

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

Il nome specificato viene utilizzato come valore per l'attributo generato `[Register]` nell'associazione. Se `Name` viene omesso, viene usato il nome breve del tipo come valore per l' `[Register]` attributo nell'output generato.

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType. Events e BaseType. delegati

Queste proprietà vengono usate per gestire la generazione di eventi di tipo C# nelle classi generate. Vengono usati per collegare una determinata classe con la relativa classe delegata Objective-C. Si verificheranno molti casi in cui una classe usa una classe Delegate per inviare notifiche ed eventi. Ad esempio, un oggetto `BarcodeScanner` avrebbe una `BardodeScannerDelegate` classe complementare. La `BarcodeScanner` classe in genere dispone di una `Delegate` proprietà a cui si assegna un'istanza di `BarcodeScannerDelegate` , mentre questo funziona, potrebbe essere necessario esporre agli utenti un'interfaccia eventi di tipo C# e, in questi casi, si utilizzeranno le `Events` `Delegates` proprietà e dell' [`[BaseType]`](#BaseTypeAttribute) attributo.

Queste proprietà vengono sempre impostate insieme e devono avere lo stesso numero di elementi e rimanere sincronizzati. La `Delegates` matrice contiene una stringa per ogni delegato con tipizzazione debole di cui si vuole eseguire il wrapping e la `Events` matrice contiene un tipo per ogni tipo a cui si vuole associarlo.

```csharp
[BaseType (typeof (NSObject),
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIAccelerometerDelegate)})]
public interface UIAccelerometer {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIAccelerometerDelegate {
}
```

#### <a name="basetypekeeprefuntil"></a>BaseType. KeepRefUntil

Se si applica questo attributo quando vengono create nuove istanze di questa classe, l'istanza di tale oggetto verrà mantenuta fino a quando non viene richiamato il metodo a cui fa riferimento `KeepRefUntil` . Questa operazione è utile per migliorare l'usabilità delle API, quando non si vuole che l'utente mantenga un riferimento a un oggetto per l'uso del codice. Il valore di questa proprietà è il nome di un metodo nella `Delegate` classe, quindi è necessario utilizzare questo insieme `Events` anche alle `Delegates` proprietà e.

Nell'esempio seguente viene illustrato come viene utilizzato da `UIActionSheet` in Novell. iOS:

```csharp
[BaseType (typeof (NSObject), KeepRefUntil="Dismissed")]
[BaseType (typeof (UIView),
           KeepRefUntil="Dismissed",
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIActionSheetDelegate)})]
public interface UIActionSheet {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIActionSheetDelegate {
    [Export ("actionSheet:didDismissWithButtonIndex:"), EventArgs ("UIButton")]
    void Dismissed (UIActionSheet actionSheet, nint buttonIndex);
}
```

<a name="DesignatedDefaultCtorAttribute"></a>

### <a name="designateddefaultctorattribute"></a>DesignatedDefaultCtorAttribute

Quando questo attributo viene applicato alla definizione dell'interfaccia, verrà generato un `[DesignatedInitializer]` attributo sul costruttore predefinito (generato), che esegue il mapping al `init` selettore.

<a name="DisableDefaultCtorAttribute"></a>

### <a name="disabledefaultctorattribute"></a>DisableDefaultCtorAttribute

Quando questo attributo viene applicato alla definizione dell'interfaccia, impedisce al generatore di produrre il costruttore predefinito.

Utilizzare questo attributo quando è necessario che l'oggetto venga inizializzato con uno degli altri costruttori nella classe.

<a name="PrivateDefaultCtorAttribute"></a>

### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

Quando questo attributo viene applicato alla definizione dell'interfaccia, il costruttore predefinito viene contrassegnato come privato. Ciò significa che è ancora possibile creare un'istanza dell'oggetto di questa classe internamente dal file di estensione, ma solo che non sarà accessibile agli utenti della classe.

<a name="CategoryAttribute"></a>

### <a name="categoryattribute"></a>CategoryAttribute

Usare questo attributo in una definizione di tipo per associare le categorie Objective-C ed esporre tali categorie come metodi di estensione C# per eseguire il mirroring del modo in cui Objective-C espone la funzionalità.

Le categorie sono un meccanismo Objective-C usato per estendere il set di metodi e proprietà disponibili in una classe.   In pratica, vengono utilizzati per estendere la funzionalità di una classe di base (ad esempio `NSObject` ) quando un Framework specifico è collegato, ad esempio `UIKit` , rendendo disponibili i metodi, ma solo se il nuovo Framework è collegato.   In altri casi vengono usati per organizzare le funzionalità in una classe in base alla funzionalità.   Sono simili a metodi di estensione per C#.

Questa è l'aspetto di una categoria in Objective-C:

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

L'esempio precedente si trova in una libreria che estende le istanze di `UIView` con il metodo `makeBackgroundRed` .

Per associarle, è possibile usare l' [`[Category]`](#CategoryAttribute) attributo in una definizione di interfaccia.   Quando si usa l' [`[Category]`](#CategoryAttribute) attributo, il significato dell' [`[BaseType]`](#BaseTypeAttribute) attributo viene modificato da usato per specificare la classe base da estendere, in modo da essere il tipo da estendere.

Di seguito viene illustrato il modo in cui le `UIView` estensioni vengono associate e trasformate nei metodi di estensione C#:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

Il codice precedente creerà una `MyUIViewExtension` classe che contiene il `MakeBackgroundRed` metodo di estensione.   Ciò significa che è ora possibile chiamare `MakeBackgroundRed` su qualsiasi `UIView` sottoclasse, offrendo la stessa funzionalità che si otterrebbe in Objective-C.

In alcuni casi, si troveranno membri **statici** all'interno di categorie come nell'esempio seguente:

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

In questo modo si otterrà una definizione di interfaccia C# di categoria non **corretta** :

```csharp
[Category]
[BaseType (typeof (FooObject))]
interface FooObject_Extensions {

    // Incorrect Interface definition
    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Questa operazione non è corretta perché per utilizzare l' `BoolMethod` estensione è necessaria un'istanza di `FooObject` ma si sta associando un'estensione **statica** objc, questo è un effetto collaterale del fatto che i metodi di estensione C# sono implementati.

L'unico modo per utilizzare le definizioni precedenti è il seguente codice Ugly:

```csharp
(null as FooObject).BoolMethod (range);
```

Il suggerimento per evitare questo problema consiste nell'incorporare la `BoolMethod` definizione all'interno della `FooObject` definizione di interfaccia stessa, in modo da poter chiamare questa estensione come previsto `FooObject.BoolMethod (range)` .

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Viene emesso un avviso (BI1117) ogni volta che viene trovato un [`[Static]`](#StaticAttribute) membro all'interno di una [`[Category]`](#CategoryAttribute) definizione. Se si desidera effettivamente includere [`[Static]`](#StaticAttribute) membri all'interno [`[Category]`](#CategoryAttribute) delle definizioni, è possibile disattivare l'avviso utilizzando `[Category (allowStaticMembers: true)]` o decorando la definizione dell'interfaccia o del membro [`[Category]`](#CategoryAttribute) con [`[Internal]`](#InternalAttribute) .

<a name="StaticAttribute_Class"></a>

### <a name="staticattribute"></a>StaticAttribute

Quando questo attributo viene applicato a una classe, viene semplicemente generata una classe statica, una che non deriva da `NSObject` , quindi l' [`[BaseType]`](#BaseTypeAttribute) attributo viene ignorato. Le classi statiche vengono utilizzate per ospitare le variabili pubbliche C che si desidera esporre.

Ad esempio:

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

Genererà una classe C# con l'API seguente:

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>Definizioni di protocollo/modello

I modelli vengono in genere utilizzati dall'implementazione del protocollo.
Si differenziano per il fatto che il runtime registrerà solo con Objective-C i metodi che sono stati effettivamente sovrascritti.
In caso contrario, il metodo non verrà registrato.

In generale, quando si crea una sottoclasse di una classe contrassegnata con `ModelAttribute` , non è necessario chiamare il metodo di base.   La chiamata a tale metodo genererà un'eccezione. si presuppone che si debba implementare l'intero comportamento sulla sottoclasse per tutti i metodi di cui si esegue l'override.

<a name="AbstractAttribute"></a>

### <a name="abstractattribute"></a>AbstractAttribute

Per impostazione predefinita, i membri che fanno parte di un protocollo non sono obbligatori. Ciò consente agli utenti di creare una sottoclasse dell' `Model` oggetto semplicemente derivando dalla classe in C# ed eseguendo l'override solo dei metodi a cui si è interessati. In alcuni casi il contratto Objective-C richiede che l'utente fornisca un'implementazione per questo metodo (quelli contrassegnati con la `@required` direttiva in Objective-c). In questi casi, è necessario contrassegnare tali metodi con l' `[Abstract]` attributo.

L' `[Abstract]` attributo può essere applicato a metodi o proprietà e fa in modo che il generatore contrassegni il membro generato come astratto e che la classe sia una classe astratta.

Il codice seguente è tratto da Novell. iOS:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UITableViewDataSource {
    [Export ("tableView:numberOfRowsInSection:")]
    [Abstract]
    nint RowsInSection (UITableView tableView, nint section);
}
```

<a name="DefaultValueAttribute"></a>

### <a name="defaultvalueattribute"></a>DefaultValueAttribute

Specifica il valore predefinito che deve essere restituito da un metodo del modello se l'utente non fornisce un metodo per questo particolare metodo nell'oggetto modello.

Sintassi:

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

Ad esempio, nella classe delegata immaginaria seguente per una `Camera` classe, viene fornito un oggetto `ShouldUploadToServer` che verrebbe esposto come una proprietà nella `Camera` classe. Se l'utente della `Camera` classe non imposta in modo esplicito il valore su un'espressione lambda che può rispondere true o false, il valore predefinito restituito in questo caso sarebbe false, ovvero il valore specificato nell' `DefaultValue` attributo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

Se l'utente imposta un gestore nella classe immaginaria, questo valore verrà ignorato:

```csharp
var camera = new Camera ();
camera.ShouldUploadToServer = (camera, action) => return SomeDecision ();
```

Vedere anche: [`[NoDefaultValue]`](#NoDefaultValueAttribute) [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute) .

<a name="DefaultValueFromArgumentAttribute"></a>

### <a name="defaultvaluefromargumentattribute"></a>DefaultValueFromArgumentAttribute

Sintassi:

```csharp
public class DefaultValueFromArgumentAttribute : Attribute {
    public DefaultValueFromArgumentAttribute (string argument);
    public string Argument { get; }
}
```

Questo attributo, se fornito su un metodo che restituisce un valore in una classe del modello, indicherà al generatore di restituire il valore del parametro specificato se l'utente non ha fornito il proprio metodo o l'espressione lambda.

Esempio:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

Nel caso precedente, se l'utente della `NSAnimation` classe ha scelto di usare uno degli eventi o delle proprietà di C# e non è stato impostato `NSAnimation.ComputeAnimationCurve` su un metodo o un'espressione lambda, il valore restituito sarà il valore passato nel parametro progress.

Vedere anche: [`[NoDefaultValue]`](#NoDefaultValueAttribute) ,[`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

A volte è opportuno non esporre un evento o una proprietà di delegato da una classe di modello nella classe host, quindi l'aggiunta di questo attributo indicherà al generatore di evitare la generazione di qualsiasi metodo decorato con esso.

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);

    [Export ("imagePickerController:didFinishPickingImage:"), IgnoredInDelegate)] // No event generated for this method
    void FinishedPickingImage (UIImagePickerController picker, UIImage image);
}
```

### <a name="delegatenameattribute"></a>DelegateNameAttribute

Questo attributo viene utilizzato nei metodi del modello che restituiscono valori per impostare il nome della firma del delegato da utilizzare.

Esempio:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, float progress);
}
```

Con la definizione precedente, il generatore produrrà la seguente dichiarazione pubblica:

```csharp
public delegate float NSAnimationProgress (MonoMac.AppKit.NSAnimation animation, float progress);
```

### <a name="delegateapinameattribute"></a>DelegateApiNameAttribute

Questo attributo viene usato per consentire al generatore di modificare il nome della proprietà generata nella classe host. A volte è utile quando il nome del metodo della classe FooDelegate è appropriato per la classe Delegate, ma potrebbe sembrare strano nella classe host come proprietà.

Si tratta di un'operazione molto utile (e necessaria) quando si hanno due o più metodi di overload che hanno senso mantenerli denominati come sono nella classe FooDelegate, ma si desidera esporli nella classe host con un nome migliore.

Esempio:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

Con la definizione precedente, il generatore produrrà la seguente dichiarazione pubblica nella classe host:

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```

<a name="EventArgsAttribute"></a>

### <a name="eventargsattribute"></a>EventArgsAttribute

Per gli eventi che accettano più parametri (in Objective-C la convenzione è che il primo parametro in una classe delegata è l'istanza dell'oggetto mittente), è necessario specificare il nome desiderato per la classe EventArgs generata. Questa operazione viene eseguita con l' `[EventArgs]` attributo nella dichiarazione di metodo nella `Model` classe.

Ad esempio:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

La dichiarazione precedente genera una `UIImagePickerImagePickedEventArgs` classe che deriva da EventArgs e comprime entrambi i parametri, `UIImage` e `NSDictionary` . Il generatore produce quanto segue:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Espone quindi quanto segue nella `UIImagePickerController` classe:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

### <a name="eventnameattribute"></a>EventNameAttribute

Questo attributo viene usato per consentire al generatore di modificare il nome di un evento o di una proprietà generata nella classe. A volte è utile quando il nome del metodo della classe del modello ha senso per la classe del modello, ma potrebbe sembrare strano nella classe di origine come un evento o una proprietà.

Ad esempio, `UIWebView` Usa il bit seguente di `UIWebViewDelegate` :

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

Il sopra esposto viene esposto `LoadingFinished` come metodo in `UIWebViewDelegate` , ma `LoadFinished` come evento da associare a in un `UIWebView` :

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute"></a>

### <a name="modelattribute"></a>ModelAttribute

Quando si applica l' `[Model]` attributo a una definizione di tipo nell'API del contratto, il runtime genera codice speciale che rileverà solo le chiamate ai metodi nella classe se l'utente ha sovrascritto un metodo nella classe. Questo attributo viene in genere applicato a tutte le API che esegue il wrapping di una classe delegata Objective-C.

<a name="NoDefaultValueAttribute"></a>

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

Specifica che il metodo sul modello non fornisce un valore restituito predefinito.

Funziona con il runtime di Objective-C rispondendo `false` alla richiesta di runtime Objective-c per determinare se il selettore specificato è implementato in questa classe.

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

Vedere anche: [`[DefaultValue]`](#DefaultValueAttribute) ,[`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)  

<a name="ProtocolAttribute"></a>

## <a name="protocols"></a>Protocolli

Il concetto di protocollo Objective-C non esiste realmente in C#. I protocolli sono simili alle interfacce C#, ma si differenziano per il fatto che non tutti i metodi e le proprietà dichiarati in un protocollo devono essere implementati dalla classe che la adotta. Alcuni metodi e proprietà, invece, sono facoltativi.

Alcuni protocolli vengono in genere usati come classi di modelli, che devono essere associati usando l' [`[Model]`](#ModelAttribute) attributo.

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}
```

A partire da Novell. iOS 7,0 è stata incorporata una nuova e migliorata funzionalità di associazione del protocollo.  Tutte le definizioni che contengono l'attributo genereranno `[Protocol]` tre classi di supporto che migliorano notevolmente il modo in cui si utilizzano i protocolli:

```csharp
// Full method implementation, contains all methods
class MyProtocol : IMyProtocol {
    public void Say (string msg);
    public void Listen (string msg);
}

// Interface that contains only the required methods
interface IMyProtocol: INativeObject, IDisposable {
    [Export ("say:")]
    void Say (string msg);
}

// Extension methods
static class IMyProtocol_Extensions {
    public static void Optional (this IMyProtocol this, string msg);
    }
}
```

L' **implementazione della classe** fornisce una classe astratta completa che è possibile ignorare i singoli metodi di e ottenere la massima indipendenza dai tipi. Tuttavia, a causa di C# che non supporta l'ereditarietà multipla, esistono scenari in cui potrebbe essere necessaria una classe base diversa, ma si vuole comunque implementare un'interfaccia.

Qui viene fornita la **definizione dell'interfaccia** generata.  Si tratta di un'interfaccia che dispone di tutti i metodi richiesti dal protocollo.  Ciò consente agli sviluppatori che vogliono implementare il protocollo di implementare semplicemente l'interfaccia.  Il runtime registrerà automaticamente il tipo come adozione del protocollo.

Si noti che l'interfaccia elenca solo i metodi richiesti ed espone i metodi facoltativi.   Ciò significa che le classi che adottano il protocollo otterranno il controllo del tipo completo per i metodi richiesti, ma dovranno ricorrere alla tipizzazione debole (usando manualmente gli attributi di esportazione e la firma corrispondente) per i metodi di protocollo facoltativi.

Per semplificare l'utilizzo di un'API che utilizza protocolli, lo strumento di associazione produrrà anche una classe del metodo Extensions che espone tutti i metodi facoltativi.   Ciò significa che, se si utilizza un'API, sarà possibile considerare i protocolli come dotati di tutti i metodi.

Se si vogliono usare le definizioni di protocollo nell'API, sarà necessario scrivere interfacce vuote di ossatura nella definizione dell'API.   Se si vuole usare il protocollo protocollo in un'API, è necessario eseguire questa operazione:

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}

interface IMyProtocol {}

[BaseType (typeof(NSObject))]
interface MyTool {
    [Export ("getProtocol")]
    IMyProtocol GetProtocol ();
}
```

Il valore precedente è necessario perché in fase di associazione `IMyProtocol` non esiste, per questo motivo è necessario fornire un'interfaccia vuota.

### <a name="adopting-protocol-generated-interfaces"></a>Adozione di interfacce generate dal protocollo

Ogni volta che si implementa una delle interfacce generate per i protocolli, come indicato di seguito:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

L'implementazione per i metodi di interfaccia viene automaticamente esportata con il nome appropriato, quindi è equivalente a quanto segue:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

Non è importante se l'interfaccia viene implementata in modo implicito o esplicito.

### <a name="protocol-inlining"></a>Incorporamento del protocollo

Quando si associano i tipi Objective-C esistenti che sono stati dichiarati come adozione di un protocollo, è necessario inline direttamente il protocollo. A tale scopo, è sufficiente dichiarare il protocollo come interfaccia senza alcun [`[BaseType]`](#BaseTypeAttribute) attributo ed elencare il protocollo nell'elenco delle interfacce di base per l'interfaccia.

Esempio:

```csharp
interface SpeakProtocol {
    [Export ("say:")]
    void Say (string msg);
}

[BaseType (typeof (NSObject))]
interface Robot : SpeakProtocol {
    [Export ("awake")]
    bool Awake { get; set; }
}
```

## <a name="member-definitions"></a>Definizioni dei membri

Gli attributi in questa sezione vengono applicati a singoli membri di un tipo: proprietà e dichiarazioni di metodo.

### <a name="alignattribute"></a>AlignAttribute

Utilizzato per specificare il valore di allineamento per i tipi restituiti della proprietà. Determinate proprietà accettano puntatori a indirizzi che devono essere allineati a determinati limiti (in Novell. iOS questo accade ad esempio con alcune `GLKBaseEffect` proprietà che devono essere allineate a 16 byte). È possibile usare questa proprietà per decorare il getter e usare il valore di allineamento. Viene in genere utilizzato con i `OpenTK.Vector4` `OpenTK.Matrix4` tipi e quando si integrano con le API Objective-C.

Esempio:

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```

### <a name="appearanceattribute"></a>AppearanceAttribute

L' `[Appearance]` attributo è limitato a iOS 5, in cui è stato introdotto il gestore di aspetto.

L' `[Appearance]` attributo può essere applicato a qualsiasi metodo o proprietà che partecipa al `UIAppearance` Framework. Quando questo attributo viene applicato a un metodo o a una proprietà in una classe, il generatore di associazione viene indirizzato per creare una classe di aspetto fortemente tipizzata utilizzata per applicare uno stile a tutte le istanze di questa classe o le istanze che corrispondono a determinati criteri.

Esempio:

```csharp
public interface UIToolbar {
    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);
}
```

Il codice precedente genera il codice seguente in UIToolbar:

```csharp
public partial class UIToolbar {
    public partial class UIToolbarAppearance : UIView.UIViewAppearance {
        public virtual void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);
        public virtual UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics)
    }
    public static new UIToolbarAppearance Appearance { get; }
    public static new UIToolbarAppearance AppearanceWhenContainedIn (params Type [] containers);
}
```

### <a name="autoreleaseattribute-xamarinios-54"></a>AutoReleaseAttribute (Novell. iOS 5,4)

Usare i `[AutoReleaseAttribute]` metodi e le proprietà per eseguire il wrapping della chiamata del metodo al metodo in un oggetto `NSAutoReleasePool` .

In Objective-C sono disponibili alcuni metodi che restituiscono valori che vengono aggiunti all'impostazione predefinita `NSAutoReleasePool` . Per impostazione predefinita, vengono indirizzati al thread `NSAutoReleasePool` , ma poiché Novell. iOS mantiene anche un riferimento agli oggetti fino a quando l'oggetto gestito si trova, potrebbe non essere necessario mantenere un riferimento aggiuntivo in `NSAutoReleasePool` che verrà svuotato solo fino a quando il thread non restituisce il controllo al thread successivo oppure si torna al ciclo principale.

Questo attributo viene applicato ad esempio in proprietà complesse, ad esempio `UIImage.FromFile` , che restituisce gli oggetti che sono stati aggiunti al valore predefinito `NSAutoReleasePool` . Senza questo attributo, le immagini vengono conservate fino a quando il thread non ha restituito il controllo al ciclo principale. UF il thread era un tipo di Downloader in background che è sempre attivo e in attesa di lavoro, le immagini non vengono mai rilasciate.

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

`[ForcedTypeAttribute]`Viene utilizzato per imporre la creazione di un tipo gestito anche se l'oggetto non gestito restituito non corrisponde al tipo descritto nella definizione dell'associazione.

Questa operazione è utile quando il tipo descritto in un'intestazione non corrisponde al tipo restituito del metodo nativo. ad esempio, adottare la definizione Objective-C seguente da `NSURLSession` :

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

Indica chiaramente che verrà restituita un' `NSURLSessionDownloadTask` istanza, ma che **restituisce** `NSURLSessionTask` , ovvero una superclasse e pertanto non convertibile in `NSURLSessionDownloadTask` . Poiché si trova in un contesto indipendente dai tipi, `InvalidCastException` si verificherà un errore.

Per conformarsi alla descrizione dell'intestazione ed evitare `InvalidCastException` , `[ForcedTypeAttribute]` viene utilizzato.

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

`[ForcedTypeAttribute]`Accetta inoltre un valore booleano denominato `Owns` che è `false` per impostazione predefinita `[ForcedType (owns: true)]` . Il parametro proprietario viene usato per seguire i [criteri di proprietà](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html) per gli oggetti **Core Foundation** .

`[ForcedTypeAttribute]`È valido solo per parametri, proprietà e valore restituito.

<a name="BindAsAttribute"></a>

### <a name="bindasattribute"></a>BindAsAttribute

`[BindAsAttribute]`Consente di associare `NSNumber` `NSValue` e `NSString` (enumerazioni) a tipi C# più accurati. L'attributo può essere usato per creare un'API .NET migliore, più accurata sull'API nativa.

È possibile decorare i metodi (in valore restituito), i parametri e le proprietà con `BindAs` . L'unica restrizione è che il membro **non deve** trovarsi all'interno di un' `[Protocol]` [`[Model]`](#ModelAttribute) interfaccia o.

Ad esempio:

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Output:

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

Internamente si eseguiranno le `bool?`  <->  `NSNumber` `CGRect`  <->  `NSValue` conversioni e.

I tipi di incapsulamento supportati correnti sono:

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

I tipi di dati C# seguenti sono supportati per l'incapsulamento da/in `NSValue` :

* CGAffineTransform
* NSRange
* CGVector
* SCNMatrix4
* CLLocationCoordinate2D
* SCNVector3
* SCNVector4
* CGPoint/PointF
* CGRect/RectangleF
* CGSize/SizeF
* UIEdgeInsets
* UIOffset
* MKCoordinateSpan
* CMTimeRange
* CMTime
* CMTimeMapping
* CATransform3D

#### <a name="nsnumber"></a>NSNumber

I tipi di dati C# seguenti sono supportati per l'incapsulamento da/in `NSNumber` :

* bool
* byte
* double
* float
* short
* INT
* long
* sbyte
* ushort
* uint
* ulong
* nfloat
* nint
* nuint
* Enumerazioni

#### <a name="nsstring"></a>NSString

[`[BindAs]`](#BindAsAttribute)funziona in conjuntion con [enumerazioni supportate da una costante NSString](#enum-attributes) in modo da poter creare un'API .NET migliore, ad esempio:

```csharp
[BindAs (typeof (CAScroll))]
[Export ("supportedScrollMode")]
NSString SupportedScrollMode { get; set; }
```

Output:

```csharp
[Export ("supportedScrollMode")]
CAScroll SupportedScrollMode { get; set; }
```

La conversione verrà gestita `enum`  <->  `NSString` solo se il tipo enum fornito a [`[BindAs]`](#BindAsAttribute) è [supportato da una costante NSString](#enum-attributes).

#### <a name="arrays"></a>Matrici

[`[BindAs]`](#BindAsAttribute)supporta anche matrici di qualsiasi tipo supportato, è possibile avere la definizione API seguente come esempio:

```csharp
[return: BindAs (typeof (CAScroll []))]
[Export ("getScrollModesAt:")]
NSString [] GetScrollModes ([BindAs (typeof (CGRect []))] NSValue [] rects);
```

Output:

```csharp
[Export ("getScrollModesAt:")]
CAScroll? [] GetScrollModes (CGRect [] rects) { ... }
```

Il `rects` parametro verrà incapsulato in un oggetto `NSArray` che contiene un oggetto `NSValue` per ogni `CGRect` e in return verrà restituita una matrice di `CAScroll?` che è stata creata utilizzando i valori dell'oggetto restituito che `NSArray` contiene `NSStrings` .

<a name="BindAttribute"></a>

### <a name="bindattribute"></a>BindAttribute

L' `[Bind]` attributo ha due usi uno se applicato a una dichiarazione di metodo o di proprietà e un altro quando viene applicato al Getter o al setter singolo in una proprietà.

Quando viene usato per un metodo o una proprietà, l'effetto dell' `[Bind]` attributo è generare un metodo che richiama il selettore specificato. Tuttavia, il metodo generato risultante non è decorato con l' [`[Export]`](#ExportAttribute) attributo, il che significa che non può partecipare all'override del metodo. Viene in genere usato in combinazione con l' `[Target]` attributo per l'implementazione di metodi di estensione Objective-C.

Ad esempio:

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

Quando viene usato in un getter o un setter, l' `[Bind]` attributo viene usato per modificare i valori predefiniti dedotti dal generatore di codice durante la generazione dei nomi di selettori Objective-C getter e setter per una proprietà. Per impostazione predefinita, quando si contrassegna una proprietà con il nome `fooBar` , il generatore genera un' `fooBar` esportazione per il getter e `setFooBar:` per il setter. In alcuni casi, Objective-C non segue questa convenzione, in genere il nome del Getter viene modificato in `isFooBar` .
Usare questo attributo per informare il generatore di questo.

Ad esempio:

```csharp
// Default behavior
[Export ("active")]
bool Active { get; set; }

// Custom naming with the Bind attribute
[Export ("visible")]
bool Visible { [Bind ("isVisible")] get; set; }
```

<a name="AsyncAttribute"></a>

### <a name="asyncattribute"></a>AsyncAttribute

Disponibile solo in Novell. iOS 6,3 e versioni successive.

Questo attributo può essere applicato ai metodi che accettano un gestore di completamento come ultimo argomento.

È possibile utilizzare l' `[Async]` attributo nei metodi il cui ultimo argomento è un callback.  Quando si applica questa operazione a un metodo, il generatore di associazioni genererà una versione di tale metodo con il suffisso `Async` .  Se il callback non accetta parametri, il valore restituito sarà `Task` , se il callback accetta un parametro, il risultato sarà un oggetto `Task<T>` .

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

Il codice seguente genererà questo metodo asincrono:

```csharp
Task LoadFileAsync (string file);
```

Se il callback accetta più parametri, è necessario impostare `ResultType` o `ResultTypeName` per specificare il nome desiderato del tipo generato che conterrà tutte le proprietà.

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

Il codice seguente genererà questo metodo asincrono, dove `FileLoading` contiene le proprietà per accedere a `files` e `byteCount` :

```csharp
Task<FileLoading> LoadFile (string file);
```

Se l'ultimo parametro del callback è un oggetto `NSError` , il metodo generato `Async` verificherà se il valore non è null. in tal caso, il metodo asincrono generato imposterà l'eccezione dell'attività.

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

Il codice precedente genera il seguente metodo asincrono:

```csharp
Task<string> UploadAsync (string file);
```

In questo modo, l'attività risultante avrà l'eccezione impostata su un oggetto `NSErrorException` che esegue il wrapping dell'oggetto risultante `NSError` .

#### <a name="asyncattributeresulttype"></a>AsyncAttribute. ResultType

Utilizzare questa proprietà per specificare il valore per l' `Task` oggetto restituito.   Questo parametro accetta un tipo esistente, quindi deve essere definito in una delle definizioni API principali.

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

Utilizzare questa proprietà per specificare il valore per l' `Task` oggetto restituito.   Questo parametro accetta il nome del tipo desiderato, il generatore produrrà una serie di proprietà, una per ogni parametro richiesto dal callback.

#### <a name="asyncattributemethodname"></a>AsyncAttribute. MethodName

Usare questa proprietà per personalizzare il nome dei metodi asincroni generati.   Per impostazione predefinita, viene usato il nome del metodo e viene aggiunto il testo "Async", che può essere usato per modificare questa impostazione predefinita.

<a name="DesignatedInitializerAttribute"></a>

### <a name="designatedinitializerattribute"></a>DesignatedInitializerAttribute

Quando questo attributo viene applicato a un costruttore, genererà lo stesso `[DesignatedInitializer]` nell'assembly della piattaforma finale. Questo consente all'IDE di indicare quale costruttore deve essere usato nelle sottoclassi.

Questa operazione deve essere mappata all'uso di Objective-C/Clang `__attribute__((objc_designated_initializer))` .

<a name="DisableZeroCopyAttribute"></a>

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

Questo attributo viene applicato ai parametri stringa o alle proprietà di stringa e indica al generatore di codice di non utilizzare il marshalling della stringa di copia zero per questo parametro e di creare invece una nuova istanza di NSString dalla stringa C#.
Questo attributo è necessario solo per le stringhe se si indica al generatore di usare il marshalling della stringa di copia zero usando l' `--zero-copy` opzione della riga di comando o impostando l'attributo a livello di assembly `ZeroCopyStringsAttribute` .

Questa operazione è necessaria nei casi in cui la proprietà viene dichiarata in Objective-C come `retain` `assign` proprietà o anziché come `copy` Proprietà. Si tratta in genere di librerie di terze parti che sono state ottimizzate in maniera non corretta dagli sviluppatori. In generale, `retain` le `assign` `NSString` proprietà o non sono corrette perché `NSMutableString` o le classi derivate dall'utente di `NSString` potrebbero modificare il contenuto delle stringhe senza conoscere il codice della libreria, suddividendo leggermente l'applicazione. Questa situazione si verifica in genere a causa dell'ottimizzazione prematura.

Di seguito vengono illustrate due proprietà di questo tipo in Objective-C:

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```

<a name="DisposeAttribute"></a>

### <a name="disposeattribute"></a>DisposeAttribute

Quando si applica `[DisposeAttribute]` a una classe, si fornisce un frammento di codice che verrà aggiunto all' `Dispose()` implementazione del metodo della classe.

Poiché il `Dispose` metodo viene generato automaticamente dagli `bmac-native` strumenti e `btouch-native` , è necessario usare l' `[Dispose]` attributo per inserire codice nell' `Dispose` implementazione del metodo generato.

Ad esempio:

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute"></a>

### <a name="exportattribute"></a>ExportAttribute

L' `[Export]` attributo viene utilizzato per contrassegnare un metodo o una proprietà da esporre al runtime di Objective-C. Questo attributo viene condiviso tra lo strumento di associazione e i runtime Novell. iOS e Novell. Mac effettivi. Per i metodi, il parametro viene passato Verbatim al codice generato, per le proprietà, vengono generate le esportazioni getter e setter in base alla dichiarazione di base (vedere la sezione in [`[BindAttribute]`](#BindAttribute) per informazioni su come modificare il comportamento dello strumento di associazione).

Sintassi:

```csharp
public enum ArgumentSemantic {
    None, Assign, Copy, Retain.
}

[AttributeUsage (AttributeTargets.Method | AttributeTargets.Constructor | AttributeTargets.Property)]
public class ExportAttribute : Attribute {
    public ExportAttribute();
    public ExportAttribute (string selector);
    public ExportAttribute (string selector, ArgumentSemantic semantic);
    public string Selector { get; set; }
    public ArgumentSemantic ArgumentSemantic { get; set; }
}
```

Il [selettore](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html) rappresenta il nome del metodo o della proprietà Objective-C sottostante da associare.

#### <a name="exportattributeargumentsemantic"></a>ExportAttribute. ArgumentSemantic

<a name="FieldAttribute"></a>

### <a name="fieldattribute"></a>FieldAttribute

Questo attributo viene usato per esporre una variabile globale C come campo caricato su richiesta ed esposto al codice C#. Questa operazione è in genere necessaria per ottenere i valori delle costanti definite in C o Objective-C e che potrebbero essere token utilizzati in alcune API o i cui valori sono opachi e che devono essere utilizzati così come sono dal codice utente.

Sintassi:

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

`symbolName`È il simbolo C con il quale eseguire il collegamento. Per impostazione predefinita, questo verrà caricato da una libreria il cui nome viene dedotto dallo spazio dei nomi in cui è definito il tipo. Se non si tratta della libreria in cui viene cercato il simbolo, è necessario passare il `libraryName` parametro. Se si sta collegando una libreria statica, usare `__Internal` come `libraryName` parametro.

Le proprietà generate sono sempre statiche.

Le proprietà contrassegnate con l'attributo field possono essere dei tipi seguenti:

* `NSString`
* `NSArray`
* `nint` / `int` / `long`
* `nuint` / `uint` / `ulong`
* `nfloat` / `float`
* `double`
* `CGSize`
* `System.IntPtr`
* Enumerazioni

I setter non sono supportati per le [enumerazioni supportate dalle costanti NSString](#enum-attributes), ma possono essere associate manualmente, se necessario.

Esempio:

```csharp
[Static]
interface CameraEffects {
     [Field ("kCameraEffectsZoomFactorKey", "CameraLibrary")]
     NSString ZoomFactorKey { get; }
}
```

<a name="InternalAttribute"></a>

### <a name="internalattribute"></a>InternalAttribute

L' `[Internal]` attributo può essere applicato a metodi o proprietà e ha l'effetto di contrassegnare il codice generato con la `internal` parola chiave C# rendendo il codice accessibile solo al codice nell'assembly generato. Questa funzionalità viene in genere usata per nascondere API troppo basse o fornire un'API pubblica non ottimale che si vuole migliorare o per le API che non sono supportate dal generatore e richiedono una codifica manuale.

Quando si progetta l'associazione, in genere si nasconde il metodo o la proprietà utilizzando questo attributo e si specifica un nome diverso per il metodo o la proprietà e quindi si aggiunge un wrapper fortemente tipizzato che espone la funzionalità sottostante per il file di supporto.

Ad esempio:

```csharp
[Internal]
[Export ("setValue:forKey:")]
void _SetValueForKey (NSObject value, NSObject key);

[Internal]
[Export ("getValueForKey:")]
NSObject _GetValueForKey (NSObject key);
```

Nel file di supporto potrebbe quindi essere presente codice simile al seguente:

```csharp
public NSObject this [NSObject idx] {
    get {
        return _GetValueForKey (idx);
    }
    set {
        _SetValueForKey (value, idx);
    }
}
```

<a name="IsThreadStaticAttribute"></a>

### <a name="isthreadstaticattribute"></a>IsThreadStaticAttribute

Questo attributo contrassegna il campo sottostante per una proprietà che deve essere annotata con l' `[ThreadStatic]` attributo .NET. Questa operazione è utile se il campo è una variabile thread-static.

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions (Novell. iOS 6.0.6)

Questo attributo renderà un metodo che supporta le eccezioni native (Objective-C).
Invece di chiamare `objc_msgSend` direttamente, la chiamata passa attraverso un trampolino personalizzato che intercetta le eccezioni ObjectiveC e ne esegue il marshalling in eccezioni gestite.

Attualmente `objc_msgSend` sono supportate solo alcune firme (è possibile scoprire se una firma non è supportata quando il collegamento nativo di un'app che usa l'associazione ha esito negativo con un monotouch_ mancante *_objc_msgSend* simbolo), ma è possibile aggiungere altro alla richiesta.

### <a name="newattribute"></a>NewAttribute

Questo attributo viene applicato ai metodi e alle proprietà affinché il generatore generi la `new` parola chiave davanti alla dichiarazione.

Viene usato per evitare gli avvisi del compilatore quando lo stesso nome di metodo o proprietà viene introdotto in una sottoclasse già esistente in una classe base.

<a name="NotificationAttribute"></a>

### <a name="notificationattribute"></a>NotificationAttribute

È possibile applicare questo attributo ai campi affinché il generatore produca una classe di notifiche Helper fortemente tipizzata.

Questo attributo può essere usato senza argomenti per le notifiche che non contengono payload oppure è possibile specificare un oggetto `System.Type` che fa riferimento a un'altra interfaccia nella definizione dell'API, in genere con il nome che termina con "EventArgs". Il generatore trasformerà l'interfaccia in una classe che sottoclassi `EventArgs` e includerà tutte le proprietà elencate. L' [`[Export]`](#ExportAttribute) attributo deve essere utilizzato nella `EventArgs` classe per elencare il nome della chiave utilizzata per cercare il dizionario Objective-C per recuperare il valore.

Ad esempio:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

Il codice precedente genererà una classe annidata `MyClass.Notifications` con i metodi seguenti:

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
      public static NSObject ObserveDidStart (NSObject objectToObserve, EventHandler<NSNotificationEventArgs> handler)
   }
}
```

Gli utenti del codice possono quindi sottoscrivere facilmente le notifiche pubblicate in [NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter) usando un codice simile al seguente:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

O per impostare un oggetto specifico da osservare. Se si passa `null` a `objectToObserve` questo metodo, si comporterà esattamente come l'altro peer.

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

Il valore restituito da `ObserveDidStart` può essere usato per arrestare facilmente la ricezione delle notifiche, come segue:

```csharp
token.Dispose ();
```

In alternativa, è possibile chiamare [NSNotification. DefaultCenter. RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) e passare il token. Se la notifica contiene parametri, è necessario specificare un'interfaccia di supporto `EventArgs` simile alla seguente:

```csharp
interface MyClass {
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}

// The helper EventArgs declaration
interface MyScreenChangedEventArgs {
    [Export ("ScreenXKey")]
    nint ScreenX { get; set; }

    [Export ("ScreenYKey")]
    nint ScreenY { get; set; }

    [Export ("DidGoOffKey")]
    [ProbePresence]
    bool DidGoOff { get; }
}
```

In precedenza viene generata una `MyScreenChangedEventArgs` classe con le `ScreenX` `ScreenY` proprietà e che recupereranno i dati dal dizionario [NSNotification. UserInfo](xref:Foundation.NSNotification.UserInfo) usando le chiavi `ScreenXKey` e `ScreenYKey` rispettivamente e applicando le conversioni corrette. L' `[ProbePresence]` attributo viene usato per il generatore per verificare se la chiave è impostata in `UserInfo` , anziché tentare di estrarre il valore. Viene usato nei casi in cui la presenza della chiave è il valore, in genere per i valori booleani.

In questo modo è possibile scrivere codice simile al seguente:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

In alcuni casi, non esiste alcuna costante associata al valore passato nel dizionario.  Apple a volte usa costanti simboli pubbliche e talvolta usa costanti di stringa.  Per impostazione predefinita [`[Export]`](#ExportAttribute) , l'attributo nella `EventArgs` classe fornita utilizzerà il nome specificato come simbolo pubblico da cercare in fase di esecuzione.  In caso contrario, è preferibile eseguire la ricerca come costante di stringa, quindi passare il `ArgumentSemantic.Assign` valore all'attributo Export.

**Novità in Novell. iOS 8,4**

In alcuni casi, le notifiche iniziano senza alcun argomento, pertanto l'uso di [`[Notification]`](#NotificationAttribute) senza argomenti è accettabile.  Tuttavia, a volte verranno introdotti i parametri della notifica.  Per supportare questo scenario, l'attributo può essere applicato più volte.

Se si sta sviluppando un'associazione e si desidera evitare di interferire con il codice utente esistente, è possibile attivare una notifica esistente da:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

In una versione in cui l'attributo di notifica viene elencato due volte, come indicato di seguito:

```csharp
interface MyClass {
    [Notification]
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

<a name="NullAllowedAttribute"></a>

### <a name="nullallowedattribute"></a>NullAllowedAttribute

Quando viene applicato a una proprietà, la proprietà viene contrassegnata in modo da consentire l'assegnazione del valore `null` . Questa operazione è valida solo per i tipi di riferimento.

Quando viene applicato a un parametro in una firma del metodo, indica che il parametro specificato può essere null e che non deve essere eseguito alcun controllo per il passaggio di `null` valori.

Se il tipo di riferimento non dispone di questo attributo, lo strumento di associazione genererà un controllo del valore assegnato prima di passarlo a Objective-C e genererà un controllo che genererà un'operazione `ArgumentNullException` se il valore assegnato è `null` .

Ad esempio:

```csharp
// In properties

[NullAllowed]
UIImage IconFile { get; set; }

// In methods
void SetImage ([NullAllowed] UIImage image, State forState);
```

<a name="OverrideAttribute"></a>

### <a name="overrideattribute"></a>OverrideAttribute

Utilizzare questo attributo per indicare al generatore di associazioni che l'associazione per questo particolare metodo deve essere contrassegnata con una `override` parola chiave.

### <a name="presnippetattribute"></a>PreSnippetAttribute

È possibile utilizzare questo attributo per inserire parte del codice da inserire dopo che i parametri di input sono stati convalidati, ma prima che il codice venga chiamato in Objective-C.

Esempio:

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

È possibile utilizzare questo attributo per inserire il codice da inserire prima che uno qualsiasi dei parametri venga convalidato nel metodo generato.

Esempio:

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>PostGetAttribute

Indica al generatore di associazioni di richiamare la proprietà specificata da questa classe per recuperare un valore.

Questa proprietà viene in genere utilizzata per aggiornare la cache che punta a oggetti di riferimento che mantengono l'oggetto grafico a cui si fa riferimento. In genere viene visualizzato nel codice con operazioni come Aggiungi/Rimuovi. Questo metodo viene utilizzato in modo che dopo l'aggiunta o la rimozione di elementi la cache interna venga aggiornata per garantire che vengano mantenuti i riferimenti gestiti agli oggetti effettivamente in uso. Questo è possibile perché lo strumento di associazione genera un campo sottostante per tutti gli oggetti di riferimento in una determinata associazione.

Esempio:

```csharp
[BaseType (typeof (NSObject))]
[Since (4,0)]
public interface NSOperation {
    [Export ("addDependency:")][PostGet ("Dependencies")]
    void AddDependency (NSOperation op);

    [Export ("removeDependency:")][PostGet ("Dependencies")]
    void RemoveDependency (NSOperation op);

    [Export ("dependencies")]
    NSOperation [] Dependencies { get; }
}
```

In questo caso, la `Dependencies` proprietà verrà richiamata dopo l'aggiunta o la rimozione di dipendenze dall' `NSOperation` oggetto, assicurando che sia presente un grafico che rappresenta gli oggetti caricati effettivi, impedendo le perdite di memoria e il danneggiamento della memoria.

### <a name="postsnippetattribute"></a>PostSnippetAttribute

È possibile usare questo attributo per inserire il codice sorgente C# da inserire dopo che il codice ha richiamato il metodo Objective-C sottostante

Esempio:

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>ProxyAttribute

Questo attributo viene applicato ai valori restituiti per contrassegnarli come oggetti proxy. Alcune API Objective-C restituiscono oggetti proxy che non possono essere distinti dalle associazioni utente. L'effetto di questo attributo consiste nel contrassegnare l'oggetto come un `DirectBinding` oggetto. Per uno scenario in Novell. Mac, è possibile vedere la [discussione su questo bug](https://bugzilla.novell.com/show_bug.cgi?id=670844).

### <a name="retainlistattribute"></a>RetainListAttribute

Indica al generatore di memorizzare un riferimento gestito al parametro o di rimuovere un riferimento interno al parametro. Viene usato per evitare che venga fatto riferimento a oggetti.

Sintassi:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Se il valore di `doAdd` è true, il parametro viene aggiunto a `__mt_{0}_var List<NSObject>;` . Dove `{0}` viene sostituito con l'oggetto specificato `listName` . È necessario dichiarare questo campo sottostante nella classe parziale complementare all'API.

Per un esempio, vedere [Foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) e [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute (Novell. iOS 6,0)

Questo può essere applicato ai tipi restituiti per indicare che il generatore deve chiamare `Release` sull'oggetto prima di restituirlo. Questa operazione è necessaria solo quando un metodo fornisce un oggetto mantenuto (in contrapposizione a un oggetto autorelease, che è lo scenario più comune)

Esempio:

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

Questo attributo viene inoltre propagato al codice generato, in modo che il runtime di Novell. iOS sappia che deve mantenere l'oggetto quando viene restituito a Objective-C da tale funzione.

### <a name="sealedattribute"></a>SealedAttribute

Indica al generatore di contrassegnare il metodo generato come sealed. Se questo attributo non viene specificato, l'impostazione predefinita consiste nel generare un metodo virtuale, ovvero un metodo virtuale, un metodo astratto o una sostituzione, a seconda della modalità di utilizzo di altri attributi.

<a name="StaticAttribute"></a>

### <a name="staticattribute"></a>StaticAttribute

Quando l' `[Static]` attributo viene applicato a un metodo o a una proprietà, viene generato un metodo o una proprietà statica. Se questo attributo non viene specificato, il generatore produce un metodo di istanza o una proprietà.

### <a name="transientattribute"></a>TransientAttribute

Usare questo attributo per contrassegnare le proprietà i cui valori sono temporanei, ovvero gli oggetti creati temporaneamente da iOS ma che non sono di lunga durata. Quando questo attributo viene applicato a una proprietà, il generatore non crea un campo sottostante per questa proprietà, il che significa che la classe gestita non mantiene un riferimento all'oggetto.

<a name="WrapAttribute"></a>

### <a name="wrapattribute"></a>WrapAttribute

Nella progettazione delle associazioni Novell. iOS/Novell. Mac, l' `[Wrap]` attributo viene usato per eseguire il wrapping di un oggetto tipizzato in modo debole con un oggetto fortemente tipizzato. Questa operazione viene eseguita principalmente con gli oggetti delegati Objective-C, generalmente dichiarati come di tipo `id` o `NSObject` . La convenzione usata da Novell. iOS e Novell. Mac consiste nell'esporre tali delegati o origini dati come di tipo `NSObject` e vengono denominati usando la convenzione "vulnerabile" + il nome esposto. Una `id delegate` proprietà di Objective-C verrebbe esposta come una `NSObject WeakDelegate { get; set; }` proprietà nel file di contratto dell'API.

Tuttavia, in genere, il valore assegnato a questo delegato è di un tipo sicuro, quindi viene esposto il tipo forte e viene applicato l' `[Wrap]` attributo, ovvero gli utenti possono scegliere di utilizzare i tipi deboli se hanno bisogno di un controllo accurato o se devono ricorrere a trucchi di basso livello oppure possono utilizzare la proprietà fortemente tipizzata per la maggior parte del lavoro.

Esempio:

```csharp
[BaseType (typeof (NSObject))]
interface Demo {
     [Export ("delegate"), NullAllowed]
     NSObject WeakDelegate { get; set; }

     [Wrap ("WeakDelegate")]
     DemoDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
interface DemoDelegate {
    [Export ("doDemo")]
    void DoDemo ();
}
```

Questo è il modo in cui l'utente utilizzerà la versione con tipizzazione debole del delegato:

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

Questo è il modo in cui l'utente utilizzerà la versione fortemente tipizzata. si noti che l'utente utilizza il sistema di tipi di C# e utilizza la parola chiave override per dichiarare il suo scopo e che non deve decorare manualmente il metodo con `[Export]` , dal momento che il lavoro è stato eseguito nell'associazione per l'utente:

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

Un altro utilizzo dell' `[Wrap]` attributo consiste nel supportare la versione fortemente tipizzata dei metodi.  Ad esempio:

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Quando l' `[Wrap]` attributo viene applicato a un metodo all'interno di un tipo decorato con un `[Category]` attributo, è necessario includere `This` come primo argomento perché è in corso la generazione di un metodo di estensione. Ad esempio:

```csharp
[Wrap ("Write (This, image, options?.Dictionary, out error)")]
bool Write (CIImage image, CIImageRepresentationOptions options, out NSError error);
```

I membri generati da `[Wrap]` non sono `virtual` per impostazione predefinita, se è necessario un `virtual` membro che è possibile impostare `true` sul `isVirtual` parametro facoltativo.

```csharp
[BaseType (typeof (NSObject))]
interface FooExplorer {
    [Export ("fooWithContentsOfURL:")]
    void FromUrl (NSUrl url);

    [Wrap ("FromUrl (NSUrl.FromString (url))", isVirtual: true)]
    void FromUrl (string url);
}
```

`[Wrap]`può anche essere usato direttamente nei metodi get e setter della proprietà.
In questo modo è possibile avere il controllo completo su di essi e modificare il codice in base alle esigenze.
Si consideri, ad esempio, la definizione API seguente che usa le enumerazioni intelligenti:

```csharp
// Smart enum.
enum PersonRelationship {
        [Field (null)]
        None,

        [Field ("FMFather", "__Internal")]
        Father,

        [Field ("FMMother", "__Internal")]
        Mother
}
```

Definizione dell'interfaccia:

```csharp
// Property definition.

[Export ("presenceType")]
NSString _PresenceType { get; set; }

PersonRelationship PresenceType {
    [Wrap ("PersonRelationshipExtensions.GetValue (_PresenceType)")]
    get;
    [Wrap ("_PresenceType = value.GetConstant ()")]
    set;
}
```

## <a name="parameter-attributes"></a>Attributi di parametro

In questa sezione vengono descritti gli attributi che è possibile applicare ai parametri in una definizione di metodo e l'oggetto `[NullAttribute]` applicabile a una proprietà nel suo complesso.

<a name="BlockCallback"></a>

### <a name="blockcallback"></a>BlockCallback

Questo attributo viene applicato ai tipi di parametro nelle dichiarazioni di delegati C# per notificare al gestore di associazione che il parametro in questione è conforme alla convenzione di chiamata del blocco Objective-C e deve effettuare il marshalling in questo modo.

Viene in genere usato per i callback definiti come segue in Objective-C:

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Vedere anche: [CCallback](#CCallback).

<a name="CCallback"></a>

### <a name="ccallback"></a>CCallback

Questo attributo viene applicato ai tipi di parametro nelle dichiarazioni di delegati C# per notificare al gestore di associazione che il parametro in questione è conforme alla convenzione di chiamata del puntatore a funzione C ABI e deve effettuare il marshalling in questo modo.

Viene in genere usato per i callback definiti come segue in Objective-C:

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Vedere anche: [BlockCallback](#BlockCallback).

### <a name="params"></a>Parametri

È possibile usare l' `[Params]` attributo nell'ultimo parametro di matrice di una definizione di metodo per fare in modo che il generatore inserisca un "params" nella definizione.   Ciò consente al binding di consentire facilmente i parametri facoltativi.

Ad esempio, la definizione seguente:

```csharp
[Export ("loadFiles:")]
void LoadFiles ([Params]NSUrl [] files);
```

Consente di scrivere il codice seguente:

```csharp
foo.LoadFiles (new NSUrl (url));
foo.LoadFiles (new NSUrl (url1), new NSUrl (url2), new NSUrl (url3));
```

Ciò offre il vantaggio aggiuntivo che non richiede agli utenti di creare una matrice esclusivamente per il passaggio di elementi.

<a name="plainstring"></a>

### <a name="plainstring"></a>PlainString

È possibile usare l' `[PlainString]` attributo davanti ai parametri di stringa per indicare al generatore di associazioni di passare la stringa come stringa C, anziché passare il parametro come `NSString` .

La maggior parte delle API Objective-C usa `NSString` parametri, ma alcune API espongono un' `char *` API per il passaggio di stringhe anziché la `NSString` variazione.
Utilizzare `[PlainString]` in tali casi.

Ad esempio, le dichiarazioni Objective-C seguenti:

```csharp
- (void) setText: (NSString *) theText;
- (void) logMessage: (char *) message;
```

Deve essere associato come segue:

```csharp
[Export ("setText:")]
void SetText (string theText);

[Export ("logMessage:")]
void LogMessage ([PlainString] string theText);
```

### <a name="retainattribute"></a>RetainAttribute

Indica al generatore di memorizzare un riferimento al parametro specificato. Il generatore fornirà l'archivio di backup per questo campo oppure è possibile specificare un nome (il `WrapName` ) in cui archiviare il valore. Questa operazione è utile per contenere un riferimento a un oggetto gestito che viene passato come parametro a Objective-C e quando si è certi che in Objective-C verrà mantenuta solo questa copia dell'oggetto. Ad esempio, un'API come `SetDisplay (SomeObject)` utilizzerebbe questo attributo, in quanto è probabile che la visualizzazione di un oggetto venga visualizzata solo un oggetto alla volta. Se è necessario tenere traccia di più di un oggetto (ad esempio, per un'API simile a uno stack) si userà l' `[RetainList]` attributo.

Sintassi:

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```

### <a name="retainlistattribute"></a>RetainListAttribute

Indica al generatore di memorizzare un riferimento gestito al parametro o di rimuovere un riferimento interno al parametro. Viene usato per evitare che venga fatto riferimento a oggetti.

Sintassi:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Se il valore di `doAdd` è true, il parametro viene aggiunto a `__mt_{0}_var List<NSObject>` . Dove `{0}` viene sostituito con l'oggetto specificato `listName` . È necessario dichiarare questo campo sottostante nella classe parziale complementare all'API.

Per un esempio, vedere [Foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) e [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="transientattribute"></a>TransientAttribute

Questo attributo viene applicato ai parametri e viene usato solo per la transizione da Objective-C a C#.  Durante tali transizioni, i vari parametri Objective-C `NSObject` vengono racchiusi in una rappresentazione gestita dell'oggetto.

Il runtime accetta un riferimento all'oggetto nativo e mantiene il riferimento finché l'ultimo riferimento gestito all'oggetto non è più presente e il GC ha la possibilità di essere eseguito.

In alcuni casi è importante che il runtime C# non mantenga un riferimento all'oggetto nativo.  Questa situazione si verifica a volte quando il codice nativo sottostante ha associato un comportamento speciale al ciclo di vita del parametro.  Ad esempio: il distruttore per il parametro eseguirà un'azione di pulizia o disporrà di una risorsa preziosa.

Questo attributo informa il runtime che si desidera che l'oggetto venga eliminato, se possibile, quando si ritorna a Objective-C dal metodo sovrascritto.

La regola è semplice: se il runtime ha dovuto creare una nuova rappresentazione gestita dall'oggetto nativo, alla fine della funzione, il numero di conservazioni per l'oggetto nativo verrà eliminato e la proprietà handle dell'oggetto gestito verrà cancellata.   Ciò significa che se si conserva un riferimento all'oggetto gestito, tale riferimento diventerà inutile (la chiamata di metodi su di essa genererà un'eccezione).

Se l'oggetto passato non è stato creato o se è già presente una rappresentazione gestita in attesa dell'oggetto, l'eliminazione forzata non viene eseguita. 

## <a name="property-attributes"></a>Attributi proprietà

<a name="NotImplementedAttribute"></a>

### <a name="notimplementedattribute"></a>NotImplementedAttribute

Questo attributo viene usato per supportare un idioma Objective-C in cui una proprietà con un getter viene introdotta in una classe di base e una sottoclasse modificabile introduce un setter.

Poiché C# non supporta questo modello, la classe base deve avere sia Setter che getter e una sottoclasse può usare [OverrideAttribute](#OverrideAttribute).

Questo attributo viene usato solo nei setter di proprietà e viene usato per supportare l'idioma modificabile in Objective-C.

Esempio:

```csharp
[BaseType (typeof (NSObject))]
interface MyString {
    [Export ("initWithValue:")]
    IntPtr Constructor (string value);

    [Export ("value")]
    string Value {
        get;

    [NotImplemented ("Not available on MyString, use MyMutableString to set")]
        set;
    }
}

[BaseType (typeof (MyString))]
interface MyMutableString {
    [Export ("value")]
    [Override]
    string Value { get; set; }
}
```

<a name="enum-attributes"></a>

## <a name="enum-attributes"></a>Attributi enum

Il mapping delle `NSString` costanti ai valori enum è un modo semplice per creare un'API .NET migliore. È

* consente al completamento del codice di essere più utile, mostrando **solo** i valori corretti per l'API;
* aggiunge l'indipendenza dai tipi, non è possibile usare un'altra `NSString` costante in un contesto errato; e
* consente di nascondere alcune costanti, facendo in modo che il completamento del codice mostri un elenco di API più breve senza perdere la funzionalità.

Esempio:

```csharp
enum NSRunLoopMode {

    [DefaultEnumValue]
    [Field ("NSDefaultRunLoopMode")]
    Default,

    [Field ("NSRunLoopCommonModes")]
    Common,

    [Field (null)]
    Other = 1000
}
```

Dalla definizione di associazione precedente il generatore creerà l'oggetto `enum` stesso e creerà anche un `*Extensions` tipo statico che include metodi di conversione bidirezionali tra i valori enum e le `NSString` costanti. Ciò significa che le costanti restano disponibili per gli sviluppatori anche se non fanno parte dell'API.

Esempi:

```csharp
// using the NSString constant in a different API / framework / 3rd party code
CallApiRequiringAnNSString (NSRunLoopMode.Default.GetConstant ());
```

```csharp
// converting the constants from a different API / framework / 3rd party code
var constant = CallApiReturningAnNSString ();
// back into an enum value
CallApiWithEnum (NSRunLoopModeExtensions.GetValue (constant));
```

### <a name="defaultenumvalueattribute"></a>DefaultEnumValueAttribute

Con questo attributo è possibile decorare **un** valore enum. Questa diventerà la costante restituita se il valore enum non è noto.

Dall'esempio precedente:

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

Se nessun valore enum è decorato `NotSupportedException` , verrà generata un'eccezione.

### <a name="errordomainattribute"></a>ErrorDomainAttribute

I codici di errore vengono associati come valori enum. In genere è presente un dominio di errore e non è sempre facile trovare quello che si applica (o se ne esiste già uno).

È possibile utilizzare questo attributo per associare il dominio di errore all'enumerazione stessa.

Esempio:

```csharp
[Native]
[ErrorDomain ("AVKitErrorDomain")]
public enum AVKitError : nint {
    None = 0,
    Unknown = -1000,
    PictureInPictureStartFailed = -1001
}
```

È quindi possibile chiamare il metodo `GetDomain` di estensione per ottenere la costante del dominio di qualsiasi errore.

### <a name="fieldattribute"></a>FieldAttribute

Si tratta dello stesso `[Field]` attributo usato per le costanti all'interno del tipo. Può anche essere usato all'interno di enumerazioni per eseguire il mapping di un valore con una costante specifica.

Un `null` valore può essere usato per specificare quale valore di enumerazione deve essere restituito se `null` `NSString` viene specificata una costante.

Dall'esempio precedente:

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

Se non `null` è presente alcun valore `ArgumentNullException` , verrà generata un'eccezione.

## <a name="global-attributes"></a>Attributi globali

Gli attributi globali vengono applicati usando il `[assembly:]` modificatore di attributo [`[LinkWithAttribute]`](#LinkWithAttribute) , ad esempio o, possono essere usati ovunque, ad esempio gli [`[Lion]`](#SinceAndLionAttributes) [`[Since]`](#SinceAndLionAttributes) attributi e.

<a name="LinkWithAttribute"></a>

### <a name="linkwithattribute"></a>LinkWithAttribute

Si tratta di un attributo a livello di assembly che consente agli sviluppatori di specificare i flag di collegamento necessari per riutilizzare una libreria associata senza imporre al consumer della libreria di configurare manualmente la gcc_flags e gli argomenti mTouch aggiuntivi passati a una raccolta.

Sintassi:

```csharp
// In properties
[Flags]
public enum LinkTarget {
    Simulator    = 1,
    ArmV6    = 2,
    ArmV7    = 4,
    Thumb    = 8,
}

[AttributeUsage(AttributeTargets.Assembly, AllowMultiple=true)]
public class LinkWithAttribute : Attribute {
    public LinkWithAttribute ();
    public LinkWithAttribute (string libraryName);
    public LinkWithAttribute (string libraryName, LinkTarget target);
    public LinkWithAttribute (string libraryName, LinkTarget target, string linkerFlags);
    public bool ForceLoad { get; set; }
    public string Frameworks { get; set; }
    public bool IsCxx { get; set;  }
    public string LibraryName { get; }
    public string LinkerFlags { get; set; }
    public LinkTarget LinkTarget { get; set; }
    public bool NeedsGccExceptionHandling { get; set; }
    public bool SmartLink { get; set; }
    public string WeakFrameworks { get; set; }
}
```

Questo attributo viene applicato a livello di assembly, ad esempio ciò che viene usato dalle [associazioni CorePlot](https://github.com/mono/monotouch-bindings/tree/master/CorePlot) :

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

Quando si utilizza l' `[LinkWith]` attributo, l'oggetto specificato `libraryName` viene incorporato nell'assembly risultante, consentendo agli utenti di fornire una singola DLL che contiene sia le dipendenze non gestite sia i flag della riga di comando necessari per utilizzare correttamente la libreria da Novell. iOS.

È anche possibile non fornire un oggetto `libraryName` , nel qual caso l' `LinkWith` attributo può essere usato solo per specificare flag del linker aggiuntivi:

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>Costruttori LinkWithAttribute

Questi costruttori consentono di specificare la libreria da collegare e incorporare nell'assembly risultante, le destinazioni supportate supportate dalla libreria e gli eventuali flag di libreria facoltativi necessari per il collegamento alla libreria.

Si noti che l' `LinkTarget` argomento viene dedotto da Novell. iOS e non è necessario impostarlo.

Esempi:

```csharp
// Specify additional linker:
[assembly: LinkWith (LinkerFlags = "-sqlite3")]

// Specify library name for the constructor:
[assembly: LinkWith ("libDemo.a");

// Specify library name, and link target for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator);

// Specify only the library name, link target and linker flags for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator, SmartLink = true, ForceLoad = true, IsCxx = true);
```

#### <a name="linkwithattributeforceload"></a>LinkWithAttribute.ForceLoad

La `ForceLoad` proprietà viene utilizzata per decidere se utilizzare o meno il `-force_load` flag di collegamento per collegare la libreria nativa. Per il momento, deve essere sempre true.

#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute. Frameworks

Se la libreria associata ha un requisito rigido per tutti i Framework (diversi da `Foundation` e `UIKit` ), è necessario impostare la `Frameworks` proprietà su una stringa contenente un elenco delimitato da spazi dei framework di piattaforma richiesti. Se ad esempio si associa una libreria che richiede `CoreGraphics` e `CoreText` , impostare la `Frameworks` proprietà su `"CoreGraphics CoreText"` .

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

Impostare questa proprietà su true se l'eseguibile risultante deve essere compilato utilizzando un compilatore C++ anziché quello predefinito, ovvero un compilatore C. Usare questa se la libreria che si sta associando è stata scritta in C++.

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute. libraryname

Nome della libreria non gestita da aggregare. Si tratta di un file con estensione ". a" e può contenere codice oggetto per più piattaforme (ad esempio, ARM e x86 per il simulatore).

Le versioni precedenti di Novell. iOS controllavano la `LinkTarget` proprietà per determinare la piattaforma supportata dalla libreria, ma ora è stata rilevata automaticamente e la `LinkTarget` proprietà viene ignorata.

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

La `LinkerFlags` stringa consente agli autori di associazioni di specificare eventuali flag aggiuntivi del linker necessari per collegare la libreria nativa all'applicazione.

Ad esempio, se la libreria nativa richiede libxml2 e zlib, impostare la `LinkerFlags` stringa su `"-lxml2 -lz"` .

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute. LinkTarget

Le versioni precedenti di Novell. iOS controllavano la `LinkTarget` proprietà per determinare la piattaforma supportata dalla libreria, ma ora è stata rilevata automaticamente e la `LinkTarget` proprietà viene ignorata.

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

Impostare questa proprietà su true se la libreria da collegare richiede la libreria di gestione delle eccezioni GCC (gcc_eh)

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute. SmartLink

La `SmartLink` proprietà deve essere impostata su true per consentire a Novell. iOS di determinare se `ForceLoad` è obbligatorio o meno.

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

La `WeakFrameworks` proprietà funziona allo stesso modo della `Frameworks` proprietà, ad eccezione del fatto che in fase di collegamento l' `-weak_framework` identificatore viene passato a GCC per ognuno dei Framework elencati.

`WeakFrameworks`consente alle librerie e alle applicazioni di collegarsi in modo debole ai Framework della piattaforma, in modo che possano utilizzarli facoltativamente se sono disponibili, ma non hanno una dipendenza rigida, che risulta utile se la libreria ha lo scopo di aggiungere altre funzionalità nelle versioni più recenti di iOS. Per ulteriori informazioni sul collegamento debole, vedere la documentazione di Apple sul [collegamento debole](https://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html).

I candidati idonei per il collegamento debole sono `Frameworks` come gli account,,, `CoreBluetooth` `CoreImage` `GLKit` `NewsstandKit` e `Twitter` perché sono disponibili solo in iOS 5.

<a name="SinceAndLionAttributes"></a>

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute (iOS) e LionAttribute (macOS)

Usare l' `[Since]` attributo per contrassegnare le API come se fossero state introdotte in un determinato momento. L'attributo deve essere usato solo per contrassegnare tipi e metodi che possono causare un problema di runtime se la classe, il metodo o la proprietà sottostante non è disponibile.

Sintassi:

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

In generale, non dovrebbe essere applicato a enumerazioni, vincoli o nuove strutture, perché non generano un errore di runtime se vengono eseguite in un dispositivo con una versione precedente del sistema operativo.

Esempio quando applicato a un tipo:

```csharp
// Type introduced with iOS 4.2
[Since (4,2)]
[BaseType (typeof (UIPrintFormatter))]
interface UIViewPrintFormatter {
    [Export ("view")]
    UIView View { get; }
}
```

Esempio applicato a un nuovo membro:

```csharp
[BaseType (typeof (UIViewController))]
public interface UITableViewController {
    [Export ("tableView", ArgumentSemantic.Retain)]
    UITableView TableView { get; set; }

    [Since (3,2)]
    [Export ("clearsSelectionOnViewWillAppear")]
    bool ClearsSelectionOnViewWillAppear { get; set; }
```

L' `[Lion]` attributo viene applicato nello stesso modo, ma per i tipi introdotti con Lion. Il motivo per usare `[Lion]` rispetto al numero di versione più specifico usato in iOS è che iOS viene rivisto molto spesso, mentre le versioni principali del sistema operativo vengono rilasciate raramente ed è più facile ricordare il sistema operativo in base al nome in base al numero di versione

### <a name="adviceattribute"></a>AdviceAttribute

Usare questo attributo per fornire agli sviluppatori un suggerimento su altre API che potrebbero essere più convenienti da usare.   Se, ad esempio, si specifica una versione fortemente tipizzata di un'API, è possibile usare questo attributo sull'attributo debolmente tipizzato per indirizzare lo sviluppatore all'API migliore.

Le informazioni di questo attributo sono illustrate nella documentazione e gli strumenti possono essere sviluppati per fornire suggerimenti agli utenti su come migliorare

### <a name="requiressuperattribute"></a>RequiresSuperAttribute

Si tratta di una sottoclasse specializzata dell' `[Advice]` attributo che può essere usata per suggerire allo sviluppatore che l'override di un metodo **richiede** una chiamata al metodo di base (sottoposto a override).

Corrisponde a `clang`[`__attribute__((objc_requires_super))`](https://clang.llvm.org/docs/AttributeReference.html#objc-requires-super)

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

Disponibile solo in Novell. iOS 5,4 e versioni successive.

Questo attributo indica al generatore che l'associazione per la libreria specifica (se applicata con `[assembly:]` ) o il tipo deve usare il marshalling della stringa di copia zero veloce. Questo attributo equivale a passare l'opzione della riga `--zero-copy` di comando al generatore.

Quando si usa la copia zero per le stringhe, il generatore USA efficacemente la stessa stringa C# della stringa usata da Objective-C senza incorrere nella creazione di un nuovo `NSString` oggetto e evitando la copia dei dati dalle stringhe c# alla stringa Objective-c. L'unico svantaggio dell'uso di zero stringhe di copia è che è necessario assicurarsi che qualsiasi proprietà di stringa di cui si esegue il wrapping venga contrassegnata come `retain` o `copy` abbia l' `[DisableZeroCopy]` attributo impostato. Questa operazione è necessaria perché l'handle per le stringhe di copia zero viene allocato nello stack e non è valido al momento della restituzione della funzione.

Esempio:

```csharp
[ZeroCopyStrings]
[BaseType (typeof (NSObject))]
interface MyBinding {
    [Export ("name")]
    string Name { get; set; }

    [Export ("domain"), NullAllowed]
    string Domain { get; set; }

    [DisablZeroCopy]
    [Export ("someRetainedNSString")]
    string RetainedProperty { get; set; }
}

```

È anche possibile applicare l'attributo a livello di assembly, che verrà applicato a tutti i tipi di assembly:

```csharp
[assembly:ZeroCopyStrings]
```

## <a name="strongly-typed-dictionaries"></a>Dizionari fortemente tipizzati

Con Novell. iOS 8,0 è stato introdotto il supporto per creare con facilità classi fortemente tipizzate che esegue il wrapping `NSDictionaries` .

Sebbene sia sempre stato possibile usare il tipo di dati [DictionaryContainer](xref:Foundation.DictionaryContainer) insieme a un'API manuale, ora è molto più semplice eseguire questa operazione.  Per altre informazioni, vedere [emersione dei tipi forti](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types).

<a name="StrongDictionary"></a>

### <a name="strongdictionary"></a>StrongDictionary

Quando questo attributo viene applicato a un'interfaccia, il generatore produrrà una classe con lo stesso nome dell'interfaccia che deriva da [DictionaryContainer](xref:Foundation.DictionaryContainer) e converte ogni proprietà definita nell'interfaccia in un getter e un setter fortemente tipizzati per il dizionario.

Viene generata automaticamente una classe di cui è possibile creare un'istanza da un esistente `NSDictionary` o che è stato creato nuovo.

Questo attributo accetta un parametro, ovvero il nome della classe contenente le chiavi usate per accedere agli elementi nel dizionario.   Per impostazione predefinita, ogni proprietà nell'interfaccia con l'attributo effettuerà la ricerca di un membro del tipo specificato per un nome con il suffisso "Key".

Ad esempio:

```csharp
[StrongDictionary ("MyOptionKeys")]
interface MyOption {
    string Name { get; set; }
    nint    Age  { get; set; }
}

[Static]
interface MyOptionKeys {
    // In Objective-C this is "NSString *MYOptionNameKey;"
    [Field ("MYOptionNameKey")]
    NSString NameKey { get; }

    // In Objective-C this is "NSString *MYOptionAgeKey;"
    [Field ("MYOptionAgeKey")]
    NSString AgeKey { get; }
}

```

Nel caso precedente, la `MyOption` classe produrrà una proprietà di stringa per `Name` che utilizzerà `MyOptionKeys.NameKey` come chiave nel dizionario per recuperare una stringa.   E utilizzeranno `MyOptionKeys.AgeKey` come chiave nel dizionario per recuperare un oggetto `NSNumber` che contiene un valore int.

Se si vuole usare una chiave diversa, è possibile usare l'attributo Export nella proprietà, ad esempio:

```csharp
[StrongDictionary ("MyColoringKeys")]
interface MyColoringOptions {
    [Export ("TheName")]  // Override the default which would be NameKey
    string Name { get; set; }

    [Export ("TheAge")] // Override the default which would be AgeKey
    nint    Age  { get; set; }
}

[Static]
interface MyColoringKeys {
    // In Objective-C this is "NSString *MYColoringNameKey"
    [Field ("MYColoringNameKey")]
    NSString TheName { get; }

    // In Objective-C this is "NSString *MYColoringAgeKey"
    [Field ("MYColoringAgeKey")]
    NSString TheAge { get; }
}
```

#### <a name="strong-dictionary-types"></a>Tipi di dizionario forti

Nella definizione sono supportati i tipi di dati seguenti `StrongDictionary` :

|Tipo di interfaccia C#|`NSDictionary`Tipo di archiviazione|
|---|---|
|`bool`|`Boolean`Archiviato in un`NSNumber`|
|Valori di enumerazione|Integer archiviato in un`NSNumber`|
|`int`|intero a 32 bit archiviato in un`NSNumber`|
|`uint`|Unsigned Integer a 32 bit archiviati in un`NSNumber`|
|`nint`|`NSInteger`Archiviato in un`NSNumber`|
|`nuint`|`NSUInteger`Archiviato in un`NSNumber`|
|`long`|intero a 64 bit archiviato in un`NSNumber`|
|`float`|intero a 32 bit archiviato come`NSNumber`|
|`double`|intero a 64 bit archiviato come`NSNumber`|
|`NSObject`e sottoclassi|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C# `Array` di`NSObject`|`NSArray`|
|C# `Array` di enumerazioni|`NSArray``NSNumber`valori contenenti|
