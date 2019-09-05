---
title: È possibile modificare il percorso di output del file IPA?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: b8006b1ffe253ac57c1ab435690c5b378cc709fb
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278662"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>È possibile modificare il percorso di output del file IPA?

## <a name="for-cycle-7-and-higher"></a>Per il ciclo 7 e versioni successive
Sì, è possibile usare destinazioni MSBuild personalizzate per ottenere questo risultato. L'opzione più semplice è probabilmente copiare il `.ipa` file dopo che è stato compilato.

Questi passaggi funzioneranno per qualsiasi progetto iOS che usa il motore di compilazione MSBuild in Mac o Windows. Nota: tutti i progetti API unificata usano il motore di compilazione di MSBuild.

1. Aprire il `.csproj` file per il progetto di app iOS in un editor di testo e quindi aggiungere le righe seguenti alla fine (immediatamente prima del `</Project>` tag di chiusura):

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
- La `CreateIpaDependsOn` proprietà viene definita `Xamarin.iOS.Common.targets` nel file che fa parte di Novell. iOS. Si comporta come descritto nella sezione override delle [destinazioni predefinite](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets) dell'articolo [procedura: Estendere il processo](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process)di compilazione di Visual Studio.

- Se si preferisce, è possibile utilizzare un'attività di **spostamento** anziché un'attività di **copia** . Se si sceglie questa opzione e si esegue la compilazione in Windows, sarà necessario usare il nome `<Microsoft.Build.Tasks.Move>` completo dell'attività per evitare ambiguità con le attività di compilazione di XamarinVS.

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Per le versioni precedenti Xamarin Studio 6.0.0.5174 | Novell per Visual Studio 4.1.0.530

Sì, è possibile usare destinazioni MSBuild personalizzate per ottenere questo risultato. L'opzione più semplice è probabilmente copiare il `.ipa` file dopo che è stato compilato.

Questi passaggi funzioneranno per qualsiasi progetto iOS che usa il motore di compilazione MSBuild in Mac o Windows. Nota: tutti i progetti API unificata usano il motore di compilazione di MSBuild.

1. Aprire il `.csproj` file per il progetto di app iOS in un editor di testo e quindi aggiungere le righe seguenti alla fine (immediatamente prima del `</Project>` tag di chiusura).

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

2. Impostare sulla `DestinationFolder` cartella di output desiderata. Come di consueto, se si desidera, è `$(OutputPath)`possibile utilizzare le proprietà di MSBuild, ad esempio, all'interno di questo argomento.

## <a name="notes"></a>Note
- La `CreateIpaDependsOn` proprietà viene definita `Xamarin.iOS.Common.targets` nel file che fa parte di Novell. iOS. t si comporta come descritto nella sezione [override delle destinazioni predefinite](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets) dell'articolo [procedura: Estendere il processo](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process)di compilazione di Visual Studio.

- Se si preferisce, è possibile utilizzare un'attività di **spostamento** anziché un'attività di **copia** . Se si sceglie questa opzione e si esegue la compilazione in Windows, sarà necessario usare il nome `<Microsoft.Build.Tasks.Move>` completo dell'attività per evitare ambiguità con le attività di compilazione di XamarinVS.
