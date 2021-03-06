---
title: È possibile modificare il percorso di output del file IPA?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 6df481688bfaa1e23cc56e6e34586f23d8ab9da6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031033"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>È possibile modificare il percorso di output del file IPA?

## <a name="for-cycle-7-and-higher"></a>Per il ciclo 7 e versioni successive
Sì, è possibile usare destinazioni MSBuild personalizzate per ottenere questo risultato. L'opzione più semplice è probabilmente copiare il file di `.ipa` dopo che è stato compilato.

Questi passaggi funzioneranno per qualsiasi progetto iOS che usa il motore di compilazione MSBuild in Mac o Windows. Nota: tutti i progetti API unificata usano il motore di compilazione di MSBuild.

1. Aprire il file di `.csproj` per il progetto di app iOS in un editor di testo e quindi aggiungere le righe seguenti alla fine (immediatamente prima del tag di chiusura `</Project>`):

    ```xml
    <PropertyGroup>
        <CreateIpaDependsOn>
        $(CreateIpaDependsOn);
        CopyIpa
        </CreateIpaDependsOn>
    </PropertyGroup>
    
    <Target Name="CopyIpa"
            Condition="'$(OutputType)' == 'Exe'
            And '$(ComputedPlatform)' == 'iPhone'
            And '$(BuildIpa)' == 'true'">
        <Copy
            SourceFiles="$(IpaPackagePath)"
            DestinationFolder="$(OutputPath)"
        />
    </Target>
    ```

2. Impostare DestinationFolder sulla cartella di output desiderata. Come di consueto, se si desidera, è possibile utilizzare le proprietà MSBuild (ad esempio $ (OutputPath)) in questo argomento.

## <a name="notes"></a>Note

- La proprietà `CreateIpaDependsOn` viene definita nel file `Xamarin.iOS.Common.targets` che fa parte di Xamarin.iOS. Si comporta come descritto nella sezione override delle [destinazioni predefinite](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets) dell'articolo [procedura: estendere il processo di compilazione di Visual Studio](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process).

- Se si preferisce, è possibile utilizzare un'attività di **spostamento** anziché un'attività di **copia** . Se si sceglie questa opzione e si esegue la compilazione in Windows, sarà necessario usare il nome completo dell'attività `<Microsoft.Build.Tasks.Move>` per evitare ambiguità con le attività di compilazione XamarinVS.

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Per le versioni precedenti Xamarin Studio 6.0.0.5174 | Xamarin per Visual Studio 4.1.0.530

Sì, è possibile usare destinazioni MSBuild personalizzate per ottenere questo risultato. L'opzione più semplice è probabilmente copiare il file di `.ipa` dopo che è stato compilato.

Questi passaggi funzioneranno per qualsiasi progetto iOS che usa il motore di compilazione MSBuild in Mac o Windows. Nota: tutti i progetti API unificata usano il motore di compilazione di MSBuild.

1. Aprire il file di `.csproj` per il progetto di app iOS in un editor di testo e quindi aggiungere le righe seguenti alla fine (immediatamente prima del tag di chiusura della `</Project>`).

    ```xml
    <PropertyGroup>
        <CreateIpaDependsOn>
            $(CreateIpaDependsOn);
            CopyIpa
        </CreateIpaDependsOn>
    </PropertyGroup>

    <Target Name="CopyIpa"
            Condition="'$(OutputType)' == 'Exe'
            And '$(ComputedPlatform)' == 'iPhone'
            And '$(BuildIpa)' == 'true'">
        <Copy
            SourceFiles="$(OutputPath)$(IpaPackageName)"
            DestinationFolder="/Users/macuser/Desktop/"
        />
    </Target>
    ```

2. Impostare la `DestinationFolder` sulla cartella di output desiderata. Come di consueto, se si desidera, è possibile utilizzare le proprietà di MSBuild, ad esempio `$(OutputPath)`, all'interno di questo argomento.

## <a name="notes"></a>Note

- La proprietà `CreateIpaDependsOn` viene definita nel file `Xamarin.iOS.Common.targets` che fa parte di Xamarin.iOS. t si comporta come descritto nella sezione [override delle destinazioni predefinite](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets) dell'articolo [procedura: estendere il processo di compilazione di Visual Studio](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process).

- Se si preferisce, è possibile utilizzare un'attività di **spostamento** anziché un'attività di **copia** . Se si sceglie questa opzione e si esegue la compilazione in Windows, sarà necessario usare il nome completo dell'attività `<Microsoft.Build.Tasks.Move>` per evitare ambiguità con le attività di compilazione XamarinVS.
