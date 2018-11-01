---
title: Errore di pacchetti mancanti dopo l'aggiornamento di pacchetti Nuget
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D61CC966-1D4A-49A5-8A6F-41572E28329B
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 7cb802dd60d4e4879a260ff56d4f94ea5acb2965
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674847"
---
# <a name="missing-packages-error-after-updating-nuget-packages"></a>Errore di pacchetti mancanti dopo l'aggiornamento di pacchetti Nuget

Questo problema è stato segnalato principalmente sulle soluzioni di app di esempio xamarin. Forms, ma il potenziale per risolvere questo problema può verificarsi in qualsiasi progetto che usa i pacchetti NuGet. 

Se dopo l'aggiornamento di pacchetti Nuget nel progetto o nella soluzione, viene visualizzato un errore che fa riferimento, ad esempio i numeri di versione del pacchetto precedente:

```csharp
Error: This project references NuGet package(s) that are missing on this computer.
Enable NuGet Package Restore to download them.  
For more information, see http://go.microsoft.com/fwlink/?LinkID=322105

The missing file is ../../packages/Xamarin.Forms.1.3.1.6296/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10/Xamarin.Forms.targets. (FormsGallery)
```

In questo esempio *Xamarin.Forms.1.3.1.6296* è il numero di versione precedente che è stata rimossa con l'aggiornamento del pacchetto Nuget.

Questa situazione può verificarsi se gli elementi XML nel file con estensione csproj che fanno riferimento al numero di versione precedente del pacchetto è stato aggiunto manualmente o modificato, Nuget non rimuovere o aggiornarle se fossero stati aggiunto/modificato manualmente, in modo che il progetto ora esegue la ricerca di pacchetti che sono stati eliminato. 

Per risolvere questo problema, modificare i file con estensione csproj manualmente ed eliminare tutti gli elementi che fanno riferimento al numero di versione precedente. 

Elementi di esempio da rimuovere (se hanno il numero di versione del pacchetto precedente):

```xml
<Reference Include="Xamarin.Forms.Maps">
    <HintPath>..\..\packages\Xamarin.Forms.Maps.1.3.1.6296\lib\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.Maps.dll</HintPath>
</Reference>

<Import Project="..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets" Condition="Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" />
<Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets'))" />
```