---
title: Come funziona xamarin. Mac
description: Questo documento descrive i meccanismi interni di xamarin. Mac. In particolare, analizza i costruttori, gestione della memoria, in anticipo rispetto alla compilazione e il programma di registrazione.
ms.prod: xamarin
ms.assetid: C2053ABB-6DBF-4233-AEEA-B72FC6A81FE1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 05/25/2017
ms.openlocfilehash: cd5371cde1dfcbe3cb1aea5dbdf8439816d66d95
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111317"
---
# <a name="how-xamarinmac-works"></a>Come funziona xamarin. Mac

La maggior parte dei casi lo sviluppatore non sarà mai necessario preoccuparsi di interno "magic" di xamarin. Mac, tuttavia, avere una conoscenza approssimativa del modo in cui funziona cose dietro le quinte fornirà assistenza nell'entrambi interpretazione documentazione esistente con un C# obiettivo e il debug problemi quando si verificano.

In xamarin. Mac, un'applicazione bridge tra due mondi: non è presente il runtime di Objective-C basata contenente istanze di classi native (`NSString`, `NSApplication`e così via) ed è presente il C# runtime che contiene le istanze di classi gestite (`System.String` `HttpClient`e così via). Tra questi due mondi, xamarin. Mac consente di creare un bridge bidirezionali in modo che un'app può chiamare metodi (selettori) in Objective-C (ad esempio `NSApplication.Init`) e Objective-C possono chiamare l'app C# eseguire il backup di metodi (ad esempio, i metodi in un delegato dell'app). In generale, le chiamate in Objective-C vengono gestite in modo trasparente attraverso **P/Invoke** e codice runtime Xamarin offre.

<a name="exposing-classes" />

## <a name="exposing-c-classes--methods-to-objective-c"></a>Esposizione di C# classi o metodi di Objective-c

Tuttavia, per Objective-C richiamare in un'app C# oggetti, devono essere esposte in modo da Objective-C possono comprendere. Questa operazione viene eseguita tramite il `Register` e `Export` attributi. Vedere l'esempio seguente:

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

In questo esempio, il runtime di Objective-C saprà ora su una classe denominata `MyClass` con i selettori chiamati `init` e `run`.

Nella maggior parte dei casi, questo è un dettaglio di implementazione che lo sviluppatore può ignorare, poiché la maggior parte dei callback di un'app riceve saranno tramite metodi sottoposti a override nel `base` classi (ad esempio `AppDelegate`, `Delegates`, `DataSources`) o scegliere  **Azioni** passato alle API. In tutti questi casi `Export` gli attributi non sono necessari nella C# codice.

## <a name="constructor-runthrough"></a>Costruttore runthrough

In molti casi, lo sviluppatore deve esporre l'app C# costruzione di classi API per il runtime di Objective-C in modo che è possibile creare istanze da diverse posizioni, ad esempio quando chiamato in XIB dello Storyboard o del file. Ecco i cinque costruttori più comuni usati nelle App xamarin. Mac:

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

In generale, lo sviluppatore è consigliabile lasciare il `IntPtr` e `NSCoder` costruttori generati durante la creazione di alcuni tipi, ad esempio custom `NSViews` da solo. Se xamarin. Mac è necessario chiamare uno di questi costruttori in risposta a una richiesta di runtime di Objective-C ed è stato rimosso, l'app si arresterà in codice nativo e potrebbe essere difficile individuare esattamente il problema.

## <a name="memory-management-and-cycles"></a>Cicli e gestione della memoria

Gestione della memoria in xamarin. Mac è per molti aspetti molto simili a xamarin. IOS. È anche un argomento complesso, uno esula dall'ambito di questo documento. Leggi i [consigliate per le prestazioni e memoria](~/cross-platform/deploy-test/memory-perf-best-practices.md).

## <a name="ahead-of-time-compilation"></a>-Ahead della fase di compilazione

In genere, le applicazioni .NET non compilare fino al codice macchina quando vengono compilati, ma vengono compilate a livello intermedio denominato codice linguaggio intermedio che ottiene _Just-In-Time_ (JIT) compilate in codice macchina quando l'app viene avviata.

Il tempo necessario il runtime di mono per la compilazione JIT questo codice macchina rallentando l'avvio di un'app xamarin. Mac fino al 20%, perché richiede tempo per il codice necessario macchina deve essere generato.

A causa delle limitazioni imposte da Apple iOS, la compilazione JIT del codice di linguaggio intermedio non è disponibile per xamarin. IOS. Di conseguenza, tutte le app xamarin. IOS sono pieni _Ahead Of Time_ (AOT) compilate in codice macchina durante il ciclo di compilazione.

Nuovo a xamarin. Mac è la capacità di AOT IL codice durante il ciclo di compilazione di app, proprio come xamarin. IOS possono. Xamarin. Mac Usa una _ibrida_ approccio AOT che compila la maggior parte del codice macchina necessaria, ma consente al runtime per la compilazione trampolines necessari e la flessibilità necessaria per continuare a supportare Reflection. Emit (e altri usi casi che attualmente funziona con xamarin. Mac).

Esistono due aree principali in cui AOT consentono di un'app xamarin. Mac:

- **Meglio i log di arresto anomalo del sistema "nativa"** : se un'applicazione xamarin. Mac si blocca nel codice nativo, ovvero problema comune quando si effettuano chiamate non è valide nelle API Cocoa (ad esempio l'invio un `null` in un metodo che non lo accetta) nativa di log con JIT di arresto anomalo frame sono difficili da analizzare. Poiché i frame JIT non dispongono di informazioni di debug, vi saranno più righe con gli offset esadecimale e senza alcuna indicazione di cosa stava accadendo. AOT genera frame denominati "real" e le tracce sono molto più facili da leggere. Questo significa anche app xamarin. Mac interagisce meglio con gli strumenti nativi, ad esempio **lldb** e **Instruments**.
- **Avviare migliori prestazioni in fase di** : per le applicazioni di grandi dimensioni di xamarin. Mac, con un più secondo tempo di avvio, tutto il codice di compilazione JIT possono richiedere una quantità significativa di tempo. AOT esegue queste operazioni fin dall'inizio.

### <a name="enabling-aot-compilation"></a>Abilitare la compilazione AOT

AOT è abilitata in xamarin. Mac facendo doppio clic il **nome progetto** nel **Esplora soluzioni**, il passaggio a **compilazione Mac** e l'aggiunta di `--aot:[options]` al  **Argomenti aggiuntivi di mmp:** campo (dove `[options]` corrisponde a uno o più opzioni per controllare il tipo AOT, vedere di seguito). Ad esempio:

![Aggiunta di AOT agli argomenti aggiuntivi di mmp](how-it-works-images/aot01.png "AOT aggiunta agli argomenti aggiuntivi di mmp")

> [!IMPORTANT]
> Abilita AOT compilazione aumenta notevolmente il tempo di compilazione, a volte fino a qualche minuto, ma può migliorare i tempi di avvio dell'app da una media pari al 20%. Di conseguenza, la compilazione AOT deve essere abilitata solo nei **rilascio** le compilazioni di un'app xamarin. Mac.

### <a name="aot-compilation-options"></a>Opzioni di compilazione AOT

Esistono diverse opzioni che possono essere modificate quando si abilita la compilazione AOT in un'app xamarin. Mac:

- `none` -Nessuna compilazione AOT. Questa è l'impostazione predefinita.
- `all` -AOT compila ogni assembly nel MonoBundle.
- `core` -Compilazione AOT i `Xamarin.Mac`, `System` e `mscorlib` assembly.
- `sdk` -AOT viene compilato il `Xamarin.Mac` e gli assembly di librerie di classi Base (BCL).
- `|hybrid` -Aggiunta a una delle opzioni precedenti in questo modo ibrido AOT che consente la rimozione dei livello di integrità, ma verrà comporterà tempi di compilazione più.
- `+` -Include un'unica per la compilazione AOT.
- `-` -Rimuove un singolo file dalla compilazione AOT.

Ad esempio, `--aot:all,-MyAssembly.dll` avrebbero consentito la compilazione AOT in tutti gli assembly di MonoBundle _tranne_ `MyAssembly.dll` e `--aot:core|hybrid,+MyOtherAssembly.dll,-mscorlib.dll` consentirebbe ibrida, includere codice AOT il `MyOtherAssembly.dll` ed esclusione di `mscorlib.dll`.

## <a name="partial-static-registrar"></a>Programma di registrazione statico parziale

Quando si sviluppa un'app xamarin. Mac, riducendo al minimo il tempo tra il completamento di una modifica e il relativo test diventa importante per lo sviluppo scadenze. Le strategie, ad esempio la modularizzazione della codebase e gli unit test possono contribuire a ridurre i tempi di compilazione, poiché riducono il numero di volte che un'app richiede una ricompilazione completa costosa.

Inoltre e familiarità con xamarin. Mac, _Registrar statico parziali_ (come introdotti da xamarin. IOS) può ridurre notevolmente i tempi di avvio di un'app xamarin. Mac nel **Debug** configurazione. Comprendere come è possibile usando il programma di registrazione statico parziale viene compresso un quasi un miglioramento di 5 volte nell'avvio del debug richiederà un po' di sfondo su che cos'è il registrar, che cos'è la differenza tra statico e dinamico e del funzionamento di questa versione di "statica parziale".

### <a name="about-the-registrar"></a>Sulle registrar

Dietro le quinte di qualsiasi xamarin. Mac applicazione si trova il framework di Cocoa da Apple e il runtime di Objective-C. Creazione di un bridge tra questo mondo"nativo" e "mondo gestito" di C# è il principale responsabile di xamarin. Mac. Parte di questa attività viene gestita dal programma di registrazione, che viene eseguito all'interno di `NSApplication.Init ()` (metodo). Si tratta di uno dei motivi che qualsiasi uso di API Cocoa in xamarin. Mac richiede `NSApplication.Init` per essere chiamato per primo.

Processo del registrar consiste nell'informare il runtime di Objective-C dell'esistenza dell'app C# le classi che derivano da classi, ad esempio `NSApplicationDelegate`, `NSView`, `NSWindow`, e `NSObject`. Ciò richiede un'analisi di tutti i tipi nell'app per determinare ciò che richiede la registrazione e gli elementi in ogni tipo di report.

Questa analisi può avvenire **dinamicamente**, all'avvio dell'applicazione con la reflection, o **staticamente**, come passaggio di tempo di compilazione. Durante la selezione di un tipo di registrazione, lo sviluppatore deve essere tenere presente quanto segue:

- Registrazione statica può ridurre drasticamente i tempi di avvio, ma può rallentare a volte le compilazioni significativamente (in genere più del doppio fase di compilazione di debug). Questo sarà il valore predefinito per **rilascio** compila configurazione.
- Registrazione dinamica Ritarda questo lavoro fino a quando l'applicazione avvia e ignora la generazione di codice, ma questo lavoro aggiuntivo può creare una pausa evidente (almeno due secondi) nell'avvio dell'applicazione. Ciò è particolarmente evidente nelle build di configurazione di debug, la cui impostazione predefinita per la registrazione dinamica e la cui la reflection è più lenta.

Parziali delle registrazioni statico, introdotti inizialmente in xamarin. IOS 8.13, offre agli sviluppatori il meglio di entrambe le opzioni. Pre-elaborando le informazioni di registrazione di ogni elemento `Xamarin.Mac.dll` e spedizione queste informazioni con xamarin. Mac in una libreria statica (che deve solo essere collegato a in fase di compilazione), Microsoft ha rimosso la maggior parte dei casi la reflection di dinamica Registrar durante senza influire sulla fase di compilazione.

### <a name="enabling-the-partial-static-registrar"></a>Abilitare il programma di registrazione statico parziale

Il programma di registrazione statico parziale è abilitata in xamarin. Mac facendo doppio clic il **nome progetto** nel **Esplora soluzioni**, il passaggio a **di compilazione Mac** e l'aggiunta di `--registrar:static` per il **argomenti aggiuntivi di mmp:** campo. Ad esempio:

![Aggiungendo le registrar statico parziale di argomenti aggiuntivi di mmp](how-it-works-images/psr01.png "aggiungendo le registrar statico parziale di argomenti aggiuntivi di mmp")

## <a name="additional-resources"></a>Risorse aggiuntive

Ecco alcune spiegazioni più dettagliate su come funziona internamente:

- [Selettori Objective-C](~/ios/internals/objective-c-selectors.md)
- [Registrar](~/ios/internals/registrar.md)
- [API unificata di Xamarin per iOS e OS X](~/cross-platform/macios/unified/index.md)
- [Nozioni fondamentali su Theading](~/ios/app-fundamentals/threading.md)
- [I delegati, protocolli e gli eventi](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Su `newrefcount`](~/ios/internals/newrefcount.md)

