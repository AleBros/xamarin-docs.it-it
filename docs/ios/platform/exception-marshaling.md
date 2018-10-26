---
title: Eccezioni di marshalling in xamarin. IOS
description: Questo documento descrive come gestire le eccezioni native e gestite in un'app xamarin. IOS. Illustra i problemi che possono verificarsi e una soluzione per questi problemi.
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/05/2017
ms.openlocfilehash: 167d6ac421bdd2652e7f8474e1ea21bd9040723f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114301"
---
# <a name="exception-marshaling-in-xamarinios"></a>Eccezioni di marshalling in xamarin. IOS

_Xamarin. IOS include nuovi eventi per rispondere alle eccezioni, in particolare nel codice nativo._

Sia il codice gestito e Objective-C sono supportati per le eccezioni di runtime (clausole try/catch/finally).

Tuttavia, le implementazioni sono diverse, il che significa che le librerie di runtime (runtime di Mono e le librerie di runtime di Objective-C) hanno problemi quando devono gestire le eccezioni e quindi eseguire il codice scritto in altri linguaggi.

Questo documento illustra i problemi che possono verificarsi e le possibili soluzioni.

Include anche un progetto di esempio [marshalling delle eccezioni](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling), che può essere usato per verificare diversi scenari e le relative soluzioni.

## <a name="problem"></a>Problema

Il problema si verifica quando un'eccezione viene generata un'eccezione e viene rilevata durante la rimozione di un frame dello stack che corrisponde al tipo di eccezione generata.

Un esempio tipico di questo oggetto per xamarin. IOS o xamarin. Mac è quando un'API nativa verrà generata un'eccezione di Objective-C, e quindi tale eccezione Objective-C deve essere gestito in qualche modo quando il processo di rimozione dello stack raggiunge un frame gestito.

L'azione predefinita consiste nell'eseguire alcuna operazione. Per l'esempio precedente, ciò significa che i frame di rimozione gestiti runtime Objective-C. Ciò è problematica, perché il runtime di Objective-C non sa come per la rimozione di frame gestiti; ad esempio non verrà eseguito qualsiasi `catch` o `finally` clausole nel frame.

### <a name="broken-code"></a>Codice danneggiato

Si consideri l'esempio di codice seguente:

``` csharp
var dict = new NSMutableDictionary ();
dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero); 
```

Nel codice nativo, verrà generata un' NSInvalidArgumentException Objective-C:

    NSInvalidArgumentException *** setObjectForKey: key cannot be nil

E l'analisi dello stack sarà simile al seguente:

    0   CoreFoundation          __exceptionPreprocess + 194
    1   libobjc.A.dylib         objc_exception_throw + 52
    2   CoreFoundation          -[__NSDictionaryM setObject:forKey:] + 1015
    3   libobjc.A.dylib         objc_msgSend + 102
    4   TestApp                 ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
    5   TestApp                 Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr)
    6   TestApp                 ExceptionMarshaling.Exceptions.ThrowObjectiveCException ()

Frame 0-3 sono visualizzati frame nativi e l'agente di rimozione dello stack nel runtime Objective-C _possibile_ i frame di rimozione. In particolare, sarà eseguito qualsiasi Objective-C `@catch` o `@finally` clausole.

Tuttavia, è l'agente di rimozione dello stack di Objective-C _non_ in grado di rimozione in modo corretto i frame gestiti (fotogrammi 4-6), in quanto i frame verrà rimosso, ma non verrà eseguita per la logica di eccezione gestita.

Che significa che in genere non è possibile intercettare queste eccezioni nel modo seguente:

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

Infatti, l'agente di rimozione dello stack di Objective-C non conosce gestita `catch` clausola e non verrà il `finally` clausola da eseguire.

Quando l'esempio di codice precedente _è_ efficace, è perché Objective-C dispone di un metodo di ricevere una notifica di eccezioni non gestite Objective-C, [`NSSetUncaughtExceptionHandler`][2], che Xamarin. IOS e utilizzare Xamarin.Mac e a questo punto tenta di convertire tutte le eccezioni Objective-C per le eccezioni gestite.

## <a name="scenarios"></a>Scenari

### <a name="scenario-1---catching-objective-c-exceptions-with-a-managed-catch-handler"></a>Scenario 1: rilevare eccezioni Objective-C con un gestore catch gestito

Nello scenario seguente, è possibile intercettare le eccezioni Objective-C usando managed `catch` gestori:

1. Viene generata un'eccezione di Objective-C.
2. Il runtime di Objective-C analizza lo stack, ma non di rimozione, cercando nativo `@catch` gestore in grado di gestire l'eccezione.
3. Il runtime di Objective-C non trova qualsiasi `@catch` gestori eventi, le chiamate `NSGetUncaughtExceptionHandler`e richiama il gestore installato da Xamarin.iOS/Xamarin.Mac.
4. Gestore Xamarin.iOS/Xamarin.Mac's convertirà l'eccezione di Objective-C in un'eccezione gestita e praticità del forno. Poiché di Objective-C runtime non è stata di rimozione dello stack (solo esaminato lo), il frame corrente è uguale a quello in cui è stata generata l'eccezione di Objective-C.

Un altro problema si verifica in questo caso, perché il runtime di Mono non sa come per la rimozione di fotogrammi Objective-C in modo corretto.

Quando viene chiamato callback degli xamarin. IOS non rilevate Objective-C dell'eccezione, lo stack è simile al seguente:

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

In questo caso, i frame gestiti soli sono fotogrammi 8 a 10, ma viene generata l'eccezione gestita in frame di 0. Ciò significa che il runtime di Mono necessario rimuoverli frame nativi 0-7, che comporta un problema equivalente al problema indicato in precedenza: anche se il runtime di Mono ripercorrerà frame nativi, non verrà eseguito alcun Objective-C `@catch` o `@finally` clausole .

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

E `@finally` clausola non verrà eseguita perché il runtime di Mono che viene rimosso questo frame non segnalarlo.

Una variante di questo oggetto consiste nel generare un'eccezione gestita in codice gestito e quindi per la rimozione tramite i frame nativi per ottenere il primo gestiti `catch` clausola:

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

Managed `UIApplication:Main` metodo chiama il metodo nativo `UIApplicationMain` (metodo) e quindi iOS eseguirà numerosi l'esecuzione di codice nativo prima di chiamare managed `AppDelegate:FinishedLaunching` metodo, ancora molti dei frame nativi nello stack quando un'eccezione gestita è generata:

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

Vengono gestiti frame 0-1 e 27-30, mentre tutti quelli tra sono native. Se viene rimosso Mono tramite questi frame, nessun Objective-C `@catch` o `@finally` verranno eseguite le clausole.

### <a name="scenario-2---not-able-to-catch-objective-c-exceptions"></a>Scenario 2: non è possibile intercettare le eccezioni Objective-C

Nello scenario seguente, si tratta _non_ possibile intercettare le eccezioni Objective-C usando managed `catch` gestori perché è stata gestita l'eccezione di Objective-C in un altro modo:

1. Viene generata un'eccezione di Objective-C.
2. Il runtime di Objective-C analizza lo stack, ma non di rimozione, cercando nativo `@catch` gestore in grado di gestire l'eccezione.
3. Il runtime di Objective-C rileva un `@catch` gestore, rimuove lo stack e inizia l'esecuzione di `@catch` gestore.

Questo scenario viene comunemente trovato nelle App xamarin. IOS, poiché nel thread principale in genere codice simile al seguente:

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

Ciò significa che nel thread principale non è mai effettivamente un'eccezione non gestita di Objective-C e pertanto non viene mai chiamato il callback che converte le eccezioni Objective-C per le eccezioni gestite.

Questo è anche piuttosto comune durante il debug di App xamarin. Mac su una versione di macOS precedente rispetto a xamarin. Mac supporta perché esaminare la maggior parte degli oggetti dell'interfaccia utente nel debugger proverà a recuperare le proprietà che corrispondono a selettori che non esistono sul (piattaforma in esecuzione Poiché xamarin. Mac include il supporto per una versione recente di macOS). La chiamata di tali selettori genererà un `NSInvalidArgumentException` ("selettore non riconosciuti inviati a..."), che alla fine causa l'arresto anomalo del processo.

Per riepilogare, con il runtime di Objective-C o i frame di rimozione di runtime di Mono non sono previste a handle può causare comportamenti imprevedibili, ad esempio arresti anomali del sistema, le perdite di memoria e altri tipi di comportamenti imprevedibili (mis).

## <a name="solution"></a>Soluzione

In xamarin. IOS 10 e 2.10 di xamarin. Mac, è stato aggiunto il supporto per intercettare le eccezioni gestite e Objective-C in qualsiasi limite gestito a nativo e per la conversione di tale eccezione in altro tipo.

Pseudo-codice, simile al seguente:

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

E una schermata simile viene eseguita per il case inverso (marshalling le eccezioni gestite in eccezioni Objective-C).

Rilevamento delle eccezioni in corrispondenza del limite gestito a nativo non è senza costi, pertanto è non sempre abilitato per impostazione predefinita:

- Xamarin.iOS/tvOS: l'intercettazione delle eccezioni Objective-C è abilitato nel simulatore.
- Xamarin. watchos: l'intercettazione viene applicata in tutti i casi, perché consente la rimozione di runtime di Objective-C gestita frame potrebbe causare problemi al garbage collector e rendere il blocco o un arresto anomalo del sistema.
- Xamarin. Mac: l'intercettazione delle eccezioni Objective-C è abilitata per le compilazioni di debug.

Il [flag in fase di compilazione](#build_time_flags) sezione viene illustrato come abilitare l'intercettazione quando non è abilitato per impostazione predefinita.

## <a name="events"></a>Eventi

Sono disponibili due nuovi eventi che vengono generati una volta che viene intercettata un'eccezione: `Runtime.MarshalManagedException` e `Runtime.MarshalObjectiveCException`.

Entrambi gli eventi vengono passati un `EventArgs` oggetto che contiene l'eccezione originale che è stata generata un'eccezione (il `Exception` proprietà) e un `ExceptionMode` proprietà da definire come effettuare il marshalling dell'eccezione.

Il `ExceptionMode` proprietà può essere modificata nell'evento gestore per modificare il comportamento in base a qualsiasi elaborazione personalizzata eseguita nel gestore. Ad esempio, è possibile interrompere il processo se si verifica un'eccezione di determinati.

Modifica il `ExceptionMode` proprietà viene applicata all'evento singolo, non influenza tutte le eccezioni intercettate in futuro.

Sono disponibili le modalità seguenti:

- `Default`: Il valore predefinito varia a seconda della piattaforma. Si tratta `ThrowObjectiveCException` se il Garbage Collector è in modalità cooperativa (watchOS), e `UnwindNativeCode` in caso contrario (iOS / watchOS / macOS). Il valore predefinito può cambiare in futuro.
- `UnwindNativeCode`: Questo è il comportamento precedente (undefined). Non è disponibile quando si usa il Garbage Collector in modalità cooperativa (che è l'unica opzione disponibile in watchOS; pertanto, questo non è un'opzione valida in watchOS), ma è l'opzione predefinita per tutte le altre piattaforme.
- `ThrowObjectiveCException`: Converte le eccezioni gestite in un'eccezione di Objective-C e generare l'eccezione di Objective-C. Questo è l'impostazione predefinita in watchOS.
- `Abort`: Interrompe il processo.
- `Disable`: Disabilita l'intercettazione di eccezioni, in modo che non ha senso per impostare questo valore nel gestore dell'evento, ma una volta che viene generato l'evento è troppo tardi per disabilitarlo. In ogni caso, se impostato, si comportano come `UnwindNativeCode`.

Per effettuare il marshalling delle eccezioni Objective-C al codice gestito, sono disponibili le modalità seguenti:

- `Default`: Il valore predefinito varia a seconda della piattaforma. Si tratta `ThrowManagedException` se il Garbage Collector è in modalità cooperativa (watchOS), e `UnwindManagedCode` in caso contrario (iOS o tvOS / macOS). Il valore predefinito può cambiare in futuro.
- `UnwindManagedCode`: Questo è il comportamento precedente (undefined). Non è disponibile quando si usa il Garbage Collector in modalità cooperativa, che corrisponde alla modalità GC valida solo in watchOS, pertanto questo non è un'opzione valida in watchOS, ma è il valore predefinito per tutte le altre piattaforme.
- `ThrowManagedException`: Converte l'eccezione di Objective-C in un'eccezione gestita e genera un'eccezione gestita. Questo è l'impostazione predefinita in watchOS.
- `Abort`: Interrompe il processo.
- `Disable`: Disabilita l'intercettazione di eccezioni, in modo che non ha senso per impostare questo valore nel caso in cui gestore, ma una volta l'evento viene generato, è troppo tardi per disabilitarla. In ogni caso se impostato, verrà interrotto il processo.

Pertanto, per visualizzare ogni volta che viene effettuato il marshalling un'eccezione, è possibile eseguire questa operazione:

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

## <a name="build-time-flags"></a>Flag di fase di compilazione

È possibile passare le opzioni seguenti per **mtouch** (per le app xamarin. IOS) e **mmp** (per le app xamarin. Mac), che verrà determinare se è attivata l'intercettazione di eccezioni e impostare l'azione predefinita che deve verificarsi:

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

Ad eccezione di `disable`, questi valori sono identici al `ExceptionMode` i valori passati al `MarshalManagedException` e `MarshalObjectiveCException` eventi.

Il `disable` opzione verrà _prevalentemente_ disabilitare l'intercettazione, ma è comunque possibile intercettare le eccezioni quando non viene aggiunto alcun overhead di esecuzione. Gli eventi di marshalling vengono comunque generati per queste eccezioni, con la modalità predefinita in corso la modalità predefinita per la piattaforma in esecuzione.

## <a name="limitations"></a>Limitazioni

Abbiamo solo intercettare P/Invoke di `objc_msgSend` della famiglia di funzioni quando si tenta di rilevare le eccezioni Objective-C. Ciò significa che P/Invoke per un'altra funzione C, che quindi avvia eventuali eccezioni Objective-C, verrà comunque eseguito il comportamento precedente e non definito (ciò può essere migliorata in futuro).

[2]: https://developer.apple.com/reference/foundation/1409609-nssetuncaughtexceptionhandler?language=objc


## <a name="related-links"></a>Collegamenti correlati

- [Marshalling di eccezione (esempio)](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)
