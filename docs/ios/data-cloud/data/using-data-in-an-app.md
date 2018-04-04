---
title: Utilizzo di dati in un'App
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: 3366b78479712dffee74448dcc51e193cd07564d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="using-data-in-an-app"></a>Utilizzo di dati in un'App

Il **DataAccess_Adv** illustra un'applicazione funzionante che consente l'input dell'utente e *CRUD* funzionalità del database (Create, Read, Update e Delete). L'applicazione è costituita da due schermate: un elenco e un form di immissione dati. Tutto il codice di accesso ai dati è riutilizzabile in iOS e Android senza alcuna modifica.

Dopo avere aggiunto alcuni dati le schermate dell'applicazione simile al seguente in iOS:

 ![](using-data-in-an-app-images/image9.png "elenco di esempi di iOS")

 ![](using-data-in-an-app-images/image10.png "dettagli di esempio iOS")

IOS progetto è illustrato di seguito: il codice illustrato in questa sezione è contenuto all'interno di **Orm** directory:

 ![](using-data-in-an-app-images/image13.png "struttura di un progetto iOS")

Il codice dell'interfaccia utente nativo per ViewControllers in iOS esula dall'ambito di questo documento.
Consultare la [iOS utilizzo di tabelle e le celle](~/ios/user-interface/controls/tables/index.md) Guida per ulteriori informazioni sui controlli dell'interfaccia utente.

## <a name="read"></a>Lettura

Esistono un paio di operazioni di lettura nell'esempio:

-  La lettura dell'elenco
-  Lettura dei singoli record


I due metodi di `StockDatabase` sono:

```csharp
public IEnumerable<Stock> GetStocks ()
{
    lock (locker) {
        return (from i in Table<Stock> () select i).ToList ();
    }
}
public Stock GetStock (int id)
{
    lock (locker) {
        return Table<Stock>().FirstOrDefault(x => x.Id == id);
    }
}
```

iOS viene eseguito il rendering dei dati in modo diverso, come un `UITableView`.

## <a name="create-and-update"></a>Creazione e aggiornamento

Per semplificare il codice dell'applicazione, un singolo metodo di salvataggio è fornito che esegue un'istruzione Insert o Update a seconda se è stata impostata la PrimaryKey. Poiché il `Id` proprietà è contrassegnata con un `[PrimaryKey]` attributo, è consigliabile non impostare nel codice.
Questo metodo consentirà di rilevare se il valore è stato precedente salvata (selezionando la proprietà di chiave primaria) e inserire o aggiornare di conseguenza l'oggetto:

```csharp
public int SaveStock (Stock item)
{
    lock (locker) {
        if (item.Id != 0) {
            Update (item);
            return item.Id;
    } else {
            return Insert (item);
        }
    }
}
```



Applicazioni reali in genere richiedono una convalida (ad esempio i campi obbligatori, lunghezza minima o altre regole di business).
Le applicazioni multipiattaforma buona implementare la maggior parte della convalida logica possibile nel codice condiviso, il passaggio di errori di convalida, eseguire il backup l'interfaccia utente per la visualizzazione in base alle funzionalità della piattaforma.

## <a name="delete"></a>Eliminare

A differenza di `Insert` e `Update` metodi, il `Delete<T>` metodo può accettare solo il valore di chiave primaria anziché completa `Stock` oggetto.
In questo esempio un `Stock` oggetto viene passato al metodo, ma solo la proprietà Id viene passata al `Delete<T>` metodo.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Utilizzo di un file di database SQLite prepopolato

Alcune applicazioni vengono forniti con un database già popolato con dati.
Si può facilmente questo scopo nell'applicazione per dispositivi mobili per la spedizione di un file di database SQLite esistente con l'app e copiarlo in una directory accessibile in scrittura prima di accedervi. Poiché SQLite è un formato di file standard che viene utilizzato in molte piattaforme, sono disponibili numerosi strumenti disponibili per creare un file di database SQLite:

-  **Estensione di gestione di SQLite Firefox** – funziona su Mac e Windows e genera file che sono compatibili con iOS e Android.
-  **Riga di comando** – vedere [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) .


Prestare attenzione quando si crea un file di database per la distribuzione con l'app, con la denominazione di tabelle e colonne per assicurarsi che corrispondano cosa prevede che il codice, in particolare se si utilizza SQLite.NET che si aspetta di ricevere i nomi in modo che corrisponda l'in c# le classi e proprietà (o attributi personalizzati associati).

Per iOS, includere il file di sqlite nell'applicazione e assicurarsi sia contrassegnato con **azione di compilazione: contenuto**. Inserire il codice nel `FinishedLaunching` per copiare il file in una directory accessibile in scrittura *prima* di chiamare qualsiasi dati. Il codice seguente verrà copiato un database esistente denominato **data.sqlite**, solo se non esiste già.

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

Qualsiasi codice di accesso ai dati (se ADO.NET o tramite SQLite.NET) che viene eseguito dopo che questa operazione verrà completata hanno accesso ai dati pre-popolati.


## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzate (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS dati ricette](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
