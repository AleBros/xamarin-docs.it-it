---
title: 'Case Study di App multipiattaforma: Tasky'
description: Questo documento viene descritto come l'applicazione di esempio Tasky Portable è stato progettato e compilato come un'applicazione per dispositivi mobili multipiattaforma. Viene descritto l'app requisiti, interfaccia, modello di dati, funzionalità di base, implementazione e altro ancora.
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 48650445d06ad3bc7ca6d4da84c9b8837f8a0f88
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782235"
---
# <a name="cross-platform-app-case-study-tasky"></a>Case Study di App multipiattaforma: Tasky

*Tasky* *portabile* è un'applicazione di elenco semplice di attività da eseguire. Questo documento viene illustrato come è stata progettata e compilata, seguendo le indicazioni del [compilazione di applicazioni multipiattaforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento. La discussione include le seguenti operazioni:

<a name="Design_Process" />

## <a name="design-process"></a>Processo di progettazione

È consigliabile creare un di stradali per ciò che si desidera ottenere prima di iniziare la codifica. Ciò vale soprattutto per lo sviluppo multipiattaforma, in cui si sta compilando funzionalità che verranno esposte in più modi. A partire da un'idea di cosa si sta creando i consente di risparmiare tempo e impegno in un secondo momento nel ciclo di sviluppo.

 <a name="Requirements" />

### <a name="requirements"></a>Requisiti

Il primo passaggio nella progettazione di un'applicazione consiste nell'identificare le funzionalità desiderate. Può trattarsi di obiettivi di alto livello o descritti in dettaglio i casi di utilizzo. Tasky presenta i requisiti funzionali semplici:

 -  Visualizzare un elenco di attività
 -  Aggiungere, modificare ed eliminare le attività
 -  Impostare lo stato dell'attività su 'done'

È necessario considerare l'uso delle funzionalità specifiche della piattaforma.  Possibile Tasky sfruttare iOS geofencing o riquadri in tempo reale di Windows Phone? Anche se non si utilizza funzionalità specifiche della piattaforma nella prima versione, è necessario pianificare in anticipo per assicurarsi che la vostra azienda e i livelli di dati in grado di gestirli.

 <a name="User_Interface_Design" />

### <a name="user-interface-design"></a>Progettazione dell'interfaccia utente

Iniziare con una struttura di alto livello che può essere implementata in piattaforme di destinazione. Prestare attenzione ai vincoli di interfaccia utente Nota piattaforma specifico. Ad esempio, un `TabBarController` in iOS possono essere visualizzati più di cinque pulsanti, mentre l'equivalente di Windows Phone è possibile visualizzare fino a quattro.
Tracciare il flusso di schermata utilizzando lo strumento di propria scelta (carta funziona).

 [![](case-study-tasky-images/taskydesign.png "Disegnare il flusso di schermata utilizzando lo strumento del funzionamento di carta scelta")](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model" />

### <a name="data-model"></a>Modello dati

Conoscere quali dati devono essere archiviati consente di determinare quale meccanismo di persistenza da utilizzare. Vedere [accesso ai dati multipiattaforma](~/cross-platform/app-fundamentals/index.md) per informazioni sui meccanismi di archiviazione disponibile e informazioni sulla scelta tra di essi. Per questo progetto, verrà usato SQLite.NET.

Tasky deve archiviare tre proprietà per ogni oggetto TaskItem:

 -  **Nome** : stringa
 -  **Note sulla** : stringa
 -  **Operazione eseguita** : booleano

 <a name="Core_Functionality" />

### <a name="core-functionality"></a>Funzionalità principali

Si consideri l'API che dovrà utilizzare per soddisfare i requisiti dell'interfaccia utente. Un elenco di attività richiede le seguenti funzioni:

 -   **Elencare tutte le attività** : consente di visualizzare l'elenco di schermata principale di tutte le attività disponibili
 -  **Ottenere una sola attività** : quando viene eseguita una riga dell'attività
 -  **Salvare un'attività** : quando viene modificata un'attività
 -  **Eliminare un'attività** : quando viene eliminata un'attività
 -  **Creare attività vuota** : quando viene creata una nuova attività

Per ottenere il riutilizzo del codice, questa API deve essere implementata una volta nel *libreria di classi portabile*.

 <a name="Implementation" />

### <a name="implementation"></a>Implementazione

Una volta concordata la progettazione dell'applicazione, prendere in considerazione come può essere implementata come un'applicazione multipiattaforma. Che diventerà l'architettura dell'applicazione. Le istruzioni disponibili nel [compilazione di applicazioni multipiattaforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento, il codice dell'applicazione deve essere interrotto down nelle parti seguenti:

 -   **Il codice comune** : un progetto comune che contiene il codice riutilizzabili per archiviare i dati di attività; esporre una classe di modello e un'API per gestire il salvataggio e caricamento dei dati.
 -   **Codice specifico della piattaforma** – progetti specifici della piattaforma che implementano un'interfaccia utente nativa per ogni sistema operativo, che utilizzano il codice comune come 'back-end'.

 [![](case-study-tasky-images/taskypro-architecture.png "Progetti specifici per la piattaforma di implementano un'interfaccia utente nativa per ogni sistema operativo, che utilizzano il codice comune come back-end")](case-study-tasky-images/taskypro-architecture.png#lightbox)

Queste due parti sono descritti nelle sezioni seguenti.

 <a name="Common_(PCL)_Code" />

## <a name="common-pcl-code"></a>Codice comune (PCL)

Tasky Portable utilizza la strategia di libreria di classi portabile per condividere il codice comune. Vedere il [opzioni di condivisione del codice](~/cross-platform/app-fundamentals/code-sharing.md) documento per una descrizione delle opzioni di condivisione del codice.

Tutto il codice più comune, tra cui il livello di accesso ai dati, il codice di database e i contratti, viene inserito nel progetto di libreria.

Il progetto PCL completo è illustrato di seguito. Tutto il codice della libreria portabile è compatibile con ogni piattaforma di destinazione. Quando viene distribuito, ogni app nativa farà riferimento a tale raccolta.

![](case-study-tasky-images/portable-project.png "Quando viene distribuito, ogni app nativa verrà riferimento alla libreria")

Il diagramma classi seguente mostra le classi raggruppate per livello. La `SQLiteConnection` classe è codice boilerplate dal pacchetto di rete di Sqlite. Il resto delle classi sono codice personalizzato per Tasky. Il `TaskItemManager` e `TaskItem` classi rappresentano l'API esposta alle applicazioni specifiche della piattaforma.

 [![](case-study-tasky-images/classdiagram-core.png "Le classi TaskItemManager e TaskItem rappresentano l'API esposta alle applicazioni specifiche della piattaforma")](case-study-tasky-images/classdiagram-core.png#lightbox)

Utilizzo di spazi dei nomi per separare i livelli consente di gestire i riferimenti tra ogni livello. I progetti specifici della piattaforma è necessario solo includere un `using` istruzione per il livello Business. Il livello di accesso ai dati e il livello di dati deve essere incapsulato dall'API esposta da `TaskItemManager` nel livello Business.

 <a name="References" />

### <a name="references"></a>Riferimenti

Librerie di classi portabili necessario affinché possa essere usato in più piattaforme, ognuna con vari livelli di supporto per le funzionalità di piattaforma e framework. Per questo motivo, esistono limitazioni in cui i pacchetti e librerie del framework possono essere utilizzate. Ad esempio, non supporta xamarin c# `dynamic` (parola chiave), pertanto, una libreria di classi portabile non è possibile utilizzare qualsiasi pacchetto che dipende dal codice dinamico, anche se tale codice funzionerà in Android. Visual Studio per Mac impedirà di aggiunta di riferimenti e i pacchetti non compatibili, ma è opportuno limitazioni da considerare per evitare sorprese in un secondo momento.

Nota: Si noterà che i progetti di fare riferimento alle librerie di framework che non è stato utilizzato. Questi riferimenti sono inclusi come parte dei modelli di progetto Xamarin. Quando le applicazioni vengono compilate, il processo di collegamento rimuoverà codice privo di riferimento, pertanto anche se `System.Xml` è stato fatto riferimento, non essere incluso nell'applicazione finale perché non si sono utilizzando le funzioni di Xml.

 <a name="Data_Layer_(DL)" />

### <a name="data-layer-dl"></a>Livello dati (DL)

Il livello dati contiene il codice che esegue l'archiviazione fisica di dati, per un database, file flat o un altro meccanismo. Il livello dati Tasky è costituito da due parti: la libreria di rete di SQLite e il codice personalizzato aggiunto al collegarla.

Tasky si basa sul pacchetto nuget Sqlite net (pubblicato da Frank Kreuger) per incorporare il codice di SQLite NET che fornisce un'interfaccia di database relazionale a oggetti Mapping ORM (). Il `TaskItemDatabase` classe eredita da `SQLiteConnection` e aggiunge i necessari creazione, lettura, aggiornamento, metodi di eliminazione (CRUD) per leggere e scrivere dati in SQLite. È un'implementazione semplice boilerplate dei metodi CRUD generici che potrebbero essere riutilizzate in altri progetti.

Il `TaskItemDatabase` è un singleton, assicurando che tutti gli accessi si verifica con la stessa istanza. Un blocco viene utilizzato per impedire l'accesso simultaneo da più thread.

 <a name="SQLite_on_WIndows_Phone" />

#### <a name="sqlite-on-windows-phone"></a>SQLite in Windows Phone

IOS e Android entrambe fornite con SQLite come parte del sistema operativo, Windows Phone non include un motore di database compatibili. Per condividere il codice tra tutte e tre le piattaforme una versione di Windows phone nativo di SQLite è obbligatoria. Vedere [utilizzo di un Database locale](~/xamarin-forms/app-fundamentals/databases.md) per ulteriori informazioni sull'impostazione di progetto Windows Phone di Sqlite.

 <a name="Using_an_Interface_to_Generalize_Data_Access" />

#### <a name="using-an-interface-to-generalize-data-access"></a>Utilizzando un'interfaccia per generalizzare l'accesso ai dati

Il livello di dati assume una dipendenza dal `BL.Contracts.IBusinessIdentity` in modo che può implementare i metodi di accesso dati astratto che richiedono una chiave primaria. Qualsiasi classe di livello aziendale che implementa l'interfaccia può essere reso persistente nel livello dati.

L'interfaccia specifica solo una proprietà integer come chiave primaria:

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

La classe base implementa l'interfaccia e aggiunge gli attributi di SQLite NET per contrassegnarla come una chiave primaria con incremento automatico. Qualsiasi classe di livello aziendale che implementa questa classe di base può essere reso persistente nel livello dati:

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

Si tratta di un esempio di metodi generici nel livello dati che utilizzano l'interfaccia `GetItem<T>` metodo:

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

A [blocco](http://msdn.microsoft.com/library/c5kehkcz(v=vs.100).aspx) viene implementato all'interno di `TaskItemDatabase` classe per impedire l'accesso simultaneo al database. Questo modo si garantisce l'accesso simultaneo da thread diversi viene serializzato (in caso contrario un componente dell'interfaccia utente potrebbe tentare di leggere il database nello stesso momento aggiornamento da un thread in background). In seguito è riportato un esempio della modalità di implementazione del blocco:

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

La maggior parte del codice di livello dati potrebbero essere riutilizzata in altri progetti. Il codice specifico dell'applicazione solo nel livello di `CreateTable<TaskItem>` chiamare nel `TaskItemDatabase` costruttore.

 <a name="Data_Access_Layer_(DAL)" />

### <a name="data-access-layer-dal"></a>Livello di accesso ai dati (DAL)

Il `TaskItemRepository` classe incapsula il meccanismo di archiviazione di dati con un'API fortemente tipizzato che consente `TaskItem` oggetti da creare, eliminare, recuperato e aggiornato.

 <a name="Using_Conditional_Compilation" />

#### <a name="using-conditional-compilation"></a>Utilizzo della compilazione condizionale

La classe viene utilizzata la compilazione condizionale per impostare il percorso del file, in un esempio di implementazione di divergenza di piattaforma. La proprietà che restituisce il percorso viene compilato in codice diverso in ogni piattaforma. Il codice e le direttive del compilatore specifiche della piattaforma sono illustrate di seguito:

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

### <a name="business-layer-bl"></a>Livello Business (BL)

Il livello Business implementa le classi di modello e di facciata per gestirli.
In Tasky è il modello di `TaskItem` classe e `TaskItemManager` implementa il pattern di facciata per fornire un'API per la gestione `TaskItems`.

 <a name="Façade" />

#### <a name="faade"></a>Facciata

 `TaskItemManager` esegue il wrapping di `DAL.TaskItemRepository` per fornire Get, salvare ed eliminare i metodi che verranno fatto riferimento dall'applicazione e i livelli dell'interfaccia utente.

Se necessario, ad esempio le regole di convalida che devono essere soddisfatti prima di salvata un oggetto, verrà inseriti qui la logica e le regole di business.

 <a name="API_for_Platform-Specific_Code" />

### <a name="api-for-platform-specific-code"></a>API per il codice specifico della piattaforma

Dopo il codice comune è stata scritta, l'interfaccia utente deve essere compilato per raccogliere e visualizzare i dati esposti da esso. La `TaskItemManager` classe implementa il pattern di facciata per fornire una semplice API per il codice dell'applicazione di accedere.

Il codice scritto in ogni progetto specifico della piattaforma verranno in genere essere strettamente al SDK native del dispositivo e accedere solo il codice comune tramite l'API definita dal `TaskItemManager`. Questo include i metodi e business le classi espone, ad esempio `TaskItem`.

Le immagini non vengono condivise tra le piattaforme ma aggiunto in modo indipendente per ogni progetto. Questo è importante perché ogni piattaforma gestisce in modo diverso, le immagini mediante le risoluzioni, directory e nomi di file diversi.

Le sezioni rimanenti discutere i dettagli di implementazione specifica della piattaforma dell'interfaccia utente Tasky.

 <a name="iOS_App" />

## <a name="ios-app"></a>App iOS

Sono disponibili solo un numero limitato di classi necessarie per implementare iOS Tasky applicazione utilizzando il progetto PCL comune per archiviare e recuperare dati. Il progetto xamarin iOS completo è illustrato di seguito:

 ![](case-study-tasky-images/taskyios-solution.png "progetto iOS è illustrato di seguito")

Le classi vengono illustrate in questo diagramma, raggruppato in livelli.

 [![](case-study-tasky-images/classdiagram-android.png "Le classi vengono illustrate in questo diagramma, raggruppato in livelli")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>Riferimenti

L'app iOS fa riferimento a librerie di SDK specifiche della piattaforma: ad esempio. Xamarin. IOS e MonoTouch.Dialog-1.

È necessario inoltre fare riferimento il `TaskyPortableLibrary` progetto PCL.
L'elenco di riferimenti è illustrato di seguito:

 ![](case-study-tasky-images/taskyios-references.png "L'elenco di riferimenti è illustrato di seguito")

Il livello di applicazione e il livello dell'interfaccia utente vengono implementati nel progetto utilizzando tali riferimenti.

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Livello di applicazione (AL)

Il livello di applicazione contiene classi specifiche della piattaforma necessarie per associare gli oggetti esposti dalla libreria di classi Portabile per l'interfaccia utente. L'applicazione iOS specifiche presenta due classi consentono di visualizzare le attività:

 -   **EditingSource** : questa classe viene utilizzata per associare gli elenchi di attività per l'interfaccia utente. Poiché `MonoTouch.Dialog` è stata utilizzata per l'elenco di attività, è necessario implementare questo supporto per abilitare funzionalità scorrere per eliminare il `UITableView` . Scorrere rapidamente per eliminare è comune in iOS, ma non Android o Windows Phone, pertanto il progetto specifico di iOS è l'unico che lo implementa.
 -   **TaskDialog** : questa classe viene utilizzata per associare una singola attività nell'interfaccia utente. Usa il `MonoTouch.Dialog` API di Reflection a 'wrap' il `TaskItem` oggetto con una classe che contiene gli attributi corretti per consentire la schermata di input essere formattato correttamente.

Il `TaskDialog` utilizzato dalla classe `MonoTouch.Dialog` attributi per creare una schermata in base alle proprietà di una classe. La classe è simile al seguente:

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

Si noti il `OnTap` attributi richiedano un nome di metodo: questi metodi devono essere presente nella classe in cui il `MonoTouch.Dialog.BindingContext` viene creato (in questo caso, la `HomeScreen` classe descritti nella sezione successiva).

 <a name="User_Interface_Layer_(UI)" />

### <a name="user-interface-layer-ui"></a>Livello dell'interfaccia utente (UI)

Il Layer dell'interfaccia utente include le classi seguenti:

1.   **AppDelegate** – contiene le chiamate all'API di aspetto per definire lo stile di carattere e colori usati nell'applicazione. Tasky è una semplice applicazione pertanto vi sono in esecuzione altre attività inizializzazione `FinishedLaunching` .
2.   **Schermate** : le sottoclassi di `UIViewController` che definiscono ogni schermata e il relativo comportamento. Schermate di unire l'interfaccia utente con le classi di livello applicazione e l'API comune ( `TaskItemManager` ). In questo esempio le schermate vengono create nel codice, ma Impossibile sono stati progettati utilizzando Generatore di Xcode interfaccia o la finestra di progettazione dello storyboard.
3.   **Immagini** : gli elementi visivi sono una parte importante di ogni applicazione. Tasky include iniziale dello schermo e l'icona di immagini, che, per iOS, è necessario specificare in regolare e la risoluzione della Retina.

 <a name="Home_Screen" />

#### <a name="home-screen"></a>Schermata iniziale

La schermata iniziale è un `MonoTouch.Dialog` viene visualizzato un elenco di attività dal database di SQLite. Eredita da `DialogViewController` e implementa il codice per impostare il `Root` per contenere una raccolta di `TaskItem` oggetti da visualizzare.

 [![](case-study-tasky-images/ios-taskylist.png "Eredita da DialogViewController e implementa il codice per impostare la radice per contenere una raccolta di oggetti TaskItem per la visualizzazione")](case-study-tasky-images/ios-taskylist.png#lightbox)

I due metodi principali relativi alla visualizzazione e l'interazione con l'elenco di attività sono:

1.   **PopulateTable** : utilizza il livello di Business `TaskManager.GetTasks` metodo per recuperare una raccolta di `TaskItem` oggetti da visualizzare.
2.   **Selezionato** : quando una riga viene eseguita l'attività verrà visualizzata in una nuova schermata.

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>Schermata dei dettagli attività

Dettagli dell'attività è una schermata di input che consente di modificare o eliminare attività.

Usa tasky `MonoTouch.Dialog`dell'API di Reflection per visualizzare la schermata, pertanto non sia presente alcun `UIViewController` implementazione. Al contrario, il `HomeScreen` crea un'istanza di classe e viene visualizzato un `DialogViewController` utilizzando il `TaskDialog` classe dal livello dell'applicazione.

Questa schermata mostra una schermata vuota che illustra il `Entry` impostazione testo della filigrana attributi di **nome** e **note** campi:

 [![](case-study-tasky-images/ios-taskydetail.png "Questa schermata mostra una schermata vuota che illustra l'attributo voce impostando il testo della filigrana nei campi nome e le note")](case-study-tasky-images/ios-taskydetail.png#lightbox)

La funzionalità del **dettagli attività** schermata (ad esempio, salvare o eliminare un'attività) deve essere implementato nel `HomeScreen` classe, poiché si tratta di dove il `MonoTouch.Dialog.BindingContext` viene creato. Le operazioni seguenti `HomeScreen` metodi supportano schermata dei dettagli di attività:

1.   **ShowTaskDetails** : consente di creare un `MonoTouch.Dialog.BindingContext` per eseguire il rendering di una schermata. Crea la schermata di input utilizzando la reflection per recuperare i nomi delle proprietà e i tipi dalla `TaskDialog` classe. Informazioni aggiuntive, ad esempio il testo della filigrana per le caselle di input, viene implementate con gli attributi delle proprietà.
2.   **SaveTask** : questo metodo fa riferimento il `TaskDialog` classe tramite un `OnTap` attributo. Viene chiamato quando **salvare** viene premuto e Usa un `MonoTouch.Dialog.BindingContext` per recuperare i dati immessi dall'utente prima di salvare le modifiche utilizzando `TaskItemManager` .
3.   **DeleteTask** : questo metodo fa riferimento il `TaskDialog` classe tramite un `OnTap` attributo. Usa `TaskItemManager` per eliminare i dati utilizzando la chiave primaria (proprietà ID).

 <a name="Android_App" />

## <a name="android-app"></a>App Android

Il progetto xamarin completo è illustrato di seguito:

 ![](case-study-tasky-images/taskyandroid-solution.png "Progetto Android è raffigurato qui")

Il diagramma classi con classi raggruppati per livello:

 [![](case-study-tasky-images/classdiagram-android.png "Diagramma classi con classi raggruppati per livello")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>Riferimenti

Il progetto di app Android deve fare riferimento l'assembly xamarin specifico della piattaforma per accedere alle classi da Android SDK.

È necessario anche fare riferimento a progetto PCL (ad es. TaskyPortableLibrary) per accedere al codice di livello dati e business comune.

 ![](case-study-tasky-images/taskyandroid-references.png "TaskyPortableLibrary per accedere al codice di livello dati e business comune")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Livello di applicazione (AL)

Simile alla versione di iOS che è stato esaminato in precedenza, il livello di applicazione nella versione di Android contiene classi specifiche della piattaforma necessarie per associare gli oggetti esposti dagli elementi di base per l'interfaccia utente.

 **TaskListAdapter** : consente di visualizzare un elenco<T> degli oggetti è necessario implementare un adattatore per visualizzare gli oggetti personalizzati in un `ListView`. L'adapter controlla il layout viene utilizzato per ogni elemento nell'elenco: in questo caso il codice utilizza un layout predefinito Android `SimpleListItemChecked`.

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>Interfaccia utente (UI)

Livello dell'interfaccia utente dell'app Android è una combinazione di codice e markup XML.

 -   **Risorse/Layout** – layout dello schermo e la riga di celle implementati come file AXML di progettazione. Il AXML può essere scritto manualmente o disposto in modo visivo mediante la finestra di progettazione dell'interfaccia utente Xamarin per Android.
 -   **Risorse/Drawable** – immagini (icone) e un pulsante personalizzato.
 -   **Schermate** – sottoclassi di attività che definiscono ogni schermata e il relativo comportamento. Collega l'interfaccia utente con le classi di livello applicazione e l'API comune (`TaskItemManager`).

 <a name="Home_Screen" />

#### <a name="home-screen"></a>Schermata iniziale

La schermata iniziale è costituito da una sottoclasse di Activity `HomeScreen` e `HomeScreen.axml` file che definisce il layout (posizione dell'elenco attività e pulsante). La schermata è simile al seguente:

 [![](case-study-tasky-images/android-taskylist.png "La schermata è simile al seguente")](case-study-tasky-images/android-taskylist.png#lightbox)

Il codice della schermata iniziale definisce i gestori per fare clic sul pulsante e facendo clic sugli elementi nell'elenco, nonché popolamento dell'elenco nel `OnResume` metodo (in modo che riflette le modifiche apportate nella schermata dei dettagli attività). Dati vengono caricati utilizzando il livello di Business `TaskItemManager` e `TaskListAdapter` dal livello dell'applicazione.

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>Schermata dei dettagli attività

Schermata dei dettagli attività anche costituito da un `Activity` sottoclasse e un file di layout AXML. Il layout determina la posizione dei controlli di input e la classe c# definisce il comportamento per caricare e salvare `TaskItem` oggetti.

 [![](case-study-tasky-images/android-taskydetail.png "La classe definisce il comportamento per caricare e salvare gli oggetti TaskItem")](case-study-tasky-images/android-taskydetail.png#lightbox)

Tutti i riferimenti alla libreria PCL vengono effettuate tramite il `TaskItemManager` classe.

 <a name="Windows_Phone_App" />

## <a name="windows-phone-app"></a>App di Windows Phone
Il progetto Windows Phone completo:

 ![](case-study-tasky-images/taskywp7-solution.png "App di Windows Phone il progetto Windows Phone completo")

Nel diagramma seguente vengono visualizzate le classi raggruppate in livelli:

 [![](case-study-tasky-images/classdiagram-wp7.png "Questo diagramma Visualizza le classi raggruppate in livelli")](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References" />

### <a name="references"></a>Riferimenti

Il progetto specifico della piattaforma deve fare riferimento a librerie richieste specifiche della piattaforma (ad esempio `Microsoft.Phone` e `System.Windows`) per creare un'applicazione Windows Phone valida.

È necessario anche fare riferimento a progetto PCL (ad es. `TaskyPortableLibrary`) per utilizzare il `TaskItem` database e classi.

 ![](case-study-tasky-images/taskywp7-references.png "TaskyPortableLibrary di utilizzare la classe TaskItem e il database")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Livello di applicazione (AL)

Nuovamente, con le versioni Android e iOS, il livello di applicazione costituita dagli elementi non visive che consentono di associare i dati per l'interfaccia utente.

 <a name="ViewModels" />

#### <a name="viewmodels"></a>ViewModel

ViewModel di eseguire il wrapping dei dati da una libreria di classi Portabile ( `TaskItemManager`) e le visualizza in modo che può essere utilizzata dall'associazione di dati XAML Silverlight. Questo è un esempio di comportamento specifico della piattaforma (come descritto nel documento di applicazioni multipiattaforma).

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>Interfaccia utente (UI)

XAML è una funzionalità di associazione di dati univoca che può essere dichiarata nel markup e ridurre la quantità di codice necessario per visualizzare gli oggetti:

1.   **Pagine** : file XAML e il codebehind definire l'interfaccia utente e fa riferimento il ViewModel e il progetto libreria di classi Portabile per visualizzare e raccogliere i dati.
2.   **Immagini** – immagini della schermata, sfondo e icona iniziale sono una componente fondamentale dell'interfaccia utente.

 <a name="MainPage" />

#### <a name="mainpage"></a>MainPage

La classe utilizza MainPage il `TaskListViewModel` per visualizzare i dati di utilizzo delle funzionalità di associazione XAML. La pagina `DataContext` è impostato per il modello di visualizzazione, che viene compilato in modo asincrono. Il `{Binding}` sintassi nel codice XAML determina la modalità di visualizzazione dei dati.

 <a name="TaskDetailsPage" />

#### <a name="taskdetailspage"></a>TaskDetailsPage

Ogni attività viene visualizzata associando il `TaskViewModel` a XAML definito nel TaskDetailsPage.xaml. I dati di attività vengono recuperati tramite il `TaskItemManager` nel livello Business.

 <a name="Results" />

## <a name="results"></a>Risultati

Le applicazioni risultante è simile alla seguente in ogni piattaforma:

 <a name="iOS" />

#### <a name="ios"></a>iOS

L'applicazione utilizza Progettazione dell'interfaccia utente di iOS-standard, ad esempio il pulsante 'Aggiungi' viene posizionato nella barra di spostamento e l'utilizzo predefinito **più (+)** icona. Viene inoltre utilizzato il valore predefinito `UINavigationController` pulsante "Indietro" comportamento e supporta 'scorrere per delete' nella tabella.

 [![](case-study-tasky-images/ios-taskylist.png "Inoltre utilizza le impostazioni predefinite UINavigationController pulsante Indietro e supporta scorrere per eliminare la tabella") ](case-study-tasky-images/ios-taskylist.png#lightbox) [ ![ ] (case-study-tasky-images/ios-taskylist.png "utilizza anche il valore predefinito UINavigationController il comportamento di un pulsante e supporta scorrere per eliminare la tabella")](case-study-tasky-images/ios-taskylist.png#lightbox)

 <a name="Android" />

#### <a name="android"></a>Android

L'app Android utilizza controlli incorporati, incluso il layout predefinito per le righe che richiedono un tick visualizzato. Il comportamento di hardware e del sistema back-è supportato oltre a un pulsante Indietro su schermo.

 [![](case-study-tasky-images/android-taskylist.png "È supportato il comportamento di back-/ sistema hardware oltre a un pulsante Indietro su schermo")](case-study-tasky-images/android-taskylist.png#lightbox)[![](case-study-tasky-images/android-taskylist.png "è supportato il comportamento di back-/ sistema hardware oltre a sullo schermo pulsante Indietro")](case-study-tasky-images/android-taskylist.png#lightbox)

 <a name="Windows_Phone" />

#### <a name="windows-phone"></a>Windows Phone

L'app di Windows Phone utilizza il layout standard, la compilazione sulla barra dell'app nella parte inferiore dello schermo anziché una barra di navigazione nella parte superiore.

 [![](case-study-tasky-images/wp-taskylist.png "L'app di Windows Phone utilizza il layout standard, la barra dell'app nella parte inferiore dello schermo anziché una barra di navigazione nella parte superiore di compilazione") ](case-study-tasky-images/wp-taskylist.png#lightbox) [ ![ ] (case-study-tasky-images/wp-taskylist.png "app di Windows Phone utilizza lo standard layout, la barra dell'app nella parte inferiore dello schermo anziché una barra di navigazione nella parte superiore di compilazione")](case-study-tasky-images/wp-taskylist.png#lightbox)

 <a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo documento ha fornito una spiegazione dettagliata di come i principi di progettazione di applicazioni a più livelli applicati a un'applicazione semplice per facilitare il riutilizzo del codice tra le tre piattaforme mobili: iOS, Android e Windows Phone.

È descritto il processo utilizzato per progettare i livelli dell'applicazione e il tipo di codice illustrati &amp; funzionalità è stata implementata in ogni livello.

Il codice può essere scaricato da [github](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable).

## <a name="related-links"></a>Collegamenti correlati

- [Compilazione di applicazioni multipiattaforma (documento principale)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [App di esempio portabile tasky (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
