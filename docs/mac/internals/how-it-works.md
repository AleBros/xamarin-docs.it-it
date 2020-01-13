---
title: Come funziona Xamarin.Mac
description: Questo documento descrive i meccanismi interni di Xamarin.Mac. In particolare, vengono esaminati i costruttori, la gestione della memoria, la compilazione anticipata del tempo e il registrar.
ms.prod: xamarin
ms.assetid: C2053ABB-6DBF-4233-AEEA-B72FC6A81FE1
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 05/25/2017
ms.openlocfilehash: 347fb1021a290fa849ae354468bc66b0cdd8b684
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017587"
---
# <a name="how-xamarinmac-works"></a>Come funziona Xamarin.Mac

Nella maggior parte dei casi lo sviluppatore non dovrà mai preoccuparsi della "magia" interna di Xamarin.Mac, tuttavia, avendo una conoscenza approssimativa del modo in cui le cose funzionano dietro le quinte sarà utile per interpretare la documentazione C# esistente con un obiettivo e il debug problemi quando si verificano.

In Xamarin.Mac un'applicazione Bridge due mondi: è disponibile il runtime di Objective-C che contiene le istanze delle classi native (`NSString`, `NSApplication`e così via) ed è C# presente il runtime che contiene le istanze delle classi gestite (`System.String`,`HttpClient`e così via) . Tra questi due mondi, Xamarin.Mac crea un Bridge bidirezionale, in modo che un'app possa chiamare metodi (selettori) in Objective-C (ad esempio `NSApplication.Init`) e Objective-C può chiamare C# i metodi dell'app, come i metodi in un delegato dell'app. In generale, le chiamate in Objective-C vengono gestite in modo trasparente tramite **P/Invoke** e il codice di runtime fornito da Novell.

<a name="exposing-classes" />

## <a name="exposing-c-classes--methods-to-objective-c"></a>Esposizione di C# classi/metodi a Objective-C

Tuttavia, per fare in modo che Objective-C richiami negli C# oggetti di un'app, è necessario esporli in modo che Objective-c possa comprendere. Questa operazione viene eseguita tramite gli attributi `Register` e `Export`. Vedere l'esempio seguente:

```csharp
[Register ("MyClass")]
public class MyClass : NSObject
{
   [Export ("init")]
   public MyClass ()
   {
   }

   [Export ("run")]
   public void Run ()
   {
   }
}
```

In questo esempio, il runtime di Objective-C saprà che una classe denominata `MyClass` con i selettori denominati `init` e `run`.

Nella maggior parte dei casi, si tratta di un dettaglio di implementazione che lo sviluppatore può ignorare, in quanto la maggior parte delle richiamate ricevute da un'app sarà tramite metodi sottoposti a override sulle classi `base` (ad esempio `AppDelegate`, `Delegates`, `DataSources`) o sulle **azioni** passate alle API. In tutti questi casi, non è necessario `Export` attributi nel C# codice.

## <a name="constructor-runthrough"></a>Costruttore runthrough

In molti casi, lo sviluppatore deve esporre l'API di costruzione delle C# classi dell'app al runtime di Objective-C, in modo che sia possibile crearne un'istanza da posizioni quali, ad esempio, quando vengono chiamate in file storyboard o XIB. Ecco i cinque costruttori più comuni usati nelle app Xamarin.Mac:

```csharp
// Called when created from unmanaged code
public CustomView (IntPtr handle) : base (handle)
{
   Initialize ();
}

// Called when created directly from a XIB file
[Export ("initWithCoder:")]
public CustomView (NSCoder coder) : base (coder)
{
   Initialize ();
}

// Called from C# to instance NSView with a Frame (initWithFrame)
public CustomView (CGRect frame) : base (frame)
{
}

// Called from C# to instance NSView without setting the frame (init)
public CustomView () : base ()
{
}

// This is a special case constructor that you call on a derived class when the derived called has an [Export] constructor.
// For example, if you call init on NSString then you don’t want to call init on NSObject.
public CustomView () : base (NSObjectFlag.Empty)
{
}
```

In generale, lo sviluppatore deve lasciare i costruttori `IntPtr` e `NSCoder` generati durante la creazione di alcuni tipi, ad esempio `NSViews` personalizzati da soli. Se Xamarin.Mac deve chiamare uno di questi costruttori in risposta a una richiesta di runtime di Objective-C ed è stata rimossa, l'app si arresterà in modo anomalo all'interno del codice nativo e potrebbe essere difficile determinare esattamente il problema.

## <a name="memory-management-and-cycles"></a>Gestione della memoria e cicli

La gestione della memoria in Xamarin.Mac è molto simile a Xamarin.iOS. È anche un argomento complesso, che esula dall'ambito di questo documento. Leggere le procedure consigliate per la [memoria e le prestazioni](~/cross-platform/deploy-test/memory-perf-best-practices.md).

## <a name="ahead-of-time-compilation"></a>Compilazione anticipata

In genere, le applicazioni .NET non vengono compilate nel codice del computer quando vengono compilate, ma vengono compilate in un livello intermedio denominato codice IL che ottiene la compilazione JIT ( _just-in-Time_ ) compilata nel codice macchina quando viene avviata l'app.

Il tempo impiegato dal runtime di mono per la compilazione JIT del codice macchina può rallentare l'avvio di un'app Xamarin.Mac fino al 20%, perché richiede tempo per la generazione del codice macchina necessario.

A causa delle limitazioni imposte da Apple in iOS, la compilazione JIT del codice IL non è disponibile per Xamarin.iOS. Di conseguenza, tutte le app Xamarin.iOS sono compilate in modo completo (AOT) nel codice del computer durante il ciclo _di_ compilazione.

Una novità di Xamarin.Mac è la possibilità di AOT il codice IL durante il ciclo di compilazione dell'app, proprio come Xamarin.iOS. Xamarin.Mac usa un approccio AOT _ibrido_ che compila la maggior parte del codice computer necessario, ma consente al runtime di compilare i tappeti elastici necessari e la flessibilità di continuare a supportare Reflection. Emit (e altri casi d'uso attualmente disponibili Xamarin.Mac).

L'AOT può aiutare un'app Xamarin.Mac in due aree principali:

- **Migliori log di arresto anomalo** del sistema: se un'applicazione Xamarin.Mac si arresta in modo anomalo nel codice nativo, situazione comune durante l'esecuzione di chiamate non valide nelle API Cocoa (ad esempio l'invio di un `null` in un metodo che non lo accetta), i log di arresto anomalo nativo con frame JIT sono difficile da analizzare. Poiché i frame JIT non dispongono di informazioni di debug, saranno presenti più righe con offset esadecimali e nessun indizio sul comportamento. AOT genera frame denominati "reali" e le tracce sono molto più facili da leggere. Questo significa anche che l'app Xamarin.Mac interagisce meglio con strumenti nativi come **LLDB** e **Instruments**.
- **Prestazioni migliori in fase di avvio** : per applicazioni Xamarin.Mac di grandi dimensioni, con un tempo di avvio di più secondi, la compilazione JIT di tutto il codice può richiedere una quantità di tempo significativa. AOT funziona in primo piano.

### <a name="enabling-aot-compilation"></a>Abilitazione della compilazione AOT

AOT è abilitato in Xamarin.Mac facendo doppio clic sul **nome del progetto** nella **Esplora soluzioni**, passando a **compilazione Mac** e aggiungendo `--aot:[options]` al campo **argomenti MMP aggiuntivi:** (dove `[options]` è una o più opzioni per controllare il tipo AOT, vedere di seguito. Esempio:

![Aggiunta di AOT ad argomenti aggiuntivi di MMP](how-it-works-images/aot01.png "Aggiunta di AOT ad argomenti aggiuntivi di MMP")

> [!IMPORTANT]
> L'abilitazione della compilazione AOT comporta un aumento significativo del tempo di compilazione, a volte fino a diversi minuti, ma può migliorare i tempi di avvio delle app in base a una media del 20%. Di conseguenza, la compilazione AOT dovrebbe essere abilitata solo nelle build di **versione** di un'app Xamarin.Mac.

### <a name="aot-compilation-options"></a>Opzioni di compilazione AOT

Sono disponibili diverse opzioni che possono essere modificate quando si Abilita la compilazione AOT in un'app Xamarin.Mac:

- `none`: nessuna compilazione AOT. Questa è l'impostazione predefinita.
- `all`-AOT compila ogni assembly nel monobundle.
- `core`-AOT compila gli assembly `Xamarin.Mac`, `System` e `mscorlib`.
- `sdk`-AOT compila gli assembly di `Xamarin.Mac` e librerie di classi base (BCL).
- `|hybrid`: l'aggiunta di questo oggetto a una delle opzioni precedenti consente l'AOT ibrida che consente la rimozione del, ma comporterà tempi di compilazione più lunghi.
- `+`: include un singolo file per la compilazione AOT.
- `-`: rimuove un singolo file dalla compilazione AOT.

Ad esempio, `--aot:all,-MyAssembly.dll` Abilita la compilazione AOT su tutti gli assembly nel monobundle _eccetto_ `MyAssembly.dll` e `--aot:core|hybrid,+MyOtherAssembly.dll,-mscorlib.dll` Abilita Hybrid, il codice AOT include il `MyOtherAssembly.dll` ed esclude la `mscorlib.dll`.

## <a name="partial-static-registrar"></a>Registrar statico parziale

Quando si sviluppa un'app Xamarin.Mac, ridurre al minimo il tempo che intercorre tra il completamento di una modifica e il testing può diventare importante per soddisfare le scadenze di sviluppo. Strategie come la modulazione di codebase e unit test possono contribuire a ridurre i tempi di compilazione, poiché riducono il numero di volte in cui un'app richiede una ricompilazione completa costosa.

Inoltre, ed è una novità di Xamarin.Mac, un _registrar statico parziale_ (come pioniere di Xamarin.iOS) può ridurre notevolmente i tempi di avvio di un'app Xamarin.Mac nella configurazione di **debug** . Per comprendere in che modo l'uso del registrar statico parziale può comportare un miglioramento di quasi un 5x nell'avvio del debug, sarà necessaria una parte di background su ciò che è il registrar, qual è la differenza tra statico e dinamico e la versione "statica parziale".

### <a name="about-the-registrar"></a>Informazioni sul registrar

Dietro le quinte di qualsiasi applicazione Xamarin.Mac si trova il framework Cocoa di Apple e il runtime di Objective-C. La creazione di un bridge tra questo "mondo nativo" e il "mondo gestito C# " di è la responsabilità principale di Xamarin.Mac. Parte di questa attività viene gestita dal registrar, che viene eseguita all'interno `NSApplication.Init ()` metodo. Questo è un motivo per cui l'uso delle API Cocoa in Xamarin.Mac richiede che `NSApplication.Init` venga chiamato per primo.

Il registrar sta per informare il runtime di Objective-C dell'esistenza delle C# classi dell'app che derivano da classi quali`NSApplicationDelegate`,`NSView`,`NSWindow`e`NSObject`. Questa operazione richiede un'analisi di tutti i tipi nell'app per determinare quali elementi devono essere registrati e quali elementi per ogni tipo di report.

Questa analisi può essere eseguita in **modo dinamico**, all'avvio dell'applicazione con reflection o in modo **statico**come passaggio della fase di compilazione. Quando si sceglie un tipo di registrazione, lo sviluppatore deve tenere presente quanto segue:

- La registrazione statica può ridurre drasticamente i tempi di avvio, ma può rallentare in modo significativo le compilazioni (in genere più del doppio tempo di compilazione di debug). Si tratta dell'impostazione predefinita per le compilazioni di configurazione di **rilascio** .
- La registrazione dinamica ritarda questo lavoro fino a quando l'applicazione non viene avviata e non ignora la generazione del codice, ma questo lavoro aggiuntivo può creare una pausa percettibile (almeno due secondi) all'avvio dell'applicazione. Questo è particolarmente evidente nelle compilazioni di configurazione di debug, che per impostazione predefinita è la registrazione dinamica e la cui Reflection è più lenta.

La registrazione statica parziale, introdotta per la prima volta in Xamarin.iOS 8,13, offre allo sviluppatore il meglio di entrambe le opzioni. Precalcolando le informazioni di registrazione di ogni elemento in `Xamarin.Mac.dll` e spedendo queste informazioni con Xamarin.Mac in una libreria statica (che deve essere collegata solo in fase di compilazione), Microsoft ha rimosso la maggior parte del tempo di reflection del registrar dinamico senza alcun effetto sul tempo di compilazione.

### <a name="enabling-the-partial-static-registrar"></a>Abilitazione del registrar statico parziale

Il registrar statico parziale è abilitato in Xamarin.Mac facendo doppio clic sul **nome del progetto** nella **Esplora soluzioni**, passando a **compilazione Mac** e aggiungendo `--registrar:static` al campo **argomenti MMP aggiuntivi:** . Esempio:

![Aggiunta del registrar statico parziale ad argomenti MMP aggiuntivi](how-it-works-images/psr01.png "Aggiunta del registrar statico parziale ad argomenti MMP aggiuntivi")

## <a name="additional-resources"></a>Risorse aggiuntive

Di seguito sono riportate alcune spiegazioni più dettagliate sul funzionamento interno:

- [Selettori Objective-C](~/ios/internals/objective-c-selectors.md)
- [Registrar](~/ios/internals/registrar.md)
- [API unificata Novell per iOS e OS X](~/cross-platform/macios/unified/index.md)
- [Nozioni fondamentali su Theading](~/ios/app-fundamentals/threading.md)
- [Delegati, protocolli ed eventi](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Informazioni su `newrefcount`](~/ios/internals/newrefcount.md)
