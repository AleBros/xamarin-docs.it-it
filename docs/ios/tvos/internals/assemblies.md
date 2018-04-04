---
title: Assembly
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: 7d0ee27cfa2ae153ef481f943402f5fcfc5d04e4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="assemblies"></a>Assembly

## <a name="supported-assemblies"></a>Assembly supportati

Questo è un elenco degli assembly supportati da Xamarin per le app Xamarin.tvOS. L'elenco dettagliato di questi è elencato di seguito.  Alcune importanti omissioni includono `System.EnterpriseServices`, stack ASP.NET e Windows. Forms.

|Assembly|Aggiunto|Compatibilità con le API|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|Writer di compilatori.|
|Mono.Data.Sqlite.dll|1.2|Provider ADO.NET per SQLite; vedere [limitazioni](~/ios/data-cloud/system.data.md).|
|Mono.Data.Tds.dll|1.2|Supporto del protocollo TDS; utilizzato per [SqlClient](https://developer.xamarin.com/api/namespace/System.Data.SqlClient/) supportano all'interno di [System. Data](~/ios/data-cloud/system.data.md).|
|Mono.Security.dll|1.0|API di crittografia.|
|monotouch.dll|1.0|Questo assembly contiene le [c# associazione all'API CocoaTouch](https://developer.xamarin.com/api/root/ios-unified/).|
|mscorlib.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|OpenTK.dll|1.0|L'oggetto OpenGL/OpenAL orientata ai servizi API [estesa per supportare il dispositivo iPhone](https://developer.xamarin.com/api/namespace/OpenGLES/).|
|System.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx), nonché i tipi degli spazi dei nomi seguenti: <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System.ComponentModel.Design</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>System.Net.Cache</li> <li>System.Net.Mail</li> <li>System.Net.Mime</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>System.Timers</li></ul>|
|System.Core.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Data.dll|1.2|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx), [con alcune funzionalità rimosse](~/ios/data-cloud/system.data.md).|
|System.Data.Service.Client.dll|3.x|Client oData completo.|
|System.Drawing|1.0|System. Drawing API - solo API classica.<br />_System. Drawing non è supportata nell'API unificata per le Xamarin.Mac 4.5 di .NET Framework per dispositivi mobili._|
|System.Json.dll|1.1|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Runtime.Serialization.dll|?|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.dll|1.1|[WCF](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) dello stack come presente nella [Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.Web.dll|?|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx), nonché i tipi degli spazi dei nomi seguenti: <ul><li>System</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx); fa parte di [System. Data](https://docs.microsoft.com/xamarin/ios/data-cloud/system.data) supportano.|
|System.Web.Services|1.1|[Servizi Web Basic](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) dal profilo .NET 3.5, con le funzionalità server rimosso.|
|System.Xml.dll|1.0|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|
|System.Xml.Linq.dll|1.0|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|

<a name="Summary" />

## <a name="portable-class-libraries"></a>Librerie di classi portabili

Oltre alle associazioni Mac, è possibile utilizzare Xamarin.tvOS [librerie di classi portabili .NET](~/cross-platform/app-fundamentals/pcl.md).



## <a name="related-links"></a>Collegamenti correlati

- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
