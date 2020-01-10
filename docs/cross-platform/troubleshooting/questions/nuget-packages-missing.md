---
title: Errore dei pacchetti mancanti dopo l'aggiornamento dei pacchetti NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D61CC966-1D4A-49A5-8A6F-41572E28329B
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: a1ed4a2be63973e9e26dcb06163a3f9fd7eae649
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728096"
---
# <a name="missing-packages-error-after-updating-nuget-packages"></a>Errore dei pacchetti mancanti dopo l'aggiornamento dei pacchetti NuGet

Questo problema è stato segnalato principalmente per le soluzioni di esempio di Novell. Forms, ma la possibilità di questo problema può verificarsi in qualsiasi progetto che usa pacchetti NuGet.

Se dopo l'aggiornamento dei pacchetti NuGet nel progetto o nella soluzione, viene visualizzato un errore che fa riferimento ai numeri di versione precedenti del pacchetto, ad esempio:

```csharp
Error: This project references NuGet package(s) that are missing on this computer.
Enable NuGet Package Restore to download them.
For more information, see http://go.microsoft.com/fwlink/?LinkID=322105

The missing file is ../../packages/Xamarin.Forms.1.3.1.6296/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10/Xamarin.Forms.targets. (FormsGallery)
```

In questo esempio *Novell. Forms. 1.3.1.6296* è il numero di versione precedente che è stato rimosso con l'aggiornamento del pacchetto NuGet.

Questo problema può verificarsi se gli elementi XML nel file con estensione csproj che fanno riferimento al numero di versione precedente del pacchetto sono stati aggiunti o modificati manualmente, NuGet non li rimuove o li aggiorna se sono stati aggiunti o modificati manualmente, quindi il progetto cerca i pacchetti che sono stati eliminati.

Per risolvere il problema, modificare manualmente i file con estensione csproj ed eliminare tutti gli elementi che fanno riferimento al numero di versione precedente.

Elementi di esempio da rimuovere (se hanno il numero di versione del pacchetto precedente):

```xml
<Reference Include="Xamarin.Forms.Maps">
    <HintPath>..\..\packages\Xamarin.Forms.Maps.1.3.1.6296\lib\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.Maps.dll</HintPath>
</Reference>

<Import Project="..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets" Condition="Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" />
<Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets'))" />
```
