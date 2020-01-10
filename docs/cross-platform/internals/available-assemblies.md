---
title: Assembly disponibili
description: Questo documento elenca gli assembly disponibili per l'uso in Novell. iOS, Novell. Android e Novell. Mac. Sono inoltre disponibili collegamenti alla documentazione relativa alle librerie di .NET Standard e alle librerie di classi portabili.
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 31066d09b1e753dd054a6a908b626ca3edee008e
ms.sourcegitcommit: 04929b5ff4384ca807727bec7c0467111a7eb283
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867605"
---
# <a name="available-assemblies"></a>Assembly disponibili

Novell. iOS, Novell. Android e Novell. Mac vengono forniti con più di una decina di assembly. Proprio come Silverlight è un subset esteso degli assembly .NET desktop, Novell Platforms è anche un subset esteso di diversi assembly .NET per Silverlight e desktop.

Le piattaforme Novell non sono compatibili con l'interfaccia ABI con gli assembly esistenti compilati per un profilo diverso. È necessario ricompilare il codice sorgente per generare assembly destinati al profilo corretto (proprio come è necessario ricompilare il codice sorgente per Silverlight e .NET 3,5 separatamente).

Le applicazioni Novell. Mac possono essere compilate in tre modalità: una che usa il profilo mobile curato di Novell, il Framework Novell. Mac .NET 4,5 che consente di definire come destinazione gli assembly desktop completi esistenti e uno non supportato che usa l'API .NET disponibile in un sistema mono installazione. Per ulteriori informazioni, vedere la documentazione sui [Framework di destinazione](~/mac/platform/target-framework.md) .

## <a name="net-standard-libraries"></a>Librerie di .NET Standard

Oltre alle associazioni iOS, Android e Mac, i progetti Novell possono utilizzare le [librerie .NET standard](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="portable-class-libraries"></a>Librerie di classi portabili

I progetti Novell possono inoltre utilizzare [librerie di classi](~/cross-platform/app-fundamentals/pcl.md)portabili .NET, anche se questa tecnologia viene deprecata a favore di .NET standard.

## <a name="supported-assemblies"></a>Assembly supportati

Questi sono gli assembly disponibili in **Gestione riferimenti > assembly > Framework** (Visual Studio 2017) e **modifica riferimenti > pacchetti** (Visual Studio per Mac) e la loro compatibilità con le piattaforme Novell.

> [!div class="mx-tdCol2BreakAll"]
> |Assembly|Compatibilità API|Xamarin iOS|Xamarin Android|Xamarin Mac|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |l18N.dll|Include CJK, Medio Oriente, altro, raro, ovest|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |Microsoft.CSharp.dll| |![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |Mono.CSharp.dll| |![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |Mono.Data.Sqlite.dll|Provider ADO.NET per SQLite; vedere limitazioni.|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |Mono.Data.Tds.dll|Supporto del protocollo TDS; utilizzato per il supporto [System. Data. SqlClient](xref:System.Data.SqlClient) all'interno di [System. Data](xref:System.Data).|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |Mono.Dynamic.&#8203;Interpreter.dll| |![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")| | |
> |Mono.Security.dll|API crittografiche.|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |monotouch.dll|Questo assembly contiene l' C# associazione all'API CocoaTouch. Questa operazione è disponibile solo nei progetti iOS classici.|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")| | |
> |MonoTouch.&#8203;Dialog-1.dll| |![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")| | |
> |MonoTouch.&#8203;NUnitLite.dll| |![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")| | |
> |mscorlib.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |OpenTK-1.0.dll|API orientate agli oggetti OpenGL/OpenAL, estese per fornire il supporto per dispositivi iPhone.|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), più tipi degli spazi dei nomi seguenti:<br />System.Collections.Specialized<br />System.&#8203;ComponentModel<br />System.ComponentModel.Design<br />System.Diagnostics<br />System.IO<br />System.IO.Compression<br />System.IO.Compression.FileSystem<br />System.Net<br />System.Net.Cache<br />System.Net.Mail<br />System.Net.Mime<br />System.Net.&#8203;NetworkInformation<br />System.Net.Security<br />System.Net.Sockets<br />System.Runtime.&#8203;InteropServices<br />System.Runtime.Versioning<br />System.Security.&#8203;AccessControl<br />System.Security.Authentication<br />System.Security.&#8203;Cryptography<br />System.Security.Permissions<br />System.Threading<br />System.Timers|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.&#8203;ComponentModel.&#8203;Composition.dll| |![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.&#8203;ComponentModel.&#8203;DataAnnotations.dll| |![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.Core.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.Data.dll|[.Net 3,5](https://msdn.microsoft.com/library/ms229335.aspx) , con [alcune funzionalità rimosse](~/ios/data-cloud/system.data.md).|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.Data.&#8203;Services.&#8203;Client.dll|Client oData completo.|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.IO.&#8203;Compression| |![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.IO.&#8203;Compression.&#8203;FileSystem| |![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.Json.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.Net.&#8203;Http.dll| |![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.&#8203;Numerics.dll| |![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.Runtime.&#8203;Serialization.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.&#8203;ServiceModel.dll|Stack WCF come presente in [Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.&#8203;ServiceModel.&#8203;Internals.dll| |![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.&#8203;ServiceModel.&#8203;Web.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), più tipi degli spazi dei nomi seguenti: <br />System<br />System.ServiceModel.Channels<br />System.ServiceModel.Description<br />System.ServiceModel.Web|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.&#8203;Transactions.dll|[.Net 3,5](https://msdn.microsoft.com/library/ms229335.aspx); parte del supporto [System. Data](~/ios/data-cloud/system.data.md) .|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.Web.&#8203;Services.dll|Servizi Web di base dal profilo .NET 3,5, con le funzionalità Server rimosse.|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.&#8203;Windows.dll| |![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.&#8203;Xml.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.Xml.&#8203;Linq.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.Xml.Serialization.dll| |![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")|![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")|![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |Xamarin.iOS.dll|Questo assembly contiene l' C# associazione all'API CocoaTouch. Viene usato solo nei progetti iOS unificati.|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")| | |
> |Java.Interop.dll| | |![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")| |
> |Mono.Android.dll| | |![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")| |
> |Mono.Android.&#8203;Export.dll| | |![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")| |
> |Mono.Posix.dll| | |![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")| |
> |System.&#8203;EnterpriseServices.dll| | |![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")| |
> |Xamarin.Android.&#8203;NUnitLite.dll| | |![Novell. Android supportato](~/media/shared/yes.png "Novell. Android supportato")| |
> |Mono.CompilerServices.&#8203;SymbolWriter.dll|Per i writer del compilatore.| | |![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |Xamarin.Mac.dll| | | |![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|
> |System.&#8203;Drawing.dll|System. Drawing non è supportato nell'API unificata per Novell. Mac, .NET 4,5 o i Framework per dispositivi mobili. È possibile aggiungere il supporto System. Drawing a iOS e macOS usando la libreria [sysdrawing-CoreGraphics](https://github.com/mono/sysdrawing-coregraphics)|![Novell. iOS supportato](~/media/shared/yes.png "Novell. iOS supportato")| |![Novell. Mac supportato](~/media/shared/yes.png "Novell. Mac supportato")|