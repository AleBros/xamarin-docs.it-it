---
title: È possibile modificare il percorso di output del file IPA?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 1c3c3a63de40a63f040870505b086d67fe160773
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421195"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>È possibile modificare il percorso di output del file IPA?

## <a name="for-cycle-7-and-higher"></a>Per il ciclo di 7 e versioni successiva
Sì, è possibile usare destinazioni MSBuild personalizzate per ottenere questo risultato. L'opzione più semplice consiste probabilmente nel copiare il `.ipa` file dopo che è stata creata.

Questa procedura è indicata per qualsiasi progetto iOS che usa il motore di compilazione MSBuild in Mac o Windows. (Nota: tutti i progetti di API unificata usano il motore di compilazione MSBuild.)

1. Aprire il `.csproj` file per il progetto di app iOS in un editor di testo e quindi aggiungere le righe seguenti alla fine (immediatamente prima della chiusura `</Project>` tag):
    
    ```
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

2. Impostare il Cartelladestinazione nella cartella di output desiderato. Normalmente è possibile usare le proprietà di MSBuild (ad esempio $(OutputPath)) all'interno di questo argomento se si desidera.

## <a name="notes"></a>Note
- Il `CreateIpaDependsOn` proprietà definita nel `Xamarin.iOS.Common.targets` file che è parte di xamarin. IOS. Si comporta come descritto in *delle proprietà 'DependsOn' override* sul [ https://msdn.microsoft.com/library/ms366724.aspx ](https://msdn.microsoft.com/library/ms366724.aspx).

- È possibile usare una **spostare** attività anziché un **copia** attività se si preferisce. Se si sceglie se l'opzione e si sta generando su Windows, è necessario usare il nome completo attività `<Microsoft.Build.Tasks.Move>` per evitare ambiguità con la XamarinVS attività di compilazione.

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Per le versioni precedenti di Xamarin Studio 6.0.0.5174 | Xamarin per Visual Studio 4.1.0.530

Sì, è possibile usare destinazioni MSBuild personalizzate per ottenere questo risultato. L'opzione più semplice consiste probabilmente nel copiare il `.ipa` file dopo che è stata creata.

Questa procedura è indicata per qualsiasi progetto iOS che usa il motore di compilazione MSBuild in Mac o Windows. (Nota: tutti i progetti di API unificata usano il motore di compilazione MSBuild.)

1. Aprire il `.csproj` file per il progetto di app iOS in un editor di testo e quindi aggiungere le righe seguenti alla fine (immediatamente prima della chiusura `</Project>` tag).

    ```csharp
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

2. Impostare il `DestinationFolder` nella cartella di output desiderato. Normalmente è possibile usare le proprietà di MSBuild (ad esempio `$(OutputPath)`) all'interno di questo argomento se si desidera.

## <a name="notes"></a>Note
- Il `CreateIpaDependsOn` proprietà definita nel `Xamarin.iOS.Common.targets` file che è parte di xamarin. IOS. Si comporta come descritto in *delle proprietà "DependsOn" override* sul [ https://msdn.microsoft.com/library/ms366724.aspx ](https://msdn.microsoft.com/library/ms366724.aspx).

- È possibile usare una **spostare** attività anziché un **copia** attività se si preferisce. Se si sceglie se l'opzione e si sta generando su Windows, è necessario usare il nome completo attività `<Microsoft.Build.Tasks.Move>` per evitare ambiguità con la XamarinVS attività di compilazione.
