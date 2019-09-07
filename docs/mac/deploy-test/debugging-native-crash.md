---
title: Debug di un arresto anomalo nativo in un app Xamarin.Mac
description: Questo documento descrive come eseguire il debug di eccezioni che hanno origine nel runtime Objective-C. Illustra gli errori di asserzione, i problemi relativi ai callback, il bubbling di eccezioni e molto altro.
ms.prod: xamarin
ms.assetid: B0C0CE31-2737-4969-8EA5-D39D3333E9C2
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 10/19/2016
ms.openlocfilehash: 7340df1d65768363f39c6f080a5d50e2ac250400
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769876"
---
# <a name="debugging-a-native-crash-in-a-xamarinmac-app"></a>Debug di un arresto anomalo nativo in un app Xamarin.Mac

## <a name="overview"></a>Panoramica

In alcuni casi gli errori di programmazione possono causare arresti anomali nel runtime Objective-C nativo, che, a differenza delle eccezioni di C#, non puntano a una riga specifica nel codice che è possibile cercare di correggere. In alcuni casi può essere semplice trovarli e correggerli, in altri può essere estremamente difficile tenerne traccia. 

Verranno ora esaminati alcuni esempi reali di arresto anomalo nativo.

## <a name="example-1-assertion-failure"></a>Esempio 1: Errore di asserzione

Ecco le prime righe di un arresto anomalo in una semplice applicazione di test. Queste informazioni saranno disponibili nel riquadro **Output applicazione**:

```csharp
2014-10-15 16:18:02.364 NSOutlineViewHottness[79111:1304993] *** Assertion failure in -[NSTableView _uncachedRectHeightOfRow:], /SourceCache/AppKit/AppKit-1343.13/TableView.subproj/NSTableView.m:1855
2014-10-15 16:18:02.364 NSOutlineViewHottness[79111:1304993] NSTableView variable rowHeight error: The value must be > 0 for row 0, but the delegate <NSOutlineViewHottness_HotnessViewDelegate: 0xaa01860> gave -1.000.
2014-10-15 16:18:02.378 NSOutlineViewHottness[79111:1304993] *** Assertion failure in -[NSTableView _uncachedRectHeightOfRow:], /SourceCache/AppKit/AppKit-1343.13/TableView.subproj/NSTableView.m:1855
2014-10-15 16:18:02.378 NSOutlineViewHottness[79111:1304993] NSTableView variable rowHeight error: The value must be > 0 for row 0, but the delegate <NSOutlineViewHottness_HotnessViewDelegate: 0xaa01860> gave -1.000.
2014-10-15 16:18:02.381 NSOutlineViewHottness[79111:1304993] (
  0   CoreFoundation                      0x91888343 __raiseError + 195
  1   libobjc.A.dylib                     0x9a5e6a2a objc_exception_throw + 276
  2   CoreFoundation                      0x918881ca +[NSException raise:format:arguments:] + 138
  3   Foundation                          0x950742b1 -[NSAssertionHandler handleFailureInMethod:object:file:lineNumber:description:] + 118
  4   AppKit                              0x975db476 -[NSTableView _uncachedRectHeightOfRow:] + 373
  5   AppKit                              0x975db2f8 -[_NSTableRowHeightStorage _uncachedRectHeightOfRow:] + 143
  6   AppKit                              0x975db206 -[_NSTableRowHeightStorage _cacheRowHeights] + 167
  7   AppKit                              0x975db130 -[_NSTableRowHeightStorage _createRowHeightsArray] + 226
  8   AppKit                              0x975b5851 -[_NSTableRowHeightStorage _ensureRowHeights] + 73
  9   AppKit                              0x975b5790 -[_NSTableRowHeightStorage computeTableHeightForNumberOfRows:] + 89
  10  AppKit                              0x975b4c38 -[NSTableView _totalHeightOfTableView] + 220
```

Le righe che iniziano con numeri sono l'analisi dello stack nativo, da cui risulta evidente che l'arresto anomalo si è verificato all'interno di `NSTableView` che gestisce l'altezza delle righe. `NSAssertionHandler` genera quindi un'eccezione `NSException (objc_exception_throw)` e viene visualizzato l'errore di asserzione:

```csharp
rowHeight error: The value must be > 0 for row 0, but the delegate
<NSOutlineView_ViewDelegate: 0xaa01860> gave -1.000
```

A questo punto, è piuttosto chiaro che un metodo `NSOutlineViewDelegate` sta restituendo un numero negativo. Questo era problema riscontrato:

```csharp
public override nfloat GetRowHeight (NSTableView tableView, nint row)
{
    return -1;
}
```

## <a name="example-2-callback-jumped-into-middle-of-nowhere"></a>Esempio 2 Il callback ha eseguito un passaggio in un punto vuoto

```csharp
Stacktrace:

 at <unknown> <0xffffffff>
 at (wrapper managed-to-native) MonoMac.AppKit.NSApplication.NSApplicationMain (int,string[]) <IL 0x000a4, 0xffffffff>
 at MonoMac.AppKit.NSApplication.Main (string[]) [0x00041] in /Users/donblas/Programming/xamcore-master/src/AppKit/NSApplication.cs:107
 at NSOutlineViewHottness.MainClass.Main (string[]) [0x00007] in /Users/donblas/Programming/Local/NSOutlineViewHottness/NSOutlineViewHottness/Main.cs:14
 at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr) <IL 0x00050, 0xffffffff>

Native stacktrace:

Debug info from gdb:

(lldb) command source -s 0 '/tmp/mono-gdb-commands.qrHllW'
Executing commands in '/private/tmp/mono-gdb-commands.qrHllW'.
(lldb) process attach --pid 79229
Process 79229 stopped
Executable module set to "/Users/donblas/Programming/Local/NSOutlineViewHottness/NSOutlineViewHottness/bin/Debug/NSOutlineViewHottness.app/Contents/MacOS/NSOutlineViewHottness".
Architecture set to: i386-apple-macosx.
(lldb) thread list
Process 79229 stopped
* thread #1: tid = 0x142776, 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10, queue = 'com.apple.main-thread', stop reason = signal SIGSTOP
 thread #2: tid = 0x142790, 0x9af768d2 libsystem_kernel.dylib`kevent64 + 10, queue = 'com.apple.libdispatch-manager'
 thread #3: tid = 0x142792, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #4: tid = 0x142794, 0x9af6fa6a libsystem_kernel.dylib`semaphore_wait_trap + 10
 thread #5: tid = 0x142795, 0x9af75772 libsystem_kernel.dylib`__recvfrom + 10
 thread #6: tid = 0x142799, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #7: tid = 0x14279a, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #8: tid = 0x14279b, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #9: tid = 0x1427f8, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #10: tid = 0x1427fe, 0x9af6fa2e libsystem_kernel.dylib`mach_msg_trap + 10
(lldb) thread backtrace all
* thread #1: tid = 0x142776, 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10, queue = 'com.apple.main-thread', stop reason = signal SIGSTOP
 * frame #0: 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10
   frame #1: 0x986bfb25 libsystem_c.dylib`waitpid$UNIX2003 + 48
   frame #2: 0x028ba36d libmono-2.0.dylib`mono_handle_native_sigsegv(signal=11, ctx=0x03115fe0) + 541 at mini-exceptions.c:2323
   frame #3: 0x0290a8bb libmono-2.0.dylib`mono_arch_handle_altstack_exception(sigctx=<unavailable>, fault_addr=<unavailable>, stack_ovf=0) + 155 at exceptions-x86.c:1159
   frame #4: 0x0280b4fd libmono-2.0.dylib`mono_sigsegv_signal_handler(_dummy=<unavailable>, info=<unavailable>, context=<unavailable>) + 445 at mini.c:6861
   frame #5: 0x91ef403b libsystem_platform.dylib`_sigtramp + 43
   frame #6: 0x9a5dd0bd libobjc.A.dylib`objc_msgSend + 45
   frame #7: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #8: 0x9773ba91 AppKit`-[NSApplication sendAction:to:from:] + 548
   frame #9: 0x9773b82d AppKit`-[NSControl sendAction:to:] + 102
   frame #10: 0x97934d36 AppKit`__26-[NSCell _sendActionFrom:]_block_invoke + 176
   frame #11: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #12: 0x97787975 AppKit`-[NSCell _sendActionFrom:] + 161
   frame #13: 0x979188ea AppKit`-[NSButtonCell _sendActionFrom:] + 55
   frame #14: 0x979366e6 AppKit`__48-[NSCell trackMouse:inRect:ofView:untilMouseUp:]_block_invoke965 + 43
   frame #15: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #16: 0x977a3d00 AppKit`-[NSCell trackMouse:inRect:ofView:untilMouseUp:] + 2815
   frame #17: 0x977a2df4 AppKit`-[NSButtonCell trackMouse:inRect:ofView:untilMouseUp:] + 524
   frame #18: 0x977a233b AppKit`-[NSControl mouseDown:] + 762
   frame #19: 0x97cbc112 AppKit`-[_NSThemeWidget mouseDown:] + 378
   frame #20: 0x97d36d74 AppKit`-[NSWindow _reallySendEvent:] + 12353
   frame #21: 0x977201f9 AppKit`-[NSWindow sendEvent:] + 409
   frame #22: 0x976cdc67 AppKit`-[NSApplication sendEvent:] + 4679
   frame #23: 0x9754807c AppKit`-[NSApplication run] + 1003
```

Si tratta di un problema di cui è stato molto più difficile tenere traccia. Quando viene visualizzato `at <unknown> <0xffffffff>` o `MonoMac.ObjCRuntime.Runtime.GetNSObject (IntPtr ptr)` all'inizio dell'analisi dello stack gestita, è probabile che si stia provando a eseguire codice gestito con un oggetto raccolto dal Garbage Collector. L'analisi dello stack nativa mostra `trackMouse:inRect:ofView:untilMouseUp` in `NSCell _sendActionFrom`, quindi ci si trova in un punto che gestisce un evento clic e si sta provando a richiamare C# senza risultato.

In generale, è difficile tenere traccia di errori di questo. È stato aggiunto `GC.Collect(2)` a un gestore di pulsanti per tenere traccia di questo problema (forzando una Garbage Collection) in modo che sia riproducibile. Dopo aver diviso l'esempio in due parti per un momento, rimuovendo le sezioni di codice fino alla scomparsa del problema, è risultato [questo bug](https://bugzilla.xamarin.com/show_bug.cgi?id=23378):

```csharp
mainWindowController.Window.StandardWindowButton (NSWindowButton.CloseButton).Activated += HandleActivated;
```

Il bug era dovuto al fatto che `NSButton` restituito da `StandardWindowButton()` veniva raccolto anche se vi era stato registrato un evento. Quando si prova a chiamare tale evento facendo clic, se il pulsante è stato raccolto dal Garbage Collector, si verifica l'arresto anomalo.

Anche se non si trattava della causa radice di questo particolare problema, analisi dello stack come questa possono essere causate anche da firme di metodi non corrette nelle funzioni esportate con `[Export]` in Objective-C. Se ad esempio, un metodo prevede che un parametro sia `out string` e lo si digita come `string`, può verificarsi allo stesso modo un arresto anomalo.

## <a name="example-3-callbacks-and-managed-objects"></a>Esempio 3: Callback e oggetti gestiti

Molte API Cocoa vengono automaticamente "richiamate" dalla libreria quando si verifica un evento, offrendo la possibilità di rispondere, o quando sono necessari alcuni dati per eseguire un'attività. Anche se è normale pensare subito ai modelli **Delegate** e **DataSource**, esiste un gran numero di API che funzionano in questo modo. Quando, ad esempio, si esegue l'override dei metodi di `NSView` e quindi lo si inserisce nella struttura ad albero visuale, ci si aspetta un callback da parte di AppKit quando si verificano determinati eventi.

Nella quasi totalità dei casi, Xamarin.Mac eviterà correttamente che la destinazione oggetto gestito di questi callback venga raccolta nel Garbage Collector, anche se può ancora essere richiamata. Alcuni rari bug nel binding possono tuttavia interrompere questa procedura. In questo caso, è possibile che vengano visualizzati arresti anomali indesiderati simili al seguente:

```csharp
Thread 0 Crashed:: Dispatch queue: com.apple.main-thread

0   libsystem_kernel.dylib              0x98c2f69a __pthread_kill + 10
1   libsystem_pthread.dylib             0x90341f19 pthread_kill + 101
2   libsystem_c.dylib                   0x9453feee abort + 156
3   libmonosgen-2.0.dylib               0x020bfba5 mono_handle_native_sigsegv + 757
4   libmonosgen-2.0.dylib               0x0210b812 mono_arch_handle_altstack_exception + 162
5   libmonosgen-2.0.dylib               0x0200c55e mono_sigsegv_signal_handler + 446
6   libsystem_platform.dylib            0x9513003b _sigtramp + 43
7   ???                                 0xffffffff 0 + 4294967295
8   libmonosgen-2.0.dylib               0x0200c3a0 mono_sigill_signal_handler + 48
9   com.apple.AppKit                    0x99f76041 -[NSView setFrame:] + 448
10  com.apple.AppKit                    0x9a1fd4ea -[NSToolbarView adjustToWindow:attachedToEdge:] + 198
11  com.apple.AppKit                    0x9a1fd414 -[NSToolbar _adjustViewToWindow] + 68
12  com.apple.AppKit                    0x9a01eb0d -[NSToolbar _windowWillShowToolbar] + 79

```

Questa guida consente di tenere traccia di eventuali bug di questo tipo, di segnalarli correttamente in modo che possano essere corretti e, fino ad allora, di trovare una soluzione alternativa nel codice.

### <a name="locating"></a>Individuazione

Quasi sempre con i bug di questo tipo, il sintomo principale è un arresto anomalo nativo, in genere con elementi simili a `mono_sigsegv_signal_handler` o `_sigtrap` nei frame in alto dello stack. Cocoa sta provando a richiamare il codice C#, raggiungendo un oggetto raccolto dal Garbage Collector e provocando un arresto anomalo. Non tutti gli arresti anomali del sistema con questi simboli sono tuttavia causati da un problema di binding come questo e sarà necessario effettuare altre indagini per confermare che si tratta proprio di questo problema. 

È difficile tenere traccia di questi bug perché si verificano solo **dopo** che una Garbage Collection ha eliminato l'oggetto in questione. Se si ritiene che si sia verificato uno di questi bug, aggiungere il codice seguente in un punto della sequenza di avvio:

```csharp
new System.Threading.Thread (() => 
{
    while (true) {
         System.Threading.Thread.Sleep (1000);
         GC.Collect ();
    }
}).Start (); 
```

Questo codice forzerà l'applicazione a eseguire il Garbage Collector ogni secondo. Eseguire nuovamente l'applicazione e provare a riprodurre il bug. Se l'arresto anomalo si verifica immediatamente o sistematicamente invece che in modo casuale, si è sulla strada giusta.

### <a name="reporting"></a>Report

Il passaggio successivo consiste nel segnalare il problema a Xamarin in modo che il binding possa essere corretto nelle versioni future. I titolari di una licenza Business o Enterprise possono aprire un ticket all'indirizzo 

[http://xamarin.com/support](http://xamarin.com/support)

In caso contrario, cercare un problema esistente:

- Controllare i [forum di Xamarin.Mac](https://forums.xamarin.com/categories/mac)
- Cercare nel [repository di problemi](https://github.com/xamarin/xamarin-macios/issues)
- Prima di passare ai problemi di GitHub, si è tenuta traccia dei problemi di Xamarin in [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi), dove è possibile cercare i problemi corrispondenti.
- Se non si riesce a trovare un problema corrispondente, inserirne uno nuovo nel [repository di problemi GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

I problemi di GitHub sono tutti pubblici. Non è possibile nascondere commenti o allegati. 

Includere tutte le informazioni disponibili seguenti:

- Un esempio semplice che riproduce il problema. Questo è **molto importante**, ove possibile. 
- L'analisi dello stack completa dell'arresto anomalo.
- Il codice C# relativo all'arresto anomalo.   

### <a name="working-around"></a>Soluzione alternativa

Dopo avere individuato il problema, può essere semplice applicarvi una patch con una soluzione alternativa finché il binding non viene corretto. Lo scopo è evitare che l'oggetto (**View**, **Delegate**, **DataSource**) eliminato per errore non lasci la memoria, mantenendo un riferimento aperto.

Nei casi semplici in cui è presente una sola istanza dell'oggetto, modificare il codice seguente:

```csharp
void AddObject ()
{
    item.View = new MyView ();
    ...
}
```

Per poter usare una variabile statica come la seguente:

```csharp
static NSObject view;
...

void AddObject ()
{
    view = new MyView ();
    item.View = view;
    ...
}
```

Nei casi in cui possono essere create più istanze, è possibile usare un elemento `HashSet` statico:

```csharp
static HashSet<NSObject> collection = new HashSet<NSObject> ();
...

void AddObject ()
{
    item.View = new MyView ();
    collection.Add (item.View );
    ...
}
```

Dopo che il binding è stato corretto ed è stato eseguito l'aggiornamento alla versione di Xamarin.Mac che include la correzione, il codice con la soluzione alternativa può essere rimosso.

## <a name="exception-bubbling-and-objective-c"></a>Bubbling di eccezioni e Objective-C

Non è mai consigliabile consentire a un'eccezione C# di "eludere" il codice gestito per il metodo Objective-C chiamante. In caso contrario, i risultati non sono definiti, ma in genere comportano un arresto anomalo. In generale, verrà fatto tutto il possibile per visualizzare informazioni utili per gli arresti anomali del sistema sia nativi che gestiti e consentire di risolvere rapidamente i problemi.

Senza perdersi nei motivi tecnici, la configurazione dell'infrastruttura per intercettare le eccezioni gestite in ogni limite gestito/nativo è piuttosto costosa e in molte operazioni comuni è presente un _numero elevato_ di transizioni. Molte operazioni, in particolare quelle che coinvolgono il thread dell'interfaccia utente devono terminare rapidamente per evitare lo stuttering dell'app e livelli di prestazioni inaccettabili. Molti di tali callback eseguono operazioni molto semplici che di rado possono generare eccezioni, quindi questo sovraccarico sarebbe costoso e inutile in tali casi.

Tali istruzioni try/catch non sono quindi state configurate. Nei casi in cui il codice esegue operazioni non semplici (più complesse, ad esempio, della restituzione di un valore booleano o di una semplice operazione matematica), è possibile inserire manualmente un'istruzione try catch. 
