---
title: Assembly disponibili
description: Assembly disponibili in xamarin. IOS, xamarin e Xamarin.Mac
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/13/2018
ms.openlocfilehash: a80a23e8c3a41b7a06e1bbcb33d171ed2dc30fd2
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2018
---
# <a name="available-assemblies"></a>Assembly disponibili

_Assembly disponibili in xamarin. IOS, xamarin e Xamarin.Mac_

Xamarin. IOS, xamarin e Xamarin.Mac tutti forniti con decine assembly. Come Silverlight è un sottoinsieme degli assembly .NET desktop esteso, Xamarin piattaforme è anche un sottoinsieme dei numerosi Silverlight e gli assembly .NET desktop esteso.

Piattaforme di Xamarin non sono ABI compatibile con gli assembly esistenti compilati per un profilo diverso. È necessario ricompilare il codice sorgente per generare gli assembly come destinazione il profilo corretto (esattamente come è necessario ricompilare il codice sorgente per Silverlight e .NET 3.5 come destinazione separatamente).

Applicazioni Xamarin.Mac possono essere compilate in tre modalità: una che usa Xamarin del curata relativa profilo per dispositivi mobili, Xamarin.Mac .NET 4.5 Framework che consente di destinazione gli assembly desktop completi esistenti e uno non supportato che usa l'API .NET è disponibile in un sistema Mono installazione. Per ulteriori informazioni, vedere il nostro [Framework di destinazione](~/mac/platform/target-framework.md) documentazione.


## <a name="net-standard-libraries"></a>Librerie standard di .NET

Oltre a Mac, iOS e Android associazioni, i progetti possono utilizzare Xamarin [librerie .NET Standard](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="portable-class-libraries"></a>Librerie di classi portabili
 
Possono usare anche i progetti di Xamarin [librerie di classi portabili .NET](~/cross-platform/app-fundamentals/pcl.md), sebbene questa tecnologia è stata deprecata a favore .NET Standard.

## <a name="supported-assemblies"></a>Assembly supportati

> [!div class="mx-tdCol2BreakAll"]
> |Assembly|Compatibilità con le API|Xamarin iOS|Xamarin Android|Xamarin Mac|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |✓|✓|✓|
> |l18N.dll|Include CJK, MidEast, altri, rari, ovest|✓|✓|✓|
> |Microsoft.CSharp.dll| |✓|✓|✓|
> |Mono.CSharp.dll| |✓|✓|✓|
> |Mono.Data.Sqlite.dll|Provider ADO.NET per SQLite; vedere le limitazioni.|✓|✓|✓|
> |Mono.Data.Tds.dll|Supporto del protocollo TDS; utilizzato per [SqlClient](https://developer.xamarin.com/api/namespace/System.Data.SqlClient/) supportano all'interno di [System. Data](https://developer.xamarin.com/api/namespace/System.Data/).|✓|✓|✓|
> |Mono.Dynamic.&#8203;Interpreter.dll| |✓| | |
> |Mono.Security.dll|API di crittografia.|✓|✓|✓|
> |monotouch.dll|Questo assembly contiene l'associazione all'API CocoaTouch c#. Questo è disponibile solo all'interno dei progetti iOS classica.|✓| | |
> |MonoTouch.&#8203;Dialog-1.dll| |✓| | |
> |MonoTouch.&#8203;NUnitLite.dll| |✓| | |
> |mscorlib.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |OpenTK-1.0.dll|L'oggetto OpenGL/OpenAL orientata ai servizi API estese per fornire il supporto dei dispositivi iPhone.|✓|✓|✓|
> |System.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), nonché i tipi degli spazi dei nomi seguenti:<br />System.Collections.Specialized<br />System.&#8203;ComponentModel<br />System.ComponentModel.Design<br />System.Diagnostics<br />System.IO<br />System.IO.Compression<br />System.IO.Compression.FileSystem<br />System.Net<br />System.Net.Cache<br />System.Net.Mail<br />System.Net.Mime<br />System.Net.&#8203;NetworkInformation<br />System.Net.Security<br />System.Net.Sockets<br />System.Runtime.&#8203;InteropServices<br />System.Runtime.Versioning<br />System.Security.&#8203;AccessControl<br />System.Security.Authentication<br />System.Security.&#8203;Cryptography<br />System.Security.Permissions<br />System.Threading<br />System.Timers|✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;Composition.dll| |✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;DataAnnotations.dll| |✓|✓|✓|
> |System.Core.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Data.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx) , con [alcune delle funzionalità rimosse](~/ios/data-cloud/system.data.md).|✓|✓|✓|
> |System.Data.&#8203;Services.&#8203;Client.dll|Client oData completo.|✓|✓|✓|
> |System.IO.&#8203;Compression| |✓|✓|✓|
> |System.IO.&#8203;Compression.&#8203;FileSystem| |✓|✓|✓|
> |System.Json.dll|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Net.&#8203;Http.dll| |✓|✓|✓|
> |System.&#8203;Numerics.dll| |✓|✓|✓|
> |System.Runtime.&#8203;Serialization.dll|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.dll|Stack WCF come presente nella [Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Internals.dll| |✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Web.dll|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx), nonché i tipi degli spazi dei nomi seguenti: <br />System<br />System.ServiceModel.Channels<br />System.ServiceModel.Description<br />System.ServiceModel.Web|✓|✓|✓|
> |System.&#8203;Transactions.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx); fa parte di [System. Data](~/ios/data-cloud/system.data.md) supportano.|✓|✓|✓|
> |System.Web.&#8203;Services.dll|Servizi Web di base del profilo di .NET 3.5, con le funzionalità server rimosso.|✓|✓|✓|
> |System.&#8203;Windows.dll| |✓|✓|✓|
> |System.&#8203;Xml.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.&#8203;Linq.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.Serialization.dll| |✓|✓|✓|
> |Xamarin.iOS.dll|Questo assembly contiene l'associazione all'API CocoaTouch c#. Viene utilizzato solo nei progetti iOS unificato.|✓| | |
> |Java.Interop.dll| | |✓| |
> |Mono.Android.dll| | |✓| |
> |Mono.Android.&#8203;Export.dll| | |✓| |
> |Mono.Posix.dll| | |✓| |
> |System.&#8203;EnterpriseServices.dll| | |✓| |
> |Xamarin.Android.&#8203;NUnitLite.dll| | |✓| |
> |Mono.CompilerServices.&#8203;SymbolWriter.dll|Writer di compilatori.| | |✓|
> |Xamarin.Mac.dll| | | |✓|
> |System.&#8203;Drawing.dll|System. Drawing API - solo API classica. System. Drawing non è supportata nell'API unificata per le Xamarin.Mac 4.5 di .NET Framework per dispositivi mobili. Può essere aggiunto il supporto di System. Drawing per iOS e OS X con il [sysdrawing coregraphics](https://github.com/mono/sysdrawing-coregraphics) libreria|✓| |✓|
