---
title: Uso dei dati in un'app iOS
description: Questo documento descrive l'esempio DataAccess_Adv, che illustra come raccogliere l'input dell'utente ed eseguire operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) del database in un'app Novell. iOS.
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/11/2016
ms.openlocfilehash: 060e4b8e7856e0024e6d236652c2b04c1da16f66
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008244"
---
# <a name="using-data-in-an-ios-app"></a>Uso dei dati in un'app iOS

L'esempio **DataAccess_Adv** Mostra un'applicazione funzionante che consente l'input dell'utente e la funzionalità di database *CRUD* (creazione, lettura, aggiornamento ed eliminazione). L'applicazione è costituita da due schermate: un elenco e un form di immissione dati. Tutto il codice di accesso ai dati è riutilizzabile in iOS e Android senza modifiche.

Dopo l'aggiunta di alcuni dati, le schermate dell'applicazione hanno un aspetto simile al seguente in iOS:

 ![](using-data-in-an-app-images/image9.png "iOS sample list")

 ![](using-data-in-an-app-images/image10.png "iOS sample detail")

Il progetto iOS è illustrato di seguito. il codice illustrato in questa sezione è contenuto nella directory **ORM** :

 ![](using-data-in-an-app-images/image13.png "iOS project tree")

Il codice dell'interfaccia utente nativa per ViewControllers in iOS non rientra nell'ambito di questo documento.
Per altre informazioni sui controlli dell'interfaccia utente, vedere la Guida [uso di tabelle e celle per iOS](~/ios/user-interface/controls/tables/index.md) .

## <a name="read"></a>Lettura

Nell'esempio sono presenti due operazioni di lettura:

- Lettura dell'elenco
- Lettura di singoli record

I due metodi della classe `StockDatabase` sono:

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

iOS esegue il rendering dei dati in modo diverso come `UITableView`.

## <a name="create-and-update"></a>Creazione e aggiornamento

Per semplificare il codice dell'applicazione, viene fornito un singolo metodo Save che esegue un inserimento o un aggiornamento a seconda che sia stato impostato PrimaryKey. Poiché la proprietà `Id` è contrassegnata con un attributo `[PrimaryKey]` non è necessario impostarla nel codice.
Questo metodo consente di rilevare se il valore è stato salvato in precedenza (controllando la proprietà della chiave primaria) e inserire o aggiornare l'oggetto di conseguenza:

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

Le applicazioni reali richiederanno in genere una convalida, ad esempio campi obbligatori, lunghezze minime o altre regole business.
Le applicazioni multipiattaforma valide implementano la maggior parte del possibile di convalida nel codice condiviso, passando gli errori di convalida all'interfaccia utente per la visualizzazione in base alle funzionalità della piattaforma.

## <a name="delete"></a>Eliminazione

A differenza dei metodi `Insert` e `Update`, il metodo `Delete<T>` può accettare solo il valore della chiave primaria invece di un oggetto `Stock` completo.
In questo esempio un oggetto `Stock` viene passato al metodo, ma solo la proprietà ID viene passata al metodo `Delete<T>`.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Uso di un file di database SQLite pre-popolato

Alcune applicazioni vengono fornite con un database già popolato con i dati.
Per eseguire questa operazione nell'applicazione per dispositivi mobili, è possibile spedire un file di database SQLite esistente all'app e copiarlo in una directory scrivibile prima di accedervi. Poiché SQLite è un formato di file standard usato in molte piattaforme, sono disponibili diversi strumenti per creare un file di database SQLite:

- **Estensione per Firefox di gestione SQLite** : funziona su Mac e Windows e produce file compatibili con iOS e Android.
- **Riga di comando** : vedere [www.sqlite.org/SQLite.html](https://www.sqlite.org/sqlite.html) .

Quando si crea un file di database per la distribuzione con l'app, prestare attenzione alla denominazione di tabelle e colonne per assicurarsi che corrispondano a quanto previsto dal codice, specialmente se si usa SQLite.NET, che prevede che i nomi C# corrispondano alle classi e alle proprietà ( o gli attributi personalizzati associati).

Per iOS, includere il file SQLite nell'applicazione e assicurarsi che sia contrassegnato con **azione di compilazione: contenuto**. Inserire il codice nel `FinishedLaunching` per copiare il file in una directory scrivibile *prima* di chiamare qualsiasi metodo di dati. Nel codice seguente viene copiato un database esistente denominato **Data. sqlite**, solo se non esiste già.

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

Qualsiasi codice di accesso ai dati (se ADO.NET o SQLite.NET) che viene eseguito dopo che questa operazione è stata completata avrà accesso ai dati precompilati.

## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Ricette per i dati iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Accesso ai dati di Novell. Forms](~/xamarin-forms/data-cloud/data/databases.md)
