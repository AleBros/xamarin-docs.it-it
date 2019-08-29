---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: Linee guida per la portabilità delle app desktop
description: Una semplice spiegazione di come separare le app esistenti di Windows Forms o WPF per creare app multipiattaforma da eseguire in macOS, iOS, Android e UWP/Windows 10.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: bdea1c472d95c86037056a2905679b43e12e0468
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120385"
---
# <a name="desktop-app-porting-guidance"></a>Linee guida per la portabilità delle app desktop

La maggior parte del codice dell'applicazione può essere suddivisa in una delle seguenti aree:

- Codice dell'interfaccia utente (ad esempio finestre e pulsanti)
- controlli di terze parti (ad esempio grafici
- Logica di business (ad esempio regole di convalida)
- Archiviazione e accesso ai dati locali
- Servizi Web e accesso ai dati remoti

Per le applicazioni Windows Forms e WPF scritte C# con (o Visual Basic.NET) una quantità sorprendente di logica di business, l'accesso ai dati locali e il codice dei servizi Web possono essere condivisi tra le diverse piattaforme.

## <a name="net-portability-analyzer"></a>.NET Portability Analyzer

Visual Studio 2017 e versioni successive supportano [.NET Portability Analyzer](https://docs.microsoft.com/dotnet/articles/standard/portability-analyzer) ([download per Windows](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)) che può esaminare le applicazioni esistenti e indicare la quantità di codice che è possibile trasferire "così come sono" ad altre piattaforme. Per ulteriori informazioni, vedere questo video di [Channel 9](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer).

È anche possibile scaricare uno strumento da riga di comando da Portability [Analyzer su GitHub](https://github.com/Microsoft/dotnet-apiport) e usarlo per fornire gli stessi report.

## <a name="x-of-my-code-is-portable-what-next"></a>"x% del codice è portabile. Operazioni successive

L'analizzatore Mostra che un'ampia parte del codice è portabile, ma è certo che alcune parti di ogni app _non possono_ essere spostate in altre piattaforme.

I diversi blocchi di codice probabilmente rientrano in uno di questi bucket, descritti in modo più dettagliato di seguito:

- Codice portabile riutilizzabile
- Codice che richiede modifiche
- Codice che non è portabile ed è necessario riscrivere

### <a name="re-useable-portable-code"></a>Codice portabile riutilizzabile

Il codice .NET scritto su API disponibili su tutte le piattaforme può essere eseguito senza modifiche multipiattaforma. Idealmente, sarà possibile spostare tutto questo codice in una libreria di classi portabile, in una libreria condivisa o in una libreria di .NET Standard, quindi testarla all'interno dell'app esistente.

Tale libreria condivisa può quindi essere aggiunta ai progetti di applicazione per altre piattaforme, ad esempio Android, iOS e macOS.

### <a name="code-that-requires-changes"></a>Codice che richiede modifiche

Alcune API .NET potrebbero non essere disponibili in tutte le piattaforme. Se queste API sono presenti nel codice, sarà necessario riscrivere tali sezioni per usare le API multipiattaforma.

Alcuni esempi includono l'uso di API di Reflection disponibili in .NET 4,6, ma non sono disponibili in tutte le piattaforme.

Dopo aver riscritto il codice usando le API portabili, sarà possibile creare un pacchetto di tale codice in una libreria condivisa e testarlo all'interno dell'app esistente.

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>Codice che non è portabile ed è necessario riscrivere

Esempi di codice che probabilmente non sono multipiattaforma includono:

- **Interfaccia utente** : le schermate Windows Forms o WPF non possono essere usate nei progetti in Android o iOS, ad esempio. L'interfaccia utente dovrà essere riscritta, usando questo [confronto dei controlli](~/cross-platform/desktop/controls/index.md) come riferimento.

- Codice di **archiviazione specifico della piattaforma** che si basa su una tecnologia specifica della piattaforma (ad esempio un database di SQL Server Express locale). È necessario riscriverlo usando un'alternativa multipiattaforma, ad esempio SQLite per il motore di database.
Potrebbe anche essere necessario modificare alcune operazioni di file system, perché UWP ha API leggermente diverse per Android e iOS, ad esempio Alcuni filesystem fanno distinzione tra maiuscole e minuscole e altri non lo sono.

- **componenti** di terze parti: controllare se i componenti di terze parti nelle applicazioni sono disponibili su altre piattaforme. Alcuni, ad esempio i pacchetti NuGet non visivi, potrebbero essere disponibili, ma altri, soprattutto controlli visivi come grafici o lettori multimediali.

## <a name="tips-for-making-code-portable"></a>Suggerimenti per rendere portabile il codice

- **Inserimento** delle dipendenze: fornisce implementazioni diverse per ogni piattaforma e

- **Approccio** a più livelli: se MVVM, MVC, MVP o un altro modello che consente di separare il codice portatile dal codice specifico della piattaforma.

- **Messaggistica** : è possibile usare il passaggio dei messaggi nel codice per separare le interazioni tra parti diverse dell'applicazione.
