---
title: Configurazione
ms.prod: xamarin
ms.assetid: 44526226-4E4E-4FFF-9A16-CA7B1E01BB8F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/11/2016
ms.openlocfilehash: d418cb174861b962060757d4d1e0914e82ecd6fb
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119800"
---
# <a name="configuration"></a>Configurazione

Per l'uso di SQLite nell'applicazione xamarin. Android è necessario determinare il percorso di file corretto per il file di database.

## <a name="database-file-path"></a>Percorso File di database

Indipendentemente dal metodo di accesso dei dati è usare, è necessario creare un file di database prima che i dati possono essere archiviati con SQLite. A seconda di quale piattaforma di destinazione è il percorso del file sarà diverso. Per Android è possibile utilizzare la classe di ambiente per costruire un percorso valido, come illustrato nel frammento di codice seguente:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

Ci sono altri aspetti da prendere in considerazione prima di decidere dove archiviare il file di database. In Android, ad esempio, è possibile scegliere se usare l'archiviazione interna o esterna.

Se si vuole usare un percorso diverso in ogni piattaforma nell'applicazione multipiattaforma: è possibile usare una direttiva del compilatore come illustrato per generare un percorso diverso per ogni piattaforma:

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

Per suggerimenti sull'utilizzo del file system in Android, vedere la [Sfoglia file](https://github.com/xamarin/recipes/tree/master/Recipes/android/data/files/browse_files) recipe. Vedere le [compilazione di applicazioni della piattaforma incrociata](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento per altre informazioni sull'uso di direttive del compilatore per scrivere il codice specifico per ogni piattaforma.

## <a name="threading"></a>Threading

Non si utilizzino la stessa connessione di database SQLite attraverso più thread. Prestare attenzione aprire, utilizzare e quindi chiudere tutte le connessioni create sullo stesso thread.

Per assicurarsi che il codice non tenti di accedere al database SQLite da più thread contemporaneamente, attivare manualmente un blocco ogni volta che si desidera accedere al database, simile al seguente:

```csharp
object locker = new object(); // class level private field
// rest of class code
lock (locker){
    // Do your query or insert here
}
```

L'accesso al database (letture, scritture, aggiornamenti e così via) deve essere eseguito il wrapping con lo stesso blocco. Prestare attenzione per evitare una situazione di deadlock, garantendo che il lavoro all'interno della clausola di blocco viene mantenuto semplice e non chiamare altri metodi che potrebbero anche richiedere un blocco.


## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzate (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Ricette dei dati Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
