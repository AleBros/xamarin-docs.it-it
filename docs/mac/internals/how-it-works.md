---
title: Funzionamento di Xamarin.Mac
description: Questo documento descrive i processi interni di Xamarin.Mac. In particolare, esamina i costruttori, gestione della memoria, prima di quelli di compilazione e il programma di registrazione.
ms.prod: xamarin
ms.assetid: C2053ABB-6DBF-4233-AEEA-B72FC6A81FE1
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/25/2017
ms.openlocfilehash: baa60d177a7d7d070a218108b2f6779eeaf94f78
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30787089"
---
# <a name="how-xamarinmac-works"></a>Funzionamento di Xamarin.Mac

La maggior parte dei casi, lo sviluppatore non è necessario preoccuparsi di interno "magiche" di Xamarin.Mac, tuttavia, con una conoscenza di come sarà utile per operazioni funzionamento dietro le quinte interpretazione documentazione esistente con un obiettivo c# sia il debug dei problemi approssimativa quando si verificano.

In Xamarin.Mac, un'applicazione bridge tra due mondi: è il runtime Objective-C in base che contiene le istanze di classi native (`NSString`, `NSApplication`e così via) ed è presente il runtime in c# che contiene le istanze di classi gestite (`System.String`, `HttpClient`e così via). Tra questi due mondi, Xamarin.Mac crea un bridge a due vie in modo un'applicazione può chiamare i metodi (selettori) in Objective-C (ad esempio `NSApplication.Init`) e Objective-C può chiamare i metodi dell'app c# nuovamente (come i metodi su un delegato di app). In generale, le chiamate in Objective-C vengono gestite in modo trasparente tramite **P/Invoke** e il codice runtime Xamarin offre.

<a name="exposing-classes" />

## <a name="exposing-c-classes--methods-to-objective-c"></a>Esposizione di classi c# / metodi per Objective-C

Tuttavia, per Objective-C richiamare gli oggetti di un'app c#, devono essere esposte in modo che possa comprendere Objective-C. Questa operazione viene eseguita tramite il `Register` e `Export` gli attributi. Vedere l'esempio seguente:

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

In questo esempio, il runtime Objective-C ora conosceranno una classe denominata `MyClass` con selettori chiamati `init` e `run`.

Nella maggior parte dei casi, questo è un dettaglio di implementazione che lo sviluppatore può ignorare, poiché la maggior parte dei callback riceve un'app saranno tramite metodi sottoposti a override in `base` classi (ad esempio `AppDelegate`, `Delegates`, `DataSources`) oppure  **Azioni** passato nelle API. In tutti questi casi, `Export` attributi non sono necessari nel codice c#.

## <a name="constructor-runthrough"></a>Costruttore runthrough

In molti casi, lo sviluppatore dovrà esporre c# classi costruzione dell'app API al runtime di Objective-C in modo che può essere implementato da posizioni, ad esempio quando viene chiamato in file dello Storyboard o XI. Ecco i costruttori di cinque più comuni utilizzati nelle applicazioni Xamarin.Mac:

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

In generale, è consigliabile lasciare lo sviluppatore di `IntPtr` e `NSCoder` costruttori generati durante la creazione di alcuni tipi, ad esempio custom `NSViews` da solo. Se è necessario chiamare uno di questi costruttori in risposta a una richiesta di runtime Objective-C Xamarin.Mac e aver rimosso, verificherà un arresto anomalo dell'app all'interno di codice nativo e potrebbe risultare difficile individuare esattamente il problema.

## <a name="memory-management-and-cycles"></a>Cicli e gestione della memoria

Gestione della memoria in Xamarin.Mac è per molti aspetti molto simili a xamarin. IOS. È anche un argomento complesso uno esula dall'ambito di questo documento. Leggere la [memoria e prestazioni ottimali](~/cross-platform/deploy-test/memory-perf-best-practices.md).

## <a name="ahead-of-time-compilation"></a>Avanti di compilazione

In genere, le applicazioni .NET non compilare fino a codice macchina quando vengono generati, a un livello intermedio chiamato IL codice che ottiene invece compilano _Just-In-Time_ (JIT) compilate in codice macchina quando viene avviata l'app.

Il tempo impiegato il runtime mono per la compilazione JIT questo codice macchina può rallentare l'avvio di un'app Xamarin.Mac fino al 20%, come il tempo per il codice necessario macchina deve essere generato.

A causa delle limitazioni imposte da Apple in iOS, compilazione JIT del codice di linguaggio intermedio non è disponibile per xamarin. IOS. Di conseguenza, tutte le app xamarin sono pieni _Ahead di tempo_ (AOT) compilate in codice macchina durante il ciclo di compilazione.

Nuovo per Xamarin.Mac è la possibilità di AOT IL codice durante il ciclo di compilazione di app, esattamente come è possibile xamarin. IOS. Xamarin.Mac utilizza un _ibrida_ approccio AOT che compila la maggior parte del codice macchina necessario, ma consente al runtime di compilare trampolines necessari e la flessibilità necessaria per continuare a supportare Emit (e altri casi attualmente funziona Xamarin.Mac).

Ci sono due aree principali in cui AOT consente un'app Xamarin.Mac:

- **Meglio i registri di arresto anomalo del sistema "nativa"** : se un'applicazione Xamarin.Mac si blocca nel codice nativo, ovvero problema comune quando si effettuano chiamate non valide nelle API Cocoa (ad esempio l'invio di un `null` in un metodo che non lo accetta) nativi di arresto anomalo del log con JIT frame sono difficili da analizzare. Poiché i frame JIT non dispone di informazioni di debug, saranno presenti più righe con gli offset esadecimale e nessuna indicazione di ciò che era in corso. AOT genera frame denominati "real" e le tracce siano molto più facili da leggere. Questo significa anche app Xamarin.Mac interagisce meglio gli strumenti nativi, ad esempio **lldb** e **strumenti**.
- **Avviare migliori prestazioni in fase di** : per le applicazioni di grandi dimensioni di Xamarin.Mac, con un più secondo tempo di avvio, tutto il codice di compilazione JIT possono richiedere una quantità significativa di tempo. AOT esegue queste operazioni in anticipo.

### <a name="enabling-aot-compilation"></a>Abilitazione di compilazione AOT

AOT è abilitata in Xamarin.Mac facendo doppio clic sul **nome progetto** nel **Esplora**, la navigazione a **Mac compilare** e l'aggiunta di `--aot:[options]` per il  **Argomenti aggiuntivi mmp:** campo (dove `[options]` è una o più opzioni per controllare il tipo AOT, vedere di seguito). Ad esempio:

![Aggiunta di AOT agli argomenti aggiuntivi mmp](how-it-works-images/aot01.png "AOT aggiunta agli argomenti aggiuntivi mmp")

> [!IMPORTANT]
> L'abilitazione di AOT compilazione aumenta notevolmente il tempo di compilazione, a volte alcuni minuti, ma è possibile migliorare i tempi di avvio dell'app da una media del 20%. Di conseguenza, la compilazione AOT deve essere abilitata solo nei **versione** build di un'app Xamarin.Mac.

### <a name="aot-compilation-options"></a>Opzioni di compilazione AOT

Sono disponibili diverse opzioni che possono essere modificate quando si abilita la compilazione AOT in un'app Xamarin.Mac:

- `none` -Nessuna compilazione AOT. Questa è l'impostazione predefinita.
- `all` -AOT compila ogni assembly nel MonoBundle.
- `core` -AOT compila il `Xamarin.Mac`, `System` e `mscorlib` assembly.
- `sdk` -AOT compila il `Xamarin.Mac` e gli assembly di librerie di classi Base (BCL).
- `|hybrid` -Aggiunta a una delle opzioni sopra indicate in questo modo, ibrido AOT che consente di eliminare IL, ma verrà comporterà compilato più volte.
- `+` -Include un singolo per la compilazione AOT.
- `-` -Rimuove un singolo file di compilazione AOT.

Ad esempio, `--aot:all,-MyAssembly.dll` consentirebbe compilazione AOT in tutti gli assembly di MonoBundle _tranne_ `MyAssembly.dll` e `--aot:core|hybrid,+MyOtherAssembly.dll,-mscorlib.dll` consentirebbe ibrido, includere codice AOT il `MyOtherAssembly.dll` ed esclusi i `mscorlib.dll`.

## <a name="partial-static-registrar"></a>Programma di registrazione statico parziale

Quando si sviluppa un'app Xamarin.Mac, riducendo al minimo il tempo tra il completamento di una modifica e il relativo test può diventare importante per il rispetto dei cicli di sviluppo. Strategie, ad esempio la modularità di basi di codice e unit test può contribuire a ridurre i tempi di compilazione, poiché riducono il numero di volte in cui un'applicazione che richiede una ricompilazione completa costosa.

Inoltre e nuovi per Xamarin.Mac, _parziale Registrar statico_ (come proposto volta da xamarin ha) può ridurre notevolmente i tempi di avvio di un'app Xamarin.Mac nel **Debug** configurazione. Comprendere come è possibile utilizzare il programma di registrazione statico parziale viene compresso un quasi un miglioramento 5x debug avvio richiederà un po' di sfondo su qual è il programma di registrazione, che cos'è la differenza tra statici e dinamici e scopo di questa versione di "statica parziale".

### <a name="about-the-registrar"></a>Sulla funzione di registrazione

Dietro le quinte di qualsiasi Xamarin.Mac applicazione si trova il framework Cocoa da Apple e il runtime Objective-C. La creazione di un bridge tra questo world"nativa" e "world gestita" di c# è il principale responsabile di Xamarin.Mac. Ambito di questa attività viene gestita dal programma di registrazione, che viene eseguita all'interno `NSApplication.Init ()` metodo. Si tratta di uno dei motivi che per utilizzare l'API Cocoa in Xamarin.Mac necessario `NSApplication.Init` a essere chiamato per primo.

Processo del programma di registrazione è informare il runtime Objective-C l'esistenza dell'app in c# le classi che derivano da classi quali `NSApplicationDelegate`, `NSView`, `NSWindow`, e `NSObject`. Ciò richiede un'analisi di tutti i tipi nell'app per determinare le esigenze di registrazione e gli elementi di ogni tipo di report.

Questa analisi può avvenire **dinamicamente**, all'avvio dell'applicazione tramite la reflection, o **staticamente**, come una fase di compilazione. Durante la selezione di un tipo di registrazione, lo sviluppatore deve essere compatibile con le seguenti:

- Registrazione statica può ridurre drasticamente i tempi di avvio, ma può rallentare volte le compilazioni significativamente (in genere più del doppio fase di compilazione di debug). Questo sarà il valore predefinito per **versione** configurazione Compila.
- La registrazione dinamica Ritarda questo lavoro fino a quando l'applicazione, avviare e ignora la generazione di codice, ma questo lavoro aggiuntivo è possibile creare una pausa evidente (almeno due secondi) nell'avvio dell'applicazione. Ciò è particolarmente evidente nelle build di configurazione di debug, che per impostazione predefinita per la registrazione dinamica e la cui la libreria reflection è più lenta.

Registrazione statico parziale, introdotti inizialmente in xamarin. IOS 8.13, fornisce allo sviluppatore il meglio di entrambe le opzioni. Per pre-elaborazione le informazioni di registrazione di ogni elemento in `Xamarin.Mac.dll` e shipping queste informazioni con Xamarin.Mac in una libreria statica (solo deve essere collegato a in fase di compilazione), è stata rimossa la maggior parte dei casi la reflection di dinamica Registrar durante non influire sul tempo di compilazione.

### <a name="enabling-the-partial-static-registrar"></a>Abilitare il programma di registrazione statico parziale

Il programma di registrazione statico parziale è attivata in Xamarin.Mac facendo doppio clic sul **nome progetto** nel **Esplora**, la navigazione a **Mac compilare** e l'aggiunta di `--registrar:static` per il **argomenti aggiuntivi mmp:** campo. Ad esempio:

![Aggiungere il programma di registrazione statico parziale argomenti aggiuntivi mmp](how-it-works-images/psr01.png "aggiungere il programma di registrazione statico parziale mmp ulteriori argomenti")

## <a name="additional-resources"></a>Risorse aggiuntive

Ecco alcune spiegazioni più dettagliate su funzionamento internamente:

- [Selettori Objective-C](~/ios/internals/objective-c-selectors.md)
- [Registrar](~/ios/internals/registrar.md)
- [Xamarin Unified API per iOS e OS X](~/cross-platform/macios/unified/index.md)
- [Nozioni fondamentali su Theading](~/ios/app-fundamentals/threading.md)
- [Delegati, protocolli e gli eventi](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Su `newrefcount`](~/ios/internals/newrefcount.md)

