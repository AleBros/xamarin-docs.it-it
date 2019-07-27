---
title: Ottimizzazioni di compilazione
description: Questo documento illustra le varie ottimizzazioni applicate in fase di compilazione per le app Novell. iOS e Novell. Mac.
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: conceptdev
ms.author: crdun
ms.date: 04/16/2018
ms.openlocfilehash: a14845646fb400285adac8579af4b15db61e047b
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511157"
---
# <a name="build-optimizations"></a>Ottimizzazioni di compilazione

Questo documento illustra le varie ottimizzazioni applicate in fase di compilazione per le app Novell. iOS e Novell. Mac.

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>Rimuovere UIApplication. EnsureUIThread/NSApplication. EnsureUIThread

Rimuove le chiamate a [UIApplication. EnsureUIThread][1] (per Novell. iOS) `NSApplication.EnsureUIThread` o (per Novell. Mac).

Questa ottimizzazione cambierà il tipo di codice seguente:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    global::UIKit.UIApplication.EnsureUIThread ();
    // ...
}
```

negli elementi seguenti:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    // ...
}
```

Questa ottimizzazione richiede che il linker sia abilitato e venga applicato solo ai metodi con l' `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

Per impostazione predefinita, è abilitato per le build di rilascio.

Il comportamento predefinito può essere sostituito passando `--optimize=[+|-]remove-uithread-checks` a mTouch/MMP.

[1]: https://docs.microsoft.com/dotnet/api/UIKit.UIApplication.EnsureUIThread

## <a name="inline-intptrsize"></a>Inline IntPtr. size

Incorpora il valore costante di `IntPtr.Size` in base alla piattaforma di destinazione.

Questa ottimizzazione cambierà il tipo di codice seguente:

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

negli elementi seguenti (quando si compila per una piattaforma a 64 bit):

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

Questa ottimizzazione richiede che il linker sia abilitato e venga applicato solo ai metodi con l' `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

Per impostazione predefinita, è abilitato se la destinazione è una singola architettura o per l'assembly della piattaforma (**Novell. iOS. dll**, **Novell. TVOS. dll**, **Novell. watchos. dll** o **Novell. Mac. dll**).

Se la destinazione è costituita da più architetture, questa ottimizzazione creerà assembly diversi per la versione a 32 bit e la versione a 64 bit dell'app ed è necessario che entrambe le versioni siano incluse nell'app, aumentando in questo modo le dimensioni dell'app finale anziché diminuendo è.

Il comportamento predefinito può essere sostituito passando `--optimize=[+|-]inline-intptr-size` a mTouch/MMP.

## <a name="inline-nsobjectisdirectbinding"></a>Inline NSObject. il reindirizzamento

`NSObject.IsDirectBinding`Proprietà dell'istanza che determina se una particolare istanza è di tipo wrapper o meno (un tipo di wrapper è un tipo gestito mappato a un tipo nativo. ad esempio, il tipo `UIKit.UIView` gestito viene mappato al `UIView` tipo nativo. il contrario è un tipo di utente , in questo caso `class MyUIView : UIKit.UIView` è un tipo di utente.

È necessario stabilire il valore di `IsDirectBinding` quando si chiama in Objective-C, perché il valore determina la versione di `objc_msgSend` da usare.

Dato solo il codice seguente:

```csharp
class UIView : NSObject {
    public virtual string SomeProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class NSUrl : NSObject {
    public virtual string SomeOtherProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class MyUIView : UIView {
}
```

È possibile determinare che nel `UIView.SomeProperty` valore di `IsDirectBinding` non è una costante e non può essere inline:

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

Tuttavia, è possibile esaminare tutti i tipi nell'app e determinare che non esistono tipi che ereditano da `NSUrl`ed è quindi possibile inlinere il `IsDirectBinding` valore a una costante `true`:

```csharp
void myURL = new NSUrl ();
Console.WriteLine (myURL.SomeOtherProperty); // prints 'true'
// There's no way to make SomeOtherProperty print anything but 'true', since there are no NSUrl subclasses.
```

In particolare, questa ottimizzazione cambierà il tipo di codice seguente (questo è il codice di associazione `NSUrl.AbsoluteUrl`per):

```csharp
if (IsDirectBinding) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

nelle seguenti condizioni (quando è possibile determinare che non sono presenti sottoclassi di `NSUrl` nell'app):

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

Questa ottimizzazione richiede che il linker sia abilitato e venga applicato solo ai metodi con l' `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

È sempre abilitato per impostazione predefinita per Novell. iOS ed è sempre disabilitato per impostazione predefinita per Novell. Mac (perché è possibile caricare in modo dinamico gli assembly in Novell. Mac, non è possibile determinare che una particolare classe non sia mai sottoclassata).

Il comportamento predefinito può essere sostituito passando `--optimize=[+|-]inline-isdirectbinding` a mTouch/MMP.

## <a name="inline-runtimearch"></a>Runtime inline. Arch

Questa ottimizzazione cambierà il tipo di codice seguente:

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

negli elementi seguenti (durante la compilazione per il dispositivo):

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

Questa ottimizzazione richiede che il linker sia abilitato e venga applicato solo ai metodi con l' `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

È sempre abilitato per impostazione predefinita per Novell. iOS (non è disponibile per Novell. Mac).

Il comportamento predefinito può essere sostituito passando `--optimize=[+|-]inline-runtime-arch` a mTouch.

## <a name="dead-code-elimination"></a>Eliminazione del codice inattivo

Questa ottimizzazione cambierà il tipo di codice seguente:

```csharp
if (true) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

in

```csharp
Console.WriteLine ("Doing this");
```

Valuterà anche i confronti costanti, come indicato di seguito:

```csharp
if (8 == 8) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

e determinare che l'espressione `8 == 8` è sempre true e ridurla a:

```csharp
Console.WriteLine ("Doing this");
```

Si tratta di un'ottimizzazione potente se utilizzata insieme alle ottimizzazioni di incorporamento, perché può trasformare il tipo di codice seguente (questo è il codice di associazione `NFCIso15693ReadMultipleBlocksConfiguration.Range`per):

```csharp
NSRange ret;
if (IsDirectBinding) {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret (out ret, this.Handle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    }
} else {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret (out ret, this.SuperHandle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    }
}
return ret;
```

in questo (quando si compila per un dispositivo a 64 bit e quando è possibile verificare che non vi siano `NFCIso15693ReadMultipleBlocksConfiguration` sottoclassi nell'app):

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

Il compilatore AOT è già in grado di eliminare codice inattivo come questo, ma questa ottimizzazione viene eseguita all'interno del linker, il che significa che il linker è in grado di verificare che siano presenti più metodi che non vengono più utilizzati e che quindi possono essere rimossi (a meno che non vengano usati altrove) :

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

Questa ottimizzazione richiede che il linker sia abilitato e venga applicato solo ai metodi con l' `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

È sempre abilitato per impostazione predefinita (quando il linker è abilitato).

Il comportamento predefinito può essere sostituito passando `--optimize=[+|-]dead-code-elimination` a mTouch/MMP.

## <a name="optimize-calls-to-blockliteralsetupblock"></a>Ottimizzare le chiamate a BlockLiteral. SetupBlock

Il runtime di Novell. iOS/Mac deve comprendere la firma del blocco quando si crea un blocco Objective-C per un delegato gestito. Potrebbe trattarsi di un'operazione piuttosto costosa. Questa ottimizzazione consente di calcolare la firma del blocco in fase di compilazione e modificare il linguaggio il `SetupBlock` per chiamare un metodo che accetta la firma come argomento. In questo modo si evita la necessità di calcolare la firma in fase di esecuzione.

I benchmark mostrano che questa operazione accelera la chiamata di un blocco con un fattore da 10 a 15.

Verrà trasformato il [codice](https://github.com/xamarin/xamarin-macios/blob/018f7153441d9d7e0f58e2046f39eeb46f1ff480/src/UIKit/UIAccessibility.cs#L198-L211)seguente:

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlock (callback, completionHandler);
    // ...
}
```

in

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlockImpl (callback, completionHandler, true, "v@?B");
    // ...
}
```

Questa ottimizzazione richiede che il linker sia abilitato e venga applicato solo ai metodi con l' `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

Questa funzionalità è abilitata per impostazione predefinita quando si usa il registrar statico (in Novell. iOS il registrar statico è abilitato per impostazione predefinita per le compilazioni del dispositivo e in Novell. Mac il registrar statico è abilitato per impostazione predefinita per le build di rilascio).

Il comportamento predefinito può essere sostituito passando `--optimize=[+|-]blockliteral-setupblock` a mTouch/MMP.

## <a name="optimize-support-for-protocols"></a>Ottimizzazione del supporto per i protocolli

Il runtime di Novell. iOS/Mac richiede informazioni sul modo in cui i tipi gestiti implementano i protocolli Objective-C. Queste informazioni vengono archiviate in interfacce (e attributi su queste interfacce), che non sono un formato molto efficiente, né sono semplici da usare per il linker.

Un esempio è che queste interfacce archiviano informazioni su tutti i membri del `[ProtocolMember]` protocollo in un attributo, che tra l'altro contengono riferimenti ai tipi di parametro di tali membri. Ciò significa che la semplice implementazione di tale interfaccia farà in modo che il linker mantenga tutti i tipi usati nell'interfaccia, anche per i membri facoltativi che l'app non chiama né implementa.

Questa ottimizzazione farà in modo che il registrar statico memorizzi le informazioni necessarie in un formato efficiente che usa poca memoria facile e veloce da trovare in fase di esecuzione.

Viene inoltre insegnato al linker che non deve necessariamente mantenere queste interfacce, né gli attributi correlati.

Questa ottimizzazione richiede l'abilitazione sia del linker che del registrar statico.

In Novell. iOS questa ottimizzazione è abilitata per impostazione predefinita quando il linker e il registrar statico sono abilitati.

In Novell. Mac questa ottimizzazione non è mai abilitata per impostazione predefinita, perché Novell. Mac supporta il caricamento dinamico degli assembly e tali assembly potrebbero non essere noti in fase di compilazione e pertanto non ottimizzati.

Il comportamento predefinito può essere sostituito passando `--optimize=-register-protocols` a mTouch/MMP.

## <a name="remove-the-dynamic-registrar"></a>Rimuovere il registrar dinamico

Il runtime di Novell. iOS e Novell. Mac includono il supporto per la [registrazione dei tipi gestiti](~/ios/internals/registrar.md) con il runtime di Objective-C. Questa operazione può essere eseguita in fase di compilazione oppure in fase di esecuzione (o parzialmente in fase di compilazione e in fase di esecuzione), ma se viene completata in fase di compilazione, significa che il codice di supporto per eseguirlo in fase di esecuzione può essere rimosso. Ciò comporta una riduzione significativa delle dimensioni delle app, in particolare per le app più piccole, ad esempio le estensioni o le app watchos.

Questa ottimizzazione richiede l'abilitazione sia del registrar statico che del linker.

Il linker tenterà di determinare se è sicuro rimuovere il registrar dinamico e, in caso affermativo, tenterà di rimuoverlo.

Poiché Novell. Mac supporta il caricamento dinamico di assembly in fase di esecuzione (che non erano noti in fase di compilazione), è Impossibile determinare in fase di compilazione se si tratta di un'ottimizzazione sicura. Questo significa che questa ottimizzazione non è mai abilitata per impostazione predefinita per le app Novell. Mac.

Il comportamento predefinito può essere sostituito passando `--optimize=[+|-]remove-dynamic-registrar` a mTouch/MMP.

Se per rimuovere il registrar dinamico viene eseguito l'override dell'impostazione predefinita, il linker emetterà avvisi se rileva che non è sicuro (ma il registrar dinamico verrà comunque rimosso).

## <a name="inline-runtimedynamicregistrationsupported"></a>Runtime inline. DynamicRegistrationSupported

Incorpora il valore di `Runtime.DynamicRegistrationSupported` come determinato in fase di compilazione.

Se il registrar dinamico viene rimosso (vedere la [rimozione dell'ottimizzazione del registrar dinamico](#remove-the-dynamic-registrar) ), si `false` tratta di un valore costante. in `true` caso contrario, si tratta di un valore costante.

Questa ottimizzazione cambierà il tipo di codice seguente:

```csharp
if (Runtime.DynamicRegistrationSupported) {
    Console.WriteLine ("do something");
} else {
    throw new Exception ("dynamic registration is not supported");
}
```

Quando viene rimosso il registrar dinamico:

```csharp
throw new Exception ("dynamic registration is not supported");
```

Quando non viene rimosso il registrar dinamico:

```csharp
Console.WriteLine ("do something");
```

Questa ottimizzazione richiede che il linker sia abilitato e venga applicato solo ai metodi con l' `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

È sempre abilitato per impostazione predefinita (quando il linker è abilitato).

Il comportamento predefinito può essere sostituito passando `--optimize=[+|-]inline-dynamic-registration-supported` a mTouch/MMP.

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>Pre-calcola i metodi per creare delegati gestiti per i blocchi Objective-C

Quando Objective-C chiama un selettore che accetta un blocco come parametro e quindi il codice gestito ha sottoposto a override tale metodo, il runtime di Novell. iOS/Novell. Mac deve creare un delegato per tale blocco.

Il codice di associazione generato dal generatore di associazioni includerà `[BlockProxy]` un attributo che specifica il tipo con un `Create` metodo che può eseguire questa operazione.

Dato il seguente codice Objective-C:

```objc
@interface ObjCBlockTester : NSObject {
}
-(void) classCallback: (void (^)())completionHandler;
-(void) callClassCallback;
@end

@implementation ObjCBlockTester
-(void) classCallback: (void (^)())completionHandler
{
}

-(void) callClassCallback
{
    [self classCallback: ^()
    {
        NSLog (@"called!");
    }];
}
@end
```

e il seguente codice di associazione:

```csharp
[BaseType (typeof (NSObject))]
interface ObjCBlockTester
{
    [Export ("classCallback:")]
    void ClassCallback (Action completionHandler);
}
```

il generatore produrrà:

```csharp
[Register("ObjCBlockTester", true)]
public unsafe partial class ObjCBlockTester : NSObject {
    // unrelated code...

    [Export ("callClassCallback")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public virtual void CallClassCallback ()
    {
        if (IsDirectBinding) {
            ApiDefinition.Messaging.void_objc_msgSend (this.Handle, Selector.GetHandle ("callClassCallback"));
        } else {
            ApiDefinition.Messaging.void_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("callClassCallback"));
        }
    }
    
    [Export ("classCallback:")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public unsafe virtual void ClassCallback ([BlockProxy (typeof (Trampolines.NIDActionArity1V0))] System.Action completionHandler)
    {
        // ...
        
    }
}

static class Trampolines
{
    [UnmanagedFunctionPointerAttribute (CallingConvention.Cdecl)]
    [UserDelegateType (typeof (System.Action))]
    internal delegate void DActionArity1V0 (IntPtr block);

    static internal class SDActionArity1V0 {
        static internal readonly DActionArity1V0 Handler = Invoke;
        
        [MonoPInvokeCallback (typeof (DActionArity1V0))]
        static unsafe void Invoke (IntPtr block) {
            var descriptor = (BlockLiteral *) block;
            var del = (System.Action) (descriptor->Target);
            if (del != null)
                del (obj);
        }
    }
    
    internal class NIDActionArity1V0 {
        IntPtr blockPtr;
        DActionArity1V0 invoker;
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe NIDActionArity1V0 (BlockLiteral *block)
        {
            blockPtr = _Block_copy ((IntPtr) block);
            invoker = block->GetDelegateForBlock<DActionArity1V0> ();
        }
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        ~NIDActionArity1V0 ()
        {
            _Block_release (blockPtr);
        }
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe static System.Action Create (IntPtr block)
        {
            if (block == IntPtr.Zero)
                return null;
            if (BlockLiteral.IsManagedBlock (block)) {
                var existing_delegate = ((BlockLiteral *) block)->Target as System.Action;
                if (existing_delegate != null)
                    return existing_delegate;
            }
            return new NIDActionArity1V0 ((BlockLiteral *) block).Invoke;
        }
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        unsafe void Invoke ()
        {
            invoker (blockPtr);
        }
    }
}
```

Quando si chiama `[ObjCBlockTester callClassCallback]`Objective-C, il runtime Novell. iOS/Novell. Mac esamina l' `[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]` attributo sul parametro. Viene quindi ricercato il `Create` metodo su quel tipo e viene chiamato il metodo per creare il delegato.

Questa ottimizzazione troverà il `Create` metodo in fase di compilazione e il registrar statico genererà il codice che cerca il metodo in fase di esecuzione usando i token di metadati usando invece l'attributo e la reflection (questo è molto più veloce e consente anche al linker per rimuovere il codice runtime corrispondente, rendendo più piccola l'app.

Se MMP/mTouch non riesce a trovare il `Create` metodo, verrà visualizzato un avviso MT4174/MM4174 e la ricerca verrà eseguita in fase di esecuzione.
La ragione più probabile è la scrittura manuale del codice di associazione `[BlockProxy]` senza gli attributi obbligatori.

Questa ottimizzazione richiede l'abilitazione del registrar statico.

È sempre abilitato per impostazione predefinita (purché sia abilitato il registrar statico).

Il comportamento predefinito può essere sostituito passando `--optimize=[+|-]static-delegate-to-block-lookup` a mTouch/MMP.
