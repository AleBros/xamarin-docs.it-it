---
title: 'Case Study di App multipiattaforma con: Tasky'
description: Questo documento descrive come l'applicazione di esempio Tasky Portable è stato progettato e compilato come un'applicazione per dispositivi mobili multipiattaforma. Viene descritto l'app requisiti, interfaccia, modello di dati, funzionalità di base, implementazione e informazioni.
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 7bea80c22f6931858d0629382f6882203dfd374f
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829943"
---
# <a name="cross-platform-app-case-study-tasky"></a>Case Study di App multipiattaforma con: Tasky

*Tasky* *portabile* è un'applicazione elenco attività semplice. Questo documento illustra come è stata progettata e compilata, seguendo le indicazioni del [Building Cross-Platform Applications](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento. Nella discussione vengono illustrate le aree seguenti:

<a name="Design_Process" />

## <a name="design-process"></a>Processo di progettazione

È consigliabile creare della Guida di orientamento per ciò che si vuole ottenere prima di iniziare a scrivere codice. Ciò vale soprattutto per lo sviluppo multipiattaforma, in cui si sta compilando la funzionalità che sarà esposti in diversi modi. A partire da un'idea chiara di cosa riuscirai a creare consente di risparmiare tempo e impegno in un secondo momento nel ciclo di sviluppo.

 <a name="Requirements" />

### <a name="requirements"></a>Requisiti

Il primo passaggio nella progettazione di un'applicazione consiste nell'identificare le funzionalità desiderate. Può trattarsi di obiettivi di alto livello o casi d'uso descritti in dettaglio. Tasky presenta i requisiti funzionali semplice:

-  Visualizzare un elenco di attività
-  Aggiungere, modificare ed eliminare le attività
-  Impostare lo stato dell'attività su 'done'

È necessario considerare l'uso di funzionalità specifiche della piattaforma.  Tasky trarre vantaggio dalle geofencing iOS o Windows Phone Live Tile? Anche se non si utilizza funzionalità specifiche della piattaforma nella prima versione, è consigliabile pianificare, assicurarsi che la vostra azienda e i livelli di dati in grado di gestirli.

 <a name="User_Interface_Design" />

### <a name="user-interface-design"></a>Progettazione dell'interfaccia utente

Iniziare con una progettazione di alto livello che può essere implementata per le piattaforme di destinazione. Prestare attenzione ai vincoli di interfaccia utente Nota piattaforma specifico. Ad esempio, un `TabBarController` in iOS può visualizzare più di cinque pulsanti, mentre l'equivalente di Windows Phone possa visualizzare fino a quattro.
Tracciare il flusso di schermo usando lo strumento di propria scelta (paper funziona).

 [![](case-study-tasky-images/taskydesign.png "Tracciare il flusso di schermo usando lo strumento del funzionamento di carta scelta")](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model" />

### <a name="data-model"></a>Modello dati

Sapere quali dati devono essere archiviati per determinare quale meccanismo di persistenza da utilizzare. Visualizzare [l'accesso ai dati di multi-piattaforma](~/cross-platform/app-fundamentals/index.md) per informazioni sui meccanismi di archiviazione disponibile e ulteriori informazioni sulla scelta tra di essi. Per questo progetto, verrà usato SQLite.NET.

Tasky deve archiviare tre proprietà per ogni 'TaskItem':

- **Nome** : stringa
- **Note sulla** : stringa
- **Operazione eseguita** : booleano

 <a name="Core_Functionality" />

### <a name="core-functionality"></a>Funzionalità principali

Si consideri l'API dell'interfaccia utente da utilizzare per soddisfare i requisiti. Un elenco di attività richiede le seguenti funzioni:

- **Elenca tutte le attività** : consente di visualizzare l'elenco di schermata principale di tutte le attività disponibili
- **Ottenere una sola attività** : quando una riga dell'attività modificata
- **Salvare un'attività** : quando un'attività viene modificata
- **Eliminare un'attività** : quando un'attività viene eliminata
- **Creazione di attività vuoto** : quando viene creata una nuova attività

Per ottenere il riutilizzo del codice, questa API deve essere implementata una volta nel *libreria di classi portabile*.

 <a name="Implementation" />

### <a name="implementation"></a>Implementazione

Dopo che stata concordata la progettazione dell'applicazione, prendere in considerazione la modalità può essere implementata come un'applicazione multi-piattaforma. Che diventerà l'architettura dell'applicazione. Seguire le istruzioni disponibili nel [Building Cross-Platform Applications](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento, il codice dell'applicazione deve essere interrotto down nelle parti seguenti:

- **Il codice comune** : un progetto comune che contiene il codice generiche riutilizzabili per archiviare i dati di attività; espone una classe di modello e un'API per gestire il salvataggio e caricamento dei dati.
- **Codice specifico della piattaforma** – progetti specifici delle piattaforme che implementano un'interfaccia utente nativa per ogni sistema operativo, che usano il codice comune come 'back-end'.

[![](case-study-tasky-images/taskypro-architecture.png "Progetti specifici delle piattaforme implementano un'interfaccia utente nativa per ogni sistema operativo, che usano il codice comune come back-end")](case-study-tasky-images/taskypro-architecture.png#lightbox)

Queste due parti sono descritte nelle sezioni seguenti.

 <a name="Common_(PCL)_Code" />

## <a name="common-pcl-code"></a>Common (PCL) Code

Tasky Portable Usa la strategia di libreria di classi portabile per condividere codice comune. Vedere le [opzioni di condivisione del codice](~/cross-platform/app-fundamentals/code-sharing.md) documento per una descrizione delle opzioni di condivisione del codice.

Tutto il codice più comune, tra cui il livello di accesso ai dati, codice di database e contratti, viene inserito nel progetto di libreria.

Il progetto libreria di classi Portabile completo è illustrato di seguito. Tutto il codice nella libreria portabile è compatibile con ogni piattaforma di destinazione. Quando viene distribuito, ogni app nativa farà riferimento a tale libreria.

![](case-study-tasky-images/portable-project.png "Quando viene distribuito, ogni app nativa farà riferimento a tale libreria")

Il diagramma classi seguente vengono illustrate le classi raggruppate per livello. Il `SQLiteConnection` classe è codice boilerplate dal pacchetto di Sqlite-NET. Il resto delle classi sono codici personalizzati per Tasky. Il `TaskItemManager` e `TaskItem` classi rappresentano l'API esposta alle applicazioni specifiche della piattaforma.

 [![](case-study-tasky-images/classdiagram-core.png "Le classi TaskItemManager e TaskItem rappresentano l'API esposta alle applicazioni specifiche della piattaforma")](case-study-tasky-images/classdiagram-core.png#lightbox)

L'utilizzo di spazi dei nomi per separare i livelli consente di gestire i riferimenti tra ogni livello. I progetti specifici della piattaforma è necessario solo includere un `using` istruzione per il livello Business. Il livello di accesso ai dati e il livello di dati deve essere incapsulata dall'API esposta da `TaskItemManager` nel livello Business.

 <a name="References" />

### <a name="references"></a>Riferimenti

Librerie di classi portabile devono essere utilizzabili in più piattaforme, ognuna con vari livelli di supporto per le funzionalità della piattaforma e framework. Per questo motivo, sono previste limitazioni in cui i pacchetti e librerie del framework possono essere usate. Ad esempio, xamarin. IOS non supporta il linguaggio c# `dynamic` (parola chiave), in modo da una libreria di classi portabili non è possibile usare i pacchetti che dipende dal codice dinamico, anche se tale codice funzionerebbe in Android. Visual Studio per Mac impedirà di aggiunta di pacchetti non compatibili e riferimenti, ma è opportuno tenere presente per evitare sorprese in un secondo momento le limitazioni.

Nota: Si noterà che i progetti di fare riferimento alle librerie di framework che non è stato utilizzato. Questi riferimenti sono inclusi come parte dei modelli di progetto Xamarin. Quando le app vengono compilate, il processo di collegamento rimuoverà codice privo di riferimento, pertanto anche se `System.Xml` è stato fatto riferimento, verrà non incluso nell'applicazione finale poiché non si utilizzano le funzioni di Xml.

 <a name="Data_Layer_(DL)" />

### <a name="data-layer-dl"></a>Livello dati (DL)

Il livello dati contiene il codice che esegue l'archiviazione fisica di dati, a un database, file flat o altro meccanismo. Il livello dati Tasky è costituito da due parti: la libreria di SQLite-NET e il codice personalizzato aggiunto per associare lo.

Tasky si basa sul pacchetto nuget Sqlite-net (pubblicato da Frank Kreuger) per incorporare il codice di SQLite-NET che fornisce un'interfaccia di database relazionale a oggetti di Mapping (ORM). Il `TaskItemDatabase` classe eredita da `SQLiteConnection` e aggiunge i necessari creazione, lettura, aggiornamento, i metodi di eliminazione (CRUD) per leggere e scrivere dati in SQLite. È un'implementazione semplice boilerplate di metodi CRUD generici che potrebbero essere riutilizzate in altri progetti.

Il `TaskItemDatabase` è un singleton, assicurando che tutti gli accessi si verifica con l'istanza stessa. Un blocco viene utilizzato per impedire l'accesso simultaneo da più thread.

 <a name="SQLite_on_WIndows_Phone" />

#### <a name="sqlite-on-windows-phone"></a>SQLite in Windows Phone

IOS e Android entrambe fornite con SQLite come parte del sistema operativo, Windows Phone non include un motore di database compatibili. Per condividere codice tra tutti e tre le piattaforme è necessaria una versione di Windows phone native di SQLite. Visualizzare [utilizzo di un Database locale](~/xamarin-forms/data-cloud/data/databases.md) per altre informazioni sulla configurazione di progetto Windows Phone per Sqlite.

 <a name="Using_an_Interface_to_Generalize_Data_Access" />

#### <a name="using-an-interface-to-generalize-data-access"></a>Utilizzando un'interfaccia per generalizzare l'accesso ai dati

Il livello di dati assume una dipendenza dal `BL.Contracts.IBusinessIdentity` in modo che possono implementare metodi di accesso astratta dei dati che richiedono una chiave primaria. Qualsiasi classe di livello aziendale che implementa l'interfaccia può essere reso persistente nel livello di dati.

L'interfaccia specifica solo una proprietà integer come chiave primaria:

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

La classe base implementa l'interfaccia e aggiunge gli attributi di SQLite-NET per contrassegnarlo come una chiave primaria con incremento automatico. Qualsiasi classe di livello aziendale che implementa questa classe di base può essere reso persistente nel livello di dati:

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

Questo è un esempio di metodi generici nel livello di dati che usano l'interfaccia `GetItem<T>` metodo:

```csharp
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

 <a name="Locking_to_prevent_Concurrent_Access" />

#### <a name="locking-to-prevent-concurrent-access"></a>Il blocco per impedire l'accesso simultaneo

Oggetto [blocco](https://msdn.microsoft.com/library/c5kehkcz(v=vs.100).aspx) viene implementato all'interno di `TaskItemDatabase` classe per impedire l'accesso simultaneo al database. Questo modo si garantisce l'accesso simultaneo da thread diversi è serializzato (in caso contrario, un componente dell'interfaccia utente potrebbe tentare di leggere il database allo stesso tempo un thread in background è aggiornarla). Di seguito è riportato un esempio della modalità di implementazione del blocco:

```csharp
static object locker = new object ();
public IEnumerable<T> GetItems<T> () where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return (from i in Table<T> () select i).ToList ();
    }
}
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

La maggior parte del codice del livello dati potrebbero essere riutilizzata in altri progetti. Il codice specifico dell'applicazione solo nel livello è il `CreateTable<TaskItem>` chiamare nel `TaskItemDatabase` costruttore.

 <a name="Data_Access_Layer_(DAL)" />

### <a name="data-access-layer-dal"></a>Livello di accesso ai dati (DAL)

Il `TaskItemRepository` classe incapsula il meccanismo di archiviazione dei dati con un'API fortemente tipizzato che consente a `TaskItem` oggetti da creare, eliminare, recuperato e aggiornato.

 <a name="Using_Conditional_Compilation" />

#### <a name="using-conditional-compilation"></a>Utilizza la compilazione condizionale

La classe viene utilizzata la compilazione condizionale per impostare il percorso del file: si tratta di un esempio di implementazione divergenza di piattaforma. La proprietà che restituisce il percorso viene compilato in codice diverso in ogni piattaforma. Il codice e direttive del compilatore specifiche della piattaforma riportate di seguito:

```csharp
public static string DatabaseFilePath {
    get {
        var sqliteFilename = "TaskDB.db3";
#if SILVERLIGHT
        // Windows Phone expects a local path, not absolute
        var path = sqliteFilename;
#else
#if __ANDROID__
        // Just use whatever directory SpecialFolder.Personal returns
        string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
        // we need to put in /Library/ on iOS5.1+ to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder
#endif
        var path = Path.Combine (libraryPath, sqliteFilename);
                #endif
                return path;
    }
}
```

A seconda della piattaforma, l'output sarà "<app
path>/Library/TaskDB.db3" per iOS, "<app
path>/Documents/TaskDB.db3" per Android o semplicemente "TaskDB.db3" per Windows Phone.

### <a name="business-layer-bl"></a>Livello di business (BL)

Il livello di Business implementa le classi del modello e una facciata per gestirli.
In Tasky il modello è il `TaskItem` classe e `TaskItemManager` implementa il pattern di facciata per fornire un'API per la gestione `TaskItems`.

 <a name="Façade" />

#### <a name="faade"></a>Façade

 `TaskItemManager` esegue il wrapping di `DAL.TaskItemRepository` per fornire Get, salvare ed eliminare i metodi che verranno fatto riferimento dall'applicazione e livelli dell'interfaccia utente.

Se necessario, ad esempio tutte le regole di convalida che devono essere soddisfatti prima del salvataggio di un oggetto, verranno inseriti qui la logica e le regole di business.

 <a name="API_for_Platform-Specific_Code" />

### <a name="api-for-platform-specific-code"></a>API per il codice specifico della piattaforma

Dopo il codice comune è stata scritta, l'interfaccia utente deve essere compilato per raccogliere e visualizzare i dati esposti da esso. Il `TaskItemManager` classe implementa il pattern di facciata per fornire una semplice API per il codice dell'applicazione per accedere.

Il codice scritto in ogni progetto specifico della piattaforma verranno in genere essere strettamente collegati ai SDK nativo del dispositivo e accedere solo il codice comune con l'API definita dal `TaskItemManager`. Sono inclusi i metodi e business le classi espone, ad esempio `TaskItem`.

Le immagini non vengono condivise tra le piattaforme ma aggiungere in modo indipendente per ogni progetto. Questo è importante perché ogni piattaforma gestisce le immagini in modo diverso, mediante le soluzioni, le directory e nomi di file diversi.

Le sezioni rimanenti illustrano i dettagli di implementazione specifica della piattaforma dell'interfaccia utente Tasky.

 <a name="iOS_App" />

## <a name="ios-app"></a>App iOS

Sono disponibili solo un numero limitato di classi necessarie per implementare l'applicazione Tasky usando il progetto di libreria di classi Portabile comune per archiviare e recuperare i dati di iOS. Di seguito è riportato il progetto xamarin. IOS versione completa di iOS:

 ![](case-study-tasky-images/taskyios-solution.png "progetto iOS è illustrato di seguito")

Le classi sono illustrate in questo diagramma, raggruppato in livelli.

 [![](case-study-tasky-images/classdiagram-android.png "Le classi sono illustrate in questo diagramma, raggruppato in livelli")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>Riferimenti

L'app per iOS le librerie SDK specifiche della piattaforma: fa riferimento ad es. Xamarin. IOS e monotouch. Dialog-1.

Anche necessario fare riferimento il `TaskyPortableLibrary` progetto libreria di classi Portabile.
Seguito è riportato l'elenco di riferimenti:

 ![](case-study-tasky-images/taskyios-references.png "L'elenco di riferimenti è illustrato di seguito")

Il livello dell'applicazione e il livello dell'interfaccia utente vengono implementati in questo progetto utilizzando tali riferimenti.

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Livello dell'applicazione (AL)

Il livello dell'applicazione contiene le classi specifiche della piattaforma necessarie per associare gli oggetti esposti dalla libreria di classi Portabile per l'interfaccia utente. L'applicazione iOS specifico ha due classi per consentire di visualizzare le attività:

- **EditingSource** : questa classe viene utilizzata per associare gli elenchi di attività per l'interfaccia utente. In quanto `MonoTouch.Dialog` è stato usato per l'elenco di attività, è necessario implementare questo helper per abilitare la funzionalità di scorrimento rapido-a-delete nel `UITableView` . Scorrere rapidamente-a-delete è comune in iOS, ma non Android o Windows Phone, in modo che il progetto specifico di iOS è l'unico che lo implementa.
- **TaskDialog** : questa classe viene utilizzata per associare una singola attività nell'interfaccia utente. Usa il `MonoTouch.Dialog` API di Reflection a 'wrap' il `TaskItem` oggetto con una classe che contiene gli attributi corretti per consentire la schermata di input da formattare correttamente.

Il `TaskDialog` utilizzato dalla classe `MonoTouch.Dialog` attributi per creare una schermata in base alle proprietà della classe. La classe è simile alla seguente:

```csharp
public class TaskDialog {
    public TaskDialog (TaskItem task)
    {
        Name = task.Name;
        Notes = task.Notes;
        Done = task.Done;
    }
    [Entry("task name")]
    public string Name { get; set; }
    [Entry("other task info")]
    public string Notes { get; set; }
    [Entry("Done")]
    public bool Done { get; set; }
    [Section ("")]
    [OnTap ("SaveTask")]    // method in HomeScreen
    [Alignment (UITextAlignment.Center)]
    public string Save;
    [Section ("")]
    [OnTap ("DeleteTask")]  // method in HomeScreen
    [Alignment (UITextAlignment.Center)]
    public string Delete;
}
```

Si noti che il `OnTap` attributi richiedono un nome di metodo: questi metodi devono essere presente nella classe in cui la `MonoTouch.Dialog.BindingContext` viene creato (in questo caso, il `HomeScreen` classe descritti nella sezione successiva).

 <a name="User_Interface_Layer_(UI)" />

### <a name="user-interface-layer-ui"></a>Livello dell'interfaccia utente (UI)

Il Layer dell'interfaccia utente costituito dalle classi seguenti:

1.   **AppDelegate** – contiene le chiamate all'API di aspetto per definire lo stile di carattere e colori usati nell'applicazione. Tasky è una semplice applicazione in modo che non sono presenti altre attività di inizializzazione in esecuzione in `FinishedLaunching` .
2.   **Le schermate** – le sottoclassi di `UIViewController` che definiscono ogni schermata e il relativo comportamento. Le schermate di unire l'interfaccia utente con le classi di livello applicazione e l'API comune ( `TaskItemManager` ). In questo esempio vengono create le schermate nel codice, ma è stato possibile sono stati progettati con Interface Builder di Xcode o la finestra di progettazione dello storyboard.
3.   **Le immagini** : gli elementi visivi sono una parte importante di tutte le applicazioni. Tasky ha iniziale dello schermo e l'icona immagini che per iOS devono essere specificate in normali e di risoluzione Retina.

 <a name="Home_Screen" />

#### <a name="home-screen"></a>Schermata iniziale

La schermata Home è una `MonoTouch.Dialog` schermata che visualizza un elenco di attività dal database SQLite. Eredita da `DialogViewController` e implementa il codice per impostare il `Root` per contenere una raccolta di `TaskItem` oggetti per la visualizzazione.

 [![](case-study-tasky-images/ios-taskylist.png "Eredita da DialogViewController e implementa il codice per impostare la radice per contenere una raccolta di oggetti TaskItem da visualizzare")](case-study-tasky-images/ios-taskylist.png#lightbox)

I due metodi principali correlati alla visualizzazione e l'interazione con l'elenco di attività sono:

1.   **PopulateTable** : Usa il livello di Business `TaskManager.GetTasks` metodo per recuperare una raccolta di `TaskItem` oggetti da visualizzare.
2.   **Selezionato** : quando una riga viene manipolata, consente di visualizzare l'attività in una nuova schermata.

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>Schermata dei dettagli attività

Dettagli dell'attività è una schermata di input che consente alle attività di essere modificato o eliminato.

Usa tasky `MonoTouch.Dialog`dell'API di Reflection per visualizzare la schermata, pertanto non sia presente alcun `UIViewController` implementazione. Al contrario, il `HomeScreen` crea un'istanza di classe e viene visualizzato un `DialogViewController` usando il `TaskDialog` classe dal livello dell'applicazione.

Questo screenshot Mostra una schermata vuota a cui viene illustrato il `Entry` attributo impostazione del testo della filigrana nel **nome** e **note** campi:

 [![](case-study-tasky-images/ios-taskydetail.png "Questo screenshot Mostra una schermata vuota a cui viene illustrato l'attributo voce impostando il testo della filigrana nei campi nome e le note")](case-study-tasky-images/ios-taskydetail.png#lightbox)

La funzionalità del **dettagli attività** schermata (ad esempio, salvare o eliminare un'attività) deve essere implementato nel `HomeScreen` classe, poiché si tratta di dove il `MonoTouch.Dialog.BindingContext` viene creato. Nell'esempio `HomeScreen` metodi supportano la schermata dei dettagli attività:

1.   **ShowTaskDetails** – consente di creare un `MonoTouch.Dialog.BindingContext` per eseguire il rendering di una schermata. Crea la schermata di input usando la reflection per recuperare i nomi delle proprietà e i tipi dal `TaskDialog` classe. Informazioni aggiuntive, ad esempio il testo della filigrana per le caselle di input, viene implementate con gli attributi alle proprietà.
2.   **SaveTask** : questo metodo fa riferimento il `TaskDialog` classe tramite un `OnTap` attributo. Viene chiamato quando **salvare** pressione e Usa una `MonoTouch.Dialog.BindingContext` per recuperare i dati immessi dall'utente prima di salvare le modifiche utilizzando `TaskItemManager` .
3.   **DeleteTask** : questo metodo fa riferimento il `TaskDialog` classe tramite un `OnTap` attributo. Usa `TaskItemManager` per eliminare i dati usando la chiave primaria (la proprietà ID).

 <a name="Android_App" />

## <a name="android-app"></a>App Android

Il progetto xamarin. Android completato è illustrato di seguito:

 ![](case-study-tasky-images/taskyandroid-solution.png "Progetto Android è raffigurato di seguito")

Diagramma classi con classi raggruppati per livello:

 [![](case-study-tasky-images/classdiagram-android.png "Diagramma classi con classi raggruppati per livello")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>Riferimenti

Il progetto di app Android deve fare riferimento assembly specifici della piattaforma xamarin. Android per accedere alle classi di Android SDK.

Anche necessario fare riferimento al progetto libreria di classi Portabile (ad es. TaskyPortableLibrary) per accedere al codice di livello dati e business comune.

 ![](case-study-tasky-images/taskyandroid-references.png "TaskyPortableLibrary per accedere al codice di livello aziendale e dati comune")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Livello dell'applicazione (AL)

Analogamente alla versione iOS che è stato esaminato in precedenza, il livello dell'applicazione nella versione di Android contiene le classi specifiche della piattaforma necessarie per associare gli oggetti esposti dagli elementi di base per l'interfaccia utente.

 **TaskListAdapter** : consente di visualizzare un elenco<T> degli oggetti è necessario implementare un adattatore per visualizzare gli oggetti personalizzati in un `ListView`. L'adapter controlla il layout viene utilizzato per ogni elemento nell'elenco, in questo caso il codice Usa un layout predefinito Android `SimpleListItemChecked`.

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>Interfaccia utente (UI)

Livello dell'interfaccia utente dell'app per Android è una combinazione di codice e markup XML.

- **Le risorse/Layout** – progettazione implementati come file AXML delle celle layout dello schermo e della riga. Il AXML può essere scritto manualmente o disposto in modo visivo mediante la finestra di progettazione dell'interfaccia utente di Xamarin per Android.
- **Le risorse/Drawable** – immagini (icone) e un pulsante personalizzato.
- **Le schermate** – sottoclassi di attività che definiscono ogni schermata e il relativo comportamento. Collega l'interfaccia utente con le classi di livello applicazione e l'API comune (`TaskItemManager`).

 <a name="Home_Screen" />

#### <a name="home-screen"></a>Schermata iniziale

La schermata iniziale è costituito da una sottoclasse di attività `HomeScreen` e il `HomeScreen.axml` file che definisce il layout (posizione dell'elenco attività e pulsante). La schermata sarà simile al seguente:

 [![](case-study-tasky-images/android-taskylist.png "La schermata sarà simile al seguente")](case-study-tasky-images/android-taskylist.png#lightbox)

Il codice della schermata iniziale definisce i gestori per clic sul pulsante e facendo clic sugli elementi nell'elenco, nonché compilazione dell'elenco nel `OnResume` (metodo) (in modo che rifletta le modifiche apportate nella schermata dei dettagli attività). I dati vengono caricati utilizzando il livello di Business `TaskItemManager` e il `TaskListAdapter` dal livello dell'applicazione.

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>Schermata dei dettagli attività

Schermata dei dettagli attività anche è costituito da un `Activity` sottoclasse e un file di layout AXML. Il layout determina la posizione dei controlli di input e il C# classe definisce il comportamento per caricare e salvare `TaskItem` oggetti.

 [![](case-study-tasky-images/android-taskydetail.png "La classe definisce il comportamento per caricare e salvare gli oggetti TaskItem")](case-study-tasky-images/android-taskydetail.png#lightbox)

Tutti i riferimenti alla libreria PCL siano attraverso il `TaskItemManager` classe.

 <a name="Windows_Phone_App" />

## <a name="windows-phone-app"></a>App di Windows Phone
Il progetto Windows Phone completo:

 ![](case-study-tasky-images/taskywp7-solution.png "App di Windows Phone il progetto Windows Phone completo")

Il diagramma seguente offre le classi raggruppate in livelli:

 [![](case-study-tasky-images/classdiagram-wp7.png "Questo diagramma illustra le classi raggruppate in livelli")](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References" />

### <a name="references"></a>Riferimenti

Il progetto specifico della piattaforma deve fare riferimento le librerie specifiche della piattaforma necessarie (ad esempio `Microsoft.Phone` e `System.Windows`) per creare un'applicazione Windows Phone valida.

Anche necessario fare riferimento al progetto libreria di classi Portabile (ad es. `TaskyPortableLibrary`) per utilizzare il `TaskItem` classe e il database.

 ![](case-study-tasky-images/taskywp7-references.png "TaskyPortableLibrary di utilizzare la classe TaskItem e il database")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Livello dell'applicazione (AL)

Anche in questo caso, come con le versioni di Android e iOS, il livello dell'applicazione costituito da elementi non visivo che consentono di associare i dati all'interfaccia utente.

 <a name="ViewModels" />

#### <a name="viewmodels"></a>ViewModel

ViewModel di eseguire il wrapping dei dati dalla libreria di classi Portabile ( `TaskItemManager`) e le visualizza in modo che può essere utilizzato dall'associazione dati Silverlight o XAML. Questo è un esempio di comportamento specifici della piattaforma (come descritto nel documento di applicazioni multipiattaforma).

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>Interfaccia utente (UI)

XAML è una funzionalità di associazione dati univoca che può essere dichiarata nel markup e ridurre la quantità di codice necessario per visualizzare gli oggetti:

1.   **Pagine** – loro codebehind e i file XAML definire l'interfaccia utente e i ViewModel e il progetto libreria di classi Portabile per visualizzare e raccogliere dati di riferimento.
2.   **Le immagini** -immagini della schermata di visualizzazione, sfondo e icona iniziale sono una parte fondamentale dell'interfaccia utente.

 <a name="MainPage" />

#### <a name="mainpage"></a>MainPage

La classe MainPage Usa il `TaskListViewModel` per visualizzare i dati usando le funzionalità di data binding di XAML. La pagina `DataContext` è impostato per il modello di visualizzazione, che viene compilato in modo asincrono. Il `{Binding}` sintassi di XAML determina la modalità di visualizzazione dei dati.

 <a name="TaskDetailsPage" />

#### <a name="taskdetailspage"></a>TaskDetailsPage

Ogni attività viene visualizzata mediante l'associazione di `TaskViewModel` al XAML definito nel TaskDetailsPage.xaml. I dati di attività viene recuperati tramite il `TaskItemManager` nel livello Business.

 <a name="Results" />

## <a name="results"></a>Risultati

Le applicazioni risultante è simile al seguente in ogni piattaforma:

 <a name="iOS" />

#### <a name="ios"></a>iOS

L'applicazione usa progettazione dell'interfaccia utente iOS-standard, ad esempio il pulsante 'Aggiungi' viene posizionato sulla barra di spostamento e l'utilizzo integrato **segno più (+)** icona. Usa inoltre il valore predefinito `UINavigationController` pulsante "Indietro" comportamento e supporta 'scorrere rapidamente-a-delete' nella tabella.

 [![](case-study-tasky-images/ios-taskylist.png "Inoltre utilizza le impostazioni predefinite UINavigationController pulsante Indietro e supporta scorrere per eliminare la tabella")](case-study-tasky-images/ios-taskylist.png#lightbox) [![](case-study-tasky-images/ios-taskylist.png "utilizza anche il valore predefinito UINavigationController il comportamento di un pulsante e supporta scorrere per eliminare la tabella")](case-study-tasky-images/ios-taskylist.png#lightbox)

 <a name="Android" />

#### <a name="android"></a>Android

L'app per Android utilizza controlli incorporati, tra cui il layout predefinito per le righe che richiedono un 'tick' visualizzato. È supportato il comportamento del back-hardware/sistema oltre a un pulsante Indietro sullo schermo.

 [![](case-study-tasky-images/android-taskylist.png "È supportato il comportamento del back-hardware/sistema oltre a un pulsante Indietro su schermo")](case-study-tasky-images/android-taskylist.png#lightbox)[![](case-study-tasky-images/android-taskylist.png "è supportato il comportamento del back-hardware/sistema oltre a sullo schermo pulsante Indietro")](case-study-tasky-images/android-taskylist.png#lightbox)

 <a name="Windows_Phone" />

#### <a name="windows-phone"></a>Windows Phone

L'app di Windows Phone Usa il layout standard, popolamento barra dell'app nella parte inferiore della schermata anziché una barra di navigazione nella parte superiore.

 [![](case-study-tasky-images/wp-taskylist.png "L'app di Windows Phone utilizza il layout standard, la barra dell'app nella parte inferiore dello schermo anziché una barra di navigazione nella parte superiore di compilazione")](case-study-tasky-images/wp-taskylist.png#lightbox) [![](case-study-tasky-images/wp-taskylist.png "app di Windows Phone utilizza lo standard layout, la barra dell'app nella parte inferiore dello schermo anziché una barra di navigazione nella parte superiore di compilazione")](case-study-tasky-images/wp-taskylist.png#lightbox)

 <a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo documento è fornita una spiegazione dettagliata del modo in cui i principi di progettazione di applicazioni a più livelli applicati a un'applicazione semplice per facilitare il riutilizzo del codice tra tre le piattaforme per dispositivi mobili: iOS, Android e Windows Phone.

Viene descritto il processo utilizzato per progettare i livelli applicazione e descritto il tipo di codice &amp; funzionalità è stata implementata in ogni livello.

Il codice può essere scaricato dal [github](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable).

## <a name="related-links"></a>Collegamenti correlati

- [Building Cross-Platform Applications (documento principale)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [App di esempio portatile tasky (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
