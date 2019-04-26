---
title: Guida di riferimento per i tipi di associazione
description: Questa Guida di riferimento descrive vari attributi e i concetti necessari per comprendere durante la creazione di C# binding alle librerie Objective-C.
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
author: conceptdev
ms.author: crdun
ms.date: 03/06/2018
ms.openlocfilehash: d460bf867ce09e614be76d0a4a7ffef01420cf82
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61266505"
---
# <a name="binding-types-reference-guide"></a>Guida di riferimento per i tipi di associazione

Questo documento descrive l'elenco di attributi che è possibile usare per annotare i file di contratto API per guidare l'associazione e il codice generato

I contratti API xamarin. Mac e xamarin. IOS sono scritti in C# per lo più come definizioni di interfaccia che definiscono il modo in cui il codice Objective-C verrà rese disponibili in C#. Il processo include una combinazione di dichiarazioni di interfaccia più alcune definizioni del tipo di base che potrebbe richiedere il contratto API. Per un'introduzione ai tipi di associazione, vedere la Guida complementare [librerie Objective-C dell'associazione](~/cross-platform/macios/binding/objective-c-libraries.md).

## <a name="type-definitions"></a>Definizioni di tipo

Sintassi:

```csharp
[BaseType (typeof (BTYPE))
interface MyType [: Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

Ogni interfaccia nella definizione del contratto con il [ `[BaseType]` ](#BaseTypeAttribute) attributo che dichiara il tipo di base per l'oggetto generato. Nella dichiarazione precedente un `MyType` classe C# verrà generato il tipo che viene associato a un tipo di Objective-C chiamato `MyType`.

Se si specifica alcun tipo dopo il typename (nell'esempio precedente `Protocol1` e `Protocol2`) utilizzando la sintassi di ereditarietà dell'interfaccia il contenuto di tali interfacce verrà resa inline come se fossero stati parte del contratto per `MyType`.
Il modo in cui le superfici di xamarin. IOS che un tipo adotta un protocollo è per l'incorporamento tutti i metodi e proprietà che sono state dichiarate nella specifica del protocollo nel tipo stesso.

Il seguente viene illustrato come la dichiarazione di Objective-C per `UITextField` viene definita in un contratto di xamarin. ios:

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

Potrebbe essere scritto così come un C# contratto API:

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

È possibile controllare molti altri aspetti della generazione del codice dall'applicazione di altri attributi per l'interfaccia nonché la configurazione di [ `[BaseType]` ](#BaseTypeAttribute) attributo.


### <a name="generating-events"></a>La generazione di eventi

Una funzionalità di progettazione API xamarin. Mac e xamarin. IOS è che è eseguire il mapping di classi di delegati di Objective-C come C# eventi e callback. Gli utenti possono scegliere in ogni istanza se si desidera adottare il modello di programmazione di Objective-C, tramite l'assegnazione alle proprietà, ad esempio `Delegate` un'istanza di una classe che implementa i vari metodi per chiama il runtime di Objective-C, o da scelta di C#-gli eventi e le proprietà di stile.

Possiamo vedere un esempio di come usare il modello di Objective-C:

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

Nell'esempio precedente, è possibile vedere che abbiamo deciso di sovrascrivere i due metodi, una notifica che un evento di scorrimento ha avuto luogo, mentre la seconda che sia un callback che deve restituire un valore booleano che indica il `scrollView` se è necessario scorrere per il primi o No.

Il C# modello consente all'utente della libreria ascoltare le notifiche tramite il C# sintassi di evento o la sintassi delle proprietà per associare i callback che devono restituire valori.

Questo è il modo in C# per la stessa funzionalità è simile a espressioni lambda tramite codice:

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

Poiché gli eventi non restituiscono valori (hanno un tipo restituito void) è possibile connettere più copie. Il `ShouldScrollToTop` non è un evento, è invece una proprietà con il tipo `UIScrollViewCondition` con questa firma:

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

Restituisce un `bool` valore, in questo caso la sintassi lambda consente di restituire solo il valore di `MakeDecision` (funzione).

Il generatore di associazione supporta la generazione di eventi e proprietà che si collegano una classe come `UIScrollView` con relativo `UIScrollViewDelegate` (anche detti la classe del modello), questa operazione viene eseguita annotando il [ `[BaseType]` ](#BaseTypeAttribute) definizione con la `Events` e `Delegates` parametri (come descritti di seguito). Oltre ad annotare il [ `[BaseType]` ](#BaseTypeAttribute) con quei parametri è necessario informare il generatore di alcuni altri componenti.

Per gli eventi che accettano più parametri (in Objective-C la convenzione è che il primo parametro in una classe delegata è l'istanza dell'oggetto mittente) è necessario fornire il nome desiderato per generato `EventArgs` classe venga. Questa operazione viene eseguita con il [ `[EventArgs]` ](#EventArgsAttribute) attributo nella dichiarazione del metodo della classe modello. Ad esempio:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

La dichiarazione precedente genererà una `UIImagePickerImagePickedEventArgs` classe che deriva da `EventArgs` Pack entrambi i parametri e il `UIImage` e il `NSDictionary`. Il generatore produce questo:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Quindi espone le operazioni seguenti nel `UIImagePickerController` classe:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

Modello i metodi che restituiscono un valore vengono associati in modo diverso. Tali criteri richiedono sia un nome per il generato C# delegato (la firma del metodo) e anche un valore predefinito da restituire nel caso in cui l'utente non fornisce un'implementazione se stesso. Ad esempio, il `ShouldScrollToTop` definizione è questo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

Il codice precedente creerà un `UIScrollViewCondition` delegato con la firma che è stato illustrato in precedenza e se l'utente non fornisce un'implementazione, il valore restituito sarà true.

Oltre al [ `[DefaultValue]` ](#DefaultValueAttribute) attributo, è anche possibile usare i [ `[DefaultValueFromArgument]` ](#DefaultValueFromArgumentAttribute) attributo che indirizza il generatore per restituire il valore del parametro specificato nella chiamata o il [ `[NoDefaultValue]` ](#NoDefaultValueAttribute) parametro che indica il generatore che non vi è alcun valore predefinito.

<a name="BaseTypeAttribute" />

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

Si utilizza il `Name` proprietà per controllare il nome che verrà associato questo tipo al mondo di Objective-C. Ciò in genere viene usato per assegnare il C# digitare un nome che è conforme alle linee guida di progettazione di .NET Framework, ma che esegue il mapping a un nome in Objective-C che non segue questa convenzione.

Esempio, nel caso seguente è eseguire il mapping di Objective-C `NSURLConnection` tipo `NSUrlConnection`, come linee guida di progettazione di .NET Framework utilizzo "Url" anziché "URL":

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

Il nome specificato viene utilizzato come valore per il generato `[Register]` attributo nell'associazione. Se `Name` non è specificato, il nome del tipo short viene utilizzato come valore per il `[Register]` attributo nell'output generato.

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType.Events e BaseType.Delegates

Queste proprietà vengono usate per gestire la generazione di C#: applicare uno stile eventi nelle classi generate. Vengono utilizzati per collegare una determinata classe con la classe delegata Objective-C. Si verificheranno molti i casi in cui una classe Usa una classe delegata per inviare le notifiche e gli eventi. Ad esempio un `BarcodeScanner` avrebbe complementare `BardodeScannerDelegate` classe. Il `BarcodeScanner` classe può disporre di un `Delegate` proprietà che è necessario assegnare un'istanza di `BarcodeScannerDelegate` per, mentre questo funzionamento, si potrebbe voler esporre agli utenti un C#-come interfaccia di eventi di tipo e, in tali casi si utilizzerebbe il `Events` e `Delegates` delle proprietà delle [ `[BaseType]` ](#BaseTypeAttribute) attributo.

Queste proprietà vengono sempre impostate insieme e devono avere lo stesso numero di elementi e mantenute sincronizzati. Il `Delegates` matrice contiene una sola stringa per ogni delegato con tipizzazione debole che si desidera eseguire il wrapping, e il `Events` matrice contiene un tipo per ogni tipo che si desidera associare.

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


#### <a name="basetypekeeprefuntil"></a>BaseType.KeepRefUntil

Se si applica questo attributo quando vengono create nuove istanze di questa classe, l'istanza di tale oggetto verrà mantenuto intorno a fino il metodo fa riferimento il `KeepRefUntil` è stato richiamato. Ciò è utile per migliorare l'usabilità delle API, quando non si desidera mantenere un riferimento a un oggetto intorno a usare il codice all'utente. Il valore di questa proprietà è il nome di un metodo nel `Delegate` classe, pertanto è necessario usare questo in combinazione con il `Events` e `Delegates` anche le proprietà.

Nell'esempio seguente mostra come viene utilizzato da `UIActionSheet` in xamarin. ios:

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


### <a name="disabledefaultctorattribute"></a>DisableDefaultCtorAttribute

Quando questo attributo viene applicato alla definizione dell'interfaccia impedirà il generatore che produce il costruttore predefinito.

Usare questo attributo quando è necessario l'oggetto da inizializzare con uno degli altri costruttori nella classe.


### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

Quando questo attributo viene applicato alla definizione dell'interfaccia che contrassegna il costruttore predefinito come privato. Ciò significa che è possibile comunque creare un'istanza di oggetto di questa classe internamente dal file dell'estensione, ma semplicemente non essere accessibili agli utenti della classe.

<a name="CategoryAttribute" />

### <a name="categoryattribute"></a>CategoryAttribute

Usare questo attributo su una definizione di tipo per associare le categorie di Objective-C ed esporre come C# metodi di estensione per rispecchiare il modo Objective-C espone la funzionalità.

Le categorie sono un meccanismo di Objective-C utilizzato per estendere il set di metodi e le proprietà disponibili in una classe.   In pratica, vengono usati per estendere le funzionalità di una classe di base (ad esempio `NSObject`) quando è collegato un framework specifico nel (ad esempio `UIKit`), rendendo i relativi metodi disponibili, ma solo se è collegato al nuovo framework.   In alcuni casi, vengono usati per organizzare le funzionalità in una classe dalla funzionalità.   Sono simili come spirito al C# metodi di estensione.

Si tratta di una categoria aspetto in Objective-c:

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

Nell'esempio precedente viene trovato in una libreria che si estende anche istanze di `UIView` con il metodo `makeBackgroundRed`.

Per associare questi, è possibile usare la [ `[Category]` ](#CategoryAttribute) attributo in una definizione di interfaccia.   Quando si usa la [ `[Category]` ](#CategoryAttribute) dell'attributo, il significato del [ `[BaseType]` ](#BaseTypeAttribute) attributo cambia vengano utilizzati per specificare la classe di base per estendere, allo stato di tipo da estendere.

Il seguente viene illustrato come la `UIView` estensioni sono associate e trasformate in C# i metodi di estensione:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

Il codice precedente creerà un `MyUIViewExtension` una classe che contiene il `MakeBackgroundRed` metodo di estensione.   Ciò significa che è ora possibile chiamare `MakeBackgroundRed` su qualsiasi `UIView` sottoclasse, fornendo le stesse funzionalità si otterrebbe in Objective-C.

In alcuni casi troveranno **statici** membri all'interno di categorie, come illustrato nell'esempio seguente:

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

Ciò causerà un **errato** categoria C# definizione dell'interfaccia:

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

Non è corretto perché usare la `BoolMethod` estensione necessaria un'istanza di `FooObject` ma si associa un ObjC **statici** estensione, questo è un effetto collaterale dovuto al fatto di come C# vengono implementati i metodi di estensione .

L'unico modo per usare le definizioni di cui sopra è il seguente codice confusa:

```csharp
(null as FooObject).BoolMethod (range);
```

Per evitare questo problema si consiglia di rendere inline le `BoolMethod` definizione all'interno di `FooObject` definizione dell'interfaccia stessa, in questo modo sarà possibile chiamare questa estensione, ad esempio si tratta di `FooObject.BoolMethod (range)`.

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Microsoft emetterà un avviso (BI1117) ogni volta che è stata trovata una [ `[Static]` ](#StaticAttribute) membro all'interno di una [ `[Category]` ](#CategoryAttribute) definizione. Se si vuole poter disporre [ `[Static]` ](#StaticAttribute) membri all'interno di [ `[Category]` ](#CategoryAttribute) definizioni, è possibile ignorare l'avviso usando `[Category (allowStaticMembers: true)]` o tramite la decorazione di un membro o [ `[Category]` ](#CategoryAttribute) definizione con dell'interfaccia [ `[Internal]` ](#InternalAttribute).

<a name="StaticAttribute_Class" />

### <a name="staticattribute"></a>StaticAttribute

Quando questo attributo viene applicato a una classe appena genererà una classe statica, che non deriva da `NSObject`, in modo che il [ `[BaseType]` ](#BaseTypeAttribute) attributo viene ignorato. Le classi statiche vengono utilizzate per ospitare C variabili pubbliche che si desidera esporre.

Ad esempio:

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

Verrà generato un C# classe con l'API seguente:

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>Le definizioni di protocollo o modello

In genere vengono utilizzati modelli dall'implementazione del protocollo.
Si differenziano in quanto il runtime registrerà solo con Objective-C i metodi che effettivamente sono stati sovrascritti.
In caso contrario, il metodo non verranno registrato.

Ciò in genere significa che quando si crea una sottoclasse una classe che è stata contrassegnata con il `ModelAttribute`, non è necessario chiamare il metodo di base.   Una chiamata al metodo genererà un'eccezione, si dovrebbe per implementare il comportamento intero in una sottoclasse per tutti i metodi che si esegue l'override.

<a name="AbstractAttribute" />

### <a name="abstractattribute"></a>AbstractAttribute

Per impostazione predefinita, i membri che fanno parte di un protocollo non sono obbligatori. Ciò consente agli utenti di creare una sottoclasse del `Model` oggetto semplicemente derivando dalla classe C# ed eseguire l'override solo i metodi si preoccupi. In alcuni casi il contratto di Objective-C richiede che l'utente fornisce un'implementazione per questo metodo (quelli sono contrassegnati con il `@required` direttiva in Objective-C). In questi casi, è necessario contrassegnare i metodi con il `[Abstract]` attributo.

Il `[Abstract]` attributo può essere applicato a metodi o proprietà e fa sì che il generatore contrassegnare il membro generato come classe astratta e la classe da una classe astratta.

Di seguito viene eseguita da xamarin. ios:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UITableViewDataSource {
    [Export ("tableView:numberOfRowsInSection:")]
    [Abstract]
    nint RowsInSection (UITableView tableView, nint section);
}
```

<a name="DefaultValueAttribute" />

### <a name="defaultvalueattribute"></a>DefaultValueAttribute

Specifica il valore predefinito da restituire dal metodo modello se l'utente non è incluso un metodo per questo particolare metodo nell'oggetto Model

Sintassi:

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

Ad esempio, nella classe immaginaria delegato seguente per un `Camera` classe, offriamo una `ShouldUploadToServer` che verrà esposta come una proprietà nel `Camera` classe. Se l'utente del `Camera` classe non viene impostata in modo esplicito un valore da un'espressione lambda che può rispondere true o false, il valore predefinito restituito in questo caso sarebbe impostato su false, il valore specificato nel `DefaultValue` attributo:

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

Vedere anche: [ `[NoDefaultValue]` ](#NoDefaultValueAttribute), [ `[DefaultValueFromArgument]` ](#DefaultValueFromArgumentAttribute).

<a name="DefaultValueFromArgumentAttribute" />

### <a name="defaultvaluefromargumentattribute"></a>DefaultValueFromArgumentAttribute

Sintassi:

```csharp
public class DefaultValueFromArgumentAttribute : Attribute {
    public DefaultValueFromArgumentAttribute (string argument);
    public string Argument { get; }
}
```

Questo attributo quando viene fornito su un metodo che restituisce un valore in una classe di modello indicherà il generatore per restituire il valore del parametro specificato, se l'utente non ha fornito il proprio metodo o l'espressione lambda.

Esempio:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

In caso di precedente se l'utente del `NSAnimation` classe ha scelto di usare uno del C# proprietà/eventi e non sono state impostate `NSAnimation.ComputeAnimationCurve` a un metodo o l'espressione lambda, il valore restituito sarà il valore passato nel parametro di stato di avanzamento.

Vedere anche: [ `[NoDefaultValue]` ](#NoDefaultValueAttribute), [`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

In alcuni casi è opportuno non espongono un evento o delegare proprietà da una classe di modello nella classe host in modo che si aggiunge questo attributo indicherà il generatore per evitare la generazione di qualsiasi metodo decorato con esso.

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

Questo attributo viene usato nei metodi di modello che restituiscono valori per impostare il nome della firma del delegato da usare.

Esempio:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, float progress);
}
```

Con la definizione precedente, il generatore di genererà la seguente dichiarazione pubblica:

```csharp
public delegate float NSAnimationProgress (MonoMac.AppKit.NSAnimation animation, float progress);
```

### <a name="delegateapinameattribute"></a>DelegateApiNameAttribute

Questo attributo viene usato per consentire il generatore modificare il nome della proprietà generata nella classe dell'host. In alcuni casi è utile quando il nome del metodo della classe FooDelegate risulta utile per la classe Delegate, ma potrebbe sembrare strano nella classe host come proprietà.

Inoltre, questa è effettivamente utile (e necessari) quando si dispone di due o più metodi di overload appropriata per mantenerli denominato perché si trova nella classe FooDelegate ma si vuole esporli nella classe host con un nome specifico migliorato.

Esempio:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

Con la definizione precedente, il generatore di genererà la seguente dichiarazione pubblica nella classe dell'host:

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```

<a name="EventArgsAttribute" />

### <a name="eventargsattribute"></a>EventArgsAttribute

Per gli eventi che accettano più parametri (in Objective-C la convenzione è che il primo parametro in una classe delegata è l'istanza dell'oggetto mittente) è necessario fornire il nome desiderato per la classe EventArgs generata sia. Questa operazione viene eseguita con il `[EventArgs]` attributo nella dichiarazione del metodo nel `Model` classe.

Ad esempio:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

La dichiarazione precedente verrà generato un `UIImagePickerImagePickedEventArgs` classe che deriva da EventArgs e pacchetti di entrambi i parametri, il `UIImage` e il `NSDictionary`. Il generatore produce questo:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Quindi espone le operazioni seguenti nel `UIImagePickerController` classe:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```


### <a name="eventnameattribute"></a>EventNameAttribute

Questo attributo viene usato per consentire il generatore modificare il nome di un evento o una proprietà generata nella classe. In alcuni casi è utile quando il nome del metodo della classe modello risulta utile per la classe modello, ma potrebbe sembrare strano nella classe di origine come un evento o una proprietà.

Ad esempio, il `UIWebView` utilizza il seguente bit dal `UIWebViewDelegate`:

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

Il precedente espone `LoadingFinished` come metodo nel `UIWebViewDelegate`, ma `LoadFinished` come evento da associare fino a un `UIWebView`:

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute" />

### <a name="modelattribute"></a>ModelAttribute

Quando si applica il `[Model]` attributo a una definizione di tipo nel contratto di API, il runtime genererà codice speciale che segnalare solo le chiamate ai metodi nella classe se l'utente è stato sovrascritto da un metodo nella classe. Questo attributo viene in genere applicato a tutte le API che eseguono il wrapping di una classe delegata Objective-C.

<a name="NoDefaultValueAttribute" />

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

Specifica che il metodo per il modello non fornisce un valore restituito predefinito.

Questo codice funziona con il runtime di Objective-C rispondendo `false` alla richiesta di runtime di Objective-C per determinare se il selettore specificato viene implementato in questa classe.

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

Vedere anche: [ `[DefaultValue]` ](#DefaultValueAttribute), [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)  

<a name="ProtocolAttribute" />

## <a name="protocols"></a>Protocolli

Il concetto di protocollo Objective-C non esiste effettivamente C#. I protocolli sono simili a C# interfacce, ma sono diversi in quanto non tutti i metodi e proprietà dichiarate in un protocollo deve essere implementata dalla classe che adotta lo. Invece, alcune delle proprietà e metodi sono facoltativi.

Alcuni protocolli sono in genere utilizzati come le classi del modello, quelli deve essere associati usando il [ `[Model]` ](#ModelAttribute) attributo.

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

A partire da xamarin. IOS 7.0 un protocollo di nuovo e migliorato funzionalità di associazione è stata incorporata.  Qualsiasi definizione che contiene il `[Protocol]` attributo effettivamente genera tre classi di supporto che consentono di migliorare notevolmente la modalità che tu utilizzi protocolli:

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

Il **implementazione della classe** fornisce una classe astratta completata che è possibile eseguire l'override di singoli metodi di e ottenere l'indipendenza completa. Ma a causa dell'errore C# non supporta più ereditarietà, esistono scenari in cui si potrebbe richiedere un'altra classe base, ma si vuole comunque implementare un'interfaccia.

Si tratta di dove generato **definizione dell'interfaccia** è disponibile in.  È un'interfaccia che dispone di tutti i metodi richiesti dal protocollo.  In questo modo gli sviluppatori che vogliono implementare il protocollo per implementare semplicemente l'interfaccia.  Il runtime registrerà automaticamente il tipo come adottando il protocollo.

Notare che sono elencati i metodi necessari solo l'interfaccia e di esporre i metodi facoltativi.   Ciò significa che le classi che adottano il protocollo otterranno pieno controllo dei tipi per i metodi richiesti, ma saranno necessario ricorrere alla tipizzazione debole (manualmente utilizzando gli attributi di esportazione e la firma di corrispondenza) per i metodi di protocollo facoltativo.

Per rendere più pratico utilizzare un'API che usa i protocolli, lo strumento di associazione inoltre genererà una classe di metodo di estensioni che espone tutti i metodi facoltativi.   Ciò significa che fino a quando si utilizza un'API, sarà in grado di trattare i protocolli come se avessero tutti i metodi.

Se si desidera usare le definizioni di protocollo nell'API, è necessario scrivere scheletro interfacce vuote nella definizione di API.   Se si desidera utilizzare il Protocollo1 in un'API, è necessario eseguire questa operazione:

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

Il codice precedente è necessario perché in fase di associazione di `IMyProtocol` non esiste, vale a dire il motivo per cui è necessario fornire un'interfaccia vuota.

### <a name="adopting-protocol-generated-interfaces"></a>Adozione di interfacce generate protocollo

Ogni volta che si implementa una delle interfacce generate per i protocolli, simile al seguente:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

L'implementazione per i metodi di interfaccia automaticamente esportato con il nome appropriato, pertanto è equivalente a questa:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

Non è importante se l'interfaccia viene implementata in modo implicito o esplicito.

### <a name="protocol-inlining"></a>L'incorporamento di protocollo

Anche se si associano tipi Objective-C esistenti che sono stati dichiarati come adottando un protocollo, è opportuno inline di protocollo direttamente. A tale scopo, dichiarare semplicemente il protocollo come un'interfaccia senza alcuna [ `[BaseType]` ](#BaseTypeAttribute) attributo ed elencare il protocollo nell'elenco delle interfacce di base per l'interfaccia.

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

Vengono applicati gli attributi in questa sezione a singoli membri di un tipo: proprietà e le dichiarazioni di metodo.


### <a name="alignattribute"></a>AlignAttribute

Consente di specificare il valore di allineamento per i tipi restituiti di proprietà. Alcune proprietà accettano puntatori agli indirizzi che devono essere allineati a determinati limiti (in questo caso, ad esempio con alcune xamarin. IOS `GLKBaseEffect` allineato a proprietà che devono essere a 16 byte). È possibile utilizzare questa proprietà per decorare il metodo di richiamo e usare il valore di allineamento. Viene in genere utilizzato con il `OpenTK.Vector4` e `OpenTK.Matrix4` tipi quando è integrato con le API Objective-C.

Esempio:

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```


### <a name="appearanceattribute"></a>AppearanceAttribute

Il `[Appearance]` attributo è limitato ai dispositivi iOS 5, in cui è stata introdotta la gestione di aspetto.

Il `[Appearance]` attributo può essere applicato a qualsiasi metodo o proprietà che fanno parte di `UIAppearance` framework. Quando questo attributo viene applicato a un metodo o proprietà in una classe, indirizza il generatore di associazione per creare una classe fortemente tipizzata aspetto che viene usata per definire lo stile tutte le istanze di questa classe o le istanze che soddisfano determinati criteri.

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

### <a name="autoreleaseattribute-xamarinios-54"></a>AutoReleaseAttribute (xamarin. IOS 5.4)

Usare la `[AutoReleaseAttribute]` sui metodi e proprietà per eseguire il wrapping la chiamata al metodo al metodo in un `NSAutoReleasePool`.

In Objective-C sono disponibili alcuni metodi che restituiscono valori che vengono aggiunti per impostazione predefinita `NSAutoReleasePool`. Per impostazione predefinita, questi andrebbe al tuo thread `NSAutoReleasePool`, ma poiché xamarin. IOS mantiene anche un riferimento a oggetti, purché si trova l'oggetto gestito, non è possibile mantenere un riferimento aggiuntivo `NSAutoReleasePool` che solo ottenere svuotati finché il thread Restituisce il controllo al thread successivo, o torna al ciclo principale.

Questo attributo viene applicato ad esempio su proprietà pesante (ad esempio `UIImage.FromFile`) che restituisce oggetti che sono stati aggiunti per impostazione predefinita `NSAutoReleasePool`. Senza questo attributo, le immagini verranno conservate fino a quando il thread non ha restituito un controllo per il ciclo principale. UF il thread è stata una sorta di downloader in background che è sempre attivo e in attesa di lavoro, le immagini veniva mai rilasciate.

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

Il `[ForcedTypeAttribute]` è utilizzato per attivare la creazione di un tipo gestito, anche se l'oggetto restituito non gestito non corrisponde al tipo descritto nella definizione dell'associazione.

Ciò è utile quando al tipo descritto in un'intestazione corrisponde al tipo restituito del metodo nativo, ad esempio richiedere la seguente definizione di Objective-C da `NSURLSession`:

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

Afferma chiaramente che verrà restituito un `NSURLSessionDownloadTask` dell'istanza, ma ancora si **restituisce** una `NSURLSessionTask`, che è una superclasse e pertanto non è convertibile in `NSURLSessionDownloadTask`. Poiché si è in un contesto indipendente dai tipi un `InvalidCastException` verrà eseguito.

Conforme con la descrizione di intestazione e di evitare il `InvalidCastException`, il `[ForcedTypeAttribute]` viene usato.

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

Il `[ForcedTypeAttribute]` accetta anche un valore booleano denominato `Owns` vale a dire `false` per impostazione predefinita `[ForcedType (owns: true)]`. Il proprietario parametro viene usato per seguire le [criteri di proprietà dei](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html) per **componente di base** oggetti.

Il `[ForcedTypeAttribute]` è valido solo per i parametri, proprietà e valore restituito.

<a name="BindAsAttribute" />

### <a name="bindasattribute"></a>BindAsAttribute

Il `[BindAsAttribute]` consente di associare `NSNumber`, `NSValue` e `NSString`(enum) in più accurate C# tipi. L'attributo può essere utilizzato per creare migliori, più accurato, l'API .NET rispetto all'API nativa.

È possibile decorare i metodi (nel valore restituito), parametri e le proprietà con `BindAs`. L'unica restrizione è che il membro **non deve** trovarsi all'interno di un `[Protocol]` oppure [ `[Model]` ](#ModelAttribute) interfaccia.

Ad esempio:

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Output sarà:

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

Internamente questa operazione verrà eseguita la `bool?`  <->  `NSNumber` e `CGRect`  <->  `NSValue` conversioni.

I tipi supportati incapsulamento corrente sono:

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

Il seguente C# sono supportati i tipi di dati per essere incapsulate da/in `NSValue`:

* CGAffineTransform
* NSRange
* CGVector
* SCNMatrix4
* CLLocationCoordinate2D
* SCNVector3
* SCNVector4
* CGPoint / PointF
* CGRect / RectangleF
* CGSize / SizeF
* UIEdgeInsets
* UIOffset
* MKCoordinateSpan
* CMTimeRange
* CMTime
* CMTimeMapping
* CATransform3D

#### <a name="nsnumber"></a>NSNumber

Il seguente C# sono supportati i tipi di dati per essere incapsulate da/in `NSNumber`:

* bool
* byte
* double
* float
* short
* int
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

[`[BindAs]`](#BindAsAttribute) funziona con disposizioni [enumerazioni supportata da una costante NSString](#enum-attributes) in modo che è possibile creare migliore dell'API .NET, ad esempio:

```csharp
[BindAs (typeof (CAScroll))]
[Export ("supportedScrollMode")]
NSString SupportedScrollMode { get; set; }
```

Output sarà:

```csharp
[Export ("supportedScrollMode")]
CAScroll SupportedScrollMode { get; set; }
```

Gestiremo le `enum`  <->  `NSString` conversione solo se il tipo di enumerazione fornito al [ `[BindAs]` ](#BindAsAttribute) è [supportata da una costante NSString](#enum-attributes).

#### <a name="arrays"></a>Matrici

[`[BindAs]`](#BindAsAttribute) supporta anche le matrici di uno qualsiasi dei tipi supportati, è possibile ad esempio la definizione dell'API seguente:

```csharp
[return: BindAs (typeof (CAScroll []))]
[Export ("getScrollModesAt:")]
NSString [] GetScrollModes ([BindAs (typeof (CGRect []))] NSValue [] rects);
```

Output sarà:

```csharp
[Export ("getScrollModesAt:")]
CAScroll? [] GetScrollModes (CGRect [] rects) { ... }
```

Il `rects` parametro verrà incapsulato in un `NSArray` che contiene un' `NSValue` per ogni `CGRect` in cambio si ottiene una matrice di `CAScroll?` che è stato creato utilizzando i valori dell'oggetto restituito `NSArray` contenente `NSStrings`.

<a name="BindAttribute" />

### <a name="bindattribute"></a>BindAttribute

Il `[Bind]` attributo ha due utilizzi uno quando applicato a un metodo o la dichiarazione di proprietà e un altro quando applicate al singolo getter o setter all'interno di una proprietà.

Quando viene utilizzato per un metodo o proprietà, l'effetto del `[Bind]` attributo consiste nel generare un metodo che richiama il selettore specificato. Ma il metodo generato risulta non è decorato con il [ `[Export]` ](#ExportAttribute) attributo, il che significa che possono non partecipare nell'override del metodo. Viene in genere utilizzato in combinazione con il `[Target]` attributo per l'implementazione di metodi di estensione Objective-C.

Ad esempio:

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

Quando usato in un getter o setter, il `[Bind]` attributo viene usato per modificare le impostazioni predefinite dedotte dal generatore di codice durante la generazione di nomi di selettore Objective-C getter e setter per una proprietà. Per impostazione predefinita quando si contrassegna una proprietà con nome `fooBar`, genera il generatore di un `fooBar` esportazione per il getter e `setFooBar:` per il setter. In alcuni casi, Objective-C non segue questa convenzione, in genere cambiano il nome del metodo Get in `isFooBar`.
Utilizzare questo attributo per informare il generatore di questo oggetto.

Ad esempio:

```csharp
// Default behavior
[Export ("active")]
bool Active { get; set; }

// Custom naming with the Bind attribute
[Export ("visible")]
bool Visible { [Bind ("isVisible")] get; set; }
```

<a name="AsyncAttribute" />

### <a name="asyncattribute"></a>AsyncAttribute

Solo disponibili in xamarin. IOS 6.3 e versioni successive.

Questo attributo può essere applicato ai metodi che accettano un gestore di completamento come ultimo argomento.

È possibile usare il `[Async]` attributo nei metodi il cui ultimo argomento è un callback.  Quando si applica questo a un metodo, il generatore di binding genera una versione di tale metodo con il suffisso `Async`.  Se il callback non accetta parametri, il valore restituito sarà una `Task`, se il callback accetta un parametro, il risultato sarà un `Task<T>`.

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

Di seguito genera questo metodo asincrono:

```csharp
Task LoadFileAsync (string file);
```

Se il callback accetta più parametri, è necessario impostare il `ResultType` o `ResultTypeName` per specificare il nome del tipo generato che conterrà tutte le proprietà desiderato.

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

Gli elementi seguenti genereranno questo metodo asincrono, dove `FileLoading` contiene le proprietà per accedere sia `files` e `byteCount`:

```csharp
Task<FileLoading> LoadFile (string file);
```

Se l'ultimo parametro del metodo di callback è un' `NSError`, quindi generato `Async` metodo controllerà se il valore non null e se è questo il caso, il metodo asincrono generato imposterà l'eccezione di attività.

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

Il codice precedente genera il metodo asincrono seguente:

```csharp
Task<string> UploadAsync (string file);
```

E in caso di errore, l'attività risultante avrà l'eccezione impostata su un `NSErrorException` che esegue il wrapping risultante `NSError`.

#### <a name="asyncattributeresulttype"></a>AsyncAttribute.ResultType

Utilizzare questa proprietà per specificare il valore per la restituzione `Task` oggetto.   Questo parametro accetta un tipo esistente, pertanto deve essere definito in una delle definizioni di api principali.

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

Utilizzare questa proprietà per specificare il valore per la restituzione `Task` oggetto.   Questo parametro accetta il nome del nome del tipo desiderato, il generatore produce una serie di proprietà, uno per ogni parametro che accetta il callback.

#### <a name="asyncattributemethodname"></a>AsyncAttribute.MethodName

Utilizzare questa proprietà per personalizzare il nome dei metodi async generato.   L'impostazione predefinita consiste nell'usare il nome del metodo e aggiungere il testo "Async", è possibile usare questo modificare le impostazioni predefinite.

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

Questo attributo viene applicato a parametri di stringa o le proprietà della stringa e indica al generatore di codice non usare la copia da zero il marshalling delle stringhe per questo parametro, e invece creare una nuova istanza NSString dal C# string.
Questo attributo è necessaria solo per le stringhe, se si specifica che il generatore di usare il marshalling delle stringhe di copia da zero usando il `--zero-copy` opzione della riga di comando o l'impostazione dell'attributo a livello di assembly `ZeroCopyStringsAttribute`.

Questa operazione è necessaria nei casi in cui la proprietà è dichiarata in Objective-C da un `retain` oppure `assign` proprietà anziché una `copy` proprietà. Questi in genere si verificano nelle librerie di terze parti che sono stati erroneamente "ottimizzate" dagli sviluppatori. In generale, `retain` oppure `assign` `NSString` proprietà non sono corrette perché `NSMutableString` o classi derivate da utenti di `NSString` potrebbe alterare il contenuto delle stringhe senza conoscerne il codice della libreria, rilievo leggermente il applicazione. In genere ciò si verifica a causa dell'ottimizzazione prematura.

Di seguito mostra due proprietà di questo tipo in Objective-c:

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```


### <a name="disposeattribute"></a>DisposeAttribute

Quando si applica il `[DisposeAttribute]` a una classe, si fornisce un frammento di codice che verrà aggiunto al `Dispose()` implementazione del metodo della classe.

Poiché il `Dispose` metodo viene generato automaticamente dal `bmac-native` e `btouch-native` tools, è necessario usare i `[Dispose]` attributo per inserire codice nell'oggetto generato `Dispose` implementazione del metodo.

Ad esempio:

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute" />

### <a name="exportattribute"></a>ExportAttribute

Il `[Export]` attributo viene usato per contrassegnare un metodo o una proprietà possono essere esposti al runtime di Objective-C. Questo attributo viene condiviso tra i runtime di xamarin. IOS e xamarin. Mac effettivi e lo strumento di associazione. Per i metodi, il parametro viene passato verbatim per il codice generato, per le esportazioni di proprietà, un getter e setter vengono generate in base la dichiarazione di base (vedere la sezione sulla [ `[BindAttribute]` ](#BindAttribute) per informazioni su come modificare il comportamento dello strumento di associazione).

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

Il [selettore](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html) rappresenta il nome del metodo Objective-C sottostante o della proprietà che viene associato.

#### <a name="exportattributeargumentsemantic"></a>ExportAttribute.ArgumentSemantic

<a name="FieldAttribute" />

### <a name="fieldattribute"></a>FieldAttribute

Questo attributo viene usato per esporre una variabile globale C come un campo che viene caricato su richiesta ed esposti a C# codice. In genere questa operazione è necessaria per ottenere i valori costanti che sono definiti in C o Objective-C e che possa fungere da entrambi i token usati in alcune API o i cui valori sono opachi e devono essere usati come-sia dal codice utente.

Sintassi:

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

Il `symbolName` è il simbolo di C per il collegamento a. Per impostazione predefinita questo verrà caricato da una raccolta il cui nome viene dedotto dallo spazio dei nomi in cui il tipo è definito. Se non si tratta della libreria in cui viene cercato il simbolo, è necessario passare il `libraryName` parametro. Se si collegano una libreria statica, usare `__Internal` come il `libraryName` parametro.

Le proprietà generate sono sempre statiche.

Le proprietà contrassegnate con l'attributo del campo possono essere dei tipi seguenti:

* `NSString`
* `NSArray`
* `nint` / `int` / `long`
* `nuint` / `uint` / `ulong`
* `nfloat` / `float`
* `double`
* `CGSize`
* `System.IntPtr`
* Enumerazioni

Setter non sono supportati per [enumerazioni supportata da NSString costanti](#enum-attributes), ma possono essere associate manualmente se necessario.

Esempio:

```csharp
[Static]
interface CameraEffects {
     [Field ("kCameraEffectsZoomFactorKey", "CameraLibrary")]
     NSString ZoomFactorKey { get; }
}
```

<a name="InternalAttribute" />

### <a name="internalattribute"></a>InternalAttribute

Il `[Internal]` attributo può essere applicato a metodi o proprietà e ha l'effetto di contrassegnare il codice generato con il `internal` C# (parola chiave) eseguendo il codice accessibile solo al codice nell'assembly generato. Ciò in genere consente di nascondere le API che sono troppo basso livello o forniscono un'API pubblica non ottimale che si desidera migliorare al momento o per le API che non sono supportate dal generatore e richiedono alcuni-codifica manuale.

Quando si progetta l'associazione, si sarebbe in genere per nascondere il metodo o proprietà utilizzando questo attributo e fornire un nome diverso per il metodo o proprietà, quindi su di C# file di supporto aggiuntivo, si aggiunge un wrapper fortemente tipizzato che espone il funzionalità sottostante.

Ad esempio:

```csharp
[Internal]
[Export ("setValue:forKey:")]
void _SetValueForKey (NSObject value, NSObject key);

[Internal]
[Export ("getValueForKey:")]
NSObject _GetValueForKey (NSObject key);
```

Quindi, nel file di supporto, si può includere codice simile al seguente:

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

<a name="IsThreadStaticAttribute" />

### <a name="isthreadstaticattribute"></a>IsThreadStaticAttribute

Questo attributo contrassegna il campo sottostante per una proprietà da annotare con .NET `[ThreadStatic]` attributo. Ciò è utile se il campo è una variabile statica thread.

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions (Xamarin.iOS 6.0.6)

Questo attributo consentirà un metodo supporto nativo (Objective-C) le eccezioni.
Anziché chiamare `objc_msgSend` direttamente, la chiamata passa attraverso un trampoline personalizzato che intercetta le eccezioni ObjectiveC e ne esegue il marshalling nelle eccezioni gestite.

Attualmente solo per pochi `objc_msgSend` sono supportate le firme (consentirà di scoprire se una firma non è supportata quando il collegamento native di un'app che usa l'associazione ha esito negativo con un monotouch_ mancanti *_objc_msgSend* simboli), ma è più possibile aggiunta alla richiesta.


### <a name="newattribute"></a>NewAttribute

Questo attributo viene applicato ai metodi e proprietà affinché il generatore di generare il `new` parola chiave prima della dichiarazione.

Viene utilizzato per evitare avvisi del compilatore quando il metodo di stesso o il nome della proprietà è stato introdotto in una sottoclasse già esistenti in una classe base.

<a name="NotificationAttribute" />

### <a name="notificationattribute"></a>NotificationAttribute

È possibile applicare questo attributo ai campi per produrre il generatore un helper fortemente tipizzati classe delle notifiche.

Questo attributo può essere usato senza argomenti per le notifiche che non trasportano alcun payload, oppure è possibile specificare un `System.Type` che fa riferimento a un'altra interfaccia nella definizione dell'API, in genere con il nome che termina con "EventArgs". Il generatore di ricerca si trasforma l'interfaccia in una classe che rappresenti le sottoclassi `EventArgs` e includerà tutte le proprietà desiderate disponibili. Il [ `[Export]` ](#ExportAttribute) attributo deve essere utilizzato nel `EventArgs` classe per elencare il nome della chiave utilizzata per cercare il dizionario di Objective-C per recuperare il valore.

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

Gli utenti del codice quindi facilmente richiedere notifiche registrate per il [NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter) usando codice simile al seguente:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

O impostare un oggetto specifico da osservare. Se si passa `null` a `objectToObserve` questo metodo si comporta esattamente come suo altri peer.

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

Il valore restituito da `ObserveDidStart` consente facilmente smettere di ricevere notifiche, simile al seguente:

```csharp
token.Dispose ();
```

Oppure è possibile chiamare [NSNotification.DefaultCenter.RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) e passare il token. Se la notifica contiene parametri, è necessario specificare un helper `EventArgs` interfaccia, simile alla seguente:

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

Il codice precedente genererà una `MyScreenChangedEventArgs` classe con il `ScreenX` e `ScreenY` le proprietà che recupero i dati dal [NSNotification.UserInfo](xref:Foundation.NSNotification.UserInfo) dizionario usando le chiavi `ScreenXKey` e `ScreenYKey` rispettivamente e applicare le conversioni appropriate. Il `[ProbePresence]` viene usato per il generatore per verificare se la chiave viene impostata nel `UserInfo`, anziché tentare di estrarre il valore. Viene utilizzato per i casi in cui la presenza della chiave è il valore (in genere per i valori booleani).

In questo modo è possibile scrivere codice simile al seguente:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

In alcuni casi, non è costante non associato al valore passato nel dizionario.  In alcuni casi vengono utilizzate costanti di simboli pubblici di Apple e a volte vengono utilizzate costanti di stringa.  Per impostazione predefinita il [ `[Export]` ](#ExportAttribute) attributo fornito `EventArgs` classe utilizzerà il nome specificato come un simbolo pubblico per essere cercati in fase di esecuzione.  Se non è questo il caso, e invece si suppone che per essere cercati come costante stringa quindi passare il `ArgumentSemantic.Assign` valore all'attributo di esportazione.

**Novità in xamarin. IOS 8.4**

In alcuni casi, le notifiche inizierà vita senza argomenti, pertanto l'uso di [ `[Notification]` ](#NotificationAttribute) senza argomenti, è accettabile.  Ma in alcuni casi, verranno introdotti i parametri per la notifica.  Per supportare questo scenario, l'attributo può essere applicato più volte.

Se si sta sviluppando un'associazione e si vuole evitare di danneggiare il codice utente esistente, viene attivata una notifica esistente da:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

In una versione in cui sono elencati due volte, l'attributo di notifica simile al seguente:

```csharp
interface MyClass {
    [Notification]
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

<a name="NullAllowedAttribute" />

### <a name="nullallowedattribute"></a>NullAllowedAttribute

Quando questo viene applicato a una proprietà che contrassegna la proprietà in modo che il valore `null` da assegnare a esso. Ciò è valido solo per i tipi di riferimento.

Quando questo viene applicato a un parametro in una firma del metodo indica che il parametro specificato può essere null e che è non necessario eseguire alcun controllo per il passaggio `null` valori.

Se il tipo di riferimento non ha questo attributo, lo strumento di associazione verrà generato un controllo per il valore assegnato prima di passarlo Objective-c e genererà un controllo che genererà un' `ArgumentNullException` se il valore assegnato è `null`.

Ad esempio:

```csharp
// In properties

[NullAllowed]
UIImage IconFile { get; set; }

// In methods
void SetImage ([NullAllowed] UIImage image, State forState);
```

<a name="OverrideAttribute" />

### <a name="overrideattribute"></a>OverrideAttribute

Usare questo attributo per indicare il generatore di associazione che l'associazione per questo particolare metodo deve essere contrassegnata con un `override` (parola chiave).

### <a name="presnippetattribute"></a>PreSnippetAttribute

È possibile usare questo attributo per inserire un codice da inserire dopo che i parametri di input sono stati convalidati, ma prima che il codice chiami in Objective-C.

Esempio:

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

È possibile usare questo attributo per inserire codice da inserire prima i parametri vengono convalidati nel metodo generato.

Esempio:

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>PostGetAttribute

Indica al generatore di binding per richiamare la proprietà specificata da questa classe per recuperare un valore da quest'ultimo.

Questa proprietà viene in genere utilizzata per aggiornare la cache che punta a oggetti di riferimento che consentono di mantenere l'oggetto grafico cui si fa riferimento. In genere viene visualizzato nel codice che include operazioni come aggiunta/rimozione. Questo metodo viene utilizzato in modo che dopo gli elementi vengono aggiunti o rimossi aggiornate per assicurarsi che la cache interna, vengono conservati gestito i riferimenti agli oggetti che sono effettivamente in uso. Questo è possibile perché lo strumento di associazione genera un campo sottostante per tutti gli oggetti di riferimento in una determinata associazione.

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

In questo caso, il `Dependencies` proprietà verrà richiamata dopo l'aggiunta o rimozione delle dipendenze dal `NSOperation` oggetto, assicurando che abbiamo un grafico che rappresenta l'effettivo caricato gli oggetti, impedendo le perdite di memoria nonché il danneggiamento della memoria.

### <a name="postsnippetattribute"></a>PostSnippetAttribute

È possibile usare questo attributo per inserire alcuni C# nel codice sorgente per essere inserita dopo che il codice ha richiamato il metodo Objective-C sottostante

Esempio:

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>ProxyAttribute

Questo attributo viene applicato ai valori restituiti per contrassegnarli come oggetti proxy. Alcuni oggetti proxy restituiti API Objective-C che non possono essere distinti dai binding dell'utente. L'effetto di questo attributo è per contrassegnare l'oggetto come un `DirectBinding` oggetto. Per uno scenario in xamarin. Mac, è possibile vedere le [discussione su questo bug](https://bugzilla.novell.com/show_bug.cgi?id=670844).

### <a name="retainlistattribute"></a>RetainListAttribute

Indica il generatore di mantenere un riferimento al parametro gestito o rimuovere un riferimento interno per il parametro. Ciò consente di mantenere gli oggetti cui viene fatto riferimenti.

Sintassi:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Se il valore di `doAdd` è true, viene aggiunto il parametro per il `__mt_{0}_var List<NSObject>;`. In cui `{0}` viene sostituito con il determinato `listName`. È necessario dichiarare questo campo sottostante nella classe parziale complementare per l'API.

Per un esempio vedere [foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) e [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute (xamarin. IOS 6.0)

Ciò può essere applicato per restituire i tipi per indicare che il generatore deve chiamare `Release` nell'oggetto prima di restituirlo. È necessario solo quando un metodo fornisce un oggetto memorizzato (invece di un oggetto autoreleased, ovvero lo scenario più comune)

Esempio:

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

Inoltre questo attributo viene propagato al codice generato, in modo da indicare al runtime di xamarin. IOS che è necessario conservare l'oggetto restituito da Objective-C da tale funzione.


### <a name="sealedattribute"></a>SealedAttribute

Indica il generatore per contrassegnare il metodo generato come sealed. Se questo attributo viene omesso, il valore predefinito è per generare un metodo virtuale (un metodo virtuale, un metodo astratto o override a seconda del modo in cui vengono utilizzati altri attributi).

<a name="StaticAttribute" />

### <a name="staticattribute"></a>StaticAttribute

Quando il `[Static]` attributo viene applicato a un metodo o proprietà, verrà generato un metodo statico o una proprietà. Se questo attributo viene omesso, il generatore produce un metodo di istanza o una proprietà.


### <a name="transientattribute"></a>TransientAttribute

Usare questo attributo alle proprietà di flag i cui valori sono temporanei, vale a dire, gli oggetti vengono create temporaneamente per iOS, ma non sono di lunga durata. Quando questo attributo viene applicato a una proprietà, il generatore non crea un campo sottostante per questa proprietà, il che significa che la classe gestita non mantiene un riferimento all'oggetto.

<a name="WrapAttribute" />

### <a name="wrapattribute"></a>WrapAttribute

Nella progettazione delle associazioni Xamarin.iOS/Xamarin.Mac, il `[Wrap]` attributo viene usato per eseguire il wrapping di un oggetto con tipizzazione debole con un oggetto fortemente tipizzato. Questo entra in gioco principalmente con gli oggetti delegati di Objective-C che in genere vengono dichiarati come tipo `id` o `NSObject`. La convenzione usata da xamarin. IOS e xamarin. Mac consiste nell'esporre tali origini dati o delegati come tipo `NSObject` e vengono denominati usando la convenzione "Weak" + nome esposto. Un' `id delegate` proprietà da Objective-C verrà esposta come un `NSObject WeakDelegate { get; set; }` proprietà nel file del contratto API.

Ma in genere il valore assegnato a questo delegato è di un tipo sicuro, la tipizzazione forte della superficie di attacco e applicare il `[Wrap]` attributo, ciò significa che gli utenti possono scegliere di utilizzare tipi vulnerabili se necessitano di un controllo di granularità o se devono ricorrere a basso livello tric KS, oppure è possibile usare la proprietà fortemente tipizzata per la maggior parte del lavoro.

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

Si tratta di come l'utente utilizzerà la versione con tipizzazione debole del delegato:

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

Si tratta di come l'utente potrebbe usare la versione fortemente tipizzata, si noti che l'utente si avvale di C#Usa la parola chiave override per dichiarare il suo scopo e del sistema di tipi e che non è manualmente decorare il metodo con `[Export]`, poiché è ha funzionato nell'associazione per l'utente:

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

Un altro impiego del `[Wrap]` attributo deve supportare la versione fortemente tipizzata di metodi.  Ad esempio:

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

I membri generati da `[Wrap]` non sono `virtual` per impostazione predefinita, se è necessario un `virtual` è possibile impostare su membro `true` facoltativo `isVirtual` parametro.

```csharp
[BaseType (typeof (NSObject))]
interface FooExplorer {
    [Export ("fooWithContentsOfURL:")]
    void FromUrl (NSUrl url);

    [Wrap ("FromUrl (NSUrl.FromString (url))", isVirtual: true)]
    void FromUrl (string url);
}
```

## <a name="parameter-attributes"></a>Attributi dei parametri

In questa sezione vengono descritti gli attributi che è possibile applicare ai parametri nella definizione di un metodo, nonché `[NullAttribute]` che si applica a una proprietà nel suo complesso.

<a name="BlockCallback" />

### <a name="blockcallback"></a>BlockCallback

Questo attributo viene applicato a tipi di parametro in C# dichiarazioni di delegato per notificare lo strumento di associazione che il parametro in questione è conforme a di Objective-C block convenzione di chiamata e deve eseguire il marshalling, in questo modo.

Viene in genere utilizzato per i callback sono definiti come segue in Objective-c:

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Vedere anche: [CCallback](#CCallback).

<a name="CCallback" />

### <a name="ccallback"></a>CCallback

Questo attributo viene applicato a tipi di parametro in C# delegare le dichiarazioni per notificare lo strumento di associazione che il parametro in questione sia conforme alla convenzione di chiamata il puntatore a funzione ABI C e deve eseguire il marshalling, in questo modo.

Viene in genere utilizzato per i callback sono definiti come segue in Objective-c:

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Vedere anche: [BlockCallback](#BlockCallback).

### <a name="params"></a>params

È possibile usare il `[Params]` attributo nell'ultimo parametro della definizione di un metodo affinché il generatore di inserire un "params" nella definizione della matrice.   In questo modo l'associazione facilitare l'autorizzazione per i parametri facoltativi.

Ad esempio, la definizione seguente:

```csharp
[Export ("loadFiles:")]
void LoadFiles ([Params]NSUrl [] files);
```

Consente lo scrittura di codice seguente:

```csharp
foo.LoadFiles (new NSUrl (url));
foo.LoadFiles (new NSUrl (url1), new NSUrl (url2), new NSUrl (url3));
```

Ciò offre il vantaggio aggiunto che non richiede agli utenti di creare una matrice esclusivamente per il passaggio di elementi.

<a name="plainstring" />

### <a name="plainstring"></a>PlainString

È possibile usare la `[PlainString]` attributo davanti a parametri di stringa per indicare il generatore di binding per passare la stringa come stringa C, anziché passare il parametro come un `NSString`.

Utilizzano la maggior parte delle API di Objective-C `NSString` parametri, ma un numero limitato di API esporre una `char *` API per il passaggio di stringhe, anziché il `NSString` variazione.
Usare `[PlainString]` in questi casi.

Ad esempio, le seguenti dichiarazioni di Objective-C:

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

Indica il generatore per mantenere un riferimento al parametro specificato. Il generatore fornirà l'archivio di backup per questo campo oppure è possibile specificare un nome (il `WrapName`) per archiviare il valore in corrispondenza. Ciò è utile per mantenere un riferimento a un oggetto gestito che viene passato come parametro per Objective-C e quando si sa già che Objective-C manterrà solo questa copia dell'oggetto. Ad esempio, un'API, ad esempio `SetDisplay (SomeObject)` utilizzerebbe questo attributo in quanto è probabile che il SetDisplay potrebbe visualizzare solo un oggetto alla volta. Se è necessario tenere traccia di più di un oggetto (ad esempio, per un'API simile a Stack) si userà il `[RetainList]` attributo.

Sintassi:

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```


### <a name="retainlistattribute"></a>RetainListAttribute

Indica il generatore di mantenere un riferimento al parametro gestito o rimuovere un riferimento interno per il parametro. Ciò consente di mantenere gli oggetti cui viene fatto riferimenti.

Sintassi:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Se il valore di `doAdd` è true, viene aggiunto il parametro per il `__mt_{0}_var List<NSObject>`. In cui `{0}` viene sostituito con il determinato `listName`. È necessario dichiarare questo campo sottostante nella classe parziale complementare per l'API.

Per un esempio vedere [foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) e [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)


### <a name="transientattribute"></a>TransientAttribute

Questo attributo viene applicato a parametri e viene usato solo durante la transizione da Objective-C in C#.  Durante le transizioni di Objective-C vari `NSObject` parametri vengono incapsulati in una rappresentazione dell'oggetto gestita.

Il runtime consente di accettare un riferimento all'oggetto nativo e di mantenere il riferimento fino a quando l'ultimo riferimento gestito all'oggetto è scomparso e che il Garbage Collector ha la possibilità di eseguire.

In alcuni casi, è importante per il C# runtime di non mantenere un riferimento all'oggetto nativo.  In alcuni casi ciò si verifica quando il codice nativo sottostante ha collegato un comportamento speciale per il ciclo di vita del parametro.  Ad esempio: il distruttore per il parametro verrà eseguita un'operazione di pulizia o eliminare alcune risorse preziose.

Questo attributo comunica al runtime che si desidera l'oggetto per essere eliminato se possibile la restituzione al Objective-C dal metodo sovrascritto.

La regola è semplice: se il runtime era necessario creare una nuova rappresentazione gestita dall'oggetto nativo, quindi alla fine della funzione, verrà eliminato il conteggio di conservazione per l'oggetto nativo e le proprietà di Handle dell'oggetto gestito verrà cancellata.   Ciò significa che se si ha mantenuto un riferimento all'oggetto gestito, che fanno riferimento a diventerà inutilizzabile (richiamo di metodi su di esso verrà generata un'eccezione).

Se l'oggetto passato non è stato creato, o se era già presente una rappresentazione gestita in attesa dell'oggetto, l'eliminazione forzata non viene eseguita. 


## <a name="property-attributes"></a>Attributi proprietà

<a name="NotImplementedAttribute" />

### <a name="notimplementedattribute"></a>NotImplementedAttribute

Questo attributo viene utilizzato per supportare un linguaggio di Objective-C in cui una proprietà con un metodo di richiamo è stato introdotto in una classe di base e una sottoclasse modificabile introduce un setter.

Poiché C# non supporta questo modello, la classe di base deve avere sia il setter e getter e può usare una sottoclasse di [OverrideAttribute](#OverrideAttribute).

Questo attributo viene usato solo nei Setter delle proprietà e viene usato per supportare l'idioma modificabile in Objective-C.

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

<a name="enum-attributes" />

## <a name="enum-attributes"></a>Enum (attributi)

Mapping `NSString` costanti per valori di enumerazione è un modo semplice per creare migliore dell'API .NET. È:

* consente il completamento del codice essere più utile, mostrando **solo** i valori corretti per le API.
* Aggiunge l'indipendenza, non è possibile usare un altro `NSString` costante in un contesto non corretto; e
* Consente di nascondere alcune costanti, rendendo il completamento del codice mostrano più breve elenco di API senza perdita di funzionalità.

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

Dalla definizione del binding precedente verrà creato il generatore il `enum` stesso e verrà creata anche una `*Extensions` tipi statici che include i metodi di conversione di due modi tra i valori di enumerazione e la `NSString` costanti. Ciò significa che le costanti rimane disponibile per gli sviluppatori, anche se non fanno parte dell'API.

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

È possibile decorare **uno** valore enum con tale attributo. Che diventerà la costante viene restituita se non si conosce il valore di enumerazione.

Nell'esempio precedente:

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

Se nessun valore di enumerazione è decorato un `NotSupportedException` verrà generata.

### <a name="errordomainattribute"></a>ErrorDomainAttribute

Codici di errore vengono associati come valori di enumerazione. È in genere un dominio di errore per tali e non è sempre facile trovare quale applica (o se ne esiste ancora).

È possibile usare questo attributo per associare il dominio di errore con l'enumerazione stessa.

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

È quindi possibile chiamare il metodo di estensione `GetDomain` per ottenere il dominio costante di eventuali errori.

### <a name="fieldattribute"></a>FieldAttribute

Questo è lo stesso `[Field]` attributo usato per le costanti all'interno di tipo. Può anche essere utilizzato all'interno di enumerazioni per mappare un valore con una costante specifica.

Oggetto `null` valore può essere utilizzato per specificare quale valore di enumerazione deve essere restituito se un `null` `NSString` costante è specificata.

Nell'esempio precedente:

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

Se nessun `null` valore è presente un' `ArgumentNullException` verrà generata.

## <a name="global-attributes"></a>Attributi globali

Gli attributi globali vengono applicati sia utilizzando il `[assembly:]` modificatore di attributo, ad esempio il [ `[LinkWithAttribute]` ](#LinkWithAttribute) oppure può essere utilizzata in qualsiasi posizione, ad esempio il [ `[Lion]` ](#SinceAndLionAttributes) e [ `[Since]` ](#SinceAndLionAttributes) attributi.

<a name="LinkWithAttribute" />

### <a name="linkwithattribute"></a>LinkWithAttribute

Si tratta di un attributo a livello di assembly che consente agli sviluppatori di specificare il flag di collegamento necessari a riutilizzare una libreria associata senza forzare il consumer della libreria per configurare manualmente il gcc_flags e argomenti aggiuntivi di mtouch passati a una raccolta.

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

Questo attributo viene applicato a livello di assembly, ad esempio, questo è ciò che il [associazioni CorePlot](https://github.com/mono/monotouch-bindings/tree/master/CorePlot) usare:

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

Quando si usa la `[LinkWith]` dell'attributo, l'oggetto specificato `libraryName` vengono incorporate nell'assembly risultante, consentendo agli utenti di fornire una singola DLL che contiene le dipendenze non gestite nonché i flag della riga di comando necessari per utilizzare correttamente la libreria di xamarin. IOS.

È anche possibile non fornire una `libraryName`, nel qual caso il `LinkWith` attributo può essere utilizzato per specificare solo flag aggiuntivi del linker:

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>Costruttori LinkWithAttribute

Questi costruttori consentono di specificare la libreria per il collegamento con e incorporare nell'assembly risultante, le destinazioni supportate che supporta la libreria e qualsiasi flag della libreria facoltativa che è necessario eseguire il collegamento con la libreria.

Si noti che il `LinkTarget` argomento viene dedotto da xamarin. IOS e non dovrà essere impostata.

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

Il `ForceLoad` proprietà viene usata per stabilire o meno il `-force_load` collegamento flag viene utilizzato per collegare la libreria nativa. Per ora, questo deve essere sempre true.

#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute.Frameworks

Se la raccolta a cui è associata dispone di un requisito fondamentale su qualsiasi Framework (diverso da `Foundation` e `UIKit`), è consigliabile impostare il `Frameworks` proprietà a una stringa contenente un elenco delimitato da spazi dei framework di piattaforma necessarie. Ad esempio, se si associa una libreria che richiede `CoreGraphics` e `CoreText`, è necessario impostare la `Frameworks` proprietà `"CoreGraphics CoreText"`.

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

Impostare questa proprietà su true se il file eseguibile risultante deve essere compilata usando un compilatore C++ anziché il valore predefinito, ovvero un compilatore C. Usare questa opzione se la raccolta che si esegue l'associazione è stato scritto in C++.

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute.LibraryName

Il nome della libreria non gestita per creare un bundle. Si tratta di un file con estensione "estensione a" e può contenere codice oggetto per più piattaforme (ad esempio, ARM e x86 per il simulatore).

Le versioni precedenti di xamarin. IOS selezionata la `LinkTarget` proprietà per determinare la libreria supportata della piattaforma, ma questo viene ora rilevato automaticamente e il `LinkTarget` proprietà viene ignorata.

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

Il `LinkerFlags` stringa offre un modo per gli autori delle associazioni specificare qualsiasi flag aggiuntivi del linker necessari quando si collega la libreria nativa all'applicazione.

Ad esempio, se la libreria nativa richiede libxml2 e zlib, è necessario impostare il `LinkerFlags` da string a `"-lxml2 -lz"`.

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute.LinkTarget

Le versioni precedenti di xamarin. IOS selezionata la `LinkTarget` proprietà per determinare la libreria supportata della piattaforma, ma questo viene ora rilevato automaticamente e il `LinkTarget` proprietà viene ignorata.

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

Impostare questa proprietà su true se la raccolta che si crea un collegamento richiede la libreria di gestione delle eccezioni GCC (gcc_eh)

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute.SmartLink

Il `SmartLink` proprietà deve essere impostata su true per consentire a xamarin. IOS di determinare se `ForceLoad` è necessario o meno.

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

Il `WeakFrameworks` proprietà funziona esattamente come i `Frameworks` proprietà, tranne che in fase di collegamento, il `-weak_framework` identificatore viene passato a gcc per ognuno dei Framework elencata.

`WeakFrameworks` rende possibile per le librerie e applicazioni da un collegamento debole su Framework della piattaforma in modo che è possibile usarli, facoltativamente, se sono disponibili ma non accettano una dipendenza rigida su di essi, che risulta utile se la libreria è progettata per aggiungere ulteriori funzionalità in più recente versioni di iOS. Per altre informazioni sui collegamenti deboli, vedere la documentazione di Apple sul [collegamento debole](https://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html).

Sarebbero buoni candidati per il collegamento debole `Frameworks` come account `CoreBluetooth`, `CoreImage`, `GLKit`, `NewsstandKit` e `Twitter` poiché sono disponibili solo in iOS 5.

<a name="SinceAndLionAttributes" />

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute (iOS) e LionAttribute (macOS)

Si utilizza il `[Since]` per le API di flag dell'attributo come se avessero introdotto un certo punto nel tempo. L'attributo deve essere utilizzato solo per contrassegnare tipi e metodi che potrebbero causare un problema di runtime se la classe sottostante, metodo o proprietà non è disponibile.

Sintassi:

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

Non deve in genere essere applicata per enumerazioni, i vincoli o nuove strutture come quelli non potrebbero causare un errore di runtime se vengono eseguite su un dispositivo con una versione precedente del sistema operativo.

Esempio quando viene applicato a un tipo:

```csharp
// Type introduced with iOS 4.2
[Since (4,2)]
[BaseType (typeof (UIPrintFormatter))]
interface UIViewPrintFormatter {
    [Export ("view")]
    UIView View { get; }
}
```

Esempio quando viene applicato a un nuovo membro:

```csharp
[BaseType (typeof (UIViewController))]
public interface UITableViewController {
    [Export ("tableView", ArgumentSemantic.Retain)]
    UITableView TableView { get; set; }

    [Since (3,2)]
    [Export ("clearsSelectionOnViewWillAppear")]
    bool ClearsSelectionOnViewWillAppear { get; set; }
```

Il `[Lion]` attributo viene applicato allo stesso modo, ma per i tipi introdotti con Lion. La ragione per utilizzare `[Lion]` rispetto al numero di versione più specifico che viene usato in iOS è tale iOS viene revisionato molto spesso, mentre le versioni principali di OS X si verificano raramente e rende più facile da ricordare il sistema operativo da loro nome in codice di base al numero di versione

### <a name="adviceattribute"></a>AdviceAttribute

Usare questo attributo per offrire agli sviluppatori un suggerimento su altre API che potrebbero essere più opportuno per poter usare.   Ad esempio, se si specifica una versione fortemente tipizzata di un'API, si potrebbe usare questo attributo sull'attributo con tipizzazione debole per indicare che lo sviluppatore dell'API migliorato.

Le informazioni di questo attributo viene visualizzate nella documentazione e gli strumenti possono essere sviluppati per suggerimenti su come migliorare utente

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

Solo disponibili in xamarin. IOS 5.4 e successive.

Questo attributo indica al generatore di che il binding per questa libreria specifica (se applicate con `[assembly:]`) o tipo deve essere utilizzato il marshalling delle stringhe copia veloce da zero. Questo attributo equivale a passando l'opzione della riga di comando `--zero-copy` al generatore.

Quando si usa copia zero per le stringhe, il generatore Usa in modo efficace lo stesso C# stringa come la stringa di Objective-C Usa senza dover sostenere la creazione di un nuovo `NSString` oggetto ed evitando di copiare i dati dal C# stringhe per il Stringa di Objective-C. L'unico svantaggio dell'uso di stringhe di copia da Zero è che è necessario assicurarsi che qualsiasi proprietà di stringa che si esegue il wrapping che accade viene contrassegnata come `retain` oppure `copy` ha il `[DisableZeroCopy]` set di attributi. Questa è necessario perché l'handle per le stringhe di copia da zero è allocato nello stack e non è valido dopo la funzione restituita.

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

È anche possibile applicare l'attributo a livello di assembly e verrà applicata a tutti i tipi dell'assembly:

```csharp
[assembly:ZeroCopyStrings]
```

## <a name="strongly-typed-dictionaries"></a>Dizionari fortemente tipizzati

Con xamarin. IOS 8.0 è stato introdotto il supporto per la creazione semplificata di classi fortemente tipizzate che eseguono il wrapping `NSDictionaries`.

Sebbene sia sempre stato possibile usare la [DictionaryContainer](xref:Foundation.DictionaryContainer) del tipo di dati insieme a un'API manuale, è ora molto più semplice eseguire questa operazione.  Per altre informazioni, vedere [esponendo tipi sicuri](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types).

<a name="StrongDictionary" />

### <a name="strongdictionary"></a>StrongDictionary

Quando questo attributo viene applicato a un'interfaccia, il generatore di genererà una classe con lo stesso nome dell'interfaccia che deriva da [DictionaryContainer](xref:Foundation.DictionaryContainer) e lo trasforma ogni proprietà definita nell'interfaccia nell'oggetto fortemente tipizzato getter e setter per il dizionario.

Questo genera automaticamente una classe che può essere creata un'istanza da un oggetto esistente `NSDictionary` o che è stato creato nuovo.

Questo attributo accetta un parametro, il nome della classe contenente le chiavi usate per accedere agli elementi nel dizionario.   Per impostazione predefinita ogni proprietà nell'interfaccia con l'attributo ricerca un membro nel tipo specificato per un nome con suffisso "Key".

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

In questo caso, il `MyOption` classe produrrà una proprietà di stringa per `Name` che verrà utilizzato il `MyOptionKeys.NameKey` come chiave nel dizionario per recuperare una stringa.   E Usa il `MyOptionKeys.AgeKey` come chiave nel dizionario da cui recuperare un `NSNumber` che contiene un valore int.

Se si desidera usare una chiave diversa, è possibile usare l'attributo export sulla proprietà, ad esempio:

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

#### <a name="strong-dictionary-types"></a>Tipi di dizionario sicuro

Tipi di dati seguenti sono supportati nel `StrongDictionary` definizione:

|C#Tipo di interfaccia|`NSDictionary` Tipo di archiviazione|
|---|---|
|`bool`|`Boolean` archiviati in un `NSNumber`|
|Valori di enumerazione|numero intero archiviato in un `NSNumber`|
|`int`|intero a 32 bit archiviato in un `NSNumber`|
|`uint`|intero senza segno a 32 bit archiviato in un `NSNumber`|
|`nint`|`NSInteger` archiviati in un `NSNumber`|
|`nuint`|`NSUInteger` archiviati in un `NSNumber`|
|`long`|intero a 64 bit archiviato in un `NSNumber`|
|`float`|intero a 32 bit archiviato come un `NSNumber`|
|`double`|intero a 64 bit archiviato come un `NSNumber`|
|`NSObject` e sottoclassi contenuti|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C#`Array` di `NSObject`|`NSArray`|
|C#`Array` delle enumerazioni|`NSArray` contenente `NSNumber` valori|
