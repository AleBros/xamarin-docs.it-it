---
title: Prestazioni multipiattaforma
description: Questo documento descrive le varie tecniche che possono essere usate per migliorare le prestazioni di un'applicazione per dispositivi mobili. Vengono illustrati il profiler, la risorsa IDisposable, i riferimenti deboli, il Garbage Collector SGen, le tecniche di riduzione delle dimensioni e altro ancora.
ms.prod: xamarin
ms.assetid: 9ce61f18-22ac-4b93-91be-5b499677d661
author: davidortinau
ms.author: daortin
ms.date: 03/24/2017
ms.openlocfilehash: d0b195b90bb57b6d0717c0fb06d0202857851fe7
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016520"
---
# <a name="cross-platform-performance"></a>Prestazioni multipiattaforma

Le prestazioni insoddisfacenti di un'applicazione si manifestano in molti modi. Può sembrare che l'applicazione non risponda, lo scorrimento diventa lento e si riduce la durata della batteria. Tuttavia, l'ottimizzazione delle prestazioni implica più della semplice implementazione di codice efficiente. Deve essere considerata anche l'esperienza dell'utente in termini di prestazioni dell'applicazione. Ad esempio, verificando che le operazioni vengano eseguite senza impedire all'utente di eseguire altre attività può contribuire a migliorare l'esperienza dell'utente.

<a name="profiler" />

## <a name="use-the-profiler"></a>Usare il Profiler

Quando si sviluppa un'applicazione, è importante tentare di ottimizzare il codice solo dopo che è stato profilato. La profilatura è una tecnica che consente di determinare i punti del codice in cui l'ottimizzazione può influire maggiormente sulla riduzione dei problemi di prestazioni. Il profiler tiene traccia dell'utilizzo di memoria dell'applicazione e registra il tempo di esecuzione dei metodi nell'applicazione stessa. Questi dati consentono di spostarsi attraverso i percorsi di esecuzione dell'applicazione e di esaminare il costo di esecuzione del codice, rendendo possibile l'individuazione delle migliori opportunità di ottimizzazione.

Xamarin Profiler consente l'individuazione dei problemi relativi alle prestazioni in un'applicazione, quantificando e valutando i problemi individuati. Questo strumento può essere usato per profilare le applicazioni Xamarin.iOS e Xamarin.Android da Visual Studio per Mac o da Visual Studio. Per altre informazioni su Xamarin Profiler, vedere [Introduzione a Xamarin Profiler](~/tools/profiler/index.md).

Per la profilatura di un'app è opportuno seguire le procedure consigliate riportate di seguito:

- Evitare di profilare un'applicazione in un simulatore. Un simulatore, infatti, può falsare le prestazioni dell'applicazione.
- In linea di principio, la profilatura deve essere eseguita in un'ampia gamma di dispositivi. La misurazione delle prestazioni in un dispositivo, infatti, non è necessariamente indicativa delle caratteristiche delle prestazioni di altri dispositivi. La profilatura, comunque, deve essere eseguita almeno in un dispositivo con le specifiche previste più basse.
- Chiudere tutte le altre applicazioni per assicurarsi di misurare completamente l'impatto della sola applicazione profilata, senza l'impatto di altre applicazioni.

<a name="idisposable" />

## <a name="release-idisposable-resources"></a>Rilasciare le risorse IDisposable

L'interfaccia `IDisposable` rappresenta un meccanismo che consente il rilascio delle risorse in modo esplicito tramite l'implementazione del metodo `Dispose`. `IDisposable` non è un distruttore e deve essere implementato solo nelle circostanze seguenti:

- Quando la classe è proprietaria di risorse non gestite. Tra le risorse non gestite che richiedono il rilascio troviamo file, flussi e connessioni di rete.
- Quando la classe è proprietaria di risorse `IDisposable` gestite.

I consumer di tipi possono quindi chiamare l'implementazione di `IDisposable.Dispose` per liberare risorse quando l'istanza non è più necessaria. Per raggiungere questo obiettivo sono possibili due approcci:

- Wrapping dell'oggetto `IDisposable` in un'istruzione `using`.
- Wrapping della chiamata a `IDisposable.Dispose` in un blocco `try` / `finally`.

### <a name="wrapping-the-idisposable-object-in-a-using-statement"></a>Wrapping dell'oggetto IDisposable in un'istruzione using

L'esempio di codice riportato di seguito illustra un oggetto `IDisposable` in un'istruzione `using`:

```csharp
public void ReadText (string filename)
{
  ...
  string text;
  using (StreamReader reader = new StreamReader (filename)) {
    text = reader.ReadToEnd ();
  }
  ...
}
```

La classe `StreamReader` implementa l'oggetto `IDisposable`e l'istruzione `using` rappresenta una sintassi comoda per chiamare il metodo `StreamReader.Dispose` per l'oggetto `StreamReader` prima che quest'ultimo diventi esterno all'ambito. All'interno del blocco `using` l'oggetto `StreamReader` è di sola lettura e non può essere riassegnato. L'istruzione `using` assicura anche che il metodo `Dispose` venga chiamato anche se si verifica un'eccezione, dato che il compilatore implementa il linguaggio intermedio (IL) per un blocco `try`/`finally`.

### <a name="wrapping-the-call-to-idisposabledispose-in-a-tryfinally-block"></a>Wrapping della chiamata a IDisposable.Dispose in un blocco Try/Finally

L'esempio di codice riportato di seguito illustra come eseguire il wrapping della chiamata a `IDisposable.Dispose` in un blocco `try`/`finally`:

```csharp
public void ReadText (string filename)
{
  ...
  string text;
  StreamReader reader = null;
  try {
    reader = new StreamReader (filename);
    text = reader.ReadToEnd ();
  } finally {
    if (reader != null) {
      reader.Dispose ();
    }
  }
  ...
}
```

La classe `StreamReader` implementa `IDisposable` e il blocco `finally` chiama il metodo `StreamReader.Dispose` per rilasciare la risorsa.

Per altre informazioni, vedere [Interfaccia IDisposable](xref:System.IDisposable).

<a name="events" />

## <a name="unsubscribe-from-events"></a>Annullare le sottoscrizioni agli eventi

Per evitare perdite di memoria, è necessario annullare le sottoscrizioni agli eventi prima dell'eliminazione dell'oggetto sottoscrittore corrispondente. Finché non si annulla la sottoscrizione di un evento, il delegato per l'evento nell'oggetto che esegue la pubblicazione contiene un riferimento al delegato che incapsula il gestore eventi del sottoscrittore. Finché l'oggetto che esegue la pubblicazione include tale riferimento, l'operazione di Garbage Collection non può recuperare la memoria occupata dall'oggetto sottoscrittore.

L'esempio di codice seguente illustra come annullare la sottoscrizione a un evento:

```csharp
public class Publisher
{
  public event EventHandler MyEvent;

  public void OnMyEventFires ()
  {
    if (MyEvent != null) {
      MyEvent (this, EventArgs.Empty);
    }
  }
}

public class Subscriber : IDisposable
{
  readonly Publisher publisher;

  public Subscriber (Publisher publish)
  {
    publisher = publish;
    publisher.MyEvent += OnMyEventFires;
  }

  void OnMyEventFires (object sender, EventArgs e)
  {
    Debug.WriteLine ("The publisher notified the subscriber of an event");
  }

  public void Dispose ()
  {
    publisher.MyEvent -= OnMyEventFires;
  }
}
```

La classe `Subscriber` annulla la sottoscrizione all'evento all'interno del metodo `Dispose` della classe stessa.

Quando si usano gestori degli eventi e la sintassi lambda, possono anche verificarsi cicli di riferimento, perché le espressioni lambda possono fare riferimento agli oggetti, mantenendoli attivi. Un riferimento al metodo anonimo, quindi, può essere memorizzato in un campo e usato per annullare la sottoscrizione all'evento, come illustrato nell'esempio di codice seguente:

```csharp
public class Subscriber : IDisposable
{
  readonly Publisher publisher;
  EventHandler handler;

  public Subscriber (Publisher publish)
  {
    publisher = publish;
    handler = (sender, e) => {
      Debug.WriteLine ("The publisher notified the subscriber of an event");
    };
    publisher.MyEvent += handler;
  }

  public void Dispose ()
  {
    publisher.MyEvent -= handler;
  }
}
```

Il campo `handler` mantiene il riferimento al metodo anonimo e viene usato per la sottoscrizione di eventi e per l'annullamento di tali sottoscrizioni.

<a name="weakreferences" />

## <a name="use-weak-references-to-prevent-immortal-objects"></a>Evitare l'immortalità degli oggetti usando riferimenti deboli

> [!NOTE]
> Gli sviluppatori iOS devono esaminare la documentazione su come [evitare riferimenti circolari in iOS](~/ios/deploy-test/performance.md#avoid-strong-circular-references) per garantire un uso efficiente della memoria da parte delle app.

<a name="lazy" />

## <a name="delay-the-cost-of-creating-objects"></a>Ritardare il costo della creazione di oggetti

Per rinviare la creazione di un oggetto fino al momento in cui viene usato per la prima volta, è possibile usare l'inizializzazione differita. Questa tecnica viene usata in particolare per migliorare le prestazioni, evitare calcoli e ridurre i requisiti di memoria.

È consigliabile considerare l'uso dell'inizializzazione differita per gli oggetti la cui creazione comporta un costo elevato nei due scenari seguenti:

- L'oggetto potrebbe non venire usato dall'applicazione.
- Prima della creazione dell'oggetto è necessario completare altre operazioni dal costo elevato.

La classe `Lazy<T>` viene usata per definire un tipo con inizializzazione differita, come illustrato nell'esempio di codice seguente:

```csharp
void ProcessData(bool dataRequired = false)
{
  Lazy<double> data = new Lazy<double>(() =>
  {
    return ParallelEnumerable.Range(0, 1000)
                 .Select(d => Compute(d))
                 .Aggregate((x, y) => x + y);
  });

  if (dataRequired)
  {
    if (data.Value > 90)
    {
      ...
    }
  }
}

double Compute(double x)
{
  ...
}
```

L'inizializzazione differita viene eseguita la prima volta che si accede alla proprietà `Lazy<T>.Value`. Il tipo con wrapping viene creato e restituito in occasione del primo accesso e viene quindi archiviato per l'eventuale accesso futuro.

Per altre informazioni sull'inizializzazione differita, vedere [Inizializzazione differita](https://msdn.microsoft.com/library/dd997286(v=vs.110).aspx).

<a name="async" />

## <a name="implement-asynchronous-operations"></a>Implementare operazioni asincrone

.NET rende disponibile la versione asincrona di molte delle proprie API. A differenza delle API sincrone, le API asincrone assicurano che il thread di esecuzione attivo non blocchi mai il thread chiamante per un periodo di tempo significativo. Quando si chiama un'API dal thread dell'interfaccia utente, quindi, usare l'API asincrona, se disponibile. Questo consente di mantenere sbloccato il thread dell'interfaccia utente, contribuendo a migliorare l'esperienza utente dell'applicazione.

È anche consigliabile eseguire le operazioni a esecuzione prolungata in un thread in background, per evitare di bloccare il thread dell'interfaccia utente. .NET fornisce le parole chiave `async` e `await`, che consentono la scrittura di codice asincrono per l'esecuzione di operazioni a esecuzione prolungata in un thread in background, e dopo il completamento accede ai risultati. Anche se le operazioni a esecuzione prolungata possono essere eseguite in modo asincrono con la parola chiave `await`, questo non garantisce che l'esecuzione dell'operazione avvenga in un thread in background. Per eseguire un'operazione a esecuzione prolungata in background, passarla a `Task.Run`, come illustrato nell'esempio di codice seguente:

```csharp
public class FaceDetection
{
  ...
  async void RecognizeFaceButtonClick(object sender, EventArgs e)
  {
    await Task.Run(() => RecognizeFace ());
    ...
  }

  async Task RecognizeFace()
  {
    ...
  }
}
```

Il metodo `RecognizeFace` viene eseguito in un thread in background. Il metodo `RecognizeFaceButtonClick` attende il completamento del metodo `RecognizeFace` prima di continuare.

Le operazioni a esecuzione prolungata devono anche supportare l'annullamento. La continuazione di un'operazione a esecuzione prolungata, ad esempio, può diventare inutile se l'utente si sposta all'interno dell'applicazione. Il modello per l'implementazione dell'annullamento è il seguente:

- Creare un'istanza di `CancellationTokenSource`. Questa istanza gestisce e invia le notifiche di annullamento.
- Passare il valore della proprietà `CancellationTokenSource.Token` a ogni attività che deve essere annullabile.
- Fornire a ogni attività un meccanismo per rispondere all'annullamento.
- Chiamare il metodo `CancellationTokenSource.Cancel` per fornire la notifica dell'annullamento.

> [!IMPORTANT]
> Poiché la classe `CancellationTokenSource` implementa l'interfaccia `IDisposable`, il metodo `CancellationTokenSource.Dispose` deve essere richiamato al termine dell'istanza `CancellationTokenSource`.

Per altre informazioni, vedere [Panoramica del supporto asincrono](~/cross-platform/platform/async.md).

<a name="sgen" />

## <a name="use-the-sgen-garbage-collector"></a>Usare il Garbage Collector SGen

I linguaggi gestiti, come C#, usano la Garbage Collection per recuperare la memoria allocata oggetti non più in uso. I due Garbage Collector usati dalla piattaforma Xamarin sono:

- [**SGen** ](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/): Garbage Collector generazionale che rappresenta il Garbage Collector predefinito nella piattaforma Xamarin.
- [**Boehm** ](http://www.hboehm.info/gc/): Garbage Collector non generazionale conservativo. È il Garbage Collector predefinito per le applicazioni Xamarin.iOS che usano l'API classica.

Per allocare spazio per gli oggetti, SGen utilizza uno dei tre heap seguenti:

- **Nursery**: qui vengono allocati i nuovi oggetti di piccole dimensioni. Quando lo spazio di questo heap si esaurisce, viene eseguita una Garbage Collection secondaria e tutti gli oggetti attivi vengono spostati nell'heap principale.
- **Heap principale**: contiene gli oggetti a esecuzione prolungata. Se la memoria disponibile nell'heap principale non è sufficiente, viene eseguita un'operazione di Garbage Collection principale. Se la Garbage Collection principale non riesce a liberare una quantità di memoria sufficiente, SGen chiederà una maggiore quantità di memoria al sistema.
- **Spazio dei Large Object**: contiene gli oggetti che richiedono più di 8000 byte. I Large Object non vengono creati nella Nursery, ma vengono allocati in questo heap.

Uno dei vantaggi offerti da SGen è che il tempo necessario per eseguire una Garbage Collection secondaria è proporzionale al numero di nuovi oggetti attivi creati dopo l'ultima Garbage Collection secondaria. Ciò riduce l'impatto dell'operazione di Garbage Collection sulle prestazioni di un'applicazione, dato che le operazioni di Garbage Collection secondaria richiedono meno tempo rispetto a quelle di Garbage Collection principale. La Garbage Collection principale continuerà ad essere eseguita, ma con frequenza minore.

### <a name="reducing-pressure-on-the-garbage-collector"></a>Riduzione della pressione sul Garbage Collector

Quando SGen avvia un'operazione di Garbage Collection, interrompe i thread dell'applicazione e recupera la memoria. Durante il recupero della memoria, potrebbe verificarsi una breve pausa o stuttering dell'applicazione nell'interfaccia utente. La percettibilità di questa pausa dipende da due fattori:

1. **Frequenza**: la frequenza con cui si verifica l'operazione di Garbage Collection. La frequenza di questa operazione aumenta man mano che viene allocata una maggiore quantità di memoria tra le raccolte.
1. **Durata**: il tempo necessario per ogni singola operazione di Garbage Collection. È approssimativamente proporzionale al numero di oggetti attivi che vengono raccolti.

Nel complesso ciò significa che se molti oggetti vengono allocati ma non restano attivi, vengono eseguite molte operazioni di Garbage Collection brevi. Al contrario, se l'allocazione di nuovi oggetti è lenta e gli oggetti restano attivi, vengono eseguite meno operazioni di Garbage Collection, ma più lunghe.

Per ridurre la pressione sul Garbage Collector, attenersi alle indicazioni seguenti:

- Evitare operazioni di Garbage Collection in cicli ridotti usando pool di oggetti. Questa indicazione è pertinente in particolare per i giochi, che devono creare in anticipo la maggior parte degli oggetti usati.
- Rilasciare in modo esplicito risorse quali flussi, connessioni di rete, grandi blocchi di memoria e file quando non sono più necessari. Per altre informazioni, vedere [Rilasciare le risorse IDisposable](#idisposable).
- Per consentire la raccolta degli oggetti, annullare la registrazione dei gestori degli eventi quando non sono più necessari. Per altre informazioni, vedere [Annullare le sottoscrizioni agli eventi](#events).

<a name="linker" />

## <a name="reduce-the-size-of-the-application"></a>Ridurre le dimensioni dell'applicazione

Per comprendere i motivi alla base delle dimensioni del file eseguibile di un'applicazione, è importante comprendere il processo di compilazione per ogni piattaforma:

- Le applicazioni iOS vengono sottoposte a compilazione AOT (Ahead Of Time) nel linguaggio assembly ARM. .NET Framework è incluso. Le classi non usate vengono rimosse solo se è abilitata l'opzione relativa al linker appropriata.
- Le applicazioni Android vengono compilate in un linguaggio intermedio (IL) e incluse in un pacchetto con MonoVM e compilazione JIT. Le classi .NET Framework non usate vengono rimosse solo se è abilitata l'opzione relativa al linker appropriata.
- Le applicazioni Windows Phone vengono compilate in un linguaggio intermedio ed eseguite dal runtime incorporato.

Se poi un'applicazione fa ampio uso di generics, le dimensioni dell'eseguibile finale aumentano di più, perché l'eseguibile conterrà versioni compilate in modo nativo dei generics possibili.

Per favorire la riduzione delle dimensioni delle applicazioni, gli strumenti di compilazione della piattaforma Xamarin includono un linker. Per impostazione predefinita, il linker è disabilitato e deve essere abilitato nelle opzioni del progetto dell'applicazione. In fase di compilazione, il linker esegue un'analisi statica dell'applicazione per determinare i tipi e i membri effettivamente usati dall'applicazione stessa. Rimuove quindi tutti i tipi e tutti i metodi inutilizzati dall'applicazione.

Lo screenshot seguente illustra le opzioni del linker in Visual Studio per Mac per un progetto Xamarin.iOS:

![](memory-perf-best-practices-images/linker-options-ios.png)

Lo screenshot seguente illustra le opzioni del linker in Visual Studio per Mac per un progetto Xamarin.Android:

![](memory-perf-best-practices-images/linker-options-droid.png)

Per controllare il comportamento del linker sono disponibili tre impostazioni diverse:

- **Non collegare**: il linker non rimuoverà i tipi e i metodi inutilizzati. Per motivi di prestazioni, questa è l'impostazione predefinita per le compilazioni di debug.
- **Collega solo SDK del framework/Solo assembly SDK**: questa impostazione consente di ridurre le dimensioni dei soli assembly in dotazione con Xamarin e non influisce sul codice utente.
- **Collega tutti gli assembly**: consente di eseguire un'ottimizzazione più aggressiva degli assembly dell'SDK e del codice utente. Per le associazioni verranno rimossi i campi sottostanti inutilizzati e ogni istanza (o ogni oggetto associato) verrà alleggerito e userà una quantità inferiore di memoria.

L'opzione *Collega tutti gli assembly* deve essere usata con cautela perché può compromettere l'applicazione in modo imprevisto. L'analisi statica eseguita dal linker potrebbe non identificare correttamente tutto il codice effettivamente necessario e, di conseguenza, potrebbe rimuovere una quantità eccessiva di codice dall'applicazione compilata. Questa situazione si rivela solo in runtime con l'arresto anomalo dell'applicazione. Per questo motivo, dopo aver modificato il comportamento del linker è importante testare l'applicazione in modo accurato.

Se il test rivela che il linker ha erroneamente rimosso una classe o un metodo, è possibile contrassegnare i tipi o i metodi a cui non si fa riferimento in modo statico, ma che sono necessari per l'applicazione, tramite uno degli attributi seguenti:

- `Xamarin.iOS.Foundation.PreserveAttribute`: attributo per progetti Xamarin.iOS.
- `Android.Runtime.PreserveAttribute`: attributo per progetti Xamarin.Android.

Può essere ad esempio necessario mantenere i costruttori predefiniti dei tipi di cui vengono create istanze in modo dinamico. L'uso della serializzazione XML, poi, può richiedere il mantenimento delle proprietà dei tipi.

Per altre informazioni, vedere [Linker for iOS](~/ios/deploy-test/linker.md) (Linker per iOS) e [Linker for Android](~/android/deploy-test/linker.md) (Linker per Android).

### <a name="additional-size-reduction-techniques"></a>Altre tecniche per la riduzione delle dimensioni

I dispositivi mobili sono azionati da CPU basate su un'ampia gamma di architetture. Xamarin.iOS e Xamarin.Android generano *file binari FAT* contenenti la versione compilata dell'applicazione per ogni architettura della CPU. In questo modo un'applicazione per dispositivi mobili può essere sicuramente eseguita in un dispositivo, indipendentemente dall'architettura della CPU di questo.

È possibile eseguire i passaggi seguenti per ridurre ancora di più le dimensioni dei file eseguibili delle applicazioni:

- Assicurarsi che venga generata una build di versione.
- Ridurre il numero di architetture per le quali l'applicazione viene compilata, per evitare la generazione di un file binario FAT.
- Verificare che venga usato il compilatore LLVM, per una maggiore ottimizzazione del file eseguibile generato.
- Ridurre le dimensioni del codice gestito dell'applicazione. A tale scopo, abilitare il linker per ogni assembly (*Collega tutto* per i progetti iOS e *Collega tutti gli assembly* per i progetti Android).

Le app Android possono anche essere divise in file APK separati per ogni interfaccia ABI ("architettura").
Altre informazioni in questo post di blog: [How To Keep Your Android App Size Down](https://montemagno.com/how-to-keep-your-android-app-size-down/) (Come mantenere ridotte le dimensioni delle app Android).

<a name="optimizeimages" />

## <a name="optimize-image-resources"></a>Ottimizzare le risorse immagine

Le immagini sono tra le risorse più dispendiose usate dalle applicazioni e spesso vengono acquisite a risoluzioni elevate. Le immagini create in questo modo sono vivaci e ricche di dettagli, ma le applicazioni che le usano devono di solito impegnare maggiormente la CPU per decodificarle e usare una maggiore quantità di memoria dopo averle decodificate. È uno spreco decodificare un'immagine ad alta risoluzione in memoria se poi per visualizzarla è necessario ridurne le dimensioni. È invece consigliabile ridurre l'utilizzo della CPU e il footprint della memoria con la creazione di più versioni delle immagini archiviate, con dimensioni simili a quelle previste per la visualizzazione ma con risoluzioni diverse. Un'immagine visualizzata in una visualizzazione elenco molto probabilmente dovrà avere una risoluzione più bassa di un'immagine visualizzata a schermo intero. Le versioni ridotte delle immagini ad alta risoluzione, poi, possono essere caricate e visualizzate in modo efficiente con un impatto minimo sulla memoria. Per altre informazioni, vedere [Load Large Bitmaps Efficiently](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently) (Caricare in modo efficiente bitmap di grandi dimensioni).

Indipendentemente dalla risoluzione delle immagini, la visualizzazione di queste può aumentare notevolmente il footprint di memoria dell'app. Di conseguenza, tali risorse devono essere create solo se necessario e rilasciate non appena l'applicazione non le richiede più.

<a name="activationperiod" />

## <a name="reduce-the-application-activation-period"></a>Ridurre il periodo di attivazione dell'applicazione

Tutte le applicazioni hanno un *periodo di attivazione*, che è il periodo che intercorre tra il momento dell'avvio dell'applicazione e quello in cui l'applicazione è pronta all'uso. Il periodo di attivazione offre agli utenti la prima impressione dell'applicazione. È quindi importante ridurre sia la durata di questo periodo che la percezione di esso da parte degli utenti.

Prima della comparsa dell'interfaccia utente dell'applicazione, è consigliabile visualizzare una schermata iniziale per indicare all'utente che l'avvio dell'applicazione è in corso. Se l'interfaccia utente iniziale dell'applicazione non può essere visualizzata rapidamente, è consigliabile usare la schermata iniziale per informare l'utente dello stato del periodo di attivazione e per dimostrare che l'applicazione non è bloccata. A tale scopo, è possibile usare un indicatore di stato o un controllo simile.

Durante il periodo di attivazione, viene eseguita la logica di attivazione, che spesso include il caricamento e l'elaborazione delle risorse. È possibile ridurre il periodo di attivazione verificando che le risorse necessarie siano presenti all'interno del pacchetto dell'app e che non sia quindi necessario recuperarle in remoto. In alcuni casi, ad esempio, durante il periodo di attivazione può risultare appropriato caricare i dati segnaposto archiviati localmente. Quindi, dopo la visualizzazione dell'interfaccia utente iniziale e quando l'utente è in grado di interagire con l'app, i dati segnaposto possono essere man mano sostituiti da dati scaricati da un'origine remota. La logica di attivazione dell'app, poi, deve eseguire solo le operazioni necessarie per consentire all'utente di iniziare a usare l'applicazione. Può essere utile ritardare il caricamento di assembly aggiuntivi, dato che gli assembly vengono caricati la prima volta che vengono usati.

<a name="webservicecommunication" />

## <a name="reduce-web-service-communication"></a>Ridurre la comunicazione con i servizi Web

La connessione a un servizio Web da un'applicazione può influire sulle prestazioni dell'applicazione stessa. Un uso maggiore della larghezza di banda, ad esempio, comporta un uso maggiore della batteria del dispositivo. Esiste poi la possibilità che gli utenti usino l'applicazione in un ambiente con larghezza di banda limitata. È quindi opportuno limitare l'utilizzo della larghezza di banda tra l'applicazione e un servizio Web.

Un approccio per ridurre l'utilizzo della larghezza di banda da parte di un'applicazione consiste nel comprimere i dati prima di trasferirli attraverso una rete. L'utilizzo aggiuntivo della CPU dovuto al processo di compressione, tuttavia, può comportare un utilizzo maggiore della batteria. È quindi necessario valutare con attenzione i pro e i contro prima di decidere se spostare dati compressi in rete.

Un altro problema da considerare è il formato dei dati da trasferire tra l'applicazione e il servizio Web. I due formati principali sono XML (eXtensible Markup Language) e JSON (JavaScript Object Notation). XML è un formato di interscambio dei dati basato su testo che genera payload dei dati relativamente grandi, perché contiene un numero elevato di caratteri di formattazione. JSON è un formato di interscambio dei dati basato su testo che genera payload dei dati compatti e richiede quindi una larghezza di banda ridotta per l'invio e la ricezione dei dati. JSON, pertanto, è spesso il formato preferito per le applicazioni per dispositivi mobili.

Per trasferire dati tra un'applicazione e un servizio Web, è consigliabile usare oggetti DTO (Data Transfer Object). Un oggetto DTO contiene un set di dati per il trasferimento in rete. Con i DTO, è possibile trasmettere una maggiore quantità di dati con una singola chiamata remota. Ciò consente di ridurre il numero delle chiamate remote effettuate dall'applicazione. Una chiamata remota che trasmette un payload di dati maggiore richiede in genere una quantità di tempo simile a quella richiesta da una chiamata che trasporta un payload di dati di piccole dimensioni.

È consigliabile memorizzare nella cache locale i dati recuperati dal servizio Web e usare i dati memorizzati nella cache anziché recuperarli ripetutamente dal servizio Web. Se si adotta questo approccio, tuttavia, è necessario implementare anche una strategia appropriata per l'aggiornamento dei dati memorizzati nella cache locale, che devono riflettere le modifiche apportate ai dati nel servizio Web.

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto e illustrato le tecniche per incrementare le prestazioni delle applicazioni create con la piattaforma Xamarin. Insieme, queste tecniche possono ridurre notevolmente il carico di lavoro di una CPU e la quantità di memoria usata da un'applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Prestazioni di Xamarin.iOS](~/ios/deploy-test/performance.md)
- [Xamarin.Android Performance](~/android/deploy-test/performance.md) (Prestazioni di Xamarin.Android)
- [Introduzione a Xamarin Profiler](~/tools/profiler/index.md)
- [Prestazioni di Xamarin.Forms](~/xamarin-forms/deploy-test/performance.md)
- [Panoramica del supporto asincrono](~/cross-platform/platform/async.md)
- [IDisposable](xref:System.IDisposable)
