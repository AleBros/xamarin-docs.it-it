---
title: Errore di pacchetti mancante dopo l'aggiornamento di pacchetti Nuget
ms.topic: article
ms.prod: xamarin
ms.assetid: D61CC966-1D4A-49A5-8A6F-41572E28329B
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: f330590132e4881484eeae3efafe570d991a509a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="missing-packages-error-after-updating-nuget-packages"></a>Errore di pacchetti mancante dopo l'aggiornamento di pacchetti Nuget

Questo problema è stato segnalato principalmente sulle soluzioni di app di esempio xamarin. Forms, ma il potenziale per questo problema può verificarsi in qualsiasi progetto che utilizza i pacchetti NuGet. 

Se dopo l'aggiornamento pacchetti Nuget nel progetto o soluzione, viene visualizzato un errore che fa riferimento ai numeri di versione precedente del pacchetto, ad esempio:

```csharp
Error: This project references NuGet package(s) that are missing on this computer.
Enable NuGet Package Restore to download them.  
For more information, see http://go.microsoft.com/fwlink/?LinkID=322105

The missing file is ../../packages/Xamarin.Forms.1.3.1.6296/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10/Xamarin.Forms.targets. (FormsGallery)

```

In questo esempio *Xamarin.Forms.1.3.1.6296* è il numero di versione precedente che è stata rimossa con l'aggiornamento del pacchetto Nuget.

Questa situazione può verificarsi se gli elementi XML nel file con estensione csproj che fa riferimento il numero di versione precedente del pacchetto sono stati aggiunti manualmente o modificato, Nuget non rimuovere o aggiornarli se fossero stati aggiunti/modificato manualmente, pertanto il progetto ora esegue la ricerca per i pacchetti che sono stati eliminato. 

Per risolvere questo problema, modificare i file con estensione csproj manualmente ed Elimina tutti gli elementi che fanno riferimento a numero di versione precedente. 

Elementi di esempio da rimuovere (se hanno il numero di versione precedente del pacchetto):

```xml
<Reference Include="Xamarin.Forms.Maps">
    <HintPath>..\..\packages\Xamarin.Forms.Maps.1.3.1.6296\lib\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.Maps.dll</HintPath>
</Reference>

<Import Project="..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets" Condition="Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" />
<Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets'))" />

```

