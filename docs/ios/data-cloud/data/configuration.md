---
title: Configurazione
ms.topic: article
ms.prod: xamarin
ms.assetid: 44526226-4E4E-4FFF-9A16-CA7B1E01BB8F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: 804fc3ae6fa969859520cd889886f3cab66c60c9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="configuration"></a>Configurazione

Per l'utilizzo di SQLite xamarin nell'applicazione in uso, è necessario determinare il percorso del file corretto per il file del database.

## <a name="database-file-path"></a>Percorso File di database

Indipendentemente dal fatto il metodo di accesso ai dati da utilizzare, è necessario creare un file di database prima di dati possono essere archiviati con SQLite. A seconda di quale piattaforma di destinazione è il percorso del file sarà diverso. Per iOS è possibile utilizzare la classe di ambiente per costruire un percorso valido, come illustrato nel frammento di codice seguente:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

Ci sono altri aspetti da prendere in considerazione per decidere dove archiviare il file di database. In iOS è il database da backup automaticamente (o meno).

Se si desidera utilizzare un percorso diverso in ogni piattaforma dell'applicazione su più piattaforme è possibile utilizzare una direttiva del compilatore come illustrato per generare un percorso diverso per ogni piattaforma:

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

Consultare la [funziona con il File System](~/ios/app-fundamentals/file-system.md) per ulteriori informazioni su quali percorsi di file da utilizzare in iOS. Vedere il [compilazione di applicazioni della piattaforma incrociata](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) per ulteriori informazioni sull'utilizzo delle direttive del compilatore per scrivere codice specifico per ogni piattaforma.

## <a name="threading"></a>Threading

Utilizzare la stessa connessione database SQLite non tra più thread. Assicurarsi di aprire, utilizzare e quindi chiudere tutte le connessioni create sullo stesso thread.

Per garantire che il codice non tenti di accedere al database SQLite da più thread contemporaneamente, attivare manualmente un blocco ogni volta che si desidera accedere al database, simile al seguente:

```csharp
object locker = new object(); // class level private field
// rest of class code
lock (locker){
    // Do your query or insert here
}
```

L'accesso al database (letture, scritture, gli aggiornamenti e così via) deve essere eseguito il wrapping con lo stesso blocco. Prestare attenzione per evitare una situazione di deadlock, verificare che il lavoro all'interno della clausola di blocco viene mantenuto semplice e non chiamare altri metodi che è anche possano acquisire un blocco.


## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzate (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS dati ricette](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
