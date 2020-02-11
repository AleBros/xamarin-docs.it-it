---
title: Installazione dell'incorporamento di .NET
description: Questo documento descrive come installare l'incorporamento .NET. Viene illustrato come eseguire manualmente gli strumenti, come generare automaticamente le associazioni, come utilizzare le destinazioni MSBuild personalizzate e i passaggi di post-compilazione necessari.
ms.prod: xamarin
ms.assetid: 47106AF3-AC6E-4A0E-B30B-9F73C116DDB3
author: chamons
ms.author: chhamo
ms.date: 04/18/2018
ms.openlocfilehash: 47efbaa12475f627b5963cb6613c3441a1d96aac
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227846"
---
# <a name="installing-net-embedding"></a>Installazione dell'incorporamento di .NET

## <a name="installing-net-embedding-from-nuget"></a>Installazione dell'incorporamento di .NET da NuGet

Scegliere **aggiungi > Aggiungi pacchetti NuGet...** e installare **Embeddinator-4000** da Gestione pacchetti NuGet:

![Gestione pacchetti NuGet](images/visualstudionuget.png)

Il file **Embeddinator-4000. exe** e **objcgen** verrà installato nella directory **packages/Embeddinator-4000/Tools** .

In generale, per il download è necessario selezionare la versione più recente di Embeddinator-4000. Il supporto Objective-C richiede 0,4 o versione successiva.

## <a name="running-manually"></a>Esecuzione manuale

Ora che NuGet è installato, è possibile eseguire manualmente gli strumenti.

- Aprire un terminale (macOS) o un prompt dei comandi (Windows)
- Passare alla directory radice della soluzione
- Gli strumenti vengono installati in:
  - **./Packages/Embeddinator-4000. [Versione]/Tools/objcgen** (Objective-C)
  - **./Packages/Embeddinator-4000. [Versione]/tools/Embeddinator-4000.exe** (Java/C)
- In macOS, **objcgen** può essere eseguito direttamente.
- In Windows, **Embeddinator-4000. exe** può essere eseguito direttamente.
- In macOS, **Embeddinator-4000. exe** deve essere eseguito con **mono**:
  - `mono ./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe`

Ogni chiamata al comando richiede una serie di parametri elencati nella documentazione specifica della piattaforma.

## <a name="automatic-binding-generation"></a>Generazione automatica di binding

Esistono due approcci per l'esecuzione automatica della parte di incorporamento .NET del processo di compilazione.

- Destinazioni MSBuild personalizzate
- Passaggi di post-compilazione

Sebbene in questo documento vengano descritti entrambi, è preferibile usare una destinazione MSBuild personalizzata. I passaggi di post-compilazione non vengono eseguiti necessariamente quando si compila dalla riga di comando.

### <a name="custom-msbuild-targets"></a>Destinazioni MSBuild personalizzate

Per personalizzare la compilazione con le destinazioni MSbuild, creare prima un file **Embeddinator-4000. targets** accanto al file csproj simile al seguente:

```xml
<Project DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="RunEmbeddinator" AfterTargets="AfterBuild" Inputs="$(OutputPath)/$(AssemblyName).dll" Outputs="$(IntermediateOutputPath)/Embeddinator/$(AssemblyName).framework/$(AssemblyName)">
        <Exec Command="" />
    </Target>
</Project>
```

In questo caso, il testo del comando deve essere compilato con una delle chiamate di incorporamento .NET elencate nella documentazione specifica della piattaforma.

Per usare questa destinazione:

- Chiudere il progetto in Visual Studio 2017 o Visual Studio per Mac
- Aprire la libreria csproj in un editor di testo
- Aggiungere questa riga nella parte inferiore, sopra la riga `</Project>` finale:

```xml
 <Import Project="Embeddinator-4000.targets" />
```

- Riaprire il progetto

### <a name="post-build-steps"></a>Passaggi di post-compilazione

I passaggi per aggiungere un passaggio di post-compilazione per l'esecuzione dell'incorporamento di .NET variano a seconda dell'IDE:

#### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

In Visual Studio per Mac passare a **Opzioni progetto > compila > comandi personalizzati** e aggiungere un'istruzione **dopo la compilazione** .

Configurare il comando elencato nella documentazione specifica della piattaforma.

> [!NOTE]
> Assicurarsi di usare il numero di versione installato da NuGet

Se si intende eseguire lo sviluppo continuo sul C# progetto, è anche possibile aggiungere un comando personalizzato per pulire la directory di **output** prima di eseguire l'incorporamento di .NET:

```shell
rm -Rf '${SolutionDir}/output/'
```

![Azione di compilazione personalizzata](images/visualstudiocustombuild.png)

> [!NOTE]
> Il binding generato verrà inserito nella directory indicata dal `--outdir` parametro o. `--out` Il nome dell'associazione generato può variare in quanto alcune piattaforme presentano limitazioni per i nomi dei pacchetti.

#### <a name="visual-studio-2017"></a>Visual Studio 2017

In sostanza, la stessa operazione viene configurata, ma i menu in Visual Studio 2017 sono leggermente diversi. Anche i comandi della shell sono leggermente diversi.

Passare a **Opzioni progetto > compila eventi** e immettere il comando elencato nella documentazione specifica della piattaforma nella casella **riga di comando eventi post-compilazione** . Ad esempio:

![Incorporamento di .NET in Windows](images/visualstudiowindows.png)
 