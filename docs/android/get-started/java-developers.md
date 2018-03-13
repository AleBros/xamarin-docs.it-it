---
title: Xamarin per sviluppatori Java
description: "Gli sviluppatori Java possono sfruttare le competenze e il codice esistente nella piattaforma Xamarin con i vantaggi che derivano dal riutilizzare il codice C#. La sintassi C# è molto simile alla sintassi Java ed entrambi i linguaggi offrono funzionalità analoghe. Ci sono inoltre funzionalità specifiche di C# che semplificano notevolmente lo sviluppo."
ms.topic: article
ms.prod: xamarin
ms.assetid: A3B6C041-4052-4E7D-999C-C4FA10BE3D67
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 240fc917a154085a459c6612f68c8b71e3ef01bb
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="xamarin-for-java-developers"></a>Xamarin per sviluppatori Java

_Gli sviluppatori Java possono sfruttare le competenze e il codice esistente nella piattaforma Xamarin con i vantaggi che derivano dal riutilizzare il codice C#. La sintassi C# è molto simile alla sintassi Java ed entrambi i linguaggi offrono funzionalità analoghe. Ci sono inoltre funzionalità specifiche di C# che semplificano notevolmente lo sviluppo._


## <a name="overview"></a>Panoramica

Questo articolo fornisce un'introduzione alla programmazione C# per sviluppatori Java, con particolare attenzione alle funzionalità del linguaggio C# usate per lo sviluppo di applicazioni Xamarin.Android. L'articolo illustra anche la differenza tra queste funzionalità e le relative controparti Java e presenta importanti funzionalità di C# (rilevanti per Xamarin.Android) non disponibili in Java. Sono inclusi collegamenti a materiale di riferimento aggiuntivo, per consentire di usare l'articolo come punto di partenza per approfondire i concetti relativi a C# e .NET.

Se si ha familiarità con Java, risulterà immediatamente familiare anche la sintassi C#. La sintassi C# è molto simile alla sintassi Java. C# è un linguaggio basato su parentesi graffe, come Java, C e C++. La sintassi C# è per molti aspetti un superset della sintassi Java, ma con alcune parole chiave rinominate e aggiuntive.

Molte importanti caratteristiche di Java sono disponibili anche in C#:

-   Programmazione orientata a oggetti basata su classi

-   Tipizzazione forte

-   Supporto per le interfacce

-   Generics

-   Garbage Collection

-   Compilazione runtime

Sia per Java che per C# la compilazione avviene in un linguaggio intermedio che viene eseguito in un ambiente di esecuzione gestito. Sia C# che Java sono tipizzati in modo statico ed entrambi i linguaggi considerano le stringhe come tipi non modificabili.
Entrambi i linguaggi usano una gerarchia di classi con singola radice. Come Java, C# supporta solo l'ereditarietà singola e non consente i metodi globali.
In entrambi i linguaggi gli oggetti vengono creati nell'heap usando la parola chiave `new` e vengono sottoposti a Garbage Collection quando non sono più usati. Entrambi i linguaggi forniscono supporto per la gestione formale delle eccezioni con la semantica `try`/`catch`. Entrambi forniscono supporto per la gestione e la sincronizzazione di thread.

Ci sono tuttavia numerose differenze tra Java e C#. Ad esempio:

-   Java non supporta le variabili locali tipizzate in modo implicito (C# supporta la parola chiave `var`).

-   In Java è possibile passare parametri solo tramite il valore, mentre in C# è possibile passarli sia tramite il valore che tramite riferimento. C# fornisce le parole chiave `ref` e `out` per passare i parametri tramite riferimento, mentre in Java non c'è una caratteristica equivalente.

-   Java non supporta le direttive per il preprocessore come `#define`.

-   Java non supporta i tipi Unsigned Integer, mentre C fornisce tipi Unsigned Integer come `ulong`, `uint`, `ushort` e `byte`.

-   Java non supporta l'overload degli operatori. C# consente l'overload degli operatori e le conversioni.

-   In un'istruzione `switch` Java, il codice può passare alla sezione switch successiva, mentre in C# la fine di ogni sezione `switch` deve presentare un'interruzione (la fine di ogni sezione deve chiudersi con un'istruzione `break`).

-   In Java le eccezioni generate da un metodo vengono specificate con la parola chiave `throws`, mentre in C# non c'è il concetto di eccezioni verificate e la parola chiave `throws` non è supportata.

-   C# supporta Language Integrated Query (LINQ), che consente di usare le parole chiave riservate `from`, `select` e `where` per scrivere query sulle raccolte in modo simile alle query di database.


Naturalmente ci sono molte altre differenze tra C# e Java che esulano dall'ambito di questo articolo. Java e C# inoltre continuano a evolversi (Java 8, ad esempio, che non fa ancora parte della toolchain Android, supporta espressioni lambda in stile C#) e quindi queste differenze cambieranno nel corso del tempo. In questo articolo vengono illustrate solo le differenze più importanti attualmente riscontrate dagli sviluppatori Java che non hanno familiarità con Xamarin.Android.

-   [Dallo sviluppo Java a C#](#fundamentals) fornisce un'introduzione alle principali differenze tra i linguaggi C# e Java.

-   [Funzionalità di programmazione orientate a oggetti](#oopfeatures) illustra le più importanti differenze tra le funzionalità orientate a oggetti tra i due linguaggi.

-   [Differenze tra parole chiave](#keywords) fornisce una tabella di parole chiave equivalenti utili, parole chiave solo C#-e collegamenti alle definizioni delle parole chiave C#.

C# rende disponibili in Xamarin.Android numerose funzionalità che attualmente non sono disponibili per gli sviluppatori Java in Android. Queste funzionalità consentono di scrivere codice migliore in meno tempo:

-   [Proprietà](#properties): con il sistema di proprietà C#, è possibile accedere alle variabili membro in modo sicuro e diretto senza dover scrivere metodi setter e getter.

-   [Espressioni lambda](#lambdas): in C# è possibile usare i metodi anonimi (detti anche *lambda*) per esprimere le funzionalità in modo più conciso e più efficiente. È possibile evitare il sovraccarico di lavoro legato alla necessità di scrivere oggetti monouso ed è possibile passare lo stato locale a un metodo senza dover aggiungere parametri.

-   [Gestione degli eventi](#events): C# fornisce supporto a livello di linguaggio per la *programmazione guidata dagli eventi*, in cui un oggetto può registrarsi per ricevere una notifica quando si verifica un evento di interesse. La parola chiave `event` definisce un meccanismo di trasmissione multicast che una classe publisher può usare per inviare notifiche ai sottoscrittori di eventi.

-   [Programmazione asincrona](#async): le funzionalità di programmazione asincrona di C# (`async`/`await`) garantiscono la reattività delle app.
    Il supporto a livello di linguaggio di questa funzionalità rende la programmazione asincrona facile da implementare e meno soggetta a errori.


Infine, Xamarin consente di [sfruttare le risorse Java esistenti](#interop) tramite una tecnologia nota come *binding*. È possibile chiamare codice, framework e librerie Java esistenti da C# usando i generatori di binding automatici di Xamarin. A tale scopo, è sufficiente creare una libreria statica in Java ed esporla in C# tramite un binding.

<a name="fundamentals" />

## <a name="going-from-java-to-c-development"></a>Dallo sviluppo Java a C#

Le sezioni seguenti descrivono le differenze di base tra C# e Java. Una sezione successiva descrive le differenze orientate a oggetti tra questi linguaggi.



### <a name="libraries-vs-assemblies"></a>Confronto tra librerie e Assembly

In Java in genere vengono creati pacchetti di classi correlate in file **JAR**. In C# e .NET, tuttavia, i bit riutilizzabili di codice precompilato vengono raccolti in *assembly*, che sono in genere file *DLL*. Un assembly è un'unità di distribuzione per il codice C#/.NET e ogni assembly è in genere associato a un progetto C#. Gli assembly contengono codice intermedio (IL) che è codice JIT compilato nel runtime.

Per altre informazioni sugli assembly, vedere l'argomento [Assembly e Global Assembly Cache](https://msdn.microsoft.com/en-us/library/ms173099.aspx) in MSDN.


### <a name="packages-vs-namespaces"></a>Confronto tra pacchetti e Spazi dei nomi

C# usa la parola chiave `namespace` per raggruppare i tipi correlati, in modo analogo alla parola chiave `package` Java. In genere, un'app Xamarin.Android si trova in uno spazio dei nomi creato per l'app. Il codice C# seguente, ad esempio, dichiara il wrapper dello spazio dei nomi `WeatherApp` per un'app meteo:

```csharp
namespace WeatherApp
{
    ...
```


### <a name="importing-types"></a>Importazione di tipi

Quando si usano tipi definiti negli spazi dei nomi esterni, si importano questi tipi con un'istruzione `using` (molto simile all'istruzione `import` Java). In Java è possibile importare un singolo tipo con un'istruzione simile alla seguente:

```java
import javax.swing.JButton
```

È possibile importare un intero pacchetto Java con un'istruzione simile alla seguente:

```java
import javax.swing.*
```

L'istruzione `using` C# funziona in modo molto simile, ma consente di importare un intero pacchetto senza specificare un carattere jolly. Spesso, ad esempio, è presente una serie di istruzioni `using` all'inizio dei file di origine Xamarin.Android, come illustrato in questo esempio:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using System.Net;
using System.IO;
using System.Json;
using System.Threading.Tasks;
```

Queste istruzioni importano funzionalità dagli spazi dei nomi `System`, `Android.App`, `Android.Content` e così via.



### <a name="generics"></a>Generics

Sia Java che C# supportano i *generics*, che sono segnaposto che consentono di inserire diversi tipi in fase di compilazione. I generics, tuttavia, funzionano in modo leggermente diverso in C#. In Java la [cancellazione dei tipi](https://docs.oracle.com/javase/tutorial/java/generics/erasure.html) rende disponibili le informazioni sui tipi solo in fase di compilazione e non in fase di esecuzione. .NET Common Language Runtime (CLR) fornisce invece supporto esplicito per i tipi generici, il che significa che C# può accedere alle informazioni sui tipi in fase di esecuzione. Nelle attività quotidiane di sviluppo in Xamarin.Android l'importanza di questa distinzione spesso non è evidente, ma se si usa la [reflection](https://msdn.microsoft.com/en-us/library/ms173183.aspx) si dipende da questa funzionalità per accedere alle informazioni sui tipi in fase di esecuzione.

In Xamarin.Android viene spesso usato il metodo generico `FindViewById` per ottenere un riferimento a un controllo di layout. Questo metodo accetta un parametro di tipo generico che specifica il tipo di controllo da cercare. Ad esempio:

```csharp
TextView label = FindViewById<TextView> (Resource.Id.Label);
```

In questo esempio di codice `FindViewById` ottiene un riferimento al controllo `TextView` definito nel layout come **Label**, quindi lo restituisce come tipo `TextView`.

Per altre informazioni sui generics, vedere l'argomento [Generics](https://msdn.microsoft.com/en-us/library/512aeb7t.aspx) in MSDN.
Si noti che ci sono alcune limitazioni al supporto di Xamarin.Android per le classi C# generiche. Per altre informazioni, vedere [Limitations](~/android/internals/limitations.md) (Limitazioni).


<a name="oopfeatures" />

## <a name="object-oriented-programming-features"></a>Funzionalità di programmazione orientate a oggetti

Java e C# usano linguaggi di programmazione orientata a oggetti molto simili:

-   Tutte le classi in definitiva sono derivate da un singolo oggetto radice: tutti gli oggetti Java derivano da `java.lang.Object`, mentre tutti gli oggetti C# derivano da `System.Object`.

-   Le istanze delle classi sono tipi riferimento.

-   Quando si accede a proprietà e metodi di un'istanza, si usa l'operatore "<code>.</code>".

-   Tutte le istanze delle classi vengono create nell'heap tramite l'operatore `new`.

-   Poiché entrambi i linguaggi usano il processo di Garbage Collection, è possibile rilasciare in modo esplicito gli oggetti inutilizzati, ovvero non c'è una parola chiave `delete` come in C++.

-   È possibile estendere le classi attraverso l'ereditarietà ed entrambi i linguaggi consentono solo una singola classe di base per ogni tipo.

-   È possibile definire le interfacce e una classe può ereditare da (ovvero implementa) più definizioni di interfaccia.

Ci sono tuttavia alcune importanti differenze:

-   Java ha due funzionalità potenti non supportate da C#: le classi anonime e le classi interne. C#, tuttavia, consente l'annidamento delle definizioni di classe. Le classi annidate C# sono come le classi annidate statiche Java.

-   C# supporta strutture di tipo C (`struct`) mentre Java no.

-   In C# è possibile implementare una definizione di classe in file di origine distinti usando la parola chiave `partial`.

-   Le interfacce C# non possono dichiarare i campi.

-   C# usa la sintassi dei distruttori di tipo C++ per esprimere i finalizzatori. La sintassi è diversa dal metodo Java `finalize`, ma la semantica è quasi la stessa. Si noti che in C# i distruttori chiamano automaticamente il distruttore della classe di base, mentre in Java viene usata una chiamata esplicita a `super.finalize`.



### <a name="class-inheritance"></a>Ereditarietà di classe

Per estendere una classe in Java, si usa la parola chiave `extends`. Per estendere una classe in C#, si usano i due punti (`:`) per indicare la derivazione. Nelle app Xamarin.Android, ad esempio, sono spesso presenti derivazioni di classi simili al frammento di codice seguente:

```csharp
public class MainActivity : Activity
{
    ...
```

In questo esempio `MainActivity` eredita dalla classe `Activity`.

Per dichiarare il supporto per un'interfaccia in Java, si usa la parola chiave `implements`. In C# invece è sufficiente aggiungere i nomi di interfaccia all'elenco delle classi da cui ereditare, come illustrato in questo frammento di codice:

```csharp
public class SensorsActivity : Activity, ISensorEventListener
{
    ...
```

In questo esempio `SensorsActivity` eredita da `Activity` e implementa la funzionalità dichiarata nell'interfaccia `ISensorEventListener`. Si noti che l'elenco di interfacce deve essere specificato dopo la classe di base. In caso contrario, si verificherà un errore in fase di compilazione. Per convenzione, i nomi di interfaccia C# sono preceduti da una "I" maiuscola. Ciò consente di determinare quali classi sono interfacce senza che sia necessaria una parola chiave `implements`.

Quando si vuole impedire che in una classe vengano inserite ulteriori sottoclassi in C#, è necessario anteporre `sealed` al nome della classe, mentre in Java è necessario anteporre `final`.

Per altre informazioni sulle definizioni di classe C#, vedere gli argomenti [Classi e struct](https://msdn.microsoft.com/en-us/library/x9afc042.aspx) ed [Ereditarietà](https://msdn.microsoft.com/en-us/library/x9afc042.aspx) in MSDN.


<a name="properties" />

### <a name="properties"></a>Proprietà

In Java i metodi mutatori (setter) e i metodi di controllo (getter) vengono spesso usati per controllare come vengono apportate modifiche ai membri delle classi, nascondendo e proteggendo tali membri dal codice esterno. La classe `TextView` Android, ad esempio, fornisce i metodi `getText` e `setText`. C# fornisce un meccanismo simile ma più diretto, noto come *proprietà*.
Gli utenti di una classe C# possono accedere a una proprietà nello stesso modo in cui accedono a un campo, ma ogni accesso comporta una chiamata a un metodo trasparente al chiamante. Questo metodo "invisibile" può provocare effetti collaterali, ad esempio l'impostazione di altri valori, l'esecuzione di conversioni o la modifica dello stato degli oggetti.

Le proprietà vengono spesso usate per accedere ai membri degli oggetti dell'interfaccia utente e modificarli. Ad esempio:

```csharp
int width = rulerView.MeasuredWidth;
int height = rulerView.MeasuredHeight;
...
rulerView.DrawingCacheEnabled = true;
```

In questo esempio i valori di larghezza e altezza vengono letti dall'oggetto `rulerView` accedendo alle relative proprietà `MeasuredWidth` e `MeasuredHeight`. Quando queste proprietà vengono lette, i valori dei campi associati, ma nascosti, vengono recuperati in background e restituiti al chiamante. L'oggetto `rulerView` può archiviare i valori di larghezza e altezza in un'unità di misura (ad esempio, pixel) e convertire questi valori immediatamente in una diversa unità di misura (ad esempio, millimetri) quando viene eseguito l'accesso alle proprietà `MeasuredWidth` e `MeasuredHeight`.

L'oggetto `rulerView` ha anche una proprietà denominata `DrawingCacheEnabled`. Il codice di esempio imposta questa proprietà su `true` per abilitare la cache di disegno in `rulerView`. Un file nascosto associato viene aggiornato in background con il nuovo valore e possono venire modificati altri aspetti dello stato di `rulerView`. Quando, ad esempio, la proprietà `DrawingCacheEnabled` è impostata su `false`, `rulerView` può anche cancellare eventuali informazioni della cache di disegno già accumulate nell'oggetto.

L'accesso alle proprietà può essere in lettura/scrittura, di sola lettura o di sola scrittura. È inoltre possibile usare modificatori di accesso diversi per la lettura e la scrittura. È ad esempio possibile definire una proprietà con accesso in lettura pubblico ma accesso in scrittura privato.

Per altre informazioni sulle proprietà C#, vedere l'argomento [Proprietà](https://msdn.microsoft.com/en-us/library/x9fsa0sw.aspx) in MSDN.



### <a name="calling-base-class-methods"></a>Chiamata dei metodi di una classe di base

Per chiamare un costruttore di una classe di base in C# si usano i due punti (`:`) seguiti dalla parola chiave `base` e un elenco di inizializzatori. Questa chiamata al costruttore `base` viene inserita immediatamente dopo l'elenco di parametri del costruttore derivato. Il costruttore della classe di base viene chiamato all'ingresso nel costruttore derivato. Il compilatore inserisce la chiamata al costruttore di base all'inizio del corpo del metodo. Il frammento di codice seguente illustra un costruttore di base chiamato da un costruttore derivato in un'app Xamarin.Android:

```csharp
public class PictureLayout : ViewGroup
{
    ...
    public class PictureLayout (Context context)
           : base (context)
    {
        ...
    }
    ...
}

```

In questo esempio la classe `PictureLayout` è derivata dalla classe `ViewGroup`. Il costruttore `PictureLayout` illustrato in questo esempio accetta un argomento `context` e lo passa al costruttore `ViewGroup` tramite la chiamata a `base(context)`.

Per chiamare un metodo di una classe di base in C# si usa la parola chiave `base`. Le app Xamarin.Android, ad esempio, eseguono spesso chiamate ai metodi di base come illustrato qui:

```csharp
public class MainActivity : Activity
{
    ...
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
```

In questo caso, il metodo `OnCreate` definito dalla classe derivata (`MainActivity`) chiama il metodo `OnCreate` della classe di base (`Activity`).



### <a name="access-modifiers"></a>Modificatori di accesso

Java e C# supportano entrambi i modificatori di accesso `public`, `private` e `protected`. C#, tuttavia, supporta due modificatori di accesso aggiuntivi:

-   **`internal`**: il membro della classe è accessibile solo all'interno dell'assembly corrente.

-   **`protected internal`**: il membro della classe è accessibile all'interno dell'assembly di definizione, della classe di definizione e delle classi derivate (l'accesso è consentito alle classi derivate sia all'interno che all'esterno dell'assembly).

Per altre informazioni sui modificatori di accesso C#, vedere l'argomento [Modificatori di accesso](https://msdn.microsoft.com/en-us/library/ms173121.aspx) in MSDN.



### <a name="virtual-and-override-methods"></a>Metodi virtuali e di override

Sia Java che C# supportano il *polimorfismo*, ovvero la possibilità di gestire gli oggetti correlati nello stesso modo. In entrambi i linguaggi è possibile usare un riferimento a una classe di base per fare riferimento a un oggetto classe derivata e i metodi di una classe derivata possono eseguire l'override delle classi di base. Entrambi i linguaggi prevedono il concetto di metodo *virtuale*, un metodo in una classe di base progettato per essere sostituito da un metodo in una classe derivata.
Come Java, C# supporta classi e metodi `abstract`.

Ci sono tuttavia alcune differenze tra Java e C# relative al modo in cui i metodi virtuali vengono dichiarati e ne viene eseguito l'override:

-   In C# i metodi sono non virtuali per impostazione predefinita. Le classi padre devono indicare esplicitamente i metodi che devono essere sottoposti a override usando la parola chiave `virtual`. Al contrario, tutti i metodi Java sono metodi virtuali per impostazione predefinita.

-   Per impedire l'override di un metodo in C#, è sufficiente omettere la parola chiave `virtual`. Java usa invece la parola chiave `final` per indicare che l'override di in metodo non è consentito.

-   Le classi derivate C# devono usare la parola chiave `override` per indicare in modo esplicito che viene eseguito l'override di un metodo di una classe di base virtuale.

Per altre informazioni sul supporto di C# per il polimorfismo, vedere l'articolo [Polimorfismo](https://msdn.microsoft.com/en-us/library/ms173152.aspx) in MSDN.


<a name="lambdas" />

## <a name="lambda-expressions"></a>Espressioni lambda

C# consente di creare *closure*: metodi inline anonimi che possono accedere allo stato del metodo in cui sono racchiusi.
Usando le espressioni lambda, è possibile scrivere un numero inferiore di righe di codice per implementare la stessa funzionalità che in Java avrebbe richiesto molte più righe di codice.

Le espressioni lambda permettono di evitare il lavoro aggiuntivo necessario per creare una classe monouso o una classe anonima come si farebbe in Java. È invece sufficiente scrivere la logica di business del codice del metodo inline. Poiché inoltre le espressioni lambda hanno accesso alle variabili nel metodo circostante, non è necessario creare un lungo elenco di parametri per passare lo stato al codice del metodo.

In C# le espressioni lambda vengono create con l'operatore `=>`, come illustrato di seguito:

```csharp
(arg1, arg2, ...) => {
    // implementation code
};
```

In Xamarin.Android le espressioni lambda vengono spesso usate per definire gestori eventi. Ad esempio:

```csharp
button.Click += (sender, args) => {
    clickCount += 1;    // access variable in surrounding code
    button.Text = string.Format ("Clicked {0} times.", clickCount);
};
```

In questo esempio il codice dell'espressione lambda (il codice tra parentesi graffe) incrementa il conteggio dei clic e aggiorna il testo di `button` per visualizzare il numero di clic. Questa espressione lambda è registrata con l'oggetto `button` come gestore di eventi clic da chiamare a ogni tocco del pulsante. I gestori eventi sono illustrati in modo più dettagliato più avanti. In questo semplice esempio i parametri `sender` e `args` non vengono usati dal codice dell'espressione lambda, ma sono necessari nell'espressione lambda per soddisfare i requisiti di firma del metodo per la registrazione degli eventi. In background il compilatore C# converte l'espressione lambda in un metodo anonimo che viene chiamato per ogni evento clic sul pulsante.

Per altre informazioni su C# e sulle espressioni lambda, vedere l'argomento [Espressioni lambda](https://msdn.microsoft.com/en-us/library/bb397687.aspx) in MSDN.


<a name="events" />

## <a name="event-handling"></a>Gestione di eventi

Un *evento* è un modo in cui un oggetto notifica ai sottoscrittori registrati il verificarsi di un'attività di interesse sull'oggetto stesso. A differenza di Java, in cui un sottoscrittore in genere implementa un'interfaccia `Listener` che contiene un metodo di callback, C# fornisce il supporto a livello di linguaggio per la gestione degli eventi tramite *delegati*. Un *delegato* è simile a un puntatore a funzione indipendente dai tipi e orientato a oggetti e incapsula un riferimento all'oggetto e un token di metodo. Se un oggetto client vuole sottoscrivere un evento, crea un delegato e lo passa all'oggetto che invia la notifica.
Quando si verifica l'evento, l'oggetto che invia la notifica richiama il metodo rappresentato dall'oggetto delegato, notificando l'evento all'oggetto client che ha eseguito la sottoscrizione. In C# i gestori eventi essenzialmente non sono altro che metodi richiamati tramite delegati.

Per altre informazioni sui delegati, vedere l'argomento [Delegati](https://msdn.microsoft.com/en-us/library/ms173171.aspx) in MSDN.

In C# gli eventi sono di tipo *multicast*, ovvero più di un listener può ricevere una notifica quando si verifica un evento. Questa differenza si nota quando si considerano le differenze sintattiche tra la registrazione di eventi Java e C#. In Java si chiama `SetXXXListener` per registrare le notifiche degli eventi, mentre in C# si usa l'operatore `+=` per registrare le notifiche degli eventi "aggiungendo" il delegato all'elenco di listener di eventi.
In Java si chiama `SetXXXListener` per annullare la registrazione, mentre in C# si usa `-=` per "sottrarre" il delegato dall'elenco di listener.

In Xamarin.Android gli eventi vengono spesso usati per inviare notifiche agli oggetti quando un utente esegue un'operazione su un controllo dell'interfaccia utente. In genere, un controllo dell'interfaccia utente ha membri definiti usando la parola chiave `event` e si collegano delegati a questi membri per sottoscrivere gli eventi di tale controllo dell'interfaccia utente.

Per sottoscrivere un evento:

1.  Creare un oggetto delegato che fa riferimento al metodo che si vuole richiamare quando si verifica l'evento.

2.  Usare l'operatore `+=` per collegare il delegato all'evento sottoscritto.

L'esempio seguente definisce un delegato (con l'uso esplicito della parola chiave `delegate`) per sottoscrivere i clic sul pulsante.
Questo gestore dei clic sul pulsante avvia una nuova attività:

```csharp
startActivityButton.Click += delegate {
    Intent intent = new Intent (this, typeof (MyActivity));
    StartActivity (intent);
};

```

Tuttavia, è anche possibile usare un'espressione lambda per registrare gli eventi, ignorando completamente la parola chiave `delegate`. Ad esempio:

```csharp
startActivityButton.Click += (sender, e) => {
    Intent intent = new Intent (this, typeof (MyActivity));
    StartActivity (intent);
};

```

In questo esempio l'oggetto `startActivityButton` ha un evento che prevede un delegato con una determinata firma di metodo, che accetta argomenti di mittente ed evento e restituisce un valore nullo. Per evitare, tuttavia, le operazioni necessarie per definire in modo esplicito un delegato di questo tipo o il relativo metodo, si dichiara la firma del metodo con `(sender, e)` e si usa un'espressione lambda per implementare il corpo del gestore eventi.
Si noti che è necessario dichiarare l'elenco di parametri anche se non si usano i parametri `sender` e `e`.

È importante ricordare che è possibile annullare la sottoscrizione di un delegato (tramite l'operatore `-=`), ma non è possibile annullare la sottoscrizione di un'espressione lambda. Il tentativo di eseguire questa operazione può causare perdite di memoria. Usare il formato lambda di registrazione dell'evento solo quando il gestore non annullerà la sottoscrizione dell'evento.

In genere, le espressioni lambda vengono usate per dichiarare gestori eventi nel codice Xamarin.Android. Questo metodo rapido di dichiarare i gestori eventi può sembrare complesso, inizialmente, ma consente di risparmiare un'enorme quantità di tempo quando si scrive e si legge il codice. Acquisendo familiarità e imparando a riconoscere questo modello, che si trova spesso nel codice Xamarin.Android, sarà possibile dedicare più tempo alla logica di business dell'applicazione e meno alla creazione della sintassi.


<a name="async" />

## <a name="asynchronous-programming"></a>Programmazione asincrona

La *programmazione asincrona* consente di migliorare a livello generale la velocità di risposta dell'applicazione. Le funzionalità di programmazione asincrona permettono al resto del codice dell'app di continuare l'esecuzione mentre alcune parti dell'app sono bloccate da un'operazione che richiede molto tempo.
L'accesso al Web, l'elaborazione di immagini e la lettura e la scrittura di file sono esempi di operazioni che possono far risultare bloccata un'intera app se la scrittura non avviene in modo asincrono.

C# include il supporto a livello di linguaggio per la programmazione asincrona tramite le parole chiave `async` e `await`. Queste funzionalità del linguaggio semplificano notevolmente la scrittura di codice che esegue attività di lunga durata senza bloccare il thread principale dell'applicazione. In breve, si usa la parola chiave `async` in un metodo per indicare che il codice nel metodo deve essere eseguito in modo asincrono e non deve bloccare il thread del chiamante. La parola chiave `await` viene usata quando si chiamano metodi contrassegnati con `async`. Il compilatore interpreta `await` come il punto in cui l'esecuzione del metodo deve essere spostata in un thread in background (viene restituita un'attività al chiamante). Quando questa attività viene completata, l'esecuzione del codice riprende nel thread del chiamante in corrispondenza del punto `await` nel codice, restituendo i risultati della chiamata `async`. Per convenzione, ai nomi dei metodi eseguiti in modo asincrono viene aggiunto il suffisso `Async`.

Nelle applicazioni Xamarin.Android le parole chiave `async` e `await` vengono usate in genere per liberare il thread dell'interfaccia utente, in modo che possa rispondere all'input utente, ad esempio il tocco sul pulsante **Annulla**, mentre è in corso un'operazione di lunga durata in un'attività in background.

Nell'esempio seguente un gestore dell'evento clic su un pulsante fa in modo che un'operazione asincrona scarichi un'immagine dal Web:


```csharp
downloadButton.Click += downloadAsync;
...
async void downloadAsync(object sender, System.EventArgs e)
{
    webClient = new WebClient ();
    var url = new Uri ("http://photojournal.jpl.nasa.gov/jpeg/PIA15416.jpg");
    byte[] bytes = null;

    bytes = await webClient.DownloadDataTaskAsync(url);

    // display the downloaded image ...
```

In questo esempio quando l'utente fa clic sul controllo `downloadButton`, il gestore dell'evento `downloadAsync` crea un oggetto `WebClient` e un oggetto `Uri` per recuperare un'immagine dall'URL specificato. Viene quindi chiamato il metodo `DownloadDataTaskAsync` dell'oggetto `WebClient` con questo URL per recuperare l'immagine.

Si noti che la dichiarazione del metodo `downloadAsync` è preceduta dalla parola chiave `async` per indicare che l'esecuzione avverrà in modo asincrono e verrà restituita un'attività. Si noti anche che la chiamata a `DownloadDataTaskAsync` è preceduta dalla parola chiave `await`. L'app sposta l'esecuzione del gestore dell'evento (a partire dal punto in cui è presente `await`) in un thread in background fino al completamento di `DownloadDataTaskAsync` e alla restituzione di un risultato.
Nel frattempo, il thread dell'interfaccia utente dell'app può continuare a rispondere all'input utente e attivare i gestori eventi per gli altri controlli. Quando il metodo `DownloadDataTaskAsync` viene completato (l'operazione potrebbe richiedere alcuni secondi), l'esecuzione riprende dal punto in cui è impostata la variabile `bytes` fino al risultato della chiamata a `DownloadDataTaskAsync` e la parte rimanente del codice del gestore dell'evento visualizza l'immagine scaricata nel thread (interfaccia utente) del chiamante.

Per un'introduzione a `async`/`await` in C#, vedere l'argomento [Programmazione asincrona con Async e Await](https://msdn.microsoft.com/en-us/library/hh191443.aspx) in MSDN.
Per altre informazioni sul supporto delle funzionalità di programmazione asincrona in Xamarin, vedere [Async Support Overview](~/cross-platform/platform/async.md) (Panoramica del supporto di async).


<a name="keywords" />

## <a name="keyword-differences"></a>Differenze tra parole chiave

Numerose parole chiave del linguaggio usate in Java sono usate anche in C#. Ci sono anche molte parole chiave Java che hanno una controparte in C# equivalente ma con un nome diverso, come elencato in questa tabella:

<table align="center" border="1" cellpadding="1" cellspacing="1">
  <thead>
    <th>
        <strong>Java</strong>
    </th>
    <th>
        <strong>C#</strong>
    </th>
    <th>
        <strong>Descrizione</strong>
    </th>
  </thead>
  <tbody>
    <tr>
      <td valign="top">
        <code>boolean</code>
      </td>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/c8f5xwh7.aspx">bool</a>
      </td>
      <td valign="top">
Usata per dichiarare i valori booleani <code>true</code> e <code>false</code>.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <code>extends</code>
      </td>
      <td valign="top">
        <code>:</code>
      </td>
      <td valign="top">
Precede la classe e le interfacce da cui ereditare.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <code>implements</code>
      </td>
      <td valign="top">
        <code>:</code>
      </td>
      <td valign="top">
Precede la classe e le interfacce da cui ereditare.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <code>import</code>
      </td>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/zhdeatwt.aspx">uso</a>
      </td>
      <td valign="top">
Importa i tipi di uno spazio dei nomi ed è usata anche per creare un alias dello spazio dei nomi.
      </td>
    </tr>
    <tr>
      <td valign="final">
        <code>final</code>
      </td>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/88c54tsw.aspx">sealed</a>
      </td>
      <td valign="top">
Impedisce la derivazione di classi. Impedisce l'override di metodi e proprietà nelle classi derivate.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <code>instanceof</code>
      </td>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/scekt9xw.aspx">is</a>
      </td>
      <td valign="top">
Valuta se un oggetto è compatibile con un tipo specificato.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <code>native</code>
      </td>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/e59b22c5.aspx">extern</a>
      </td>
      <td valign="top">
Dichiara un metodo implementato esternamente.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <code>package</code>
      </td>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/z2kcy19k.aspx">namespace</a>
      </td>
      <td valign="top">
Dichiara un ambito per un set di oggetti correlato.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <code>T...</code>
      </td>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/w5zay9db.aspx">params T[]</a>
      </td>
      <td valign="top">
Specificare un parametro di metodo che accetta un numero variabile di argomenti.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <code>super</code>
      </td>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/hfw7t1ce.aspx">base</a>
      </td>
      <td valign="top">
Usata per accedere ai membri della classe padre da una classe derivata.
      </td>
    </tr>
    <tr>
      <td valign="synchronized">
        <code>synchronized</code>
      </td>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/c5kehkcz.aspx">lock</a>
      </td>
      <td valign="top">
Esegue il wrapping di una sezione critica del codice con acquisizione e rilascio del blocco.
      </td>
    </tr>
  </tbody>
</table>


Ci sono inoltre numerose parole chiave specifiche di C# che non hanno una controparte in Java. Il codice Xamarin.Android usa spesso le parole chiave C# seguenti (questa tabella è utile come riferimento quando si legge il [codice di esempio](https://developer.xamarin.com/samples/android/all/) Xamarin.Android):


<table align="center" border="1" cellpadding="1" cellspacing="1">
  <thead>
    <th>
        <strong>C#</strong>
    </th>
    <th>
        <strong>Descrizione</strong>
    </th>
  </thead>
  <tbody>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/cscsdfbt.aspx">as</a>
      </td>
      <td valign="top">
Esegue conversioni tra tipi riferimento compatibili o tipi nullable.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/hh156513.aspx">async</a>
      </td>
      <td valign="top">
Specifica che un'espressione lambda o un metodo è asincrono.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/hh156528.aspx">await</a>
      </td>
      <td valign="top">
Sospende l'esecuzione di un metodo fino al completamento di un'attività.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/5bdb6693.aspx">byte</a>
      </td>
      <td valign="top">
Tipo Unsigned Integer a 8 bit.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/900fyy8e.aspx">delegate</a>
      </td>
      <td valign="top">
Usata per incapsulare un metodo o un metodo anonimo.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/sbbt4032.aspx">enum</a>
      </td>
      <td valign="top">
Dichiara un'enumerazione, un set di costanti denominate.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/8627sbea.aspx">event</a>
      </td>
      <td valign="top">
Dichiara un evento in una classe publisher.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/f58wzh21.aspx">fixed</a>
      </td>
      <td valign="top">
Impedisce di rilocare variabile.
      </td>
    </tr>
    <tr>
      <td valign="top">
get </td>
      <td valign="top">
Definisce un metodo della funzione di accesso che recupera il valore di una proprietà.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/dd469484.aspx">in</a>
      </td>
      <td valign="top">
Consente a un parametro di accettare un tipo meno derivato in un'interfaccia generica.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/9kkx3h3c.aspx">object</a>
      </td>
      <td valign="top">
Alias per il tipo <code>Object</code> in .NET Framework.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/t3c3bfhx.aspx">out</a>
      </td>
      <td valign="top">
Modificatore di parametro o dichiarazione di parametro di tipo generico.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/ebca9ah3.aspx">override</a>
      </td>
      <td valign="top">
Estende o modifica l'implementazione di un membro ereditato.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/6b0scde8.aspx">partial</a>
      </td>
      <td valign="top">
Dichiara una definizione da dividere in più file oppure divide una definizione di metodo dalla relativa implementazione.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/acdd6hb7.aspx">readonly</a>
      </td>
      <td valign="top">
Dichiara che un membro di classe può essere assegnato solo in fase di dichiarazione o dal costruttore della classe.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/14akc2c7.aspx">ref</a>
      </td>
      <td valign="top">
Fa in modo che un argomento venga passato tramite riferimento invece che tramite valore.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/ms228368.aspx">set</a>
      </td>
      <td valign="top">
Definisce un metodo della funzione di accesso che imposta il valore di una proprietà.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/362314fe.aspx">string</a>
      </td>
      <td valign="top">
Alias per il tipo <code>String</code> in .NET Framework.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/ah19swz4.aspx">struct</a>
      </td>
      <td valign="top">
Tipo di valore che incapsula un gruppo di variabili correlate.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/58918ffs.aspx">typeof</a>
      </td>
      <td valign="top">
Ottiene il tipo di un oggetto.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/bb383973.aspx">var</a>
      </td>
      <td valign="top">
Dichiara una variabile locale tipizzata in modo implicito.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/a1khb4f8.aspx">value</a>
      </td>
      <td valign="top">
Fa riferimento al valore che il codice client vuole assegnare a una proprietà.
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="https://msdn.microsoft.com/en-us/library/9fkccyh4.aspx">virtual</a>
      </td>
      <td valign="top">
Consente l'override di un metodo in una classe derivata.
      </td>
    </tr>
  </tbody>
</table>


<a name="interop" />

## <a name="interoperating-with-existing-java-code"></a>Interoperabilità con il codice Java esistente

Se si hanno funzionalità Java esistenti che non si vuole convertire in C#, è possibile riutilizzare le librerie Java esistenti nelle applicazioni Xamarin.Android tramite due tecniche:

-  **Creazione di una libreria di binding Java**: usando questo approccio, si usano gli strumenti Xamarin per generare wrapper C# per tipi Java. Questi wrapper vengono detti *binding*. L'applicazione Xamarin.Android può così usare il file *JAR* chiamando questi wrapper.

-  **Interfaccia nativa di Java**: l'*interfaccia nativa di Java* (JNI, Java Native Interface) è un framework che consente alle app C# di chiamare codice Java o essere chiamate da tale codice.

Per altre informazioni su queste tecniche, vedere [Java Integration Overview](~/android/platform/java-integration/index.md) (Panoramica dell'integrazione di Java).



## <a name="for-further-reading"></a>Ulteriori informazioni

La [Guida per programmatori C#](https://msdn.microsoft.com/en-us/library/67ef8sbd.aspx) in MSDN è un ottimo modo per iniziare ad apprendere il linguaggio di programmazione C#. In [Riferimenti per C#](https://msdn.microsoft.com/en-us/library/618ayhy6.aspx) è possibile cercare funzionalità specifiche del linguaggio C#.

Analogamente alla conoscenza di Java che si basa sulla familiarità con le librerie di classi Java e sulla conoscenza del linguaggio Java, la conoscenza pratica di C# richiede familiarità con .NET Framework. Il pacchetto didattico Microsoft [Moving to C# and the .NET Framework, for Java Developers](https://www.microsoft.com/en-us/download/details.aspx?id=6073) (Passaggio a C# e .NET Framework per sviluppatori Java) consente di apprendere altre informazioni su .NET Framework da una prospettiva Java, approfondendo, nel contempo, la conoscenza di C#.

Quando si è pronti per creare il primo progetto Xamarin.Android in C#, la serie [Hello, Android](~/android/get-started/hello-android/index.md) può aiutare a creare la prima applicazione Xamarin.Android e approfondire maggiormente la conoscenza delle nozioni fondamentali dello sviluppo di applicazioni Android con Xamarin.



## <a name="summary"></a>Riepilogo

In questo articolo è stata fornita un'introduzione all'ambiente di programmazione C# Xamarin.Android dal punto di vista di uno sviluppatore Java. Sono state illustrate le analogie tra C# e Java e le differenze pratiche. Sono state fornite informazioni introduttive su assembly e spazi dei nomi, è stato illustrato come importare i tipi esterni ed è stata fornita una panoramica delle differenze in relazione a modificatori di accesso, generics, derivazione di classi, chiamata dei metodi di una classe di base, override dei metodi e gestione degli eventi. Sono state presentate le funzionalità C# non disponibili in Java, ad esempio proprietà, programmazione asincrona `async`/`await`, lambda, delegati C# e il sistema di gestione degli eventi C#. È inclusa inoltre una tabella delle parole chiave C# importanti, viene illustrata l'interoperabilità con le librerie Java esistenti e vengono forniti i collegamenti alla documentazione correlata per approfondire i concetti.


## <a name="related-links"></a>Collegamenti correlati

- [Java Integration Overview](~/android/platform/java-integration/index.md) (Panoramica dell'integrazione di Java)
- [Guida per programmatori C#](https://msdn.microsoft.com/en-us/library/67ef8sbd.aspx)
- [Riferimenti per C#](https://msdn.microsoft.com/en-us/library/618ayhy6.aspx)
- [Moving to C# and the .NET Framework, for Java Developers](https://www.microsoft.com/en-us/download/details.aspx?id=6073) (Passaggio a C# e .NET Framework per sviluppatori Java)
