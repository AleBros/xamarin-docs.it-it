---
title: Marshalling delle eccezioni in Xamarin.iOS
description: Questo documento descrive come usare le eccezioni native e gestite in un'app Xamarin.iOS. Vengono illustrati i problemi che possono verificarsi e una soluzione per questi problemi.
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/05/2017
ms.openlocfilehash: ca3d4dfcd773a4f236ffbfd715cb53b514f6e2a3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032518"
---
# <a name="exception-marshaling-in-xamarinios"></a>Marshalling delle eccezioni in Xamarin.iOS

_Xamarin.iOS contiene nuovi eventi che consentono di rispondere alle eccezioni, in particolare nel codice nativo._

Il codice gestito e Objective-C sono supportati per le eccezioni di runtime (clausole try/catch/finally).

Tuttavia, le rispettive implementazioni sono diverse, il che significa che le librerie di runtime (il runtime di mono e le librerie di runtime di Objective-C) presentano problemi quando devono gestire le eccezioni e quindi eseguire il codice scritto in altri linguaggi.

In questo documento vengono illustrati i problemi che possono verificarsi e le possibili soluzioni.

Include anche un progetto di esempio, il [marshalling delle eccezioni](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling), che può essere usato per testare diversi scenari e le relative soluzioni.

## <a name="problem"></a>Problema

Il problema si verifica quando viene generata un'eccezione e durante la rimozione dello stack viene rilevato un frame che non corrisponde al tipo di eccezione generata.

Un esempio tipico di questo tipo per Xamarin.iOS o Xamarin.Mac è quando un'API nativa genera un'eccezione Objective-C e quindi tale eccezione di Objective-C deve essere gestita in qualche modo quando il processo di rimozione dello stack raggiunge un frame gestito.

L'azione predefinita consiste nel non eseguire alcuna operazione. Per l'esempio precedente, ciò significa consentire al runtime di Objective-C di rimuovere i frame gestiti. Questo problema è dovuto al fatto che il runtime di Objective-C non è in grado di rimuovere i frame gestiti; ad esempio, non verrà eseguita alcuna clausola `catch` o `finally` nel frame.

### <a name="broken-code"></a>Codice interruppe

Si consideri l'esempio di codice seguente:

``` csharp
var dict = new NSMutableDictionary ();
dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero); 
```

Verrà generato un NSInvalidArgumentException Objective-C in codice nativo:

```
NSInvalidArgumentException *** setObjectForKey: key cannot be nil
```

E l'analisi dello stack sarà simile alla seguente:

```
0   CoreFoundation          __exceptionPreprocess + 194
1   libobjc.A.dylib         objc_exception_throw + 52
2   CoreFoundation          -[__NSDictionaryM setObject:forKey:] + 1015
3   libobjc.A.dylib         objc_msgSend + 102
4   TestApp                 ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
5   TestApp                 Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr)
6   TestApp                 ExceptionMarshaling.Exceptions.ThrowObjectiveCException ()
```

I frame 0-3 sono frame nativi e lo stato di rimozione dello stack nel runtime Objective-C _può_ rimuovere tali frame. In particolare, eseguirà qualsiasi clausola Objective-C `@catch` o `@finally`.

Tuttavia, l'operazione di rimozione dello stack Objective-C _non_ è in grado di rimuovere correttamente i frame gestiti (frame 4-6), in quanto i frame verranno rimossi, ma la logica di eccezione gestita non verrà eseguita.

Il che significa che non è in genere possibile intercettare queste eccezioni nel modo seguente:

```csharp
try {
    var dict = new NSMutableDictionary ();
    dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero);
} catch (Exception ex) {
    Console.WriteLine (ex);
} finally {
    Console.WriteLine ("finally");
}
```

Ciò è dovuto al fatto che l'operazione di rimozione dello stack Objective-C non è in grado di conoscere la clausola `catch` gestita, né la clausola `finally` essere eseguita.

Quando l'esempio di codice precedente _è_ efficace, questo è dovuto al fatto che Objective-c ha un metodo di notifica delle eccezioni Objective-c non gestite, [`NSSetUncaughtExceptionHandler`][2], che Xamarin.iOS e Xamarin.Mac usano e a quel punto tenta di convertire qualsiasi Objective-c eccezioni alle eccezioni gestite.

## <a name="scenarios"></a>Scenari

### <a name="scenario-1---catching-objective-c-exceptions-with-a-managed-catch-handler"></a>Scenario 1: intercettazione delle eccezioni Objective-C con un gestore catch gestito

Nello scenario seguente è possibile rilevare le eccezioni Objective-C utilizzando gestori di `catch` gestiti:

1. Viene generata un'eccezione Objective-C.
2. Il runtime di Objective-C esamina lo stack, ma non lo rimuove, cercando un gestore di `@catch` nativo in grado di gestire l'eccezione.
3. Il runtime di Objective-C non trova gestori di `@catch`, chiama `NSGetUncaughtExceptionHandler`e richiama il gestore installato da Xamarin.iOS/Xamarin.Mac.
4. Il gestore di Xamarin.iOS/Xamarin.Mac convertirà l'eccezione Objective-C in un'eccezione gestita e la genererà. Poiché il runtime di Objective-C non ha rimosso lo stack (solo a sua volta), il frame corrente è lo stesso in cui è stata generata l'eccezione Objective-C.

Un altro problema si verifica qui, perché il runtime di mono non è in grado di rimuovere correttamente i frame Objective-C.

Quando viene chiamato il callback di eccezioni Objective-C di Xamarin.iOS, lo stack è simile al seguente:

```
 0 libxamarin-debug.dylib   exception_handler(exc=name: "NSInvalidArgumentException" - reason: "*** setObjectForKey: key cannot be nil")
 1 CoreFoundation           __handleUncaughtException + 809
 2 libobjc.A.dylib          _objc_terminate() + 100
 3 libc++abi.dylib          std::__terminate(void (*)()) + 14
 4 libc++abi.dylib          __cxa_throw + 122
 5 libobjc.A.dylib          objc_exception_throw + 337
 6 CoreFoundation           -[__NSDictionaryM setObject:forKey:] + 1015
 7 libxamarin-debug.dylib   xamarin_dyn_objc_msgSend + 102
 8 TestApp                  ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
 9 TestApp                  Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr) [0x00000]
10 TestApp                  ExceptionMarshaling.Exceptions.ThrowObjectiveCException () [0x00013]
```

In questo caso, gli unici frame gestiti sono i frame 8-10, ma l'eccezione gestita viene generata nel frame 0. Ciò significa che il runtime di mono deve rimuovere i frame nativi 0-7, causando un problema equivalente al problema descritto in precedenza: anche se il runtime di mono rimuove i frame nativi, non esegue alcuna clausola Objective-C `@catch` o `@finally`.

Esempio di codice:

```objc
-(id) setObject: (id) object forKey: (id) key
{
    @try {
        if (key == nil)
            [NSException raise: @"NSInvalidArgumentException"];
    } @finally {
        NSLog (@"This will not be executed");
    }
}
```

E la clausola `@finally` non verrà eseguita perché il runtime di mono che rimuove questo frame non ne è a conoscenza.

Una variante di questo è la generazione di un'eccezione gestita nel codice gestito e la rimozione dei frame nativi per ottenere la prima clausola `catch` gestita:

```csharp
class AppDelegate : UIApplicationDelegate {
    public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
    {
        throw new Exception ("An exception");
    }
    static void Main (string [] args)
    {
        try {
            UIApplication.Main (args, null, typeof (AppDelegate));
        } catch (Exception ex) {
            Console.WriteLine ("Managed exception caught.");
        }
    }
}
```

Il metodo di `UIApplication:Main` gestito chiamerà il metodo di `UIApplicationMain` nativo e quindi iOS effettuerà una grande esecuzione del codice nativo prima di chiamare il metodo `AppDelegate:FinishedLaunching` gestito, con una grande quantità di frame nativi nello stack quando viene generata l'eccezione gestita. :

```
 0: TestApp                 ExceptionMarshaling.IOS.AppDelegate:FinishedLaunching (UIKit.UIApplication,Foundation.NSDictionary)
 1: TestApp                 (wrapper runtime-invoke) <Module>:runtime_invoke_bool__this___object_object (object,intptr,intptr,intptr) 
 2: libmonosgen-2.0.dylib   mono_jit_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
 3: libmonosgen-2.0.dylib   do_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
 4: libmonosgen-2.0.dylib   mono_runtime_invoke [inlined] mono_runtime_invoke_checked(method=<unavailable>, obj=<unavailable>, params=<unavailable>, error=0xbff45758)
 5: libmonosgen-2.0.dylib   mono_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>)
 6: libxamarin-debug.dylib  xamarin_invoke_trampoline(type=<unavailable>, self=<unavailable>, sel="application:didFinishLaunchingWithOptions:", iterator=<unavailable>), context=<unavailable>)
 7: libxamarin-debug.dylib  xamarin_arch_trampoline(state=0xbff45ad4)
 8: libxamarin-debug.dylib  xamarin_i386_common_trampoline
 9: UIKit                   -[UIApplication _handleDelegateCallbacksWithOptions:isSuspended:restoreState:]
10: UIKit                   -[UIApplication _callInitializationDelegatesForMainScene:transitionContext:]
11: UIKit                   -[UIApplication _runWithMainScene:transitionContext:completion:]
12: UIKit                   __84-[UIApplication _handleApplicationActivationWithScene:transitionContext:completion:]_block_invoke.3124
13: UIKit                   -[UIApplication workspaceDidEndTransaction:]
14: FrontBoardServices      __37-[FBSWorkspace clientEndTransaction:]_block_invoke_2
15: FrontBoardServices      __40-[FBSWorkspace _performDelegateCallOut:]_block_invoke
16: FrontBoardServices      __FBSSERIALQUEUE_IS_CALLING_OUT_TO_A_BLOCK__
17: FrontBoardServices      -[FBSSerialQueue _performNext]
18: FrontBoardServices      -[FBSSerialQueue _performNextFromRunLoopSource]
19: FrontBoardServices      FBSSerialQueueRunLoopSourceHandler
20: CoreFoundation          __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION__
21: CoreFoundation          __CFRunLoopDoSources0
22: CoreFoundation          __CFRunLoopRun
23: CoreFoundation          CFRunLoopRunSpecific
24: CoreFoundation          CFRunLoopRunInMode
25: UIKit                   -[UIApplication _run]
26: UIKit                   UIApplicationMain
27: TestApp                 (wrapper managed-to-native) UIKit.UIApplication:UIApplicationMain (int,string[],intptr,intptr)
28: TestApp                 UIKit.UIApplication:Main (string[],intptr,intptr)
29: TestApp                 UIKit.UIApplication:Main (string[],string,string)
30: TestApp                 ExceptionMarshaling.IOS.Application:Main (string[])
```

I frame 0-1 e 27-30 vengono gestiti, mentre tutti quelli compresi tra sono nativi. Se mono viene rimosso attraverso questi frame, non verrà eseguita alcuna clausola Objective-C `@catch` o `@finally`.

### <a name="scenario-2---not-able-to-catch-objective-c-exceptions"></a>Scenario 2: non è possibile intercettare le eccezioni Objective-C

Nello scenario seguente _non_ è possibile intercettare le eccezioni Objective-c utilizzando gestori di `catch` gestiti perché l'eccezione Objective-c è stata gestita in un altro modo:

1. Viene generata un'eccezione Objective-C.
2. Il runtime di Objective-C esamina lo stack, ma non lo rimuove, cercando un gestore di `@catch` nativo in grado di gestire l'eccezione.
3. Il runtime di Objective-C trova un gestore di `@catch`, rimuove lo stack e avvia l'esecuzione del gestore di `@catch`.

Questo scenario è comunemente disponibile nelle app Xamarin.iOS, perché sul thread principale è in genere presente codice simile al seguente:

``` objective-c
void UIApplicationMain ()
{
    @try {
        while (true) {
            ExecuteRunLoop ();
        }
    } @catch (NSException *ex) {
        NSLog (@"An unhandled exception occured: %@", exc);
        abort ();
    }
}

```

Ciò significa che nel thread principale non esiste mai un'eccezione Objective-C non gestita e pertanto il callback che converte le eccezioni Objective-C in eccezioni gestite non viene mai chiamato.

Questa operazione è molto comune anche quando si esegue il debug di app Xamarin.Mac in una versione precedente di macOS rispetto a Xamarin.Mac perché il controllo della maggior parte degli oggetti dell'interfaccia utente nel debugger tenterà di recuperare proprietà che corrispondono ai selettori che non esistono nella piattaforma in esecuzione ( Poiché Xamarin.Mac include il supporto per una versione di macOS più elevata). La chiamata di tali selettori genererà un `NSInvalidArgumentException` ("selettore non riconosciuto inviato a..."), che alla fine causa l'arresto anomalo del processo.

Per riepilogare, la presenza del runtime di Objective-C o dei frame di rimozione del runtime di mono che non sono programmati per la gestione può causare comportamenti indefiniti, ad esempio arresti anomali, perdite di memoria e altri tipi di comportamenti imprevedibili (MIS).

## <a name="solution"></a>Soluzione

In Xamarin.iOS 10 e Xamarin.Mac 2,10 è stato aggiunto il supporto per intercettare le eccezioni gestite e Objective-C su qualsiasi limite nativo gestito e per convertire l'eccezione in un altro tipo.

In pseudo-codice l'aspetto è simile al seguente:

``` csharp
[DllImport ("libobjc.dylib")]
static extern void objc_msgSend (IntPtr handle, IntPtr selector);

static void DoSomething (NSObject obj)
{
    objc_msgSend (obj.Handle, Selector.GetHandle ("doSomething"));
}
```

Viene intercettato P/Invoke per objc_msgSend, che viene invece chiamato:

``` objective-c
void
xamarin_dyn_objc_msgSend (id obj, SEL sel)
{
    @try {
        objc_msgSend (obj, sel);
    } @catch (NSException *ex) {
        convert_to_and_throw_managed_exception (ex);
    }
}
```

Per la Reverse (marshalling di eccezioni gestite a eccezioni Objective-C) viene eseguita un'operazione simile.

L'intercettazione delle eccezioni sul limite nativo gestito non è priva di costi, quindi non è sempre abilitata per impostazione predefinita:

- Xamarin.iOS/tvOS: l'intercettazione delle eccezioni Objective-C è abilitata nel simulatore.
- Xamarin.watchos: l'intercettazione viene applicata in tutti i casi, perché consentire ai frame gestiti di rimozione del runtime di Objective-C di confondere il Garbage Collector e di bloccarlo o arrestarlo in modo anomalo.
- Xamarin.Mac: l'intercettazione di eccezioni Objective-C è abilitata per le compilazioni di debug.

La sezione [flag della fase di compilazione](#build_time_flags) illustra come abilitare l'intercettazione quando non è abilitata per impostazione predefinita.

## <a name="events"></a>eventi

Una volta intercettata un'eccezione, vengono generati due nuovi eventi: `Runtime.MarshalManagedException` e `Runtime.MarshalObjectiveCException`.

A entrambi gli eventi viene passato un oggetto `EventArgs` contenente l'eccezione originale generata (la proprietà `Exception`) e una proprietà `ExceptionMode` per definire la modalità di marshalling dell'eccezione.

Per modificare il comportamento in base a qualsiasi elaborazione personalizzata eseguita nel gestore, è possibile modificare la proprietà `ExceptionMode` nel gestore eventi. Un esempio potrebbe essere interrompere il processo se si verifica una determinata eccezione.

La modifica della proprietà `ExceptionMode` si applica al singolo evento, ma non influisce sulle eccezioni intercettate in futuro.

Sono disponibili le modalità seguenti:

- `Default`: il valore predefinito varia in base alla piattaforma. È `ThrowObjectiveCException` se il GC è in modalità cooperativa (watchos) e `UnwindNativeCode` in caso contrario (iOS/watchos/macOS). Il valore predefinito può cambiare in futuro.
- `UnwindNativeCode`: si tratta del comportamento precedente (non definito). Questa opzione non è disponibile quando si usa GC in modalità cooperativa, che è l'unica opzione disponibile in watchos; pertanto, non è un'opzione valida in watchos, ma è l'opzione predefinita per tutte le altre piattaforme.
- `ThrowObjectiveCException`: converte l'eccezione gestita in un'eccezione Objective-C e genera l'eccezione Objective-C. Questa è l'impostazione predefinita per watchos.
- `Abort`: interrompere il processo.
- `Disable`: Disabilita l'intercettazione delle eccezioni, pertanto non è consigliabile impostare questo valore nel gestore eventi, ma una volta generato l'evento è troppo tardi per disabilitarlo. In ogni caso, se impostato, si comporterà come `UnwindNativeCode`.

Per il marshalling di eccezioni Objective-C al codice gestito, sono disponibili le modalità seguenti:

- `Default`: il valore predefinito varia in base alla piattaforma. È `ThrowManagedException` se il GC è in modalità cooperativa (watchos) e `UnwindManagedCode` in caso contrario (iOS/tvOS/macOS). Il valore predefinito può cambiare in futuro.
- `UnwindManagedCode`: si tratta del comportamento precedente (non definito). Questa opzione non è disponibile quando si usa GC in modalità cooperativa (che è l'unica modalità GC valida in watchos, quindi questa non è un'opzione valida in watchos), ma è l'impostazione predefinita per tutte le altre piattaforme.
- `ThrowManagedException`: converte l'eccezione Objective-C in un'eccezione gestita e genera l'eccezione gestita. Questa è l'impostazione predefinita per watchos.
- `Abort`: interrompere il processo.
- `Disable`:D Abilita l'intercettazione delle eccezioni, pertanto non ha senso impostare questo valore nel gestore eventi, ma una volta generato l'evento, è troppo tardi per disabilitarlo. In ogni caso, se impostato, il processo verrà interrotto.

Quindi, per visualizzare ogni volta che viene eseguito il marshalling di un'eccezione, è possibile eseguire questa operazione:

``` csharp
Runtime.MarshalManagedException += (object sender, MarshalManagedExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling managed exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
    
};
Runtime.MarshalObjectiveCException += (object sender, MarshalObjectiveCExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling Objective-C exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
};
```

<a name="build_time_flags" />

## <a name="build-time-flags"></a>Flag della fase di compilazione

È possibile passare le opzioni seguenti a **mTouch** (per le app Xamarin.iOS) e **MMP** (per le app Xamarin.Mac), per determinare se l'intercettazione delle eccezioni è abilitata e impostare l'azione predefinita che deve verificarsi:

- `--marshal-managed-exceptions=`
  - `default`
  - `unwindnativecode`
  - `throwobjectivecexception`
  - `abort`
  - `disable`

- `--marshal-objectivec-exceptions=`
  - `default`
  - `unwindmanagedcode`
  - `throwmanagedexception`
  - `abort`
  - `disable`

Ad eccezione di `disable`, questi valori sono identici ai valori `ExceptionMode` passati agli eventi di `MarshalManagedException` e `MarshalObjectiveCException`.

L'opzione `disable` Disabilita _per lo più_ l'intercettazione, con la differenza che verranno comunque intercettate le eccezioni quando non viene aggiunto alcun overhead di esecuzione. Gli eventi di marshalling vengono comunque generati per queste eccezioni e la modalità predefinita è la modalità predefinita per la piattaforma in esecuzione.

## <a name="limitations"></a>Limitazioni

Quando si tenta di rilevare le eccezioni Objective-C, vengono intercettati solo i P/Invoke alla famiglia di funzioni `objc_msgSend`. Ciò significa che un P/Invoke per un'altra funzione C, che quindi genera eccezioni Objective-C, verrà comunque eseguito nel comportamento precedente e non definito (questo potrebbe essere migliorato in futuro).

[2]: https://developer.apple.com/reference/foundation/1409609-nssetuncaughtexceptionhandler?language=objc

## <a name="related-links"></a>Collegamenti correlati

- [Marshalling delle eccezioni (esempio)](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)
