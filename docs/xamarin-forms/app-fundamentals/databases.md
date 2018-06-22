---
title: Database locale di xamarin. Forms
description: Xamarin. Forms supporta le applicazioni basate sul database utilizzando il motore di database SQLite, che consente di caricare e salvare gli oggetti nel codice condiviso. In questo articolo viene descritto come xamarin. Forms applicazioni possono leggere e scrivere dati in un database locale di SQLite utilizzando SQLite.Net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: feec4993a0719a083d713e084552b18aead8ee42
ms.sourcegitcommit: eac092f84b603958c761df305f015ff84e0fad44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310140"
---
# <a name="xamarinforms-local-databases"></a>Database locale di xamarin. Forms

_Xamarin. Forms supporta le applicazioni basate sul database utilizzando il motore di database SQLite, che consente di caricare e salvare gli oggetti nel codice condiviso. In questo articolo viene descritto come xamarin. Forms applicazioni possono leggere e scrivere dati in un database locale di SQLite utilizzando SQLite.Net._

## <a name="overview"></a>Panoramica

Xamarin. Forms applicazioni possono utilizzare il [NuGet PCL SQLite.NET](https://www.nuget.org/packages/sqlite-net-pcl/) pacchetto per incorporare le operazioni di database in codice condiviso facendo riferimento al `SQLite` classi disponibili in NuGet. È possibile definire le operazioni del database nel progetto di libreria Standard di .NET della soluzione xamarin. Forms.

Il tipo di accompagnamento [applicazione di esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo) è una semplice applicazione elenco attività. Le schermate seguenti mostrano come nell'esempio viene visualizzata in ogni piattaforma:

[![Schermate di esempio xamarin. Forms database](databases-images/todo-list-sml.png "TodoList prima Page Screenshots")](databases-images/todo-list.png#lightbox "TodoList prima Page Screenshots") [ ![ Schermate di esempio xamarin. Forms database](databases-images/todo-list-sml.png "TodoList prima Page Screenshots")](databases-images/todo-list.png#lightbox "Page Screenshots prima TodoList")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>Utilizzo di SQLite

Per aggiungere il supporto di SQLite per una libreria .NET di xamarin. Forms Standard, utilizzare la funzione di ricerca di NuGet per trovare **sqlite-net-libreria di classi portabile** e installare il pacchetto più recente:

![Aggiungere NuGet SQLite.NET PCL pacchetto](databases-images/vs2017-sqlite-pcl-nuget.png "aggiungere NuGet SQLite.NET PCL pacchetto")

Esistono un numero di pacchetti NuGet con nomi simili, il pacchetto corretto questi attributi è:

- **Creato da:** Frank A. Krueger
- **ID:** sqlite-net-libreria di classi portabile
- **Collegamento di NuGet:** [sqlite-net-libreria di classi portabile](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Nonostante il nome del pacchetto, usare il **sqlite-net-libreria di classi portabile** pacchetto NuGet anche in progetti .NET Standard.

Dopo aver aggiunto il riferimento, aggiungere una proprietà di `App` classe che restituisce un percorso file locale per l'archiviazione del database:

```csharp
static TodoItemDatabase database;

public static TodoItemDatabase Database
{
  get
  {
    if (database == null)
    {
      database = new TodoItemDatabase(
        Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "TodoSQLite.db3"));
    }
    return database;
  }
}
```

Il `TodoItemDatabase` costruttore che accetta il percorso del file di database come argomento, è illustrato di seguito:

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

Il vantaggio derivante dall'esposizione di database come un singleton indica che viene creata una singola connessione di database che verrà mantenuto aperto durante l'applicazione non viene eseguito, pertanto evitare le spese correlate all'apertura e chiusura del file di database ogni volta che un'operazione di database viene eseguita.

Il resto della `TodoItemDatabase` classe contiene le query SQLite eseguite multipiattaforma. Di seguito è riportato il codice di query di esempio (ulteriori dettagli sulla sintassi, vedere il [SQLite.NET utilizzando](~/cross-platform/app-fundamentals/index.md) articolo):

```csharp
public Task<List<TodoItem>> GetItemsAsync()
{
  return database.Table<TodoItem>().ToListAsync();
}

public Task<List<TodoItem>> GetItemsNotDoneAsync()
{
  return database.QueryAsync<TodoItem>("SELECT * FROM [TodoItem] WHERE [Done] = 0");
}

public Task<TodoItem> GetItemAsync(int id)
{
  return database.Table<TodoItem>().Where(i => i.ID == id).FirstOrDefaultAsync();
}

public Task<int> SaveItemAsync(TodoItem item)
{
  if (item.ID != 0)
  {
    return database.UpdateAsync(item);
  }
  else {
    return database.InsertAsync(item);
  }
}

public Task<int> DeleteItemAsync(TodoItem item)
{
  return database.DeleteAsync(item);
}
```

> [!NOTE]
> Il vantaggio di utilizzare l'API SQLite.Net asincrona è il database in cui operazioni vengono spostate al thread in background. Inoltre, non è necessario scrivere ulteriore concorrenza codice di gestione in quanto l'API si occupa di esso.

## <a name="summary"></a>Riepilogo

Xamarin. Forms supporta le applicazioni basate sul database utilizzando il motore di database SQLite, che consente di caricare e salvare gli oggetti nel codice condiviso.

In questo articolo è incentrata sulla **accesso** un database SQLite con xamarin. Forms. Per ulteriori informazioni sull'uso di SQLite.Net stesso, consultare il [SQLite.NET in Android](~/android/data-cloud/data-access/using-sqlite-orm.md) oppure [SQLite.NET in iOS](~/ios/data-cloud/data/using-sqlite-orm.md) documentazione.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di attività](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Cartella di lavoro database](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/database/database.workbook)
