---
title: Guida di riferimento dei tipi di associazione
description: Questa Guida di riferimento vengono descritti vari attributi e i concetti necessari per comprendere quando si creano associazioni c# alle librerie Objective-C.
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: 3de85a1e3c84366a2059a8f7c479c20ce873508d
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782170"
---
# <a name="binding-types-reference-guide"></a>Guida di riferimento dei tipi di associazione

Questo documento descrive l'elenco di attributi che è possibile usare per annotare i file di contratto API per guidare l'associazione e il codice generato

Xamarin. IOS e API Xamarin.Mac contratti vengono scritti in c# per lo più come definizioni di interfaccia che definiscono il modo che il codice Objective-C verrà rese disponibili per c#. Il processo include una combinazione di dichiarazioni di interfaccia e alcune definizioni del tipo di base che potrebbe richiedere contratto API. Per un'introduzione ai tipi di associazione, vedere la Guida complementare [associazione Objective-C librerie](~/cross-platform/macios/binding/objective-c-libraries.md).

## <a name="type-definitions"></a>Definizioni di tipo

Sintassi:

```csharp
[BaseType (typeof (BTYPE))
interface MyType [: Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

Ogni interfaccia nella definizione del contratto con il [ `[BaseType]` ](#BaseTypeAttribute) attributo che dichiara il tipo base per l'oggetto generato. Nella dichiarazione precedente un `MyType` verrà generata classe tipo c# che viene associato a un tipo di Objective-C chiamato `MyType`.

Se si specifica alcun tipo dopo typename (nell'esempio precedente `Protocol1` e `Protocol2`) utilizzando la sintassi di ereditarietà di interfaccia il contenuto di tali interfacce verrà resa inline come se fossero stati parte del contratto per `MyType`.
Il modo in cui è superfici di xamarin che un tipo adotta un protocollo per l'incorporamento tutti i metodi e proprietà che sono state dichiarate nel protocollo nel tipo stesso.

Il seguente viene illustrato come la dichiarazione di Objective-C per `UITextField` deve essere definito in un contratto di xamarin:

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

Viene scritto come questo come un contratto API c#:

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

È possibile controllare molti altri aspetti della generazione del codice dall'applicazione di altri attributi per l'interfaccia nonché configurare le [ `[BaseType]` ](#BaseTypeAttribute) attributo.


### <a name="generating-events"></a>La generazione di eventi

Una funzionalità del progetto xamarin e API Xamarin.Mac è che le classi delegate Objective-C vengono mappate come callback ed eventi in c#. Gli utenti possono scegliere in ogni istanza se desiderano adottare il modello di programmazione Objective-C, tramite l'assegnazione alle proprietà, ad esempio `Delegate` un'istanza di una classe che implementa i vari metodi per chiama il runtime Objective-C, o in base scelta di c#-eventi e proprietà di stile.

Possiamo vedere un esempio di come utilizzare il modello di Objective-C:

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

Nell'esempio precedente, si noterà che, abbiamo scelto di sovrascrivere i due metodi, una notifica che un evento di scorrimento è eseguita sul posto, mentre la seconda che è un callback che deve restituire un valore booleano che indica il `scrollView` se è necessario scorrere per il primi oppure No.

Modello c# consente all'utente della libreria ascoltare le notifiche tramite la sintassi dell'evento in c# o la sintassi della proprietà per associare i callback che devono restituire valori.

Ciò è illustrato l'aspetto di codice c# per la stessa funzionalità come l'uso di espressioni lambda:

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

Poiché gli eventi non restituiscono valori (hanno un tipo restituito void) è possibile connettere più copie. Il `ShouldScrollToTop` non è un evento, è invece una proprietà con il tipo `UIScrollViewCondition` che presenta questa firma:

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

Restituisce un `bool` valore, in questo caso la sintassi di espressione lambda consente di restituire solo il valore di `MakeDecision` (funzione).

Il generatore di associazione supporta la generazione di eventi e proprietà che si collegano una classe, ad esempio `UIScrollView` con il relativo `UIScrollViewDelegate` (anche chiamare questi la classe di modello), questa operazione viene eseguita mediante annotazione il [ `[BaseType]` ](#BaseTypeAttribute) definizione con il `Events` e `Delegates` parametri (descritti di seguito). Oltre ad annotare la [ `[BaseType]` ](#BaseTypeAttribute) con questi parametri è necessario informare il generatore di alcuni altri componenti.

Per gli eventi che accettano più di un parametro (in Objective-C la convenzione è che il primo parametro in una classe delegata è l'istanza dell'oggetto mittente) è necessario fornire il nome che si desidera per generato `EventArgs` classe sia. Questa operazione viene eseguita con il [ `[EventArgs]` ](#EventArgsAttribute) attributo nella dichiarazione del metodo nella classe del modello. Ad esempio:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

La dichiarazione precedente verrà generato un `UIImagePickerImagePickedEventArgs` classe che deriva da `EventArgs` Pack entrambi i parametri e il `UIImage` e `NSDictionary`. Il generatore produce questo:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Successivamente espone le seguenti operazioni nel `UIImagePickerController` classe:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

Metodi che restituiscono un valore del modello sono associati in modo diverso. Tali criteri richiedono sia un nome per il delegato generato c# (la firma del metodo) e anche un valore predefinito da restituire nel caso in cui l'utente non fornisce un'implementazione se stesso. Ad esempio, il `ShouldScrollToTop` si tratta di definizione:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

Il precedente creerà una `UIScrollViewCondition` delegato con la firma che è stato illustrato in precedenza e se l'utente non fornisce un'implementazione, il valore restituito sarà true.

Oltre ai [ `[DefaultValue]` ](#DefaultValueAttribute) attributo, è anche possibile usare i [ `[DefaultValueFromArgument]` ](#DefaultValueFromArgumentAttribute) attributo che indirizza il generatore per restituire il valore del parametro specificato nella chiamata o di [ `[NoDefaultValue]` ](#NoDefaultValueAttribute) parametro che indica il generatore che non sia presente alcun valore predefinito.

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

Utilizzare il `Name` proprietà per controllare il nome che consente l'associazione a questo tipo nel mondo Objective-C. Questa è in genere usata per assegnare il tipo c# un nome che è conforme alle linee guida di progettazione di .NET Framework, ma che esegue il mapping a un nome in Objective-C che seguono questa convenzione.

L'esempio, nel caso seguente vengono mappate Objective-C `NSURLConnection` tipo `NSUrlConnection`, come linee guida di progettazione di .NET Framework utilizzare "Url" anziché "URL":

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

Il nome specificato viene utilizzato come valore per generato `[Register]` attributo nell'associazione. Se `Name` non è specificato, nome breve del tipo viene utilizzato come valore per il `[Register]` attributo nell'output generato.

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType.Events e BaseType.Delegates

Queste proprietà sono utilizzate per controllare la generazione di c#-applicare stili di eventi in classi generate. Vengono utilizzati per collegare una determinata classe con la relativa classe di delegato Objective-C. Si verificheranno molti casi in cui una classe viene utilizzata una classe delegata per inviare notifiche e gli eventi. Ad esempio un `BarcodeScanner` avrebbe un complementare `BardodeScannerDelegate` classe. Il `BarcodeScanner` classe in genere dispongono di un `Delegate` proprietà che è necessario assegnare un'istanza di `BarcodeScannerDelegate` per, mentre questo funzionamento, è possibile esporre agli utenti in c#-come interfaccia di eventi di stile e in questi casi si utilizzerebbe il `Events`e `Delegates` delle proprietà del [ `[BaseType]` ](#BaseTypeAttribute) attributo.

Queste proprietà sono sempre impostate insieme e devono avere lo stesso numero di elementi e mantenere sincronizzati. Il `Delegates` matrice contiene un'unica stringa per ogni delegato con tipizzazione debole che si desidera eseguire il wrapping, e il `Events` matrice contiene un tipo per ogni tipo che si desidera associare.

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

Se si applica questo attributo quando vengono create nuove istanze di questa classe, l'istanza di tale oggetto verrà conservato intorno finché il metodo a cui fa riferimento il `KeepRefUntil` è stato richiamato. Ciò è utile per migliorare l'utilizzabilità delle API, quando non si desidera mantenere un riferimento a un oggetto per usare il codice all'utente. Il valore di questa proprietà è il nome di un metodo nel `Delegate` classe, pertanto è necessario utilizzare tale in combinazione con il `Events` e `Delegates` anche proprietà.

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

Usare questo attributo quando è necessario l'oggetto di essere inizializzata con uno dei costruttori della classe.


### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

Quando questo attributo viene applicato alla definizione dell'interfaccia verrà segnala come privato il costruttore predefinito. Ciò significa che è possibile comunque creare un'istanza di oggetto di questa classe internamente dal file dell'estensione, ma non appena si essere accessibile agli utenti della classe.

<a name="CategoryAttribute" />

### <a name="categoryattribute"></a>CategoryAttribute

Utilizzare questo attributo su una definizione di tipo per associare le categorie di Objective-C e per esporre come metodi di estensione c# per la modalità di che Objective-C espone la funzionalità di mirroring.

Le categorie sono un meccanismo di Objective-C utilizzato per estendere il set di metodi e le proprietà disponibili in una classe.   In pratica, vengono utilizzati per estendere la funzionalità di una classe di base (ad esempio `NSObject`) quando è associato un framework specifico (ad esempio `UIKit`), rendendo i metodi disponibili, ma solo se è collegato al nuovo framework.   In alcuni casi, vengono utilizzati per organizzare le funzionalità in una classe dalla funzionalità.   Sono simili per spirito ai metodi di estensione c#.

Si tratta di una categoria aspetto in Objective-c:

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

Nell'esempio precedente si trova su una libreria che si estende anche le istanze di `UIView` con il metodo `makeBackgroundRed`.

Per associare quelli, è possibile usare il [ `[Category]` ](#CategoryAttribute) attributo in una definizione di interfaccia.   Quando si utilizza il [ `[Category]` ](#CategoryAttribute) attributo, il significato del [ `[BaseType]` ](#BaseTypeAttribute) attributo cambia vengano utilizzati per specificare la classe di base per estendere a essere il tipo da estendere.

Nella seguente come `UIView` le estensioni sono associate e convertite in metodi di estensione c#:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

Il precedente creerà una `MyUIViewExtension` una classe che contiene il `MakeBackgroundRed` metodo di estensione.   Ciò significa che è ora possibile chiamare `MakeBackgroundRed` su qualsiasi `UIView` sottoclasse, permettendo di avere la stessa funzionalità si otterrebbe in Objective-C.

In alcuni casi troverà **statico** membri all'interno delle categorie come nell'esempio seguente:

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

Questo può provocare un **corretto** definizione di interfaccia di categoria in c#:

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

Non è corretto perché per utilizzare il `BoolMethod` estensione necessaria un'istanza di `FooObject` ma si associa un ObjC **statico** estensione, si tratta di un effetto collaterale dovuto al fatto di implementazione dei metodi di estensione c#.

L'unico modo per utilizzare definizioni di cui sopra è nel seguente codice propriamente:

```csharp
(null as FooObject).BoolMethod (range);
```

Per evitare questo problema si consiglia di rendere inline le `BoolMethod` definizione all'interno di `FooObject` definizione dell'interfaccia stessa, questo consente di chiamare questa estensione, ad esempio è destinato `FooObject.BoolMethod (range)`.

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Verrà emesso un avviso (BI1117) ogni volta che è stata trovata una [ `[Static]` ](#StaticAttribute) membro all'interno di una [ `[Category]` ](#CategoryAttribute) definizione. Se si vuole poter disporre [ `[Static]` ](#StaticAttribute) membri all'interno di [ `[Category]` ](#CategoryAttribute) definizioni è possibile disattiva l'avviso utilizzando `[Category (allowStaticMembers: true)]` o tramite la decorazione di un membro o [ `[Category]` ](#CategoryAttribute) definizione con dell'interfaccia [ `[Internal]` ](#InternalAttribute).

<a name="StaticAttribute_Class" />

### <a name="staticattribute"></a>StaticAttribute

Quando questo attributo viene applicato a una classe appena genererà una classe statica, che non derivano da `NSObject`, pertanto il [ `[BaseType]` ](#BaseTypeAttribute) attributo viene ignorato. Le classi statiche vengono utilizzate per ospitare variabili pubbliche C che si desidera esporre.

Ad esempio:

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

Genera una classe c# con l'API seguente:

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>Le definizioni di modello/protocollo

I modelli vengono utilizzati in genere dall'implementazione del protocollo.
Si differenziano in quanto il runtime registrerà solo con Objective-C i metodi che in realtà sono stati sovrascritti.
In caso contrario, il metodo non verranno registrato.

Ciò in genere significa che quando si crea una sottoclasse una classe che è stata contrassegnata con il `ModelAttribute`, non è necessario chiamare il metodo di base.   La chiamata di tale metodo genererà un'eccezione, è richiesta per implementare il comportamento intero in una sottoclasse per tutti i metodi che si esegue l'override.

<a name="AbstractAttribute" />

### <a name="abstractattribute"></a>AbstractAttribute

Per impostazione predefinita, i membri che fanno parte di un protocollo non sono obbligatori. Ciò consente agli utenti di creare una sottoclasse di `Model` oggetto semplicemente derivanti dalla classe in c# e si esegue l'override solo i metodi si interessano. In alcuni casi il contratto Objective-C richiede che l'utente fornisce un'implementazione di questo metodo (quelli contrassegnati con il `@required` direttiva Objective-C). In questi casi, è opportuno contrassegnare i metodi con il `[Abstract]` attributo.

Il `[Abstract]` attributo può essere applicato ai metodi o proprietà e fa sì che il generatore contrassegnare il membro generato come astratta e la classe sia una classe astratta.

Di seguito viene eseguita da xamarin:

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

Specifica il valore predefinito da restituire dal metodo modello se l'utente non fornisce un metodo per questo particolare metodo nell'oggetto modello

Sintassi:

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

Ad esempio, nella classe seguente immaginaria delegato per un `Camera` classe è fornire un `ShouldUploadToServer` che viene esposto come proprietà nel `Camera` classe. Se l'utente di `Camera` class non è impostato in modo esplicito un valore da un'espressione lambda che può rispondere true o false, il valore predefinito restituito in questo caso sarebbe impostato su false, il valore specificato nel `DefaultValue` attributo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

Se l'utente imposta un gestore della classe immaginaria, questo valore verrà ignorato:

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

Questo attributo quando viene fornito un metodo che restituisce un valore di una classe di modello indicherà il generatore per restituire il valore del parametro specificato, se l'utente non ha fornito il proprio metodo o l'espressione lambda.

Esempio:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

Nel caso precedente l'utente di `NSAnimation` classe scelto di utilizzare le proprietà in c# eventi/e non è stata impostata `NSAnimation.ComputeAnimationCurve` a un metodo o l'espressione lambda, il valore restituito sarà il valore passato nel parametro di stato di avanzamento.

Vedere anche: [ `[NoDefaultValue]` ](#NoDefaultValueAttribute), [`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

In alcuni casi è opportuno non espone un evento o un delegato proprietà da una classe di modello nella classe dell'host in modo l'aggiunta di questo attributo indicherà il generatore per evitare la generazione di qualsiasi metodo decorato con esso.

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

Questo attributo viene utilizzato nei metodi di modello che restituiscono valori per impostare il nome della firma del delegato da utilizzare.

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

Questo attributo viene utilizzato per consentire il generatore di modificare il nome della proprietà generato nella classe dell'host. Talvolta è utile quando il nome del metodo della classe FooDelegate appropriato per la classe del delegato, ma potrebbe sembrare strano nella classe dell'host come proprietà.

Anche questo è molto utile (e necessari) quando si dispone di due o più metodi di overload appropriata per mantenerli denominato come la classe FooDelegate ma si desidera esporli nella classe dell'host con un nome specifico migliorato.

Esempio:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

Con la definizione precedente, il generatore produrrà la dichiarazione seguente pubblica nella classe dell'host:

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```

<a name="EventArgsAttribute" />

### <a name="eventargsattribute"></a>EventArgsAttribute

Per gli eventi che accettano più di un parametro (in Objective-C la convenzione è che il primo parametro in una classe delegata è l'istanza dell'oggetto mittente) è necessario fornire il nome che si desidera usare la classe EventArgs generata da. Questa operazione viene eseguita con il `[EventArgs]` attributo nella dichiarazione del metodo nel `Model` classe.

Ad esempio:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

La dichiarazione precedente verrà generato un `UIImagePickerImagePickedEventArgs` classe che deriva da EventArgs e Pack entrambi i parametri, il `UIImage` e `NSDictionary`. Il generatore produce questo:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Successivamente espone le seguenti operazioni nel `UIImagePickerController` classe:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```


### <a name="eventnameattribute"></a>EventNameAttribute

Questo attributo viene utilizzato per consentire il generatore di modificare il nome di un evento o proprietà nella classe generata. A volte risulta utile quando il nome del metodo della classe modello appropriato per la classe modello, ma potrebbe sembrare strano nella classe di origine come un evento o proprietà.

Ad esempio, il `UIWebView` utilizza il seguente bit dal `UIWebViewDelegate`:

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

Espone i precedente `LoadingFinished` come metodo di `UIWebViewDelegate`, ma `LoadFinished` dell'evento per associare fino a un `UIWebView`:

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute" />

### <a name="modelattribute"></a>ModelAttribute

Quando si applica il `[Model]` attributo a una definizione di tipo nel contratto di API, il runtime genererà codice speciale che solo esporrà le chiamate ai metodi nella classe se l'utente è stato sovrascritto da un metodo nella classe. Questo attributo viene in genere applicato a tutte le API che eseguono il wrapping di una classe delegata Objective-C.

<a name="NoDefaultValueAttribute" />

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

Specifica che il metodo per il modello non fornisce un valore restituito predefinito.

Inoltre, funziona con il runtime Objective-C rispondendo `false` alla Objective-C runtime richiesta per determinare se il selettore specificato è implementato in questa classe.

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

Il concetto di protocollo Objective-C effettivamente inesistente in c#. I protocolli sono simili alle interfacce c#, ma differiscono in quanto non tutti i metodi e le proprietà dichiarate in un protocollo deve essere implementati dalla classe che si adotta. Invece, alcuni dei metodi e proprietà sono facoltative.

Alcuni protocolli sono in genere usati come le classi del modello, quelli deve essere associati usando il [ `[Model]` ](#ModelAttribute) attributo.

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

Inizia con xamarin. IOS 7.0 un protocollo di nuovo e migliorato funzionalità di associazione è stata incorporata.  Qualsiasi definizione che contiene il `[Protocol]` attributo effettivamente genera tre classi di supporto che consentono di migliorare notevolmente la modalità che si utilizzano i protocolli:

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

Il **implementazione della classe** fornisce una classe astratta completezza che è possibile eseguire l'override di singoli metodi di e ottenere l'indipendenza completa. Tuttavia, a causa di c# non supporta l'ereditarietà multipla, esistono scenari in cui potrebbero richiedere un'altra classe base, ma ancora si desidera implementare un'interfaccia.

Questa opzione è quando generato **definizione dell'interfaccia** è disponibile in.  È un'interfaccia con tutti i metodi richiesti dal protocollo.  In questo modo gli sviluppatori che desiderano implementare il protocollo per implementare semplicemente l'interfaccia.  Il runtime registrerà automaticamente il tipo come adottare il protocollo.

Si noti che sono elencati i metodi richiesti solo l'interfaccia e di esporre i metodi facoltativi.   Ciò significa che le classi che adottano il protocollo otterranno pieno controllo dei tipi per i metodi necessari, ma saranno necessario ricorrere al tipizzazione debole (manualmente utilizzando gli attributi di esportazione e corrispondere alla firma) per i metodi facoltativi del protocollo.

Per rendere più semplice utilizzare un'API che utilizza i protocolli, lo strumento di associazione anche produrrà una classe di metodo di estensioni che espone tutti i metodi facoltativi.   Ciò significa che fino a quando si utilizza un'API, sarà in grado di gestire i protocolli come con tutti i metodi.

Se si desidera utilizzare le definizioni di protocollo dell'API, è necessario scrivere scheletro interfacce vuote nella definizione di API.   Se si desidera utilizzare il Protocollo1 in un'API, è necessario eseguire questa operazione:

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

Il precedente è necessaria in quanto in fase di associazione di `IMyProtocol` non esiste, vale a dire perché è necessario fornire un'interfaccia vuota.

### <a name="adopting-protocol-generated-interfaces"></a>Adozione delle interfacce generate protocollo

Ogni volta che si implementa una delle interfacce generate per i protocolli, simile al seguente:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

L'implementazione per i metodi di interfaccia automaticamente viene esportato con il nome corretto, pertanto è equivalente a questa:

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

Mentre si associano tipi Objective-C esistenti che sono stati dichiarati come adottare un protocollo, è possibile incorporare il protocollo direttamente. A tale scopo, dichiarare semplicemente il protocollo come un'interfaccia senza alcuna [ `[BaseType]` ](#BaseTypeAttribute) attributo ed elencare il protocollo nell'elenco delle interfacce di base per l'interfaccia.

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

Gli attributi in questa sezione verranno applicati a singoli membri di un tipo: proprietà e le dichiarazioni di metodo.


### <a name="alignattribute"></a>AlignAttribute

Utilizzato per specificare il valore di allineamento per i tipi restituiti di proprietà. Alcune proprietà accettano puntatori agli indirizzi che devono essere allineati a determinati limiti (in xamarin. IOS in questo caso, ad esempio con alcuni `GLKBaseEffect` allineato proprietà che devono essere 16 byte). È possibile utilizzare questa proprietà per decorare i metodi get e utilizzare il valore di allineamento. In genere utilizzato con il `OpenTK.Vector4` e `OpenTK.Matrix4` tipi quando è integrato con le API di Objective-C.

Esempio:

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```


### <a name="appearanceattribute"></a>AppearanceAttribute

Il `[Appearance]` attributo è limitato a iOS 5, in cui è stata introdotta la gestione di aspetto.

Il `[Appearance]` attributo può essere applicato a qualsiasi metodo o proprietà che fanno parte di `UIAppearance` framework. Quando questo attributo viene applicato a una proprietà in una classe o metodo, verrà diretta del generatore di associazione per creare una classe fortemente tipizzata aspetto che viene utilizzata per definire lo stile tutte le istanze di questa classe o le istanze che corrispondono a determinati criteri.

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

Il precedente genera il codice seguente in UIToolbar:

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

Utilizzare il `[AutoReleaseAttribute]` sui metodi e proprietà per eseguire il wrapping la chiamata del metodo al metodo in un `NSAutoReleasePool`.

Sono disponibili alcuni metodi che restituiscono valori che vengono aggiunti per impostazione predefinita in Objective-C `NSAutoReleasePool`. Per impostazione predefinita, questi diventano per il thread `NSAutoReleasePool`, ma poiché xamarin. IOS mantiene anche un riferimento a oggetti, purché si trova l'oggetto gestito, potrebbe non si desidera mantenere un riferimento aggiuntivo `NSAutoReleasePool` cui solo ottenere svuotate finché il thread Restituisce il controllo al thread successivo o si torna indietro per il ciclo principale.

Questo attributo viene applicato, ad esempio su proprietà pesanti (ad esempio `UIImage.FromFile`) che restituisce gli oggetti che sono stati aggiunti per il valore predefinito `NSAutoReleasePool`. Senza questo attributo, le immagini verranno conservate fino a quando il thread non ha restituito un controllo per il ciclo principale. UF il thread è stata una qualche forma di downloader sfondo che è sempre attivo e in attesa di lavoro, le immagini sarebbero mai essere rilasciate.

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

Il `[ForcedTypeAttribute]` è utilizzato per attivare la creazione di un tipo gestito, anche se l'oggetto restituito non gestito non corrisponde al tipo descritto nella definizione di associazione.

Ciò è utile quando il tipo descritto in un'intestazione corrisponde il tipo restituito del metodo nativo, ad esempio richiedere la seguente definizione Objective-C da `NSURLSession`:

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

In cui viene indicato chiaramente che verrà restituito un `NSURLSessionDownloadTask` istanza, ma ancora è **restituisce** un `NSURLSessionTask`, una superclasse e pertanto non è convertibile in `NSURLSessionDownloadTask`. Poiché si trovi in un contesto di tipo safe un `InvalidCastException` verrà eseguito.

È conforme con la descrizione di intestazione e di evitare il `InvalidCastException`, `[ForcedTypeAttribute]` viene utilizzato.

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

Il `[ForcedTypeAttribute]` accetta inoltre un valore booleano denominato `Owns` ovvero `false` per impostazione predefinita `[ForcedType (owns: true)]`. Il proprietario di parametro viene utilizzato per seguire il [criteri di proprietà](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html) per **Foundation Core** oggetti.

Il `[ForcedTypeAttribute]` sono valide solo su parametri, proprietà e valore restituito.

<a name="BindAsAttribute" />

### <a name="bindasattribute"></a>BindAsAttribute

Il `[BindAsAttribute]` consente l'associazione `NSNumber`, `NSValue` e `NSString`(enum) in tipi c# più accurati. L'attributo può essere utilizzato per creare una migliore, più accurato, l'API di .NET tramite l'API nativa.

È possibile decorare i metodi (per il valore restituito), parametri e proprietà con `BindAs`. L'unica restrizione è che il membro **non devono** trovarsi all'interno di un `[Protocol]` o [ `[Model]` ](#ModelAttribute) interfaccia.

Ad esempio:

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Genererebbe:

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

I seguenti tipi di dati c# sono supportati per essere incapsulate da/in `NSValue`:

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

I seguenti tipi di dati c# sono supportati per essere incapsulate da/in `NSNumber`:

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

[`[BindAs]`](#BindAsAttribute) funziona con disposizioni [enumerazioni supportato da una costante NSString](#enum-attributes) pertanto è possibile creare una migliore API .NET, ad esempio:

```csharp
[BindAs (typeof (CAScroll))]
[Export ("supportedScrollMode")]
NSString SupportedScrollMode { get; set; }
```

Genererebbe:

```csharp
[Export ("supportedScrollMode")]
CAScroll SupportedScrollMode { get; set; }
```

Abbiamo gestirà la `enum`  <->  `NSString` conversione solo se il valore enum fornito digitare per [ `[BindAs]` ](#BindAsAttribute) è [supportato da una costante NSString](#enum-attributes).

#### <a name="arrays"></a>Matrici

[`[BindAs]`](#BindAsAttribute) supporta inoltre le matrici di uno qualsiasi dei tipi supportati, è ad esempio la definizione dell'API seguente:

```csharp
[return: BindAs (typeof (CAScroll []))]
[Export ("getScrollModesAt:")]
NSString [] GetScrollModes ([BindAs (typeof (CGRect []))] NSValue [] rects);
```

Genererebbe:

```csharp
[Export ("getScrollModesAt:")]
CAScroll? [] GetScrollModes (CGRect [] rects) { ... }
```

Il `rects` parametro verrà incapsulato in un `NSArray` che contiene un `NSValue` per ogni `CGRect` e verrà visualizzato in una matrice di `CAScroll?` che è stato creato utilizzando i valori dell'oggetto restituito `NSArray` contenente `NSStrings`.

<a name="BindAttribute" />

### <a name="bindattribute"></a>BindAttribute

Il `[Bind]` attributo ha due utilizzi uno quando applicato a un metodo o dichiarazione di proprietà e un altro quando applicata al singolo getter o setter in una proprietà.

Quando viene utilizzato per un metodo o proprietà, l'effetto del `[Bind]` attributo consiste nel generare un metodo che richiama il selettore specificato. Ma il metodo generato risulta non è decorato con il [ `[Export]` ](#ExportAttribute) attributo, il che significa che non può partecipare si esegue l'override di metodo. In genere viene utilizzato in combinazione con il `[Target]` attributo per l'implementazione di metodi di estensione Objective-C.

Ad esempio:

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

Quando utilizzato in un metodo Get o set, il `[Bind]` attributo viene utilizzato per modificare le impostazioni predefinite dedotte dal generatore di codice durante la generazione di nomi di selettore Objective-C getter e setter per una proprietà. Per impostazione predefinita quando si contrassegna una proprietà con il nome `fooBar`, il generatore genererebbero un `fooBar` esportazione per il metodo di richiamo e `setFooBar:` per il metodo di impostazione. In alcuni casi, Objective-C non seguono questa convenzione, in genere cambiano il nome di metodo di richiamo sia `isFooBar`.
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

Solo disponibili in xamarin. IOS 6.3 e successive.

Questo attributo può essere applicato ai metodi che accettano un gestore di completamento come ultimo argomento.

È possibile utilizzare il `[Async]` attributo nei metodi il cui ultimo argomento è un callback.  Quando si applica questo a un metodo, il generatore di associazione genererà una versione di tale metodo con il suffisso `Async`.  Se il callback non accetta parametri, il valore restituito sarà una `Task`, se il callback accetta un parametro, il risultato sarà un `Task<T>`.

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

Nell'esempio verrà generato il metodo asincrono, dove `FileLoading` contiene le proprietà per accedere sia `files` e `byteCount`:

```csharp
Task<FileLoading> LoadFile (string file);
```

Se l'ultimo parametro del metodo di callback è un `NSError`, quindi generato `Async` metodo controllerà se il valore non è null e se è questo il caso, il metodo asincrono generato imposterà l'eccezione di attività.

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

Il precedente genera il seguente metodo async:

```csharp
Task<string> UploadAsync (string file);
```

E in caso di errore, l'attività risultante avrà l'eccezione impostata su un `NSErrorException` che include il valore risultante `NSError`.

#### <a name="asyncattributeresulttype"></a>AsyncAttribute.ResultType

Utilizzare questa proprietà per specificare il valore per la restituzione di `Task` oggetto.   Questo parametro accetta un tipo esistente, pertanto deve essere definito in una delle definizioni di api di base.

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

Utilizzare questa proprietà per specificare il valore per la restituzione di `Task` oggetto.   Questo parametro accetta il nome del nome del tipo desiderato, il generatore produrrà una serie di proprietà, uno per ogni parametro che accetta il callback.

#### <a name="asyncattributemethodname"></a>AsyncAttribute.MethodName

Utilizzare questa proprietà per personalizzare il nome dei metodi async generato.   Il valore predefinito consiste nell'utilizzare il nome del metodo e aggiungere il testo "Async", è possibile utilizzare questo metodo per modificare questa impostazione predefinita.

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

Questo attributo viene applicato a parametri di stringa o le proprietà della stringa indica al generatore di codice di non utilizzare la stringa di zero copia marshalling per questo parametro e invece di creare una nuova istanza di NSString dalla stringa di c#.
Questo attributo è necessaria solo per le stringhe se si specifica che il generatore di utilizzare il marshalling delle stringhe zero copia utilizzando il `--zero-copy` opzione della riga di comando o l'impostazione dell'attributo a livello di assembly `ZeroCopyStringsAttribute`.

Questa operazione è necessaria nei casi in cui la proprietà viene dichiarata in Objective-C per essere un `retain` oppure `assign` proprietà anziché una `copy` proprietà. Queste in genere si verificano nelle librerie di terze parti che sono stati erroneamente "ottimizzate" dagli sviluppatori. In generale, `retain` oppure `assign` `NSString` proprietà non sono corrette dopo `NSMutableString` o classi derivate da utente di `NSString` può comportare la modifica del contenuto delle stringhe senza conoscerne il codice della libreria, rilievo leggermente il applicazione. In genere ciò si verifica a causa dell'ottimizzazione prematura.

Di seguito viene illustrato due di tali proprietà in Objective-c:

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```


### <a name="disposeattribute"></a>DisposeAttribute

Quando si applica il `[DisposeAttribute]` a una classe, si fornisce un frammento di codice che verrà aggiunto al `Dispose()` implementazione del metodo della classe.

Poiché il `Dispose` metodo vengono generato automaticamente il `bmac-native` e `btouch-native` strumenti, è necessario utilizzare il `[Dispose]` attributo per inserire codice nell'oggetto generato `Dispose` implementazione del metodo.

Ad esempio:

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute" />

### <a name="exportattribute"></a>ExportAttribute

Il `[Export]` attributo viene utilizzato per contrassegnare un metodo o proprietà per essere esposti al runtime Objective-C. Questo attributo è condivisa tra i runtime di xamarin. IOS e Xamarin.Mac effettivi e lo strumento di associazione. Per i metodi, il parametro viene passato verbatim nel codice generato per le proprietà, un getter e setter esportazioni vengono generate in base a una dichiarazione di base (vedere la sezione di [ `[BindAttribute]` ](#BindAttribute) per informazioni su come modificare il comportamento dello strumento di associazione).

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

Il [selettore](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html) rappresenta il nome del metodo Objective-C sottostante o della proprietà da associare.

#### <a name="exportattributeargumentsemantic"></a>ExportAttribute.ArgumentSemantic

<a name="FieldAttribute" />

### <a name="fieldattribute"></a>FieldAttribute

Questo attributo viene utilizzato per esporre una variabile globale C come un campo che viene caricato su richiesta ed esposta al codice c#. In genere questa operazione è necessaria per ottenere i valori delle costanti che sono definiti in C o Objective-C e che entrambi i token utilizzati in alcune API può essere oppure i cui valori sono opachi e devono essere utilizzati come-è dal codice utente.

Sintassi:

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

Il `symbolName` è il simbolo C il collegamento. Per impostazione predefinita questo verrà caricato da una raccolta il cui nome è derivato dallo spazio dei nomi in cui è definito il tipo. Se non si tratta di libreria in cui viene ricercato il simbolo, è necessario passare il `libraryName` parametro. Se si sta collegando una libreria statica, utilizzare `__Internal` come il `libraryName` parametro.

Le proprietà generate sono sempre statiche.

Proprietà contrassegnata con l'attributo di campo possono essere dei tipi seguenti:

* `NSString`
* `NSArray`
* `nint` / `int` / `long`
* `nuint` / `uint` / `ulong`
* `nfloat` / `float`
* `double`
* `CGSize`
* `System.IntPtr`
* Enumerazioni

Metodi di impostazione non sono supportati per [enumerazioni supportato da costanti NSString](#enum-attributes), ma possono essere associate manualmente se necessario.

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

Il `[Internal]` attributo può essere applicato ai metodi o proprietà e ha l'effetto di contrassegnare il codice generato con il `internal` parola chiave c# rendere il codice accessibile solo al codice nell'assembly generato. Questa è in genere usata per nascondere le API che sono troppo basso livello o forniscono un'API pubblica non ottimale che si desidera migliorare al momento o per le API che non sono supportate dal generatore e richiedono alcuni-codifica manuale.

Quando si progetta l'associazione, che in genere sarebbero nascondere il metodo o proprietà utilizzando l'attributo e specificare un nome diverso per il metodo o proprietà e quindi sul file di supporto complementare c#, aggiungere un wrapper fortemente tipizzato che espone il funzionalità sottostante.

Ad esempio:

```csharp
[Internal]
[Export ("setValue:forKey:")]
void _SetValueForKey (NSObject value, NSObject key);

[Internal]
[Export ("getValueForKey:")]
NSObject _GetValueForKey (NSObject key);
```

Nel file di supporto, è possibile che alcuni codice simile al seguente:

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

L'attributo contrassegna il campo sottostante per una proprietà a essere annotato con .NET `[ThreadStatic]` attributo. Ciò è utile se il campo è una variabile di thread statica.

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions (xamarin. IOS 6.0.6)

Questo attributo consentirà un metodo supporto nativo (Objective-C) le eccezioni.
Anziché chiamare `objc_msgSend` direttamente, la chiamata verrà inviata tramite un trampoline personalizzato che intercetta le eccezioni ObjectiveC e ne esegue il marshalling in eccezioni gestite.

Attualmente solo alcuni `objc_msgSend` sono supportate le firme (verrà indicato se una firma non è supportata quando il collegamento native di un'applicazione che utilizza l'associazione ha esito negativo con un monotouch_ mancante *_objc_msgSend* simbolo), ma più può essere aggiunta alla richiesta.


### <a name="newattribute"></a>NewAttribute

Questo attributo viene applicato ai metodi e proprietà per il generatore di generare il `new` parola chiave prima della dichiarazione.

Consente di evitare gli avvisi del compilatore quando il metodo stesso o il nome di proprietà è stato introdotto in una sottoclasse già esistenti in una classe base.

<a name="NotificationAttribute" />

### <a name="notificationattribute"></a>NotificationAttribute

È possibile applicare questo attributo per i campi che producono il generatore fortemente tipizzata helper di una classe di notifiche.

Questo attributo può essere usato senza argomenti per le notifiche che non trasportano alcun payload, oppure è possibile specificare un `System.Type` che fa riferimento a un'altra interfaccia di definizione dell'API, in genere con il nome che termina con "EventArgs". Il generatore Trasforma l'interfaccia in una classe che rappresenta una sottoclasse `EventArgs` e includerà tutte le proprietà elencate. Il [ `[Export]` ](#ExportAttribute) attributo deve essere utilizzato nel `EventArgs` classe per elencare il nome della chiave utilizzata per cercare il dizionario di Objective-C per recuperare il valore.

Ad esempio:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

Il codice precedente genera una classe annidata `MyClass.Notifications` con i metodi seguenti:

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
      public static NSObject ObserveDidStart (NSObject objectToObserve, EventHandler<NSNotificationEventArgs> handler)
   }
}
```

Gli utenti del codice quindi facilmente richiedere notifiche registrate per il [NSDefaultCenter](https://developer.xamarin.com/api/property/Foundation.NSNotificationCenter.DefaultCenter/) utilizzando codice simile al seguente:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

O impostare un oggetto specifico da osservare. Se si passa `null` per `objectToObserve` questo metodo si comporterà come proprio altri peer.

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

Il valore restituito da `ObserveDidStart` può essere utilizzato per arrestare facilmente la ricezione delle notifiche, simile al seguente:

```csharp
token.Dispose ();
```

Oppure è possibile chiamare [NSNotification.DefaultCenter.RemoveObserver](https://developer.xamarin.com/api/member/Foundation.NSNotificationCenter.RemoveObserver/p/Foundation.NSObject//) e passare il token. Se la notifica contiene parametri, è necessario specificare un helper `EventArgs` interfaccia, simile al seguente:

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

Sopra verrà generato un `MyScreenChangedEventArgs` classe con il `ScreenX` e `ScreenY` le proprietà che recupero i dati dal [NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/) dizionario usando i tasti `ScreenXKey` e `ScreenYKey` rispettivamente e applicare le conversioni appropriate. Il `[ProbePresence]` attributo viene utilizzato per il generatore di verificare se la chiave viene impostata nel `UserInfo`, anziché tentare di estrarre il valore. Viene utilizzato per i casi in cui la presenza della chiave è il valore (in genere per i valori booleani).

In questo modo è possibile scrivere codice simile al seguente:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

In alcuni casi, non è costante non associato al valore passato nel dizionario.  Apple talvolta vengono utilizzate costanti di simboli pubblici e a volte vengono utilizzate costanti di stringa.  Per impostazione predefinita il [ `[Export]` ](#ExportAttribute) fornito l'attributo `EventArgs` classe utilizzerà il nome specificato come simbolo pubblico per essere cercati in fase di esecuzione.  Se non è il caso, e invece deve essere ricercato come una costante di stringa, quindi passare il `ArgumentSemantic.Assign` valore per l'attributo di esportazione.

**Novità di xamarin. IOS 8.4**

In alcuni casi, le notifiche inizierà vita senza argomenti, pertanto l'utilizzo di [ `[Notification]` ](#NotificationAttribute) senza argomenti non è accettabile.  Ma in alcuni casi, verranno introdotte parametri per la notifica.  Per supportare questo scenario, l'attributo può essere applicato più volte.

Se si sta sviluppando un'associazione e si desidera evitare l'interruzione nel codice utente esistente, viene attivata una notifica esistente da:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

In una versione che elenca l'attributo notifica due volte, simile al seguente:

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

Quando questo viene applicato a una proprietà contrassegna la proprietà in modo che il valore `null` per poter essere assegnati a esso. Ciò è valido solo per i tipi di riferimento.

Quando questo viene applicato a un parametro in una firma del metodo indica che il parametro specificato può essere null e che è non necessario eseguire alcun controllo per il passaggio `null` valori.

Se il tipo di riferimento non dispone di questo attributo, lo strumento di associazione genererà un controllo per il valore assegnato prima di passarlo a Objective-C e genererà un controllo che verrà generata una `ArgumentNullException` se il valore assegnato `null`.

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

È possibile usare questo attributo per inserire codice da inserire dopo che sono stati convalidati i parametri di input, ma prima le chiamate di codice in Objective-C.

Esempio:

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

È possibile usare questo attributo per inserire codice da inserire prima che i parametri vengono convalidati nel metodo generato.

Esempio:

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>PostGetAttribute

Indica il generatore di associazione per richiamare la proprietà specificata da questa classe per recuperare un valore da esso.

Questa proprietà viene in genere utilizzata per aggiornare la cache che fa riferimento a oggetti di riferimento che consentono di mantenere l'oggetto grafico a cui fa riferimento. In genere viene visualizzato nel codice che include operazioni come l'installazione. Questo metodo viene utilizzato in modo che dopo gli elementi vengono aggiunti o rimossi che è possibile aggiornare la cache interna per assicurarsi che si sono mantenendo riferimenti gestiti per gli oggetti che vengono effettivamente in uso. Questo è possibile perché lo strumento di associazione genera un campo sottostante per tutti gli oggetti di riferimento in una determinata associazione.

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

In questo caso, il `Dependencies` verrà richiamata dopo l'aggiunta o rimozione di dipendenze dalla proprietà di `NSOperation` oggetto, assicurando che abbiamo un grafico che rappresenta l'effettivo caricato gli oggetti, impedendo le perdite di memoria come danneggiamento della memoria.

### <a name="postsnippetattribute"></a>PostSnippetAttribute

È possibile usare questo attributo per inserire alcune il codice sorgente c# da inserire dopo che il codice ha richiamato il metodo sottostante Objective-C

Esempio:

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>ProxyAttribute

Questo attributo viene applicato a valori restituiti per contrassegnarli come oggetti proxy. Alcuni oggetti proxy restituito Objective-C API che non possono essere differenziati dai binding dell'utente. L'effetto di questo attributo è per contrassegnare l'oggetto come un `DirectBinding` oggetto. In uno scenario in Xamarin.Mac, è possibile visualizzare il [discussione sul bug](https://bugzilla.novell.com/show_bug.cgi?id=670844).

### <a name="retainlistattribute"></a>RetainListAttribute

Indica il generatore di mantenere un riferimento gestito per il parametro o rimuovere un riferimento interno per il parametro. Viene utilizzato per mantenere gli oggetti a cui fa riferimento.

Sintassi:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Se il valore di `doAdd` è true, il parametro viene aggiunto per il `__mt_{0}_var List<NSObject>;`. Dove `{0}` viene sostituito con il determinato `listName`. Nella classe parziale complementare per l'API, è necessario dichiarare il campo sottostante.

Per un esempio vedere [foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) e [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute (xamarin. IOS 6.0)

Questo può essere applicato per restituire i tipi per indicare che il generatore deve chiamare `Release` per l'oggetto prima di restituirlo. Questo è necessario solo quando un metodo fornisce un oggetto memorizzato (in contrapposizione a un oggetto autoreleased, ovvero lo scenario più comune)

Esempio:

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

Inoltre, questo attributo viene propagato al codice generato, in modo da indicare al runtime di xamarin. IOS che è necessario conservare l'oggetto restituito per Objective-C da tale funzione.


### <a name="sealedattribute"></a>SealedAttribute

Indica il generatore di contrassegnare il metodo generato come sealed. Se questo attributo viene omesso, il valore predefinito consiste nel generare un metodo virtuale (un metodo virtuale, un metodo astratto o override a seconda di come vengono usati altri attributi).

<a name="StaticAttribute" />

### <a name="staticattribute"></a>StaticAttribute

Quando il `[Static]` attributo viene applicato a una proprietà o metodo, verrà generato un metodo statico o una proprietà. Se questo attributo viene omesso, il generatore produce un metodo di istanza o una proprietà.


### <a name="transientattribute"></a>TransientAttribute

Utilizzare questo attributo flag di proprietà i cui valori sono temporanei, vale a dire, gli oggetti che vengono creati temporaneamente da iOS, ma non sono di lunga durata. Quando questo attributo viene applicato a una proprietà, il generatore non crea un campo sottostante per questa proprietà, il che significa che la classe gestita non mantiene un riferimento all'oggetto.

<a name="WrapAttribute" />

### <a name="wrapattribute"></a>WrapAttribute

Nella progettazione delle associazioni, Xamarin.iOS/Xamarin.Mac il `[Wrap]` attributo viene utilizzato per eseguire il wrapping di un oggetto con tipizzazione debole a un oggetto fortemente tipizzato. Ciò si verifica principalmente con gli oggetti delegato Objective-C che in genere vengono dichiarati come di tipo `id` o `NSObject`. La convenzione utilizzata da xamarin. IOS e Xamarin.Mac consiste nell'esporre tali origini dati o delegati come di tipo `NSObject` e vengono denominati usando la convenzione "Weak" + il nome esposto. Un' `id delegate` proprietà da Objective-C viene esposto come un `NSObject WeakDelegate { get; set; }` proprietà nel file di contratto API.

Ma in genere il valore assegnato a questo delegato è di tipo sicuro, superficie di attacco di tipo sicuro e applicare il `[Wrap]` attributo, ciò significa che gli utenti possono scegliere di utilizzare tipi deboli se hanno bisogno di un controllo di fine o se è necessario ricorrere al tric di basso livello localmente oppure è possibile utilizzare la proprietà fortemente tipizzata per la maggior parte del lavoro.

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

Ecco come viene utilizzata la versione con tipizzazione debole del delegato:

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

E questo è come l'utente potrebbe usare la versione fortemente tipizzata, si noti che l'utente si avvale di # type system e utilizza la parola chiave override per dichiarare il suo scopo e che non è a manualmente il metodo con `[Export]`, dal momento che è stata eseguita supporta l'associazione per l'utente:

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

Utilizzare il `[Wrap]` attributo è per supportare la versione fortemente tipizzata di metodi.  Ad esempio:

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

I membri generati da `[Wrap]` non `virtual` per impostazione predefinita, se è necessario un `virtual` membro può essere impostata `true` facoltativo `isVirtual` parametro.

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

In questa sezione vengono descritti gli attributi che è possibile applicare ai parametri in una definizione di metodo, nonché `[NullAttribute]` che si applica a una proprietà nel suo complesso.

<a name="BlockCallback" />

### <a name="blockcallback"></a>BlockCallback

Questo attributo viene applicato a tipi di parametro nelle dichiarazioni di delegato c# per notificare lo strumento di associazione che il parametro in questione sia conforme al blocco di Objective-C, la convenzione di chiamata e deve effettuare il marshalling, in questo modo.

In genere viene utilizzato per i callback che sono definiti come segue in Objective-c:

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Vedere anche: [CCallback](#CCallback).

<a name="CCallback" />

### <a name="ccallback"></a>CCallback

Questo attributo viene applicato a tipi di parametro nelle dichiarazioni di delegato c# per notificare lo strumento di associazione che il parametro in questione sia conforme alla convenzione di chiamata C ABI funzione puntatore e deve effettuare il marshalling, in questo modo.

In genere viene utilizzato per i callback che sono definiti come segue in Objective-c:

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Vedere anche: [BlockCallback](#BlockCallback).

### <a name="params"></a>Params

È possibile utilizzare il `[Params]` l'ultimo parametro di matrice della definizione di un metodo per il generatore di inserire "ref" nella definizione di attributo.   In questo modo l'associazione facilitare l'autorizzazione per i parametri facoltativi.

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

Questo è il vantaggio che non richiede agli utenti di creare una matrice esclusivamente per il passaggio di elementi.

<a name="plainstring" />

### <a name="plainstring"></a>PlainString

È possibile utilizzare il `[PlainString]` attributo davanti a parametri di stringa per indicare il generatore di associazione per passare la stringa come una stringa C, anziché passare il parametro come un `NSString`.

Utilizzare la maggior parte delle API Objective-C `NSString` parametri, ma un numero limitato di API di esporre un `char *` API per il passaggio di stringhe, anziché il `NSString` variazione.
Utilizzare `[PlainString]` in questi casi.

Ad esempio, le seguenti dichiarazioni di Objective-C:

```csharp
- (void) setText: (NSString *) theText;
- (void) logMessage: (char *) message;
```

Deve essere associato simile al seguente:

```csharp
[Export ("setText:")]
void SetText (string theText);

[Export ("logMessage:")]
void LogMessage ([PlainString] string theText);
```

### <a name="retainattribute"></a>RetainAttribute

Indica il generatore di mantenere un riferimento al parametro specificato. Il generatore fornirà l'archivio di backup per questo campo oppure è possibile specificare un nome (il `WrapName`) per archiviare il valore. Ciò è utile per mantenere un riferimento a un oggetto gestito che viene passato come parametro per Objective-C e quando si sa che Objective-C manterrà solo questa copia dell'oggetto. Ad esempio, un'API simile `SetDisplay (SomeObject)` utilizzerà questo attributo come è probabile che il SetDisplay può visualizzare solo un oggetto alla volta. Se si desidera tenere traccia di più di un oggetto (ad esempio, per un'API simile dello Stack) si utilizzerebbe il `[RetainList]` attributo.

Sintassi:

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```


### <a name="retainlistattribute"></a>RetainListAttribute

Indica il generatore di mantenere un riferimento gestito per il parametro o rimuovere un riferimento interno per il parametro. Viene utilizzato per mantenere gli oggetti a cui fa riferimento.

Sintassi:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Se il valore di `doAdd` è true, il parametro viene aggiunto per il `__mt_{0}_var List<NSObject>`. Dove `{0}` viene sostituito con il determinato `listName`. Nella classe parziale complementare per l'API, è necessario dichiarare il campo sottostante.

Per un esempio vedere [foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) e [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)


### <a name="transientattribute"></a>TransientAttribute

Questo attributo viene applicato a parametri e viene utilizzato solo durante la transizione da Objective-C in c#.  Durante le transizioni di vari Objective-C `NSObject` parametri vengono incapsulati in una rappresentazione dell'oggetto gestita.

Il runtime consente di accettare un riferimento all'oggetto nativo e di mantenere il riferimento finché non viene eliminato l'ultimo riferimento gestito all'oggetto e il Garbage Collector ha la possibilità di eseguire.

In alcuni casi, è importante per il runtime in c# per non mantenere un riferimento all'oggetto nativo.  Talvolta ciò si verifica quando il codice nativo sottostante è associato un comportamento speciale per il ciclo di vita del parametro.  Ad esempio: il distruttore per il parametro verrà eseguita un'operazione di pulizia o eliminare alcune risorse preziose.

Questo attributo informa il runtime che desiderate direttamente l'oggetto in fase di eliminazione se possibile la restituzione al Objective-C dal metodo sovrascritto.

La regola è semplice: se il runtime era necessario creare una nuova rappresentazione gestita dall'oggetto nativo, alla fine della funzione, verrà eliminato il conteggio di gestione per l'oggetto nativo, e la proprietà di Handle dell'oggetto gestito verrà cancellata.   Ciò significa che se mantenuto un riferimento all'oggetto gestito, che fanno riferimento non sarà più inutile (chiamata di metodi su di essa verrà generata un'eccezione).

Se l'oggetto passato non è stato creato o se è già una rappresentazione gestita in attesa dell'oggetto, l'eliminazione forzata non viene eseguita. 


## <a name="property-attributes"></a>Attributi delle proprietà

<a name="NotImplementedAttribute" />

### <a name="notimplementedattribute"></a>NotImplementedAttribute

Questo attributo viene usato per supportare un linguaggio Objective-C in una proprietà con un metodo di richiamo è stato introdotto in una classe di base e una sottoclasse modificabile introduce un setter.

Poiché c# non supporta questo modello, la classe di base deve avere sia il metodo di impostazione e il metodo di richiamo e può usare una sottoclasse di [OverrideAttribute](#OverrideAttribute).

Questo attributo viene utilizzato solo in setter di proprietà e viene usato per supportare il linguaggio modificabile in Objective-C.

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

Mapping `NSString` costanti per valori di enumerazione è un modo semplice per creare una migliore API .NET. È:

* consente il completamento del codice essere più utile, mostrando **solo** i valori corretti per l'API.
* Aggiunge l'indipendenza dai tipi, non è possibile utilizzare un altro `NSString` costante in un contesto errato; e
* Consente di nascondere alcune costanti, rendendo il completamento del codice mostra un elenco di API più breve senza perdere la funzionalità.

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

Dalla definizione di associazione precedente verrà creato il generatore di `enum` stesso e verrà creata anche una `*Extensions` tipo statico che include i metodi di conversione di due modi tra i valori di enumerazione e il `NSString` costanti. Ciò significa che le costanti rimane disponibile per gli sviluppatori, anche se non fanno parte dell'API.

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

È possibile decorare **uno** valore enum con questo attributo. Che diventerà la costante viene restituita se il valore enum non è noto.

Nell'esempio precedente:

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

Se nessun valore di enumerazione è decorato un `NotSupportedException` verrà generata.

### <a name="errordomainattribute"></a>ErrorDomainAttribute

Codici di errore vengono associati come valori di enumerazione. È in genere un dominio di errore relativa e non è sempre facile trovare quello che si applica (o se esiste uno anche definito).

È possibile utilizzare questo attributo per associare il dominio di errore dell'enumerazione stessa.

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

Questo è lo stesso `[Field]` attributo utilizzato per le costanti all'interno di tipo. Può anche essere utilizzato all'interno di enumerazioni per mappare un valore con una costante specifico.

Oggetto `null` valore può essere utilizzato per specificare quale valore di enumerazione deve essere restituito se un `null` `NSString` costante specificata.

Nell'esempio precedente:

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

Se non `null` valore è presente una `ArgumentNullException` verrà generata.

## <a name="global-attributes"></a>Attributi globali

Gli attributi globali vengono applicati sia utilizzando la `[assembly:]` modificatore di attributo, ad esempio il [ `[LinkWithAttribute]` ](#LinkWithAttribute) o possono essere usati ovunque, ad esempio il [ `[Lion]` ](#SinceAndLionAttributes) e [ `[Since]` ](#SinceAndLionAttributes) attributi.

<a name="LinkWithAttribute" />

### <a name="linkwithattribute"></a>LinkWithAttribute

Si tratta di un attributo a livello di assembly che consente agli sviluppatori di specificare il flag di collegamento è necessario riutilizzare una libreria associata senza imporre al consumer della libreria di configurare manualmente il gcc_flags e argomenti mtouch aggiuntivi passati a una raccolta.

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

Questo attributo viene applicato a livello di assembly, ad esempio, questo è ciò che il [CorePlot associazioni](https://github.com/mono/monotouch-bindings/tree/master/CorePlot) utilizzare:

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

Quando si utilizza il `[LinkWith]` attributo specificato `libraryName` è incorporato nell'assembly risultante, consentendo agli utenti di fornire una singola DLL che contiene sia le dipendenze non gestite come i flag della riga di comando necessari per utilizzare correttamente il libreria di xamarin. IOS.

È anche possibile non fornire un `libraryName`, nel qual caso il `LinkWith` attributo può essere utilizzato per specificare solo il flag del linker aggiuntive:

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>Costruttori LinkWithAttribute

Tali costruttori consentono di specificare la libreria per il collegamento con e incorporare l'assembly risulta, le destinazioni supportate che supporta la libreria e qualsiasi flag facoltativo che sono necessari per il collegamento con la libreria.

Si noti che il `LinkTarget` argomento viene dedotto dal xamarin. IOS e non dovrà essere impostata.

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

Il `ForceLoad` proprietà viene utilizzata per stabilire o meno il `-force_load` collegamento flag viene utilizzato per collegare la libreria nativa. Per il momento, questo deve essere sempre true.

#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute.Frameworks

Se la raccolta associata ha un requisito di disco rigido in alcun Framework (diverso da `Foundation` e `UIKit`), è necessario impostare il `Frameworks` proprietà a una stringa contenente un elenco delimitato da virgole dei framework piattaforma richiesta. Ad esempio, se si sta associando una libreria che richiede `CoreGraphics` e `CoreText`, impostare il `Frameworks` proprietà `"CoreGraphics CoreText"`.

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

Impostare questa proprietà su true se il file eseguibile risultante deve essere compilata utilizzando un compilatore C++ anziché il valore predefinito, ovvero un compilatore C. Utilizzare questa opzione se la raccolta che si esegue l'associazione è stata scritta in C++.

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute.LibraryName

Il nome della libreria non gestita da aggregare. Si tratta di un file con estensione "con estensione" e può contenere codice oggetto per più piattaforme (ad esempio, ARM e x86 per il simulatore).

Le versioni precedenti di xamarin. IOS selezionata la `LinkTarget` proprietà per determinare la piattaforma di raccolta supportata, ma questo viene ora rilevata automaticamente e `LinkTarget` proprietà viene ignorata.

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

Il `LinkerFlags` stringa fornisce un modo per gli autori delle associazioni specificare alcun flag linker aggiuntive necessarie quando la libreria nativa di collegamento all'applicazione.

Ad esempio, se la libreria nativa richiede libxml2 e zlib, è necessario impostare il `LinkerFlags` da string a `"-lxml2 -lz"`.

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute.LinkTarget

Le versioni precedenti di xamarin. IOS selezionata la `LinkTarget` proprietà per determinare la piattaforma di raccolta supportata, ma questo viene ora rilevata automaticamente e `LinkTarget` proprietà viene ignorata.

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

Impostare questa proprietà su true se la raccolta che si sta collegando richiede la libreria di gestione delle eccezioni GCC (gcc_eh)

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute.SmartLink

Il `SmartLink` proprietà deve essere impostata su true per consentire di determinare xamarin se `ForceLoad` è necessario o meno.

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

Il `WeakFrameworks` proprietà funziona esattamente come il `Frameworks` proprietà, tranne che in fase di collegamento, il `-weak_framework` identificatore viene passato a gcc per ognuno dei Framework elencati.

`WeakFrameworks` rende possibile per le raccolte e alle applicazioni di collegamento in modo debole da Framework della piattaforma in modo che può facoltativamente usarli se sono disponibili ma non accettano una dipendenza rigida su di essi, che risulta utile se la raccolta è progettata per aggiungere ulteriori funzionalità in più recente versioni di iOS. Per ulteriori informazioni sul collegamento debole, vedere la documentazione di Apple in [collegamento debole](http://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html).

Sarebbero buoni candidati per il collegamento debole `Frameworks` come account, `CoreBluetooth`, `CoreImage`, `GLKit`, `NewsstandKit` e `Twitter` poiché sono disponibili solo in iOS 5.

<a name="SinceAndLionAttributes" />

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute (iOS) e LionAttribute (macOS)

Utilizzare il `[Since]` attributo per contrassegnare le API come dotate viene introdotto in un determinato punto nel tempo. L'attributo deve essere utilizzato solo per contrassegnare i tipi e metodi che possono causare un problema di runtime se la classe sottostante, metodo o proprietà non è disponibile.

Sintassi:

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

Non deve in genere applicata per le enumerazioni, vincoli o nuove strutture di esempio quelli non causare un errore di runtime se vengono eseguite in un dispositivo con una versione precedente del sistema operativo.

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

Il `[Lion]` è applicato l'attributo nello stesso modo, ma per i tipi introdotti con Lion. Il motivo per utilizzare `[Lion]` rispetto al numero di versione più specifico che viene utilizzato per iOS è tale iOS viene modificato molto spesso, mentre le versioni principali di OS X si verifichino raramente e risulta più semplice da ricordare il sistema operativo da loro nome in codice di base al numero di versione

### <a name="adviceattribute"></a>AdviceAttribute

Usare questo attributo per consentire agli sviluppatori un suggerimento su altre API che potrebbero essere più utile per impedire l'uso.   Ad esempio, se si fornisce una versione fortemente tipizzata di un'API, è possibile utilizzare questo attributo sull'attributo con tipizzazione debole per indirizzare lo sviluppatore all'API migliorato.

Le informazioni di questo attributo viene visualizzate nella documentazione e gli strumenti possono essere sviluppati per fornire suggerimenti utente su come migliorare

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

Solo disponibili in xamarin. IOS 5.4 e successive.

Questo attributo indica al generatore di che l'associazione per questa libreria specifica (se applicate con `[assembly:]`) o di tipo deve essere utilizzato il marshalling della stringa di zero-copia veloce. Questo attributo equivale a passare l'opzione della riga di comando `--zero-copy` al generatore.

Quando si utilizza copia zero per le stringhe, il generatore in modo efficace utilizza la stessa stringa c# della stringa che utilizza Objective-C evitando la creazione di un nuovo `NSString` oggetto ed evitando di copiare i dati dalle stringhe c# alla stringa di Objective-C. L'unico svantaggio dell'utilizzo delle stringhe copia Zero è necessario verificare che le proprietà della stringa che si esegue il wrapping che possono essere contrassegnati come `retain` oppure `copy` ha la `[DisableZeroCopy]` set di attributi. Questa operazione è necessario perché l'handle per le stringhe zero copia viene allocato nello stack e non è valido dopo la funzione restituita.

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

È inoltre possibile applicare l'attributo a livello di assembly e verrà applicata a tutti i tipi dell'assembly:

```csharp
[assembly:ZeroCopyStrings]
```

## <a name="strongly-typed-dictionaries"></a>Dizionari fortemente tipizzati

Con xamarin. IOS 8.0 è stato introdotto il supporto per la creazione di classi fortemente tipizzate facilmente che eseguono il wrapping `NSDictionaries`.

Mentre è sempre stato possibile utilizzare il [DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/) del tipo di dati con un'API manuale, è molto più semplice eseguire questa operazione.  Per ulteriori informazioni, vedere [superfici tipi sicuro](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types).

<a name="StrongDictionary" />

### <a name="strongdictionary"></a>StrongDictionary

Quando questo attributo viene applicato a un'interfaccia, il generatore produrrà una classe con lo stesso nome di interfaccia che deriva da [DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/) e la trasforma ogni proprietà definita nell'interfaccia in un fortemente tipizzati getter e setter per il dizionario.

Questo genera automaticamente una classe che può essere implementata da un oggetto esistente `NSDictionary` o che sono stati creati nuovi.

Questo attributo accetta un parametro, il nome della classe contenente le chiavi utilizzate per accedere agli elementi nel dizionario.   Per impostazione predefinita, ogni proprietà nell'interfaccia con l'attributo verrà ricerca un membro nel tipo specificato per un nome con il suffisso "Key".

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

Nell'esempio precedente, il `MyOption` classe produrrà una proprietà di stringa per `Name` che utilizzerà il `MyOptionKeys.NameKey` come chiave nel dizionario per recuperare una stringa.   E utilizzerà il `MyOptionKeys.AgeKey` come chiave nel dizionario per recuperare un `NSNumber` che contiene un tipo int.

Se si desidera utilizzare una chiave diversa, è possibile utilizzare l'attributo di esportazione sulla proprietà, ad esempio:

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

Sono supportati i seguenti tipi di dati nel `StrongDictionary` definizione:

|Tipo di interfaccia in c#|`NSDictionary` Tipo di archiviazione|
|---|---|
|`bool`|`Boolean` archiviati in un `NSNumber`|
|Valori di enumerazione|intero archiviati in un `NSNumber`|
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
|Linguaggio c# `Array` di `NSObject`|`NSArray`|
|Linguaggio c# `Array` delle enumerazioni|`NSArray` contenente `NSNumber` valori|
