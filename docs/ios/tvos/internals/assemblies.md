---
title: Gli assembly supportati da Xamarin per tvOS
description: Per aiutare a chiarire le funzionalità disponibili alle applicazioni di tvOS, questo documento fornisce un elenco di assembly supportato da Xamarin per lo sviluppo di tvOS.
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: 3b5eae4d218fdc66f7f99f0aab8aa32af3cbabf4
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667362"
---
# <a name="assemblies-supported-by-xamarin-for-tvos"></a>Gli assembly supportati da Xamarin per tvOS

## <a name="supported-assemblies"></a>Assembly supportati

Questo è un elenco degli assembly supportati da Xamarin per le app xamarin. tvos. L'elenco dettagliato di questi è elencato di seguito.  Includono alcune importanti omissioni `System.EnterpriseServices`, lo stack di ASP.NET e Windows. Forms.

|Assembly|Aggiunto|Compatibilità con le API|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|Writer di compilatori.|
|Mono.Data.Sqlite.dll|1.2|Provider ADO.NET per SQLite; visualizzare [limitazioni](~/ios/data-cloud/system.data.md).|
|Mono.Data.Tds.dll|1.2|Supporto del protocollo TDS; usato per [SqlClient](xref:System.Data.SqlClient) supportano entro [System. Data](~/ios/data-cloud/system.data.md).|
|Mono.Security.dll|1.0|API di crittografia.|
|monotouch.dll|1.0|Questo assembly contiene le [c# l'associazione all'API CocoaTouch](https://docs.microsoft.com/dotnet/api/?view=xamarinios-10.8).|
|mscorlib.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|OpenTK.dll|1.0|L'oggetto OpenGL/OpenAL orientata ai servizi API, [esteso per fornire supporto per dispositivi iPhone](https://developer.xamarin.com/api/namespace/OpenGLES/).|
|System.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), oltre a tipi degli spazi dei nomi seguenti: <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System.ComponentModel.Design</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>System.Net.Cache</li> <li>System.Net.Mail</li> <li>System.Net.Mime</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>System.Timers</li></ul>|
|System.Core.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Data.dll|1.2|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx), [con alcune funzionalità rimosse](~/ios/data-cloud/system.data.md).|
|System.Data.Service.Client.dll|3.x|Client oData completo.|
|System.Drawing|1.0|System. Drawing API - solo l'API classica.<br />_System. Drawing non è supportata nell'API unificata di xamarin. Mac .NET 4.5 o Framework per dispositivi mobili._|
|System.Json.dll|1.1|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Runtime.Serialization.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.dll|1.1|[WCF](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) stack presente nel [Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.Web.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), oltre a tipi degli spazi dei nomi seguenti: <ul><li>System</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx); in parte [System. Data](https://docs.microsoft.com/xamarin/ios/data-cloud/system.data) supportano.|
|System.Web.Services|1.1|[Servizi Web di base](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) dal profilo .NET 3.5, con le funzionalità server rimossi.|
|System.Xml.dll|1.0|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|
|System.Xml.Linq.dll|1.0|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|

<a name="Summary" />

## <a name="portable-class-libraries"></a>Librerie di classi portabili

Oltre alle associazioni Mac, è possibile utilizzare xamarin. tvos [librerie di classi portabile .NET](~/cross-platform/app-fundamentals/pcl.md).

## <a name="related-links"></a>Collegamenti correlati

- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
