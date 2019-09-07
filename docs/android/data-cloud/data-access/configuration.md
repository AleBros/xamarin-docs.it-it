---
title: Configurazione
ms.prod: xamarin
ms.assetid: 44526226-4E4E-4FFF-9A16-CA7B1E01BB8F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/11/2016
ms.openlocfilehash: 5ebafa70239305210da631c3e9c34278f83b272b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754661"
---
# <a name="configuration"></a>Configurazione

Per usare SQLite nell'applicazione Novell. Android, è necessario determinare il percorso del file corretto per il file di database.

## <a name="database-file-path"></a>Percorso file di database

Indipendentemente dal metodo di accesso ai dati usato, è necessario creare un file di database prima che i dati possano essere archiviati con SQLite. A seconda della piattaforma di destinazione, il percorso del file sarà diverso. Per Android è possibile usare la classe Environment per costruire un percorso valido, come illustrato nel frammento di codice seguente:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

Quando si decide dove archiviare il file di database, è necessario prendere in considerazione altri aspetti. Ad esempio, in Android è possibile scegliere se usare l'archiviazione interna o esterna.

Se si vuole usare un percorso diverso in ogni piattaforma nell'applicazione multipiattaforma, è possibile usare una direttiva del compilatore, come illustrato per generare un percorso diverso per ogni piattaforma:

```csharp
var sqliteFilename = "MyDatabase.db3";
#if __ANDROID__
// Just use whatever directory SpecialFolder.Personal returns
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
// we need to put in /Library/ on iOS5.1 to meet Apple's iCloud terms
// (they don't want non-user-generated data in Documents)
string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder instead
#endif
var path = Path.Combine (libraryPath, sqliteFilename);
```

Per suggerimenti sull'uso del file system in Android, vedere la pagina relativa all' [esplorazione dei file](https://github.com/xamarin/recipes/tree/master/Recipes/android/data/files/browse_files) . Per altre informazioni sull'uso delle direttive del compilatore per scrivere codice specifico per ogni piattaforma, vedere il documento [compilazione di applicazioni multipiattaforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) .

## <a name="threading"></a>Threading

Non usare la stessa connessione di database SQLite tra più thread. Prestare attenzione ad aprire, usare e quindi chiudere tutte le connessioni create nello stesso thread.

Per assicurarsi che il codice non tenti di accedere al database SQLite da più thread contemporaneamente, eseguire manualmente un blocco ogni volta che si accede al database, come segue:

```csharp
object locker = new object(); // class level private field
// rest of class code
lock (locker){
    // Do your query or insert here
}
```

Tutti gli accessi al database (letture, Scritture, aggiornamenti e così via) devono essere racchiusi con lo stesso blocco. È necessario prestare attenzione per evitare una situazione di deadlock assicurandosi che il lavoro all'interno della clausola di blocco venga mantenuto semplice e non chiami altri metodi che potrebbero anche assumere un blocco.

## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Ricette per i dati Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Accesso ai dati di Novell. Forms](~/xamarin-forms/data-cloud/data/databases.md)
