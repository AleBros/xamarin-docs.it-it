---
title: Configurazione di SQLite in xamarin. IOS
description: Questo documento descrive come determinare il percorso per un file di database SQLite in un'applicazione xamarin. IOS. Questi concetti sono rilevanti, indipendentemente dal meccanismo di accesso di dati selezionato.
ms.prod: xamarin
ms.assetid: E5582F4B-AD74-420F-9E6D-B07CFB420B3A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: f170aa753ff490b75ab66ac858051516935876fe
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241266"
---
# <a name="configuring-sqlite-in-xamarinios"></a>Configurazione di SQLite in xamarin. IOS

Per l'uso di SQLite nell'applicazione xamarin. IOS è necessario determinare il percorso di file corretto per il file di database.

## <a name="database-file-path"></a>Percorso File di database

Indipendentemente dal metodo di accesso dei dati è usare, è necessario creare un file di database prima che i dati possono essere archiviati con SQLite. A seconda di quale piattaforma di destinazione è il percorso del file sarà diverso. Per iOS è possibile utilizzare la classe di ambiente per costruire un percorso valido, come illustrato nel frammento di codice seguente:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

Ci sono altri aspetti da prendere in considerazione prima di decidere dove archiviare il file di database. In iOS è possibile utilizzare il database da backup automaticamente (o meno).

Se si vuole usare un percorso diverso in ogni piattaforma nell'applicazione multipiattaforma: è possibile usare una direttiva del compilatore come illustrato per generare un percorso diverso per ogni piattaforma:

```csharp
var sqliteFilename = "MyDatabase.db3";
#if __ANDROID__
// Just use whatever directory SpecialFolder.Personal returns
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
// we need to put in /Library/ on iOS5.1+ to meet Apple's iCloud terms
// (they don't want non-user-generated data in Documents)
string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder instead
#endif
var path = Path.Combine (libraryPath, sqliteFilename);
```

Vedere la [funziona con il File System](~/ios/app-fundamentals/file-system.md) per altre informazioni su quali percorsi di file da utilizzare in iOS. Vedere le [compilazione di applicazioni della piattaforma incrociata](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento per altre informazioni sull'uso di direttive del compilatore per scrivere il codice specifico per ogni piattaforma.

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
- [iOS recipe di dati](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
