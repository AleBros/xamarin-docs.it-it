---
title: Database locale di xamarin. Forms
description: Xamarin. Forms supporta applicazioni basate su database con il motore di database SQLite, che consente di caricare e salvare gli oggetti nel codice condiviso. Questo articolo descrive come le applicazioni xamarin. Forms possono leggere e scrivere dati in un database SQLite locale usando SQLite.Net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 05c77c4c47841a897d0d1de5c3ba004db524ea2d
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "36310140"
---
# <a name="xamarinforms-local-databases"></a>Database locale di xamarin. Forms

_Xamarin. Forms supporta applicazioni basate su database con il motore di database SQLite, che consente di caricare e salvare gli oggetti nel codice condiviso. Questo articolo descrive come le applicazioni xamarin. Forms possono leggere e scrivere dati in un database SQLite locale usando SQLite.Net._

## <a name="overview"></a>Panoramica

Le applicazioni xamarin. Forms possono usare la [NuGet PCL SQLite.NET](https://www.nuget.org/packages/sqlite-net-pcl/) pacchetto per incorporare le operazioni del database nel codice condiviso facendo riferimento al `SQLite` classi disponibili in NuGet. Operazioni di database possono essere definite nel progetto della libreria .NET Standard della soluzione xamarin. Forms.

Il tipo di accompagnamento [applicazione di esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo) è una semplice applicazione elenco attività. Le schermate seguenti illustrano come nell'esempio viene visualizzato in ogni piattaforma:

[![Screenshot di esempio del database di xamarin. Forms](databases-images/todo-list-sml.png "TodoList prima Page Screenshots")](databases-images/todo-list.png#lightbox "TodoList prima Page Screenshots") [ ![ Screenshot di esempio del database di xamarin. Forms](databases-images/todo-list-sml.png "TodoList prima Page Screenshots")](databases-images/todo-list.png#lightbox "TodoList prima Page Screenshots")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>Utilizzo di SQLite

Per aggiungere il supporto di SQLite in una libreria .NET Standard di xamarin. Forms, usare la funzione di ricerca di NuGet per trovare **sqlite-net-libreria di classi portabile** e installare il pacchetto più recente:

![Aggiungere il pacchetto di libreria di classi Portabile NuGet SQLite.NET](databases-images/vs2017-sqlite-pcl-nuget.png "aggiungere NuGet SQLite.NET PCL pacchetto")

Esistono una serie di pacchetti NuGet con nomi simili, il pacchetto corretto con questi attributi:

- **Creato da:** Frank A. Krueger
- **ID:** sqlite-net-libreria di classi portabile
- **Collegamento di NuGet:** [sqlite-net-libreria di classi portabile](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Nonostante il nome del pacchetto, usare il **sqlite-net-libreria di classi portabile** mobileengagement anche nei progetti .NET Standard.

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

Il vantaggio derivante dall'esposizione del database come un singleton è che viene creata una connessione di database singolo che verrà mantenuto aperto durante l'applicazione viene eseguita, evitando pertanto il costo dell'apertura e chiusura del file di database ogni volta che un'operazione di database viene eseguita.

Il resto del `TodoItemDatabase` classe contiene query SQLite che eseguono lo sviluppo multipiattaforma. Di seguito è riportato l'esempio di codice di query (ulteriori informazioni sulla sintassi sono reperibili nel [uso di SQLite.NET con xamarin. IOS](~/ios/data-cloud/data/using-sqlite-orm.md).

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
> Il vantaggio dell'uso dell'API di SQLite.Net asincrona è tale database vengono spostate operazioni a thread in background. Inoltre, non è necessario scrivere codice di gestione perché l'API di esso si occupa di concorrenza aggiuntivi.

## <a name="summary"></a>Riepilogo

Xamarin. Forms supporta applicazioni basate su database con il motore di database SQLite, che consente di caricare e salvare gli oggetti nel codice condiviso.

Questo articolo è incentrato sulla **l'accesso a** un database SQLite usando xamarin. Forms. Per altre informazioni sull'uso di SQLite.Net stesso, vedere la [SQLite.NET in Android](~/android/data-cloud/data-access/using-sqlite-orm.md) oppure [SQLite.NET in iOS](~/ios/data-cloud/data/using-sqlite-orm.md) documentazione.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio TODO](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)

