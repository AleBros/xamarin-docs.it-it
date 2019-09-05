---
title: Assembly supportati da Novell per tvOS
description: Per chiarire le funzionalità disponibili per le applicazioni tvOS, questo documento fornisce un elenco di assembly supportati da Novell per lo sviluppo di tvOS.
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/07/2016
ms.openlocfilehash: 193f4a445e21416abf2fd6279cdc18228e16c985
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283619"
---
# <a name="assemblies-supported-by-xamarin-for-tvos"></a>Assembly supportati da Novell per tvOS

## <a name="supported-assemblies"></a>Assembly supportati

Questo è un elenco degli assembly supportati da Novell per le app Novell. tvOS. L'elenco dettagliato è riportato di seguito.  Alcune omissioni significative includono `System.EnterpriseServices`lo stack ASP.NET e Windows. Forms.

|Assembly|Aggiunto|Compatibilità API|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|Per i writer del compilatore.|
|Mono.Data.Sqlite.dll|1.2|Provider ADO.NET per SQLite; vedere [limitazioni](~/ios/data-cloud/system.data.md).|
|Mono.Data.Tds.dll|1.2|Supporto del protocollo TDS; utilizzato per il supporto [System. Data. SqlClient](xref:System.Data.SqlClient) all'interno di [System. Data](~/ios/data-cloud/system.data.md).|
|Mono.Security.dll|1.0|API crittografiche.|
|monotouch.dll|1.0|Questo assembly contiene l' [ C# associazione all'API CocoaTouch](https://docs.microsoft.com/dotnet/api/?view=xamarinios-10.8).|
|mscorlib.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|OpenTK.dll|1.0|API orientate agli oggetti OpenGL/OpenAL, [estese per fornire il supporto per dispositivi iPhone](xref:OpenGLES).|
|System.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), più tipi degli spazi dei nomi seguenti: <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System.ComponentModel.Design</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>System.Net.Cache</li> <li>System.Net.Mail</li> <li>System.Net.Mime</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>System.Timers</li></ul>|
|System.Core.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Data.dll|1.2|[.Net 3,5](https://msdn.microsoft.com/library/ms229335.aspx), [con alcune funzionalità rimosse](~/ios/data-cloud/system.data.md).|
|System.Data.Service.Client.dll|3.x|Client oData completo.|
|System.Drawing|1.0|API System. Drawing: solo API classica.<br />_System. Drawing non è supportato nell'API unificata per i Framework Novell. Mac .NET 4,5 o per dispositivi mobili._|
|System.Json.dll|1.1|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Runtime.Serialization.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.dll|1.1|Stack [WCF](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) come presente in [Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.Web.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), più tipi degli spazi dei nomi seguenti: <ul><li>System</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.Net 3,5](https://msdn.microsoft.com/library/ms229335.aspx); parte del supporto [System. Data](https://docs.microsoft.com/xamarin/ios/data-cloud/system.data) .|
|System.Web.Services|1.1|[Servizi Web di base](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) dal profilo .NET 3,5, con le funzionalità Server rimosse.|
|System.Xml.dll|1.0|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|
|System.Xml.Linq.dll|1.0|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|

<a name="Summary" />

## <a name="portable-class-libraries"></a>Librerie di classi portabili

Oltre ai binding Mac, Novell. tvOS può utilizzare le librerie di [classi](~/cross-platform/app-fundamentals/pcl.md)portabili .NET.

## <a name="related-links"></a>Collegamenti correlati

- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
