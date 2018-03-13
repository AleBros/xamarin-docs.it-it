---
title: Assembly disponibili
description: Assembly disponibili in xamarin. IOS, xamarin e Xamarin.Mac
ms.topic: article
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 3a4e39582a389774e5d17327cefecb0f676adfc6
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="available-assemblies"></a>Assembly disponibili

_Assembly disponibili in xamarin. IOS, xamarin e Xamarin.Mac_

Xamarin. IOS, xamarin e Xamarin.Mac tutti forniti con decine assembly. Come Silverlight è un sottoinsieme degli assembly .NET desktop esteso, Xamarin piattaforme è anche un sottoinsieme dei numerosi Silverlight e gli assembly .NET desktop esteso.

Piattaforme di Xamarin non sono ABI compatibile con gli assembly esistenti compilati per un profilo diverso. È necessario ricompilare il codice sorgente per generare assembly di destinazione corretto. profilo (nello stesso modo in cui è necessario ricompilare il codice sorgente per interagire con Silverlight e .NET 3.5 separatamente).

Applicazioni Xamarin.Mac possono essere compilate in tre modalità: una che usa Xamarin del curata relativa profilo per dispositivi mobili, Xamarin.Mac .NET 4.5 Framework che consente di destinazione gli assembly desktop completi esistenti e uno non supportato che usa l'API .NET è disponibile in un sistema Mono installazione. Per ulteriori informazioni, vedere il nostro [Framework di destinazione](~/mac/platform/target-framework.md) documentazione.


## <a name="portable-class-libraries"></a>Librerie di classi portabili
 
Oltre a Mac, iOS e Android associazioni, piattaforme possono usare Xamarin [librerie di classi portabili .NET](~/cross-platform/app-fundamentals/pcl.md).

## <a name="supported-assemblies"></a>Assembly supportati

<style type="text/css"> .TG {bordo-Comprimi: Comprimi; border-spaziatura: 0;} td .tg {5px riempimento: 10px; border-larghezza: 1px; overflow: nascosto; word-interruzione: normal;} .tg th {font-weight: normal; 5px riempimento: 10px; border-larghezza: 1px; overflow: nascosto; word-interruzione: normal;} .tg .tg-zx05 {font-weight: bold; colore di sfondo: #d1d9dd; vertical-align: superiore} .tg .tg-w6qd {colore di sfondo: #91e2f4; text-align: center; vertical-align: superiore} .tg .tg-yw4l {vertical-align: inizio} .tg .tg-q3ci {colore di sfondo: #cfefa7; text-align: center; Vertical-align: superiore} .tg .tg-p7et {background-color: #cec0ec; text-align: center; vertical-align: inizio} </style>
<table class="tg">
  <tr>
    <th class="tg-zx05">Assembly</th>
    <th class="tg-zx05">Compatibilità con le API</th>
    <th class="tg-zx05">Xamarin.iOS</th>
    <th class="tg-zx05">Xamarin.Android</th>
    <th class="tg-zx05">Xamarin.Mac</th>
  </tr>
  <tr>
    <td class="tg-yw4l">FSharp.Core.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">l18N.dll</td>
    <td class="tg-yw4l">Include CJK, MidEast, altri, rari, ovest</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">Microsoft.CSharp.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.CSharp.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.Data.Sqlite.dll</td>
    <td class="tg-yw4l">Provider ADO.NET per SQLite; vedere <a href="~/ios/data-cloud/system.data.md">limitazioni</a>.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.Data.Tds.dll</td>
    <td class="tg-yw4l">Supporto del protocollo TDS; utilizzato per <a href="https://developer.xamarin.com/api/namespace/System.Data.SqlClient/">SqlClient</a> supportano all'interno di <a href="https://developer.xamarin.com/api/namespace/System.Data/">System. Data</a>.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.Dynamic.Interpreter.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.Security.dll</td>
    <td class="tg-yw4l">API di crittografia.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">monotouch.dll</td>
    <td class="tg-yw4l">Questo assembly contiene l'associazione all'API CocoaTouch c#. Questo è disponibile solo all'interno dei progetti iOS classica.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">MonoTouch.Dialog-1.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">MonoTouch.NUnitLite.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">mscorlib.dll</td>
    <td class="tg-yw4l"><a ref="https://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx">Silverlight</a></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">OpenTK-1.0.dll</td>
    <td class="tg-yw4l">L'oggetto OpenGL/OpenAL orientata ai servizi API estese per fornire il supporto dei dispositivi iPhone.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.dll</td>
    <td class="tg-yw4l"><a href="https://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx">Silverlight</a>, nonché i tipi degli spazi dei nomi seguenti:<br>System.Collections.Specialized<br>System.ComponentModel<br>System.ComponentModel.Design<br>System.Diagnostics<br>System.IO<br>System.IO.Compression<br>System.IO.Compression.FileSystem<br>System.Net<br>System.Net.Cache<br>System.Net.Mail<br>System.Net.Mime<br>System.Net.NetworkInformation<br>System.Net.Security<br>System.Net.Sockets<br>System.Runtime.InteropServices<br>System.Runtime.Versioning<br>System.Security.AccessControl<br>System.Security.Authentication<br>System.Security.Cryptography<br>System.Security.Permissions<br>System.Threading<br>System.Timers</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.ComponentModel.Composition.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.ComponentModel.DataAnnotations.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Core.dll</td>
    <td class="tg-yw4l"><a ref="https://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx">Silverlight</a></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Data.dll</td>
    <td class="tg-yw4l"><a href="http://msdn.microsoft.com/en-us/library/ms229335.aspx">.NET 3.5</a> , <a href="~/ios/data-cloud/system.data.md">con alcune funzionalità rimosse</a>.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Data.Services.Client.dll</td>
    <td class="tg-yw4l">Client oData completo.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.IO.Compression</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.IO.Compression.FileSystem</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Json.dll</td>
    <td class="tg-yw4l"><a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx">Silverlight</a></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Net.Http.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Numerics.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Runtime.Serialization.dll</td>
    <td class="tg-yw4l"><a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx">Silverlight</a></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.ServiceModel.dll</td>
    <td class="tg-yw4l">Stack WCF come presente nella <a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx">Silverlight</a></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.ServiceModel.Internals.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.ServiceModel.Web.dll</td>
    <td class="tg-yw4l"><a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx">Silverlight</a>, nonché i tipi degli spazi dei nomi seguenti: <br>System<br>System.ServiceModel.Channels<br>System.ServiceModel.Description<br>System.ServiceModel.Web</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Transactions.dll</td>
    <td class="tg-yw4l"><a href="http://msdn.microsoft.com/en-us/library/ms229335.aspx">.NET 3.5</a>; fa parte di <a href="~/ios/data-cloud/system.data.md">System. Data</a> supportano.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Web.Services.dll</td>
    <td class="tg-yw4l">Servizi Web di base del profilo di .NET 3.5, con le funzionalità server rimosso.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Windows.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Xml.dll</td>
    <td class="tg-yw4l"><a href="http://msdn.microsoft.com/en-us/library/ms229335.aspx">.NET 3.5</a></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Xml.Linq.dll</td>
    <td class="tg-yw4l"><a href="http://msdn.microsoft.com/en-us/library/ms229335.aspx">.NET 3.5</a></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Xml.Serialization.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">Xamarin.iOS.dll</td>
    <td class="tg-yw4l">Questo assembly contiene l'associazione all'API CocoaTouch c#. Viene utilizzato solo nei progetti iOS unificato.</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Java.Interop.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.Android.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.Android.Export.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.Posix.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.EnterpriseServices.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Xamarin.Android.NUnitLite.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-q3ci">✓</td>
    <td class="tg-yw4l"></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Mono.CompilerServices.SymbolWriter.dll</td>
    <td class="tg-yw4l">Writer di compilatori.</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">Xamarin.Mac.dll</td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-yw4l"></td>
    <td class="tg-p7et">✓</td>
  </tr>
  <tr>
    <td class="tg-yw4l">System.Drawing.dll</td>
    <td class="tg-yw4l">System. Drawing API - solo API classica.<br>System. Drawing non è supportata nell'API unificata per le Xamarin.Mac 4.5 di .NET Framework per dispositivi mobili.<br>Può essere aggiunto il supporto di System. Drawing per iOS e OS X con il <a href="https://github.com/mono/sysdrawing-coregraphics">sysdrawing coregraphics</a> libreria</td>
    <td class="tg-w6qd">✓</td>
    <td class="tg-yw4l"></td>
    <td class="tg-p7et">✓</td>
  </tr>
</table>