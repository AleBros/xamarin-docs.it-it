---
title: 'Case study sulle app multipiattaforma: Tasky'
description: Questo documento descrive il modo in cui l'applicazione di esempio Tasky Portable è stata progettata e creata come applicazione per dispositivi mobili multipiattaforma. Vengono illustrati i requisiti dell'app, l'interfaccia, il modello di dati, la funzionalità di base, l'implementazione e altro ancora.
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 71d5ed3512980086d244acc5a604d7b33a5dd77c
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571350"
---
# <a name="cross-platform-app-case-study-tasky"></a>Case study sulle app multipiattaforma: Tasky

*Tasky* *Portable* è una semplice applicazione to-do list. Questo documento illustra il modo in cui è stato progettato e creato seguendo le istruzioni del documento sulla [compilazione di applicazioni multipiattaforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) . Nella discussione vengono illustrate le aree seguenti:

<a name="Design_Process"></a>

## <a name="design-process"></a>Processo di progettazione

È consigliabile creare una mappa stradale per gli elementi che si desidera ottenere prima di iniziare a scrivere codice. Ciò vale soprattutto per lo sviluppo multipiattaforma, in cui si stanno compilando funzionalità che verranno esposte in vari modi. Partendo da un'idea chiara di ciò che si sta compilando Risparmia tempo e fatica più avanti nel ciclo di sviluppo.

 <a name="Requirements"></a>

### <a name="requirements"></a>Requisiti

Il primo passaggio nella progettazione di un'applicazione consiste nell'identificare le funzionalità desiderate. Questi possono essere obiettivi di alto livello o casi di utilizzo dettagliati. Tasky presenta requisiti funzionali semplici:

- Visualizzare un elenco di attività
- Aggiungere, modificare ed eliminare attività
- Imposta lo stato di un'attività su' done '

È necessario considerare l'utilizzo di funzionalità specifiche della piattaforma.  È possibile sfruttare i vantaggi della geoschermatura di iOS o Windows Phone riquadri animati? Anche se non si usano le funzionalità specifiche della piattaforma nella prima versione, è consigliabile pianificare in anticipo per assicurarsi che i livelli di dati aziendali & siano in grado di adattarsi a tali funzionalità.

 <a name="User_Interface_Design"></a>

### <a name="user-interface-design"></a>Progettazione dell'interfaccia utente

Inizia con una progettazione di alto livello che può essere implementata tra le piattaforme di destinazione. Prendere in considerazione i vincoli dell'interfaccia utente della piattaforma specifico. Ad esempio, un `TabBarController` in iOS può visualizzare più di cinque pulsanti, mentre il Windows Phone equivalente può essere visualizzato fino a quattro.
Per creare il flusso dello schermo, usare lo strumento di propria scelta (Paper Works).

 [![](case-study-tasky-images/taskydesign.png "Draw the screen-flow using the tool of your choice paper works")](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model"></a>

### <a name="data-model"></a>Modello di dati

Conoscere quali dati devono essere archiviati consente di determinare il meccanismo di persistenza da usare. Per informazioni sui meccanismi di archiviazione disponibili e su come decidere tra di essi, vedere [accesso ai dati multipiattaforma](~/cross-platform/app-fundamentals/index.md) . Per questo progetto, si userà SQLite.NET.

L'attività deve archiviare tre proprietà per ogni ' TaskItem ':

- **Name** – String
- **Note** -stringa
- **Completato** : booleano

 <a name="Core_Functionality"></a>

### <a name="core-functionality"></a>Funzionalità principali

Si consideri l'API che l'interfaccia utente dovrà utilizzare per soddisfare i requisiti. Un elenco attività richiede le funzioni seguenti:

- **Elencare tutte le attività** : per visualizzare l'elenco principale della schermata di tutte le attività disponibili
- **Ottenere un'attività** : quando viene toccata una riga attività
- **Salva un'attività** : quando un'attività viene modificata
- **Eliminare un'attività** : quando un'attività viene eliminata
- **Crea attività vuota** : quando viene creata una nuova attività

Per ottenere il riutilizzo del codice, questa API deve essere implementata una sola volta nella *libreria di classi*portabile.

 <a name="Implementation"></a>

### <a name="implementation"></a>Implementazione

Una volta concordata la progettazione dell'applicazione, considerare come può essere implementata come applicazione multipiattaforma. Questa diventerà l'architettura dell'applicazione. Seguendo le istruzioni riportate nel documento [compilazione di applicazioni multipiattaforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) , il codice dell'applicazione deve essere suddiviso nelle parti seguenti:

- **Codice comune** : un progetto comune che contiene codice riutilizzabile per archiviare i dati dell'attività. esporre una classe modello e un'API per gestire il salvataggio e il caricamento dei dati.
- **Codice specifico della piattaforma** : progetti specifici della piattaforma che implementano un'interfaccia utente nativa per ogni sistema operativo, usando il codice comune come ' back-end '.

[![](case-study-tasky-images/taskypro-architecture.png "Platform-specific projects implement a native UI for each operating system, utilizing the common code as the back end")](case-study-tasky-images/taskypro-architecture.png#lightbox)

Queste due parti sono descritte nelle sezioni riportate di seguito.

 <a name="Common_(PCL)_Code"></a>

## <a name="common-pcl-code"></a>Codice comune (PCL)

Tasky Portable usa la strategia di libreria di classi portabile per la condivisione di codice comune. Per una descrizione delle opzioni di condivisione del codice, vedere il documento relativo alle [Opzioni di condivisione del codice](~/cross-platform/app-fundamentals/code-sharing.md) .

Tutto il codice comune, inclusi il livello di accesso ai dati, il codice del database e i contratti, viene inserito nel progetto di libreria.

Il progetto PCL completo è illustrato di seguito. Tutto il codice nella libreria portabile è compatibile con ogni piattaforma di destinazione. Quando viene distribuita, ogni app nativa fa riferimento a tale libreria.

![](case-study-tasky-images/portable-project.png "When deployed, each native app will reference that library")

Il diagramma classi seguente mostra le classi raggruppate per livello. La `SQLiteConnection` classe è un codice standard del pacchetto sqlite-net. Le altre classi sono codice personalizzato per Tasky. Le `TaskItemManager` `TaskItem` classi e rappresentano l'API esposta alle applicazioni specifiche della piattaforma.

 [![](case-study-tasky-images/classdiagram-core.png "The TaskItemManager and TaskItem classes represent the API that is exposed to the platform-specific applications")](case-study-tasky-images/classdiagram-core.png#lightbox)

L'utilizzo di spazi dei nomi per separare i livelli consente di gestire i riferimenti tra ogni livello. È necessario che i progetti specifici della piattaforma includano solo un' `using` istruzione per il livello business. Il livello di accesso ai dati e il livello dati devono essere incapsulati dall'API esposta da `TaskItemManager` nel livello business.

 <a name="References"></a>

### <a name="references"></a>Riferimenti

Le librerie di classi portabili devono essere utilizzabili su più piattaforme, ognuna con diversi livelli di supporto per le funzionalità della piattaforma e del Framework. Per questo motivo, esistono alcune limitazioni per i pacchetti e le librerie di Framework che è possibile usare. Ad esempio, Novell. iOS non supporta la `dynamic` parola chiave c#, pertanto una libreria di classi portabile non può usare alcun pacchetto che dipende dal codice dinamico, anche se tale codice funziona in Android. Visual Studio per Mac impedisce di aggiungere i pacchetti e i riferimenti incompatibili, ma è opportuno tenere presenti le limitazioni per evitare le sorprese in un secondo momento.

Nota: si noterà che i progetti fanno riferimento a librerie di Framework che non sono state usate. Questi riferimenti sono inclusi come parte dei modelli di progetto Novell. Quando le app vengono compilate, il processo di collegamento rimuoverà il codice senza riferimenti, quindi anche se `System.Xml` è stato fatto riferimento a esso, non verrà incluso nell'applicazione finale perché non vengono utilizzate funzioni XML.

 <a name="Data_Layer_(DL)"></a>

### <a name="data-layer-dl"></a>Livello dati (DL)

Il livello dati contiene il codice che esegue l'archiviazione fisica dei dati, sia che si tratti di un database, file flat o altro meccanismo. Il livello dati Tasky è costituito da due parti: la libreria SQLite-NET e il codice personalizzato aggiunto per il collegamento.

Tasky si basa sul pacchetto sqlite-net NuGet (pubblicato da Frank Kreuger) per incorporare il codice SQLite-NET che fornisce un'interfaccia di database ORM (Object-Relational Mapping). La `TaskItemDatabase` classe eredita da `SQLiteConnection` e aggiunge i metodi di creazione, lettura, aggiornamento ed eliminazione (CRUD) necessari per la lettura e la scrittura dei dati in SQLite. Si tratta di un'implementazione standard semplice di metodi CRUD generici che possono essere riutilizzati in altri progetti.

`TaskItemDatabase`È un singleton, assicurando che tutti gli accessi vengano eseguiti sulla stessa istanza. Viene utilizzato un blocco per impedire l'accesso simultaneo da più thread.

 <a name="SQLite_on_WIndows_Phone"></a>

#### <a name="sqlite-on-windows-phone"></a>SQLite in Windows Phone

Sebbene iOS e Android siano entrambi forniti con SQLite come parte del sistema operativo, Windows Phone non include un motore di database compatibile. Per condividere il codice tra tutte e tre le piattaforme, è richiesta una versione nativa di SQLite di Windows Phone. Per altre informazioni sulla configurazione del progetto Windows Phone per SQLite, vedere [uso di un database locale](~/xamarin-forms/data-cloud/data/databases.md) .

 <a name="Using_an_Interface_to_Generalize_Data_Access"></a>

#### <a name="using-an-interface-to-generalize-data-access"></a>Uso di un'interfaccia per generalizzare l'accesso ai dati

Il livello dati prende una dipendenza da in `BL.Contracts.IBusinessIdentity` modo che possa implementare metodi di accesso ai dati astratti che richiedono una chiave primaria. Qualsiasi classe di livello business che implementi l'interfaccia può quindi essere resa permanente a livello di dati.

L'interfaccia specifica solo una proprietà integer che funge da chiave primaria:

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

La classe base implementa l'interfaccia e aggiunge gli attributi SQLite-NET per contrassegnarla come una chiave primaria con incremento automatico. Qualsiasi classe del livello business che implementa questa classe di base può quindi essere resa permanente nel livello dati:

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

Un esempio dei metodi generici nel livello dati che usano l'interfaccia è questo `GetItem<T>` Metodo:

```csharp
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

 <a name="Locking_to_prevent_Concurrent_Access"></a>

#### <a name="locking-to-prevent-concurrent-access"></a>Blocco per impedire l'accesso simultaneo

Un [blocco](https://msdn.microsoft.com/library/c5kehkcz(v=vs.100).aspx) viene implementato all'interno della `TaskItemDatabase` classe per impedire l'accesso simultaneo al database. Ciò consente di garantire che l'accesso simultaneo da thread diversi venga serializzato. in caso contrario, un componente dell'interfaccia utente potrebbe tentare di leggere il database nello stesso momento in cui è stato aggiornato da un thread in background. Di seguito è riportato un esempio di come viene implementato il blocco:

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

La maggior parte del codice del livello dati può essere riutilizzata in altri progetti. L'unico codice specifico dell'applicazione nel livello è la `CreateTable<TaskItem>` chiamata nel `TaskItemDatabase` costruttore.

 <a name="Data_Access_Layer_(DAL)"></a>

### <a name="data-access-layer-dal"></a>Livello di accesso ai dati (DAL)

La `TaskItemRepository` classe incapsula il meccanismo di archiviazione dei dati con un'API fortemente tipizzata che consente `TaskItem` di creare, eliminare, recuperare e aggiornare oggetti.

 <a name="Using_Conditional_Compilation"></a>

#### <a name="using-conditional-compilation"></a>Uso della compilazione condizionale

La classe usa la compilazione condizionale per impostare il percorso del file. si tratta di un esempio di implementazione della divergenza della piattaforma. La proprietà che restituisce il percorso viene compilata in codice diverso in ogni piattaforma. Le direttive del compilatore specifiche della piattaforma e del codice sono illustrate di seguito:

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

A seconda della piattaforma, l'output sarà " <app
path> /Library/TaskDB.db3" per iOS, " <app
path> /Documents/TaskDB.db3" per Android o solo "TaskDB. db3" per Windows Phone.

### <a name="business-layer-bl"></a>Livello aziendale (BL)

Il livello business implementa le classi del modello e una facciata per gestirle.
In Tasky il modello è la `TaskItem` classe e `TaskItemManager` implementa il modello di facciata per fornire un'API per la gestione di `TaskItems` .

 <a name="Façade"></a>

#### <a name="faade"></a>Facciata

 `TaskItemManager`esegue il wrapping `DAL.TaskItemRepository` di per fornire i metodi get, Save e Delete a cui i livelli dell'applicazione e dell'interfaccia utente faranno riferimento.

Le regole di business e la logica vengono inserite qui se necessario, ad esempio le regole di convalida che devono essere soddisfatte prima che un oggetto venga salvato.

 <a name="API_for_Platform-Specific_Code"></a>

### <a name="api-for-platform-specific-code"></a>API per codice specifico della piattaforma

Una volta scritto il codice comune, è necessario compilare l'interfaccia utente per raccogliere e visualizzare i dati esposti. La `TaskItemManager` classe implementa il modello di facciata per fornire un'API semplice per l'accesso al codice dell'applicazione.

Il codice scritto in ogni progetto specifico della piattaforma sarà in genere strettamente associato all'SDK nativo del dispositivo e accederà solo al codice comune usando l'API definita da `TaskItemManager` . Sono inclusi i metodi e le classi aziendali esposte, ad esempio `TaskItem` .

Le immagini non sono condivise tra le diverse piattaforme, ma sono state aggiunte in modo indipendente a ogni progetto. Questo aspetto è importante perché ogni piattaforma gestisce le immagini in modo diverso, usando nomi file diversi, directory e risoluzioni.

Le sezioni rimanenti illustrano i dettagli di implementazione specifici della piattaforma dell'interfaccia utente di Tasky.

 <a name="iOS_App"></a>

## <a name="ios-app"></a>App iOS

Per implementare l'applicazione di attività iOS usando il progetto PCL comune per archiviare e recuperare i dati, sono necessarie solo alcune classi. Il progetto iOS Novell. iOS completo è illustrato di seguito:

 ![](case-study-tasky-images/taskyios-solution.png "iOS project is shown here")

Le classi sono illustrate in questo diagramma, raggruppate in livelli.

 [![](case-study-tasky-images/classdiagram-android.png "The classes are shown in this diagram, grouped into layers")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References"></a>

### <a name="references"></a>Riferimenti

L'app iOS fa riferimento alle librerie SDK specifiche della piattaforma, ad esempio. Novell. iOS e MonoTouch. Dialog-1.

Deve anche fare riferimento al `TaskyPortableLibrary` progetto PCL.
L'elenco riferimenti è illustrato di seguito:

 ![](case-study-tasky-images/taskyios-references.png "The references list is shown here")

Il livello applicazione e il livello interfaccia utente sono implementati in questo progetto usando questi riferimenti.

 <a name="Application_Layer_(AL)"></a>

### <a name="application-layer-al"></a>Livello applicazione (AL)

Il livello dell'applicazione contiene classi specifiche della piattaforma necessarie per associare gli oggetti esposti dalla libreria PCL all'interfaccia utente. L'applicazione specifica per iOS dispone di due classi per facilitare la visualizzazione delle attività:

- **EditingSource** : questa classe viene utilizzata per associare elenchi di attività all'interfaccia utente. Poiché `MonoTouch.Dialog` è stato usato per l'elenco attività, è necessario implementare questo helper per abilitare la funzionalità di scorrimento a eliminazione in `UITableView` . L'operazione di scorrimento rapido è comune in iOS, ma non in Android o Windows Phone, quindi il progetto specifico iOS è l'unico che lo implementa.
- **TaskDialog** : questa classe viene usata per associare una singola attività all'interfaccia utente. Usa l' `MonoTouch.Dialog` API Reflection per eseguire il wrapping dell' `TaskItem` oggetto con una classe che contiene gli attributi corretti per consentire la formattazione corretta della schermata di input.

La `TaskDialog` classe USA `MonoTouch.Dialog` gli attributi per creare una schermata in base alle proprietà di una classe. La classe ha un aspetto simile al seguente:

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

Si noti `OnTap` che gli attributi richiedono un nome di metodo: questi metodi devono esistere nella classe in cui `MonoTouch.Dialog.BindingContext` viene creato l'oggetto (in questo caso, la `HomeScreen` classe descritta nella sezione successiva).

 <a name="User_Interface_Layer_(UI)"></a>

### <a name="user-interface-layer-ui"></a>Livello interfaccia utente (UI)

Il livello dell'interfaccia utente è costituito dalle classi seguenti:

1. **AppDelegate** : contiene le chiamate all'API di aspetto per applicare uno stile ai tipi di carattere e ai colori usati nell'applicazione. Tasky è una semplice applicazione, quindi non sono presenti altre attività di inizializzazione in esecuzione in `FinishedLaunching` .
2. **Schermate** : sottoclassi di `UIViewController` che definiscono ogni schermata e il relativo comportamento. Le schermate combinano l'interfaccia utente con le classi del livello dell'applicazione e l'API comune ( `TaskItemManager` ). In questo esempio le schermate vengono create nel codice, ma possono essere state progettate usando il Interface Builder o la finestra di progettazione storyboard di Xcode.
3. **Immagini** : gli elementi visivi rappresentano una parte importante di ogni applicazione. Per le attività sono disponibili immagini di schermata iniziale e icone, che per iOS devono essere fornite con la risoluzione regolare e retina.

 <a name="Home_Screen"></a>

#### <a name="home-screen"></a>Schermata Home

La schermata iniziale è una `MonoTouch.Dialog` schermata che visualizza un elenco di attività dal database SQLite. Eredita da `DialogViewController` e implementa il codice per impostare l'oggetto in modo da `Root` contenere una raccolta di `TaskItem` oggetti per la visualizzazione.

 [![](case-study-tasky-images/ios-taskylist.png "It inherits from DialogViewController and implements code to set the Root to contain a collection of TaskItem objects for display")](case-study-tasky-images/ios-taskylist.png#lightbox)

I due metodi principali correlati alla visualizzazione e all'interazione con l'elenco attività sono:

1. **PopulateTable** : usa il metodo del livello business `TaskManager.GetTasks` per recuperare una raccolta di `TaskItem` oggetti da visualizzare.
2. **Selezionato** : quando si tocca una riga, Visualizza l'attività in una nuova schermata.

 <a name="Task_Details_Screen"></a>

#### <a name="task-details-screen"></a>Schermata dei dettagli dell'attività

Dettagli attività è una schermata di input che consente di modificare o eliminare le attività.

Tasky USA `MonoTouch.Dialog` l'API Reflection per visualizzare la schermata, quindi non esiste alcuna `UIViewController` implementazione. Al contrario, la `HomeScreen` classe crea un'istanza di e visualizza un oggetto `DialogViewController` utilizzando la `TaskDialog` classe dal livello dell'applicazione.

Questa schermata mostra una schermata vuota che illustra l' `Entry` impostazione dell'attributo per il testo della filigrana nei campi **nome** e **Note** :

 [![](case-study-tasky-images/ios-taskydetail.png "This screenshot shows an empty screen that demonstrates the Entry attribute setting the watermark text in the Name and Notes fields")](case-study-tasky-images/ios-taskydetail.png#lightbox)

La funzionalità della schermata dei **Dettagli delle attività** , ad esempio il salvataggio o l'eliminazione di un'attività, deve essere implementata nella `HomeScreen` classe, perché è la posizione in cui `MonoTouch.Dialog.BindingContext` viene creato. I `HomeScreen` metodi seguenti supportano la schermata dei dettagli dell'attività:

1. **ShowTaskDetails** : crea un oggetto `MonoTouch.Dialog.BindingContext` per il rendering di una schermata. Crea la schermata di input usando la reflection per recuperare i nomi e i tipi di proprietà dalla `TaskDialog` classe. Altre informazioni, ad esempio il testo della filigrana per le caselle di input, vengono implementate con gli attributi delle proprietà.
2. **SaveTask** : viene fatto riferimento a questo metodo nella `TaskDialog` classe tramite un `OnTap` attributo. Viene chiamato quando viene premuto il metodo **Save** e utilizza un `MonoTouch.Dialog.BindingContext` per recuperare i dati immessi dall'utente prima di salvare le modifiche utilizzando `TaskItemManager` .
3. **DeleteTask** : viene fatto riferimento a questo metodo nella `TaskDialog` classe tramite un `OnTap` attributo. USA `TaskItemManager` per eliminare i dati usando la chiave primaria (proprietà ID).

 <a name="Android_App"></a>

## <a name="android-app"></a>App Android

Il progetto Novell. Android completo è illustrato di seguito:

 ![](case-study-tasky-images/taskyandroid-solution.png "Android project is pictured here")

Il diagramma classi, con le classi raggruppate per livello:

 [![](case-study-tasky-images/classdiagram-android.png "The class diagram, with classes grouped by layer")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References"></a>

### <a name="references"></a>Riferimenti

Il progetto di app Android deve fare riferimento all'assembly Novell. Android specifico della piattaforma per accedere alle classi dal Android SDK.

Deve anche fare riferimento al progetto PCL, ad esempio TaskyPortableLibrary) per accedere ai dati comuni e al codice del livello business.

 ![](case-study-tasky-images/taskyandroid-references.png "TaskyPortableLibrary to access the common data and business layer code")

 <a name="Application_Layer_(AL)"></a>

### <a name="application-layer-al"></a>Livello applicazione (AL)

Analogamente alla versione di iOS esaminata in precedenza, il livello dell'applicazione nella versione di Android contiene classi specifiche della piattaforma necessarie per associare gli oggetti esposti dal core all'interfaccia utente.

 **TaskListAdapter** : per visualizzare un elenco \<T> di oggetti, è necessario implementare un adapter per visualizzare oggetti personalizzati in un oggetto `ListView` . L'adapter controlla il layout usato per ogni elemento nell'elenco, in questo caso il codice usa un layout incorporato per Android `SimpleListItemChecked` .

 <a name="User_Interface_(UI)"></a>

### <a name="user-interface-ui"></a>Interfaccia utente

Il livello dell'interfaccia utente dell'app Android è costituito da una combinazione di codice e markup XML.

- **Risorse/layout** : layout dello schermo e progettazione di celle di riga implementati come file AXML. I AXML possono essere scritti manualmente o disposti visivamente con la finestra di progettazione dell'interfaccia utente di Novell per Android.
- **Risorse/disegnatore** : immagini (icone) e pulsante personalizzato.
- **Schermate** : sottoclassi di attività che definiscono ogni schermata e il relativo comportamento. Combina l'interfaccia utente con le classi del livello dell'applicazione e l'API comune ( `TaskItemManager` ).

 <a name="Home_Screen"></a>

#### <a name="home-screen"></a>Schermata Home

La schermata iniziale è costituita da una sottoclasse di attività `HomeScreen` e dal `HomeScreen.axml` file che definisce il layout (posizione del pulsante e dell'elenco attività). La schermata è simile alla seguente:

 [![](case-study-tasky-images/android-taskylist.png "The screen looks like this")](case-study-tasky-images/android-taskylist.png#lightbox)

Il codice della schermata iniziale definisce i gestori per fare clic sul pulsante e fare clic sugli elementi nell'elenco, nonché popolare l'elenco nel `OnResume` Metodo (in modo che rifletta le modifiche apportate nella schermata dei dettagli dell'attività). I dati vengono caricati usando il livello business `TaskItemManager` e il `TaskListAdapter` dal livello dell'applicazione.

 <a name="Task_Details_Screen"></a>

#### <a name="task-details-screen"></a>Schermata dei dettagli dell'attività

La schermata Dettagli attività è costituita anche da una `Activity` sottoclasse e un file di layout AXML. Il layout determina la posizione dei controlli di input e la classe C# definisce il comportamento per il caricamento e il salvataggio `TaskItem` degli oggetti.

 [![](case-study-tasky-images/android-taskydetail.png "The class defines the behavior to load and save TaskItem objects")](case-study-tasky-images/android-taskydetail.png#lightbox)

Tutti i riferimenti alla libreria PCL si trovano attraverso la `TaskItemManager` classe.

 <a name="Windows_Phone_App"></a>

## <a name="windows-phone-app"></a>App Windows Phone
Il progetto di Windows Phone completo:

 ![](case-study-tasky-images/taskywp7-solution.png "Windows Phone App The complete Windows Phone project")

Il diagramma seguente presenta le classi raggruppate in livelli:

 [![](case-study-tasky-images/classdiagram-wp7.png "This diagram presents the classes grouped into layers")](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References"></a>

### <a name="references"></a>Riferimenti

Il progetto specifico della piattaforma deve fare riferimento alle librerie specifiche della piattaforma, ad esempio `Microsoft.Phone` e, `System.Windows` per creare un'applicazione Windows Phone valida.

Deve anche fare riferimento al progetto PCL, ad esempio `TaskyPortableLibrary`) per utilizzare la `TaskItem` classe e il database.

 ![](case-study-tasky-images/taskywp7-references.png "TaskyPortableLibrary to utilize the TaskItem class and database")

 <a name="Application_Layer_(AL)"></a>

### <a name="application-layer-al"></a>Livello applicazione (AL)

Anche in questo caso, come per le versioni iOS e Android, il livello dell'applicazione è costituito dagli elementi non visivi che consentono di associare i dati all'interfaccia utente.

 <a name="ViewModels"></a>

#### <a name="viewmodels"></a>ViewModel

ViewModels esegue il wrapping dei dati dalla libreria di classi `TaskItemManager` portabile () e li presenta in modo che possa essere utilizzato da Silverlight/XAML Data Binding. Questo è un esempio di comportamento specifico della piattaforma, come descritto nel documento relativo alle applicazioni multipiattaforma.

 <a name="User_Interface_(UI)"></a>

### <a name="user-interface-ui"></a>Interfaccia utente

XAML dispone di una funzionalità di data binding univoca che può essere dichiarata nel markup e ridurre la quantità di codice necessario per visualizzare gli oggetti:

1. **Pagine** : i file XAML e i relativi codebehind definiscono l'interfaccia utente e fanno riferimento ai ViewModel e al progetto PCL per visualizzare e raccogliere i dati.
2. **Immagini** : le immagini della schermata iniziale, dello sfondo e delle icone sono una parte essenziale dell'interfaccia utente.

 <a name="MainPage"></a>

#### <a name="mainpage"></a>MainPage

La classe MainPage USA `TaskListViewModel` per visualizzare i dati usando le funzionalità di associazione dati di XAML. La pagina è `DataContext` impostata sul modello di visualizzazione, che viene popolato in modo asincrono. La `{Binding}` sintassi nel codice XAML determina la modalità di visualizzazione dei dati.

 <a name="TaskDetailsPage"></a>

#### <a name="taskdetailspage"></a>TaskDetailsPage

Ogni attività viene visualizzata associando l'oggetto `TaskViewModel` al codice XAML definito in TaskDetailsPage. XAML. I dati dell'attività vengono recuperati tramite il `TaskItemManager` nel livello business.

 <a name="Results"></a>

## <a name="results"></a>Risultati

Le applicazioni risultanti hanno un aspetto simile al seguente in ogni piattaforma:

 <a name="iOS"></a>

### <a name="ios"></a>iOS

L'applicazione usa la progettazione dell'interfaccia utente standard iOS, ad esempio il pulsante "Aggiungi" posizionato nella barra di spostamento e usando l'icona **più (+)** incorporata. USA inoltre il comportamento predefinito del `UINavigationController` pulsante "indietro" e supporta "Swipe-to-delete" nella tabella.

 [![](case-study-tasky-images/ios-taskylist.png "USA anche il comportamento predefinito del pulsante indietro di UINavigationController e supporta l'esecuzione di swipe per eliminare la tabella")](case-study-tasky-images/ios-taskylist.png#lightbox) [![](case-study-tasky-images/ios-taskylist.png "USA anche il comportamento predefinito del pulsante indietro di UINavigationController e supporta l'esecuzione di swipe per eliminare la tabella")](case-study-tasky-images/ios-taskylist.png#lightbox)

 <a name="Android"></a>

### <a name="android"></a>Android

L'app Android usa controlli incorporati, incluso il layout predefinito per le righe che richiedono la visualizzazione di un segno di "segno". Il comportamento hardware/System Back è supportato in aggiunta a un pulsante indietro sullo schermo.

 [![](case-study-tasky-images/android-taskylist.png "The hardware/system back behavior is supported in addition to an on-screen back button")](case-study-tasky-images/android-taskylist.png#lightbox)[![](case-study-tasky-images/android-taskylist.png "The hardware/system back behavior is supported in addition to an on-screen back button")](case-study-tasky-images/android-taskylist.png#lightbox)

 <a name="Windows_Phone"></a>

### <a name="windows-phone"></a>Windows Phone

L'app Windows Phone usa il layout standard, popolando la barra dell'app nella parte inferiore della schermata anziché una barra di navigazione nella parte superiore.

 [![](case-study-tasky-images/wp-taskylist.png "L'app Windows Phone usa il layout standard, popolando la barra dell'app nella parte inferiore della schermata anziché una barra di navigazione nella parte superiore")](case-study-tasky-images/wp-taskylist.png#lightbox) [![](case-study-tasky-images/wp-taskylist.png "L'app Windows Phone usa il layout standard, popolando la barra dell'app nella parte inferiore della schermata anziché una barra di navigazione nella parte superiore")](case-study-tasky-images/wp-taskylist.png#lightbox)

 <a name="Summary"></a>

## <a name="summary"></a>Summary

Questo documento ha fornito una spiegazione dettagliata del modo in cui sono stati applicati i principi della progettazione di applicazioni a più livelli a una semplice applicazione per facilitare il riutilizzo del codice in tre piattaforme mobili: iOS, Android e Windows Phone.

È stato descritto il processo utilizzato per progettare i livelli dell'applicazione e viene illustrata la &amp; funzionalità di codice implementata in ogni livello.

Il codice può essere scaricato da [GitHub](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable).

## <a name="related-links"></a>Collegamenti correlati

- [Compilazione di applicazioni multipiattaforma (documento principale)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [App di esempio Portable per le attività (GitHub)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
