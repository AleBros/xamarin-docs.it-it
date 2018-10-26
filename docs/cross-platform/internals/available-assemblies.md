---
title: Assembly disponibili
description: Questo documento Elenca gli assembly disponibili per l'uso in xamarin. IOS, xamarin. Android e xamarin. Mac. Anche possibile collegare alla documentazione sulle librerie .NET Standard e librerie di classi portabili.
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
author: asb3993
ms.author: amburns
ms.date: 03/13/2018
ms.openlocfilehash: 213632ae26ae60797e39bc718a95057fb7238609
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113352"
---
# <a name="available-assemblies"></a>Assembly disponibili

Xamarin. IOS, xamarin. Android e xamarin. Mac tutti forniti con oltre una dozzina di assembly. Esattamente come Silverlight che rappresenta un sottoinsieme degli assembly .NET desktop, piattaforme Xamarin è anche un subset esteso di diversi Silverlight e desktop assembly .NET.

Piattaforme Xamarin non sono ABI compatibile con gli assembly esistenti compilati per un profilo diverso. È necessario ricompilare il codice sorgente per generare assembly di destinazione è il profilo corretto (esattamente come è necessario ricompilare il codice sorgente per impostare come destinazione Silverlight e .NET 3.5 separatamente).

Le applicazioni xamarin. Mac possono essere compilate in tre modalità: una che usa Xamarin di curata profilo per dispositivi mobili xamarin. Mac .NET 4.5 Framework che consente all'utente di destinazione gli assembly desktop completa esistenti e uno non supportato che usa l'API .NET è disponibile in un sistema Mono installazione. Per altre informazioni, vedere la [Framework di destinazione](~/mac/platform/target-framework.md) documentazione.

## <a name="net-standard-libraries"></a>Librerie .NET standard

Oltre a iOS, Android e Mac, associazioni, Xamarin possono utilizzare i progetti [librerie .NET Standard](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="portable-class-libraries"></a>Librerie di classi portabili

Progetti Xamarin possono anche usare [librerie di classi portabile .NET](~/cross-platform/app-fundamentals/pcl.md), anche se questa tecnologia è stata deprecata a favore di .NET Standard.

## <a name="supported-assemblies"></a>Assembly supportati

Questi sono gli assembly disponibile nel **gestione riferimenti > assembly > Framework** (Visual Studio 2017) e **Modifica riferimenti > pacchetti** (Visual Studio per Mac) e la compatibilità con le piattaforme di Xamarin.

> [!div class="mx-tdCol2BreakAll"]
> |Assembly|Compatibilità con le API|Xamarin iOS|Xamarin Android|Xamarin Mac|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |✓|✓|✓|
> |l18N.dll|Include CJK, MidEast, altri non comune, rara, occidentale|✓|✓|✓|
> |Microsoft.CSharp.dll| |✓|✓|✓|
> |Mono.CSharp.dll| |✓|✓|✓|
> |Mono.Data.Sqlite.dll|Provider ADO.NET per SQLite; vedere le limitazioni.|✓|✓|✓|
> |Mono.Data.Tds.dll|Supporto del protocollo TDS; usato per [SqlClient](xref:System.Data.SqlClient) supportano entro [System. Data](xref:System.Data).|✓|✓|✓|
> |Mono.Dynamic.&#8203;Interpreter.dll| |✓| | |
> |Mono.Security.dll|API di crittografia.|✓|✓|✓|
> |monotouch.dll|Questo assembly contiene l'associazione all'API cocoatouch usano c#. È disponibile solo all'interno di progetti iOS classici.|✓| | |
> |MonoTouch.&#8203;Dialog-1.dll| |✓| | |
> |MonoTouch.&#8203;NUnitLite.dll| |✓| | |
> |mscorlib.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |OpenTK-1.0.dll|L'oggetto OpenGL/OpenAL orientata ai servizi API estese per fornire supporto per dispositivi iPhone.|✓|✓|✓|
> |System.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), oltre a tipi degli spazi dei nomi seguenti:<br />System.Collections.Specialized<br />System.&#8203;ComponentModel<br />System.ComponentModel.Design<br />System.Diagnostics<br />System.IO<br />System.IO.Compression<br />System.IO.Compression.FileSystem<br />System.Net<br />System.Net.Cache<br />System.Net.Mail<br />System.Net.Mime<br />System.Net.&#8203;NetworkInformation<br />System.Net.Security<br />System.Net.Sockets<br />System.Runtime.&#8203;InteropServices<br />System.Runtime.Versioning<br />System.Security.&#8203;AccessControl<br />System.Security.Authentication<br />System.Security.&#8203;Cryptography<br />System.Security.Permissions<br />System.Threading<br />System.Timers|✓|✓|✓|
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
> |System.&#8203;ServiceModel.dll|Stack WCF come presente in [Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Internals.dll| |✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Web.dll|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx), oltre a tipi degli spazi dei nomi seguenti: <br />System<br />System.ServiceModel.Channels<br />System.ServiceModel.Description<br />System.ServiceModel.Web|✓|✓|✓|
> |System.&#8203;Transactions.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx); in parte [System. Data](~/ios/data-cloud/system.data.md) supportano.|✓|✓|✓|
> |System.Web.&#8203;Services.dll|Servizi Web di base dal profilo .NET 3.5, con le funzionalità server rimossi.|✓|✓|✓|
> |System.&#8203;Windows.dll| |✓|✓|✓|
> |System.&#8203;Xml.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.&#8203;Linq.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.Serialization.dll| |✓|✓|✓|
> |Xamarin.iOS.dll|Questo assembly contiene l'associazione all'API cocoatouch usano c#. Viene usato solo nei progetti iOS unificato.|✓| | |
> |Java.Interop.dll| | |✓| |
> |Mono.Android.dll| | |✓| |
> |Mono.Android.&#8203;Export.dll| | |✓| |
> |Mono.Posix.dll| | |✓| |
> |System.&#8203;EnterpriseServices.dll| | |✓| |
> |Xamarin.Android.&#8203;NUnitLite.dll| | |✓| |
> |Mono.CompilerServices.&#8203;SymbolWriter.dll|Writer di compilatori.| | |✓|
> |Xamarin.Mac.dll| | | |✓|
> |System.&#8203;Drawing.dll|System. Drawing non è supportata nell'API unificata di xamarin. Mac, di .NET 4.5 o Framework per dispositivi mobili. Può essere aggiunto il supporto di System. Drawing per iOS e macOS usando il [sysdrawing coregraphics](https://github.com/mono/sysdrawing-coregraphics) libreria|✓| |✓|
