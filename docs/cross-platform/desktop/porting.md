---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: Indicazioni sulla portabilità di app desktop
description: Una semplice spiegazione della procedura di disaccoppiare i moduli di Windows esistenti o le applicazioni WPF per creare App multipiattaforma per l'esecuzione in macOS, iOS, Android, nonché UWP o Windows 10.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 4bf1dea170bd6b63209693963d54cc2e16163eea
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2019
ms.locfileid: "58071099"
---
# <a name="desktop-app-porting-guidance"></a>Indicazioni sulla portabilità di app desktop

Gran parte del codice dell'applicazione possono essere suddivise in una delle seguenti aree:

* Codice dell'interfaccia utente (ad es. finestre e pulsanti)
* 3 controlli di terze parti (ad es. grafici)
* Logica di business (ad es. regole di convalida)
* Accesso e archiviazione locale dei dati
* Servizi Web e accesso ai dati remoti

Per le applicazioni Windows Forms e WPF scritte con C# (o Visual Basic.NET) una quantità sorprendente della logica di business, accesso ai dati locali e il codice di servizi web può essere condivisi tra piattaforme.

## <a name="net-portability-analyzer"></a>.NET portability Analyzer

Visual Studio 2017 e versioni successive supportano il [.NET Portability Analyzer](https://docs.microsoft.com/dotnet/articles/standard/portability-analyzer) ([download per Windows](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)) che può esaminare le applicazioni esistenti e stabilire quanto codice può essere trasferita "così com'è" loro piattaforme. Sono disponibili informazioni su di essa da questo [video di Channel 9](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer).

È inoltre disponibile uno strumento da riga di comando può essere scaricato dal [Portability Analyzer in GitHub](https://github.com/Microsoft/dotnet-apiport) e usato per fornire gli stessi report.

## <a name="x-of-my-code-is-portable-what-next"></a>"x % del mio codice sia portabile. Come procedere?"

Probabilmente l'analizzatore Mostra una grande parte del codice sia portabile, ma esiste senz'altro sarà alcune parti di ogni app che _non è possibile_ essere spostati in altre piattaforme.

Diversi blocchi di codice probabilmente rientra in uno di questi bucket, illustrato in dettaglio più avanti:

* Generiche riutilizzabili di codice portabile
* Codice che richiede modifiche
* Codice non portabile e richiede la riscrittura

### <a name="re-useable-portable-code"></a>Generiche riutilizzabili di codice portabile

Il codice .NET scritto rispetto alle API disponibile in tutte le piattaforme può essere eseguito multipiattaforma senza modificato. In teoria, è possibile spostare tutto il codice in una libreria di classi portabile, libreria condiviso o libreria .NET Standard e quindi testarla all'interno di un'app esistente.

Tale libreria condivisa può quindi essere aggiunti ai progetti di applicazione per altre piattaforme (ad esempio Android, iOS, macOS).

### <a name="code-that-requires-changes"></a>Codice che richiede modifiche

Alcune API .NET potrebbero non essere disponibili in tutte le piattaforme. Se queste API esistono nel codice, è necessario riscrivere tali sezioni per usare le API multipiattaforma.

Alcuni esempi sono utilizzano le API di Reflection che sono disponibili in .NET 4.6, ma non sono disponibili in tutte le piattaforme.

Dopo avere scritto nuovamente il codice che usa API portabili, deve essere in grado di creare un pacchetto di codice in una libreria condivisa ed eseguirne il test all'interno di un'app esistente.

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>Codice non portabile e richiede la riscrittura

Esempi di codice che non è probabile che essere multi-piattaforma:

- **Interfaccia utente** : le schermate di Windows Forms o WPF non possono essere usate in progetti su Android o iOS, ad esempio. L'interfaccia utente dovranno essere riscritto usando questo [confronto tra i controlli](~/cross-platform/desktop/controls/index.md) come riferimento.

- **Archiviazione specifiche della piattaforma** -codice che si basa su una tecnologia specifica della piattaforma (ad esempio, un database di SQL Server Express locale). È necessario riscrivere questa usando un'alternativa più piattaforme (ad esempio SQLite per il motore di database).
Alcune operazioni del file system potrebbe essere necessario anche essere modificato, in quanto piattaforma UWP offre API leggermente diverse per Android e iOS (ad es. alcuni file System sono tra maiuscole e minuscole e ad altri utenti non sono).

- **componenti di terze parti 3rd** : verifica se i componenti di terze parti 3rd nelle applicazioni sono disponibili in altre piattaforme. Altri, ad esempio i pacchetti NuGet non visivo, potrebbe essere disponibile ma ad altri utenti (in particolare visual controlli, ad esempio lettori multimediali o grafici)

## <a name="tips-for-making-code-portable"></a>Suggerimenti per rendere portabile un codice

- **Inserimento di dipendenze** – fornire implementazioni diverse per ogni piattaforma, e

- **Approccio a più livelli** : indica se MVVM, MVC, MVP o un altro modello che consente di separare il codice portabile dal codice specifico della piattaforma.

- **Messaggistica** – è possibile usare un passaggio di messaggi nel codice deprovisioning accoppiare le interazioni tra le diverse parti dell'applicazione.
