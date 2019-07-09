---
title: Database locali Xamarin.Forms
description: Xamarin.Forms supporta applicazioni basate su database con il motore di database SQLite, che consente di caricare e salvare gli oggetti nel codice condiviso. Questo articolo descrive come le applicazioni Xamarin.Forms possono leggere e scrivere dati in un database SQLite locale usando SQLite.Net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 235a30293939333555c52b8d9e00bcf25ddd4dbd
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658588"
---
# <a name="xamarinforms-local-databases"></a>Database locali Xamarin.Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/Todo/)

_Xamarin.Forms supporta applicazioni basate su database con il motore di database SQLite, che consente di caricare e salvare gli oggetti nel codice condiviso. Questo articolo descrive come le applicazioni Xamarin.Forms possono leggere e scrivere dati in un database SQLite locale usando SQLite.Net._

## <a name="overview"></a>Panoramica

Le applicazioni Xamarin.Forms possono usare il pacchetto [SQLite.NET PCL NuGet](https://www.nuget.org/packages/sqlite-net-pcl/) per incorporare le operazioni di database nel codice condiviso facendo riferimento alle classi `SQLite` incluse in NuGet. Le operazioni di database possono essere definite nel progetto della libreria .NET Standard della soluzione Xamarin.Forms.

L'[applicazione di esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo) associata è una semplice applicazione per elenchi di attività. Gli screenshot seguenti illustrano come viene visualizzato l'esempio in ogni piattaforma:

[![Screenshot dell'esempio di database Xamarin.Forms](databases-images/todo-list-sml.png "Screenshot della prima pagina di TodoList")](databases-images/todo-list.png#lightbox "Screenshot della prima pagina di TodoList") [![Screenshot dell'esempio di database Xamarin.Forms](databases-images/todo-list-sml.png "Screenshot della prima pagina di TodoList")](databases-images/todo-list.png#lightbox "Screenshot della prima pagina di TodoList")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>Uso di SQLite

Per aggiungere il supporto di SQLite in una libreria .NET Standard di Xamarin.Forms, usare la funzione di ricerca di NuGet per trovare **sqlite-net-pcl** e installare il pacchetto più recente:

![Aggiungere il pacchetto NuGet SQLite.NET PCL](databases-images/vs2017-sqlite-pcl-nuget.png "Aggiungere il pacchetto NuGet SQLite.NET PCL")

Sono disponibili vari pacchetti NuGet con nomi simili. Il pacchetto corretto ha questi attributi:

- **Creato da:** Frank A. Krueger
- **ID:** sqlite-net-pcl
- **Collegamento NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Nonostante il nome del pacchetto, usare il pacchetto NuGet **sqlite-net-pcl** anche nei progetti .NET Standard.

Dopo aver aggiunto il riferimento, aggiungere una proprietà alla classe `App` che restituisce un percorso di file locale per l'archiviazione del database:

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

Il costruttore `TodoItemDatabase`, che accetta il percorso del file di database come argomento, è illustrato di seguito:

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

Il vantaggio dell'esposizione del database come singleton è la creazione di una singola connessione al database che verrà mantenuta aperta durante l'esecuzione dell'applicazione, evitando così l'onere dell'apertura e della chiusura del file di database ogni volta che viene eseguita un'operazione di database.

Il resto della classe `TodoItemDatabase` contiene query SQLite che supportano l'esecuzione multipiattaforma. Di seguito è riportato il codice di query di esempio. Altri dettagli sulla sintassi sono disponibili in [Uso di SQLite.NET con Xamarin.iOS](~/ios/data-cloud/data/using-sqlite-orm.md).

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
> Il vantaggio dell'uso dell'API SQLite.Net asincrona è lo spostamento delle operazioni di database su thread in background. Non è inoltre necessario scrivere codice aggiuntivo per la gestione della concorrenza perché se ne occupa l'API.

## <a name="summary"></a>Riepilogo

Xamarin.Forms supporta applicazioni basate su database con il motore di database SQLite, che consente di caricare e salvare gli oggetti nel codice condiviso.

Questo articolo è incentrato sull'**accesso** a un database SQLite tramite Xamarin.Forms. Per altre informazioni sull'uso di SQLite.Net, vedere la documentazione per [SQLite.NET in Android](~/android/data-cloud/data-access/using-sqlite-orm.md) oppure [SQLite.NET in iOS](~/ios/data-cloud/data/using-sqlite-orm.md).

## <a name="related-links"></a>Collegamenti correlati

- [Esempio Todo](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)

