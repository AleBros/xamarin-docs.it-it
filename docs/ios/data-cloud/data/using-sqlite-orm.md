---
title: Utilizzo di SQLite.NET con iOS
description: La libreria di NuGet PCL SQLite.NET fornisce un meccanismo di accesso ai dati semplice per le app xamarin. IOS.
ms.prod: xamarin
ms.assetid: 79813B09-42D7-47DD-AE71-A605E6B9EF24
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/18/2018
ms.openlocfilehash: e9149fc163d31e0fcbc6ae28ccaa896eeca6dd9b
ms.sourcegitcommit: 797597d902330652195931dec9ac3e0cc00792c5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2018
---
# <a name="using-sqlitenet-with-ios"></a>Utilizzo di SQLite.NET con iOS

La libreria di SQLite.NET in cui si consiglia di Xamarin è una base ORM che consente di archiviare e recuperare gli oggetti nel database SQLite locale in un dispositivo iOS.
ORM è l'acronimo di oggetti relazionali Mapping: un'API che consente di salvare e recuperare "oggetti" da un database senza scrivere istruzioni SQL.

<a name="Usage"/>

## <a name="usage"></a>Utilizzo

Per includere la libreria SQLite.NET in un'app Xamarin, aggiungere il pacchetto NuGet seguente al progetto:

- **Nome del pacchetto:** sqlite-net-libreria di classi portabile
- **Autore:** Frank A. Krueger
- **ID:** sqlite-net-libreria di classi portabile
- **URL:** [nuget.org/packages/sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

[![Pacchetto SQLite.NET NuGet](using-sqlite-orm-images/image1a-sml.png "pacchetto SQLite.NET NuGet")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> Sono disponibili numerosi pacchetti SQLite diversi: assicurarsi di scegliere quello corretto (potrebbe non essere il risultato superiore nella ricerca).

Dopo aver creato la libreria SQLite.NET disponibile, eseguire la tre procedura usarlo per accedere a un database:

1. **Aggiungere un tramite istruzione** -aggiungere l'istruzione seguente per i file c# in cui è necessario l'accesso ai dati:

    ```csharp
    using SQLite;
    ```

1. **Creare un Database vuoto** -può essere creato un riferimento al database passando il percorso del file, il costruttore della classe SQLiteConnection. Non è necessario controllare se il file esiste già, verrà automaticamente creata se necessario, in caso contrario il file di database esistente verrà aperto.

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

    La variabile dbPath deve essere determinata in base a regole illustrate in precedenza in questo documento.

1. **Salvare i dati** - dopo aver creato un oggetto SQLiteConnection, i database vengono eseguiti i comandi chiamando i metodi, ad esempio CreateTable e Insert simile al seguente:

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

1. **Recuperare i dati** : per recuperare un oggetto (o un elenco di oggetti) usa la sintassi seguente:

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>Esempio di accesso ai dati di base

Il *DataAccess_Basic* codice di esempio per questo documento è simile al seguente quando si esegue in iOS. Il codice viene illustrato come eseguire semplici operazioni SQLite.NET e vengono illustrati i risultati in formato testo nella finestra principale dell'applicazione.

**iOS**

 [![esempio SQLite.NET iOS](using-sqlite-orm-images/image2-sml.png)](using-sqlite-orm-images/image2-sml.png#lightbox)

Esempio di codice seguente mostra un'interazione intero database utilizzando la libreria SQLite.NET per incapsulare l'accesso al database sottostante. Viene illustrato:

1.  Creazione del file di database
1.  Inserimento di alcuni dati tramite la creazione di oggetti e quindi salvando i
1.  Esecuzione di una query sui dati

È necessario includere questi spazi dei nomi:

```csharp
using SQLite; // from the github SQLite.cs class
```

Questa operazione richiede che si è aggiunti SQLite al progetto, come evidenziato [qui](#Usage). Si noti che la tabella di database SQLite è definita tramite l'aggiunta di attributi a una classe (il `Stock` classe) anziché un comando CREATE TABLE.

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

Utilizzo di `[Table]` attributo senza specificare un parametro di nome di tabella causerà la tabella di database sottostante abbiano lo stesso nome di classe (in questo caso, "Stock"). Il nome della tabella effettiva è importante se si scrivono query SQL direttamente nel database anziché utilizzare i metodi di accesso ai dati ORM. Analogamente il `[Column("_id")]` attributo è facoltativo e se assente una colonna verrà aggiunta alla tabella con lo stesso nome della proprietà nella classe.

## <a name="sqlite-attributes"></a>Attributi di SQLite

Attributi comuni che è possibile applicare alle classi per controllare la modalità di archiviazione nel database sottostante includono:

-  **[PrimaryKey]**  : Questo attributo può essere applicato a una proprietà integer per far sì che la chiave primaria della tabella sottostante. Le chiavi primarie composte non sono supportate.
-  **[Incremento automatico]**  : Questo attributo causerà il valore della proprietà integer su incremento automatico per ogni nuovo oggetto inserito nel database
-  **[Column(name)]**  – Fornendo facoltativo `name` parametro sostituirà il valore predefinito del nome della colonna di database sottostante (che è identica alla proprietà).
-  **[Table(name)]**  : Contrassegna una classe come la possibilità di essere archiviati in una tabella di SQLite sottostante. Specifica il parametro name facoltativo sostituirà il valore predefinito del nome della tabella di database sottostante (che è lo stesso nome della classe).
-  **[MaxLength(value)]**  : Limitare la lunghezza di una proprietà di testo, quando si tenta di inserire un database. Utilizzo di codice, questo deve convalidare prima dell'inserimento di questo attributo è solo 'checked' quando un database di inserimento o aggiornamento operazione viene tentata l'oggetto.
-  **[Ignorare]**  – SQLite.NET fa sì che per ignorare questa proprietà. Ciò è particolarmente utile per le proprietà che hanno un tipo che non può essere archiviato nel database o le proprietà di raccolte di modelli che non possono essere risolti automaticamente SQLite.
-  **[Unique]**  : Assicura che i valori nella colonna di database sottostanti siano univoci.


La maggior parte di questi attributi sono facoltativa, SQLite verrà utilizzati i valori predefiniti per i nomi di tabella e colonna. È necessario specificare una chiave primaria di tipo integer sempre in modo che le query di selezione e l'eliminazione possono essere eseguite in modo efficiente i dati.

## <a name="more-complex-queries"></a>Query più complesse

I seguenti metodi su `SQLiteConnection` può essere utilizzato per eseguire altre operazioni di dati:

-  **Inserisci** : aggiunge un nuovo oggetto nel database.
-  **Ottenere<T>**  – tenta di recuperare un oggetto utilizzando la chiave primaria.
-  **Tabella<T>**  : restituisce tutti gli oggetti della tabella.
-  **Eliminare** : elimina un oggetto utilizzando la chiave primaria.
-  **Query<T>**  -eseguire una query SQL che restituisce un numero di righe (come oggetti).
-  **Eseguire** : utilizzare questo metodo (e non `Query` ) quando non si prevede righe da SQL (ad esempio istruzioni INSERT, UPDATE e DELETE).


### <a name="getting-an-object-by-the-primary-key"></a>Recupero di un oggetto tramite la chiave primaria

SQLite.Net fornisce il metodo Get per recuperare un singolo oggetto in base alla chiave primaria.

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>Selezione di un oggetto utilizzando Linq

I metodi che restituiscono raccolte supportano IEnumerable<T> quindi è possibile usare Linq per eseguire query o ordinare il contenuto di una tabella. Il codice seguente viene illustrato un esempio di utilizzo di Linq per filtrare tutte le voci che iniziano con la lettera "A":

```csharp
var apple = from s in db.Table<Stock>()
    where s.Symbol.StartsWith ("A")
    select s;
Console.WriteLine ("-> " + apple.FirstOrDefault ().Symbol);
```

### <a name="selecting-an-object-using-sql"></a>Selezione di un oggetto tramite SQL

Anche se SQLite.Net può fornire l'accesso ai dati basato su oggetti, in alcuni casi potrebbe essere necessario eseguire una query più complessa rispetto a Linq consente (o è possibile migliorare le prestazioni). È possibile utilizzare i comandi SQL con il metodo di Query, come illustrato di seguito:

```csharp
var stocksStartingWithA = db.Query<Stock>("SELECT * FROM Items WHERE Symbol = ?", "A");
foreach (var s in stocksStartingWithA) {
    Console.WriteLine ("a " + s.Symbol);
}
```

> [!IMPORTANT]
> Durante la scrittura di istruzioni SQL direttamente crei una dipendenza sui nomi delle tabelle e colonne del database, che sono state generate dalle classi e i relativi attributi. Se si modificano i nomi nel codice è necessario ricordarsi di aggiornare tutte le istruzioni SQL scritte manualmente.

### <a name="deleting-an-object"></a>Eliminazione di un oggetto

La chiave primaria viene utilizzata per eliminare la riga, come illustrato di seguito:

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

È possibile controllare il `rowcount` per confermare il numero di righe interessato (eliminati in questo caso).

## <a name="using-sqlitenet-with-multiple-threads"></a>Utilizzo di SQLite.NET con più thread

SQLite supporta tre diverse modalità di threading: *a thread singolo*, *multi-thread*, e *serializzato*. Se si desidera accedere al database da più thread senza restrizioni, è possibile configurare SQLite per utilizzare il **serializzato** threading modalità. È importante impostare questa modalità prima dell'applicazione (ad esempio, all'inizio del `OnCreate` (metodo)).

Per modificare la modalità di threading, chiamare `SqliteConnection.SetConfig`. Ad esempio, questa riga di codice Configura SQLite per **serializzato** modalità:

```csharp
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzate (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS dati ricette](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
