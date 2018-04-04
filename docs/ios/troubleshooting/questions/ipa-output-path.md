---
title: È possibile modificare il percorso di output del file IPA?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 06074566b3d3a05e05a1646c70de211f908f3aa9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>È possibile modificare il percorso di output del file IPA?

## <a name="for-cycle-7-and-higher"></a>Per il ciclo 7 e versioni successiva
Sì, è possibile utilizzare le destinazioni di MSBuild personalizzate per ottenere questo risultato. L'opzione più semplice consiste probabilmente nel copiare il `.ipa` file dopo che è stata creata.

Questi passaggi funzionerà per qualsiasi progetto iOS che usa il motore di compilazione MSBuild nel Mac o Windows. (Nota: il motore di compilazione MSBuild di utilizzare tutti i progetti di Unified API.)

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

2. Impostare il DestinationFolder nella cartella di output desiderato. Come al solito è possibile utilizzare la proprietà di MSBuild (ad esempio $(OutputPath)) all'interno di questo argomento se si desidera.

## <a name="notes"></a>Note
- Il `CreateIpaDependsOn` proprietà definita nel `Xamarin.iOS.Common.targets` file che è parte di xamarin. IOS. Si comporta come descritto in *delle proprietà 'DependsOn' verrà ignorato* sul [ https://msdn.microsoft.com/en-us/library/ms366724.aspx ](https://msdn.microsoft.com/en-us/library/ms366724.aspx).

- È possibile utilizzare un **spostare** attività piuttosto che un **copia** attività se preferito. Se si sceglie l'opzione e sono la creazione in Windows, è necessario utilizzare il nome completo attività `<Microsoft.Build.Tasks.Move>` per evitare ambiguità con il XamarinVS attività di compilazione.

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Per le versioni precedenti di Xamarin Studio 6.0.0.5174 | Xamarin per Visual Studio 4.1.0.530

Sì, è possibile utilizzare le destinazioni di MSBuild personalizzate per ottenere questo risultato. L'opzione più semplice consiste probabilmente nel copiare il `.ipa` file dopo che è stata creata.

Questi passaggi funzionerà per qualsiasi progetto iOS che usa il motore di compilazione MSBuild nel Mac o Windows. (Nota: il motore di compilazione MSBuild di utilizzare tutti i progetti di Unified API.)

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

2. Impostare il `DestinationFolder` nella cartella di output desiderato. Come al solito è possibile utilizzare le proprietà di MSBuild (ad esempio `$(OutputPath)`) all'interno di questo argomento se si desidera.

## <a name="notes"></a>Note
- Il `CreateIpaDependsOn` proprietà definita nel `Xamarin.iOS.Common.targets` file che è parte di xamarin. IOS. Si comporta come descritto in *delle proprietà "DependsOn" verrà ignorato* sul [ https://msdn.microsoft.com/en-us/library/ms366724.aspx ](https://msdn.microsoft.com/en-us/library/ms366724.aspx).

- È possibile utilizzare un **spostare** attività piuttosto che un **copia** attività se preferito. Se si sceglie l'opzione e sono la creazione in Windows, è necessario utilizzare il nome completo attività `<Microsoft.Build.Tasks.Move>` per evitare ambiguità con il XamarinVS attività di compilazione.
