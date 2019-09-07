---
title: Uso di SQLite.NET con Novell. iOS
description: La libreria NuGet SQLite.NET PCL fornisce un semplice meccanismo di accesso ai dati per le app Novell. iOS. In questo documento viene fornita una panoramica dell'utilizzo della libreria.
ms.prod: xamarin
ms.assetid: 79813B09-42D7-47DD-AE71-A605E6B9EF24
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/18/2018
ms.openlocfilehash: 45205859a55974414ee865b2141d2bb127e85bcc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70763137"
---
# <a name="using-sqlitenet-with-xamarinios"></a>Uso di SQLite.NET con Novell. iOS

La libreria SQLite.NET consigliata da Novell è un ORM di base che consente di archiviare e recuperare oggetti nel database SQLite locale in un dispositivo iOS.
ORM sta per il mapping relazionale a oggetti, un'API che consente di salvare e recuperare "oggetti" da un database senza scrivere istruzioni SQL.

<a name="Usage"/>

## <a name="usage"></a>Utilizzo

Per includere la libreria SQLite.NET in un'app Novell, aggiungere il pacchetto NuGet seguente al progetto:

- **Nome pacchetto:** sqlite-net-PCL
- **Autore:** Frank A. Krueger
- **ID:** sqlite-net-pcl
- **URL:** [NuGet.org/packages/sqlite-net-PCL](https://www.nuget.org/packages/sqlite-net-pcl/)

[![Pacchetto NuGet SQLite.NET](using-sqlite-orm-images/image1a-sml.png "Pacchetto NuGet SQLite.NET")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> Sono disponibili numerosi pacchetti SQLite diversi. Assicurarsi di scegliere quello corretto (potrebbe non essere il risultato principale nella ricerca).

Una volta disponibile la libreria SQLite.NET, attenersi ai tre passaggi seguenti per utilizzarla per accedere a un database:

1. **Aggiungere un'istruzione using** : aggiungere l'istruzione seguente ai C# file in cui è necessario l'accesso ai dati:

    ```csharp
    using SQLite;
    ```

1. **Creazione di un database vuoto** : è possibile creare un riferimento al database passando il percorso del file al costruttore della classe SQLiteConnection. Non è necessario verificare se il file esiste già. se necessario, verrà creato automaticamente. in caso contrario, verrà aperto il file di database esistente.

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

    La variabile dbPath deve essere determinata in base alle regole descritte in precedenza in questo documento.

1. **Salva dati** : dopo aver creato un oggetto SQLiteConnection, i comandi di database vengono eseguiti chiamando i relativi metodi, ad esempio CreateTable e INSERT come segue:

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

1. **Recuperare i dati** : per recuperare un oggetto o un elenco di oggetti, usare la sintassi seguente:

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>Esempio di accesso ai dati di base

Il codice di esempio *DataAccess_Basic* per questo documento è simile al seguente quando viene eseguito in iOS. Il codice illustra come eseguire semplici operazioni SQLite.NET e Mostra i risultati in come testo nella finestra principale dell'applicazione.

**iOS**

 [![esempio di SQLite.NET iOS](using-sqlite-orm-images/image2-sml.png)](using-sqlite-orm-images/image2-sml.png#lightbox)

Nell'esempio di codice riportato di seguito viene illustrata un'intera interazione del database utilizzando la libreria SQLite.NET per incapsulare l'accesso al database sottostante. Mostra:

1. Creazione del file di database
1. Inserimento di alcuni dati mediante la creazione di oggetti e il relativo salvataggio
1. Esecuzione di una query sui dati

È necessario includere gli spazi dei nomi seguenti:

```csharp
using SQLite; // from the github SQLite.cs class
```

A questo scopo, è necessario aver aggiunto SQLite al progetto, come evidenziato [qui](#Usage). Si noti che la tabella di database SQLite viene definita aggiungendo attributi a una classe ( `Stock` la classe) invece di un comando create table.

```csharp
[Table("Items")]
public class Stock {
    [PrimaryKey, AutoIncrement, Column("_id")]
    public int Id { get; set; }
    [MaxLength(8)]
    public string Symbol { get; set; }
}
public static void DoSomeDataAccess () {
       Console.WriteLine ("Creating database, if it doesn't already exist");
   string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "ormdemo.db3");
   var db = new SQLiteConnection (dbPath);
   db.CreateTable<Stock> ();
   if (db.Table<Stock> ().Count() == 0) {
        // only insert the data if it doesn't already exist
        var newStock = new Stock ();
        newStock.Symbol = "AAPL";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "GOOG";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "MSFT";
        db.Insert (newStock);
    }
    Console.WriteLine("Reading data");
    var table = db.Table<Stock> ();
    foreach (var s in table) {
        Console.WriteLine (s.Id + " " + s.Symbol);
    }
}
```

Se si `[Table]` utilizza l'attributo senza specificare un parametro di nome di tabella, la tabella di database sottostante avrà lo stesso nome della classe, in questo caso "stock". Il nome effettivo della tabella è importante se si scrivono query SQL direttamente sul database anziché utilizzare i metodi di accesso ai dati ORM. Analogamente `[Column("_id")]` , l'attributo è facoltativo e, se assente, una colonna verrà aggiunta alla tabella con lo stesso nome della proprietà nella classe.

## <a name="sqlite-attributes"></a>Attributi SQLite

Gli attributi comuni che è possibile applicare alle classi per controllare il modo in cui vengono archiviati nel database sottostante includono:

- **[PrimaryKey]** : questo attributo può essere applicato a una proprietà integer per forzarlo come chiave primaria della tabella sottostante. Le chiavi primarie composte non sono supportate.
- **[Incremento automatico]** : questo attributo provocherà l'incremento automatico del valore di una proprietà integer per ogni nuovo oggetto inserito nel database
- **[Column (Name)]** : se si specifica il `name` parametro facoltativo, il valore predefinito del nome della colonna del database sottostante, che corrisponde alla proprietà, verrà ignorato.
- **[Table (Name)]** : contrassegna la classe come in grado di essere archiviata in una tabella SQLite sottostante. Se si specifica il parametro del nome facoltativo, verrà eseguito l'override del valore predefinito del nome della tabella di database sottostante, che corrisponde al nome della classe.
- **[MaxLength (valore)]** : limita la lunghezza di una proprietà di testo quando viene eseguito un tentativo di inserimento del database. L'utilizzo del codice deve essere convalidato prima di inserire l'oggetto perché questo attributo è solo ' checked ' quando si tenta di eseguire un'operazione di inserimento o aggiornamento di un database.
- **[Ignore]** : fa in modo che SQLite.NET ignori questa proprietà. Questa operazione è particolarmente utile per le proprietà che dispongono di un tipo che non può essere archiviato nel database o che le proprietà che non possono essere risolte automaticamente nelle raccolte di modelli siano SQLite.
- **[Unique]** : assicura che i valori nella colonna del database sottostante siano univoci.

La maggior parte di questi attributi è facoltativa, SQLite utilizzerà i valori predefiniti per i nomi di tabelle e colonne. È sempre necessario specificare una chiave primaria Integer in modo che le query di selezione ed eliminazione possano essere eseguite in modo efficiente sui dati.

## <a name="more-complex-queries"></a>Query più complesse

Per eseguire altre operazioni `SQLiteConnection` sui dati, è possibile usare i metodi seguenti:

- **Insert** : aggiunge un nuovo oggetto al database.
- **Get\<T >** : tenta di recuperare un oggetto utilizzando la chiave primaria.
- **Table\<T >** : restituisce tutti gli oggetti nella tabella.
- **Elimina** : consente di eliminare un oggetto utilizzando la relativa chiave primaria.
- **Query\<T >** -eseguire una query SQL che restituisce un numero di righe (come oggetti).
- **Execute** : usare questo metodo (e non `Query` ) quando non si prevede che le righe vengano restituite da SQL, ad esempio istruzioni INSERT, Update e DELETE.

### <a name="getting-an-object-by-the-primary-key"></a>Recupero di un oggetto in base alla chiave primaria

SQLite.Net fornisce il metodo Get per recuperare un singolo oggetto in base alla relativa chiave primaria.

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>Selezione di un oggetto mediante LINQ

I metodi che restituiscono raccolte\<supportano IEnumerable T > pertanto è possibile utilizzare LINQ per eseguire query o ordinare il contenuto di una tabella. Nel codice seguente viene illustrato un esempio di utilizzo di LINQ per filtrare tutte le voci che iniziano con la lettera "A":

```csharp
var apple = from s in db.Table<Stock>()
    where s.Symbol.StartsWith ("A")
    select s;
Console.WriteLine ("-> " + apple.FirstOrDefault ().Symbol);
```

### <a name="selecting-an-object-using-sql"></a>Selezione di un oggetto tramite SQL

Anche se SQLite.Net è in grado di fornire l'accesso basato su oggetti ai dati, a volte potrebbe essere necessario eseguire una query più complessa di quella consentita da LINQ (oppure potrebbe essere necessaria una maggiore velocità di prestazioni). È possibile usare i comandi SQL con il metodo di query, come illustrato di seguito:

```csharp
var stocksStartingWithA = db.Query<Stock>("SELECT * FROM Items WHERE Symbol = ?", "A");
foreach (var s in stocksStartingWithA) {
    Console.WriteLine ("a " + s.Symbol);
}
```

> [!IMPORTANT]
> Quando si scrivono direttamente istruzioni SQL, si crea una dipendenza dai nomi di tabelle e colonne del database, che sono stati generati dalle classi e dai relativi attributi. Se si modificano i nomi nel codice, è necessario ricordare di aggiornare le istruzioni SQL scritte manualmente.

### <a name="deleting-an-object"></a>Eliminazione di un oggetto

La chiave primaria viene utilizzata per eliminare la riga, come illustrato di seguito:

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

È possibile controllare per `rowcount` verificare il numero di righe interessate (eliminate in questo caso).

## <a name="using-sqlitenet-with-multiple-threads"></a>Uso di SQLite.NET con più thread

SQLite supporta tre diverse modalità di threading: A *thread singolo*, *multithreading*e *serializzato*. Se si desidera accedere al database da più thread senza alcuna restrizione, è possibile configurare SQLite per l'utilizzo della modalità di threading **serializzata** . È importante impostare questa modalità all'inizio dell'applicazione (ad esempio, all'inizio del `OnCreate` metodo).

Per modificare la modalità di threading, `SqliteConnection.SetConfig` chiamare che si trova `Mono.Data.Sqlite` nello spazio dei nomi. Questa riga di codice, ad esempio, configura SQLite per la modalità **serializzata** :

```csharp
using Mono.Data.Sqlite;
...
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Accesso ai dati di Novell. Forms](~/xamarin-forms/data-cloud/data/databases.md)
