---
title: Installazione di .NET di incorporamento
description: Questo documento descrive come installare .NET di incorporamento. Illustra come eseguire gli strumenti manualmente, come le associazioni di generare automaticamente, come usare le destinazioni MSBuild personalizzate e passaggi di post-compilazione necessari.
ms.prod: xamarin
ms.assetid: 47106AF3-AC6E-4A0E-B30B-9F73C116DDB3
author: chamons
ms.author: chhamo
ms.date: 04/18/2018
ms.openlocfilehash: 2a572748c21d2a640add3346d1162f4b6bdc8e99
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668439"
---
# <a name="installing-net-embedding"></a>Installazione di .NET di incorporamento

## <a name="installing-net-embedding-from-nuget"></a>Installazione di incorporamento di .NET da NuGet

Scegliere **Aggiungi > Aggiungi pacchetti NuGet...**  e installare **Embeddinator 4000** da Gestione pacchetti NuGet:

![Gestione pacchetti NuGet](images/visualstudionuget.png)

Verrà installato **Embeddinator 4000.exe** e **objcgen** nel **pacchetti/Embeddinator-4000/tools** directory.

In generale, è necessario selezionare la versione più recente di Embeddinator-4000 caratteri per il download. Supporto di Objective-C richiede 0.4 o versione successiva.

## <a name="running-manually"></a>Esecuzione manuale

Ora che NuGet è installato, è possibile eseguire gli strumenti manualmente.

- Aprire un terminale (macOS) o il prompt dei comandi (Windows)
- Passare alla directory radice del soluzione
- Gli strumenti viene installato in:
    - **./packages/Embeddinator-4000.[VERSION]/tools/objcgen** (Objective-C)
    - **./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe** (Java/C)
- In macOS **objcgen** può essere eseguito direttamente.
- In Windows, **Embeddinator 4000.exe** può essere eseguito direttamente.
- In macOS **Embeddinator 4000.exe** deve essere eseguito con **mono**:
    - `mono ./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe`

Ogni chiamata a un comando sarà necessario un numero di parametri elencati nella documentazione specifica della piattaforma.

## <a name="automatic-binding-generation"></a>Generazione di associazione automatico

Esistono due approcci per l'esecuzione automatica di incorporamento .NET parte del processo di compilazione.

- Destinazioni MSBuild personalizzate
- Passaggi di post-compilazione

Sebbene questo documento illustra entrambi, è preferibile usare una destinazione di MSBuild personalizzata. I passaggi di compilazione successivi non verranno necessariamente eseguiti durante la compilazione dalla riga di comando.

### <a name="custom-msbuild-targets"></a>Destinazioni MSBuild personalizzate

Per personalizzare la compilazione con le destinazioni di MSbuild, creare prima di tutto una **Embeddinator 4000.targets** file accanto il csproj simile al seguente:

```xml
<Project DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="RunEmbeddinator" AfterTargets="AfterBuild" Inputs="$(OutputPath)/$(AssemblyName).dll" Outputs="$(IntermediateOutputPath)/Embeddinator/$(AssemblyName).framework/$(AssemblyName)">
        <Exec Command="" />
    </Target>
</Project>
```

In questo caso, il testo del comando deve essere compilato con una delle chiamate dell'incorporamento di .NET elencate nella documentazione specifica della piattaforma.

Per usare questa destinazione:

- Chiudere il progetto in Visual Studio 2017 o Visual Studio per Mac
- Aprire il file csproj libreria in un editor di testo
- Aggiungere questa riga nella parte inferiore, sopra l'elemento finale `</Project>` riga:

```xml
 <Import Project="Embeddinator-4000.targets" />
```

- Riaprire il progetto

### <a name="post-build-steps"></a>Passaggi di post-compilazione

I passaggi per aggiungere un passaggio di post-compilazione per eseguire l'incorporamento .NET variano a seconda dell'IDE:

#### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

In Visual Studio per Mac, passare a **opzioni progetto > compilazione > comandi personalizzati** e aggiungere un' **dopo la compilazione** passaggio.

Configurare il comando riportato nella documentazione specifica della piattaforma.

> [!NOTE]
> Assicurarsi di usare il numero di versione che è stato installato da NuGet

Se si intende eseguire lo sviluppo in corso su di C# progetto, è possibile anche aggiungere un comando personalizzato per pulire i **output** directory prima di eseguire l'incorporamento di .NET:

```shell
rm -Rf '${SolutionDir}/output/'
```

![Azione di compilazione personalizzato](images/visualstudiocustombuild.png)

> [!NOTE]
> L'associazione generata verrà inserito nella directory indicata dal `--outdir` o `--out` parametro. Il nome dell'associazione generate può variare poiché alcune piattaforme presentano alcune limitazioni nei nomi dei pacchetti.

#### <a name="visual-studio-2017"></a>Visual Studio 2017

Si configurerà essenzialmente la stessa operazione, ma il menu di scelta in Visual Studio 2017 sono leggermente diversi. Inoltre, i comandi della shell sono leggermente diversi.

Passare a **opzioni progetto > eventi di compilazione** e immettere il comando riportato nella documentazione specifica della piattaforma nel **riga di comando eventi post-compilazione** casella. Ad esempio:

![Incorporamento in Windows .NET](images/visualstudiowindows.png)
