---
title: Ottimizzazioni della build
description: Questo documento illustra le diverse ottimizzazioni che vengono applicate in fase di compilazione per le app xamarin. IOS e xamarin. Mac.
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: conceptdev
ms.author: crdun
ms.date: 04/16/2018
ms.openlocfilehash: f1aa805b9b7a16ad1e8af573cf4170f885eb0197
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58870352"
---
# <a name="build-optimizations"></a>Ottimizzazioni della build

Questo documento illustra le diverse ottimizzazioni che vengono applicate in fase di compilazione per le app xamarin. IOS e xamarin. Mac.

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>Rimuovere UIApplication.EnsureUIThread / NSApplication.EnsureUIThread

Rimuove le chiamate a [UIApplication.EnsureUIThread] [ 1] (per xamarin. IOS) o `NSApplication.EnsureUIThread` (per xamarin. Mac).

Questa ottimizzazione cambierà il tipo di codice seguente:

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

Per impostazione predefinita di che è abilitata per la versione build.

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]remove-uithread-checks` di mtouch/mmp.

[1]: https://docs.microsoft.com/dotnet/api/UIKit.UIApplication.EnsureUIThread

## <a name="inline-intptrsize"></a>Inline IntPtr.Size

Il valore costante Inlines di `IntPtr.Size` in base alla piattaforma di destinazione.

Questa ottimizzazione cambierà il tipo di codice seguente:

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

in quanto segue (durante la creazione di una piattaforma a 64 bit):

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

Questa ottimizzazione è necessario il linker deve essere abilitata e viene applicato solo ai metodi con il `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

Per impostazione predefinita è abilitata se la destinazione è un'unica architettura o per l'assembly di piattaforma (**DLL**, **Xamarin.TVOS.dll**, **Xamarin.WatchOS.dll** o **Xamarin**).

Se la destinazione più architetture, questa ottimizzazione creerà assembly diversi per la versione a 32 bit e la versione a 64 bit dell'app e dovranno essere inclusi nell'app, in modo efficace l'aumento delle dimensioni finali app anziché diminuire entrambe le versioni si tratta.

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]inline-intptr-size` di mtouch/mmp.

## <a name="inline-nsobjectisdirectbinding"></a>inline NSObject.IsDirectBinding

`NSObject.IsDirectBinding` è una proprietà dell'istanza che determina se una particolare istanza è di un tipo di wrapper o meno (un tipo di wrapper è un tipo gestito che viene eseguito il mapping a un tipo nativo, per istanza gestita `UIKit.UIView` tipo viene mappato all'oggetto nativo `UIView` tipo - l'opposto è un tipo di utente in questo caso `class MyUIView : UIKit.UIView` sarebbe un tipo di utente).

È necessario conoscere il valore della `IsDirectBinding` quando si chiama in Objective-C, perché il valore determina quale versione di `objc_msgSend` da usare.

Assegnato solo il codice seguente:

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

È possibile determinare che in `UIView.SomeProperty` il valore di `IsDirectBinding` non è una costante e non può essere impostato come inline:

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

Tuttavia, è possibile esaminare tutti i tipi nell'app e determinare che non sono disponibili tipi che ereditano da `NSUrl`, e pertanto è opportuno inline le `IsDirectBinding` valore a una costante `true`:

```csharp
void myURL = new NSUrl ();
Console.WriteLine (myURL.SomeOtherProperty); // prints 'true'
// There's no way to make SomeOtherProperty print anything but 'true', since there are no NSUrl subclasses.
```

In particolare, questa ottimizzazione cambierà il tipo di codice seguente (si tratta del codice di associazione per `NSUrl.AbsoluteUrl`):

```csharp
if (IsDirectBinding) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

in quanto segue (quando è possibile determinare che non esistono Nessun sottoclassi di `NSUrl` nell'app):

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

Questa ottimizzazione è necessario il linker deve essere abilitata e viene applicato solo ai metodi con il `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

È sempre abilitata per impostazione predefinita per xamarin. IOS e sempre disabilitata per impostazione predefinita per xamarin. Mac (poiché è possibile caricare dinamicamente assembly in xamarin. Mac, non è possibile determinare che una determinata classe non è una sottoclasse mai).

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]inline-isdirectbinding` di mtouch/mmp.

## <a name="inline-runtimearch"></a>inline Runtime.Arch

Questa ottimizzazione cambierà il tipo di codice seguente:

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

in quanto segue (durante la compilazione per il dispositivo):

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

Questa ottimizzazione è necessario il linker deve essere abilitata e viene applicato solo ai metodi con il `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

È sempre abilitato per impostazione predefinita per xamarin. IOS (non è disponibile per xamarin. Mac).

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]inline-runtime-arch` di mtouch.

## <a name="dead-code-elimination"></a>Eliminazione di codice non utilizzato

Questa ottimizzazione cambierà il tipo di codice seguente:

```csharp
if (true) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

into:

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

e determina che l'espressione `8 == 8` è un sempre true e ridurlo a:

```csharp
Console.WriteLine ("Doing this");
```

Ciò costituisce un'ottimizzazione potenti quando usato con le ottimizzazioni l'incorporamento, perché possibile trasformare il tipo di codice seguente (si tratta del codice di associazione per `NFCIso15693ReadMultipleBlocksConfiguration.Range`):

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

In questo (durante la creazione di un dispositivo a 64 bit e quando anche in grado di assicurare vi sono alcun `NFCIso15693ReadMultipleBlocksConfiguration` sottoclassi nell'app):

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

Il compilatore AOT è già in grado di eliminare il codice inattivo simile al seguente, ma questa ottimizzazione viene eseguita all'interno di linker, il che significa che il linker in grado di vedere che sono disponibili diversi metodi che non vengono più usati e pertanto potrebbero essere rimosso (solo se usato in un' posizione) :

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

Questa ottimizzazione è necessario il linker deve essere abilitata e viene applicato solo ai metodi con il `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

È sempre abilitato per impostazione predefinita (quando il linker è abilitato).

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]dead-code-elimination` di mtouch/mmp.

## <a name="optimize-calls-to-blockliteralsetupblock"></a>Ottimizzare le chiamate a BlockLiteral.SetupBlock

Il runtime Xamarin.iOS/Mac deve conoscere la firma di blocco durante la creazione di un blocco di Objective-C per la creazione di delegato. Potrebbe trattarsi di un'operazione piuttosto dispendiosa. Questa ottimizzazione calcolerà la firma di blocco in fase di compilazione e modificare il livello di integrità per chiamare un `SetupBlock` metodo che accetta la firma come argomento invece. In questo modo si evita la necessità di calcolare la firma in fase di esecuzione.

Statistiche mostrano che questo modo si velocizza la chiamata a un blocco di un fattore di 10 e 15.

Trasformerà seguenti [codice](https://github.com/xamarin/xamarin-macios/blob/018f7153441d9d7e0f58e2046f39eeb46f1ff480/src/UIKit/UIAccessibility.cs#L198-L211):

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlock (callback, completionHandler);
    // ...
}
```

into:

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlockImpl (callback, completionHandler, true, "v@?B");
    // ...
}
```

Questa ottimizzazione è necessario il linker deve essere abilitata e viene applicato solo ai metodi con il `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

Si è abilitato per impostazione predefinita quando si usa il programma di registrazione statico (in xamarin. IOS le registrar statico è abilitato per impostazione predefinita per compilazioni del dispositivo e in xamarin. Mac le registrar statico è abilitato per impostazione predefinita per il rilascio si basa).

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]blockliteral-setupblock` di mtouch/mmp.

## <a name="optimize-support-for-protocols"></a>Ottimizzare il supporto per protocolli

Il runtime Xamarin.iOS/Mac necessita di informazioni sui protocolli implementa Objective-C di tipi gestiti. Queste informazioni vengono archiviate in interfacce e gli attributi su tali interfacce, che non è un formato molto efficiente e non è facile integrazione del linker.

Un esempio è che queste interfacce archiviano le informazioni su tutti i membri di protocollo un `[ProtocolMember]` attributo, che tra l'altro contiene i riferimenti ai tipi di parametro di tali membri. Ciò significa che è sufficiente che implementa tale interfaccia renderà il linker mantenere tutti i tipi usati in tale interfaccia, anche per i membri facoltativi dell'app mai chiama o implementa.

Questa ottimizzazione renderà il programma di registrazione statico archiviare qualsiasi informazione necessarie in un formato efficiente che utilizza memoria minimo che è semplice e rapido individuare in fase di esecuzione.

Inoltre ti spiegherà il linker che non è necessario mantenere queste interfacce, né uno qualsiasi degli attributi correlati.

Questa ottimizzazione richiede che il linker e il programma di registrazione statico deve essere abilitata.

In xamarin. IOS questa ottimizzazione è abilitata per impostazione predefinita, quando sono abilitati sia il linker e il programma di registrazione statico.

In xamarin. Mac questa ottimizzazione non è mai abilitata per impostazione predefinita, perché xamarin. Mac supporta il caricamento degli assembly in modo dinamico e tali assembly potrebbero non avere stato noto in fase di compilazione (e pertanto non è ottimizzato).

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=-register-protocols` di mtouch/mmp.

## <a name="remove-the-dynamic-registrar"></a>Rimuovere il programma di registrazione dinamica

Di xamarin. IOS sia il runtime di xamarin. Mac include il supporto per [la registrazione di tipi gestiti](https://developer.xamarin.com/guides/ios/advanced_topics/registrar/) nel runtime Objective-C. Può essere eseguita in fase di compilazione o in fase di esecuzione (o parzialmente in fase di compilazione e il resto in fase di esecuzione), ma se completamente avviene in fase di compilazione, significa che il codice di supporto per tale operazione in fase di esecuzione può essere rimossi. Ciò comporta una riduzione significativa delle dimensioni dell'app, in particolare per le app più piccole, ad esempio estensioni o App watchOS.

Questa ottimizzazione richiede che il programma di registrazione statico e il linker deve essere abilitata.

Il linker tenterà di stabilire se è possibile rimuovere le registrar dinamico e, se in modo che verrà effettuato un tentativo per rimuoverlo.

Poiché xamarin. Mac supporta in modo dinamico il caricamento di assembly in fase di esecuzione (che non sono noti in fase di compilazione), è possibile determinare in fase di compilazione se ciò costituisce un'ottimizzazione sicura. Ciò significa che questa ottimizzazione non è mai abilitata per impostazione predefinita per le app xamarin. Mac.

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]remove-dynamic-registrar` di mtouch/mmp.

Se il valore predefinito è sottoposto a override per rimuovere il programma di registrazione dinamica, il linker genererà avvisi se rileva che non è sicuro (ma verranno comunque rimosse le registrar dinamico).

## <a name="inline-runtimedynamicregistrationsupported"></a>inline Runtime.DynamicRegistrationSupported

Il valore di proprietà Inlines di `Runtime.DynamicRegistrationSupported` determinata in fase di compilazione.

Se viene rimosso il programma di registrazione dinamica (vedere la [rimuovere il programma di registrazione dinamica](#remove-the-dynamic-registrar) optimization), questa è una costante `false` valore, in caso contrario, è una costante `true` valore.

Questa ottimizzazione cambierà il tipo di codice seguente:

```csharp
if (Runtime.DynamicRegistrationSupported) {
    Console.WriteLine ("do something");
} else {
    throw new Exception ("dynamic registration is not supported");
}
```

in quanto segue quando si rimuove il programma di registrazione dinamica:

```csharp
throw new Exception ("dynamic registration is not supported");
```

in quanto segue quando non viene rimosso il programma di registrazione dinamica:

```csharp
Console.WriteLine ("do something");
```

Questa ottimizzazione è necessario il linker deve essere abilitata e viene applicato solo ai metodi con il `[BindingImpl (BindingImplOptions.Optimizable)]` attributo.

È sempre abilitato per impostazione predefinita (quando il linker è abilitato).

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]inline-dynamic-registration-supported` di mtouch/mmp.

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>Pre-calcola i metodi per creare delegati gestiti per i blocchi di Objective-C

Quando Objective-C chiama un selettore che accetta un blocco come parametro e quindi il codice gestito ha eseguito l'override il metodo, di xamarin. IOS / xamarin. Mac runtime deve creare un delegato per tale blocco.

Il codice di associazione generato dal generatore di binding includerà un `[BlockProxy]` attributo, che specifica il tipo con un `Create` metodo che è possibile eseguire questa operazione.

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

e il codice di associazione seguente:

```csharp
[BaseType (typeof (NSObject))]
interface ObjCBlockTester
{
    [Export ("classCallback:")]
    void ClassCallback (Action completionHandler);
}
```

il generatore produce:

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

Quando chiama Objective-C `[ObjCBlockTester callClassCallback]`, di xamarin. IOS / xamarin. Mac runtime verrà presi in esame il `[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]` attributo sul parametro. Verrà quindi cercare il `Create` metodo su tale tipo e chiamare il metodo per creare il delegato.

Questa ottimizzazione troverà il `Create` metodo in fase di compilazione e il programma di registrazione statico genera il codice che cerca il metodo in fase di esecuzione usando i token di metadati anziché l'attributo e reflection (questo è molto più veloce e consente inoltre il linker Per rimuovere il codice di runtime corrispondenti, rendendo più piccolo dell'app).

Se non riesce a trovare mmp/mtouch il `Create` (metodo), quindi verrà visualizzato un avviso MT4174/MM4174 e la ricerca verrà eseguita in fase di esecuzione invece.
La causa più probabile viene scritta manualmente il codice di associazione senza i necessari `[BlockProxy]` attributi.

Questa ottimizzazione è necessario il programma di registrazione statico deve essere abilitata.

È sempre abilitato per impostazione predefinita (purché le registrar statico è abilitato).

Il comportamento predefinito può essere sostituito dal passaggio `--optimize=[+|-]static-delegate-to-block-lookup` di mtouch/mmp.
