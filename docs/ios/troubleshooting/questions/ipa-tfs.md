---
title: Come copiare i file di output IPA alla cartella di ricezione TFS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 087a20ea3b573595e6cbd2b40d77de649676391e
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2018
ms.locfileid: "31883709"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>Come copiare i file di output IPA alla cartella di ricezione TFS?

Aprire il `.csproj` file per il progetto di app iOS in un editor di testo e quindi aggiungere le righe seguenti alla fine (immediatamente prima della chiusura `</Project>` tag):

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
        And '$(BuildIpa)' == 'true'
        And '$(TF_BUILD)' == 'true'">
    <Copy
        SourceFiles="$(OutputPath)$(IpaPackageName)"
        DestinationFolder="$(TF_BUILD_BINARIESDIRECTORY)"
    />
</Target>
```

## <a name="notes"></a>Note

-   È la stessa tecnica generale descritto in [è possibile modificare il percorso di output del file IPA?](~/ios/troubleshooting/questions/ipa-output-path.md). I due punti importanti sono impostare `$(TF_BUILD_BINARIESDIRECTORY)` come cartella di destinazione e aggiungere una condizione aggiuntiva in modo `CopyIpa` verrà eseguito solo per le build di TFS.

-   Per una descrizione delle `TF_BUILD_BINARIESDIRECTORY` vedere [ https://msdn.microsoft.com/library/hh850448.aspx ](https://msdn.microsoft.com/library/hh850448.aspx).

## <a name="additional-references"></a>Riferimenti aggiuntivi

- [Documentazione sull'installazione di TFS per l'uso con Xamarin](https://docs.microsoft.com/vsts/tfvc/overview)
- [Attività di compilazione TFS: xamarin](https://docs.microsoft.com/vsts/build-release/tasks/build/xamarin-android)
- [Attività di compilazione TFS: xamarin](https://docs.microsoft.com/vsts/build-release/tasks/build/xamarin-ios)

### <a name="next-steps"></a>Passaggi successivi
Questo documento viene illustrato il comportamento corrente a partire da 3.11.666 Xamarin per Visual Studio e xamarin 8.10.3 nel Mac, host di compilazione. Per ulteriore assistenza, contattare Microsoft, o se il problema rimane anche dopo l'utilizzo di queste informazioni, vedere [quali opzioni di supporto sono disponibili per Xamarin?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, i suggerimenti, nonché come Se necessario, archiviare un nuovo bug. 



