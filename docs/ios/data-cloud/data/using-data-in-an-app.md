---
title: Uso dei dati in un'app per iOS
description: Questo documento descrive la DataAccess_Adv un esempio che illustra come raccogliere input dell'utente ed eseguire create, read, update e delete (CRUD) di operazioni in un'app xamarin. IOS di database.
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: a47ab26777c594658810b014025477486bbe5cc2
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650155"
---
# <a name="using-data-in-an-ios-app"></a>Uso dei dati in un'app per iOS

Il **DataAccess_Adv** esempio illustra un'applicazione funzionante che consente l'input dell'utente e *CRUD* funzionalità del database (creazione, lettura, aggiornamento ed eliminazione). L'applicazione è costituita da due schermate seguenti: un elenco e un modulo di immissione dati. Tutto il codice di accesso di dati è riutilizzato in iOS e Android senza alcuna modifica.

Dopo aver aggiunto alcuni dati le schermate dell'applicazione l'aspetto seguente in iOS:

 ![](using-data-in-an-app-images/image9.png "elenco di esempi di iOS")

 ![](using-data-in-an-app-images/image10.png "dettagli di esempio iOS")

IOS Project è illustrato di seguito: il codice riportato in questa sezione è contenuto all'interno di **Orm** directory:

 ![](using-data-in-an-app-images/image13.png "struttura del progetto iOS")

Il codice dell'interfaccia utente nativo per ViewControllers in iOS è esterna all'ambito di questo documento.
Vedere le [iOS utilizzo di tabelle e celle](~/ios/user-interface/controls/tables/index.md) Guida per altre informazioni sui controlli dell'interfaccia utente.

## <a name="read"></a>Lettura

Esistono un paio di operazioni di lettura nell'esempio:

-  La lettura dell'elenco
-  La lettura dei singoli record


I due metodi nel `StockDatabase` classe sono:

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

iOS rende i dati in modo diverso come un `UITableView`.

## <a name="create-and-update"></a>Creare e aggiornare

Per semplificare il codice dell'applicazione, un singolo metodo di salvataggio è fornito che consente di eseguire un'istruzione Insert o Update a seconda che sia stata impostata il PrimaryKey. Poiché il `Id` proprietà è contrassegnata con un `[PrimaryKey]` attributo è consigliabile non impostarlo nel codice.
Questo metodo rileva se il valore è stato precedente salvata (controllando la proprietà di chiave primaria) e inserire o aggiornare di conseguenza l'oggetto:

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



Applicazioni reali richiede in genere una convalida (ad esempio i campi obbligatori, le lunghezze minima o altre regole di business).
Le applicazioni multipiattaforma buona implementare la maggior parte della convalida logico possibile nel codice condiviso, il passaggio di errori di convalida, eseguire il backup l'interfaccia utente per la visualizzazione in base alle funzionalità della piattaforma.

## <a name="delete"></a>Eliminare

A differenza di `Insert` e `Update` metodi, il `Delete<T>` metodo accetta semplicemente il valore di chiave primaria anziché completa `Stock` oggetto.
In questo esempio un `Stock` oggetto viene passato al metodo, ma solo la proprietà Id viene passata al `Delete<T>` (metodo).

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Utilizzo di un file di database SQLite prepopolato

Alcune applicazioni vengono forniti con un database già popolato con i dati.
È possibile farlo facilmente nell'applicazione per dispositivi mobili da un file di database SQLite esistente con l'app di spedizione e copiarla in una directory scrivibile prima dell'accesso. Poiché SQLite è un formato di file standard che viene usato in molte piattaforme, sono disponibili numerosi strumenti disponibili per creare un file di database SQLite:

-  **Estensione per Firefox SQLite Manager** – funziona su Mac e Windows e produce file che sono compatibili con iOS e Android.
-  **Riga di comando** , vedere [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) .


Prestare attenzione quando si crea un file di database per la distribuzione con l'app, con la denominazione di tabelle e colonne per assicurarsi che corrispondano a ciò che prevede che il codice, soprattutto se si usa SQLite.NET che si aspetta di ricevere i nomi in base ai in c# le classi e proprietà (o il attributi personalizzati associati).

Per iOS, includere il file di sqlite nell'applicazione e verificare sia contrassegnata con **azione di compilazione: Content**. Inserire il codice nel `FinishedLaunching` copiare il file in una directory scrivibile *prima di* è chiamare qualsiasi metodo dati. Il codice seguente verrà copiato un database esistente denominato **data.sqlite**, solo se non esiste già.

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

Qualsiasi codice di accesso ai dati (se ADO.NET o uso di SQLite.NET) che viene eseguito dopo che questa operazione verrà completata hanno accesso ai dati già popolati.


## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzate (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS recipe di dati](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
