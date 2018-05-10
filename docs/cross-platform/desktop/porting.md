---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: Guida al porting app desktop
description: Una spiegazione semplice come separare i moduli di Windows esistenti o App WPF per creare App multipiattaforma per l'esecuzione su Mac OS, iOS, Android, nonché UWP/Windows 10.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: cdf70065893a4da268f628369fa94336291ead1f
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="desktop-app-porting-guidance"></a>Guida al porting app desktop

Gran parte del codice dell'applicazione possono essere suddivisi in categorie in una delle seguenti aree:

* Codice dell'interfaccia utente (ad es. Windows e i pulsanti)
* controlli di terze parti di 3 (ad es. grafici)
* Logica di business (ad es. regole di convalida)
* L'accesso e l'archiviazione di dati locali
* Servizi Web e accesso a dati remoti

Per Windows Form e WPF applicazioni scritte con c# (o Visual Basic.NET) una considerevole quantità di logica di business, accedere a dati locali e codice di servizi web può essere condivise tra le piattaforme.

## <a name="net-portability-analyzer"></a>.NET portability Analyzer

Supporto di Visual Studio 2015 e 2017 il [.NET Portability Analyzer](https://docs.microsoft.com/en-us/dotnet/articles/standard/portability-analyzer) ([scaricare per Windows](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)) che può esaminare le applicazioni esistenti e richiedere di specificare la quantità di codice possono essere trasferite "così com'è" ad altre piattaforme . Altre informazioni relative a essa da questo [video di Channel 9](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer).

È inoltre disponibile uno strumento da riga di comando può essere scaricato dal [Portability Analyzer su GitHub](https://github.com/Microsoft/dotnet-apiport) e consentono di fornire agli stessi report.

## <a name="x-of-my-code-is-portable-what-next"></a>"x % di codice è portabile. Come procedere?"

Probabilmente l'analizzatore viene illustrato un grande parte del codice sia portabile, ma è sicuramente occorra da alcune parti di tutte le applicazioni che _Impossibile_ essere spostati in altre piattaforme.

Diversi blocchi di codice verranno probabilmente rientrano in uno di questi bucket illustrati in dettaglio più avanti:

* Codice portabile generiche riutilizzabili
* Codice che richiede le modifiche
* Codice che non è portabile e richiede la riscrittura

### <a name="re-useable-portable-code"></a>Codice portabile generiche riutilizzabili

È possibile portare il codice .NET viene scritto usando API disponibile in tutte le piattaforme multipiattaforma invariata. In teoria, sarà possibile spostare tutto il codice in una libreria di classi portabile, una libreria condivisa o la libreria Standard di .NET e quindi eseguirne il test all'interno dell'app esistente.

Tale libreria condivisa può quindi essere aggiunta per i progetti di applicazione per altre piattaforme (ad esempio Android, iOS, Mac OS).

### <a name="code-that-requires-changes"></a>Codice che richiede le modifiche

Alcune API .NET potrebbe non essere disponibili in tutte le piattaforme. Se queste API presenti nel codice, è necessario scrivere nuovamente tali sezioni per usare le API multipiattaforma.

Alcuni esempi sono l'utilizzo di API di Reflection che sono disponibili in .NET 4.6, ma non sono disponibili in tutte le piattaforme.

Dopo aver nuovamente scritto il codice che usa le API portabile, deve essere in grado di creare un pacchetto di codice in una libreria condivisa ed eseguirne il test all'interno dell'app esistente.

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>Codice che non è portabile e richiede la riscrittura

Esempi di codice che non è probabilmente multipiattaforma includono:

- **Interfaccia utente** – schermate WPF o Windows Form non possono essere usate nei progetti in Android o iOS, ad esempio. L'interfaccia utente dovrà essere riscritto utilizzando questa [confronto controlli](~/cross-platform/desktop/controls/index.md) come riferimento.

- **Archiviazione specifiche per la piattaforma** -codice che si basa su una tecnologia specifica della piattaforma (ad esempio un database di SQL Server Express locale). È necessario scrivere nuovamente questo utilizzando un'alternativa più piattaforme (ad esempio, SQLite per il motore di database).
Alcune operazioni del file system potrebbe essere necessario anche essere modificate, poiché UWP è leggermente diverse API per Android e iOS (ad es. alcuni file System alcuna distinzione tra maiuscole e altri utenti non sono).

- **componenti di terze parti 3rd** : verifica se i componenti di terze parti 3rd nelle applicazioni sono disponibili in altre piattaforme. Altri, ad esempio pacchetti di NuGet non visivi, potrebbero essere disponibili, ma altri utenti (soprattutto visual controlli, ad esempio lettori multimediali o grafici)

## <a name="tips-for-making-code-portable"></a>Suggerimenti per rendere portabile un codice

- **Inserimento di dipendenze** – fornire implementazioni diverse per ogni piattaforma, e

- **Approccio a più livelli** : indica se MVVM, MVC, MVP o alcuni altri modelli che consente di separare il codice portabile dal codice specifico della piattaforma.

- **Messaggistica** – è possibile utilizzare un passaggio di messaggi nel codice per deallocare accoppiato le interazioni tra le diverse parti dell'applicazione.
