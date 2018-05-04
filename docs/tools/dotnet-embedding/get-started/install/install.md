---
title: Installazione di .NET incorporamento
ms.prod: xamarin
ms.assetid: 47106AF3-AC6E-4A0E-B30B-9F73C116DDB3
ms.technology: xamarin-cross-platform
author: chamons
ms.author: chhamo
ms.date: 4/18/2018
ms.openlocfilehash: d4ee3ef610611dd489d90a364d15f727ea49a79e
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="installing-net-embedding"></a>Installazione di .NET incorporamento

## <a name="installing-net-embedding-from-nuget"></a>L'installazione di .NET incorporamento da NuGet

Scegliere **aggiungere > aggiungere pacchetti NuGet...**  e installare **Embeddinator 4000** da Gestione pacchetti NuGet:

![Gestione pacchetti NuGet](images/visualstudionuget.png)

Verrà installato **Embeddinator 4000.exe** e **objcgen** nel **pacchetti/Embeddinator-4000/tools** directory.

In generale, la versione più recente di Embeddinator-4000 deve essere selezionata per il download. Supporto di Objective-C richiede 0,4 o versione successiva.

## <a name="running-manually"></a>Esecuzione manuale

NuGet è installato, è possibile eseguire lo strumento manualmente.

- Aprire un terminale (macOS) o un prompt dei comandi (Windows)
- Spostarsi nella directory per la radice della soluzione
- Lo strumento viene installato:
    - **. / pacchetti/Embeddinator-4000. [Versione] / tools/objcgen** (Objective-C)
    - **. / pacchetti/Embeddinator-4000. [VERSION]/tools/Embeddinator-4000.exe** (Java/C) 
- In, macOS **objcgen** può essere eseguito direttamente. 
- In Windows, **Embeddinator 4000.exe** può essere eseguito direttamente.
- In, macOS **Embeddinator 4000.exe** deve essere eseguito con **mono**: 
    - `mono ./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe`

Ogni chiamata a un comando sarà necessario un numero di parametri elencati nella documentazione di specifiche della piattaforma.

## <a name="automatic-binding-generation"></a>Generazione di associazione automatico

Esistono due approcci per l'esecuzione automaticamente durante l'incorporamento .NET parte del processo di compilazione.

- Destinazioni di MSBuild personalizzate
- Passaggi di post-compilazione

Mentre questo documento vengono illustrati entrambi, sia preferibile utilizzare una destinazione di MSBuild personalizzata. Istruzioni di compilazione post non necessariamente funzionerà durante la compilazione dalla riga di comando.

### <a name="custom-msbuild-targets"></a>Destinazioni di MSBuild personalizzate

Per personalizzare la compilazione con le destinazioni di MSbuild, creare innanzitutto una **Embeddinator 4000.targets** file accanto il csproj simile al seguente:

```xml
<Project DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="RunEmbeddinator" AfterTargets="AfterBuild" Inputs="$(OutputPath)/$(AssemblyName).dll" Outputs="$(IntermediateOutputPath)/Embeddinator/$(AssemblyName).framework/$(AssemblyName)">
        <Exec Command="" />
    </Target>
</Project>
```

In questo caso, il testo del comando deve essere compilato con una delle chiamate dell'incorporamento .NET elencate nella documentazione di specifiche della piattaforma.

Per usare questa destinazione:

- Chiudere il progetto in Visual Studio 2017 o Visual Studio per Mac
- Aprire il file csproj libreria in un editor di testo
- Aggiungere questa riga nella parte inferiore, sopra l'elemento finale `</Project>` riga:

```xml
 <Import Project="Embeddinator-4000.targets" />
```

- Riaprire il progetto

### <a name="post-build-steps"></a>Passaggi di post-compilazione

I passaggi per aggiungere un passaggio di post-compilazione per l'esecuzione durante l'incorporamento .NET variano a seconda dell'IDE:

#### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

In Visual Studio per Mac, passare a **opzioni progetto > compilare > comandi personalizzati** e aggiungere un **dopo la compilazione** passaggio.

Consente di impostare il comando riportato nella documentazione di specifiche della piattaforma.

> [!NOTE]
> Assicurarsi di usare il numero di versione che è stato installato da NuGet

Se si intende eseguire continuo sviluppo del progetto c#, è possibile anche aggiungere un comando personalizzato per pulire il **output** directory prima di eseguire l'incorporamento .NET:

```shell
rm -Rf '${SolutionDir}/output/'
```

![Azione di compilazione personalizzata](images/visualstudiocustombuild.png)

> [!NOTE]
> L'associazione generata verrà inserito nella directory indicata dal `--outdir` o `--out` parametro. Il nome dell'associazione generata può variare poiché alcune piattaforme presentano alcune limitazioni nei nomi dei pacchetti.

#### <a name="visual-studio-2017"></a>Visual Studio 2017

Si imposterà essenzialmente la stessa operazione, ma i menu in Visual Studio 2017 sono leggermente diversi. Inoltre, i comandi della shell sono leggermente diversi.

Passare a **opzioni progetto > eventi di compilazione** e immettere il comando elencato nella documentazione di specifiche della piattaforma nel **riga di comando eventi post-compilazione** casella. Ad esempio:

![Incorporamento in Windows .NET](images/visualstudiowindows.png)
