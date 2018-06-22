---
title: Le ottimizzazioni di compilazione
description: Questo documento illustra le varie ottimizzazioni che vengono applicate in fase di compilazione per le app xamarin. IOS e Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: bradumbaugh
ms.author: brumbaug
dateupdated: 04/16/2018
ms.openlocfilehash: abdd1223c0105156580b8f23fc2c020f2f45caa6
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
ms.locfileid: "33918773"
---
# <a name="build-optimizations"></a>Le ottimizzazioni di compilazione

Questo documento illustra le varie ottimizzazioni che vengono applicate in fase di compilazione per le app xamarin. IOS e Xamarin.Mac.

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>Rimuovere UIApplication.EnsureUIThread / NSApplication.EnsureUIThread

Rimuove le chiamate a [UIApplication.EnsureUIThread] [ 1] (per xamarin. IOS) o `NSApplication.EnsureUIThread` (per Xamarin.Mac).

Questa ottimizzazione modificherà il tipo di codice seguente:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    global::UIKit.UIApplication.EnsureUIThread ();
    // ...
}
```

in quanto segue:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    // ...
}
```

Questa ottimizzazione è necessario il linker deve essere abilitata e viene applicato solo ai metodi con il `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

Per impostazione predefinita che è abilitata per la versione build.

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]remove-uithread-checks` a mtouch/mmp.

[1]: https://developer.xamarin.com/api/member/UIKit.UIApplication.EnsureUIThread/

## <a name="inline-intptrsize"></a>inline IntPtr.Size

Incorpora il valore costante di `IntPtr.Size` a seconda della piattaforma di destinazione.

Questa ottimizzazione modificherà il tipo di codice seguente:

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

in quanto segue (durante la compilazione per una piattaforma a 64 bit):

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

Questa ottimizzazione è necessario il linker deve essere abilitata e viene applicato solo ai metodi con il `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

Per impostazione predefinita è abilitato se la destinazione di un'unica architettura o per l'assembly di piattaforma (**Xamarin.iOS.dll**, **Xamarin.TVOS.dll**, **Xamarin.WatchOS.dll** o **Xamarin.Mac.dll**).

Se la destinazione diverse architetture, questa ottimizzazione creerà assembly diversi per la versione a 32 bit e la versione a 64 bit dell'app e dovranno essere incluso nell'app, in modo efficace aumento delle dimensioni app finale anziché diminuire entrambe le versioni .

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]inline-intptr-size` a mtouch/mmp.

## <a name="inline-nsobjectisdirectbinding"></a>inline NSObject.IsDirectBinding

`NSObject.IsDirectBinding` è una proprietà dell'istanza che determina se una particolare istanza è di un tipo di wrapper (un tipo di wrapper è un tipo gestito che viene eseguito il mapping a un tipo nativo; per istanza gestita `UIKit.UIView` mapping nativo del tipo `UIView` tipo - l'opposto è un tipo di utente in questo caso `class MyUIView : UIKit.UIView` sarebbe un tipo di utente).

È necessario conoscere il valore di `IsDirectBinding` quando si chiama in Objective-C, perché il valore determina quale versione di `objc_msgSend` da utilizzare.

Si consideri il seguente codice seguente:

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

È possibile determinare che in `UIView.SomeProperty` il valore di `IsDirectBinding` non è una costante e non può essere resa inline:

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

Tuttavia, è possibile esaminare tutti i tipi nell'app e determinare che non esistono tipi che ereditano da `NSUrl`, e pertanto è consigliabile incorporare il `IsDirectBinding` valore a una costante `true`:

```csharp
void myURL = new NSUrl ();
Console.WriteLine (myURL.SomeOtherProperty); // prints 'true'
// There's no way to make SomeOtherProperty print anything but 'true', since there are no NSUrl subclasses.
```

In particolare, questa ottimizzazione modificherà il tipo di codice seguente (si tratta del codice di associazione per `NSUrl.AbsoluteUrl`):

```csharp
if (IsDirectBinding) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

Nella seguente (quando è possibile determinare che non esistono alcun le sottoclassi di `NSUrl` nell'app):

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

Questa ottimizzazione è necessario il linker deve essere abilitata e viene applicato solo ai metodi con il `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

È sempre abilitata per impostazione predefinita per xamarin. IOS e sempre disabilitata per impostazione predefinita per Xamarin.Mac (poiché è possibile caricare in modo dinamico gli assembly in Xamarin.Mac, non è possibile determinare che una determinata classe non è una sottoclasse mai).

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]inline-isdirectbinding` a mtouch/mmp.

## <a name="inline-runtimearch"></a>inline Runtime.Arch

Questa ottimizzazione modificherà il tipo di codice seguente:

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

Nella seguente (quando si compila per dispositivo):

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

Questa ottimizzazione è necessario il linker deve essere abilitata e viene applicato solo ai metodi con il `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

È sempre abilitata per impostazione predefinita per xamarin. IOS (non è disponibile per Xamarin.Mac).

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]inline-runtime-arch` a mtouch.

## <a name="dead-code-elimination"></a>Eliminazione dei messaggi non recapitabili codice

Questa ottimizzazione modificherà il tipo di codice seguente:

```csharp
if (true) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

in:

```csharp
Console.WriteLine ("Doing this");
```

Viene inoltre valutata confronti costanti, simile al seguente:

```csharp
if (8 == 8) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

e determina che l'espressione `8 == 8` è un sempre true e lo riducono a:

```csharp
Console.WriteLine ("Doing this");
```

Si tratta di una potente ottimizzazione se usato insieme le ottimizzazioni inline, poiché è possibile trasformare il tipo di codice seguente (si tratta del codice di associazione per `NFCIso15693ReadMultipleBlocksConfiguration.Range`):

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

In questo (quando si compila per un dispositivo a 64 bit e quando anche in grado di verificare che siano presenti alcun `NFCIso15693ReadMultipleBlocksConfiguration` sottoclassi nell'app):

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

Il compilatore AOT è già in grado di eliminare i messaggi non recapitabili codice simile al seguente, ma questa ottimizzazione viene eseguita all'interno del linker, il che significa che il linker in grado di visualizzare che sono disponibili diversi metodi che non vengono più utilizzati e pertanto possono essere rimosso (solo se utilizzata in un' posizione) :

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

Questa ottimizzazione è necessario il linker deve essere abilitata e viene applicato solo ai metodi con il `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

È sempre abilitata per impostazione predefinita (quando il linker è abilitato).

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]dead-code-elimination` a mtouch/mmp.

## <a name="optimize-calls-to-blockliteralsetupblock"></a>Ottimizzare le chiamate a BlockLiteral.SetupBlock

Il runtime Xamarin.iOS/Mac deve conoscere la firma di blocco durante la creazione di un blocco di Objective-C per la creazione di delegato. Potrebbe trattarsi di un'operazione alquanto costosa. Questa ottimizzazione calcolare la firma di blocco in fase di compilazione e modificare il codice per chiamare un `SetupBlock` metodo che accetta invece la firma come argomento. In questo modo si evita la necessità di calcolare la firma in fase di esecuzione.

Statistiche mostrano che questo velocizza la chiamata di un blocco di un fattore di 10 e 15.

Trasformerà seguenti [codice](https://github.com/xamarin/xamarin-macios/blob/018f7153441d9d7e0f58e2046f39eeb46f1ff480/src/UIKit/UIAccessibility.cs#L198-L211):

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlock (callback, completionHandler);
    // ...
}
```

in:

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlockImpl (callback, completionHandler, true, "v@?B");
    // ...
}
```

Questa ottimizzazione è necessario il linker deve essere abilitata e viene applicato solo ai metodi con il `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

Quando si utilizza il programma di registrazione statico (in xamarin. IOS registrar statico è abilitato per impostazione predefinita per le compilazioni di dispositivo, in cui il programma di registrazione statico è abilitato per impostazione predefinita per il rilascio Xamarin.Mac Compila) è abilitata per impostazione predefinita.

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]blockliteral-setupblock` a mtouch/mmp.

## <a name="optimize-support-for-protocols"></a>Ottimizzare il supporto per protocolli

Il runtime Xamarin.iOS/Mac necessita di informazioni sui protocolli implementa Objective-C di tipi gestiti. Queste informazioni vengono archiviate nelle interfacce (e gli attributi di queste interfacce), che non è un formato molto efficiente e non è orientato al linker.

Un esempio è che queste interfacce archiviano le informazioni su tutti i membri di protocollo in un `[ProtocolMember]` attributo, ovvero tra l'altro contiene i riferimenti ai tipi di parametro di tali membri. Questo significa semplicemente che implementa tale interfaccia renderà il linker mantenere tutti i tipi utilizzati in tale interfaccia, anche per i membri facoltativi mai chiama o app implementa.

Questa ottimizzazione renderà il registrar statico di archiviare le informazioni necessarie in un formato efficiente che usa la memoria minima che è facile e veloce trovare in fase di esecuzione.

Ma verrà anche insegna al linker che non necessariamente deve mantenere queste interfacce, né uno qualsiasi degli attributi correlati.

Questa ottimizzazione è necessario il linker e il programma di registrazione statico deve essere abilitata.

In xamarin. IOS questa ottimizzazione è abilitata per impostazione predefinita, quando sono abilitati sia il linker e il programma di registrazione statico.

In Xamarin.Mac questa ottimizzazione non è mai abilitata per impostazione predefinita, perché Xamarin.Mac supporta il caricamento degli assembly in modo dinamico e tali assembly potrebbero non avere stato noto in fase di compilazione (e pertanto non è ottimizzata).

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=-register-protocols` a mtouch/mmp.

## <a name="remove-the-dynamic-registrar"></a>Rimuovere il programma di registrazione dinamica

Sia il xamarin. IOS e il runtime Xamarin.Mac includono il supporto per [la registrazione di tipi gestiti](https://developer.xamarin.com/guides/ios/advanced_topics/registrar/) con il runtime Objective-C. Può essere eseguita in fase di compilazione o in fase di esecuzione (o parzialmente in fase di compilazione e il resto in fase di esecuzione), ma se completamente avviene in fase di compilazione, significa che il codice di supporto per l'esecuzione in fase di esecuzione può essere rimossi. Ciò comporta una diminuzione significativa delle dimensioni di app, in particolare per le app più piccole, ad esempio estensioni o watchOS.

Questa ottimizzazione richiesta che il programma di registrazione statico e il linker deve essere abilitata.

Il linker tenterà di determinare se è consigliabile rimuovere la registrazione dinamica e if in modo proverà a rimuoverlo.

Poiché Xamarin.Mac supporta in modo dinamico il caricamento assembly in fase di esecuzione (che non sono stati noto in fase di compilazione), è possibile determinare in fase di compilazione se si tratta di un'ottimizzazione-safe. Ciò significa che questa ottimizzazione non è mai abilitata per impostazione predefinita per le app Xamarin.Mac.

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]remove-dynamic-registrar` a mtouch/mmp.

Se il valore predefinito è sottoposto a override per rimuovere il programma di registrazione dinamica, il linker genererà avvisi se rileva che non è sicuro (ma il programma di registrazione dinamica verrà comunque rimosse).

## <a name="inline-runtimedynamicregistrationsupported"></a>inline Runtime.DynamicRegistrationSupported

Incorpora il valore di `Runtime.DynamicRegistrationSupported` come determinato in fase di compilazione.

Se viene rimosso il registrar dinamico (vedere la [rimuovere il programma di registrazione dinamica](#remove-the-dynamic-registrar) ottimizzazione), si tratta di una costante `false` valore, in caso contrario, è una costante `true` valore.

Questa ottimizzazione modificherà il tipo di codice seguente:

```csharp
if (Runtime.DynamicRegistrationSupported) {
    Console.WriteLine ("do something");
} else {
    throw new Exception ("dynamic registration is not supported");
}
```

in quanto segue quando viene rimossa la registrazione dinamica:

```csharp
throw new Exception ("dynamic registration is not supported");
```

in quanto segue quando si programma di registrazione dinamica non viene rimosso:

```csharp
Console.WriteLine ("do something");
```

Questa ottimizzazione è necessario il linker deve essere abilitata e viene applicato solo ai metodi con il `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

È sempre abilitata per impostazione predefinita (quando il linker è abilitato).

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]inline-dynamic-registration-supported` a mtouch/mmp.

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>Pre-calcola i metodi per creare delegati gestiti per i blocchi di Objective-C

Quando Objective-C chiama un selettore che accetta un blocco come parametro e quindi il codice gestito ha sottoposta a override questo metodo, di xamarin. IOS / runtime Xamarin.Mac deve creare un delegato per tale blocco.

Il codice di associazione generato dal generatore di associazione includerà un `[BlockProxy]` attributo, che specifica il tipo con un `Create` metodo che a tale scopo.

Si consideri il seguente codice Objective-C seguente:

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

e il codice di associazione seguenti:

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

Quando chiama il metodo Objective-C `[ObjCBlockTester callClassCallback]`, di xamarin. IOS / runtime Xamarin.Mac esaminerà il `[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]` attributo sul parametro. Verrà quindi cercare la `Create` metodo su tale tipo e chiamare tale metodo per creare il delegato.

Questa ottimizzazione troverà il `Create` metodo in fase di compilazione e il programma di registrazione statico genera il codice che cerca il metodo in fase di esecuzione utilizzando il token di metadati utilizzando invece l'attributo e reflection (questo è molto più veloce e consente inoltre il linker rimuovere il codice di runtime corrispondente, come ridurre le dimensioni dell'app).

Se mmp/mtouch non riesce a trovare il `Create` (metodo), quindi verrà visualizzato un avviso MT4174/MM4174 e la ricerca verrà eseguita in fase di esecuzione.
La causa più probabile manualmente viene scritto il codice di associazione senza i necessari `[BlockProxy]` attributi.

Questa ottimizzazione è necessario il programma di registrazione statico deve essere abilitata.

È sempre abilitata per impostazione predefinita (fino a quando il programma di registrazione statico è abilitato).

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]static-delegate-to-block-lookup` a mtouch/mmp.
