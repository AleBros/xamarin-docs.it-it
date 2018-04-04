---
title: Marshalling di eccezione
description: Xamarin. IOS contiene nuovi eventi per consentire di rispondere alle eccezioni, in particolare in codice nativo.
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/05/2017
ms.openlocfilehash: bb9c16985d958772193093434350435ce477956a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="exception-marshaling"></a>Marshalling di eccezione

_Xamarin. IOS contiene nuovi eventi per consentire di rispondere alle eccezioni, in particolare in codice nativo._

Sia il codice gestito e Objective-C è disponibile supporto per le eccezioni di runtime (clausole try/catch/finally).

Tuttavia, le implementazioni sono diverse, il che significa che le librerie di runtime (runtime di Mono e le librerie di runtime Objective-C) persistono quando è necessario gestire le eccezioni e quindi eseguire il codice scritto in altri linguaggi.

Questo documento illustra i problemi che possono verificarsi e le possibili soluzioni.

Include inoltre un progetto di esempio, [eccezione marshalling](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling), che può essere utilizzato per verificare diversi scenari e le relative soluzioni.

## <a name="problem"></a>Problema

Il problema si verifica quando un'eccezione viene generata un'eccezione e viene rilevata durante la rimozione di un frame dello stack che non corrisponde al tipo di eccezione generata.

Un esempio tipico di xamarin. IOS o Xamarin.Mac è quando un'API nativa genera un'eccezione di Objective-C, e quindi tale eccezione Objective-C deve essere gestito in qualche modo quando il processo di rimozione dello stack raggiunge un frame gestito.

L'azione predefinita è che non accada nulla. Per l'esempio precedente, ciò significa consentire i frame di rimozione gestiti Objective-C runtime. Questo è problematico, perché il runtime Objective-C non è in grado di rimuovere i frame gestiti. ad esempio non eseguirà qualsiasi `catch` o `finally` clausole nel frame.

### <a name="broken-code"></a>Codice danneggiato

Si consideri l'esempio di codice seguente:

``` csharp
var dict = new NSMutableDictionary ();
dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero); 
```

Nel codice nativo, verrà generata una NSInvalidArgumentException Objective-C:

    NSInvalidArgumentException *** setObjectForKey: key cannot be nil

E la traccia dello stack sarà simile al seguente:

    0   CoreFoundation          __exceptionPreprocess + 194
    1   libobjc.A.dylib         objc_exception_throw + 52
    2   CoreFoundation          -[__NSDictionaryM setObject:forKey:] + 1015
    3   libobjc.A.dylib         objc_msgSend + 102
    4   TestApp                 ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
    5   TestApp                 Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr)
    6   TestApp                 ExceptionMarshaling.Exceptions.ThrowObjectiveCException ()

Frame 0-3 sono visualizzati frame nativi e l'agente di rimozione dello stack in fase di esecuzione Objective-C _possibile_ i frame di rimozione. In particolare, verrà eseguito alcun Objective-C `@catch` o `@finally` clausole.

Tuttavia, l'agente di rimozione dello stack Objective-C è _non_ in grado di rimozione correttamente i frame gestiti (fotogrammi 4-6), in quanto i frame verrà rimosso, ma non verrà eseguita la logica di eccezione gestita.

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

In questo modo l'agente di rimozione dello stack Objective-C non conosce gestito `catch` clausola e non verrà eseguito il `finally` clausola da eseguire.

Quando l'esempio di codice precedente _è_ efficace, è perché Objective-C dispone di un metodo di ricevere una notifica di eccezioni non gestite Objective-C, [ `NSSetUncaughtExceptionHandler` ] [ 2], che Xamarin. IOS e utilizzare Xamarin.Mac e a questo punto tenta di convertire tutte le eccezioni Objective-C per le eccezioni gestite.

## <a name="scenarios"></a>Scenari

### <a name="scenario-1---catching-objective-c-exceptions-with-a-managed-catch-handler"></a>Scenario 1: intercettare le eccezioni Objective-C con un gestore catch gestito

Nello scenario seguente, è possibile intercettare le eccezioni Objective-C utilizzando gestiti `catch` gestori:

1. Viene generata un'eccezione di Objective-C.
2. Il runtime Objective-C esamina lo stack (ma non di rimozione), cercando nativo `@catch` gestore in grado di gestire l'eccezione.
3. Il runtime Objective-C non viene trovato uno `@catch` gestori, chiama `NSGetUncaughtExceptionHandler`e richiama il gestore installato da Xamarin.iOS/Xamarin.Mac.
4. Gestore Xamarin.iOS/Xamarin.Mac's verrà convertire l'eccezione Objective-C in un'eccezione gestita e la generazione. Poiché Objective-C runtime non di rimozione dello stack (solo viene esaminato), il frame corrente corrisponde a quella in cui è stata generata l'eccezione di Objective-C.

Un altro problema si verifica in questo caso, perché il runtime Mono non è in grado di rimuovere correttamente frame Objective-C.

Quando viene chiamato callback di eccezione Objective-C non rilevata di xamarin, lo stack è simile al seguente:

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

In questo caso, i frame gestiti soli sono fotogrammi 8-10, ma viene generata un'eccezione gestita nel frame 0. Ciò significa che il runtime Mono deve rimozione frame nativi 0-7, che causa un problema equivalente per il problema descritto sopra: anche se il runtime Mono rimuoverà i frame nativi, non eseguirà alcun Objective-C `@catch` o `@finally` clausole .

Esempio di codice:

``` objective-c
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

E `@finally` clausola non verrà eseguita perché il runtime Mono che rimuove questo frame non conosce su di esso.

Una variante di questo consiste nel generare un'eccezione gestita in codice gestito e quindi la rimozione tramite frame nativi per ottenere al primo gestiti `catch` clausola:

``` csharp
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

Gestito `UIApplication:Main` metodo chiama il metodo nativo `UIApplicationMain` (metodo) e iOS verranno richieste numerose attività di esecuzione del codice nativo prima di chiamare gestito `AppDelegate:FinishedLaunching` (metodo), con molte ancora una frame nativi nello stack quando un'eccezione gestita è generata:

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

Vengono gestiti frame 0-1 e 27-30, mentre tutti quelli tra sono di tipo nativi. Se Mono rimuove tramite questi frame, nessun Objective-C `@catch` o `@finally` clausole verranno eseguite.

### <a name="scenario-2---not-able-to-catch-objective-c-exceptions"></a>Scenario 2: non è possibile intercettare le eccezioni di Objective-C

Nello scenario seguente, è _non_ possibile intercettare le eccezioni Objective-C utilizzando gestito `catch` gestori perché è stata gestita l'eccezione Objective-C in un altro modo:

1. Viene generata un'eccezione di Objective-C.
2. Il runtime Objective-C esamina lo stack (ma non di rimozione), cercando nativo `@catch` gestore in grado di gestire l'eccezione.
3. Il runtime Objective-C trova un `@catch` gestore, rimuove lo stack e avvia l'esecuzione di `@catch` gestore.

Questo scenario viene comunemente reperibile nelle App xamarin poiché nel thread principale in genere codice simile al seguente:

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

Ciò significa che nel thread principale non è mai un'eccezione non gestita di Objective-C, e pertanto non viene mai chiamato il callback che converte le eccezioni di Objective-C per le eccezioni gestite.

Questo è abbastanza frequente durante il debug di App Xamarin.Mac in una versione precedente di macOS rispetto a Xamarin.Mac supportato poiché controllando la maggior parte degli oggetti dell'interfaccia utente nel debugger tenta di recuperare le proprietà corrispondenti per i selettori che non esistono nell'esecuzione di piattaforma ( Poiché Xamarin.Mac include il supporto per una versione successiva di macOS). La chiamata di questi selettori genererà un `NSInvalidArgumentException` ("non riconosciuto selettore inviati a..."), che causa il blocco di arresto anomalo del processo.

Per riepilogare, con il runtime Objective-C o i frame di rimozione runtime Mono che essi non sono programmate per l'handle può causare comportamenti imprevedibili, ad esempio arresti anomali del sistema, le perdite di memoria e altri tipi di comportamenti imprevedibili (mis).

## <a name="solution"></a>Soluzione

10 di xamarin e Xamarin.Mac 2.10, è stato aggiunto il supporto per intercettare le eccezioni gestite e Objective-C in qualsiasi limite gestito a nativo e per la conversione di eccezioni in altro tipo.

Nel codice di programmazione, simile al seguente:

``` csharp
[DllImport ("libobjc.dylib")]
static extern void objc_msgSend (IntPtr handle, IntPtr selector);

static void DoSomething (NSObject obj)
{
    objc_msgSend (obj.Handle, Selector.GetHandle ("doSomething"));
}
```

P/Invoke per objc_msgSend viene intercettato e questo viene invece chiamato:

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

E un messaggio simile viene eseguito per il case inverso (il marshalling delle eccezioni gestite eccezioni Objective-C).

Intercettazione di eccezioni in corrispondenza del limite nativo gestito non è senza costo, pertanto è non sempre abilitato per impostazione predefinita:

- Xamarin.iOS/tvOS: è attivata l'intercettazione di eccezioni Objective-C nel simulatore.
- Xamarin.watchOS: intercettazione viene applicata in tutti i casi, perché consente la rimozione di Objective-C runtime gestita frame verranno confondere il garbage collector e rendere il blocco o arresto anomalo.
- Xamarin.Mac: intercettazione di eccezioni Objective-C è abilitata per le build di debug.

Il [flag in fase di compilazione](#build_time_flags) sezione viene illustrato come abilitare l'intercettazione quando non è abilitato per impostazione predefinita.

## <a name="events"></a>Eventi

Esistono due nuovi eventi che vengono generati una volta che viene intercettata un'eccezione: `Runtime.MarshalManagedException` e `Runtime.MarshalObjectiveCException`.

Entrambi gli eventi vengono passati un `EventArgs` oggetto che contiene l'eccezione originale che è stata generata un'eccezione (il `Exception` proprietà) e un `ExceptionMode` proprietà per definire la modalità di marshalling di eccezione.

Il `ExceptionMode` proprietà può essere modificata nel gestore per modificare il comportamento in base a qualsiasi elaborazione personalizzata eseguita nel gestore. Ad esempio, è possibile interrompere il processo se si verifica un'eccezione di alcuni.

Modifica il `ExceptionMode` proprietà viene applicata all'evento singolo, non si applica tutte le eccezioni intercettate in futuro.

Sono disponibili le modalità seguenti:

- `Default`: Il valore predefinito dipende dalla piattaforma. È `ThrowObjectiveCException` se il Garbage Collector è in modalità cooperativa (watchOS), e `UnwindNativeCode` in caso contrario (iOS / watchOS / macOS). Il valore predefinito può cambiare in futuro.
- `UnwindNativeCode`: Questo è il comportamento precedente (indefinito). Non è disponibile quando si usa la Garbage Collection in modalità cooperativa (che è l'unica opzione in watchOS; pertanto, questo non è un'opzione valida in watchOS), ma è l'opzione predefinita per tutte le altre piattaforme.
- `ThrowObjectiveCException`: Convertire un'eccezione gestita in un'eccezione di Objective-C e generare l'eccezione Objective-C. Questo è il valore predefinito su watchOS.
- `Abort`: Il processo di interruzione.
- `Disable`: Disabilita l'intercettazione di eccezioni, in modo che non ha senso per impostare questo valore nel gestore eventi, ma una volta che viene generato l'evento è troppo tardi per disabilitarlo. In ogni caso, se impostato, si comportano come `UnwindNativeCode`.

Per il marshalling delle eccezioni Objective-C per codice gestito, sono disponibili le modalità seguenti:

- `Default`: Il valore predefinito dipende dalla piattaforma. È `ThrowManagedException` se il Garbage Collector è in modalità cooperativa (watchOS), e `UnwindManagedCode` in caso contrario (iOS / tvOS / macOS). Il valore predefinito può cambiare in futuro.
- `UnwindManagedCode`: Questo è il comportamento precedente (indefinito). Non è disponibile quando si usa la Garbage Collection in modalità cooperativa (ovvero la modalità di Garbage Collection è valida solo su watchOS; pertanto questo non è un'opzione valida in watchOS), ma è il valore predefinito per tutte le altre piattaforme.
- `ThrowManagedException`: Converte l'eccezione Objective-C per un'eccezione gestita e genera un'eccezione gestita. Questo è il valore predefinito su watchOS.
- `Abort`: Il processo di interruzione.
- `Disable`: Disabilita l'intercettazione dell'eccezione, pertanto può non essere opportuno impostare questo valore nel caso in cui gestore, ma una volta l'evento viene generato, è troppo tardi per disabilitarlo. In ogni caso se impostato, verrà interrotto il processo.

In tal caso, per visualizzare ogni volta che viene effettuato il marshalling di un'eccezione, è possibile farlo:

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

## <a name="build-time-flags"></a>Flag in fase di compilazione

È possibile passare le opzioni seguenti per **mtouch** (per App xamarin) e **mmp** (per App Xamarin.Mac), che determinano se è attivata l'intercettazione di eccezioni e impostare l'azione predefinita che deve essere eseguita:

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

Ad eccezione di `disable`, questi valori sono identici al `ExceptionMode` valori che vengono passati al `MarshalManagedException` e `MarshalObjectiveCException` eventi.

Il `disable` opzione viene _principalmente_ disabilitare l'intercettazione, ma è ancora possibile intercettare le eccezioni quando non viene aggiunto alcun sovraccarico di esecuzione. Gli eventi di marshalling vengono comunque generati per le eccezioni, con la modalità predefinita il metodo predefinito per la piattaforma di esecuzione.

## <a name="limitations"></a>Limitazioni

È solo intercettare P/Invoke il `objc_msgSend` famiglia di funzioni quando si tenta di rilevare eccezioni Objective-C. Questo significa che P/Invoke per un'altra funzione C, quindi genera qualsiasi eccezione Objective-C, verrà comunque eseguito esplicitamente il comportamento dei vecchio e non definito (questo è possibile migliorare in futuro).

[2]: https://developer.apple.com/reference/foundation/1409609-nssetuncaughtexceptionhandler?language=objc


## <a name="related-links"></a>Collegamenti correlati

- [Marshalling di eccezione (esempio)](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)
